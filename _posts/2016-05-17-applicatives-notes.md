---
layout: post
title:  "My first node app"
date:   2016-05-15 02:19:40 -0400
categories: purescript notes
---
**This post is a Work in progess**

I think I've finally wrapped my head around functors in haskell/ps.  I kind of had two a-ha moments.  Usually when you
see tutorials about functors online, you'll hear them say that Functor is like a box that holds some value.  Problem is
that doesn't hold up that well when you think about 2 major applications for Functors:  IO and functions.  Moreover,
most tutorials I have seen really tell you __why__ Functors are needed or useful.  This is all the more puzzling,
because the infamous monad is just a special kind of Functor.  So what is a Functor, and why do we need them?

# The context of a functor

When you see it explained that functors are a "box" that holds some value, that analogy really only holds in some kinds
of functors.  For example, what is the "box" when you consider that a function type is a Functor?  What is the box when
you consider IO as a functor (is it the outside world)?  The better analogy is that a functor has or needs a context.

For example (+3) returns a value, depending on the parameter it is given.  The functor (Just "foo") may return a new
Just or Nothing, depending on the value.  In regular vernacular, we often say "the meaning depends on the context".  
Let's break that down.  The meaning of something can change depending on some other factors.  You have to consider the
other factors (the context) before you understand a value's actual meaning.

Recall the defintion of a fmap:

{% highlight haskell %}
fmap :: (Functor f) => forall a b. (a -> b) -> f a -> f b
{% endhighlight %}

This says that fmap takes a function that takes some type a and retunrs a type of b (note that a and b can be of the
same type), and another argument which is a functor of type a, and it returns a functor of type b.  An alternative way
of seeing this function that might be more useful is to look at it like this:

{% highlight haskell %}
fmap :: (Functor f) => forall a b. (a -> b) -> (f a -> f b)
{% endhighlight %}

This says that fmap takes a regular function (not wrapped in a Functor) that takes a type of regular a and returns a
regular b, and returns a function which takes a Functor of a and returns a Functor of b.

But why is that useful?  Before we answer that, lets see some instances of functors where its a stretch to think of them
as boxes.

## Functions as types

First, let me start off by saying I hadn't really thought of functions as types, but it makes perfect sense.  How else
would you be able to treat functions as first class citizens unless they had a type?  The **->** operator is actually
a type constructor for a function.

{% highlight haskell %}
getKey :: k -> Map k v -> Maybe v
getKey k (Map key )
{% endhighlight %}


## Applicatives

I think the major part with applicatives is that if a function is wrapped inside a Functor, we cant just use that
"wrapped inside a functor" function with fmap.  That's where Applicatives come into play

{% highlight haskell %}
(+) <$> [10,20] <*> [3,4]
(replicate) <$> [2,4] <*> ["a","b"]
{% endhighlight %}

# Defining an instance of an Applicative


# What's the point of functors and applicatives?

So why go through all this?  Functors are useful if you have a regular function that takes non-Functor values, and
you need to apply the function to arguments that are wrapped in a functor.  An applicative is useful (specifically the
apply method \<\*\>) when the function you want applied is wrapped inside a functor and the arguments are functors.

But what about if the arguments might be invalid or optional?  This is exactly why we need functors.  A functor is
useful if we think about it in terms of something whose return value may differ.  For example, Maybe type is for when a
value is optional, Either is used when you indicate an error, and functions yield a value depending on the parameter
that is given.  This is what they mean when they say a functor has a computational context.  Depending on which functor
type is being used, the computation may differ.  Think of this sort of like a polymorphic method.  

{% highlight haskell %}
-- a Maybe type
(+) <$> (*5) <*> Just 20
{% endhighlight %}

## why does this work?

{% highlight haskell %}
import Prelude
import Data.Array
import Data.Maybe

[(+), (*)] <*> [Just 3] <*> [Just 5]
-- map :: forall f. (Functor f) => (a -> b) -> (f a -> f b)
-- apply :: forall f. (Functor f) => f (a -> b) -> f a -> f b
-- This is the same as: apply (apply [(+), (*)] [Just 3]) [Just 5]
{% endhighlight %}
