# CSCD01 PROJECT DELIVERABLE#4

## **Table of Contents**

1. **[Design Of Feature](#feature)**

2. **[User Guide](#guide)**

3. **[Design Of Code](#code)**

4. **[Acceptance Test](#acceptance)**

   - [Test GET Requests](#testget)

   - [Test POST Requests](#testpost)

5. **[Unit Test](#unit)**

6. **[Software Development Process](#process)**



<a name="feature"></a>

## Design Of Feature

### Overview

Add-on rating is one of the most important features of AMO. For each add-on on the Mozilla Add-on Market, user reviews are displayed on the All Review section, however, currently there is no API supporting users to upvote/downvote a review, thus our team would implement the voting API, which allows users to target the most useful review more easily.

### Goals

Implement an API that allows users to vote for a review as helpful or not.Users could not vote for their own review, nor their own add-on(s).

### API calls

##### POST /api/v4/ratings/rating/(*int:* *id*)

| Endpoint | What it does                                                 |
| -------- | ------------------------------------------------------------ |
| /vote    | This endpoint allows you to vote an existing rating by its id, upon success, a rating object will be returned (https://addons-server.readthedocs.io/en/latest/topics/api/ratings.html#rating-detail-object ) |



##### GET /api/v4/ratings/rating/

| Endpoint       | What it does                                                 |
| -------------- | ------------------------------------------------------------ |
| /(*int:* *id*) | This endpoint allows you to get a vote status for an existing voting by its id, upon success, a rating object will be returned (https://addons-server.readthedocs.io/en/latest/topics/api/ratings.html#rating-detail-object ) |



### Fields reference

##### Request

| Field Name     | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| id             | Int representation for a specific voting                     |
| addon          | Int representation for a specific addon                      |
| show_votes_for | Integer representation<br/>0: don’t show detail for voting<br/>1: show counts for upvote and downvote |
|                |                                                              |

##### Respond

| Field Name | Description                                                  |
| ---------- | ------------------------------------------------------------ |
| id         | Id for a rating vote                                         |
| addon      | Foreign key representing an addon id                         |
| rating     | Foreign key representing an rating id                        |
| user       | Foreign key representing an user id                          |
| created    | Time stamp shows when this vote is created                   |
| modified   | Time stamp indicates the last modification                   |
| vote       | Integer representation for a vote<br/>0: downvote<br/>1: upvote<br/>-1: not voted |



<a name="guide"></a>

## User Guide

Step 1: prepare the environment

- Clone the code from our team repo(https://github.com/CSCD01/addons-server-team02)
- Install with docker(https://addons-server.readthedocs.io/en/latest/topics/install/docker.html)
- Add `127.0.0.1 olympia.test` to `/etc/hosts/` in order to have proper CSS applied on the local build, `/etc/hosts` need to be modified before the actual running (sudo need to be applied for editing `/etc/hosts/`)
- On the web browser, visit `localhost:3000`, which will automatically redirect you to http://olympia.test/en-US/firefox/

![](./pic/ug_step1.png)

Step 2: grant access token, the easier way to get the authentication is through frontend(https://addons-server.readthedocs.io/en/latest/topics/api/v3_legacy/auth.html)

- On the top right corner, register/log in
- Upon successful login, open the web inspector. In the Network section, inspect the endpoint /api/v3/accounts/authenticate/. The token is available in the cookie called frontend_auth_token

![](./pic/ug_step2.png)

Step 3: make API call

*below we’re using a mock Bearer token, while users should create their own Bearer token by following step 2*

1. Upvote an review

   1. Execute the api
    ```
   curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/10/vote/?lang=en-CA&wrap_outgoing_links=true' \
   --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiI5ZDZhNjgwMDRiODAyOTc1Nzg2ZmYwYTVmMmY0YzI5NDkyMDk3MTBjIiwidXNlcl9pZCI6MTEyMDN9:1jK9c6:WOLrKX0S_SSbwaRMBZcfBg6st-s' \
   --header 'Cookie: multidb_pin_writes=y' \
   --form 'vote=1'
    ```

   2. A Json object should be return and contain the following:
   ```json
   {
   "vote":1,
   "rating":{
      "id":10,
      "addon":{
         "id":2,
         "slug":"food-truck-chocolate",
         "name":{
            "en-US":"Food Truck Chocolate",
            "es":"(español) Food Truck Chocolate",
            "fr":"(français) Food Truck Chocolate"
         },
         "icon_url":"http://olympia.test/static/img/addon-icons/video-64.png"
      },
      "body":"Test Review 5",
      "created":"2020-04-02T23:46:50Z",
      "is_deleted":false,
      "is_developer_reply":false,
      "is_latest":true,
      "previous_count":0,
      "user":{
         "id":10979,
         "name":"testuser-dVX9qVmwLwkR@example.com",
         "url":null,
         "username":"testuser-dVX9qVmwLwkR@example.com"
      },
      "score":1,
      "reply":null,
      "version":null
   },
   "user":{
      "id":11203,
      "name":"Firefox user 11203",
      "url":"http://olympia.test/en-CA/firefox/user/11203/",
      "username":"anonymous-f99a771a6d949fff6806562851ae0080"
   },
   "addon":{
      "id":2,
      "slug":"food-truck-chocolate",
      "name":{
         "en-US":"Food Truck Chocolate",
         "es":"(español) Food Truck Chocolate",
         "fr":"(français) Food Truck Chocolate"
      },
      "icon_url":"http://olympia.test/static/img/addon-icons/video-64.png"
   }
   }
   ```
   3. By sending the same request again will undo the vote. The response object will be like the following:
    ```json
    {
       "vote":-1,
       "rating":{
          "id":6,
          "addon":{
             "id":2,
             "slug":"tasty-stew",
             "name":{
                "en-US":"Tasty Stew",
                "es":"(español) Tasty Stew",
                "fr":"(français) Tasty Stew"
             },
             "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
          },
          "body":"Test Review 5",
          "created":"2020-03-27T20:42:04Z",
          "is_deleted":false,
          "is_developer_reply":false,
          "is_latest":true,
          "previous_count":0,
          "user":{
             "id":10979,
             "name":"testuser-S813lZ3MI6n9@example.com",
             "url":"http://olympia.test/en-CA/firefox/user/10979/",
             "username":"testuser-S813lZ3MI6n9@example.com"
          },
          "score":2,
          "reply":null,
          "version":null
       },
       "user":{
          "id":1,
          "name":"Firefox user 1",
          "url":"http://olympia.test/en-CA/firefox/user/1/",
          "username":"MonsterAlan"
       },
       "addon":{
          "id":2,
          "slug":"tasty-stew",
          "name":{
             "en-US":"Tasty Stew",
             "es":"(español) Tasty Stew",
             "fr":"(français) Tasty Stew"
          },
          "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
       }
    }
    ```

2. Downvote an review
   1. Execute the api
    ```
    curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/?lang=en-CA&wrap_outgoing_links=true' \
    --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiI5ZDZhNjgwMDRiODAyOTc1Nzg2ZmYwYTVmMmY0YzI5NDkyMDk3MTBjIiwidXNlcl9pZCI6MTEyMDN9:1jK9c6:WOLrKX0S_SSbwaRMBZcfBg6st-s' \
    --header 'Cookie: multidb_pin_writes=y' \
    --form 'vote=0'
    ```
   2. A Json object should be return and contain the following:
    ```json
    {
       "vote":0,
       "rating":{
          "id":6,
          "addon":{
             "id":2,
             "slug":"tasty-stew",
             "name":{
                "en-US":"Tasty Stew",
                "es":"(español) Tasty Stew",
                "fr":"(français) Tasty Stew"
             },
             "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
          },
          "body":"Test Review 1",
          "created":"2020-03-27T20:42:04Z",
          "is_deleted":false,
          "is_developer_reply":false,
          "is_latest":true,
          "previous_count":0,
          "user":{
             "id":10975,
             "name":"testuser-oK0RCiveDX6o@example.com",
             "url":"http://olympia.test/en-CA/firefox/user/10975/",
             "username":"testuser-oK0RCiveDX6o@example.com"
          },
          "score":2,
          "reply":null,
          "version":null
       },
       "user":{
          "id":1,
          "name":"Firefox user 1",
          "url":"http://olympia.test/en-CA/firefox/user/1/",
          "username":"MonsterAlan"
       },
       "addon":{
          "id":2,
          "slug":"tasty-stew",
          "name":{
             "en-US":"Tasty Stew",
             "es":"(español) Tasty Stew",
             "fr":"(français) Tasty Stew"
          },
          "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
       }
    }
    ```

    3. By sending the same request again will undo the vote. The response object will be like the following:
    ```json
    {
       "vote":-1,
       "rating":{
          "id":6,
          "addon":{
             "id":2,
             "slug":"tasty-stew",
             "name":{
                "en-US":"Tasty Stew",
                "es":"(español) Tasty Stew",
                "fr":"(français) Tasty Stew"
             },
             "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
          },
          "body":"Test Review 5",
          "created":"2020-03-27T20:42:04Z",
          "is_deleted":false,
          "is_developer_reply":false,
          "is_latest":true,
          "previous_count":0,
          "user":{
             "id":10979,
             "name":"testuser-S813lZ3MI6n9@example.com",
             "url":"http://olympia.test/en-CA/firefox/user/10979/",
             "username":"testuser-S813lZ3MI6n9@example.com"
          },
          "score":2,
          "reply":null,
          "version":null
       },
       "user":{
          "id":1,
          "name":"Firefox user 1",
          "url":"http://olympia.test/en-CA/firefox/user/1/",
          "username":"MonsterAlan"
       },
       "addon":{
          "id":2,
          "slug":"tasty-stew",
          "name":{
             "en-US":"Tasty Stew",
             "es":"(español) Tasty Stew",
             "fr":"(français) Tasty Stew"
          },
          "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
       }
    }
    ```

3. Change from upvote to downvote
    1. Execute the api
    ```
    curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/?lang=en-CA&wrap_outgoing_links=true' \
    --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiI5ZDZhNjgwMDRiODAyOTc1Nzg2ZmYwYTVmMmY0YzI5NDkyMDk3MTBjIiwidXNlcl9pZCI6MTEyMDN9:1jK9c6:WOLrKX0S_SSbwaRMBZcfBg6st-s' \
    --header 'Cookie: multidb_pin_writes=y' \
    --form 'vote=0'
    ```
    2. A Json object should be return and contain the following:
    ```json
    {
       "vote":0,
       "rating":{
          "id":6,
          "addon":{
             "id":2,
             "slug":"tasty-stew",
             "name":{
                "en-US":"Tasty Stew",
                "es":"(español) Tasty Stew",
                "fr":"(français) Tasty Stew"
             },
             "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
          },
          "body":"Test Review 1",
          "created":"2020-03-27T20:42:04Z",
          "is_deleted":false,
          "is_developer_reply":false,
          "is_latest":true,
          "previous_count":0,
          "user":{
             "id":10975,
             "name":"testuser-oK0RCiveDX6o@example.com",
             "url":"http://olympia.test/en-CA/firefox/user/10975/",
             "username":"testuser-oK0RCiveDX6o@example.com"
          },
          "score":2,
          "reply":null,
          "version":null
       },
       "user":{
          "id":1,
          "name":"Firefox user 1",
          "url":"http://olympia.test/en-CA/firefox/user/1/",
          "username":"MonsterAlan"
       },
       "addon":{
          "id":2,
          "slug":"tasty-stew",
          "name":{
             "en-US":"Tasty Stew",
             "es":"(español) Tasty Stew",
             "fr":"(français) Tasty Stew"
          },
          "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
       }
    }
    ```
4. Change from downvote to upvote
  1. Execute the api
  ```
	curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/?lang=en-CA&wrap_outgoing_links=true' \
	--header 'Authorization: Bearer eyJhdXRoX2hhc2giOiI5ZDZhNjgwMDRiODAyOTc1Nzg2ZmYwYTVmMmY0YzI5NDkyMDk3MTBjIiwidXNlcl9pZCI6MTEyMDN9:1jK9c6:WOLrKX0S_SSbwaRMBZcfBg6st-s' \
	--header 'Cookie: multidb_pin_writes=y' \
	--form 'vote=1'
  ```
  2. A Json object should be return and contain the following:
  ```json
  {
     "vote":1,
     "rating":{
        "id":6,
        "addon":{
           "id":2,
           "slug":"tasty-stew",
           "name":{
              "en-US":"Tasty Stew",
              "es":"(español) Tasty Stew",
              "fr":"(français) Tasty Stew"
           },
           "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
        },
        "body":"Test Review 1",
        "created":"2020-03-27T20:42:04Z",
        "is_deleted":false,
        "is_developer_reply":false,
        "is_latest":true,
        "previous_count":0,
        "user":{
           "id":10975,
           "name":"testuser-oK0RCiveDX6o@example.com",
           "url":"http://olympia.test/en-CA/firefox/user/10975/",
           "username":"testuser-oK0RCiveDX6o@example.com"
        },
        "score":2,
        "reply":null,
        "version":null
     },
     "user":{
        "id":1,
        "name":"Firefox user 1",
        "url":"http://olympia.test/en-CA/firefox/user/1/",
        "username":"MonsterAlan"
     },
     "addon":{
        "id":2,
        "slug":"tasty-stew",
        "name":{
           "en-US":"Tasty Stew",
           "es":"(español) Tasty Stew",
           "fr":"(français) Tasty Stew"
        },
        "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
     }
  }
  ```
5. Undo a vote by sending the upvote/downvote request twice
   1. Execute the api
   ```
   curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/?lang=en-CA&wrap_outgoing_links=true' \
   --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiI5ZDZhNjgwMDRiODAyOTc1Nzg2ZmYwYTVmMmY0YzI5NDkyMDk3MTBjIiwidXNlcl9pZCI6MTEyMDN9:1jK9c6:WOLrKX0S_SSbwaRMBZcfBg6st-s' \
   --header 'Cookie: multidb_pin_writes=y' \
   --form 'vote=1'
   ```
   *undo a downvote: change vote=1 to vote=0*
	2. A Json object should be return and contain the following:
	```json
  {
     "vote":-1,
     "rating":{
        "id":6,
        "addon":{
           "id":2,
           "slug":"tasty-stew",
           "name":{
              "en-US":"Tasty Stew",
              "es":"(español) Tasty Stew",
              "fr":"(français) Tasty Stew"
           },
           "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
        },
        "body":"Test Review 1",
        "created":"2020-03-27T20:42:04Z",
        "is_deleted":false,
        "is_developer_reply":false,
        "is_latest":true,
        "previous_count":0,
        "user":{
           "id":10975,
           "name":"testuser-oK0RCiveDX6o@example.com",
           "url":"http://olympia.test/en-CA/firefox/user/10975/",
           "username":"testuser-oK0RCiveDX6o@example.com"
        },
        "score":2,
        "reply":null,
        "version":null
     },
     "user":{
        "id":1,
        "name":"Firefox user 1",
        "url":"http://olympia.test/en-CA/firefox/user/1/",
        "username":"MonsterAlan"
     },
     "addon":{
        "id":2,
        "slug":"tasty-stew",
        "name":{
           "en-US":"Tasty Stew",
           "es":"(español) Tasty Stew",
           "fr":"(français) Tasty Stew"
        },
        "icon_url":"http://olympia.test/static/img/addon-icons/webdev-64.png"
     }
  }
	```


<a name="code"></a>

## Design Of Code



<a name="acceptance"></a>

## Acceptance Test

<a name="testget"></a>

1. #### Test GET Requests

     1. When a user sends get rating request with show vote option on (`show_votes_for = 1`), a detail of the specific rating(ex. rating with id 6, add-on with id 2) should be returned

        Curl command:

        ```
        curl --location --request GET 'http://olympia.test/api/v4/ratings/rating/6/?show_votes_for=1'
        ```

        Response:

        ```json
        {
          "id":6,
          "addon":{
             "id":2,
             "slug":"artisanal-salad",
             "name":{
                "en-US":"Artisanal Salad",
                "es":"(español) Artisanal Salad",
                "fr":"(français) Artisanal Salad"
             },
             "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
          },
          "body":"Test Review 1",
          "created":"2020-03-20T21:36:32Z",
          "votes":{
             "upvote":0,
             "downvote":1
          },
          "is_deleted":false,
          "is_developer_reply":false,
          "is_latest":true,
          "previous_count":0,
          "user":{
             "id":10975,
             "name":"testuser-sdeYbFa0E2cH@example.com",
             "url":null,
             "username":"testuser-sdeYbFa0E2cH@example.com"
          },
          "score":1,
          "reply":null,
          "version":null
        }
        ```

     2. When a user sends a get rating request with show vote option on (`show_votes_for = 1`), a detail of the specific addon(ex. add-on with id 2) should be returned

        Curl command:

        ```
        curl --location --request GET 'http://olympia.test/api/v4/ratings/rating/?addon=2&show_votes_for=1'
        ```

        Response:

        ```json
        {
          "page_size":25,
          "page_count":1,
          "count":6,
          "next":null,
          "previous":null,
          "results":[
             {
                "id":162,
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                },
                "body":"hiashdpasndna;sdn;a",
                "created":"2020-04-03T01:49:03Z",
                "votes":{
                   "upvote":0,
                   "downvote":0
                },
                "is_deleted":false,
                "is_developer_reply":false,
                "is_latest":true,
                "previous_count":0,
                "user":{
                   "id":1,
                   "name":"Firefox user 1",
                   "url":null,
                   "username":"samuelzhu"
                },
                "score":5,
                "reply":null,
                "version":{
                   "id":2,
                   "version":"0.1"
                }
             },
             {
                "id":10,
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                },
                "body":"Test Review 5",
                "created":"2020-03-20T21:36:32Z",
                "votes":{
                   "upvote":0,
                   "downvote":0
                },
                "is_deleted":false,
                "is_developer_reply":false,
                "is_latest":true,
                "previous_count":0,
                "user":{
                   "id":10979,
                   "name":"testuser-MnPbnfAZxEWx@example.com",
                   "url":null,
                   "username":"testuser-MnPbnfAZxEWx@example.com"
                },
                "score":2,
                "reply":null,
                "version":null
             },
             {
                "id":9,
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                },
                "body":"Test Review 4",
                "created":"2020-03-20T21:36:32Z",
                "votes":{
                   "upvote":0,
                   "downvote":0
                },
                "is_deleted":false,
                "is_developer_reply":false,
                "is_latest":true,
                "previous_count":0,
                "user":{
                   "id":10978,
                   "name":"testuser-NENKVy6BKhDM@example.com",
                   "url":null,
                   "username":"testuser-NENKVy6BKhDM@example.com"
                },
                "score":5,
                "reply":null,
                "version":null
             },
             {
                "id":8,
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                },
                "body":"Test Review 3",
                "created":"2020-03-20T21:36:32Z",
                "votes":{
                   "upvote":0,
                   "downvote":0
                },
                "is_deleted":false,
                "is_developer_reply":false,
                "is_latest":true,
                "previous_count":0,
                "user":{
                   "id":10977,
                   "name":"testuser-GAKimmOP5T3a@example.com",
                   "url":null,
                   "username":"testuser-GAKimmOP5T3a@example.com"
                },
                "score":5,
                "reply":null,
                "version":null
             },
             {
                "id":7,
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                },
                "body":"Test Review 2",
                "created":"2020-03-20T21:36:32Z",
                "votes":{
                   "upvote":0,
                   "downvote":0
                },
                "is_deleted":false,
                "is_developer_reply":false,
                "is_latest":true,
                "previous_count":0,
                "user":{
                   "id":10976,
                   "name":"testuser-KqxPpLb6jeiB@example.com",
                   "url":null,
                   "username":"testuser-KqxPpLb6jeiB@example.com"
                },
                "score":1,
                "reply":null,
                "version":null
             },
             {
                "id":6,
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                },
                "body":"Test Review 1",
                "created":"2020-03-20T21:36:32Z",
                "votes":{
                   "upvote":0,
                   "downvote":1
                },
                "is_deleted":false,
                "is_developer_reply":false,
                "is_latest":true,
                "previous_count":0,
                "user":{
                   "id":10975,
                   "name":"testuser-sdeYbFa0E2cH@example.com",
                   "url":null,
                   "username":"testuser-sdeYbFa0E2cH@example.com"
                },
                "score":1,
                "reply":null,
                "version":null
             }
          ]
        }
        ```

     3. When a user sends a get rating request with invalid show vote flag (`show_votes_for = daf`), the response complain about the invalid vote flag value.

        curl command:

        ```
        curl --location --request GET 'http://olympia.test/api/v4/ratings/rating/6/?show_votes_for=daf'
        ```

        Response:

        ```json
        {
            "detail": "show_votes_for parameter should be a boolean"
        }
        ```

     4. When a user sends a get rating request with show vote off (`show_votes_for = 0`), the response should not contain any vote info.

        Curl Command:

        ```
        curl --location --request GET 'http://olympia.test/api/v4/ratings/rating/6/?show_votes_for=0'
        ```

        Response:

        ```json
        {
          "id":6,
          "addon":{
             "id":2,
             "slug":"artisanal-salad",
             "name":{
                "en-US":"Artisanal Salad",
                "es":"(español) Artisanal Salad",
                "fr":"(français) Artisanal Salad"
             },
             "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
          },
          "body":"Test Review 1",
          "created":"2020-03-20T21:36:32Z",
          "is_deleted":false,
          "is_developer_reply":false,
          "is_latest":true,
          "previous_count":0,
          "user":{
             "id":10975,
             "name":"testuser-sdeYbFa0E2cH@example.com",
             "url":null,
             "username":"testuser-sdeYbFa0E2cH@example.com"
          },
          "score":1,
          "reply":null,
          "version":null
        }
        ```

<a name="testpost"></a>

2. #### Test POST Requests

     1. When the user is not logged-in (in curl or Postman requesting case, it means no authentication token), if the user tries to vote for a rating (ex. rating with id 6, vote with upvote), a 401 unauthorization response should be returned.

        Curl Command:

        ```
        curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/' \--header 'Content-Type: multipart/form-data \--form 'vote=1'
        ```

        Response:

        ```json
        {
          "detail":"Authentication credentials were not provided."
        }
        ```

     2. When the user is logged-in (in curl or Postman requesting case, it means with authentication token) and a upvote requests sent,

        - if the user is the author of the add-on, a 403 response should be returned

           Curl Command:

           ```
           curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/162/vote/' \
           --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiJjZjAwM2UyZDdjYTJjM2QyODQxZWMyODQ1NGUxN2E2ZDZkYzhiNzIzIiwidXNlcl9pZCI6MX0:1jKB6y:DtACgTxMqzxWAVVPwX9euc8XCxs' \
           --header 'Content-Type: multipart/form-data \
           --form 'vote=1'
           ```

           Response:

           ```json
           {
             "detail": "You do not have permission to perform this action."
           }
           ```

        - if the user is the author of the rating, a 403 response should be returned

           Curl Command:

           ```
           curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/163/vote/' \
           --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiJjZjAwM2UyZDdjYTJjM2QyODQxZWMyODQ1NGUxN2E2ZDZkYzhiNzIzIiwidXNlcl9pZCI6MX0:1jKB6y:DtACgTxMqzxWAVVPwX9euc8XCxs' \
           --header 'Content-Type: multipart/form-data \
           --form 'vote=1'
           ```

           Response:

           ```json
           {
             "detail": "You do not have permission to perform this action."
           }
           ```

        - if the user is neither the author of the add-on nor the author of the rating and

           - the user did not vote for the rating, then the request should successfully go through and a 202 response should be returned with upvoting data in database increased by 1

              First we can use send a get request to get the voting for the rating with id 6 using curl below:

              ```
              curl --location --request GET 'http://olympia.test/api/v4/ratings/rating/6/?show_votes_for=1'
              ```

              and then we can see the response (notice that the upvote is 0 now):

              ```json
              {
                "id":6,
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                },
                "body":"Test Review 1",
                "created":"2020-03-20T21:36:32Z",
                "votes":{
                   "upvote":0,
                   "downvote":0
                },
                "is_deleted":false,
                "is_developer_reply":false,
                "is_latest":true,
                "previous_count":0,
                "user":{
                   "id":10975,
                   "name":"testuser-sdeYbFa0E2cH@example.com",
                   "url":null,
                   "username":"testuser-sdeYbFa0E2cH@example.com"
                },
                "score":1,
                "reply":null,
                "version":null
             }
             ```

             Use curl below to upvote the rating:

             ```
              curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/' \
              --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiJjZjAwM2UyZDdjYTJjM2QyODQxZWMyODQ1NGUxN2E2ZDZkYzhiNzIzIiwidXNlcl9pZCI6MX0:1jKB6y:DtACgTxMqzxWAVVPwX9euc8XCxs' \
              --header 'Content-Type: multipart/form-data \
              --form 'vote=1'
             ```

              and we get response below:

              ```json
              {
                "vote":1,
                "rating":{
                   "id":6,
                   "addon":{
                      "id":2,
                      "slug":"artisanal-salad",
                      "name":{
                         "en-US":"Artisanal Salad",
                         "es":"(español) Artisanal Salad",
                         "fr":"(français) Artisanal Salad"
                      },
                      "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                   },
                   "body":"Test Review 1",
                   "created":"2020-03-20T21:36:32Z",
                   "is_deleted":false,
                   "is_developer_reply":false,
                   "is_latest":true,
                   "previous_count":0,
                   "user":{
                      "id":10975,
                      "name":"testuser-sdeYbFa0E2cH@example.com",
                      "url":"http://olympia.test/en-US/firefox/user/10975/",
                      "username":"testuser-sdeYbFa0E2cH@example.com"
                   },
                   "score":1,
                   "reply":null,
                   "version":null
                },
                "user":{
                   "id":1,
                   "name":"Firefox user 1",
                   "url":"http://olympia.test/en-US/firefox/user/1/",
                   "username":"samuelzhu"
                },
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                }
             }
              ```

             and if we send the get request again, we now see the upvote is increased to 1 (originally 0):

             ```json
             {
               "id":6,
               "addon":{
                  "id":2,
                  "slug":"artisanal-salad",
                  "name":{
                     "en-US":"Artisanal Salad",
                     "es":"(español) Artisanal Salad",
                     "fr":"(français) Artisanal Salad"
                  },
                  "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
               },
               "body":"Test Review 1",
               "created":"2020-03-20T21:36:32Z",
               "votes":{
                  "upvote":1,
                  "downvote":0
               },
               "is_deleted":false,
               "is_developer_reply":false,
               "is_latest":true,
               "previous_count":0,
               "user":{
                  "id":10975,
                  "name":"testuser-sdeYbFa0E2cH@example.com",
                  "url":null,
                  "username":"testuser-sdeYbFa0E2cH@example.com"
               },
               "score":1,
               "reply":null,
               "version":null
             }
             ```

           - the user voted for the rating as upvote before, then the request should successfully go through and a 202 response should be returned with upvoting data in database decreased by 1 (meaning undo upvote)

              Use curl below to upvote the rating(with `vote` set to 1 means upvote for the rating with id 6):

              ```
              curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/' \
              --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiJjZjAwM2UyZDdjYTJjM2QyODQxZWMyODQ1NGUxN2E2ZDZkYzhiNzIzIiwidXNlcl9pZCI6MX0:1jKB6y:DtACgTxMqzxWAVVPwX9euc8XCxs' \
              --header 'Content-Type: multipart/form-data \
              --form 'vote=1'
              ```

              and we get response below:

              ```json
              {
                "vote":1,
                "rating":{
                   "id":6,
                   "addon":{
                      "id":2,
                      "slug":"artisanal-salad",
                      "name":{
                         "en-US":"Artisanal Salad",
                         "es":"(español) Artisanal Salad",
                         "fr":"(français) Artisanal Salad"
                      },
                      "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                   },
                   "body":"Test Review 1",
                   "created":"2020-03-20T21:36:32Z",
                   "is_deleted":false,
                   "is_developer_reply":false,
                   "is_latest":true,
                   "previous_count":0,
                   "user":{
                      "id":10975,
                      "name":"testuser-sdeYbFa0E2cH@example.com",
                      "url":"http://olympia.test/en-US/firefox/user/10975/",
                      "username":"testuser-sdeYbFa0E2cH@example.com"
                   },
                   "score":1,
                   "reply":null,
                   "version":null
                },
                "user":{
                   "id":1,
                   "name":"Firefox user 1",
                   "url":"http://olympia.test/en-US/firefox/user/1/",
                   "username":"samuelzhu"
                },
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                }
             }
             ```

             And now if we send the request again, we will see `vote` is set to -1 (which means undo to unvoted status):

             ```json
             {
               "vote":-1,
               "rating":{
                  "id":6,
                  "addon":{
                     "id":2,
                     "slug":"artisanal-salad",
                     "name":{
                        "en-US":"Artisanal Salad",
                        "es":"(español) Artisanal Salad",
                        "fr":"(français) Artisanal Salad"
                     },
                     "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                  },
                  "body":"Test Review 1",
                  "created":"2020-03-20T21:36:32Z",
                  "is_deleted":false,
                  "is_developer_reply":false,
                  "is_latest":true,
                  "previous_count":0,
                  "user":{
                     "id":10975,
                     "name":"testuser-sdeYbFa0E2cH@example.com",
                     "url":"http://olympia.test/en-US/firefox/user/10975/",
                     "username":"testuser-sdeYbFa0E2cH@example.com"
                  },
                  "score":1,
                  "reply":null,
                  "version":null
               },
               "user":{
                  "id":1,
                  "name":"Firefox user 1",
                  "url":"http://olympia.test/en-US/firefox/user/1/",
                  "username":"samuelzhu"
               },
               "addon":{
                  "id":2,
                  "slug":"artisanal-salad",
                  "name":{
                     "en-US":"Artisanal Salad",
                     "es":"(español) Artisanal Salad",
                     "fr":"(français) Artisanal Salad"
                  },
                  "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
               }
             }
             ```

           - the user voted for the rating as downvote before, then the request should successfully go through and a 202 response should be returned with upvoting data in database increased by 1 and downvoting data decreased by 1 (meaning change downvote to upvote)

              Use curl below to downvote the rating (with `vote` set to 0 means downvote for the rating with id 6):

              ```
              curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/' \
              --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiJjZjAwM2UyZDdjYTJjM2QyODQxZWMyODQ1NGUxN2E2ZDZkYzhiNzIzIiwidXNlcl9pZCI6MX0:1jKB6y:DtACgTxMqzxWAVVPwX9euc8XCxs' \
              --header 'Content-Type: multipart/form-data \
              --form 'vote=0'
              ```

              and we get response below:

              ```json
              {
                "vote":0,
                "rating":{
                   "id":6,
                   "addon":{
                      "id":2,
                      "slug":"artisanal-salad",
                      "name":{
                         "en-US":"Artisanal Salad",
                         "es":"(español) Artisanal Salad",
                         "fr":"(français) Artisanal Salad"
                      },
                      "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                   },
                   "body":"Test Review 1",
                   "created":"2020-03-20T21:36:32Z",
                   "is_deleted":false,
                   "is_developer_reply":false,
                   "is_latest":true,
                   "previous_count":0,
                   "user":{
                      "id":10975,
                      "name":"testuser-sdeYbFa0E2cH@example.com",
                      "url":"http://olympia.test/en-US/firefox/user/10975/",
                      "username":"testuser-sdeYbFa0E2cH@example.com"
                   },
                   "score":1,
                   "reply":null,
                   "version":null
                },
                "user":{
                   "id":1,
                   "name":"Firefox user 1",
                   "url":"http://olympia.test/en-US/firefox/user/1/",
                   "username":"samuelzhu"
                },
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                }
             }
             ```

              And now if we send an upvote request , we will see `vote` is set to 1 (which means upvote):

              ```json
              {
                "vote":1,
                "rating":{
                   "id":6,
                   "addon":{
                      "id":2,
                      "slug":"artisanal-salad",
                      "name":{
                         "en-US":"Artisanal Salad",
                         "es":"(español) Artisanal Salad",
                         "fr":"(français) Artisanal Salad"
                      },
                      "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                   },
                   "body":"Test Review 1",
                   "created":"2020-03-20T21:36:32Z",
                   "is_deleted":false,
                   "is_developer_reply":false,
                   "is_latest":true,
                   "previous_count":0,
                   "user":{
                      "id":10975,
                      "name":"testuser-sdeYbFa0E2cH@example.com",
                      "url":"http://olympia.test/en-US/firefox/user/10975/",
                      "username":"testuser-sdeYbFa0E2cH@example.com"
                   },
                   "score":1,
                   "reply":null,
                   "version":null
                },
                "user":{
                   "id":1,
                   "name":"Firefox user 1",
                   "url":"http://olympia.test/en-US/firefox/user/1/",
                   "username":"samuelzhu"
                },
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                }
             }
              ```

     3. When the user is logged-in (in curl or Postman requesting case, it means no authentication token) and a downvote request is sent,

        - if the user is the author of the add-on, a 403 response should be returned

           Curl Command( `vote=0` means downvote):

           ```
           curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/' \--header 'Content-Type: multipart/form-data \--form 'vote=0'
           ```

           Response:

           ```json
           {
             "detail": "You do not have permission to perform this action."
           }
           ```

        - if the user is the author of the rating, a 403 response should be returned

           Curl Command( `vote=0` means downvote):

           ```
           curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/' \--header 'Content-Type: multipart/form-data \--form 'vote=0'
           ```

           Response:

           ```json
           {
             "detail": "You do not have permission to perform this action."
           }
           ```

        - if the user is neither the author of the add-on nor the author of the rating and

           - the user did not vote for the rating, then the request should successfully go through and a 202 response should be returned with downvoting data in database increased by 1

              Use curl command below to send a downvote request for rating with id 6:

              ```
              curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/' \
              --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiJjZjAwM2UyZDdjYTJjM2QyODQxZWMyODQ1NGUxN2E2ZDZkYzhiNzIzIiwidXNlcl9pZCI6MX0:1jKB6y:DtACgTxMqzxWAVVPwX9euc8XCxs' \
              --header 'Content-Type: multipart/form-data;
              --form 'vote=0'
              ```

              should get response as below:

              ```json
              {
                "vote":0,
                "rating":{
                   "id":6,
                   "addon":{
                      "id":2,
                      "slug":"artisanal-salad",
                      "name":{
                         "en-US":"Artisanal Salad",
                         "es":"(español) Artisanal Salad",
                         "fr":"(français) Artisanal Salad"
                      },
                      "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                   },
                   "body":"Test Review 1",
                   "created":"2020-03-20T21:36:32Z",
                   "is_deleted":false,
                   "is_developer_reply":false,
                   "is_latest":true,
                   "previous_count":0,
                   "user":{
                      "id":10975,
                      "name":"testuser-sdeYbFa0E2cH@example.com",
                      "url":"http://olympia.test/en-US/firefox/user/10975/",
                      "username":"testuser-sdeYbFa0E2cH@example.com"
                   },
                   "score":1,
                   "reply":null,
                   "version":null
                },
                "user":{
                   "id":1,
                   "name":"Firefox user 1",
                   "url":"http://olympia.test/en-US/firefox/user/1/",
                   "username":"samuelzhu"
                },
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                }
             }
             ```

           - the user voted for the rating as downvote before, then the request should successfully go through and a 202 response should be returned with downvoting data decreased by 1 (meaning undo downvote)

           Use curl below to downvote the rating(with `vote` set to 0 means upvote for the rating with id 6):

           ```
           curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/' \
           --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiJjZjAwM2UyZDdjYTJjM2QyODQxZWMyODQ1NGUxN2E2ZDZkYzhiNzIzIiwidXNlcl9pZCI6MX0:1jKB6y:DtACgTxMqzxWAVVPwX9euc8XCxs' \
           --header 'Content-Type: multipart/form-data \
           --form 'vote=0'
           ```

           and we get response below:

           ```json
           {
             "vote":0,
             "rating":{
                "id":6,
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                },
                "body":"Test Review 1",
                "created":"2020-03-20T21:36:32Z",
                "is_deleted":false,
                "is_developer_reply":false,
                "is_latest":true,
                "previous_count":0,
                "user":{
                   "id":10975,
                   "name":"testuser-sdeYbFa0E2cH@example.com",
                   "url":"http://olympia.test/en-US/firefox/user/10975/",
                   "username":"testuser-sdeYbFa0E2cH@example.com"
                },
                "score":1,
                "reply":null,
                "version":null
             },
             "user":{
                "id":1,
                "name":"Firefox user 1",
                "url":"http://olympia.test/en-US/firefox/user/1/",
                "username":"samuelzhu"
             },
             "addon":{
                "id":2,
                "slug":"artisanal-salad",
                "name":{
                   "en-US":"Artisanal Salad",
                   "es":"(español) Artisanal Salad",
                   "fr":"(français) Artisanal Salad"
                },
                "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
             }
          }
          ```

          And now if we send the request again, we will see `vote` is set to -1 (which means undo to unvoted status):

          ```json
          {
            "vote":-1,
            "rating":{
               "id":6,
               "addon":{
                  "id":2,
                  "slug":"artisanal-salad",
                  "name":{
                     "en-US":"Artisanal Salad",
                     "es":"(español) Artisanal Salad",
                     "fr":"(français) Artisanal Salad"
                  },
                  "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
               },
               "body":"Test Review 1",
               "created":"2020-03-20T21:36:32Z",
               "is_deleted":false,
               "is_developer_reply":false,
               "is_latest":true,
               "previous_count":0,
               "user":{
                  "id":10975,
                  "name":"testuser-sdeYbFa0E2cH@example.com",
                  "url":"http://olympia.test/en-US/firefox/user/10975/",
                  "username":"testuser-sdeYbFa0E2cH@example.com"
               },
               "score":1,
               "reply":null,
               "version":null
            },
            "user":{
               "id":1,
               "name":"Firefox user 1",
               "url":"http://olympia.test/en-US/firefox/user/1/",
               "username":"samuelzhu"
            },
            "addon":{
               "id":2,
               "slug":"artisanal-salad",
               "name":{
                  "en-US":"Artisanal Salad",
                  "es":"(español) Artisanal Salad",
                  "fr":"(français) Artisanal Salad"
               },
               "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
            }
          }
          ```

           - the user voted for the rating as upvote before, then the request should successfully go through and a 202 response should be returned with downvoting data in database increased by 1 and upvoting data decreased by 1 (meaning change upvote to downvote)

              Use curl command below to send a upvote request for rating with id 6:

              ```
              curl --location --request POST 'http://olympia.test/api/v4/ratings/rating/6/vote/' \
              --header 'Authorization: Bearer eyJhdXRoX2hhc2giOiJjZjAwM2UyZDdjYTJjM2QyODQxZWMyODQ1NGUxN2E2ZDZkYzhiNzIzIiwidXNlcl9pZCI6MX0:1jKB6y:DtACgTxMqzxWAVVPwX9euc8XCxs' \
              --header 'Content-Type: multipart/form-data;
              --form 'vote=1'
              ```

              and now, should get response as below:

              ```json
              {
                "vote":1,
                "rating":{
                   "id":6,
                   "addon":{
                      "id":2,
                      "slug":"artisanal-salad",
                      "name":{
                         "en-US":"Artisanal Salad",
                         "es":"(español) Artisanal Salad",
                         "fr":"(français) Artisanal Salad"
                      },
                      "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                   },
                   "body":"Test Review 1",
                   "created":"2020-03-20T21:36:32Z",
                   "is_deleted":false,
                   "is_developer_reply":false,
                   "is_latest":true,
                   "previous_count":0,
                   "user":{
                      "id":10975,
                      "name":"testuser-sdeYbFa0E2cH@example.com",
                      "url":"http://olympia.test/en-US/firefox/user/10975/",
                      "username":"testuser-sdeYbFa0E2cH@example.com"
                   },
                   "score":1,
                   "reply":null,
                   "version":null
                },
                "user":{
                   "id":1,
                   "name":"Firefox user 1",
                   "url":"http://olympia.test/en-US/firefox/user/1/",
                   "username":"samuelzhu"
                },
                "addon":{
                   "id":2,
                   "slug":"artisanal-salad",
                   "name":{
                      "en-US":"Artisanal Salad",
                      "es":"(español) Artisanal Salad",
                      "fr":"(français) Artisanal Salad"
                   },
                   "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                }
             }
             ```

             we can send a downvote request to the same rating, and get response(`vote=0`means changed to downvote):

             ```json
             {
               "vote":0,
               "rating":{
                  "id":6,
                  "addon":{
                     "id":2,
                     "slug":"artisanal-salad",
                     "name":{
                        "en-US":"Artisanal Salad",
                        "es":"(español) Artisanal Salad",
                        "fr":"(français) Artisanal Salad"
                     },
                     "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
                  },
                  "body":"Test Review 1",
                  "created":"2020-03-20T21:36:32Z",
                  "is_deleted":false,
                  "is_developer_reply":false,
                  "is_latest":true,
                  "previous_count":0,
                  "user":{
                     "id":10975,
                     "name":"testuser-sdeYbFa0E2cH@example.com",
                     "url":"http://olympia.test/en-US/firefox/user/10975/",
                     "username":"testuser-sdeYbFa0E2cH@example.com"
                  },
                  "score":1,
                  "reply":null,
                  "version":null
               },
               "user":{
                  "id":1,
                  "name":"Firefox user 1",
                  "url":"http://olympia.test/en-US/firefox/user/1/",
                  "username":"samuelzhu"
               },
               "addon":{
                  "id":2,
                  "slug":"artisanal-salad",
                  "name":{
                     "en-US":"Artisanal Salad",
                     "es":"(español) Artisanal Salad",
                     "fr":"(français) Artisanal Salad"
                  },
                  "icon_url":"http://olympia.test/static/img/addon-icons/posts-64.png"
               }
             }
             ```



<a name="unit"></a>

## Unit Test

Since we added new codes and modified some of the existing codes in models.py, serializers.py, permissions.py, and views.py, we added our unit test suites to the corresponding test files: test_models.py, test_serializers.py, test_permissions.py, and test_views.py. In addition to the unit test suite for the new functional code for the voting API, we also modified some of the existing test cases to adapt the new configurations or attributes we added for implementing the voting API. Below is an overview of the test suite we added or modified in each corresponding test file:

In **test_models.py**:

- Modified `class TestRatingModel` to test the creation process of the new database tables we introduced, and modified some existing test cases to fit the new database tables
- Added new `class TestGroupedVoting` to test fetching the summary of upvoting and downvoting counts from the new database tables we introduced

In **test_serializers.py**:

- Modified `class TestBaseRatingSerializer` to test whether the voting serializer could correctly serialize voting summary from the database records

In **test_permissions.py**:

- Added new `class TestCanVoteRatingPermission` to test voting for rating permission, only a logged-in user that is neither the author of the add-on nor the author of the rating could vote

In **test_views.py**:

- Modified `class TestRatingViewSetGet` to test whether the rating view set could correctly include voting summary section in the body of response if the client sent GET requests
- Added new `class TestRatingViewSetVote` to test whether the rating view set could correctly modified the voting record in database and send back the correct response once the client sent POST requests for upvoting or downvoting

After all changes in the test files, we executed the full test suites for the **ratings app** on both master branch and our feature implementing branch. We then compared the two results. The results are the same, which implies both the correctness of our new functional codes and the correctness of our new corresponding test suites. The screenshots below are the result comparison.

Test suite on **master** branch execution results:![](./pic/master_test_result.png)

Test suite on **8611-voting_api_final_implementation** branch execution results:![](./pic/feature_test_result.png)

The full test execution results can be found here:

[Test suite on master branch execution results](./file/run_test_src_olympia_ratings_master.txt)

[Test suite on 8611-voting_api_final_implementation branch execution results](./file/run_test_src_olympia_ratings_result_final_version.txt)



<a name="process"></a>

## Software Development Process

Our **[Kanban Board]( https://trello.com/b/tX6AbRV4/addon-server )**

Due to the limitation of time, and we expected the division of work to become more flexible, we still used Kanban as our development process in deliverable 4. Similar to the kanban board in deliverable 2, we kept 6 columns (ToDo, In Progress, Code Review1, Code Review2, QA Testing, and Done) with the following rules:

- Each one could take at most two tickets at the same time(one in ToDo/In Progress, the other one in the rest of the columns)
- The person who implements codes / writes documentations could not do Code Review / QA Testing himself/herself
- Anyone who takes a new card needs to assign his/her name on the card immediately
- After each one has done his/her job, he/she should move the ticket to the next column and remove his/her name from the card

![](./pic/dev_process_d4.png)

At our first meeting to prepare the work and divide the tasks for deliverable 4, we actually had some problems, which were different from what we have done in the previous deliverable. We noticed that we were going to implement a new feature (voting API) for the addon-server, and with our analysis in deliverable 3, only a few files were involved. However, in each file, a large chunk of codes with the corresponding tests (unit tests) needed to be implemented. Because of the mechanism behind the Django framework, models and serializers are closely related. Meanwhile, views and permissions are closely coupled. So, if we broke the coding tasks into just one task for each file on our board, the person who took models and the person who took serializers need to frequently work together anyway. Similar situation applied to views and permissions. Thus, we decided to divide the coding tasks into two groups (1. permissions and views; 2. models and serializers). Pair-programming will be used on each group of tasks. For each coding task group, we created two tickets: one is implementing xxx with another task co-implementing xxx, for example, **Implementing POST part of permissions.py and views.py** and **Co-implementing POST part of permissions.py and views.py**. Although in the Kanban process, pair-programming is not common, we thought pair-programming could help us code more efficiently and reduce our communicating costs. Additionally, two people in a group could check each other's work on a real-time basis. Also, pair-programming allowed the two people in a group having a deeper understanding of the whole project and writing corresponding test cases easily. Once they encounter some problems, pair-programming may help them speed up the resolving process.

![](./pic/dev_process_d4_0.png)

In addition, in the process of deliverable 4, we met some problems that we’ve never met before. It was because we have some analysis errors in deliverable 3, which led us to be unaware of a part of code implementation. The problem was discussed at one of our meetings during the process. At that time, the person who took the User Guide documentation task had done her work, and the ticket had already been put into the Second Code Review. Faced with this situation, we had to put the ticket back in ToDo and write down the specific reason in the card detail. That was how we solved this problem.

![](./pic/dev_process_d4_1.png)

Another interesting thing we want to mention is that we added a ticket of fixing broken existing test cases in the middle of the development process of deliverable 4, for example, tickets like **Fixing broken test cases caused by introducing the new feature**. With the benefits of Kanban, we can easily add this new ticket as soon as we realize the necessity. The reason why we need this type of tickets was that some of the existing test cases would be broken as we added new functional code for the voting feature. Since some of the existing test cases were mocking objects with the original attributes, as we introduced new attributes to the same objects, these test cases may not work. This type of tickets allowed us to go through the existing test cases that could be affected by our new functional codes. As a result, we will not break the existing test cases and result in unnecessary work for the open-source community in the future.

![](./pic/dev_process_d4_2.png)
