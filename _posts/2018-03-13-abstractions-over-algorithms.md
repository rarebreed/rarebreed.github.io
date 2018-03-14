---
layout: post
title:  "Abstractions over algorithms"
date:   2018-03-13 20:41:00 -0400
categories: FP, FRP, culture
---
# Abstractions are how you think, algorithms are what your code does

I was reading an [interesting article][-cont-monads] while trying to better understand different kinds of monads.  The
author brought up an interesting point that I had never really thought about that much.  The author said that he was more
interested in algorithms than abstractions.  As I digested that, I realized immediately that I didn't concur with him.

But what did he mean by abstractions?  In the context of that article, he mentioned programming languages specifically,
but I think abstractions run deeper than that.  Or perhaps, your language helps deepen your ability to abstract.

## Algorithms are very context sensitive

Often in interviews, the erstwhile candidate will be grilled with all kinds of algorithmic and data structure questions.

- Describe the differences between In Order, Post Order, and Pre Order tree traversals
- How can you detect a cycle in a graph?
- Show a deletion function for a balanced tree such as an AV or Red Black Tree
- Why do you need a good hash function for hash maps/dictionaries?
- What is the worst case runtime scenario for quicksorts?

Frankly, I could care less about any of those questions.  Ostensibly, such questions are supposed to be good indicators
for how good of a software engineer you are (or at least whether you attend hacker competitions).  But why do I care so
little about algorithms and datastructures?  Aren't they like, the foundation of computer science?

I would argue that no, they are not.  The reason is that the algorithms you choose are dependent on the data structures
you have, and the data structures you need/have are highly dependent on the problem you are working with.  If for example
you need to work with possibly infinite data structures, how is any kind of graph or tree going to help you?  

_Wait...how are infinite data structures even possible?_

They are all over the place.  Lots of mathematical calculations (sequences and series) are effectively infinite unless 
they are asymptotic.  But in the "real world" everytime you click on a mouse or a keyboard, you are entering data.  This 
is theoretically infinite.  So are twitter feeds.  So are generators (and possibly coroutines).

_But those aren't data structures!! That's just data!_

Ah, so now we get to the crux of the matter.  All we _really_ care about is the data correct?  When you hear about Big-O
notation, that doesn't exactly work for infinite sequences does it (at least for space time complexity).  And for that
matter, all of those data structures you learned about in school were synchronous data structures.  It assumes that the
data is already stuffed away, either in-memory or persisted on disk and reserialized.  None of those data structures 
accounts for data that arrives at some future time (eg mouse clicks).  

Now, we sometimes care about storing data from potentially infinite, lazy, or asynchronous data source into something we 
can access.  This is when knowledge of certain data structures can be useful.  Maybe you need a Trie to lookup strings, 
or if you are going to do a lot of insertions/deletions, a balanced tree would be useful.  But honestly, in my career, 
only one time have I had to do come up with actual algorithm for a data structure (it was a graph for a dependency 
resolver).

### The real problem is state management

In my career, the bane of my existence has been state management.  Admittedly, I have not done 3d programming, big data
or machine learning, where speed and performance do matter.  I touched on RBTrees (a specialized Btree) in some file
storage work, and as I mentioned before, I did have to write a dependency resolver once (not that I could do it off the
top of my head again).

But state...that's the million dollar problem.  I got into Functional Programming to try to combat it, and when I read
Rich Hickey's article about "State, you're doing it wrong", I like had this epiphany.  Or maybe it was a nightmare.  But
I just remember thinking, "oh my god, he's right".  And Functional Programming required a whole new slew of data 
structures to be invented.  Clojure for example, mostly uses special tries for their associative containers.  But any
data structures you learned prior to about 2010 were probably all mutable data structures.  And since your data types
were mutable, your algorithms often mutated them.  Which means that all those fancy data structures and algorithms you 
learned in school were worthless in a FP environment.

This doesnt mention the fact that imperative languages mostly don't deal with laziness.  The recent trend in languages
is to introduce generators or coroutines of some kind, or close cousins like streams.  But it's only been relatively 
recently that these kind of data generating functions were even considered unless you were a hardcore PLT guy.

And all of this is because managing state becomes much much easier when you can't mutate it.  My point however is that
data structures and algorithms are highly dependent on your use case and programming needs, whereas on the hand your
typical abstractions are often universally applicable.  And this is because they are often more of a strategy than a 
tactic.

## Abstractions deal with expressing problems

I kind of equate data structures and algorithms with calculus, and abstractions with, well, abstract algebra.  And as 
hard as calculus and diff eq was, it's not like abstract algebra.  I minored in math in school, and I remember the 
College of Math had a big warning to Undergraduates that Abstract Algebra and Number Theory were considered the two
hardest classes for Undergraduates to take.

Now, why is that?  Abstract Algebra builds on your ability to prove things (which I sucked at, but somehow managed to 
limp by) but more importantly, to study relationships.  When you study groups, rings, and fields, and concepts like
isomorphisms and homomorphisms, what the class is really training you to do is to see patterns between what seems 
like unrelated concepts.

_Oh man.  Are you one of those Ivory Tower whitebeards who looks down on everyone and throws out words like Endofunctor?_

Ok, first off, that's a childish and silly attitude to have.  Unfortunately, I see this attitude a lot in comments I
read.  It's almost like there's a geek backlash, because some geeks feel like they aren't geeky enough, and have to 
revolt against the "you're even geekier than me" crowd.  I understand there's more than enough Smug Lisp Weenies and
Insufferable Haskell Pricks.  And sadly, those folks are making it harder for FP and FRP to become popular.  It is 
hard to learn about abstractions, but it is helpful, and there's a lot we can learn from those Ivory Whitebeards.

_Like what?  How is knowing what a comonad is or a functor is supposed to help me?_

Perhaps you are familiar with SOLID principles?  How come no one ever made fun of Liskov Substitution?  And what about 
all those Design Patterns the Gang of Four and others highlighted?  I never heard the OOP crowd make fun of these 
before.  And most people didn't seem to have a problems with Design Patterns like the Observer, Singleton, or Visitor
Patterns because those words come from the common vernacular as opposed to mathematical jargon like Monoid, Monad, 
Arrows, Combinators etc. People start hearing those terms freaking out.

Those words just happen to come from Category Theory (or lambda calculus), and they are the mathematical equivalent of
Design Patterns.  In fact, Monads have become ubiquitous (or maybe I just see them more, now that I know what monads 
are).

- Are you a javascript programmer that's used Promises?  Guess what, you used a monad.
- Have you used reactive libraries?  Guess what, Observables are monads

A Monad is just an abstraction that solves certain classes of problems.  In a way, a Monad is like a factory of patterns.
You can have State Monads, or Writer Monads, or Reader Monads, or Maybe Monads, etc etc. Rust's Result/Option type is a 
poor man's version of the Maybe Monad, as is Java 8's Optional.

Or what about Functors?  You use javascript's Array.map() at all?  Well, you didn't realize, but javascript arrays are
Functors.  If you can have a function that takes two arguments: a function that maps an object of TypeA to an object 
of TypeB and returns a _container_ of TypeB, and some _container_ of TypeA, then that _container_ is a Functor.  I put 
_container_ in italics, because not all Functors are literally containers like Arrays.  A function is also a Functor (the
Functor of a function is the composition of two functions, or in haskell-speak:  (map f g) = f . g ).

_I still don't get it.  How is any of this useful to me?_

When we have to solve a problem, we need to figure out two steps at a minimum:

- Map the real world problem space into my head
- Convert this problem into solvable steps, using the constructs I know

For example, let's say you never heard of recursion.  You would need to solve every problem with some kind of looping 
construct.  While you can convert any loop to a recursion or vice versa, many problems simply frame themselves in one
construct or the other.  By not knowing recursion, you set yourself up for more difficulty with this translation 
process. The same is with monads.  If you don't know what a monad is, you fail to see that there are already solutions
out there that can solve your problems, just like Design Patterns are "cookbook" like blueprints to solve well-known 
problems.

By learning new abstractions, you aren't just adding a new tool to your tool belt.  You are learning a potential new
way to wield the tools you _already know_.  Monads are not unique to haskell.  As I mentioned earlier, Promises and
Observables are also monadic.  So is Java 8 streams.

### Does it go deeper?

If we can apply the Sapir-Whorf hypothesis to computer languages, then wouldn't learning new formal languages actually 
change how we think?  Is our very way of thinking constrained by our vocabulary and semantic model of our language?
If this is true for the spoken language, I would wager that it is also true for formal languages.

If all you have is a nail, everything becomes a hammer.  If you only know how to think of the world inside of Object
Oriented Classes, then you could miss out on simpler abstractions (ie functions).  This is why learning lambda
calculus can actually be helpful.

Speaking of which, I should brush up on the first chapter of Haskell from First Principles which is entirely dedicated
to the lambda calculus.  And from there finally understand what the Y-combinator truly is :)

[-cont-monads]: http://blog.paralleluniverse.co/2015/08/07/scoped-continuations/