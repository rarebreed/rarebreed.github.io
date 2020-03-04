---
layout: post
title:  "Everything learned from khadga"
date:   2020-02-26 15:00:00 -0500
categories: rust wasm css kubernetes
---

# What I've learned working on khadga

khadga has been a really interesting project so far.  It's dusted off a few skill sets, and forced
me to learn quite a few new ones.  Unfortunately, I haven't even been able to work on what I
_really_ want, which is the machine learning part.  At the rate I am going, I'm figuring it will be
about anothe 2 months to get to the point where I can start implementing tensorflow.

That being said, here's everything I've been skilling up on in the last 2 months or so

## CSS and the DOM

Many moons ago, I did a very small hobby web page.  This was pre-flexbox and pre-grid days.  IIRC, I
was using python's pyramid web framework (but it could have been another I no longer remember the
name of).  I remembered struggling with aligning images and text, and getting confused between
inline, block and inline-block display types.

At the time, I didn't know any javascript, so I couldn't dynamically manipulate the CSS.  This time
around, things are different.  I consider myself an adequate modern javascript/typescript dev now.
Notice the qualifier _modern_.  ES5 and god forbid ES3 look like holy nightmares.  Wrapping my head
around _this_ is bad enough.  But in modern javascript, you almost never see abominations like
IIFE's, or reliance on hoisting, and _var_ is all but gone.  Originally, I mostly worked on the node
runtime, but for the last few months I've been more on the browser side of things.

But being able to have javascript manipulate the DOM and the CSS is pretty neat.  I'm still getting
familiar with CSS Grid and Flexbox, but it's already helped tremendously.  You don't need to worry
about `float` as much now, and getting your _divs_ or _spans_ to stretch all the way across seems
easier than I remember using Flexbox.

I've got to the point with my CSS that I was able to drop the bulma CSS framework, and khadga is now
100% hand crafted CSS.  I'm far, **far** from being good, or perhaps even decent at CSS.  However, I
am at the point where I can look at other CSS and understand what it's doing and maybe just look up
some css attributes or values I haven't heard of.

## React and Redux

I first got started with react late in my Red Hat career.  I wrote one small internal web page
running on node express.  All it did was let you view messages coming across dbus or an ActiveMQ
bus, and also to let you send your own messages from a text input.  But, it was enough to get me
familiar with the basics of React, including state, props and the react lifecycle.

I also started the work on figuring out how to test the new cockpit web application and the
subscription management plugin.  I recalled doing some proof of concept testing using the Enzyme
library, along with AVA and jest.

When I got to Netflix, we only owned two web page components, one of which wasn't written in react
at all.  Nevertheless, I was only one of two engineers on our team who had any web front end (or
backend for that matter) experience.  So I wound up digging for bugs, but not much else.  But at
least it forced me to remember the React concepts.

However, it wasn't until I started work on khadga that I also learned redux.  I was torn on using
redux or mobx, but there just seemed to be way more documentation for redux.  Now that I am more
familiar with redux, I'm not all that impressed honestly.  In order to work with async data, you
have to use middleware like redux-thunk.  This is where Reactive programming shines.  In fact, I
don't even use redux-thunk.  I just create reactivex Subjects, and I do my async data and push it to
the Subject.  The sunscriber to the Subject will then have the ready data which can then call the
actual reducer.  Just seems to make sense to ditch the reducers (and action creators) altogether and
just use ReactiveX.

## Kubernetes

This is the first time I had a chance to work with kubernetes.  I could have used something like a
vanilla GCE (Google Compute Engine) or the GAE (Google App Engine) for my needs.  Currently, I've
only got one service running, so it's not like I need a bunch of coordination for lots of little
microservices.

On the other hand, as khadga grows, and/or needs to scale out, I've got a path forward for this.
That being said, kubernetes is a pretty big learning curve all by itself.  Clusters, nodes, pods,
deployments, services, secrets, ingresses....oh my!

And then there's the yaml files.  Yaml files everywhere.  And that's the _easy_ way to work with
kubernetes! The alternative is to run a bunch of kubectl commands (or use the gcloud API) to
imperatively have the master run commands on your cluster to get it in the right state.

There's still a lot to learn with kubernetes, but I think I at least have the fundamentals down.  At
this point, a lot of it is figuring out the particulars of the Google Kubernetes Engine.  Speaking
of which...

## Google Kubernetes Engine

My deployment platform turned out to be GKE.  I didn't want to use AWS even though I'm a little more
familiar with AWS because that's what we used at Netflix.  At least with AWS, I understood how their
IAM worked, and had some hands on experience working with some of their APIs (like S3 or route53 for
example).  With AWS I also understood a little more of their networking model, and how they did
VPC's and load balancing.  Nevertheless, I chose not to go with AWS.

For one thing, AWS is pretty expensive. GCP gives you some free tier usage that's pretty nice and
their other base costs seems better than AWS too.  The second reason is that I don't want "feed the
monster".  Amazon is already way too powerful (as if Google isn't...but Amazon is just a beast).
Also, Google, unlike Amazon, has done some things to minimize environmental impact of their data
centers.

I actually originally wanted to use Openshift as my deployment platform, but Red Hat's documentation
forced me to go elsewhere.  It's a shame, since I'm an ex-Red Hatter, I'd like to see Red Hat
succeed, but the lack of documentation was a definite failing.  I couldn't even figure out how to
upload my container to their internal docker registry.  So much for Openshift.

So with GKE, you have to not only understand kubernetes, but how it does things.  For example, the
Ingress or load balancer in GKE is either something you can add yourself like ingress-nginx, or you
can use the internal one provided by GKE.  Then there's other fun things you have to learn, like how
to set up secrets or make sure your load balancer timeout doesn't cut off your connection after
30 seconds (which is the default, and not good for websockets btw).

Then of course there are other details like billing, health checks, and other minutiae that you have
to learn that isn't represented by kubernetes itself.

## Docker

I've known docker for awhile, but this is where I really got comfortable with it, and started to
actually "get it".  I have been building small docker images since my latter Red Hat days, but it
was infrequent, and I didn't really fully grok what was going on.

At Netflix, we used docker a bit more and I got introduced to some more advanced concepts, like how
layers really work, and being able to create new images off old images.

But for khadga, I've done slightly more complicated Dockerfiles, and done the best I could to trim
down the image size and used docker-compose more.  There's still a ton about docker I don't know,
but at least I am on firmer ground now and understand how to mount volumes, tag properly, be wary of
creating too many layers, and even some intricacies around pushing to private repos.

## Rust

My rust has gotten to the point where I can sometimes spot memory problems before the compiler does
:)  For example, sometimes I realize "oh yeah, I need to pass in a reference, otherwise my value is
going to get moved" before the compiler yells at me.  At this point, I haven't actually learned
anything new other than perhaps using Arc's and getting better at Rust's new async.

Arc's are Atomic Reference Counters and are basically Rc's that can be shared across threads.  Why
is that important?  In the backend code, I need shared state across several of the endpoints or
multiple invocations of the same endpoint.  When you spawn a tokio (or std::async) task, this task
will run on the async runtime's Executor.  This Executor might be, and probably is, multi-threaded.
This means that the closure you pass to your async task might be running on a different thread from
where it is being executed.

This implies that your shared state could be shared across threads.  I also got bit by a deadlock.
Rust's _fearless concurrency_ only extends to data consistency, but not to deadlocks or livelocks.
Turns out that in one of my async tasks, I was inadvertently holding onto the Arc's lock from a
cloned copy of the Arc.  At least locking and unlocking in rust is trivial.  All you need to do to
mark the critical section of your code is to nest it inside a block.  Whenever rust sees a block, it
introduces a lifetime or scope.  Once it hits the end brace, the lifetime ends, and drop() is
implicitly called on everything...including any locks.

I've also gotten a bit better at rust's async, though there's still a few rough spots in my
understanding.  But the async/await syntax greatly helps in this understanding.  One area I would
like to improve on is to create my own Stream and Sink types to further cement my understanding.
While reading the docs on tokio, it seems that Streams are a lot like Observables in reactivex with
one big exception.  In reactivex, Observables push data out.  In Streams, data must be polled.  One
of the mysteries of async that I still need to grok in my head is how this polling is actually done.
My loose understanding is that the polling is done by the Executor, but that it is only called when
a wake up function is called.  What invokes _that wakeup function_ is what is still a mystery to me.

Another area of rust that I need to improve on is macros.  I have yet to write a macro and would
like to learn, but I still need to learn how to create them in general.

Otherwise, I feel like I'm slowly graduating from beginner to intermediate rust programmer.  I've
probably got about 5000 lines of rust under my belt now (that includes writing and rewriting code),
and there's roughly over 1000 now in khadga alone (probably 2000 including rewrites/refactors).  I
feel that I am at least qualified to say this now:

Rust is easier than Java.

Ok, why did I say that?  Rust the language is probably a tiny bit harder than Java.  You can be
oblivious about memory in Java...which can actually be a bad thing.  Sadly, many developers in GC'ed
languages think they are immune from memory leaks, but in fact they are not.  Anytime you throw away
the handle to some callback (or anonymous class or lambda for Java) you just created a potential
memory leak.  Ever wonder why javascript has a `removeEventListener` method?  But beyond memory
issues, with Rust you have to learn about pattern matching tha Java doesn't have, and the Trait
and Generic system is more powerful.  Also, in rust, generics aren't type erased.  In fact, rust
will perform monomorphization to your generics and produce actual concrete instances (which can
bloat your binary size if not careful).  Also, Java has no equivalent of enums (or Sum types in
general).  But heck, typescript has a more powerful type system than Java has.

So why did I say Rust is easier than Java?  Because there is more to a programming language than the
language itself.  There's also the build environment and runtime environment to think about.  Ever
had to tweak heap space in the JVM?  Remember PermGen?  And need I say `classpath` or `modulepath`?
All that stuff just goes away with rust.  And should I talk about the nightmare that is the JVM
build system, whether it's gradle or maven (or heaven forbid, ant)?  Cargo is truly a breath of
fresh air compared to any build system I've ever seen for the Java world (I would in fact, compare
it to leiningen for clojure in ease of use...maybe easier since you don't have to deal with
classpath).  And how about delivery mechanisms of your new JVM project?  Oh yeah, I either have to
create a container for you ("hey, here's my 4Gb container for my JVM project"), or give a page
explanation on how to setup a JVM, etc to run your app with gradle run or some such.

For rust, it's "hey, here's my 15Mb binary for linux.  Have fun".  Sure, you have to compile for
each arch/os, but that's still way simpler than just about any gradle or maven project I've ever
seen.  I always said that the hardest thing about C(++) programming wasn't the language, it was
building the damn project.  Ofc, that was back in the bad old days before CMake.  But even
disregarding the arcane incantations of autoconf/automake, or the relative goodness of newer C(++)
build tools like waf, scons, or meson...you can't underestimate the difficulty of getting your
builds right.  In that sense, rust is a hands down winner.

## Webassembly

I've been poking my nose around webassembly for quite awhile now, but khadga is my first real foray
into it.  Webassembly is kind of its own thing and there are many aspects of it to learn.

First, there's just webassembly itself.  I haven't really had to dissect wasm files, but for fun, I
have converted them to wast to look at the s-expression form (since I love lisps).  I honestly think
that webassembly is the future, because it's the best tech we have to do a few things right.  The
most important of these will actually be security.

I've been looking at WASI, but I only made some toy programs to read and write files.  The node
interpreter can now load and run wasm compiled with the wasi triple `wasm32-wasi` (yeah, I know,
it's just a double, but hey, that's what they're called).

For rust, if you want to get into webassembly, then understanding wasm-bindgen is a must.  I pored
over the docs to figure out what you can and cant import and export from javascript to rust, and the
other way around.  I did a fair bit of digging into the web-sys and js-sys crate as well even with
as little code as I have written so far.

The other tool you will need to learn to use for rust + webassembly is called wasm-pack.  This tool
will help you build your rust code into webassembly, and package it up into an npm for you.
Actually, most of the heavy lifting is done by webpack, but wasm-pack will integrate everything for
you.

## WebRTC

I've been going through a fair bit of WebRTC documentation in order to access the Media Devices and
get the media streams.  There's still a lot I need to figure out and implement, but at least I have
a basic understanding.  In fact, my next major task I will work on is the Signaling Service and a
basic STUN server implementation.

## Security

Netflix was really my first introduction to having to think about security.  Whether it was DNS
spoofing, SNI with https servers, JWTs vs http sig, TLS cert rotation, etc etc...that was really the
first time I had to consider security.  All my previous jobs, our servers were firewalled off inside
the internal LAN.  The backend services we built were plain old http, and we relied upon the
security of the "exterior" network to protect our services.

This is of course, dumb.  If an attacker manages to breach or sneak inside (say for example, an
innocent looking npm or pypi package that you need as a dependency), then you will be in a world of
hurt.

With khadga, I am now forced to be the "security expert".  Currently, I'm reading up on JWT's, since
they kind of go hand in hand with OAuth.  Speaking of OAuth, I decided to go with Google's OAuth
rather than have user's register with an email and password.  I might still go back to that as an
extra choice if the user wants.  The reason I got rid of that option was because I discovered that
mongodb has a SSPL license which means that if you use mongod in the cloud, you have to release your
source under the AGPL license.