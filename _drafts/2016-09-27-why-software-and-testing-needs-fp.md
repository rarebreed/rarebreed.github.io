---
layout: post
title:  "Why software and testing needs FP"
date:   2016-09-27 07:53:00 -0700
categories: purescript haskell FP testing notes
---
My company is starting to narrow down on "acceptable" technology stacks and this could be a good or bad thing.  My fear
is that since Functional Programming is seen as a very niche either FP languages, or even FP style will be frowned on.
I have seen this anti-FP reaction before.

I have talked to several people (including managers) who feel one or more of the following:

- FP is all hype for elitists who think they are better than OOP (or imperative) programmers
- FP languages are just a passing fancy for people who want to be cool and hip (hey I read Pragmatic Programmer!!)
- It's too hard to find engineers who can do FP
- FP is for Ivory Tower academics who don't work in the real world (ie, you can't do GUI or games)
- FP is just another overblown and oversold idea, just like OOP

# The myths about FP

So, please allow me to dispell these notions and myths surrounding functional programming.  Just in case you haven't
heard about Functional Programming, it describes a style of programming which more closely resembles the mathematical
definition of a function.  By following this more mathematical definition, several benefits are obtained.

## FP is for elitists

This is a big one, and you will see it _alot_ online (especially if you visit quora).  There seems to be some kind of
fear driving OOP programmers in dismissing FP.  I believe a lot of this has to do with the fear that an engineer's
skill will start to become obsolete, and let's face it, a lot of engineers just don't want to learn anything new.

I see this even in Java.  Java 8 has made some big strides in becoming a more functional language.  Not just in the
addition of lambdas, but in the design of some of their libraries like the Stream API.  Another example is the new
CompletableFuture type which is actually an example of a monad in Java (gasp!! the horror!!).  When Java 8 was getting
lambdas, I saw so many people write that this was a stupid and unnecessary feature.  They were arguing that lambdas
were just syntactic sugar for anonymous inner classes (essentially true)

## FP is for hipsters

Another notion I see a lot of online is that FP is just a fad.  I think part of this stems from the fact that FP is
actually _not_ new.  Indeed, it's older than OOP.  The fact that FP is having a resurgence is, in this myth, due to
its perception as being cool, hip and _different_.
