---
layout: post
title: Just Questions
categories: [logs, questions]
tags: [logs, questions]
published: false
fullview: true
---

- how does health check work?

Server public an endpoint for health check. Other servers, usually load balancer, make request to this endpoint and decide the status of server based on the response. If the server responses with status 200 without the predetermined timeout, that means it is working normally. If something other than 200 is response, or the timeout is exceeded, that means something is wrong.

- how does tcp work?
This is hard to explain in detail, so just remember it is a way to send data across the network. It guarantees that packets will not be lost and received in order.

- how does http work?
Just a protocol on top of tcp. It defines a way for client and server to talk to each others. Stuffs like: method, header, data, response... . A HTTP parser needs to exacts these information and formats in a way that its callers understand.

- what is a http parser? why is it necessary?
above

- eli5: what is the internet?
hmmmmmm

- how does computer keep track of time? how does high level timer (in programming language) work?
- how does async/await work? how does Babel implement them?

- how does websocket protocol work?

- how does wifi/paging system work? wifi vs radio wave?
