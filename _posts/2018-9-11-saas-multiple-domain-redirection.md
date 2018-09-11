---
layout: post
title: SaaS - multiple domains redirection
categories: [logs, questions]
tags: [logs, questions]
published: false
fullview: true
---


### What do i know so far?

- `cname`
- hsts to restrict unauthorized cname
- saas does redirect to client's site based on `Host` or `:authority` header after ssl termination phase
- saas ssl termination point _must support_ multiple domains with Server Name Indication (SNI)
- saas can either require client to provide ssl cert or generate a cert for client (Let's encrypt)

### Links

- https://codeascraft.com/2017/01/31/how-etsy-manages-https-and-ssl-certificates-for-custom-domains-on-pattern/