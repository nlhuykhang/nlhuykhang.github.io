---
layout: post
title: Git
categories: [server]
tags: [server]
published: false
fullview: true
---

`git --no-pager log --pretty=format:%an%x09%x09%s HEAD~10..HEAD`

`git --no-pager log --pretty=format:%an%x09%x09%s master..staging`

`git --no-pager log --grep='something'`

`git merge-base master staging`
