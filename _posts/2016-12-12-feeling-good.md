---
layout: post
title: "Finally doing real programming in purescript!"
date: 2016-12-12 00:51:40 -0700
categories: purescript
---
So I've finally started writing some non-trivial code in purescript in my emissary project.  Right now, it's pretty
simple stuff, mostly calling out to create some child processes and a super simple webserver.  But what has me excited
is that I'm finally doing real-world stuff.  Slowly but surely, I'm starting to get a handle on monads and the effects
system. However, there are still three things that I need to get better to become more fluent in purescript.

- Monad Transformers
- asynchronous programming (either with aff or maybe rx)
- lenses

For the actual services, I'll also need to dig into the following modules:

- purescript-eff in particular, why my extensible effect types aren't doing what I think they should be
- purescript-websocket-simple to create the websocket upgrade connection
- purescript-node-* all the node bindings, because I need to do stuff like write to files, delete directories, etc

It's a good feeling that I have finally got some working code, and I have at least a working understanding of monads,
and how and when to lift code.  I do think think that there's not a lot of good learning material on purescript, and I
might wind up writing some "cookbook" style recipes for purescript.  As hard of a sell as clojure is to people, I think
that purescript will be an even harder sell if only due to the learning curve.

As I learn purescript, I'd also like to talk about some other pain points I've been having.  For example, why I have to
get better at the 3 things above.

# Rank-n types

So, I've been having a lot of problems with Eff and extensible effects.  Take for example this code:

```haskell
-- ObsFn is the type of a function which takes a type that is Showable, has some kind of effect, and returns Unit
type ObsFn a e :: forall a e. Show a => a -> Eff e Unit

testing :: forall a. ObsFn a (console :: CONSOLE)
testing a = do
  log $ show a
```

This function won't work though given the testing function.  You will see an error that says:

```
"Could not match Kind
  Type
with Kind
  # Effect
while checking that the Kind of ObsFn a (console :: CONSOLE)
in value declarion testing"
```

hmmmm.  What does that even mean?

First off, what does **ObsFn a (console :: CONSOLE)** even mean?  What was I trying to _say_?  If you look at the
original definition of ObsFn, look at what the e parameter is.  It's in _Eff e Unit_.  If you look at the type for Eff,
it has the following type:

```haskell
data Eff :: # Effect -> Type -> Type
```

Ok, given that, we can see that _e_ must be a type of Type.  So what's a Type?  A Type is a synonym for *, the same
symbol you see when talking about Kinds.  Ok, so how does this help us?  The problem here is figuring out which argument
was bad.  Was it the _a_ or the _(console :: CONSOLE)_ part?  Since the error said it couldn't match # Effect, that is a
hint the problem was the latter.

# FFI

I realized that the purescript-rx module was designed with the older version 4 for reactive.  For example, it uses the
just function which no longer exists, and they use fromArray (which is deprecated in version 5).  After looking at the
code, I thought I could simplify how they created Observables.  I was wrong.

As noted from the Rank N Types comment above, I am learning that I have a lot to learn about Types.  Starting to write
an FFI module is forcing me to learn more about not just Rank N Types or the runtime representation of purescript code,
but just about types in general.  Since FFI requires foreign data types to be written as Kinds, I'm getting a better
handle on Kinds also.

I'm still having some problems with

# Extensible Effects

I'm still having some problems understanding extensible effects.  I can use them just fine, but if I want to create my
own function where the effect is parameterized, I'm having great difficulty.  Take for example the type alias of ObsFn
that was declared earlier.  This type is passing in the effect as a type parameter.  But why, in the forall declaration,
is the effect itself not required?

A more simple example is if I want to create a function that can work with any Effect type.

```haskell
type FooFn :: forall a r eff. a -> Eff eff r
```

So, the trick it seems, is to do it like this:

```haskell
type FooFn eff
```

# Monad Transformers

I came across this need while trying to solve a particular problem.  The problem is that I wanted to provide a list of
environment variables as strings, passing each one to the lookupEnv function from Process.  I could then zip the list of
Strings (which are the keys) and the resultant list of Eff (process :: PROCESS | eff) (Maybe String) to a Map.  The
problem is that I started with a monad of one type (List String) and wanted to return a different monad of type
Map String (Eff (process :: PROCESS) (Maybe String)).

If you look at the type of bind, this isn't possible:

```haskell
-- The m type is fixed
bind :: forall m a b. (Monad m) => m a -> (a -> m b) -> m b
```

Recall that the _m_ here is fixed.  If I start with Eff (process :: Process | eff) (Maybe String) as my _m_, then I have
to return that type as well.  In other words, although the inner pure value can change from type _a_ to type _b_, the
type of the monad itself needs to change.  In other words, I need something like this:

```haskell
-- We have two different monadic types m1 and m2
transform :: forall m1 m2 a b. (Monad m1, Monad m2) => m1 a -> (a -> m2 b) -> m2 b
```

Ideally though, this new form should work with the do syntax to make things easier.  My understanding so far is that
this is what monad transformers provide.  This is slightly different than what extensible effects provide.

# asynchronous and deferred programming

So one cool thing about the do syntax is that it already eliminates ugly nesting callbacks.

# Cookbook

So far, I have not found any simple guides on how to do things like read or write from a file.  I don't consider
purescript to be a beginner's language by any stretch of the imagination, but the learning curve is steep enough as it
is.  Having some "how-to's" for common programming scenarios is important

# Allowable syntax

Some things for syntax just aren't explained very well in the book, and I only discovered them by looking at source code
that others have written.  One example is that in a do block, you can sequentially do pretty much any function and also
in a do block, you don't have to have the _in_ part after a _let_ block.

# Compiler error messages

I have a really hard time deciphering the error messages even with the help of the wiki.  So I'll try to post some
examples of how to decode what the error messages mean.
