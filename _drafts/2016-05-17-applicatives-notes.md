---
layout: post
title:  "My first node app"
date:   2016-05-15 02:19:40 -0400
categories: purescript notes
---
I think I've finally wrapped my head around functors in haskell/ps.  I kind of had two a-ha moments.  Usually when you
see tutorials about functors online, you'll hear them say that Functor is like a box that holds some value.  Problem is
that doesn't hold up that well when you think about 2 major applications for Functors:  IO and functions.

# The context of a functor

When you see it explained that functors are a "box" that holds some value, that analogy really only holds in some kinds
of functors.  For example, what is the "box" when you consider that a function type is a Functor?  What is the box when
you consider IO as a functor (is it the outside world)?  The better analogy is that a functor has or rather needs a
context.

For example (+3) returns a value, depending on the parameter it is given.  The functor (Just "foo") may return a new
Just or Nothing, depending on the value.  In other words, the context is 2 things:

- The 2nd argument supplied to fmap will determine what kind of functor is returned
- The context **is** the type

Recall the defintion of a map:

{% highlight haskell %}
map :: (Functor f) => forall a b. (a -> b) -> f a -> f b
{% endhighlight %}

This says that map takes a function that takes some type _a_ and returns a type of _b_ (note that _a_ and _b_ can be of
the same type), and another argument which is a functor of type _a_, and it returns a functor of type _b_).  An
alternative way of seeing this function that might be more useful is to look at it like this:

{% highlight haskell %}
fmap :: (Functor f) => forall a b. (a -> b) -> (f a -> f b)
{% endhighlight %}

This says that fmap takes a regular function (not wrapped in a Functor) that takes a type of regular a and returns a
regular b, and returns a function which takes a Functor of a and returns a Functor of b.

But why is that useful to think of map like that?  Because of the way it's defined, the function passed to map has to
take a single arg.  But what if we pass a function that takes more than one argument?  Or a partially applied function?



Before we answer that, lets see some instances of functors where its a stretch to think of them
as boxes.

## Functions as types

First, let me start off by saying I hadn't really thought of functions as types, but it makes perfect sense.  How else
would you be able to treat functions as first class citizens unless they had a type?  The **->** operator is actually
a type constructor for a function.

{% highlight haskell %}
getKey :: k -> Map k v -> Maybe v
getKey k (Map key )
{% endhighlighting %}


## Why are functors useful?

So why bother with Functors?  If you think about it, when we first encountered the map function, it was only useful
when we used it with a list:

```haskell
map (\x -> x * 2) [1 .. 5]
```


# Applicatives

I think the major part with applicatives is that if a function is wrapped inside a Functor, we cant just use that
"wrapped inside a functor" function with fmap.  That's where Applicatives come into play

{% highlight haskell %}
(+) <$> [10,20] <*> [3,4]
(replicate) <$> [2,4] <*> ["a","b"]
{% endhighlight %}

## Defining an instance of an Applicative


# Monads

Now we get to Monads!  Some quick notes:

- Monads are sequential
- Monads allow you to chain or pipeline functions in a way Applicatives can not
  - The arguments which are lifted in an Applicative are independent of each other
  - Consequence: You can parallelize Applicatives with apply, but you can not with Monads

Consider this code:

{% highlight haskell %}
-- map :: forall a b. (Functor f) => (a -> b) -> f a -> fb
map (\x -> Just x) (1 .. 5)
-- bind :: forall a b. (Monad m) => m a -> (a -> m b) -> m b
-- Intuitively, in the first arg, take out the a from the monadic context, and apply it to the 2nd arg.
bind (1 .. 5) (\x -> [Just x])
{% endhighlighting %}
