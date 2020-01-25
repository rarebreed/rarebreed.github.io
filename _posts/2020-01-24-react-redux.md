---
layout: post
title:  "React and redux, for better or worse"
date:   2020-01-24 17:00:00 -0500
categories: rust react redux
---

# React and Redux

For the [khadga][-khadga] project, I originally wanted to do everything in webassembly, including
all the UI layer stuff.  The web-sys library has functions that allow you to invoke methods on the
DOM like `document.querySelector` or creating node elements.  There is even a new framework called
[seed][-seed] that I recently came across that looked like it would be useful for an "all-in-rust"
type project.

On further reflection though, I decided not to use seed.  While this would have been pretty cool, I
decided to be a little more pragmatic.  I chose to use a fairly standard react+redux for the front
end and relegate the wasm stuff to speeding up parts of the code.  Why did I decide to go down this
route?

## Practicality

For one, I thought react and redux would be good skill sets to use.  My react and redux knowledge
are fairly limited.  I did enough react at Red Hat to figure out how to do unit tests of React
components using the enyzme library.  I created a very small proof-of-concept [web app][-mercury] to
send data in text input forms to a little webservice that in turn sent the message to an ActiveMQ
bus.  This app was bog simple, with 3 different text input boxes and some data validation, and one
of the text boxes was connected to a websocket so it could also receive messages from a subscribed
topic.

Also, that was close to 2 years ago.  At Netflix, I had to troubleshoot a few front end apps, but I
didn't really get to write any new stuff.  Since react and redux are hugely popular, I thought it
wouldn't hurt to use them.  While I thought the [cyclejs][-cycleks] library is more interesting than
react, this leads me to the other reason.

### But why specifically react and redux?

I suppose I could have chosen angular with ngrx or mobx.  Indeed, I seriously considered mobx over
redux.  The reason I picked redux was primarily its popularity.  I like the fact that mobx and ngrx
use Observables under the hood.  Redux is basically just a dispatch + reducer function.  Also, to do
a lot of things, you need to use extra libraries like redux-thunk or redux-saga.  One of the
criticisms I heard about mobx however was it was a little too "black box" which is something I am
not a big fan of.

Another reason for redux and react is just the level of documentation and tooling.  There are tons
and tons of tools and articles, books or other documentation on how to use them.  I've never been a
big fan of using a tool just because it is popular.  That being said, react and redux aren't bad,
and I think by using it, it will help more people understand khadga and how to create your own mixed
typescript and webassembly applications.

I also felt that by doing the front end in react+redux, it would make it easier for others to see
how to slowly update their own existing front end applications.  It's great when you have a brand
new toy to play with from a clean slate, but in the real world, this is a relatively rare event.

Lastly, the fewer new elements that readers or contributors (or myself for that matter) have to
learn, the better.  It's hard enough picking up rust.  Add in writing a web service, front end
frameworks, machine learning with tensorflow, mongodb integration, etc etc, and this becomes a very
time consuming and difficult task.

## The path less traveled

To be honest, a big reason in choosing react and redux is that I have invested too much time in
somewhat esoteric technologies, and I want to get better at something more mainstream.

I've spent most of my career on somewhat obscure technologies.  While I have touched on things like
front end web work (I know what CSS selectors are, how to dynamically add to the DOM, use the dev
tools in the browser to inspect the network and DOM, etc etc), a lot of that was more from a testing
perspective.  The same is true of databases.  I haven't done any serious SQL since 2012, and even
that wasn't very heavy duty or for very long.  I remember writing a few inner joins and elimination
of cross products in queries.  The only ORM I ever used was SQLalchemy to interface some python with
our MySQL database.

Even my interest in fringe computer languages means that a lot of my skill sets are not seen as
pragmatic or usable to some.  I spent probably a good year and a half banging my head against the
wall learning haskell and purescript in my spare time.  While it was definitely a very interesting
experience and it has helped me tremendously.  I think it's why I found rust relatively easy to learn
compared to others who struggle either with the advanced type system, or for those who never wrote
any C++ and don't understand RAII concepts.  Clojure and lisps in general are another example.  It
took me awhile to truly grok lisps, and then I spent about 2 years coding a fair deal in clojure.

When I first started learning about reactive programming, I was like, wow, how come we haven't been
doing this before?  When I first got to Netflix, I thought that because Ben Lesh worked at Netflix
and was the primary maintainer of rxjs, that I would be able to use lots of rxjs (I had been using a
lot of RxJava at Red Hat along with vertx).  Turns out that at least two teams there didn't like it.
They felt it was too complicated and hard to follow.  I was the exact opposite and found the
pipeline of composition of rxjs operators to be very natural.  But because many programmers are not
that strong in FP they found it harder to understand.  So once again, I found myself being one of
the only people in a team who wanted to use a fringe technology.

### Was it worth it?

But, now I am not doing haskell, purescript, or clojure and as a consequence my ability to do them
is rusting away.  But, what I have not lost, is the principles and ideas.

Lisps taught me the idea that there really isn't a separation of code and data.  They are the same
thing, and it made macros this unbelievable thing. The idea of (almost) everything being a function
blew my mind away.  Being introduced to functional programming was the greatest benefit that lisps
taught me, because it forced you to think this way (unlike "FP" style of javascript, java8, etc).
Because of my background in lisp, when I first saw map-reduce, I was like...been there, done that.

Haskell and purescript brought about the concepts of pure functional programming and a very powerful
type system.  I used to love lisps, and thought they were the pinnacle of language design...until I
got frustrated with refactoring code when I changed data types.  I also got frustrated when I didn't
document my code, and couldn't figure out what data I was supposed to pass in to a function, and
what kind of data it was returning (and I wrote a lot of defstructs and defrecords).  When I first
learned about Category Theory, I was like "wow...design patterns that are math".  At Netflix, many
of the guys on my team didn't know javascript that well and some of them struggled with Promises and
async programming in general.  When I first started learning promises, it dawned on me "oh, it's a
Monad that wraps async data".

Reactive solves so many issues, that I am amazed it's not more commonplace.  The idea that state is
not stored but passed along made me see software design in a new light.  Instead of a pull model
where one entity asks for data from another, data is pushed to subscribers.  It's like the
"Hollywood Principle" (don't call us, we'll call you) that many are familiar with dependency
injection, but for state.

So, even if I don't get to use haskell, purescript, clojure or reactive for work, I think they were
the best teachers possible for Functional Programming.  This background also helped me learn rust
fairly quickly.  The only really sad part, is that I often see solutions or ways of doing things
that others either have trouble understanding, or do not understand the value of.

[-mercury]: https://github.com/rarebreed/mercury
[-khadga]: https://github.com/rarebreed/khadga
[-seed]: https://seed-rs.org/