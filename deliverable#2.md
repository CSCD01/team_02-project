

# CSCD01 PROJECT DELIVERABLE#2

## **Table of Contents**
1. [Steps To Run Local Build Of Mozilla Addons Server](#build)
2. [Example2](#example2)
3. [Third Example](#third-example)
4. [Fourth Example](#process)


## **Steps To Run Local Build Of Mozilla Addons Server** <a name="build"></a>

1. Clone code base from [team_02 repo]( https://github.com/CSCD01/addons-server-team02 )
2. Go into addons-server-team02 directory
3. Pull docker images with `docker-compose pull`
4. Start up containers using `docker-compose up -d`
5. Run initialization (including create admin account that would be used in local build) with $ make initialize
6. Add `127.0.0.1 olympia.test` to /etc/hosts/ in order to have proper CSS applied on the local build, /etc/hosts need to be modified before the actual running (sudo need to be applied for editing /etc/hosts/)
7. Using `docker-compose stop` if containers are already running
8. Run `docker-compose up -d` again to apply changes on hosts
9. Open Firefox and go to `127.0.0.1:3000`



## **Five Issues Investigation Reports**

####  1. Issue12842 [Drop is_source_public property from Addon model](https://github.com/mozilla/addons-server/issues/12842)

##### **The problem:**

Due to the security reason, the authors of the Add-on server wanted to remove the link to the source code from the product page, such that prevent users to access the source code from Mozilla, instead the contributors should publish the source code on external sites like Github. However, after they’ve removed the View Source link, the property `is_source_public` left in `./src/olympia/addons/serializers.py` became unused and should be removed as well.

##### **Investigation:**

 The property appears in the two files:` ./addons/serializers.py` and `./addons/test/test_serializers.py`. We need to remove the property from these two files.

##### **Estimate amount of work:**

1 day to examine the code base, and determine which files to modify. 1 day to implement the fix.

##### **Fixing plan:**

Have the is_source_public property removed from serializer.py

##### **Files to change:**

In `src/olympia/addons/serializers.py`:

![](./pic/diff_serializer.png)

In `src/olympia/addons/tests/test_serializers.py`:

![](./pic/diff_test_serializer.png)

##### **Test:**

After this property is deleted, run the modified `test_serializer.py` to make sure the removal of is_source_public property in serializer.py does not affect the functionality of the code. Moreover, we test the whole `./olympia/addons` to make sure the modification does not introduce new errors.

##### **Test Result**

Test result on Master:

![](./pic/test_result_master.png)

Test result on Branch:

![](./pic/test_result_branch_12842.png)





#### 2. Issue 12790 [Validate line number in draft comments API](https://github.com/mozilla/addons-server/issues/12790 )

##### **The problem:**

All addons that are submitted to upload and publish by users are required to be reviewed and approved by reviewers. When a reviewer reviews a specific version of an addon, the reviewers can create a draft comment on a line of the addon source file, which is sent through API:

```
POST /api/v4/reviewers/addon/(*int: addon_id*)/versions/(*int:version_id*)/draft_comments/
```

The body of this API call includes a field: `lineo`, it’s the line number that the comment related to (for git diff). When a body with non-existed lineo(i.e, excess the largest line number in the file) is sent, the response would be `comment created`, with status `201`. However, the correct response should be a status `401 bad request` that tells the caller such lineo does not exist, which means the line number validation is somehow missing in draft comments API endpoint.

##### **Investigation:**

Since we are unable to upload our example addons to the local server, it’s hard to reproduce the problem exactly like the issue description. Although we can reproduce the issue by creating new test cases, it is still different to the problem at production and development environments. Also, we found that this issue is also related to the repo Mozilla Code Manage, which is a separate repo and app. To fix and continue investigating this issue, we need to set up both addons-server and code-manager on our local machine, which may take a long time(at least a few days to properly set up the environment).

##### **Estimate amount of work:**

3 - 5days to set up addons-server and code-manager on our local machine, 2 days to get familiar with both two code bases regarding the issue portion. Extra 2 days to design and implement the fix, and 1 days to design and implement unit tests and acceptance tests.

##### **Test Scenario:**

```
command:curl -X POST \
http://localhost:3000/api/v4/reviewers/addon/{addon_id}/versions/{version_id}/draft_comments/ \
 -H 'authorization: Bearer eyJhdXRoX2hhc2giOiI2NzJhZGEzNzJjNWQ3ZGRiODQzYTliZDhjMmVmZjczMWY3ZjQ5OWUyIiwidXNlcl9pZCI6MX0:1jBR85:06R4eOvmySU-exVMk-aNUWxkPSA' \
 -H 'cache-control: no-cache' \
 -H 'content-type: application/json' \
 -d '{ "comment":"testing the plus 51 comments", "filename":"manifest.json", "lineno":1029381023 }'
```

1. When we made the above example curl command, the response object body should contain status 401.
2. When we made  the above example curl command, the response object body should contain status 201.
3. In unit test suite, when a draft comment create request with non-existed lineo in the body is received, check the response status, it should be 401.
4. In unit test suite, when a draft comment create with a valid lineo in the body is received, check the response status, it should be 201, and the comment should be created.



#### **3. Issue #13508 [Deleting a group fails](https://github.com/mozilla/addons-server/issues/13508)**

The reason that we choose this issue to fix is because this 'Group function’ is common to admins who want to add new groups and attach users to certain groups and of course if no use, delete the groups. Besides, the issue is obvious and it is easy to understand and to trace the source code to get the logic of the code.

##### **The problem:**

 After login as Admin, go to localhost:3000/admin/models/ or http://olympia.test/en-US/admin/models/, inside ACCESS>Groups.

1. create a user group by clicking on ADD GROUP
2. add some user/users to it
3. save the group
4. try to delete the group and confirm the deletion

The webpage generates an error: '*Cannot delete or update a parent row: a foreign key constraint fails

```
'Cannot delete or update a parent row: a foreign key constraint fails (`olympia`.`log_activity_group`, CONSTRAINT `log_activity_group_group_id_e03ab8c8_fk_groups_id` FOREIGN KEY (`group_id`) REFERENCES `groups` (`id`))'
```

##### **Investigation and Fixing plan**:

There are two tables related to this error: `groups`, `log_activity_group`. And their relations are shown as follows:

- `group_id` in `log_activity_group` references id in groups

#### In this scenario, when we try to delete a row in the parent table `groups`, the other table `log_activity_group` prohibits this action due to foreign key constraints. (we need to delete the row in the children table first in order to delete the row in the parent table) Unless we explicitly state the “**on delete cascade**” condition.


After carefully looking into the source code of the apps “access”, “activity” and mysql database of the project. We found that the ORM (object relational mapping) in Django is coding correctly with foreign key fields having `on_delete=models.CASCADE` conditions in **activity-**>**models.py**.

```python
class Group(ModelBase):
    # If `id` is changed from PositiveAutoField, update TestPositiveAutoField.
    id = PositiveAutoField(primary_key=True)
    name = models.CharField(max_length=255, default='')
    rules = models.TextField()
    users = models.ManyToManyField(
        'users.UserProfile', through='GroupUser', related_name='groups')
    notes = models.TextField(blank=True)

    class Meta:
        db_table = 'groups'

    def __str__(self):
        return self.name
```

```python
class GroupLog(ModelBase):
    """
    This table is for indexing the activity log by access group.
    """
    id = PositiveAutoField(primary_key=True)
    activity_log = models.ForeignKey('ActivityLog', on_delete=models.CASCADE)
    group = models.ForeignKey(Group, on_delete=models.CASCADE)

    class Meta:
        db_table = 'log_activity_group'
        ordering = ('-created',)
```

However, when Django makes migrations in the “activity” app to the mysql database, even though Django states `on_delete=models.CASCADE` condition, somehow this was not captured in mysql. As a result, it gave this error.

##### **Estimate amount of work:**

1.5 days to get familiar with the code base regarding the issue portion. Another 3 days to learn ORM, how migration and custom migration work in Django.

##### **Fixing plan**:

I have to execute a custom SQL in Django migration in order to make sure mysql includes `on delete cascade` condition in the foreign key constraint in table `log_activity_group`. Below are the steps I performed to the fix:

1. Run the docker and `make initialize` to the project.

2. Under project directory, type `make shell` to get inside the Docker container.

3. Type `python manage.py makemigrations activity --empty -n added_ondelete_cascade` to create a custom empty migration named “0005_added_ondelete_cascade” under activity app.

4. Add code as following:

   ```python
   from django.db import migrations


   class Migration(migrations.Migration):

       dependencies = [
           ('activity', '0004_auto_20191125_1659'),
       ]

       operations = [
           migrations.RunSQL(
               "alter table log_activity_group drop foreign key log_activity_group_group_id_e03ab8c8_fk_groups_id",
               "alter table log_activity_group add foreign key (group_id) references groups(id) on DELETE CASCADE"
           )
       ]

   ```



5. Type `python manage.py migrate activity` to run the migration.

6. Done



##### **Potential risk of not correctly fixed:**

Because my fix is only involving the database and there are only three tables to be potentially affected. If my fix is not the desired solution, the data in `log_activity_group`, `groups_users` and ‘groups’ tables might not be populated correctly. For example, when a row is deleted in ‘groups’ table, the rows in `log_activity_group` and `groups_users` tables might not be updated/deleted to reflect the changes in their parent tables. Hence, the UI won’t show fields in Users, Groups and Activity properties properly.

##### **Test**:

User Acceptance Test:

I have performed 3 tests:

1. When creating a group with no users included, and then deleted it. -- no errors since this was working originally.
2. When creating a group with 1 user included, and then deleted. -- no errors and the group was deleted successfully.
3. When creating a group with multiple users(2-3) included, and then deleted. -- no errors and the group was deleted successfully.

At the meantime, before and after the deletion, I checked the three tables from SQL:

1. Under project directory, type `make shell` to get inside the Docker container.
2. Type `python manage.py dbshell` to get into mysql database command line.
3. Type `select * from log_activity_group;` to see what’s inside `log_activity_group` table
4. Type `select * from groups_users;` to see what’s inside `groups_users` table
5. Type `select * from groups;` to see what’s inside `groups` table

Unit Test:

Existing unit tests are executed after fixing. No new errors has been prompted.



#### **4. Issue #13200 [Admin tools - Log out results into a 500 server error](https://github.com/mozilla/addons-server/issues/13200)**

##### **The problem:**

On Admin Tools, there is a button “LOG OUT” at the right top side. When a logged-in admin clicks this button, it redirects the webpage to an unexpected URL: [http://](http://localhost:3000/en-CA/firefox/)[olympia.test](http://olympia.test/en-CA/admin/)[/en-CA/firefox/](http://localhost:3000/en-CA/firefox/). However, the expected redirected URL is http://olympia.test/en-CA/admin/. The purpose of fixing this bug is to change the redirect URL from the logout function in admin tools.

Note: [olympia.test](http://olympia.test/en-CA/admin/) is the domain of local build (same as localhost:3000)

The original issue and its reproducing steps are shown below:

<img src="./pic/admin.gif" />

##### **Steps to reproduce the issue:**

1. Running local build of Mozilla Addons-Server (refer to the Steps To Run Local Build)
2. Go to`localhost:3000` or `olympia.test:3000`
3. Login in to your account
4. Go to http://olympia.test/en-CA/admin/ and hover on “Site Settings and Environment” and then click “Django admin pages.” (Or you can access http://olympia.test/en-CA/admin/models/ directly)
5. Click the button “LOG OUT” at the right top side
6. Have a look of the redirected URL

##### **Investigation** **and** **Fixing plan:**

From my investigation, I found that the source code of the amo admin page is located on src/olympia/zadmin. During the inspection on urls.py and view.py, there does not exist an URL and implemented a function for logout, which means that when we click the logout button to reach http://olympia.test/en-CA/admin/models/logout, it called the Django admin build-in log out function. Since the build-in function will redirect the web page to its root '/,' it is reasonable that it jumps to the original add-ons page([http://](http://localhost:3000/en-CA/firefox/)[olympia.test](http://olympia.test/en-CA/admin/)[/en-CA/firefox](http://localhost:3000/en-CA/firefox/)/). What we want is that we could override the method and redirect the URL to 'zadmin.home' which could be written as `redirect(reverse('admin:index'))`

The problem is that we cannot override the logout method in urls.py and view.py directly. There might be two ways to fix it. The first way is that we go to the python packages directory and find the Django-admin templates in Django/contrib/admin/templates/admin. Then we modify the logout function in base.html directly. However, this solution is not proper because addons-server is an open-source project. The changes in standard libraries like Django would make it hard for other contributors to continue contributing, which would potentially make things more complicated and miss the point of issue fixing. Another way of doing this is to override specific Django-admin templates by putting them in the templates directory in the hierarchy like templates/admin/[app-name]/[template-name]. The impact of this change will be limited only to our admin part. No other part of the project would be impacted, which would highly reduce the risks of side-effects.

##### **Estimate amount of work:**

As I mentioned above, if we use the second way to fix it, we need to figure out how the page generates these `<a>` tags on the website. Even though we know that these are built by Django templates, there are a lot of different design choices made by the developers, which make the source codes, unlike the standard templates. I have already spent two days of investigation. The rest of the work is to find how the buttons are generated by format_html and which part of the templates should be modified. That may take 2-3 days of work. Furthermore, the time for implementing new codes might be 2-3 days. Besides, the part of testing may only cost about 1 day because we could base on existing test cases for example

```python
def test_django_admin_logout(self):
   url = reverse('admin:logout')
   response = self.client.get(url, follow=False)
   self.assert3xx(response, '/', status_code=302)
```

So the whole working progress needs at least 5 days or more.



#### **5. Issue #8262 ["More Actions" is too far down on the review page](https://github.com/mozilla/addons-server/issues/8262)**

##### **The problem:**

On Reviewer Tools, there are two commenting sections called “Whiteboard” and “Private Whiteboard.” Both of them are located in the middle of the page vertically. Usually, there is no or only one line of comment on each of these two sections. There is another section called “More Actions” following the two boards. The problem is that users find even if there are few or even no content on these two boards, the two boards are still 200px in height for each, which could fit in about 10 lines of contents. The fact is that the functionalities wrapped in the “More Actions” section are more frequently used than these two boards. Users feel cumbersome if they want to use the functionalities under the “More Actions” section.

Note: The original issue and its reproducing steps are shown below:

<img src="./pic/more_action1.gif" />

##### **Steps to reproduce the issue:**

1. Running local build of Mozilla Addons-Server (refer to the Steps To Run Local Build)
2. Go to `localhost:3000` or `olympia.test:3000`
3. Log in as an admin user
4. Click on “Get the extension” on home page
5. Scroll down a little bit and select “Content review add-on” on the left column under “More information” section, the Reviewer Tools page will be shown
6. Scroll down, both boards and “More Actions” section will both be shown

##### **Fixing plan:**

1. Firstly, we need to recreate the issue on Firefox.
2. Then, we should use the Inspect Element tool to help us locate the template which produces the Reviewer Tools page.
3. With the correct template file and Inspect Element tool, we can then target the CSS files that control the style of the whiteboard and whiteboard private elements on the web page.
4. In the CSS file, add a new default style (collapsed height) for empty boards. (`<id>:empty{}`)
5. After editing the CSS file, the .js files that are imported to the template file need to be located with the help of the Inspect Element tool as well.
6. With the .js file, we would then need to find the correct js file that can be used to insert new js-related functionalities (adjusting textarea height based on different clicking actions).
7. The last step is to fix the js-related functionalities in order to get everything fixed.

##### **Estimate amount of work:**

About one day to get familiar with templates, CSS files, and .js files, which would be involved in this change. Approximately one more day for the actual changing and testing. We probably need to spend one or two more days if automated UI testing is required because we do not have any experience with automatic UI testing.



## **Issue Decision Arguments**

With the five issues that we have investigated, we ordered them by their estimated amount of work, potential risks, and expected difficulties. These issues took each of us several days to investigate. When we had the meeting for deciding which issues we would finally try to fix, we have around five days left before the due date.

Considering about the estimated amount of work, issue #12790 and #13200 would take at least five days. Thus, these two issues were removed from the candidate list automatically.

Since issue #8262 is mainly about JS and CSS fixing for elements on templates, the risk of fixing the issue but breaking other parts is very low. Meanwhile, issue #12842 is about removing properties no longer needed. So, issue #12842 may have relatively low risk of breaking working parts as well. For issue #13508, the group deleting functionalities involve several database tables. The risk of this issue would be the potential relation-breaking of these involved tables. Thus, the risk of this issue is relatively higher than the other two issues mentioned before.

Considering about the difficulties, issue #8262 and #12842 are relatively easy. The difficulty of issue #13508 is higher than those two but still a fair one to fix.

Combining the facts of estimated amount of work, potential risks, and expected difficulties, we come to an agreement that we would be able to finish all of these three issues on time.



## **Fixed Issues**

### **1. Issue #8262 in Details**

#### **Changed Parts**
Under branch [8262-review_page_whiteboard_fix]( https://github.com/CSCD01/addons-server-team02/tree/8262-review_page_whiteboard_fix )
1. `static/css/zamboni/reviewers.less`

  Added textarea:empty for default empty style

2. `static/js/zamboni/reviewers.js`

Added click listener for whiteboard and private whiteboard in order to have expanding functionalities for these two boards. Also added click listener for elements other than the two boards in order to collapse back.

#### **How Changes Affect The Project**

The changes above will only affect the whiteboard and private whiteboard. No other elements on the template will be impacted.

#### **Fixed result:**

<img src="./pic/more_action2.gif" />

#### **Acceptance Test Criterias**

1. When whiteboard or private whiteboard is originally empty, it should be collapsed to a single-line height (20px).
2. When whiteboard or private whiteboard is not originally empty, it should be expanded to a ten-line height (200px).
3. When an empty whiteboard or private whiteboard is clicked, it should be expanded to a ten-line height (200px).
4. When an empty whiteboard or private whiteboard is selected, and the user chooses to click on somewhere else without adding any content, the whiteboard or private whiteboard should collapse back to a single-line height (20px).
5. When a non-empty whiteboard or private whiteboard is cleaned up, it should collapse back to a single-line height (20px).
6. The height of the whiteboard or private whiteboard should be consistent after any saving.

#### **Steps to Test**

1. Refer to **Steps To Run Local Build Of Mozilla Addons Server** if this is the first time of running local build
2. Go to localhost:3000 / 127.0.0.1:3000 and login with admin account that created in process of $ make initialize
3. Click “Get the extension” on home page
4. Scroll down a little bit and select “Content review add-on” on the left column under “More information” section, the Reviewer Tools page will be shown
5. Scroll down, both boards and “More Actions” section will both be shown
6. Test different acceptance test cases on the page by saving and deleting the contents on the two boards



### **2. Issue #12842 in Details**

#### The problem

Due to the security reason, the authors of the Add-on server wanted to remove the link to the source code from the product page, such that prevent users to access the source code from Mozilla, instead the contributors should publish the source code on external sites like Github. However, after they’ve removed the View Source link, the property is_source_public left in ./src/olympia/addons/serializers.py became unused and should be removed as well.

#### Changed Parts
Under branch [12842-drop-is_source_public-property]( https://github.com/CSCD01/addons-server-team02/tree/12842-drop-is_source_public-property )

1. `./src/olympia/addons/serializer.py`

  Remove property is_source_public

2. `./src/olympia/addons/tests/serializer.py`

Remove corresponding test case for the unused property is_source_public

#### How Changes Affect The Project

As the unused property is deleted, the code becomes more clean and less confusess.

#### Acceptance Test Criterias

Technically we could not design an acceptance test to test an unused property. Instead, after this property is deleted, run the modified test_serializer.py to make sure the removal of is_source_public property in serializer.py does not affect the functionality of the code. Moreover, we test the whole ./olympia/addons to make sure the modification does not introduce new errors.

#### Steps to Test

1. Refer to **Steps To Run Local Build Of Mozilla Addons Server** if this is the first time of running local build
2. In docker terminal, under the `./olympia/addons/tests` run all test cases
3. Compare the test result for master and the branch



### **3. Issue #13508 in Details**

#### The problem

After logging as Admin, and creating a new group with users inside, but then can't delete the group.
#### Changed Parts

 `./src/olympia/activity/migrations/0005_added_ondelete_cascade.py`

  added a custom migration file named “0005_added_ondelete_cascade”

 
#### How Changes Affect The Project

As running the new migration file to mysql database from Django, the group with users can be deleted successfully without error.

#### Acceptance Test Criterias

1. When creating a group with no users included, and then deleted it. -- no errors since this was working originally.
2. When creating a group with 1 user included, and then deleted. -- no errors and the group was deleted successfully.
3. When creating a group with multiple users(2-3) included, and then deleted. -- no errors and the group was deleted successfully.

#### Steps to Test
Unit test:
1. Refer to **Steps To Run Local Build Of Mozilla Addons Server** if this is the first time of running local build
2. Performed test `python manage.py test src/olympia/access`
3. Performed test `python manage.py test src/olympia/activity`
4. Compare the test result for master and the branch

User Acceptance test:

5. After login as Admin, go to localhost:3000/admin/models/ or http://olympia.test/en-US/admin/models/, inside ACCESS>Groups.
6. Create a user group by clicking on ADD GROUP
7. Add some user/users to it and save the group
8. Try to delete the group and confirm the deletion


<a name="process"></a>
## Software Development Process

During the working process of deliverable2, we used the Kanban board as our tool. We designed the board (refer to d1) with 6 columns, which included todo, in progress, code review1, code review2, QA testing, and done. The new tasks that needed to be done were placed in “to do.” Once a person picked up a task from “to do,” he/she assigned his/her name on that card and then switched the card into the next column “in progress.” However, when one of us selected the card in other columns, he/she assigned his/her name directly. After finishing one task, we unassigned our name on it and then moved it to the next block. Our rule was that one team member’s name could only appear twice at the same time(one is “in progress,” the other is in the blocks which are not “in progress”).



Actually, we did meet some problems when using our kanban board. The way that we chose to treat with issue spikes is not clear at the early time. Some of us thought that once we finished the assigned spike, the task card could be moved into the “done” column directly since there is no need to do a code review and also QA Testing. However, this defeated the purpose of using a kanban board and might let other members confuse. Then, we agreed on a solution to handle these spikes in a meeting. Since everyone had a report of his/her spike, we decided to consider a code review column as a column for proofreading as well as QA testing as a grammar check. Finally, we solved this problem and strictly followed our rules for the whole process in deliverable2.
