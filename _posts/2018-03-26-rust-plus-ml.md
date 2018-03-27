---
layout: post
title:  "What do rust, ML and algorithms have in common?"
date:   2018-03-26 18:21:40 -0400
categories: rust ml
---
# Learning project for rust

I've slowly been picking up rust for a variety of reasons.  I'd love to do some _real_ haskell, but realistically, I 
need to be able to kill two birds with one stone.  Practically speaking, this means I have to do something that can 
align well with what I am doing at work.  The sad fact is that once I am done with work, my brain is mostly dead.  I
can still read about tech, I just can't practice.

Since I am doing more networking and full stack kind of work, I was trying to figure out what I could do with rust.
There was some talk at work where a guy had an open source project about using some Machine Learning algorithms to help
with QE related stuff.  For example:

- Clustering: What tests find the most bugs (Not just the testcase, but what the test is doing that makes it useful)?
  - Bayesian Belief Networks
  - kNN
- Classifying: determine if failures are actual assertion errors or infrastructure errors
  - Neural Nets
  - SVM's
- Anomaly Detection: Find outliers or unusual data
- Prediction: Analyze logs, JMX monitoring, valgrind etc to predict impending failures
  - linear and logistic regressions

That kind of stuff fascinates me.  Truth be told, I went into Comp Sci years ago for Artificial Intelligence.  Like
many, I was into computer games, but rather than graphics, I wanted to make AI smarter.  Sadly, _the real world_ 
never allowed me to get into that kind of stuff.  But it has always fascinated me.

So it dawned me, why not learn Rust and ML at the same time?

## Why rust?

Perhaps one might be wondering if my work is currently mostly microservices and front end web stuff, why I would want
to be getting into rust.

First off, rust is the only language that can truly run anywhere.  Rust can now compile down to webassembly, and firefox
now has support for wasm modules (though it's a bit tricky at the moment).  The rust community is also going full on
gonzo supporting the web platform.  Imagine the potential here...

- Write near native speed code
- That can be loaded in a browser (no hassle for end user)
- That can interact with your javascript code and webworkers
- Or write code that can run on IoT machines
- And talk to microservices via MQTT

In essence, it would supplant even javascript and java as the "run anywhere" languages.  You can't get javascript or 
java to run on memory-constrained cortex m3 machines.  And if you need true real-time (not soft runtime) thanks to 
no garbage collectors, or running on tiny devices (128k or less RAM), then C(++) or rust are really the only game 
in town.

## A weapon for a more civilized age

The little bit I have played with rust and read up on it, it does not feel like your grandfather's system programming
language.  There's so much that has improved, that it almost feels like coding in a much higher language like kotlin.

- Modules (no #include of static code!!)
- Crates (rust's version of npm, pip, maven, etc)
- toml instead of hideous make or cmake files
- Higher level abstractions than Java or even Kotlin (really Kotlin...no pattern matching?)
  - enums which are like haskell Sum Types
  - pattern matching and destructuring (though I have not seen pattern guards)
  - impl solves the _expression problem_ for rust
- I know people give elm mighty props for having awesome compiler error messages, but it would be hard to beat rustc
- Don't have to worry when to malloc/free or new/delete (but see below)
- No black box

There are a couple of rough spots though:

- No tail call optimization
- Lifetimes are pretty gross looking, and hard to wrap your head around
- Who thought shadowing was a good idea?
- Instead of memory management ala C(++) to keep track in your head, you have ownership management to keep track of
- higher order functions get complicated
  - Fn, FnMut and FnOnce
  - Closures have anonymous types


## The good

One of the things I really hated about c(++) programming was just building stuff.  I did c(++) programming before cmake
got popular, but even when I looked at it, it didn't seem to help that much.  And heaven help you if you had to write
autoconf or automake scripts.  I spent as much time fighting compilation issues as feature bugs.

So rust's modules and crate system is awesome.  The #include system of c(++) should have died ages ago, but never did.
And if you did c++ templates, you had to write your implementations in the header files, which never made sense to me.
And remember all those inclusion header guards?

Rust's modules feel like any other module system.  I have not yet figured out if it solves dependency version hell. But
this is definitely the right direction.  The toml files are nicer than makefiles (don't forget the tabs!).  I even 
used waf, which was a build system written in python for c(++) projects which was a bit nicer, but still pretty ugly.

For some people, the biggest selling point of rust is going to be easier memory management.  Or at least, it seems that
way.  Afterall, there's no new/malloc or free/delete in rust!  It does have a Drop Trait however, which would be the
equivalent of a destructor in C++ parlance.  There's also Box<T> types, which are like smart pointers.  I haven't
gone into those yet though.  Nevertheless, if you know c++ style RAII, rust's compiler essentially enforces it.  That
is how rust becomes memory safe.

For me though, the biggest sell is rust's abstractions.  c++11 and beyond added lambdas finally, but it doesn't have
pattern matching, and while you can probably fake Sum types using template programming, that would just be gross.  Rust
ticks off most of my feature wants:

- immutable by default
- Traits which are like java interfaces or haskell typeclasses
- impl which are similar to haskell's class and solve the expression problem (types are open for extension)
- enums which are like haskell Sum types or Typescript's Union types
- higher order functions (though these are a bit messy)

I think I am ready to go back into a language that doesn't treat you with kid-gloves.  Inevitably, in a higher level
language you will hit some strange and bizarre error.  And then you will stare at it blankly, for a long long time.
Trying to get your debugger to go deeper and deeper into the guts of some library, or worse, the runtime of the 
language.  When there is too much hand-waving going on, and your language tells you "please don't look at the man
behind the curtains", this can bite you bad.  Sure, having to work with raw bits, bytes, and native executables may
seem scary, but they are also for the most part exposed.

Lastly, I am seriously impressed with the compiler error messages.  It practically tells you how to fix your code.
Granted, it's been a few years since I did c(++) coding, and the clang compiler did improve things quite a bit, but
sometimes I wanted to smash my face through the monitor trying to figure out byzantine error messages (ever forget
to put a semi-colon at the end of a class or struct?)

## The bad

While rust frees you from memory safety problems, it does come with new sets of things to wrap your head around and 
keep track of.  Fortunately, the compiler will tell you when your head failed to keep track of something.

### Ownership

The first thing to understand is the concept of ownership.  Only one thing can own a value, however it is possible
to lend out access to the underlying value through references.  If you take a reference of a variable, that is called
_borrowing_.  You have two kinds:

- mutable references _exclusively_ take access to the value of a variable
- immutable references _share_ access to the value of a variable

You can have as many immutable references to a variable as you want, or a single mutable reference, but you can **not**

- have immutable and mutable references to a variable
- more than one mutable reference

### Move vs. Copy semantics

The c++11 standard got move semantics, and rust does this too.  So what's the difference, and why is it tricky?  To 
explain the difference think about the linux commands:

```
cp file1.txt file1-copy.text
mv file1.txt file2.txt
```

When you run the cp command, you make a copy of the file so that its contents are in a newly named file.  The original
file still exists unchanged.  With the mv command, you basically rename the file (I forgot if the mv command actually
changes the inode in the filesystem or not though).  In rust, if an object has the Copy Trait, when you assign a new
variable with the = operator, it will do a copy.  If the object does not have the Copy Trait, then a move is performed
instead.

```rust
let x = 10;
let y = x;   // y has value of 10, and x is also still useable.  ownership not transferred

let s = String::from("Sean");
let t = s;   // String does not have the Copy Trait, so t now owns the contents that were in s, and s is dropped

// println!("value of s is {}", s)   // compiler complains because s was moved into t
```

The difference between what moves and what is copied means you as a programmer have to know what Traits it supplies
when you do assignments.

### Lifetimes 

Lifetimes eliminate dangling references (a reference to an object which no longer exists) but are not intuitive. The 
book even states that lifetimes are actually the defining feature of rust since no other lang has this concept.  I 
think I understand what they are for, but figuring out when I need to use them is not so clear to me.  For now, I will
just let the compiler yell at me until I get a better intuition for when I need them.

### Closures seem difficult

While rust does have support for higher order functions (functions that accept other functions, or can return a 
function), they seem fairly complex.  This is mainly due to their being 3 different kinds of function traits:

| Trait  | Description                            | Pass type
|--------|----------------------------------------|---------------------
| Fn     | takes an immutable reference (&self)   | Pass by immutable reference
| FnMut  | takes a mutable reference (&mut self)  | Pass by mutable reference
| FnOnce | takes ownership of                     | Pass by copy

## The ugly

There's just a lot about rust I still don't know.  I haven't looked at Box, RC, ARc, or some other heap related data
structures.  There's also a ton to learn about the standard libraries.  I don't really know about the rust ecosystem
so I dont know what libs it has or doesn't have.

# What does rust have to do with Machine Learning, front end react apps or microservices ?

Since a lot of the work I do lately is around microservices and websockets, I thought it would be neat to collect a 
lot of the data.  Afterall, I'm mostly the one who works with a lot of test related data: TestResults, TestCases, log
output, testcase metadata, jenkins job information, ActiveMQ JMS messages related to builds, and even DBus events 
whenever subscription management data changes or react event information inour cockpit plugin.

This is some nice data for analysis dont you think?  Time to crunch on it.

Now, the most popular languages for ML seems to be python, R, and scala at the moment.  Frankly, I really scratch my
head why python is so popular for AI.  I think I know why.  Many statisticians and data scientists are really Math
majors at heart, and didn't really learn heavy duty programming languages.  So they like python (and R) for how easy
they are to learn.  But I have issues with any language that doesn't to this day have some kind of const or final.
And the speed?  More like the lack of.

So what about scala?  While scala is marginally interesting, it's like a shadow imitation of haskell.  I remember 
reading a post from Edward Kmett about how broken Scala's type system was, especially when it started to use
exotic types (GADT's, existential types, HKT's, shadow types, higher ranked N types, etc).  If I was going to use
a JVM language, I'd prefer to use eta (which is haskell for the JVM).  But, many libraries use Scala, including 
Spark.

But since this is a learning project, why not start at the basics?  One of the reasons python became popular for 
scientific and math work was numpy and scipy.  But those are actually C programs that compile to python modules.
I figured, why not do the same thing for javascript?

With webassembly and/or the neon project, it's possible to write native modules in rust that can run in a javascript
VM!  Think about that for a second.  Look how popular python got thanks to numpy and scipy.  Why hasn't anyone
done that for the javascript ecosystem?  Imagine what you could do with that...running near native speed code from
the browser.  Imagine if you could cluster together browsers for computing power (might have some security implications
of course) but think about it.

## Step 1.  Low level math libs

Just as python exploded for ML stuff thanks to numpy, there needs to be something like this for the web world too.  
I also think this would be really fun and interesting since I never really got to do any math related stuff despite
minoring in Math.  I got so stoked thinking about doing some Math that I even bought the full Linear Algebra, Calculus
Master series, and Intro to Probability and Stastics from Udemy :D

I also bought a book from Packt over Christmas called "Stastics for Machine Learning".  When I browsed through it, I 
liked that it explained some of the math going on, and the relationship between statistical modeling and a lot of ML
algorithms.  Msot of the book shows examples using numpy and R, so I figured I could translate those into rust.

I hope to kill two birds with one stone this way.  I'll get more proficient at Rust, while at the same time brushing up
on my long lost math skills.

## Step 2. ML Algorithms

The next step (actually some of this will be worked on concurrently with Step 1) will be to work on some of the ML
algorithms.  I've always been fascinated by neural nets, but linear and logistic regressions are also important.

## Step 3. rustrx

The next step will be creating a very simple rx implementation for rust.  The reactivex API is pretty huge, so I will
probably follow something smaller, like most.js instead.  Or maybe haskell's sodium library.  It definitely has to 
handle back-pressure though.

I need this because I want a way for objects to react to changes in real time.  This will be a necessary part for the
next step.

## Step 4: websockets in rust

It appears that there is a websocket implementation for rust.  Just like I did in the arctrix project, which is an rxjs
state management tool that is also a rxjs <-> websocket bridge, I will do the same for rust.  In fact, I may not need
to do this at all, depending on how webassembly FFI with javascript works.

## Step 5: real time data analytics

Most of the data I am shuttling around happens in real time.  We've got messages from the ActiveMQ bus, DBus events from 
the subscription-manager, log output from tests, and some others.  Why not crunch on that data as it comes in?