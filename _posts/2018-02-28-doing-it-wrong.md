---
layout: post
title:  "You're doing it all wrong: Part 1 (UPDATED: 03/02/2018)"
date:   2018-02-28 22:41:00 -0400
categories: FP, FRP, culture
---
I decided that there needs to be a tool for companies to store documents necessary for software projects.  Having used
tools like Rational Tools and Polarion, there had to be something that was enterprise quality, but still more nimble
and easy to use for open source projects.  But on my journey to create better tooling, I encountered many
unusual perceptions from fellow engineers or management about how to do things.

I'll attempt here to explain my views on how to step outside of the box, and why a cultural mindset, even if
"open" can still be problematic.  While this criticism may sound like this is only against my current
company, it is not.  I have, unfortunately, experienced this at every company I've worked for in one form or
another.

# You're doing it all wrong

Ok, maybe that title sounds a bit harsh, but allow me to explain.  I seem to have this really awesome knack for
calling people's babies ugly, but I hope that you bear with me through the entire article before drawing a
conclusion (I know that's not going to happen, but at least be conscious of any emotional reactions you feel
as you read this article and make a note of why whatever you read caused you to react that way)

First off, what are we doing wrong?

- Technical
  - Not recognizing it's an asynchronous world
  - Trying to store state in some object instead of reacting to changes in state
  - OOP because it is familiar, instead of FP because it actually solves problems
  - Dynamic Typing instead of Static typing because you think static typing is unnecessary
  - Using mutable data willy nilly instead of immutable data
  - Using SQL because you know RDBMS (and you think CRUD and consistency are the only ways to handle data)
  - Monolithic entities instead of microservices
  - Wanting to use python for everything, even if it's not a good fit
  - Always using pull or push based technology (ie, one-way directional) instead of push/pull
  - Architecting/Designing to specific implementations instead of abstractions
- Cultural
  - Confusing symptoms of the disease with its cause
  - Not implementing truly Agile product teams, and instead having devs and QE as separate teams
  - Over reliance on black box end to end system testing
  - Being afraid to fail
  - Being afraid to rock the boat
  - Fear of change
  - Short term vs. Long term thinking

## The hard problems are psychological or cultural, not technical

Before I go explaining why all the above are wrong, let me talk about human psychology a little bit. As human
beings, when we are confronted by something new, we typically react in one of two ways:

- Cautiously: with a skeptical, cynical or even fearful point of view
- Curiously: with an open mind and eagerness to learn something new

So, what makes us likely to react one way more than the other?  If the new thing is at least somewhat familiar to
us, perhaps we might be cautious, but not skeptical or cynical.  Perhaps this new thing uses components we are
already familiar with and like.  For example, if you are good at python, maybe you embraced ansible more easily
since you knew what powered it under the hood.

Other factors might simply be an unwillingness to spend time to learn something new, and this manifests as cynicism
or skepticism.  You may think "but this new thing doesn't do anything for me.  Already know 'technology X' that
can solve that problem".

Or maybe, you already know _technology X_ and even though you are aware of its problems, it's better the "Devil you
know versus the devil you don't know".

On the other side of the coin, you've got people who want to hop on the latest _bandwagon of the week_.  I'm
especially looking at javascript here, which seems to come out with new web frameworks weekly.  Sometimes people
think "oh, _company Y_ made this product, so it must be awesome!".  Others may simply hop on the bandwagon because,
well, they dont want to be left behind.

### Excuses excuses

I often hear arguments against new technologies or processes, like a Functional Programming language, or not using
a super popular tool like react+redux along these lines:

- Where will we find engineers who know these technologies?
- Where will engineers find time to learn these new skills?
- But _technology X_ is so popular, we can find engineers and tooling very easy compared to _new fangled toy Y_
- It's still too new and untested
- There's not enough contributors, who will maintain or test it?

Here's the great irony.  I bet a long time ago, people talked about linux and Red Hat in the same vein.  How many
Microsoft apologists used the same exact arguments as the above?  Think about that for a second.

Why did Linux and Red Hat not only survive, how did it thrive?  Because:

- It was technically superior and solved problems customers needed to solve more efficiently
- It allowed for changes

The second point is perhaps the most important.  Change is vital.  To paraphrase Einstein, you can't solve today's
problems with yesterday's thinking.  Or a collorary to that: "Today's solutions are tomorrow's problems".  We need
tomorrow's solution for today's problems.

If your first inclination on hearing about some new technology is:

- I already know how to solve this problem with some other technology
- I dont have time to learn this new technology

Then consider that our field constantly evolves change, otherwise we would still be programming in assembly.  In other
words, your marketability depends on your capability to learn and embrace new ways of doing thing.

### Fear of change

All of this is just talking about new technologies.  About half of what we are doing wrong isn't some new framework, library
or programming language, it's a workflow process.  By process I don't necessarily mean some formal workflow like scrum
ir some ISO certified process.  I just mean "how you normally do things".

People often think because they have Scrum meetings, they are doing Agile.  Maybe managers haven't worked at other 
companies and saw the developer-to-QE ratio.  Maybe management is afraid of having unified product teams, instead of the
traditional (and separated) Dev and QE teams.

We become familiar with a certain way of doing things, and we become fearful of some new way to do things.  If QE and devs
merged into one unified team, who would manage them?  If you start doing sprints, do you have to start doing demos of what
you have been working on?  If QE do more exploratory ad-hoc testing, will managers appreciate that if the team is expected
to have X% test code coverage?  What if QE wants to write unit tests, which traditionally developers do?

This has nothing to do with a technological choice, but everything to do with a work process.  The bottomline though is
the unknown: Will this new process or technology improve or hinder my team's work efficiency and ability to deliver a
quality product?  Or perhaps to put another way, does the cost of learning to do things a new way make up for the lost
productivity while switching to the new paradigm?

## Fighting short term vs. long term thinking

So not only are we fighting intrinsic prejudices (eg "I dont want to change, because that's too much work"), we are also
fighting a time constraint.  Do you think in terms of weeks, months, or years?

In modern times, people have become more and more short sighted.  Instant gratification is the norm.  Facebook has capitalized
on "short dopamine feedback loops" to make you essentially addicted to watching their content.  We want stuff, and we want
it yesterday.  But that's not how reality works.  The consequences and ramifications of certain things can take a long time
to see the results of (for better or worse).

Often, management wants immediate feedback to determine if what is being done has positive effects or not.  Indeed,
many companies use "Management By Objective" for their review process even though it has some criticisms leveled against it.
For example, when goals (and a metric to determine whether you've achieved your goal or not has been met) become how our
performance is determined, then the only thing that matters is the goal.  A tunnel vision ensues where you miss the forest
for the trees.

When we apply certain kind of metrics expectations, we inevitably run into problems.  How do we even define whether we have
become more productive or not?  What if the positive productivity gains are only seen later down the road?

### Fear of failure

People hate to fail.  It hurts our ego, or confidence and of course there are business ramifications.  But if we become paralyzed
by fear, to the point where we are unwilling to try something new, then fortune will not only favor the prepared, but the brave.

Time and technology do not sit idle.  If you and your team is afraid to try something new, just remember that a competitor out
there might not be as afraid.  Maybe it's not just "Second best tries harder", it's "Second best isn't afraid to lose".  They
already lost before, so they know that what they tried didn't work, so either try harder, or try different.

### Laziness and familiarity

There, I said it.  Yes, a huge reason people don't want to try something is simply because they are lazy.  Sometimes this is
covered up with "But I already know how to solve that problem".

Do you really?  Perhaps you do know how to solve a problem, or your workflow is getting stuff done.  But maybe there is a more
efficient way to do things.  Did you seriously consider giving it the old college try?

Perhaps you say "but I am too busy at work".  This is more valid, and this is where managers need to step up and provide time.
A company where managers dont give opportunities for their employees to grow is not a very good company.  But I think most
managers are willing to do this, as long as the managers think the time investment is worthwhile.  For example, I dont think
any manager would have any issue whatsoever to take training that they provide internally (for example Red Hat University).

But what if you asked for time to learn rxjava?  Or how to program in kotlin?  Or heck shoot for the stars and ask them to see
if you can try out eta or haskell.  This is where most managers will probably raise an eyebrow.  I'll get to why managers
should allow this in a bit.

# Why is what we are doing wrong?

Ok, enough about the psychology about why we are afraid to try new things.  Some people actually do want to learn new things,
but perhaps they didn't know what to learn.

Look at the list up at the top.  Do you agree or disagree with them?  And more importantly, what was your emotional reaction
when you read it?

## Assuming it's a synchronous world

The vast majority of engineers have been trained in an imperative and synchronous style of programming.  We either cut our
teeth on C, C++, C#, or java, and probably picked up python, ruby or javascript on the job somewhere.  Of these people, usually
only the javascript folks have an inkling about asynchronous programming (not always, GTK programmers, or kernel programmers
are often familiar with event loops as well).

But almost all our software is built around the idea that when some function makes a call to another function, it will get some
answer back right away.  These synchronous expecting functions will block until the called function returns, and then the calling
function will resume merrily on its way.

### Real world examples

Problem is, not only do many frameworks and tools not work that way, neither does the real world.  Almost all IO should be
asynchronous in nature.  How long will it take to get a reply back from a REST call?  How about a query from a database?  In fact,
will I even get a reply back at all?

**Databases**

Most databases are synchronous in nature, and yet, this causes problems for callers to the database.  Some queries can take a long
time to send an answer back.  Do you want the rest of your code to block waiting for the result?  You may argue that you can't
continue doing anything else until you recieve information back, and yet is that always true? More on that in a bit

**Jenkins**

Jenkins jobs are very serial in nature.  You call some job, and it runs to completion.  But what if some build step along the way
takes a long time to compute or get results back from?  How do you tell a job, half-way between steps, to "move along".

We are so ingrained to think synchronously, that we no longer see dependencies in our code.  We think 'linearly' and have not trained
ourselves to see computations as graphs.  When we think synchronously, we are implicitly thinking linearly.

[show graph]

If we train ourselves to think in units of computation tasks as graphs, we can solve a lot of problems.  Instead of synchronous 
programming looking like a linked list, we can start building graphs, where each node is some unit of computation (usually a 
function, or combination of functions) and the edges are dependencies.  In other words, some function can only be called if some
other function has returned the data necessary.

## Trying to store state instead of reacting to it

If you've come from the OOP world, your first thought is to store and hide data away in some object, and provide functions to 
get and set the state (encapsulation).  This is a pull or request based way to think state.  Some ObjectB has data that ObjectA
wants to set or get, so ObjectA will call a method on ObjectB to set or get this data.

When this "store the state" in some object is used, we have to start defending it, and not just in a multithreaded world.  You get 
into the ugly ugly world of locks and transactions so that you can defend this data.  Step back for a minute and ask yourself, is
that the way the real world works?

Imagine a white board with a couple people in the room, and someone wants to write on it.  Does the writer hide the white board 
until he is done finishing writing, so the others watching don't see incomplete data? Could another writer come to the board and
write on it at the same time (assuming he didn't draw over the other writer)?

In this scenario, the drawer is creating new data constantly, perhaps one letter or picture at a time.  The writer may not even 
be done yet, but logic for deciding when the data is "complete" can be determined (eg, you recognize the letter A, or a square).
The watchers may not have all the data yet, but they also don't have to draw a conclusion yet, until the writer says, "I'm done".
Each watcher is getting the same data simultaneously.  The watchers don't constantly have to ask the drawer "are you done yet?".
Instead the drawer will declare he is done (probably by stepping away from the board, or putting down the marker).  If someone
else wants to draw at the same time on the board, he can pick some other clear space.  The observers can watch both drawers 
at the same time.

This is sort of how reactive programming works.  Instead of hiding data away on some object and making requests to see or set it, 
you just observe. If you have multiple writers, you really have two objects of interest (in the white board example, the "clear
space" is effectively a second white board).  In fact, the second writer can react to the data the first writer is writing!

Each new sentence on the white board would be a _next_ event, and stepping away from the white board would be a _complete_ event.
An _error_ event might be the writer mispelling a word, or drawing a wrong line in the diagram, perhaps triggering a new rewrite.

Maybe the first thing that comes to your mind to implement that is with callbacks.  While that is one way, there are better ways to
do reactive programming now.  And this style of programming makes writing concurrent or async programming more easy than before.

Perhaps you've heard of javascript (or java's) Promises, and think that's the way to do it, while that can be done, Promises have 
limitations.  For one, you can't cancel a promise, and two, a promise return more than once.  How could you make a promise for a 
javascript generator?  (you cant).

## OOP led us down the wrong path

This is where people usually start to get touchy.  They know OOP.  They had to pass interview questions about OOP.  So they might 
get a little defensive when they hear how much better FP is.

- But FP doesn't work for stateful apps like GUI's or games
- FP is just for Ivory Tower whitebeards who don't have to work "in the real world"
- FP is just OOP done right (ie, SOLID)
- FP is too much of a performance hit
- FP doesn't have design patterns to help you out

First, let me point you to my article about [Functional Programming on DZone][-fp-dzone].  Come back when you're done.

Read it?  You may want to read the comments too.  They are probably just as informative as the content of the article.  Noticed that
I said that OOP and FP are not mutually exclusive.  This is true, but OOP just isn't necessary.

The biggest problem with OOP is that it's just not composeable.  The other problem with OOP was conflating data (state) with
methods (algorithms).  It's an intuitive notion for sure, but it also tightly couples the two together...which we should not
be doing.

## Dynamic typing lost

Ok, bold predictions, and a sure fire way to start a flame war.  But I've got my flame retardant suit on, so bring it on :)

Why make such a bold statement that dynamic typing lost?  First off, let me make two caveats

- Dynamic typing is ok for very small projects (less than 300-500 lines of code)
- Dynamic typing is ok for prototyping, and then converting to type system

ok, so, how did dynamic typing lose?  Look at all the popular new languages developed in the last 4 years:

- elm 
- typescript
- flow
- kotlin
- swift
- go
- rust 

Not a single language was dynamic.  Sure, elixir is dynamic, but that came out more than 4 years ago.  Ditto with clojure.
Look at all the compile to javascript languages too.  How come all of them are statically typed?  If dynamic languages are so
awesome, how come Microsoft invested all this energy into typescript, and Facebook into flow and reasonml?  Or google with
dart (a failed effort, and they are now using typesscript as an official language).  If three of the biggest javascript using
companies switched over from javascript to some other typed language...why?

Ok, one could reasonably argue that javascript sucks.  I think you will be hard pressed to find javascript programmers who 
even if they like javascript, wont admit that javascript has a ton of problems.  The problems with _this_, non-lexical scoping,
hoisting, equality checking, and all kinds of other weirdness may explain why so many companies are ditching the dynamic 
ecmascript for a statically typed one.

One may also try to argue "look how popular python is!!".  And yet notice how python gained type annotations in 3.4?  Sure, 
they are optional, but why would Guido (who personally spear-headed PEP484) go so far as to mention that type annotations are 
"pythonic"?

To coincide with python getting type annotations, clojure got core.spec (also type annotations), and elixir got them as well.
So, what's going on here?  Did the world finally realize that duck-typing isn't enough?  That documentation isn't enough?
That ease of writing code did not make up for errors only caught at runtime in enterprise applications?

There's really only one good excuse for dynamically typed languages, and that is the expressivity problem.  There exists code
which would otherwise be valid, but can not be typed.  

**"A-ha!!  so typed languages are not Turing complete!!"**

That's not what I said :)  There are some expressions which are semantically and logic valid, but can't be expressed properly
with a type.  For these rare circumstances, typed systems do offer escape hatches.  But these cases are rare.

## What to talk about next?

So, I'll close this post for now, and continue with why "you're doing it all wrong".  

[-fp-dzone]: https://dzone.com/articles/functional-programming-is-not-what-you-probably-th