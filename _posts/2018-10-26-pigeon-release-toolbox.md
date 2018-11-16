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