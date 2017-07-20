---
layout: post
title: NGINX Load Balancing Log
categories: [load_balancing, logs]
tags: [load_balancing, logs]
published: true
fullview: true
---

## What?
A(or many?) reverse proxy server sit in front of all application servers. (mostly) Responsible for directing request/response between client and server.

## Why?
To scale application horizontally, improve system availability (failover)

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

What if the load balancer dead or the traffic volume is too high? -> multiple load balancers

# Load balancing between multiple load balancers?

DNS load balancing: not efficient, no health check, no sticky session?

AWS ELB: use ELB to load balancing between other load balancers (Nginx). Have support for sticky session. But why don't just use ELB and eliminate all other load balancers?

What are other options?

Are there any ways to avoid sticky session?
