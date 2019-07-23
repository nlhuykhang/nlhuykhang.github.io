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

`docker run --name myadmin -d -p 8080:80 -e PMA_HOST=192.168.1.10 -e PMA_USER=root -e PMA_PORT=3306  phpmyadmin/phpmyadmin`



```
[...document.querySelectorAll("tbody.release-report-issues tr")].reduce((acc, cur, i, arr) => {
    const type = cur.querySelector("td.type img").alt;
    const key = cur.querySelector("td.key a").text;
    const title = cur.querySelector("td.summary").title;
    let component = "¯\_(ツ)_/¯";

    switch (true) {
        case /^API/i.test(title):
            component = "API";
            break;
        case /^webapp|Projector|Admin\/Moderator|AWA|Admin Panel|Moderator Panel|Projector Panel|Kiosk Panel|Admin, Moderator Panel/i.test(title):
            component = "Webapp";
            break;
        case /^Dashboard/i.test(title):
            component = "Dashboard";
            break;
    }

    acc[component] = acc[component] || {};
    acc[component][type] = [`${key}: ${title}`, ...(acc[component][type] || [])];

    if (i !== arr.length - 1) {
        return acc;
    }

    function toString(component) {
        let re = `${component}:\n`;

        Object.keys(acc[component]).forEach((type) => {
            re += `\n  ${type}:\n`;
            re += `    ${acc[component][type].join("\n    ")}`;
        });

        return re;
    }

    return Object.keys(acc).map(toString).join("\n\n");
}, {})
```
