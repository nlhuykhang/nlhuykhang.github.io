---
layout: post
title: Pigeon Release Toolbox
categories: [logs]
tags: [logs]
published: false
fullview: true
---

```
Array.from(document.querySelectorAll(".issue-key")).reduce((acc, el) => {
    return {
        ...acc,
        [el.innerText]: el.parentNode.nextSibling.firstElementChild.innerText
    };
}, {});
```

`git --no-pager log --pretty=format:"%h    %ad    %an    %s" master..staging`

`git rev-list --pretty 2eb35d966f6e99c11b20b85107567945c98df14f^...HEAD | grep PHL-`

`git rev-list --pretty=format:"%h    %ad    %an    %s" v3.0.1...v4.0.0 | grep PHL-`

`git merge-base master staging`