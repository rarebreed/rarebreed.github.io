---
layout: post
title:  "My journey learning purescript: the good, the bad, and the ugly"
date:   2016-12-20 00:55:40 -0400
categories: purescript notes
---
Over the past few months I have slowly been picking up purescript.  It's come at a glacial pace, mostly because until
recently, I have not actually done any learning or practice during my work time.  Recently that has begun to change as
one of my other main projects has cooled off a bit.  So what I would like to talk about here is my personal experience
so far in learning a pure functional language.

# The journey of a thousand miles...

Before I get into the good, the bad, and the ugly parts of learning purescript, I will say right off the bat that this
is the most challenging language I have learned since my first programming language (C++).  It truly feels in some ways
like learning how to program all over again.  I thought that since I was fairly adept at clojure, my experience in a
mostly functional language would make learning a pure functional language like purescript easier.

I was wrong.  But that is not to say that learning clojure and its functional paradigm didn't help.  The concepts of
higher order functions, immutable data structures, folding, recursion and other functional paradigms have definitely
helped.  But learning purescript is challenging me in two fundamental regards:

- A static and algebraic type system
- Purity (and what that really means)

## The type system

The majority of the programming I have done in my career has been in dynamic languages: perhaps about 70% in dynamic
languages like python and clojure, and about 30% in a more strongly and statically typed language like java or C(++).

# My steps to learning purescript

Learning purescript has been interesting mainly due to the lack of material to help me learn.  There is currently one
book made by the author, but it gives a whirlwind tour or explanation of the syntax.  In effect, it presumes one is
already familiar with the haskell syntax.  Furthermore, the terseness of the book pretty much demands that the reader
is familiar with pure functional style programming in a haskell style.  This makes sense due to purescript's tight
closeness to haskell.

But, this has led to some weirdness.  For one, I realized that I basically had to learn at least some of haskell in
order to learn and use purescript.  The book Learn You a Haskell for Greater Good is an excellent starting point for
learning about haskell.  It does a much better job of explaining the syntax as well as the overall concepts of many
things that purescript have in common (for example Algebraic data types, Functors, Applicatives and Monads).  It also
does a much better job at giving some examples of things like folding.

But, there are places where the purescript and haskell syntax diverge, and these can trip you up.

## Learning node

Another big difference is of course the compiler and targeted environment.  Purescript is targeted for a javascript
environment.  This means that the purescript compiled code will run either on a browser or on node.  For my use case
currently, I'm doing client side programming,

## The many ways to combine functions

```haskell

addStuff :: Int -> Int
addStuff = do
    a <- (*2)
    b <- (+10)
    return (a+b)

-- Is equivalent to the above
-- Note how
addStuff' = (_ * 2) >>= \a -> (_ + 10) >>= \b -> pure (a + b)
--          m a     >>= (a -> m b)     >>= (a -> m c)
--                      m b            >>= (a -> m c)
-- From this, we see that (_ * 2) is a monad.  Ergo, functions are monads
-- But what is the a in (m a)?  For example Just 10 the m is Maybe, and the a is 10
-- In this case, the a is the function itself.  Eg we wind up with addStuff' x = (x * 2) + (x + 10)

-- And this is the Applicative way
addStuff'' = (+) <$> (_ * 2) <*> (_ + 10)
-- If you do the fmap first, you get ((_ * 2) + _) , then we apply that, we get ((_ * 2) + (_ + 10))
-- And this is equivalent
```
