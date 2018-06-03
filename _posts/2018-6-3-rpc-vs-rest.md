---
layout: post
title: RPC and REST
categories: [logs]
tags: [logs]
published: true
fullview: true
---

## REST? RPC?

REST is short for Representational State Transfer and RPC is short for Remote Procedure Call. They are both sets of guidelines on how server and client should talk to each others. **REST style emphasizes on entities and their connections**, whereas **RPC is more about what actions there are in the system.**

One easy way to spot one from another is look their URL. Given a system of blog posts and comments of posts, use case is to retrieve and create a comment for a post. With REST, the url would be:

```
/post/{postId}/comment
```

To retrieve a comment, simply send a GET request with query value: `GET /post/354/comment?commentId=12312`
To create a comment, make a POST request: `POST /post/354/comment?content=thisiscommentcontent`

In RPC world, there would need to be two urls to fulfill the same use-case:

```
/get-comment
/create-comment
```

Then make `GET` and `POST` request to them with data in request `body` to get and create comment respectively:

```
GET /get-comment
body:
commentId: 12312
```

```
POST /create-comment
body:
postId: 354
content: thisiscommentcontent
```

As shown above, a REST endpoint will fit all required data in the url, whereas data for RPC endpoint has no place in the url but needed to be sent in request body.

## When to use which?

It is said that REST is good for a system which is more about CRUD of resources, and less about action. In the contrary, if a system has many action waiting to be called, RPC would be a better fit.

In reality, many systems are mixes of both REST and RPC style. Those systems usually start as a REST api. Then as life goes, things change they adopt some RPC endpoint to cope with new requirements. The fact is there is no guidelines stating that REST and RPC cannot coexist in a same system. Therefore they are used when they fit to do the job.

## Myths

- People may refer to anything not SOAP as REST api which is wrong
- REST is not HTTP or a set of HTTP methods
- RPC is not a mean of data transportation or data format during transport