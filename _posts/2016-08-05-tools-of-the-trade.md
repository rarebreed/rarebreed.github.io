---
layout: post
title:  "Tools of the trade (UPDATED: 03-13-2018)"
date:   2016-05-15 02:19:40 -0400
categories: functional cs
---
It takes more than knowing how to program to be a good engineer.  Here, I will describe some skills that I believe are
useful as a computer scientist.  My personal goals are to focus on the following languages and stacks:

- Eta for JVM programming
- Typescript for ecmascript (possibly purescript if they ever get to 1.0 release)
- Rust for webassembly, fast ecmascript modules (think numpy for node/browser), and IoT

I'm going to avoid python where I can.  I just don't see the point when ecmascript is faster, you have to use ecmascript
for the browser anyways, and python still doesn't have a statically-typed-compile to python language (and no, PEP484 does
not count). Java I will have to do because of work. But, I would like to write new code in eta.  I'm already writing 
lots of typescript now.  Rust is something I am still learning, but it seems really interesting so far.

I think by learning these 3 languages, I have all the bases covered (from scripting, to systems programming, to backend
enterprie programming).

For the concurrency front, FRP and reactive seems to be the way to go.  Not only does it handle concurrency well, it
also handles asynchronous execution well.  Unfortunately there is no reactivex/FRP library for rust, so I might just
make my own as a learning project.  Haskell's sodium library is also a bit different.  I might wind up either 
writing a library in eta, or porting rxjava.

# Functional programming and Category Theory

This is a huge topic in and of itself, but here I will go over mostly haskell style of functional programming.

- Polymorphism via typeclasses (Functors, Applicatives, Monads)
- Category Theory (read Bartosz Milewskis articles)
- Recursion/Corecursion
- Monad Transformers
- Combinators (lambda calculus)
- Arrows (relaxed monads)
- Higher Kinded Types
- Higher Ranked Functions
- Modules as types (reasonml)
- GADTs
- Existential Types
- Dependent Types

## Data Structures

I want to be able to design my own persistent data structures including the following:

- Red Black Trees
- Finger Trees
- Tries
- B-Trees
- graphs

## Algorithms and analysis

These are the basic fundamentals of computer science.  Knowledge in this area gives you an understanding into the run
space time efficiencies of data structures and the algorithms that work on them.  For the above data structures, one
should be able to do the following:

- folding
- traversal (going through all elements of a data structure one by one)
- mapping (applying a function to all elements)
- searching (does an item exist in this structure)
- sorting (order elements on some criteria)
- adding new items
- deleting items
- balancing (preserving runtime efficiency)
- comparing (are 2 structures equivalent)
- commonality
- best fit

# Concurrency and parallelism

Concurrency in general is being able to work on multiple entities simultaneously.  There are generic approaches, but
every language platform will have its own implementation.  For example, javascript has to use cluster when using node
or web workers if in the browser.  Java can use fork/join or parallel streams.  Clojure can use agents, refs or the
core.async lib.

## Haskell and eta

- continuation monad
- MVars
- Build reactivex for eta

## Rust

- reactivex for rust
- Arc (atomc ref count)
- *shared memory* for rust/webassembly/ecmascript
- *SIMD*

## javascript

- rxjs
- *shared memory

## Java

Java 8's concurrency story has improved with java 8.  It has become more monadic

- Java 9 Flow
- RxJava (reactive for java)

## Javascript (ES6)

Javascript mostly runs in a single thread, with the exception of web workers and node's cluster.  Since js is normally
single threaded most of the time you need to worry about concurrency rather than true parallelism (SIMD is another
excetion to the rule where one operation could be run on multiple instruction units simultaneously).

The defacto concurrency solution is to use callbacks (ie, continuation passing style) but then you run into callback
hell.  So ES6 introduced 2 features to help with this: promises and generators (coroutines).  Promises allow you to
defer the result of a computation, and coroutines allow a function to be re-entered.

# Parsing and formal languages

One may wonder why you would ever need to learn how to parse some syntax.  Afterall, thats what regexes are for right?
But it is a valuable skill to learn.  If you ever need to be able to parse structured data that is not a standard
format like yaml or json, what do you do?

For example, I would like to create a modified gherkin syntax parser such that I can extract desired elements from it.

## Grammar types

- Explain what a context free grammar is
- Explain LL, LR, LL(R) and other parser types

## Monadic parser combinators

Explain what they are and write a parser.

For work related purposes, I want to build a parser for a slightly modified gherkin style format.

# Revision Control

Git is the defacto revision control system nowadays.  Get familiar with git concepts:

- branches
- commits and refs
- merging
- rebasing
- cherry picking

# Database modeling (Graphs)

Graphs are a ubiquitous data type and knowing how to use them and where they are useful is vital.  For this, I will
learb the orientdb graph database using tinkerpop along with graph theory in general.

# Requirements gathering

An oft neglected skill, but gathering all the software _and_ user Requirements is necessary for quality software.  A new
Behavioral Driven Development style helps address these concerns.  For my purposes, Getting familiar with cucumber.io
will be useful.

# OS Platform

This is a huge subject in its own right.  Getting better at some linux things cant hurt

- selinux
- RHCSA

# Virtualization Platform

The hot topic now is docker.  Openshift is another platform to watch.  Less appealing to me are IaaS systems like
Openstack.  Concentrate on PaaS or helper technologies like kubernetes and mesos.

## Docker

Docker is an interesting container platform.  Rather than run a process on full blown VM, docker runs a container in an
isolated process running on a "virtual" OS.  So where a VM is a virtual machine running an OS, a container is a virtual
OS running an isolated process.

There are many parts to learning docker:

- dockerfile
- ansible-container
- docker-compose
- docker-engine
- volumes
- docker networks

# Microservices as Middleware

What is middleware?  They are tools used by your main application to get jobs done.  A typical example of middleware is
a message bus that shuttles messages between separate programs.  Microservices is a new take on Service Oriented
Architecture using something akin to an Actor model.

Microservice is as much an architectural style as well as a way of building a single service.  For this purpose, boning
up on vertx is a good idea.  It has many low-level and higher level libraries for building reactive style microservices.

# Language Ecosystem

It's not enough to learn how to program in the syntax and grammar of a language.  You also need to learn its build Tools
libraries, and other idiosyncracies.

## Java

- Gradle is the build tool of choice nowadays
- JMS is a good skill to learn for messaging
  - kafka another good alternative
- Vertx for reactive microservices
- RxJava for reactive style state handling
- slf4j because logging is confusing but important
- maven central because you need to put your artifacts somewhere
- class loading
- reflection

### Gradle

Most modern java projects are being built from gradle now.  Gradle is a rather complicated beast that really requires
some knowledge of the groovy programming to effectively get used to.

### JMS

Is the Java Message Service SPI.  If you need a message bus, and you're in java, chances are you'll be using a message
bus framework that speaks JMS (like ActiveMQ or Camel).  Knowing what topics, channels, subscribers, and the various
message types will be very helpful.

### Vertx

Vertx is a microservice framework built on a reactive style model with an event loop.  Since it is a set of libraries
ideal for creating microservices, it allows for building middleware stacks, like message buses, web handlers, or db
handlers.

### RxJava

Is the reactive library for java programming.  Related to vertx, it can be used in tandem with vertx to create an event
driven style of async programming which, though it requires some extra plumbing, looks easier to read than nested call
back hell.

# Web ecosystem

The GUI is the browser.  Learning how to build front end web apps is never a bad thing.  There is a ton to learn

- webpack: for building and asset management
- react or cyclejs: for view layer framework
- mobx or rxjs: for state management
- bulma or bootstrap: for css framework
- typescript: dont use es6
- ramda: functional goodies for javascript
- static-land: functional types for typescript
- immutable:  immutable data structures
- graphql: because REST is too bloated
- graphdb (orientdb: for backend storage

Then there's the Web APIs

- WebRTC: for video cam
- WebGL: for 3d graphics
- Websockets: for bidirectional async communication
- DOM: got to know how the DOM works for react and CSS

# Networking fundamentals

It never hurts to understand network technologies.

- Become familiar with TCP/IP handshake
- Learn tcpdump/wireshark

## TLS and certs

You cant avoid learning this.  It's complicated because different tools do it differently.  Java has keystores and
trust managers.  Others use CA certs in different formats

- Learn about java keystores
- Learn about java keytool
- Learn about the different CA cert formats (PKS8, etc)
- Learn the node TLS module
- Read the Java PKI guide
- Read the Java JSSE reference

## HTTP 1.1 and HTTP 2 protocols

HTTP2 mostly changes how things work "on the wire" but the HTTP1.1 protocol still applies at the
application layer.  

- read the websocket protocol
- read the websocket API
- read the http protocol

# Machine learning and Data Science

This is a huge and fascinating field.  Its all the more fun because of the math.  A good practice to kill 2 birds with
one stone would be to convert the Python Machine Learning book into purescript

## Linear Algebra

Many of the machine learning algorithms require at least a basic understanding of linear algebra.  The deeper you can go
the better (ie, eigen vectors, spanning)

## Natural language processing

This is maybe the holy grail of AI.  Being able to process unstructured data would be incredibly valuable.  And to be
able to crunch on data, you need to get data.  And sometimes, the data is only available in unstructured format.

## Linear and logistic regressions

Linear regressions are a way to find a line through a scatter plot, such that the average distances of the points to the
line is minimized.

Logistic regressions are similar but instead of a straight line, you get an s-shaped curve.

## Bayesian probability

Uses bayes theorem to determine probability of an event given certain data.

## Neural nets

## K-means

## Vector machines

# Projects to work on
