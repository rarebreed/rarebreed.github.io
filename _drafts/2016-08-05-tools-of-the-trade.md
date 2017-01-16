---
layout: post
title:  "Tools of the trade"
date:   2016-05-15 02:19:40 -0400
categories: functional cs
---
It takes more than knowing how to program to be a good engineer.  Here, I will describe some skills that I believe are
useful as a computer scientist.  Note that I said scientist, and not engineer.  My interests lie more in pushing the
boundaries than in building a product per se.  Knowledge is my own reward.

# Functional programming

This is a huge topic in and of itself, but here I will go over haskell style of functional programming

- Polymorphism via typeclasses (Functors, Applicatives, Monads)
- Category Theory (read Bartosz Milewskis articles)
- Recursion/Corecursion

## Data Structures

Try to implement these in purescript

Red Black Trees
Finger Trees
Tries
B-Trees
graphs

## Algorithms and analysis

searching
sorting
relations (graph theory)
regressions

### Machine learning

Classification
Prediction

# Concurrency and parallelism

Concurrency in general is being able to work on multiple entities simultaneously.  There are generic approaches, but
every language platform will have its own implementation.  For example, javascript has to use cluster when using node
or web workers if in the browser.  Java can use fork/join or parallel streams.  Clojure can use agents, refs or the
core.async lib.

## purescript Concurrency

Since purescript runs on a javascript engine, we have to use a combination of what the language provides and what the
javascript engine can handle

- continuation monad
- node cluster
- reactive
- *shared memory*
- *SIMD*

## java

Java 8's concurrency story has improved with java 8.  It has become more monadic

- CompleteableFuture
- Fork/Join pool
- reactive
- parallel streams

# Parsing and formal languages

One may wonder why you would ever need to learn how to parse some syntax.  Afterall, thats what regexes are for right?
But it is a valuable skill to learn.  If you ever need to be able to parse structured data that is not a standard
format like yaml or json, what do you do?

## Grammar types

- Explain what a context free grammar is
- Explain LL, LR, LL(R) and other parser types

## Monadic parser combinators

Explain what they are and write a parser

# Revision Control

Git is the defacto revision control system nowadays.  Get familiar with git concepts:

- branches
- commits and refs
- merging
- rebasing
- cherry picking

# Database modeling (Graphs)

Graphs are a ubiquitous data type and knowing how to use them and where they are useful is vital.  Learn both a
graph database like arangodb, tinkerpop or orientdb along with graph theory in general

# Requirements gathering

# OS Platform

This is a huge subject in its own right.  Getting better at some linux things cant Heuristics

- selinux
- RHCSA

# Virtualization Platform

The hot topic now is docker.  Openshift is another platform to watch.  Less appealing to me are IaaS systems like
Openstack.  Concetrate on PaaS or helper technologies like kubernetes and mesos.

- Read the containerization manual to get familiar with docker

# Language Ecosystem

It's not enough to learn how to program in the syntax and grammar of a language.  You also need to learn its build Tools
libraries, and other idiosyncracies.

## Java

- Gradle is the build tool of choice nowadays.  
- JMS is a good skill to learn
- Vertx
- RxJava
- slf4j
- maven central

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

## Purescript/Javascript

Lots of work here.  Bower and pulp are the defacto build tools.  Need to learn more javascript libraries, and how to
debug javascript apps.

- Bower for dependency management
- pulp for the build tooling
- purescript-rx the reactive bindings
- purescript-aff for asynchronous programming
- purescript-node* all the node related libs, especially stream, buffer, process and child-process
- purescript-halogen for front end web page

### bower

Bower is used instead of npm for package management.  

### pulp

Is the main build tool for purescript, and calls the psc compiler to generate the javascript code

### purescript-rx

The reactive bindings for purescript.  I think that the reactive style will probably be the way to go for a lot of the
purescript functionality.  Monad transformers are hard to write and even if reactive is too, it has some interesting
side benefits too (like being able to announce to interested parties about an event) that the monad transformers dont

### purescript-aff

An alternative for handling async programming.  I'm having a hard time getting makeAff to fit the types of certain async
functions.  For example, how do you get makeAff to play nice with spawn?  But, if I can get this to work, I will use it
as it seems easier than monad transformers.

### All the purescript-node* modules

Since I'll be doing a lot of client side programming for work, I need to get familiar with both node, and how purescript
calls the foreign node functions.

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

HTTP2 is mostly a "on the wire" protocol (ie, a transport protocol) but the HTTP1.1 protocol still applies at the
application layer.  Knowing the status codes and how things work are useful

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
