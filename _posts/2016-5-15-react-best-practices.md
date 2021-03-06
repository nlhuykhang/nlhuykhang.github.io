---
layout: post
title: React - Best Practices
categories: [javascript, framework]
tags: [study, tech, react]
published: true
fullview: true
---

Warning this post is written with my poor english

# Introduction

This is a set of best practices when working with React. Many of this list come from the web and some are my own experience. I am going to update this post continuously as I work and experience more in React.

# Leverage classNames NPM module

One of the bad practice is passing your style and class names around component because:
- You have to know exactly the class or style the component expect
- It is easy to mess thing up by passing the wrong class
- String manipulation is tedious (for me)

To solve this I use [classNames](https://github.com/JedWatson/classnames), now instead of this:

    const Button = ({isPress, isHovered}) => {
      const btnClass = 'btn';

      if (isPress) {
        btn += ' btn-pressed';
      }

      if (isHovered) {
        btn += ' btn-hover';
      }

      return <button className={btnClass}>{this.props.label}</button>;
    };

I have this:

    const Button = ({isPress, isHovered}) => {
      const btnClass = classNames({
        'btn': true,
        'btn-pressed': isPress,
        'btn-hover': isHovered,
      });

      return <button className={btnClass}>{this.props.label}</button>;
    };

This way I can get rid of string manipulation, and more importantly I do not have to pass classes around, the component know what classes it needs to use itself.

# Keep your component as simple as possible

Why? because:

- Separation of concerns
- Easy to maintain
- Take advantage of React's built-in optimizations
- Having a smaller render function is much better, think about it like having a small function instead of a big, fat, thousand-lines function

# Use the PureRenderMixin

This is a official mix-in for React, its main purpose is to boost your component performance. The only requirement is that your component has to be "pure", meaning that it has to render exact the same result with the same state and props. There are also some other things need to consider, but I think this [official docs](https://facebook.github.io/react/docs/pure-render-mixin.html) explains it better than me.

# Avoid using state

Ok it is 0:00 am and I am lazy enough to stop here.

Just back from a wonderful final match. I feel very sorry for ATM now, but hey that is what life is, just experience it, feel it, then take it and let's go :).

Avoid state? why? because it is harder to maintain your component and headache to figure out why your component get update. So that you should minimize the use of state, and when you have to use it try to centralize it or make sure it only affect its own component.

# Minimize the bundle file size

By using the exact file path to load your modules, you can make the bundle file size smaller because you only load what you use, not the entire modules. E.g. instead of this:

    import { concat, map } from 'lodash';

You should write this:

    import concat from 'lodash/concat';
    import map from 'lodash/concat';

This will make a big surprise that you bundle file size is much smaller. But be aware that not all NPM modules are able to be used in this manner, so it requires a little testing at first.
