---
layout: post
title: Javascript - Implement functional methods
categories: [vim]
tags: [study, tech]
published: false
fullview: true
---


```
    Function.prototype.partialLR = function(...args1) {
      const func = this;

      return function(...args2) {
        return func.apply(this, [...args1, ...args2])
      };

    };
```


```
    Function.prototype.partialRL = function(...args1) {
      const func = this;

      args1.reverse();

      return function(...args2) {

        args2.reverse();

        return func.apply(this, [...args2, ...args1])
      };

    };
```

```
    Function.prototype.curry = function(numArgs) {
      const func = this;
      const length = numArgs || func.length;

      function subCurry(args1) {
        return function(...args2) {
          const arr = [..args1, ...args2];

          if (arr.length < length) {
            subCurry(arr);
          } else {
            func.apply(this, args);
          }
        };
      }

      return subCurry([]);
    };
```


```
  Function.prototype.compose = function(prevFunc) {
    const func = this;
    return function(...args) {
      return func.call(this, prevFunc.apply(this, args));
    };
  };
```

```
  Function.prototype.composeR = function(nextFunc) {
    const func = this;

    return function(..args) {
      return nextFunc.call(this, func.apply(this, args));
    };
  };
```


```
  const composeF = function(...args1) {
    return function(...arg2) {
      const length = args1.length;

      let func = args1[length - 1];

      for(let i = args1.length - 2; i >= 0; i--) {
        func = args1[i].call(this, func.apply(this, args2));
      }

      return func;
    };
  };
```

# Category Theory

Categories are just sets with the same type

Morphisms are pure functions that, when given a speci c set of inputs, always return the same output

Homomorphic operations are restricted to a single category

polymorphic operations can operate on multiple categories

While morphisms are mappings between types, functors are mappings between categories

Monads are tools that help you compose functions.

monads are structures that can be used as the containers that functors "reach into"

There are three monads we'll focus on:
  • Maybes
  • Promises
  • Lenses

we'll have five functors (map, compose, maybe, promise and lens)

A maybe is a variable that either has some value or it doesn't

Maybes are just tools to help us keep the logic  owing through the pipeline.
