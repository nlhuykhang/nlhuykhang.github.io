---
layout: post
title: React - My Handbook
categories: [react, javascript]
tags: [study, tech]
published: false
fullview: true
---

<h2>What do we need Virtual DOM?</h2>
Because the traditional DOM is:

- hard to manage
  usually we use jquery to manipulate the dom, and soon in a large app we run into situation when some weird behaviors happen without knowing why. That is because there many event handler on the same event of same element. React may have us here, tight coupling does have here

- and has a low performance compare to its virtual version
  Virtual dom provides the ability to compare old state with new state, then get the diffs and make the changes to real dom according to the diffs so that the performance is far better

<h2>Life cycle</h2>
shouldComponentUpdate
componentWillUpdate   - no setState
componentDidUpdate

be careful when calling setState inside life cycle, because it may cause infinite loop
