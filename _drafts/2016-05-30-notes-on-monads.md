---
layout: post
title:  "Notes on monads"
date:   2016-12-18 00:55:40 -0400
categories: purescript notes algebras
---
Some quick note taking on the various algebras and typeclasses

# What is an algebra?

# Relationship between algebras and typeclasses

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
