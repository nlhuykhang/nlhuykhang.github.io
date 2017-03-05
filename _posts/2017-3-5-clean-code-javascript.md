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

### Use searchable and explanatory variable names

```javascript

// NOTE: dirty code

const N = 1000;

// hundred lines of code ...

for (let i = 0; i < N; i++) {
  // do something with i
}

```

Now imagine you need to search for the value of constant `N` in the above code. Because `N` is a one-letter word so when searching for it you may come across many words containing the `n` letter. Therefore `N` is very un-searchable. Also just by looking at the code above, we have no idea what the value of `N` could mean, it will make it hard for developer to understand the code.

Note: we usually use `i`, `j` as the incremental variables inside loops. This is due to the tradition of code (I guess), we all start will it. Anyways, that is fine if you only have a small loop block which could fit all in your editor view (do not have to scroll to read the whole block). When your loop block has more line, consider using another name of `i`, `j`.

```javascript

// NOTE: clean code

const NUMBER_OF_ROWS = 1000;

// hundred lines of code ...

for (let i = 0; i < NUMBER_OF_ROWS; i++) {
  // do something with i
}

```

### Don't add unneeded context

```javascript

// NOTE: dirty code

const student = {
  studentName: 'Khang',
  studentClass: 'CS',
  studentAge: '23',
};

```

The prefix `student` is clearly unneeded in this situation. Since those properties are already inside the `student` object.

```javascript

// NOTE: clean code

const student = {
  name: 'Khang',
  class: 'CS',
  age: '23',
};

```

### Leverage short-circuiting

```javascript

// NOTE: dirty code

function getName(person) {
  if (person) {
    if (person.name) {
      return person.name;
    } else {
      return 'Noname';
    }
  } else {
    return 'Noname';
  }
}

```

The code above is long and hard to read even though it is just used for a simple purpose that is to get `name` property of `person` object, and return `Noname` if `person` or `person.name` are missing. We could make this function much shorter and easier to read by using short-circuiting.

```javascript

// NOTE: clean code

function getName(person) {
  return (person && person.name) || 'Noname';
}

```
