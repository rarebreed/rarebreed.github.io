---
layout: post
title:  "Notes on monads"
date:   2016-12-18 00:55:40 -0400
categories: purescript notes algebras
---
# more stuff to learn

RWS Monads
Monad Transformers (composing monads)
Continuation Monads
Reactive (purescript-rx)
applicative combinators
comonads

Some quick note taking on the various algebras and typeclasses

# Quick thoughts

- Monoids abstract the idea of folding (or catamorphisms)
  - mappend (<>) is a binary operation of a -> a -> a
  - Must have concept of an identity value for mappend such that a <> id = a
  - Must be associative
  - mappend might change the "shape" of the structure
    - eg [1,2] <> [3, 4] = [1, 2, 3, 4] but not (Sum 1) <> (Sum 2) = (Sum 3)
- Functors abstract the idea of mapping (applying a function over or to a data type)
  - When the mapping of a Functor is applied, it does not change the "shape" of the data structure
- Applicatives are a combination of monoids and Functors
- Monads generalize on the idea of flattening or concatenation (eg concatmap)

When I say "Functors are ..." or "Applicatives suport ...", when I refer to Functor, I am basically talking about what
a Functor can do.  Remember that a Functor is a typeclass.  Many types are instances of Functor, Applicative, or Monad.
If some data type is_a Functor, then that means that it supports the same operations all Functors do, and can behave
as all others do, because all Functors have to support the Functor laws.

This is not different really from the concept of interfaces and classes in Java.  In Java, an interface is similar to a
typeclass in purescript.  It represents a set of operations that a class can implement.  Where in Java you would have
a class implement an interface, in purescript you make a data type an instance of a typeclass.

# Some tips and notes

Some quick notes I wanted to jot down before I forget

## If you see a do block, you know it has to return a monad

An example of this that I ran into was while writing my code for launching child processes.  In my test code, I was
launching 2 child processes.  Not only was the 2nd process launching before the first completed, even passing the st ref
from the run function didnt have the saved data.

It _seemed_ like the process was running synchronously, because control in the repl wouldnt return until the process was
done.  Basically, the run function took a mutable Ref String which saved the output of the childprocess.  I also created
an onDataSave function which used this mutable Ref and I partially applied it to give a function useable by the onData
function.  The run function would then return this mutable ref.

<!-- The problem is that in my testing function, I would read from this ref a little after the run function was called.  But -->
because run returns almost immediately, there's nothing in the mutable ref yet.  

So, how do you make it so that if I have a dependency chain of child processes A -> B -> C, where B can't run before
A completes, and C can't run before B completes in purescript?

In node, I would either use callbacks, or perhaps a promise.  Purescript's equivalent to a callback is the Aff type and
all its helper functions.  I am currently investigating them now.
