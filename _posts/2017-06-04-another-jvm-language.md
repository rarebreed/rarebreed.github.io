---
layout: post
title:  "Time to learn another JVM language?"
date:   2017-05-04 07:21:40 -0400
categories: jvm notes type-theory
---
Hard to believe it's been about a year ago when I first started seriously looking into purescript and haskell.  Sadly,
I still don't have a lot of experience with purescript.  I did have an opportunity awhile back where I put in probably
a good month of work time investigating how to use purescript to call a subprocess.  Basically, I wanted to replace a
bunch of bash code in a jenkins job with a "real" language.

The experience was definitely hair pulling but I still have a lot of respect for pure functional languages.  What I
discovered while actually writing some non-trivial purescript was that it wasn't so much that the whole Monadic part
that was really hard to figure out (although my initial foray into Monad Transformers was quite confusing), but honestly
it was the types that got me in trouble.

I still have a lot of problems figuring out Eff, and especially Aff.  The row polymorphism is still something I struggle
with, not from a conceptual point of view, but from an implementation point of view.  The same can be said while I was
trying to work on the types.  This got me to thinking that I probably need to do more work on more abstract type systems
while working with purescript.

# In comes Java

Meanwhile, my work life means I mostly work within the JVM (although soon I'll be doing more with javascript).  But I am
becoming more and more tired of Java.  While Java 8's stream API and lambdas were a breath of fresh air, there's still
just a lot of stuff that is painful.

For example, lack of pattern matching, destructuring, Sum types, default function arguments and type erasure just sucks.
So, the last week or so while refactoring a ton of my polarize code, I yearned for something else.  I could have gone
back to clojure, but I think I can't go back to a dynamic language anymore.  Core.spec I heard you say?  Type
annotations are not a replacement for a real type system.  They are at best, a band aid, and they leave you with an
unsound system and perhaps a false sense of confidence.  Perhaps better than nothing, but it's a band aid and should be
realized as such.  Even Facebook's Flow recognized (and criticizes) Typescript for essentially the same problem.  In
essence, Typescript does not provide a real type system and so there are type holes and other errors that will slip the
typechecker.

But back in JVM land, what strongly and statically typed language could I use?  The obvious answer is "Scala dummy!". I
did look into it briefly.  It did seem to tick off all the right boxes.  It has a powerful type system, it's mostly
functional, and there's even a JS and LLVM backend for it.  But, Scala's type system, if you ask the experts, also seems
to be broken in some ways.  The gist of it seems to be that subtyping without Rank-N types or higher-kinded types leads
down a dark path.  So, what about Kotlin?  Kotlin seems to be 80% of scala minus some of Scala's more powerful type
abstractions.  But the type system is what I would like. Groovy is still a dynamic language at heart, so that's out too.
Ok, so, what's left?

Well, believe it or not, I started taking a more serious look at Ceylon.  "Uhhhh, never heard of it".  Yeah, that's
probably what most people will say.  Ceylon sort of got lost in the Kotlin stampede I think.  And I have to admit, I
didn't give it much of a glance 2 years ago when I first heard of it.  It just seemed like a mostly Object Oriented
programming language that happened to have nice IDE support and real modularity baked in.  But after some deeper reading
I think that the Ceylon team needs a better advertiser or PR person.

As I looked into Ceylon more deeply, it's actually got some attractive features to it.

- Very powerful type system
- More interesting generics, including reification
- Immutability by default
- Designed from the ground up to abstract away the VM instead of a JS backend after the fact
- Very nice error messages
- Better modularity answer than Java 9 modules
- Interesting way of handling nulls
- Limited form of dependent types

I'll go into these pros a little bit, but let's look at some cons I discovered too:

- The syntax is very verbose and "wordy" (and clunky IMHO)
- Java interop looks complicated
- Not a heck of a lot of 3rd party modules
- Will it survive with all the devs going to scala/kotlin?


And there's a few "meh" or "I hope they implement this" things to consider:

- No true pattern matching (but it has a close relative and some destructuring)
- Higher kinded types and rank-n types only experimentally supported for JS backend
- GADT support is likely, but not on the roadmap anytime soon
- There was talk of a llvm backend, but haven't seen anything of it

## The things I like about ceylon

So, some of these "pros" I am relying upon third party observations.  So take some of these with a big grain of salt.  I
won't go over all the pros however, just the ones most interesting to me.

### Powerful type system

Ceylon has a very interesting and very powerful type system.  It's one of the only languages I know of that supports
Union and Intersection types.  To be honest, I still haven't fully figured out what these are.  At first, I assumed they
are the same as Sum and Product types respectively, and yet some of the documentation seems to make a distinction.  It
even claims that there are only 2 mainstream languages that even support these types.  The examples I saw of them in
use looks a lot like Product and Sum types though, and they showed some examples of how to use them in enumerated types
with switch statements.

It also has support for flow sensitive types (as does Kotlin, Flow and Typescript).  Basically, it allows you to
determine the type of an object based on control flow (ie conditional statements).

### Variance and generics

So, this was always something that baffled me with java's generics.  What exactly does List<? extends Foo> and
List<? super Foo> really mean?  The introduction in ceylon actually explains the concept very well and shows how it uses
something called declaration site variance instead of wild cards.

Also, ceylon manages to reify generics.  If you know what type erasure is, then you probably know what reification is.
But basically, what java knows about a type at compile (with a generic class reference) is different than what java
knows about it at runtime.  With ceylon, what the compiler knows about a generic type reference at compile time is the
same as at runtime.  What's the big deal?  It allows you to do things you can't in java (like check the instance of a
generic type), and it also helps with debugging.

### Immutability by default

One of the big things that got me while first reading about Scala almost 7 years ago was it's decision to have either
val or var variables.  To me, this was a poor choice.  Some people have a hard time with clojure because basically all
the data types are immutable (with a few exceptions: vars, refs, agents and atoms).  Rust also decided to make all
bindings immutable by default, and you have to go out of your way to explicitly state when something is mutable.  This
is the stance ceylon took, and I agree with it.

### Designed from the ground up to be abstracted from the backend

The ceylon language seems to take its spec very seriously.  Rather than most languages designed around running on a
particular VM first, and then later porting it to work for some other backend, ceylon did its work from the ground up
to be backend agnostic.  This gives me more hope that it will be easier to port to other backends and to maintain the
other backends

### Nice error messages

Sometimes I look at Java stack traces and go numb.  Sure, they are nicer than the eye-stabbing horrors of clojure stack
traces, but still they can get ugly, especially when generics are involved.

### Real modularity

So, java 9 will be disappointing on the modularity front.  I don't even understand what the point will be if you can't
declare the version of your module.  It seems that the recent delay to Java 9 addresses some of these concerns, and if
jigsaw turns out to actually be a good solution, the ceylon team has already said they will support it (as it does with
OSGi as well).  But having an already working out of the box modularity is nice indeed.

### Handling of nulls

The way ceylon handles nulls is kind of interesting, and it basically provides for some syntactic sugar.  Basically,
nulls are not allowed unless you postfix a type with ? (or you use a union type).  For example:

```
String someString = someFn();  // someFn() can not return a null...it is a compiler error if it doesnt

String? anotherString = anotherFn();  // anotherFn() can return a null
if (exists anotherString) {
  // Do something with anotherString
}
```

### Limited form of dependent types

Have you ever had to work around code where you have a function that returns some kind of collection, but it's possible
that this collection is empty?  I run into this all the time.  In python, you usually have an idiom where an empty
collection is a _falsey_ value, so you can do a truth check on it.  In clojure, you can do something like (empty? coll)
and so on.  Ceylon has a way to specify if a function must return a collection with zero or more, or one or more items.

```
{String+} atLeastOneItem();
{String*} zeroOrMoreItems();
```

## The things I'm not too keen on ceylon

No language is perfect, so here are some of the things I consider warts

### Looks like lambdas can only contain a single expression

I could be wrong here, but every example I've seen with a fat arrow function is a single line expression.  I mean come
on, didn't they realize python's lambda's suck for this very reason?

### Very verbose and wordy syntax

So, one of the overriding goals of ceylon is to be "clear", so they eschew a lot of symbols.  Perhaps I've just gotten
used to reading haskell or purescript with functions like >>=, :>>=, ?, <?>, etc etc.  Now, one could argue that haskell
went too far in this direction.  But there's just a lot of things that are just too verbose.  While the author seems to
think this makes the function more readable, I don't.

### Complicated java interop

Since ceylon's type system is different from java's the interop page is quite long.  From my cursory glance, one big
gotcha is that ceylon doesn't have primitive types at all.  So arrays of primitives have to be handled differently.
Also, java's boxed types (eg Integer) can't be assigned to ceylon types and vice-versa.  Also, working around overloaded
methods is tricky.

To be fair, clojure's interop with java arrays is also a bit painful.

### Limited ceylon 3rd party modules

For a language that's been around 6 years, there's a dearth of 3rd party modules.  At this point in clojure's lifecycle,
it was already quite a big success with many jars available for it.  So that leads me to the next point...

### Popularity

Given all the mind share of kotlin (and scala and clojure), will Red Hat ultimately drop ceylon support?  A big open
source community can only go so far (and ceylon doesn't even appear to have that big of a community), so having a
sponsor really helps.

The cynical side of me also thinks that no company is going to be impressed to see ceylon as a programming language on a
resume.  But then again, having purescript on my resume probably won't either.  I always seem to be attracted to some of
the least popular programming languages (I wonder why?).

## Some _meh_ things or things I hope they implement

There are also a couple of things I am either indifferent about, or hope that they eventually support.

### Higher kinded and rank-N types

So, this is a bit esoteric, and unless you have a haskell (dialect) background (and no, that doesn't include Elm...elm
just superficially looks like haskell, but it's lacking this very feature) you may not understand what it is.  I had a
hard time understand higher kinded types (HKT) and rank-N types but I will just point you to two articles, and a very
simple explanation of what an HKT is:

```
a Higher Kinded type is just the "type of a type"
```

"A type of a type?"  Yeah, I know.  So, think about it this way.  A String or a Number is just that, a String or a
Number.  A value of that type doesn't need to know anything else, it just is.  But what about List<T>?  If I have a list
of some type T, I need to know what T is before I can instantiate an instance of one.  So the type isn't complete yet.
In haskell, the type of a type is called a Kind (hence higher kinded types).  So the Kind of a String is called *.  Yup
just an asterix.  What's the Kind of List<T> (or in haskell speak: List a)?  Well, I need to know what T is, so in
haskell the Kind of this List would be: * -> *.

Hmmmm, ok, so what do those * thingies really mean?  An asterix is a type!!  In haskell, the -> arrow is actually a
function.  But recall, in functional languages functions _are_ types!  So, a higher kinded type is a type (or function
actually) that takes one or more Types, and returns some other Type.  So, what about a Map?  In Java, you have Map<K, V>
(or in haskell it would be Map k v).  So its Kind would be * -> * -> *.  Starting to get it?  If you say you want a Map,
you really have to say "I want a Map of (key type) String to (value type) String."  You can't just say "give me a Map.".
If you did, the compiler will say "A Map of what??".

Ok, what's this got to do with ceylon and why is it a bad thing?  Afterall, java doesn't have this concept either and it
gets along fine without out.  The problem is that with out the concept of HKT's, you can't have Functors.  And without
Functors, you can't Applicatives, and without Applicatives, you can't have Monads.  Perhaps you're thinking big deal,
most languages don't have that either.

This is true, but if you're going to have a nice type system, go all the way and just support it.  According to some
blog posts, apparently the ceylon compiler itself makes use of parameterized Type constructors, so I'm not sure why they
don't implement it for both the JVM and javascript backends.

# What next?

So, I'm going to start playing around with ceylon some.  I will also eventually play around with lux, if and when it
seems to get more stable.  I'd really love to have a purely functional strongly typed lisp.  But until then, I think
ceylon will be a stop-gap to hold me over.
