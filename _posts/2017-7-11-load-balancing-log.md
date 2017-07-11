---
layout: post
title: NGINX Load Balancing Log
categories: [load_balancing, logs]
tags: [load_balancing, logs]
published: false
fullview: true
---

## What?
A(? or many) reverse proxy server sit in front of all application servers. (mostly) Responsible for directing request/response between client and server.

## Why?
To scale application horizontally

## Players?
Nginx, HAproxy, ...

## Nginx: ways to directing requests?

- Round robin
- Least connected
- Ip Hash (sticky session)

## Problems?

Using Round robin and Least connected make code, relying on local memory, work inconsistently. Examples: websocket, user session stored in local, ...

Sticky session could solve that but not good enough.
  - routing all request from certain ip to certain server is not really good
  - if a application server is down, all data stored in local memory will be lost (not really problem of sticky session but storing data in local memory in general)
  - in a websocket network, nodes in different application servers can not communicate (emit, broadcast)

## Solutions?

Store session data in db, retrieve by cookie token.
Still not solve the problem with websocket, usually Redis is used in this case.

## Multiple load balancers?
