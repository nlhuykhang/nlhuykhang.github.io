---
layout: post
title: Meteor Deep Dive - Reactive Programming With Tracker
categories: [javascript, framework]
tags: [tech, meteor, deepdive]
published: true
fullview: true
---

Warning this post is written with my poor English

# Introduction

As a Meteor developer, I am sure everyone who has worked with Meteor all have experience with Tracker, or at least have used it through some kind of interface like getMeteorData or createContainer which come with the react-meteor-data package.

But not all people know how Tracker works it magic, so within this post I am going to write about that and a bit of background for the start.

# What is Tracker?

**[Tracker](https://github.com/meteor/meteor/tree/devel/packages/tracker) is a Meteor's core package**, it is small but incredibly powerful library for **transparent reactive programming** in Meteor.

Using Tracker you have much of the power of [Functional Reactive Programming (FRP)](https://en.wikipedia.org/wiki/Functional_reactive_programming) system without following FRP's principles when implementing your application. Combined with Tracker-aware libraries, like Session/ReactiveVar/ReactiveDict, this lets you build complex event-driven programs without writing a lot of boilerplate event-handling code.

# What is great about it?

In the nutshell, it is **reactivity**. IMO, it is the strongest aspect of Meteor. Tracker helps us make our system works reactively both on client and server with ease. We do not have to learn any extra stuffs about reactive programming or functional programming to get started.

Just read the Tracker api and do the work then the magic happens. Or even some new developers who just come to Meteor do not know about Tracker but their code just work reactively. Do you know what I am talking about? it is good old [Blaze](https://goo.gl/yf7j4E) (I say it's old because I already moved to React for all new projects).

Blaze's helpers are reactive natively because they use Tracker inside, if you put inside them a [reactive data source](http://goo.gl/6DDdAn) then whenever that source changes those helpers will be recompute and you get new data on the screen.

Let's read some code and behold what I am talking about, if you are already familiar with Tracker, keep this part and move to the next section to inspect the magic.

```javascript

// Set up the reactive code
const counter1 = new ReactiveVar(0);
const counter2 = new ReactiveVar(0);

const observeCounter = function() {
  Tracker.autorun(function() {
    const text = `Counter1 is now: ${counter1.get()}`;
    console.warn(text);
  });
  console.warn(`Counter2 is now: ${counter2.get()}`);
};

const computation = Tracker.autorun(observeCounter);

/*
Message on the console:
Counter1 is now: 0
Counter2 is now: 0
*/

// and now change the counter1's value
counter1.set(1);
/*
Message on the console:
Counter1 is now: 1
*/

counter2.set(3);
/*
Message on the console:
Counter1 is now: 1
Counter2 is now: 3
*/

counter1.set(7);
/*
Message on the console:
Counter1 is now: 7
*/

```

In reality, it happens as shown below:

gif image of above code

# How does Tracker work?

Basically Tracker is a simple interface that lets reactive data sources (like *counter* in the example above) talk to reactive data consumers (the observeCounter function). Below is the flow of Tracker (I ignore some good parts to make it as simple as possible)

- Call Tracker.autorun with function F
- If inside F, there is a reactive data source named R, then that source will add F to its dependent list
- Then whenever the value of R changes, R will retrieve all its dependent from the dependent's list and run them.

Okay, that is easy to say but how does it happen?

Let look at the code:

```javascript

  const counter = new ReactiveVar(1);

  const f = function() {
    console.log(counter.get());
  };

  Tracker.autorun(f);

```

In the above code: counter is our reactive data source, the problem here is that how can it know what function it is used inside to add that function as its dependent.

This is where the Meteor team does their trick to make this flow transparent. In fact, Tracker is an implementation of the Observer pattern or at least an Observer-liked pattern. Observer pattern can be used to create a reactive library like Tracker.

In an traditional implementation of Observer, we can think of F as an observer and R as a subject. R must have an interface to add F as its observer and notify/run F when its value changes. Something like this:

```javascript

  const counter = new Source();

  const f = function(val) {
    console.log(val);
  };

  counter.addObserver(f);

```

To imitate this, Tracker provide us these interface:

  + Tracker.autorun
  + Tracker.Dependency
  + Tracker.Dependency.prototype.depend
  + Tracker.Dependency.prototype.changed

Tracker.Dependency is the implementation of Subject in traditional Observer. All of reactive data source to use with Tracker use this object inside.

Let look at the basic implementation of ReactiveVar I use for examples above:

```javascript

  ReactiveVar = function (initialValue, equalsFunc) {
    if (! (this instanceof ReactiveVar))
      // called without `new`
      return new ReactiveVar(initialValue, equalsFunc);

    this.curValue = initialValue;
    this.equalsFunc = equalsFunc;
    this.dep = new Tracker.Dependency;
  };

  ReactiveVar.prototype.get = function () {
    if (Tracker.active)
      this.dep.depend();

    return this.curValue;
  };

  ReactiveVar.prototype.set = function (newValue) {
    var oldValue = this.curValue;

    if ((this.equalsFunc || ReactiveVar._isEqual)(oldValue, newValue))
      // value is same as last time
      return;

    this.curValue = newValue;
    this.dep.changed();
  };

```

So when we create a new instance of ReactiveVar, a Tracker.Dependency object will be created. This object will have two main function depend and changed with get call inside get and set function respectively.

This is the Tracker's flow with more details:

Tracker.autorun will create a computation with the function (F) pass to it as the computation's props.

```javascript

  // https://github.com/meteor/meteor/blob/devel/packages/tracker/tracker.js#L569-L585
  Tracker.autorun = function(f, options) {
    // ...
    var c = new Tracker.Computation(f, Tracker.currentComputation, options.onError);

    // ...

    return c;
  };

```

When being initiated, this computation is also set as the current computation inside a "global" variable named Tracker.currentComputation. And F will be run for for first time.

```javascript

  // https://github.com/meteor/meteor/blob/devel/packages/tracker/tracker.js#L146-L208
  Tracker.Computation = function(f, parent, onError) {
    // ...
    self._func = f;
    self._onError = onError;
    self._recomputing = false;

    var errored = true;
    try {
      self._compute();
      errored = false;
    } finally {
      self.firstRun = false;
      if (errored)
        self.stop();
    }
  };

  // https://github.com/meteor/meteor/blob/devel/packages/tracker/tracker.js#L302-L316
  Tracker.Computation.prototype._compute = function() {
    var self = this;
    self.invalidated = false;

    var previous = Tracker.currentComputation;
    setCurrentComputation(self);
    var previousInCompute = inCompute;
    inCompute = true;
    try {
      withNoYieldsAllowed(self._func)(self);
    } finally {
      setCurrentComputation(previous);
      inCompute = previousInCompute;
    }
  };

  // https://github.com/meteor/meteor/blob/devel/packages/tracker/tracker.js#L29-L32
  var setCurrentComputation = function(c) {
    Tracker.currentComputation = c;
    Tracker.active = !!c;
  };

```

If inside body of F there is a .get operation (meaning .depend), this function will be run and set the current computation stored in the global var named Tracker.currentComputation as it's dependent.

```javascript

  // https://github.com/meteor/meteor/blob/devel/packages/tracker/tracker.js#L403-L420
  Tracker.Dependency.prototype.depend = function(computation) {
    if (!computation) {
      // ...
      computation = Tracker.currentComputation;
    }
    var self = this;
    var id = computation._id;

    if (!(id in self._dependentsById)) {
      self._dependentsById[id] = computation;
      // ...
      return true;
    }
    return false;
  };

```

Then whenever .set is call (meaning .changed), F will be rerun

```javascript

  // https://github.com/meteor/meteor/blob/devel/packages/tracker/tracker.js#L428-L432
  Tracker.Dependency.prototype.changed = function() {
    var self = this;
    for (var id in self._dependentsById)
      self._dependentsById[id].invalidate();
  };

```

Yeah so it is the basic idea. Beyond this basic flow actually there are some other important things to be take care for to have a complete production-ready Tracker library. I am not going to write about those things, instead I will just name them. And you can go and check yourself to really understand Tracker. They are:

- Tracker inside Tracker (computation inside computation)
- Clear stopped computations
- Prevent infinite loop

# Takeaways

- Tracker make it possible to do reactive programming in Meteor (actually it seems like not only in Meteor, but I never check)
- It is an observer-liked implementation
- A good trick: use a "global currentComputation" to implement transparent Observer
- Those guys who wrote Tracker are awesome
