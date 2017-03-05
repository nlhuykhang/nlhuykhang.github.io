---
layout: post
title: Clean Code Javascript
categories: [javascript]
tags: [javascript]
published: true
fullview: true
---

Warning this post is written with my poor English

# What is this?

This post is a list of principles to do clean code in Javascript. The information here is obtained from many sources: my personal experience, a wonderful, popular book named Clean Code, vlogs from some code tutors, other bloggers, etc...

# What is clean code?

There are plenty of definitions of what clean code is, you could spend some time with Google to know how famous developers around the world define clean node. For me, **clean code is code that works, easy to read, understand, develop and maintain by any developers not only its author**.

# How to make clean code in Javascript?

### Use meaningful and pronounceable variable names

```javascript

// NOTE: dirty code

const yymm = moment().format('YY/MM');

const mmddyy = moment().format('MM/DD/YY');

```

Look at the code above how would someone understand what `yymm` and `mmddyy` mean without knowing how `moment` works. Also, how would you discuss about these variable with your co-worker without being sound like stupid?? like: "Hey bud, is it something wrong with this y y m m variable?"

Therefore, the code should be written like this:

```javascript

// NOTE: clean code

const yearMonthTime = moment().format('YY/MM');

const monthDateYearTime = moment().format('MM/DD/YY');

```

### Use the same word for the same concept, eliminate noise words

```javascript

// NOTE: dirty code

getUser();
getUserInfo();
getUserObject();
fetchUser();
retrieveUser();
fetchPayment();

```

The dirty code above use three different words for the same concept: `get`, `fetch`, `retrieve`. This makes unnecessary confusion in the code. We'd better choose one word for one concept and use it across the code base.

The word `Info` and `Object` in `getUserInfo` and `getUserObject` is also unnecessary. Because we likely know that a user is represented with an object, and what do we expect when calling `getUserInfo`? user information of course. Therefore these words should be eliminated.

```javascript

// NOTE: clean code

getUser();
getPayment();

```
