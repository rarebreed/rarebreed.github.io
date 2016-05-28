---
layout: post
title:  "My first node app"
date:   2016-05-20 07:21:40 -0400
categories: purescript notes testing
---
# Things to do in purescript

## Learning

Here's a laundry list of stuff I still need to learn

- Get better at functors, applicatives, Monads
- Read up on Eff and Aff
- Do problems from the purescript book
- Do problems from the Haskell book
- Do Haskell 99 problems

I'm starting to get the hang of functors and applicatives, and I can of see now that a functor is what "wraps" a side
effecting computation.  For example, Maybe handles the possibility of a non-existant value, Either is used for functions
which might fail (math functions don't fail...even dividing by zero is not a failure, it just means you haven't properly
restricted your domain), and even lists have to handle the possibility that they are Nil or empty.  *Lifting* in
haskell/ps is taking a function that takes normal values and being able to call it with values wrapped in a functor.

Another way to look at functors and applicatives is as a way to glue together functions.  For example, suppose you want
to compose functions f and g.  For this to be possible, g must return a type(s) compatible for f's parameter(s).  But
what if g returns a value wrapped in a Functor (for example an Either) but f only takes a "regular" value?  This is
where lifting comes in handy.  The fact that Applicative typeclass instances must implement pure should give a hint that
Applicatives must somehow be concerned with interfacing pure code with impure code.

I'm going to explore functors and applicatives more in a separate post.  I also need to understand Monads relationship
to Functors and Applicatives (recall that a Monad is an Applicative, and an Applicative is a Functor).

## Work Related

Here's another list of real-world project I can work on with purescript.  I'll go into more detail for each project in
its own section.

- Write an example of FFI with node
  - Need to call into a dbus service using FFI interop with javascript library
- Look at the websocket module
  - Write a microservice (using purescript-websocket-simple)
  - Take existing protocol with pheidippides and simplify it
- Look at writing a REST client
- Investigate a test framework for purescript
  - See if we can get it to interop with cucumber
- Optional: Write a YAML parser library for purescript
- Optional: Write a ConfigParser library for purescript

## Fun Project

This is more fun stuff that I want to work on if/when I ever have time

- Write some WebGL libraries
  - Probably need to write some math libraries
- Rewrite lancer in purescript

# Test Framework for purescript

If I want to have any chance of using purescript at work, it needs to have a decent test framework.  This framework
(and purescript itself) needs to satisfy a couple of requirements:

- A way to attach metadata to a method
- A way to get this metadata at runtime
- Generate xUnit style report
  - Also needs to be able to insert polarion specific information
  - Optional: Generate
- Optional: Interface with cucumber for javascript
- Optional: Generate an html report

So far I haven't seen a (clean) way in purescript to attach metadata about a function (or even a type or typeclass).  In
fact, there isn't even a docstring mechanism.  There is a feature that will parse the code and recognize that a comment
directly preceded a function.  It might be possible to hook into the doc/comment parser and extract information that way
but a messier but certainly plausible alternative is to simply declare maps in the module.  The key would be the
function and the value would a second level map containing any metadata we needed.

Generating a xUnit style report might be more difficult.  The only native test framework I have seen for purescript does
not seem to do this.  I think a more practical approach will be to interface with the cucumber port for javascript and
use FFI to utilize its features (which includes the ability to write a custom formatter for reports).

If I write a custom report generator, I can also make it so that we can pick up the metadata about the tests and inject
it into the custom report.

# Calling dbus via purescript code

One of the next goals for work related functionality is to be able to make requests of a dbus enabled service.  I've
looked at a node library written in pure javascript for node which seems usable. The next steps will be to:

- Learn how to do FFI with a native javascript library
- Create a library to call into a dbus service
  - Make it generic at first, but this will be used to make requests into subscription-manager

# Create a microservice using websockets to replace SSH

Using SSH for RPC is relatively straightforward, but it also has drawbacks.  The biggest drawback being that it is
similar to http REST interface in that it's a pull system only:  the server can not notify a client of asynchronous
events.  The pheidippides was going to be a clojure based solution for this, however, it ran into a few problems

1.  Refactoring was a pain (and it was constant)
2.  Spinning up a JVM is very expensive (approx 500GB)
3.  Clojure boot up times are unacceptable (7+ seconds)

The idea here will be to have a node process per microservice communicating to a central daemon.  The first microservice
will be to interact with a dbus enabled service.  The second microservice will be a file system service that can among
other things send/copy files, or watch a folder for file changes. A third service will be to make REST calls to a
candlepin server.

A client will connect to the Controller daemon, and the Controller will see if a service that can implement the request
is running.  If not, the Controller will spawn a node process of the microservice that implements the request.  The
Controller will route the request to the microservice and that service will return a response message (or sequence of
messages).  

The microservice should be secure, so the websocket should be protected by TLS.

# Write a YAML and ConfigParser for purescript

ConfigParser style (or config if you prefer) key-value style property files are very common, especially in the python
and linux world.  Being able to parse these files will come in handy.  Ditto for a YAML parser.

This would be an interesting exercise as it would require learning how to generate a real parser (not just a bunch of
regexes) like a recursive descent parser.  YAML should have a real EBNF style grammar, though I don't believe there is
an official one for ConfigParser style files.  However, one could be generated that handles about 98% of the cases.

Unfortunately, there's no parsec for purescript, but it would be interesting to hand roll a recursive descent parser for
this anyhow.
