# CSCD01 PROJECT DELIVERABLE#2

## **Table of Contents**
1. **[Issue1 #8611 API for voting for user reviews](#issue1)**
   - [Description](#description1)
   - [Involved Files](#file1)
   - [ER/UML Diagrams](#diagram1)
2. **[Issue2 #12790 VALIDATE LINE NUMBER IN DRAFT COMMENTS API](#issue2)**
   - [Description](#description2)
   - [Involved Files](#file2)
   - [ER/UML Diagrams](#diagram2)
3. **[Decision Making](#decision)**
4. **[Detailed Plan](#plan)**
   - [API Documentation](#api)
   - [File Changes](#changes)
   - [Acceptance Test](#acceptance)
5. **[Architecture Review](#architecture)**


<a name="issue1"></a>
## Issue1 [#8611 API for voting for user reviews](https://github.com/mozilla/addons-server/issues/8611)

<a name="description1"></a>
#### Description

The Mozilla Firefox Addons Market has already implemented the functionality allowing users to rate and write reviews for addons on the market. Additionally, users are allowed to edit or delete their own reviews. However, the current Addons Market on production is not supporting users to vote for other user’s reviews like StackOverflow does. The current features of the review page is shown below in the screenshot.

![](./pic/review_page.png)

The Mozilla core product team had a [meeting](https://docs.google.com/document/d/1oUz9Ng9v159IPdW4UqHhbNspWa9m5SaicAAX5jU7OjA/edit#) talking about this issue. They decided to introduce the new voting features for users. As a result, it will be easier for users to get the most helpful reviews and comments while browsing on the addons description page.

To implement this voting feature, it would require modifications on both the front-end and back-end. Since addons-server is the repository for back-end supports, this issue (#8611) is mainly focusing on the API support of the new voting feature. Which means no UI or even template modifications are required by this issue. The detailed functionalities that should be included for this issue are as follow:

1. Only logged-in users should be allowed to do the voting of existing reviews.

2. The logged-in users should be able to upvote or downvote at most once for each reviews if

3. - they are not the author of the review they want vote for, and
   - they are not the author of the addon that the review belongs to

4. The logged-in users should be able to undo and change their voting choices.

5. Ratings with no helpfulness votes have a helpfulness of 0.

6. The rest have a helpfulness of *(helpful votes) - (non-helpful votes)*.


<a name="file1"></a>
#### Which files will be involved?

In **ratings** app:

- urls.py:

  Since the voting feature requires direct actions on specific review messages, a request with review id should be made when the front-end is clicking the upvote/downvote button. Thus, corresponding urls should be provided in urls.py to handle these requests.

- models.py:

  This usage of this file is talking to the database. Because we are going to create a new table including the information of the voters and reviewers, we need to change this file.


- views.py:

  This file already included who has the authority to send http methods. We need to create a new class to add a group of users to decide who can send http ‘post’ method. Here is the code that to decide whether a user can send which kind of http method, which we can refer to:

```python
class RatingViewSet(AddonChildMixin, ModelViewSet):
    serializer_class = RatingSerializer
    permission_classes = [
        ByHttpMethod({
            'get': AllowAny,
            'head': AllowAny,
            'options': AllowAny,  # Needed for CORS.

            # Deletion requires a specific permission check.
            'delete': CanDeleteRatingPermission,

            # To post a rating you just need to be authenticated.
            'post': IsAuthenticated,

            # To edit a rating you need to be the author or be an admin.
            'patch': AnyOf(AllowOwner, GroupPermission(
                amo.permissions.ADDONS_EDIT)),

            # Implementing PUT would be a little incoherent as we don't want to
            # allow users to change `version` but require it at creation time.
            # So only PATCH is allowed for editing.
        }),
    ]
```

- permissions.py:

  Since the role of this file is to judge whether a user can do some operations, we need to handle the authority of a user whether he/she could upvote or downvote of our new feature. For example, in this file, the following function handles whether or not the request user can delete a rating:


```python
def user_can_delete_rating(request, rating):
    """Return whether or not the request.user can delete a rating.

    People who can delete ratings:
      * The original rating author.
      * Reviewers with Ratings:Moderate, if the rating has been flagged and
        they are not an author of this add-on.
      * Users in a group with "Users:Edit" or "Addons:Edit" privileges and
        they are not an author of this add-on.
    """
    is_rating_author = (
        request.user.is_authenticated and rating.user_id == request.user.id)
    is_addon_author = rating.addon.has_author(request.user)
    is_moderator = (
        acl.action_allowed(request, amo.permissions.RATINGS_MODERATE) and
        rating.editorreview
    )
    can_edit_users_or_addons = (
        acl.action_allowed(request, amo.permissions.USERS_EDIT) or
        acl.action_allowed(request, amo.permissions.ADDONS_EDIT)
    )

    return (
        is_rating_author or
        (not is_addon_author and (is_moderator or can_edit_users_or_addons))
    )
```

- serializers.py:

  The role of this file is to convert the gathered data from database to json and send it to frontend. Since we will gather some voting value from the database, we need to modify it.





<a name="diagram1"></a>
#### UML/ER diagrams to illustrate interactions between new and existing codes

##### Ratings app:

![](./pic/ratings.png)






<a name="issue2"></a>
## Issue2 [#12790 VALIDATE LINE NUMBER IN DRAFT COMMENTS API](https://github.com/mozilla/addons-server/issues/12790)

<a name="description2"></a>
#### Description

The Mozilla addons server allows developers to upload and publish their own addons. However, due to security and reliability concerns, addons are required to be reviewed by addon reviewers, which include content(code) review. The process is done through addons server reviewers tool, and also, addons code manager(another frontend). When the reviewers are reading the code, the reviewer is able to create a comment to a line in the file, the POST request for adding the comment will be sent from the code manager frontend to addons server developer tool. Generally, reviewers can only select existing lines to create comment on the code manager, but if we use curl command in the terminal to add a draft comment on a non-existent line of code, the comment will be created successfully, which should fail and receive a 401 Bad Request response instead. Therefore, the issue is asking for adding validation on the lineo field (line number of code) to ensure comments can only be created at existing lines of code.

![](./pic/example-draft-comment.png)

example curl command to reproduce issue (notice that the lineo is large and not existed in code repository):

```
curl -X POST \
  'http://localhost:3000/api/v5/reviewers/addon/111/versions/123/draft_comments/?lang=en-US' \
  -H 'authorization: Bearer eyJhdXRoX2hhc2giOiI2NzJhZGEzNzJjNWQ3ZGRiODQzYTliZDhjMmVmZjczMWY3ZjQ5OWUyIiwidXNlcl9pZCI6MX0:1jBR85:06R4eOvmySU-exVMk-aNUWxkPSA' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 6982a2f1-e85a-0b5d-9534-b54d27807c9d' \
  -d '{ "comment":"testing the plus 51 comments", "filename":"manifest.json", "lineno":1029381023 }
'
```

<a name="file2"></a>
#### Which files will involve?

To add validation to the create comment POST request, we probably need these files below:

- `src/olympia/activity/models.py`, which includes the model of DraftComment, and it contains validation rules for each field.

  ```python
  class DraftComment(ModelBase):
      """A model that allows us to draft comments for reviews before we have
      an ActivityLog instance ready.

      This is being used by the commenting API by the code-manager.
      """
      id = PositiveAutoField(primary_key=True)
      version = models.ForeignKey(Version, on_delete=models.CASCADE)
      user = models.ForeignKey(UserProfile, on_delete=models.CASCADE)
      filename = models.CharField(max_length=255, null=True, blank=True)
      lineno = models.PositiveIntegerField(null=True)
      canned_response = models.ForeignKey(
          CannedResponse, null=True, default=None,
          on_delete=models.SET_DEFAULT)
      comment = models.TextField(blank=True)

      class Meta:
          db_table = 'log_activity_comment_draft'

  ```



- `src/olympia/reviewers/serializers.py`, which includes the model of DraftCommentSerializer, and the DraftCommentSerializer contains validation process.

  ```python
  def validate(self, data):
          canned_response = self.get_or_default('canned_response', data)
          comment = self.get_or_default('comment', data)

          if comment and canned_response:
              raise serializers.ValidationError(
                  {'comment': ugettext(
                      'You can\'t submit a comment if `canned_response` is '
                      'defined.')})

          if not canned_response and not comment:
              raise serializers.ValidationError(
                  {'comment': ugettext(
                      'You can\'t submit an empty comment.')})

          lineno = self.get_or_default('lineno', data)
          filename = self.get_or_default('filename', data)

          if lineno and not filename:
              raise serializers.ValidationError(
                  {'comment': ugettext(
                      'You can\'t submit a line number without associating '
                      'it to a filename.')})
          return data
  ```

<a name="diagram2"></a>
#### UML/ER diagrams to illustrate interactions between new and existing codes

Activity app:

![](./pic/activity.png)




<a name="decision"></a>
## Decision making

We select issue #8611(API for voting for user reviews) as the feature which we are going to implement in the next deliverable. First of all, compared to the second issue #12790 (Validate line number in draft comments API) that we have investigated, we prefer to implement new features rather than to fix a huge bug since the bug is not easy to trace. Also, to reproduce the problem, we need some addons that have their code stored on the local server. However, we are unable to upload addons since the validation test keeps failing on local. Although there are some example test addons on the server, their source files are not stored at local, instead, they are on some git repository, this is also why the issue creator uses dev-server to produce the problem instead of local server. In addition, we recognize that implementing new features is easier for us to divide the work, which could let us follow our software development process appropriately. Besides, after analyzing the two issues, all of us have had a clear understanding and basic ideas of the implementation of the new API. Taking all of the reasons above into account, we finally decide to implement the new feature.




<a name="plan"></a>
# Detailed plan

<a name="api"></a>
### Api Design:

#### Vote

This endpoint allows you to vote an existing rating by its id. If successful, a[ rating object](https://addons-server.readthedocs.io/en/latest/topics/api/ratings.html#rating-detail-object) is returned.

```
Note

Requires authentication and Addons:Edit permission or the user account that didn't not post the rating. Even with the right permission, users can't upvote or downvote on a rating from somebody twice.

For each user, if the user sends the same voting action as the previous action to the rating, the previous voting action will be undone.
```

- POST /api/v5/ratings/rating/(*int:* *id*)/votes
  Request JSON Object

- - **vote_action** (*string*) – A constant describing the action.

    | **Constant** | **Description**                       |
    | ------------ | ------------------------------------- |
    | up_vote      | increase the rating vote count by one |
    | down_vote    | decrease the rating vote count by one |

- Response JSON Object

- - **id** (*int*) – The rating id.
  - **addon** (*object*) – A simplified add-on object that contains only a few properties: id, name, icon_url and slug.
  - **body** (*string|null*) – The text of the rating.
  - **is_deleted** (*boolean*) – Boolean indicating whether the rating has been deleted or not.
  - **is_latest** (*boolean*) – Boolean indicating whether the rating is the latest posted by the user on the same add-on.
  - **previous_count** (*int*) – The number of ratings posted by the user on the same add-on before this one.
  - **flags[]** (*object*) – A list of flags the user requesting has previously applied to this rating (that haven’t been processed by moderators already). Only present if show_flags_for parameter sent.
  - **flags.flag** (*string*) – A constant describing the reason behind the flagging.
  - **flags.note** (*string|null*) – A note to explain further the reason behind the flagging if flag was rating_flag_reason_other; null otherwise.
  - **score** (*int*) – The score the user gave as part of the rating.
  - **reply** (*object|null*) – The rating object containing the developer reply to this rating, if any (The fields rating, reply and version are omitted).
  - **version.id** (*int*) – The add-on version id the rating applies to.
  - **version.version** (*string*) – The add-on version string the rating applies to.
  - **user** (*object*) – Object holding information about the user who posted the rating.
  - **user.id** (*string*) – The user id.
  - **user.name** (*string*) – The user name.
  - **user.url** (*string*) – The user profile URL.
  - **user.username** (*string*) – The user username.



##### Upvoting request example

```
curl -X PATCH \

http://localhost:3000/api/v5/ratings/rating/89/votes\

-H 'authorization: Bearer eyJhdXRoX2hhc2giOiIwMGQ1MTY0MTgwN2E2OGFhN2IxMzQ4ZDg3NDkyN2RkMmU4OTU2ODgxIiwidXNlcl9pZCI6MX0:1jEivV:S78-q1olDNW0ksss3aMZ5htq8Go' \

-H 'cache-control: no-cache' \

-H 'content-type: application/json' \

-d '{ "flag": "up_vote" }'
```

##### Downvoting request example

```
curl -X PATCH \

http://localhost:3000/api/v5/ratings/rating/89/votes\

-H 'authorization: Bearer eyJhdXRoX2hhc2giOiIwMGQ1MTY0MTgwN2E2OGFhN2IxMzQ4ZDg3NDkyN2RkMmU4OTU2ODgxIiwidXNlcl9pZCI6MX0:1jEivV:S78-q1olDNW0ksss3aMZ5htq8Go' \

-H 'cache-control: no-cache' \

-H 'content-type: application/json' \

-d '{ "flag": "down_vote" }'
```





<a name="changes"></a>
#### File Changes

- In **models.py**:

  We need to create a new class **RatingsVote**, similar to the original class **RatingFlag** in this file, we are going to create a new database table with some attributes which are used to record the voting operations to a review for a single user. The table includes **id, created, modified, vote_option, review_id and user_id**.

  **models.py**
  ```
    \ new class RatingsVote

    ​    # create a database table with following attributes

        - new attribute): **id**
        - new attribute: **created**
        - new attribute: **modified**
        - new attribute: **vote_option**
        - new attribute: **review_id**
        - new attribute: **user_id**
    ```

- In **permissions.py**:

  In this file, we need to define a new function called **user_can_vote_review,** which takes two parameters: **request**, **vote**. The restrictions are as follows:

  1. The users who are not the author of this add-on can vote.

  2. The users who are not the author of this review can vote.

  3. If the vote has not been flagged, which means the user has not voted, the user can vote (each user can use only either upvote or downvote once).

  Also, similar to class **CanDeleteRatingPermission**, we are going to create a new class **CanVotePermission** and define a function called **has_vote_permission**, which returns the result of the above function **user_can_vote_review**.

  **permissions.py**
  ```
     - new function user_can_vote_review(request**, **vote)

     \ new class CanVotePermission

        - new method has_vote_permission: return user_can_vote_review
  ```

- In **views.py**:

  Under the class **RatingViewSet**, we are going to define a new variable called **vote_permission_classes**, which will handle the permission of **ByHttpMethod**. Since we will use the **'post'** method as to how to update the vote numbers, we will define **'post': CanVotePermission**, which will call the class in **permissions.py** to judge the permission.

  Besides, in this class(**RatingViewSet**), we are going to implement two new functions: one is **check_can_upvote_permission_for_ratings_list**, and the other is **check_can_downvote_permission_for_ratings_list** which are used to check whether or not the current request contains a user that can upvote or downvote.

  Another new function we will implement is **vote**, which is used to check whether a user has been voted or not, and based on the previous voting action, we will give a corresponding response (just like the original function **flag** in line 352).

  **views.py**
  ```
  ​ \ class RatingViewSet

        - new variable: vote_permission_classes = [

  ​                        ByHttpMethod({'post': CanVotePermission }),

                        ]

        - new method check_can_upvote_permission_for_ratings_list

        - new method check_can_downvote_permission_for_ratings_list

        - new method vote
  ```

- In **serializers.py**:

  Under the existence class **BaseRatingSerializer** and its inside class **Meta**, we need to add two more fields in the variable **field**, which should get from the database (‘upvote’, ‘downvote’). Besides, we need a new class **RatingVoteSerializer**, which is used to validate the availability and also convert it to JSON.

  **serializers.py**
  ```

    \ class BaseRatingSerializer

  ​  \ class Meta

  ​      - variable **fields** = (... ,‘upvote’, ‘downvote’ ...)

    \ new class **RatingVoteSerializer**

        - new function to_representation

        - new function validate
  ```





<a name="acceptance"></a>
## Acceptance test

Since this new voting feature implementation does not include front-end elements, it is not very straightforward to test the new implementation. Thus, we would use curl or Postman to send requests and verify the data contained in the response. The acceptance tests will be listed in scenarios that use curl or Postman to send requests. The specific acceptance test cases are:

1. When the user is not logged-in (in curl or Postman requesting case, it means no authentication token), a 403 response should be returned.

2. When the user is logged-in (in curl or Postman requesting case, it means with authentication token) and a **upvote request** is sent,

3. 1. if the user is the author of the add-on, a 403 response should be returned

   2. if the user is the author of the rating, a 403 response should be returned

   3. if the user is neither the author of the add-on nor the author of the rating and

   4. 1. the user did not vote for the rating, then the request should successfully go through and a 200 response with upvoting data increased by 1 should be returned
      2. the user voted for the rating as upvote before, then the request should successfully go through and a 200 response with upvoting data decreased by 1 should be returned (meaning undo upvote)
      3. the user voted for the rating as downvote before, then the request should successfully go through and a 200 response with upvoting data increased by 1 and downvoting data decreased by 1 should be returned (meaning change downvote to upvote)

4. When the user is logged-in (in curl or Postman requesting case, it means no authentication token) and a **downvote request** is sent,

5. 1. if the user is the author of the add-on, a 403 response should be returned

   2. if the user is the author of the rating, a 403 response should be returned

   3. if the user is neither the author of the add-on nor the author of the rating and

   4. 1. the user did not vote for the rating, then the request should successfully go through and a 200 response with downvoting data increased by 1 should be returned
      2. the user voted for the rating as downvote before, then the request should successfully go through and a 200 response with downvoting data decreased by 1 should be returned (meaning undo downvote)
      3. the user voted for the rating as upvote before, then the request should successfully go through and a 200 response with downvoting data increased by 1 and upvoting data decreased by 1 should be returned (meaning change upvote to downvote)




<a name="architecture"></a>
# Architecture Review

This project uses Django framework, which is a framework that works ideally with databases. When a request is sent to the backend, Django first resolves its url, then View will handle the digested request by interacting with models and serializers. Model not only plays a role as a bridge that allows communication flows between View and Manager, but also it is responsible for creating/modifying/deleting entities that are stored in the database. Manager handles queries that modify the database. For instance, a typical life cycle in Django would be the following: when a GET request was received by View, it will then activate the Manager to handle certain queries that get what the request needs from the database. Once Views have the response ready, it would activate the serializer to pack the response into a json object. Then send back the json object to the frontend.

Picture is  too big…[click here to look closer](https://github.com/CSCD01/team_02-project/blob/master/pic/Project.png)

![](./pic/Project.png)



From the ER table we can tell how each model interacts with others. There are two main entities(tables) in the database: UserProfile and Addon. Many other entities uses one or many fields in these two entities as foriegner key. We are planning to create a new entity for the Issue we choose in this deliverable, the entity would include fields like user id, addon id, voting flag, etc, within these fields, user id and addon id would be foreigner keys that inherited from UserProfile and Addon.
