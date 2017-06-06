---
layout: post
title:  "Purescript tips and cookbook"
date:   2016-05-15 02:19:40 -0400
categories: purescript nodejs javascript
---
So, there's a lot of stuff that I am learning by reading purescript code in other projects that isn't really reflected
in the book.  These kinds of things might be buried in the Language Reference Guide, but I am going to keep a running
tab of things I find here.

I'm also going to try to keep a list of examples for common scenarios.  Unfortunately, there's no equivalent of clojure
doc for purescript, and that helped me learn clojure quite a bit.

# Syntax

I've discovered quite a few things from reading code that is not obvious

## The let form in do syntax does not require a matching in

Normally you see a let/in block.  The let part will introduce new symbols, and the in part will make use of them.  But
in a do block, this is not necessary.

## type can take params

I assumed that type couldn't take params, but it can.

## Triple quoted strings

Are kind of like triple quoted strings in python

## You can have a where block in do notation

I hadn't seen examples of this until recently, so I assumed this wasn't possible, but it is.  And in fact, you can have
do blocks inside of where blocks.

## Sections for partially applied functions

In haskell, you could do this:

```haskell
(+2) 10
(10-) 5
```

But in purescript you do it like this:

```haskell
(_ + 2) 10
(10 - _) 5
```

# Examples of common functions
