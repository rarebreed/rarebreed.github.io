---
layout: post
title:  "What are continuations really?"
date:   2016-05-20 07:21:40 -0400
categories: purescript notes continuation
---
A couple concepts have always mystified me in computer science.  The mystique of Monads is slowly disappearing, though I
still have a bit of a ways to make them obvious and intuitive to me.  Coroutines are another concept that still trips
me up though in principal, they are just re-entrant functions.  And of course, there's a whole plethora of algorithm
design that I need to revisit (especially graph theory and persistent data structures).

But one concept that I've always had trouble wrapping my head around was a continuation.  Continuations were one of the
hallmarks of scheme and from reading some articles about them, I got the idea that continuations were:

- amazingly powerful and could act like a time machine in a way
- notoriously hard to follow
- just another name for a callback

But somehow, the essence of what a continuation is has eluded me.  I mean, if you just look at the definition, it does
not seem that hard to follow.  A continuation is "the rest of the program".  But what does that mean?  Also, why do some
languages have first class support for continuations, and other languages need to do continuations in a Continuation
Passing Style (CPS)?  Moreover, why do I even care now?

# Asynchronous programs

So, let me answer that last question first.  Because I have started writing purescript code, and I am running it on the
node interpreter, I am forced to deal with asynchronous programming.  I've often thought that I don't know what is more
difficult, multi-threaded (shared state) programming, or asynchronous (shared state) programming.  Actually, they're
both categories of concurrent programming, it's just that asynchronous programs (usually) run on a single process of
execution (ie, a thread, process, fiber, etc etc).  In node, almost all the functions that deal with IO are blocking, as
are functions which spawn a child process for example.  

The idea is that since javascript is single-threaded, you don't want to block other computations from happening in a
blocking call.  So, an asynchronous function will go off and down its thing in the background but return immediately.
This has ramifications when functions have a dependency on each other.  Take for example some code like this:

```javascript
let attack = (skill, attrs, modifiers) => {
  // Chance to hit is skill + average of attrs, + modifiers and returns a target number
  let avgAttr =
}
```
