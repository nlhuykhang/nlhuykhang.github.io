---
layout: post
title: Mac - network throttling
categories: [server]
tags: [server]
published: false
fullview: true
---

`alias dn='sudo dnctl'`

`alias pf='sudo pfctl'`

`sudo dnctl pipe 1 config bw 10Kbit/s delay 300`
`sudo dnctl pipe 1 config bw 1Kbit/s`

`pfctl -e`
`pfctl -d`


`echo "dummynet out proto tcp from any to 127.0.0.1 pipe 1" | sudo pfctl -f -`

dnctl -q flush