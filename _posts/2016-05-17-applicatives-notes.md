---
layout: post
title:  "My first node app"
date:   2016-05-15 02:19:40 -0400
categories: purescript notes
---
I think I've finally wrapped my head around functors in haskell/ps.  I kind of had two a-ha moments.  Usually when you
see tutorials about functors online, you'll hear them say that Functor is like a box that holds some value.  Problem is
that doesn't hold up that well when you think about 2 major applications for Functors:  IO and functions.

## Functions as types

First, let me start off by saying I hadn't really thought of functions as types, but it makes perfect sense.  How else
would you be able to treat functions as first class citizens unless they had a type?  The **->** operator is actually
a type constructor for a function.

{% highlight haskell %}
getKey :: k -> Map k v -> Maybe v
getKey k (Map key )
{% endhighlighting %}
