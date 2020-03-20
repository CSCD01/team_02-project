```
curl 'https://addons.mozilla.org/api/v5/reviewers/addon/111/versions/123/draft_comments/?lang=en-US' \
-XPOST \
-H 'Accept: */*' \
-H 'Content-Type: application/json' \
-H 'Origin: http://localhost:9001' \
-H 'Content-Length: 56' \
-H 'Accept-Language: en-ca' \
-H 'Host: addons.mozilla.org' \
-H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.1.1 Safari/605.1.15' \
-H 'Referer: http://localhost:9001/iframe.html?id=codeview--mime-type-application-javascript&viewMode=story' \
-H 'Accept-Encoding: br, gzip, deflate' \
-H 'Connection: keep-alive' \
--data-binary '{"comment":"DSA","filename":"manifest.json","lineno":14}'
```

'https://addons.mozilla.org/api/v5/reviewers/addon/111/versions/123/draft_comments/?lang=en-US

## Issue2 Validate line number in draft comments API #12790

#### Description

The Mozilla addons server allows developers upload and public their own addons. However, due to security and reliability concerns, addons are required to be reviewed by addon reviewers, which include content(code) review. The procosse is done through addons server reviewers tool, and also, addons code manager(another frontend). During the reviewer reading the addon's content(code), the reviewer is able to create draft comments on a line of a file, the POST request will be sent from the code manager frontend to addons server developer tool. Generally, reviewer can only select existd lines to create comment on the code manager, but if we use curl comment to attempt to leave a draft comment on a non-existed line of code, the comment will be created successfully, which should be failed and received 401 Bad Request response instead. Therefore, the issue is asking for adding validation on the `lineo` field (line number of code) to ensure comments can only be created at existed lines of code.

![example-draft-comment](/Users/samuel/Desktop/example-draft-comment.png)

example curl command to reproduce issue (notice that the lineo is large and not existed in code repository):

```curl
curl -X POST \
  'http://localhost:3000/api/v5/reviewers/addon/111/versions/123/draft_comments/?lang=en-US' \
  -H 'authorization: Bearer eyJhdXRoX2hhc2giOiI2NzJhZGEzNzJjNWQ3ZGRiODQzYTliZDhjMmVmZjczMWY3ZjQ5OWUyIiwidXNlcl9pZCI6MX0:1jBR85:06R4eOvmySU-exVMk-aNUWxkPSA' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 6982a2f1-e85a-0b5d-9534-b54d27807c9d' \
  -d '{ "comment":"testing the plus 51 comments", "filename":"manifest.json", "lineno":1029381023 }
'
```



#### Which files will involve?

To add validation to the create comment POST request, we probably need to files below:

`src/olympia/activity/models.py`, which includes the model of DraftComment, and it contains validation rules for each field.

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



`src/olympia/reviewers/serializers.py`, which includes the model of DraftCommentSerializer, and the DraftCommentSerializer contains validation process .

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



#### UML/ER diagrams to illustrate interactions between new and existing codes

![activity](/Users/samuel/activity.jpg)

Decision makingDetailed planAcceptance testReference link: https://openclassrooms.com/en/courses/4544611-write-agile-documentation-user-stories-acceptance-tests/4810081-writing-acceptance-tests











### Vote

This endpoint allows you to vote an existing rating by its id. If successful a [rating object](https://addons-server.readthedocs.io/en/latest/topics/api/ratings.html#rating-detail-object) is returned.

> Note
>
> Requires authentication and and Addons:Edit permission or the user account that didn't not post the rating. Even with the right permission, users can not upvote or downvote pn a rating from somebody twice.
>
> For each user, if sends the same voting action as previous action to the rating, the previous voting action will be undo.

- `PATCH /api/v5/ratings/rating/`(*int:* *id*)`/votes`

  Request JSON Object 

  - **vote_action** (*string*) – A constant describing the action.

    | Constant  | Description                           |
    | --------- | ------------------------------------- |
    | up_vote   | increase the rating vote count by one |
    | down_vote | decrease the rating vote count by one |
    |           |                                       |

  Response JSON Object

  - **id** (*int*) – The rating id.
  - **addon** (*object*) – A simplified [add-on](https://addons-server.readthedocs.io/en/latest/topics/api/addons.html#addon-detail-object) object that contains only a few properties: `id`, `name`, `icon_url` and `slug`.
  - **body** (*string|null*) – The text of the rating.
  - **is_deleted** (*boolean*) – Boolean indicating whether the rating has been deleted or not.
  - **is_latest** (*boolean*) – Boolean indicating whether the rating is the latest posted by the user on the same add-on.
  - **previous_count** (*int*) – The number of ratings posted by the user on the same add-on before this one.
  - **flags[]** (*object*) – A list of flags the  user requesting has previously applied to this rating (that haven’t been processed by moderators already). Only present if `show_flags_for` parameter sent.
  - **flags.flag** (*string*) – A [constant](https://addons-server.readthedocs.io/en/latest/topics/api/ratings.html#rating-flag-constants) describing the reason behind the flagging.
  - **flags.note** (*string|null*) – A note to explain further the reason behind the flagging if `flag` was `rating_flag_reason_other`; null otherwise.
  - **score** (*int*) – The score the user gave as part of the rating.
  - **reply** (*object|null*) – The rating object containing the developer reply to this rating, if any (The fields `rating`, `reply` and `version` are omitted).
  - **version.id** (*int*) – The add-on version id the rating applies to.
  - **version.version** (*string*) – The add-on version string the rating applies to.
  - **user** (*object*) – Object holding information about the user who posted the rating.
  - **user.id** (*string*) – The user id.
  - **user.name** (*string*) – The user name.
  - **user.url** (*string*) – The user profile URL.
  - **user.username** (*string*) – The user username.

Upvoting request example

```
curl -X PATCH \
  http://localhost:3000/api/v5/ratings/rating/89/votes\
  -H 'authorization: Bearer eyJhdXRoX2hhc2giOiIwMGQ1MTY0MTgwN2E2OGFhN2IxMzQ4ZDg3NDkyN2RkMmU4OTU2ODgxIiwidXNlcl9pZCI6MX0:1jEivV:S78-q1olDNW0ksss3aMZ5htq8Go' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{ "flag": "up_vote" }
'
```

Downvoting request example

```
curl -X PATCH \
  http://localhost:3000/api/v5/ratings/rating/89/votes\
  -H 'authorization: Bearer eyJhdXRoX2hhc2giOiIwMGQ1MTY0MTgwN2E2OGFhN2IxMzQ4ZDg3NDkyN2RkMmU4OTU2ODgxIiwidXNlcl9pZCI6MX0:1jEivV:S78-q1olDNW0ksss3aMZ5htq8Go' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{ "flag": "down_vote" }
'
```

