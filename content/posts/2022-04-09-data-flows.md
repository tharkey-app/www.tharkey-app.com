---
title: "Data Flows"
tags: ["mvp", "data-flow"]
date: 2022-04-09T17:00:00+02:00
draft: false
---

Now that [functional]({{< ref "2021-11-06-mvp" >}}) and [non-functional]({{< ref "2022-04-09-nfr" >}}) requirements are defined we will try to refine the flows.

<!--more-->

## Overall data flow

```goat
                                          ID Token +
  O   start/stop recording  .-------.     position       .----------.              .---------. 
 /|\  ------------------->  | Phone | -----------------> |   API    |____________ |   Data    |
 / \                        |  app  |                    |  server  |             |   store   |
                            '-------'                    '----------'              '---------'
             login flow   _____|                          |    ^
                         |                                |    |
                         v                                |    |
                    .--------.         GET public keys    |    |
                    | AuthZ  | <--------------------------'    |
                    | server |                                 |
                    '--------'                                 |
                      ^                                        |
             _________|                                        |
            |            login flow                            |
            |                                                  |
            |                                                  |
            |               ID Token +                         |
  O    .---------.      list / get / delete previous tracks    |
 /|\   | Website | <-------------------------------------------'
 / \   '---------'

```

## Session recording

The flow for session recording is:
  1. User opens the application on a phone 
  2. Application redirects to the AuthZ server
  3. User authenticates on the AuthZ server and grant permission to the application to access basic profile details
  4. AuthZ server redirects to the application, and application and AuthZ server performs token exchange
  5. User click on "Start recording"
  6. At regular time interval, the phone sends the security token and position details to the API server
  7. When the API server receive a new position it stores it in the data store
  8. User click on "Stop recording"
  
## Session display and deletion

The flow for session display or deletion is:
  1. User opens the application website
  2. Application redirects to the AuthZ server
  3. User authenticates on the AuthZ server and grant permission to the application to access basic profile details
  4. AuthZ server redirects to the website, and website and AuthZ server performs token exchange
  5. Website request the API server for a list of the recent recorded sessions and display them
  6. User click on one of them
  7. Website request the API server for the details of one session and display them
  8. Optionally, User click on delete button
  9. Website request the API server for the deletion of one session and redirects to the page where all sessions are listed (see step 5)
