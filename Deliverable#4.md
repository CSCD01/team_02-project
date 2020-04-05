Acceptance Test

1. Test GET Requests

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

2. Test POST Requests

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

   