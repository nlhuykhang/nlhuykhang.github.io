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

### Number of arguments should be less than 2

```javascript

// NOTE: dirty code

function insertStudent(name, subjects, age, address, gpa) {
  // do sth
}

insertStudent(/* pass 5 arguments here*/);

```

We'd better use an object containing all the needed arguments and pass to `insertStudnet`

```javascript

// NOTE: clean code

const student = {
  name: '...',
  subjects: '...',
  age: '...',
  address: '...',
  gpa: '...',
};

function insertStudent(student) {
  // do sth
}

insertStudent(student);

```

### Functions should be small, named explicitly, and do only one thing (one level of abstraction)

```javascript

// NOTE: dirty code

function sendMail(email) {
  // ...
}

function sendMailToCSStudents(studentList) {
  for (let i = 0; i < studentList.length; i++) {
    if (studentList[i].major === 'CS') {
      sendMail(studentList[i].email);
    }
  }
}

```

Look at two block of code above and below, which one is more readable for you? For me the later one is surely superior. Just be looking at the functions' names, I know what they do, and how they do without reading their body.

```javascript

// NOTE: clean code

function sendMail(email) {
  // ...
}

function isCSStudent(student) {
  return student && student.major === 'CS';
}

function getEmail(student) {
  return student && student.email;
}

function sendMailToCSStudents(studentList) {
  studentList.filter(isCSStudent).map(getEmail).forEach(sendMail);
}

```

### Avoid side effects

```javascript

// NOTE: dirty code

let counter = 1;

function increaseCounter() {
  counter++;
}

increaseCounter();

console.log(counter);

```

The code above has two problems:

- First, it assumes that reader knows what `counter` is, and where it comes from. If you are the author, it is likely that you know (really???). If you are a maintainer, good luck crawling thousands line of code to find what `counter` is and what modify it.
- Second, it mutates a global values which may be used by other parts of the application. Let imagine you expect the value of `counter` equals to `1`, but surprisingly it is `2` because someone calls `increaseCounter` and changes the value of `counter`.

```javascript

// NOTE: clean code

let counter = 1;

function increaseCounter(counter) {
  return counter + 1;
}

let newCounter = increaseCounter(counter);

console.log(newCounter);

```

### Favor functional programming

#### Immutable values

```javascript

// NOTE: dirty code

const student = {
  name: 'Khang',
  age: '22',
};

function setStudentAge(student, age) {
  student.age = age;
}

setStudentAge(student, 23);

console.log(student.age);

```

In functional programming, variable values are immutable, it is believe to produce less bugs in your code. Therefore, the code above should be refactored like below:

```javascript

// NOTE: clean code

const student = {
  name: 'Khang',
  age: '22',
};

function setStudentAge(student, newAge) {
  return Object.assign({}, student, { age: newAge})
}

const newStudent = setStudentAge(student, 23);

console.log(newStudent.age);

```

#### Declarative over imperative

```javascript

// NOTE: dirty code

const students = [
  {
    name: 'Khang Nguyen-Le',
    year: '4th',
  },
  // ...
];

function getSeniorStudentInitalsListName(studentList) {
  const initialsList = [];

  for (let i = 0; i < studentList.length; i++) {
    const student = studentList[i];

    if (student.year === '4th') {
      let initial = '';
      const splitName = student.name.split(' ');

      for (let j = 0; j < splitName.length; j++) {
        initial += splitName[j][0];
      }

      initialsList.push(initial);
    }
  }

  return initialsList;
}

console.log(getSeniorStudentInitalsListName(students));

```

If you have never learned to code in a functional way, the code above is what you likely to write. It is imperative code, although it could do the job fine. There are some problems with imperative code

- Hard to read and understand, do you know what `student.year === '4th'` mean?
- Hard to test, you can only write test for the big function `getSeniorStudentInitalsListName`, not smaller blocks inside it
- There are so many variables to keep track of

To solve these problems, we could rewrite the above function in a declarative way:

```javascript

// NOTE: clean code

const students = [
  {
    name: 'Khang Nguyen-Le',
    year: '4th',
  },
  // ...
];


function isSeniorStudent(student) {
  return student && student.year === '4th';
}

function getStudentName(student) {
  return student && student.name;
}

function splitBySpace(str) {
  return str && str.split(' ') || [];
}

function getFirstCharacter(str) {
  return str && str[0] || '';
}

function getInitials(name) {
  return splitBySpace(name).map(getFirstCharacter).join('');
}

function getSeniorStudentInitalsListName(studentList) {
  return studentList.filter(isSeniorStudent)
    .map(getStudentName)
    .map(getInitials);
}

console.log(getSeniorStudentInitalsListName(students));

```

### Encapsulate conditionals

```javascript

// NOTE: dirty code

const student = {
    name: 'Khang Nguyen-Le',
    year: 4,
};

if (student.year === 4) {
  // do sth
}

```

Do you know what the condition `student.year === 4` means? I guess not, so to help you out I will refactor the code like below:

```javascript

// NOTE: clean code

const student = {
    name: 'Khang Nguyen-Le',
    year: 4,
};

function isSeniorStudent(student) {
  return student && student.year === 4;
}

if (isSeniorStudent(student)) {
  // do sth
}

```
