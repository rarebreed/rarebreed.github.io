---
layout: post
title:  "Notes on monads"
date:   2016-05-30 00:55:40 -0400
categories: purescript notes monads
---
Here's the infamous monad section.  Might as well take some notes right?

# Overview

- Monads are an abstraction (really a typeclass) for side effects
- Monads have a relationship with *do* notation.  
- There is a difference between computational side effects, and native side effects

## Relationship with do notation

Let's examine the code example given in the book:

{% highlight haskell %}
import Prelude
import Data.Array

countThrows :: Int -> Array (Array Int)
countThrows n = do
  x <- 1 .. 6
  y <- 1 .. 6
  if x + y == n
    then return [x, y]
    else empty

-- The above is equivalent to this:
countThrows' :: Int -> Array (Array Int)
countThrows' n =
  (1 .. 6) >>= \x ->
  (1 .. 6) >>= (\y -> if x + y == n
                          then return [x,y]
                          else [])

-- Which is also equivalent to this
countThrows'' :: Int -> Array (Array Int)
countThrows'' n =

{% endhighlight %}
