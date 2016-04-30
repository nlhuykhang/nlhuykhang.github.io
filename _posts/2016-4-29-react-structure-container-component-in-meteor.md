---
layout: post
title: React - Structure Container Component In Meteor
categories: [javascript, framework]
tags: [study, tech, react, meteor, ramda]
published: true
fullview: true
---

Warning:

- This post is written with my poor english
- I am writing this in perspective of React 0.14.3 used in Meteor 1.2.1

# Introduction

Over the past two months, I have been working on an interesting project at my company which is supposed to be a very big one. So at first, as a team leader and core maker, I spent quite a lot of time to find a good starting point which satisfies all the project's requirements. By starting point, I mean stuffs like how to structure the project, which frontend stack to use, how that stack connects to server, how to set up and bring all those things together etc...

Finally, I settled down with **Meteor** for backend, **React** for frontend, and **Vanilajs ES6 MVC** for the app extension (this is the most interesting part of the project, it will have its own post later). At first thing went well and smooth, needless to say that React is very good it is something that change the way I think when program.

But soon later, because this is the first time I really get on to build a big project with react, thing started to get messy. I encountered the problem that every big project coded in React has. This post is about that problem and how I attempt to solve it.

# Prerequisites

React is introduced to be simple and very easy to learn for beginner. That is definitely true, but it takes quite an effort to move belong that beginner level.

Unfortunately, this post is not aimed for beginner because I am writing about experience I learned in a big project, not only that the way to use React in this post is for Meteor project. So it may require reader to have the following knowledge to understand what I am going to write:

- [React container component](https://goo.gl/xxGHnu)
- [React unidirectional data flow](http://redux.js.org/docs/basics/DataFlow.html)
- [React Mixins](http://goo.gl/mDj9fO)
- [React in Meteor 1.2](https://goo.gl/V5v271)
- [Ramdajs](http://goo.gl/x2Sblm)

# The problem that I encountered

I can summary the problem with only one word, and that is **maintainable**.

Follow the unidirectional data flow principle, I created a container component for each routes for the problem. This component is in charge of almost every logic stuffs for the route it is using for. E.g. it takes care of subscribe data/call methods from Meteor server, be a middle man for its children component talk to each others. With all those missions, soon it becomes a very big and fat guy.

The file I coded the container component in was about 700 lines when I just finished about half of the work. It is clearly that this is unacceptable, if I had just ignored this problem and kept coding to get the job done fast, it would have been a nightmare for maintainer/debugger.

Knowing that this project is my company's product, meaning that my colleagues and I will be working on it for years. I stopped coding feature and started refactoring the code.

# First attempt

I looked at all the container's functions and realized that they can be categorized into four groups:

- Component must-have functions: getInitialState, getMeteorData, render, etc...
- Handler functions: all event handlers for all the container's children component
- Helper functions: functions to help get necessary data for container's children component based on container props and state. There functions are merely pure functions, they take input and produce output without touching the container state or talk to Meteor server.
- Logic functions: functions to modify the component state, call methods from server. These functions are called by event handlers in response to user actions

With this model in mind, I decouple the last three groups' functions into three separate files and import them back in the container through mixins.

The directory of the container now looks like:

```
root
|   ...
|---...
|---poppi:client-container-example-component
    |---client
        |---components
            |---subcomponent1
                |   ...
            |---subcomponent2
                |   ...
            |   ContainerExample.jsx
            |   ContainerExampleContainer.jsx
            |   ContainerExampleHandlers.js
            |   ContainerExampleHelpers.js
            |   ContainerExampleLogicFuncs.js
    |   package.js
|---...
```

And the code of the container

    ContainerExampleContainer = React.createClass({
      mixins: [
        ReactMeteorData,
        ContainerExampleHandlers,
        ContainerExampleHelpers,
        ContainerExampleLogicFuncs,
      ],
      getInitialState() {
        ...
      },
      getMeteorData() {
        ...
      },
      render() {
        ...
      },
    });

Now my container looks much better, it becomes a thinner and more handsome guy. This decouple technique is also a more semantic way and makes it faster when you need to find a function. But there are something still border me, it even can be better.

# Push it a bit further

Even though I separate all functions into 3 different mixin files, it is still unclear that which file a functions to. Every time I have to check all those 3 functions to identify the funciton place. This is not good enough, I need a more semantic way. A way that allows me identify the file by just looking at the function calling statement.

After a bit of thinking, I fall back to the basic technique widely in js: "namespace". I group all the functions under a namespace obj inside the mixin obj. My three mixin file now become:

    // ContainerExampleHandlers.js
    ContainerExampleHandlers = {
      Ha: {
        clickHandler() {
          this.L.test1();
          return;
        },
      },
    };

    // ContainerExampleHelpers.js
    ContainerExampleHelpers = {
      He: {
        getData(state) {
          return {
            state
          };
        },
      }
    };

    // ContainerExampleLogicFuncs.js
    ContainerExampleLogicFuncs = {
      L: {
        test1(param1) {
          const data = this.He.getData(param1);
          this.setState({
            ...data,
          });
        },
        test2(param2) {
          Meteor.call('test2', param2);
        }
      },
    };

Now by just looking at the calling function statement, I know exactly where that function comes from. This is extremely useful and efficient when working in a big, complicated project.

But unfortunately I still could not enjoy the fantasy of this technique, because the scope inside all functions are wrong. This inside these functions is referring to the outer object, not the container component which is expected.

So I have to bind their contexts to the right one.

# Ramda to the rescue

The binding process can be done easily by a for-in loop inside the **getInitialState** function of the container. But it is required more than that, I need a more unified, modern, easier to use and understand way, also shorter to use in this big project. Luckily, the rise of **Ramda** recently causes my attention and proves that it is the best match for my requirements.

Finally I come up with this binding function:

    // bindMixinFunctions.js
    bindMixinFunctions = R.converge(function(keys, values, ctx) {
      const bindValues = R.map((val) => R.bind(val, ctx), values);
      return R.zipObj(keys, bindValues);
    }, [
      R.flip(R.keys),
      R.flip(R.values),
      R.identity
    ]);

And use it in action:

    ContainerExampleContainer = React.createClass({
      mixins: [
        ReactMeteorData,
        ContainerExampleHandlers,
        ContainerExampleLogicFuncs,
        ContainerExampleHelpers,
      ],
      getInitialState() {
        const thisBindMixinFunctions = bindMixinFunctions(this);

        this.He = thisBindMixinFunctions(this.He);
        this.Ha = thisBindMixinFunctions(this.Ha);
        this.L = thisBindMixinFunctions(this.L);

        ...
      },
      ...
    });

Now after all the hard work, I am able the enjoy the result. The code runs as expected and is much better in term of structuring, semantic and maintainable. I do not have to worry about later maintaining/debugging process any more.

# Conclusion

It may be a bit tedious to write all this code and structuring before implementing the real features. But for me it is really worthy, this pattern actually save me a lot of time and headaches trying to figure out where a function is, what it does, how it connects to others functions.

If you are still using React version 0.14.3 in Meteor 1.2, which is possibly if you already worked in these frameworks, I hope that you will find the benefit in my pattern and take the best of it.

If you are going to set up a new project with the latest version of React and Meteor and decide to implement this pattern, I would love to hear how you do that.
