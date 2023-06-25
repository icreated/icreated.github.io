---
author: "Sergey Polyarus"
layout: post
title:  "How to use efficiently Postman"
date: 2023-06-24
comments: true
description: Auto connection with JWT token without manual copy/paste (time saving)
categories: automate connect jwt postman
tags: 
- webstore-api
- webportal-api
- openapi
---


## TL;DR
**Postman** is a great tool for testing REST API. It is very convenient to use it for testing WebStore API. However, it is not very convenient to copy/paste JWT token every time you need to test a new request due to the token expiration, server restart, etc.
This article describes how to automate this process.

## Environment variables
Define first environment variables for your Postman collection. You can do it in the following way:
![Postman env variables](/assets/images/projects/webstore-api/postman-env.png "Postman env variables")

**Note:** No need to define `token` variable. It will be defined automatically.


## Pre-request script
Add the following script to the pre-request script section of your Postman collection:

```javascript
pm.sendRequest(
    {
        url: pm.environment.get("baseUrl")+"/auth/login",
        method: 'POST',
        header: 'Content-Type: application/json',
        body: {
                mode: 'raw',
                raw: JSON.stringify({ 
                    "username": pm.environment.get("username"),
                    "password": pm.environment.get("password")
                    })
            }        

    }, function (err, response) {
        pm.environment.set("token", response.json().token);

});
```

What does this script do?
1. It sends a request to the login endpoint with the username and password from the environment variables.
2. It saves the JWT token from the response to the environment variable `token`.
3. This variable will be used in the Authorization header for all requests in the collection.

**Note:** 
1. Don't forget to add the `Authorization` header to the collection. 
    * Type: `Bearer Token` from context menu.
    * Token: `{ {token} }` from the environment variable.
2. This exemple uses the `baseUrl`, `username` and `password` environment variables. Login endpoint from webstore-api is `/auth/login`. You can change it to your own.values.


## Conclusion
Now you can test your API without manual copy/paste of the JWT token. Use `Inherit auth from parent` for all requests. It will be executed automatically before each request. Nice and easy!
