---
layout: post
title:  "Tools of the trade"
date:   2016-05-15 02:19:40 -0400
categories: functional cs
---
It takes more than knowing how to program to be a good engineer.  Here, I will describe some skills that are needed.

# Functional programming

This is a huge topic in and of itself, but here I will go over haskell style of functional programming

- Algebras (Functors, Applicatives, Monads)
- Type Theory (read Bartosz Milewskis articles)
- Recursion/Corecursion

## Data Structures

I want to b able to design my own persistent data structures including the following:

- Red Black Trees
- Finger Trees
- Tries
- B-Trees
- graphs

## Algorithms and analysis

These are the basic fundamentals of computer science.  Knowledge in this area gives you an understanding into the run
space time efficiencies of data structures and the algorithms that work on them.  For the above data structures, one
should be able to do the following:

- traversal (going through all elements one by one)
  - mapping (applying a function to all elements)
  - folding
- searching (does an item exist in this structure)
- sorting (order elements on some criteria)
- adding new items
- deleting items
- balancing (preserving runtime efficiency)
- comparing (are 2 structures equivalent)

# Concurrency and parallelism

Concurrency in general is being able to work on multiple entities simultaneously.  There are generic approaches, but
every language platform will have its own implementation.  For example, javascript has to use cluster when using node
or web workers if in the browser.  Java can use fork/join or parallel streams.  Clojure can use agents, refs or the
core.async lib.

## purescript Concurrency and Parallelism

Since purescript runs on the javascript vm, which is single threaded, there are a few techniques to give the illusion
of concurrency, but only 1 way to do true parallelism (depending on whether you are using node or in the browser). In
the future, there will (hopefully) be a 2nd way of doing parallelism when js shared memory is implemented

- continuation monad
- purescript-aff for asynchronous or delayed computations
- node cluster

## java

Java 8's concurrency story has improved with java 8.  It has become more monadic

- CompleteableFuture
- Fork/Join pool
- Event and signal handlers
- parallel streams
- JavaRx (reactive for java)

## javascript (ES6)

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
- NetworkManager

# Virtualization Platform

The hot topic now is docker.  Openshift is another platform to watch.  Less appealing to me are IaaS systems like
Openstack.  Concentrate on PaaS or helper technologies like kubernetes and mesos.

## Docker

Docker is an interesting container platform.  Rather than run a process on full blown VM, docker runs a container in an
isolated process running on a "virtual" OS.  So where a VM is a virtual machine running an OS, a container is a virtual
OS running an isolated process.

There are many parts to learning docker:

- dockerfile
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

- gradle for buids
- JMS for messaging
- vertx for building reactive services
- JavaRX for building Observables and observers
- Jackson for (de)serializing JSON messages
- class loading
- reflection
- profiling jmeter,jmx

## Purescript/Javascript
Lots of work here.  Bower and pulp are the defacto build tools.  Need to learn more javascript libraries, and how to
debug javascript apps.

- purescript-aff for asynchronous effects
- purescript-react for web front end state modelling
- purescript-node-* for node bindings
- es6 for FFI

## Haskell

Haskell is kind of in the same boat as purescript.  Stack is their build tool and i need to get familiar with the
standard libraries and hoogle.

# Networking fundamentals

It never hurts to understand network technologies.  Become familiar with TCP/IP handshake.  Learn tcpdump/wireshark.

## TLS and certs

You cant avoid learning this.  It's complicated because different tools do it differently.  Java has keystores and
trust managers.  Others use CA certs in different formats

## HTTP 1.1 and HTTP 2 protocols

HTTP2 is mostly a "on the wire" protocol (ie, a transport protocol) but the HTTP1.1 protocol still applies at the
application layer.  Knowing the status codes and how things work are useful

# Machine learning

This is a huge and fascinating field.  Its all the more fun because of the math.  A good practice to kill 2 birds with
one stone would be to convert the Python Machine Learning book into purescript

## Natural language processing

## Linear and logistic regressions

## Bayesian probability

## Neural nets

## K-means

## Vector machines
