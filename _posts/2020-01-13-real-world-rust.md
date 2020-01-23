---
layout: post
title:  "Rust, where you have been all my life?"
date:   2020-01-13 18:21:40 -0400
categories: rust ml
---

# Rust, I wish I had known you sooner

Now that I've got some free time, I've been thinking alot about where I am at in my career and what
I would like to do.  I have come to the realization that the way I think and see the world, as well
as how I work is vastly different from the vast majority of people.

This has been both a blessing and a curse.  For example, my curiosity in programming languages has
let me taste and sample quite a few, from lisps, to haskells, to quite a few in between.  But it has
also meant that I have not focused on "real world" skills as much.  For example, while I have done a
little docker, I have only scratched the surface, and my knowledge of css, DOM and other web goodies
is just a tiny morsel.  My big data skills have also been neglected, though to be fair, this is
because the jobs I have had mostly only allow me to enter data, and I have had very little
opportunity to query and search for data.  And while I did low-level embedded programming early in
my career, I have not been able to work on embedded code recently.

These are all important skills to have.  While learning new languages or programming styles broadens
the mind allowing you to think of different ways to solve a problem (think functional vs OOP,
reactive vs proactive, memory sharing vs message passing, or async vs sync), but when it comes down
to it, the rubber has to meet the road somewhere.  I realized how little I actually knew about
networking while at my last job, and how important it is to get things right and for security.

So, there's still this conflict amongst management and engineers (and between engineers) with
regards to what it means to truly be productive and provide business value.

## Productivity is more than just releasing a product

Some people believe that productivity is all that matters.  The ability to "get $%&# done" is the be
all and end all of being a good engineer.  The means in other words, is not as important as the end.
So, at my last job, when I tried to push for rust, I should not have been surprised when it was met
with a lukewarm if not cold reception.

My job seemed to be the poster child for using rust.  We are basically a javascript shop (with one
major java legacy app), and we had to work with IoT gateways and custom embedded hardware.  Unlike
the cloud, you can't scale out software on an IoT device.  And while IoT gateways are much larger
than their IoT sensor devices, they are still basically small platforms with finite resources.  My
worry was that as we added more node services, not only would this slow down our gateway, the
explosion in dependencies would consume storage space.

One critical argument was that the majority of engineers only knew javascript and java and some
python. Also they argued, it is much faster to code in javascript than rust.  I even tried to
champion typescript over javascript, with at least some partial success, but only once another very
important team in a different org switch to typescript.

I mentioned that with webassembly, javascript devs could still consume what had been written in
rust, and with greater speed.  Not to mention that if done in native rust, the binaries would not
just be orders of magnitude faster, but orders of magnitude smaller.  I also showed how easy it was
to distribute the rust binaries, as opposed to telling someone how to set up a private npmrc file
that pointed to an internal npm repository so they could use tools written in nodejs.

Again, the main rubric came back to productivity.  While rust was met with a lukewarm if not cold
reception, go was considered more seriously.  Afterall, it too generated standalone binaries and
though not as fast as rust, was still much faster than javascript.  I argued that go, since it had a
garbage collector, would be at a disadvantage when calling shared C libs since it was too easy for
the GC to think a variable was not in use when it was still being used on the other side of the FFI.
Also, go would not be as good for embedded usage, also due to the GC.  Not to mention that tinygo
was a subset of go.  And finally, unlike rust, where webassembly is not only a first class citizen,
but the primary language of the bytecode alliance, go's support for webassembly is not first class.

There was also quite a bit of gushing for go's "reduced feature set".  The lack of generics, for
example, was seen as a strength, and not a weakness.  The argument went that the reduced feature set
meant there was less cognitive load on the developer to understand what was going on.  One or two
engineers who had looked at rust code were aghast at the lifetime syntax (which admittedly, adds
noise) and they thought the generics looked even worse than Java (despite the `where` clause similar
to haskell and being able to use `impl`).

This seems somewhat backward to me though.  The ability to reason about code is far more than just
syntax or feature set.  The fact that rust puts everything on the table for you seems like more of a
cognitive burden, but that's so that you __can__ understand and reason about it better.  There is no
mystery about when where or how a variable will be cleaned up.  There is no mystery about whether
you have a memory alias or a mutable data value (both of which can happen in go).

So the primary argument I heard to support go was "It's fast and easy to learn, and fast to code in".

## Go slower to go faster

There's a quote in the book ["Scaling Up Excellence"][-scaling] that says that sometimes you need to
slow down in order to go faster.  Both of my parents were nurses, and they saw the health care
system up close and personal. As a result, they were strongly on the side of prevention.  As my mom
used to say; "An ounce of prevention is worth a pound of cure".

I get the feeling that managers, and even many engineers, don't like that.  They want their product,
and they want it yesterday.  I remember at one former company, when I was still fairly new on the
team my manager asked me why I hadn't pushed some code up yet, and I said I hadn't fully tested it
yet.  He said it was ok, I could just push it now.  If we found bugs, all the better, since the
developers were mandated to find a certain percentage of bugs.  I felt like I was in some weird
dream where everyone would start laughing and say it was joke...but it wasn't.

Rust is harder.  It makes you think up front and deal with problems at compile time.  It forces you
to think about your data first and how you will type it.  These problems don't magically go away
with dynamic languages, or languages with garbage collectors.  In fact, all they do is obfuscate the
problem for you when you do run into a problem.  One of the things I liked about haskell and
purescript was that since they were pure FP languages, the type system helped you pinpoint the most
likely places of error (the stateful parts).

Rust, while not pure, actually has a leg up on haskell in a sense though.  It allows for mutation
while still guaranteeing that no data races can occur.  Also unlike haskell, which does not have an
affine type system, rust can guarantee that a variable (and thus memory) can only be accessed once.

The end result however, is that using rust slows you down.  It doesn't feel as productive as say
python or javascript, or even go.  This is not a bad thing!

## My new resolution

Since it's a new year, I decided that I would do a couple of things:

- Only concentrate on rust, typescript and webassembly
- Get better at a NoSQL database
- Get better at web technologies
- Learn machine learning
- Get back to embedded microcontroller IoT devices

In order to fulfill all this, I decided on a programming project that would require all the above.
I decided that I don't need to learn any more new programming languages.  While I could have gotten
better at haskell, when rust came along, I felt that it was a nice blend of pragmatism with type
theory and it somewhat diminished the value of learning haskell.  Plus, I felt that even though rust
was a long shot at being adopted by a company, haskell or purescript would be an even longer shot.
Typescript I will still use because I think that the future of machine learning will start skewing
towards the web, or at least on the edge.  Webassembly I think is what Java and .Net bytecode could
have been.  Even the founder of docker said that if wasm + wasi existed 8 years ago, [he wouldn't
have made docker][-nodocker].  And with the security holes in open source, I think webassembly is
the best shot we have at plugging these holes, even if only partially.

I've always considered my data skills to be my weakest.  I haven't done any serious SQL since 2012,
and even that was not a lot.  Although relational db's are still king, I've always felt that the
only reason they are still popular is because people either are ignorant that all db's must be ACID
or they just don't want to learn a new skill.  Graph databases are natural fits for many domains,
and documents are just natural (though map/reduce style querying seems a bit ugly).  Since mongodb
can store graphs depending on how you index your collection and documents, I thought it might be
good to finally learn the most popular NoSQL database around.

My wanting to get better at web technologies stems from my first real foray into it just 2.5 years
ago or so. I learned a little React, at least enough to help debug and test React components.  This
was my first time having to understand how the DOM worked.  I even wound up writing a small little
web app at Red Hat just for internal testing purposes and thought it was kind of fun, even if my CSS
skills would make GeoCities look like a modern marvel.  Like it or not, the web is here to stay, so
it's a good skill to learn.  It's one of the few technologies that really hasn't changed all that
much over the years (yes, there's SPA's and progressive web apps, and virtual DOM's are all the rage
as a result).  The one real exception to this is webassembly and the rising importance of
WebGL/WebGPU. Even WebRTC is becoming more popular as a means for IoT devices to send real time data
(due to the limits of mqtt packet sizes).

And of course, what engineer nowadays doesn't want to learn Machine Learning?  Artificial
Intelligence is actually why I got into Computer Science in the first place back in the early 2000s.
But unlike most engineers who are working in the real world and not currently in school, I don't
want to "short cut" it.  Many moons ago, I took Linear Algebra, several semesters of Calculus, and
Stochastics (probability).  Like many software engineers, I never had a chance to use any of the
math I learned, so all that knowledge quickly rotted away to make room for other information.

While it is possible to learn tensorflow, keras, pytorch, etc without being good at calculus, linear
algebra or probability, I think that just makes you a technician.  I want to understand _how_ the
machine learning works. I want to understand the mathematics behind it.  As a result, this
resolution will be the most time consuming. I've already started brushing up on my calculus and
linear algebra, to the point where I was like, "oh yeah, I forgot about the chain rule when you do
derivatives".  Simultaneously, I bought a book from Manning on tensorflowjs so that I can learn how
to use it with my web app.

### Real world project

Like any skill, you have to use it to get better at it.  So, given all the above, I decided to write
a web app called [khadga][-khadga].

Khadga is a hindi word meaning "sword".  I was trying to find if the sword that Manjusri carried
(one of the most important Boddhisatvas in Buddhist lore) had a name.  Unfortunately, I couldn't
find a name, so I called it khadga instead.  But why khadga?  Manjusri carried a sword, to cut all
bindings, especially the illusions that tied up mankind.

One common theme I have had in all my jobs at every company is making sense of information that is
written informally.  While information in bug trackers like bugzilla or Jira are useful, very often
the initial and longest conversations happen in email threads or web chats (eg, IRC or slack).
Another very common problem is that many if not most bugs could have been found or even predicted
based on logging information.  Wouldn't it be nice if you could classify and predict text
information?

A second use case was for visual information.  Face detection for images seems to be a pretty common
task now, but I haven't really seen it so much for videos.  Where I live, we have a deer problem.
Those pesky (but cute) deer will often eat something from the garden.  Wouldn't it be neat to
identify, track, and target a deer to hose it down with some water?  Khadga should be able to take
in data, whether textual or visual from any agent, whether that agent is a human signed into the
web, or an IoT device sending data over WebRTC to the backend.  Having a 3-axis controller
(implemented perhaps on 3 separate precise stepper motors) that are in turn controlled from a
microcontroller that gets data from a webcam sensor would be a fun embedded project.

I also decided to use as little typescript as possible in this project so that I could get better at
webassembly and specifically wasm-bindgen.  I also am not using a CSS framework like bootstrap or
bulma, again, because I want to get better at CSS and HTML5, even it if means slowing me down some.

If this all sounds rather ambitious, yes it is.  It's a lot to learn and get better at, but it's
what I want to do. Ultimately, having had some time to reflect on the state of the world today, I
want to work on something that is going to help solve problems, not create more.  Sadly, people
think technology is the solution to our problems when in fact, it is often causing the problem, or
the side effects are worse than the problem it solves.  This is why I am deeply interested in NLP
(Natural Language Processing), because I think we need to better understand not just fake
information, but the real information that that people are writing about and see if we can make some
kind of sense of it all.

Ideally, I could concentrate on just doing the ML part, but all that other work isn't going to get
done by itself. Plus, the challenge is half the fun.

[-scaling]: https://www.amazon.com/Scaling-Up-Excellence-Getting-Settling/dp/0385347022
[-nodocker]: https://twitter.com/solomonstre/status/1111004913222324225
[-khadga]: https://github.com/rarebreed/khadga