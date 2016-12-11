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
-

## Data Structures

Red Black Trees
Finger Trees
Tries
B-Trees
graphs

## Algorithms and analysis

binary search
sorting
graph theory

# Concurrency and parallelism

Concurrency in general is being able to work on multiple entities simultaneously.  There are generic approaches, but
every language platform will have its own implementation.  For example, javascript has to use cluster when using node
or web workers if in the browser.  Java can use fork/join or parallel streams.  Clojure can use agents, refs or the
core.async lib.

## purescript Concurrency

Since purescript runs

- continuation monad
- node cluster

## java

Java 8's concurrency story has improved with java 8.  It has become more monadic

- CompleteableFuture
- Fork/Join pool
- Event and signal handlers
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

# Virtualization Platform

The hot topic now is docker.  Openshift is another platform to watch.  Less appealing to me are IaaS systems like
Openstack.  Concetrate on PaaS or helper technologies like kubernetes and mesos.

# Middleware

JBoss and Wildfly are kings in the java ecosystem.  But what exactly is middleware?  Middleware are technology
components that work across technology stacks and platforms.  For example bus messaging systems

# Language Ecosystem

It's not enough to learn how to program in the syntax and grammar of a language.  You also need to learn its build Tools
libraries, and other idiosyncracies.

## Java

Gradle is the build tool of choice nowadays.  JMS is a good skill to learn

## Purescript/Javascript

Lots of work here.  Bower and pulp are the defacto build tools.  Need to learn more javascript libraries, and how to
debug javascript apps.

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

# Projects to work on

my order of preference

-

-
