---
layout: post
title:  "My foray into purescript: Part 1"
date:   2016-05-13 20:23:40 -0400
categories: purescript nodejs javascript
---
This blog will be a running tutorial/notes on [purescript][-purescript].  This will be quite an endeavor since

- I don't know [javascript][-mdn-javascript]
- I don't know the javascript ecosystem
- I am still learning [haskell][-lyah]

I've already had to figure out a few things the hard way while reading through the [purescript book][-ps-book] and
working through some examples, so this blog will try to keep up with notes during my learning process.

This will not be a "zero to hero" set of blogs.  Rather this will just be some notes to help me practice and make
sense of purescript.  It took me a long time to grok clojure, and I think it will take me even longer to grok purescript
and this series of blogs will help me and maybe others.  Perhaps in the future, I'll write a book to fill in some gaps
especially for those who are new to javascript and functional programming.

## My motivation to learn purescript

Although haskell is really cool and I want to use it eventually, the reality is that writing haskell code at work will
be a very hard sell.  [Frege][-frege] might alleviate this to some degree since our team is a java team, but purescript
does have a couple of advantages.

Firstly, thanks to [node][-node], I can use purescript/javascript where java has a hard time: system level tasks.  Java
has just never been good at system tasks, whether that be creating a child process, or accessing C level libraries.
With node, it will be easier to do system tasks.

Secondly, java and especially [clojure][-clojure] have really bad start up times.  Maybe Java 9 will improve the story
for Java but for clojure, it won't help.  Clojure wont have knowledge about modules and var load up time is still bad
since [1.8 direct-linking][-direct-linking] didn't seem to help much.  On my laptop, [boot][-boot] repl takes about 7
seconds to get to the repl, and [lein][-lein] repl takes about 8 seconds.  That's just unacceptable when you need to
write script-like utilities.  Javascript running on node will have no problem here.  And in fact, node is even faster
than python as it's on par with [pypy][-pypy] speed.  Related to this is memory consumption. A minimal java application
will use about half a gig of memory even if you set -Xmx=256.  That's because once you add in [metaspace][-metaspace]
and [stack space][-ss] (per thread), the memory usage will balloon up quite a bit.  Javascript should take a lot less.

Thirdly, and related to the first point, we need a js binding for dbus.  Although a [Java library exists][-jdbus] for,
dbus, it is unmaintained and relatively complicated.  If anything can rival Java in terms of available libraries, it's
javascript and python.  In this specific case, the [javascript dbus library][-dbus-native] just seems to work (even if
the documentation is somewhat lacking). Since our new project will be testing a [web based application][-cockpit], it
also just makes sense to have it written in something that compiles to javascript.  If anything is more ubiquitous than
java, it's javascript.

Fourthly, purescript is pure.  Not even clojure(script) is as functional.  This means we have stronger safety guarantees
and can refactor code more easily.  Just as clojure expanded how I think about programming through macros and immutable
data, haskell/purescript makes me consider programming even more deeply by being forced to think about types and type
classes, the algebras of typeclasses, and being forced to think about pure vs impure functions.  Why is it important to
be functionally pure?  During my work on [pheidippides][-phei] (written in clojure), I ran into a lot of problems during
refactoring.  I would change some part of the code and then everything would fall apart, sometimes at runtime. I also
ran into null/nil hell.  Clojure does nothing to protect you from passing in wrong types including null values.

One may wonder why I didn't consider [clojurescript][-cljscript] as a solution.  From my very preliminary investigation
so far, I like purescript more for a couple of reasons:

- It's easier to setup and get running than clojurescript
  - boot was made specifically to solve building clojurescript
  - pulp and psci seem easier so far
- The javascript it generates makes more sense
  - purescript doesn't require the google closure library and it's human readable javascript
- purescript is more functional than clojurescript
  - have to separate pure from impure code
- purescript is strongly typed
  - types help you understand the code better
  - Maybe solves null hell

clojurescript does have 3 things going for it though:

- It's more stable than purescript
- It's easier to learn and do
- the FFI with javascript is easier

As for the stability part, all languages evolve.  Purescript just evolves more rapidly, so I am not terribly concerned
with his weakness.  Since I already know clojure well, learning clojurescript isnt that much of a stretch.  Also, since
it is a dynamic language, you dont have to (initially) think about types.  But the problem is that ultimately, you do
have to think about types in any programming you do.  Dynamic languages just makee this concern implicit and in some
cases deferred.  That said, purescript **is** a hard language.  But learning it pays off in spades, because it forces
you to think in abstract terms.  It also forces you to consider what is pure and impure, and as a result, makes you
want to make pure functions whenever you can.  While this disadvantage is more serious, I think it also comes with
benefits that clojure(script) can't provide, and is therefore worth the steep learning curve.  Finally, doing FFI with
purescript is a bit harder.  You have to write "glue code" that gets exported into your purescript module.  Ths is
necessary to manually determine pure and impure foreign functions or data.  Since clojurescript is not a pure language
it does not have to manually consider what is pure or not and dos not need to do this.  Again, this "disadvantage" is
debatable.

I don't know enough about purescript's [continuation monad][-cont-monad] to compare with clojure's [core.async][-async].
However, it should be a cleaner way to handle callback hell than regular javascript (though ES6 itself now has several
solutions to callback hell including generators and promises)

One may also ask why not chose [elm][-elm]?  It has a haskell-like syntax as well.  I mainly focused on purescript
because elm seems to be focused on GUI apps with its flavor of FRP.  Also, elm does not support typeclasses which seems
like a huge minus to me.

Another good reason to chose purescript is that it's not terribly hard to sell javascript to a company.  Sure, the real
language is a haskell dialect, but it ultimately compiles to human readable javascript.  Java has gotten some stiff
competition from javascript, and if trends hold up, javascript will eventually overtake Java as the dominant language
(especially if Oracle starts threatening API infringement or showing a lack of interest in Java).  Being able to write
code that javascript developers can interop with is useful.  Many programmers know javascript, but not that many know
clojure (ironically, our team knows clojure but not javascript).  If a manager frets about using an "unpopular" language
like purescript can be assuaged by telling them that even if the developer(s) leave, a javascript dev can just take the
compiled code and run with it.

Lastly, purescript actually kind of has an advantage over haskell in one sense.  Haskell is first and foremost a
research language.  That means that stability is not the primary concern of the developers.  In fact there's a sort of
motto for haskell "Avoid success at all costs".  That may seem counterintuitive, but basically some languages like java
become victims of their own success and enhancements come at a glacial pace.  But one advantage of being wildly popular
is that fixes to libraries comes more rapidly or have more eyes looking at it (for example TLS/SSL support)

Convincing my colleagues to switch to a new language is probably unlikely.  I hope by reading this blog post I can
persuade them that clojure, as nice as it is, has competition :)  Perhaps others who are starting to feel the pain of
dynamic languages will also take a look into purescript/haskell as alternatives

## Clojure as the gateway drug to haskell

Don't get me wrong, clojure is a good language.  But since it is dynamically typed, it shares the common weakness that
dynamically typed languages all have:  they dont scale out very well.  And if you think [core.typed][-cljtype] is a
solution, it has many limitations.  If you don't believe me, check out these other blogs:

- <http://martintrojer.github.io/beyond-clojure/2016/04/19/beyond-clojure-prelude>
- <https://www.fpcomplete.com/blog/2015/05/haskell-at-front-row>
- <https://adambard.com/blog/core-typed-vs-haskell/>

Where I think clojure is good though is as a stepping stone to haskell/purescript.  If you're coming from the imperative
world, clojure will be a bumpy ride, but not as bumpy (I think) as if you came straight to haskell.  The reason is that
clojure still has some impure escape hatches.  Now that I am learning haskell, I see that the do macro in clojure is a
poor man's do block in haskell.  The syntax is not the hard part about clojure, in fact, it's one of the coolest parts
of the language and helps make macros possible.  In fact, I wish haskell syntax looked like a lisp and more like the
lambda calculus it's based on.  Clojure will also force you into thinking about how to deal with immutable data, which
for me, was the hardest part about becoming fluent in clojure.

Clojure is good for small scale; maybe a thousand lines of code scattered across a few files.  When you start getting
into the tens of thousands of lines of code or more, it takes a lot of discipline to make a dynamically typed language
work.

I know strongly typed vs. dynamic brings out religious zealotry in many, but I'm just a typed language kind of guy.
The only valid argument that dynamic languages proponents legitimately can claim as a win is that there are some valid
untyped programs that can't be expressed in typed language.  However, typed languages win in virtually any other
category

- robustness
- finding more errors at compile time than run time
- better static analysis
- easier refactoring
- code is easier to understand

Perhaps the first and last point dynamic proponents will claim are false, but I think all the rest even they must
concede.  I will argue that typed languages are more robust (especially a language like haskell) which can get rid of
the billion dollar mistake that is null.  I will also claim that typed code is easier to understand with a simple test.
Take your average python code base and look at a function.  What are the types of the parameters that you are supposed
to pass in?  Docstrings you say?  Are you sure those are up to date?  And there's no guarantee a docstring will exists
for a function.

I will don my flame resistant firesuit and make the claim that people who prefer dynamic languages simply are too lazy
or in a rush to type things out.  Even when I was writing clojure code, I found that I often started putting things
into defrecords rather than a map.  The conventional wisdom is that you use maps unless you need performance or you
have to require that a key is inside a map.  But that's the problem.  What happens when a key doesn't exist in the map?
Now you have to do defensive checking in your code to make sure you DO have the key.  So any time you spend not making
it into a proper type gets lost on the defensive coding and adding {:pre [] :post []} contract functions.

What I am discovering about haskell's type system is why they call it Algebraic Data Types.  If you studied Abstract
Algebra or Modern Algebra in college, you'll start to see why.  For example, purescript has Semigroups and Rings.  A
SemiGroup for example must support the append operation.  Why is this important?  What good does Type Theory bring to
programming?  I just want to code!!  The reason you should learn these things is because they help you reason about
your program.  They help you understand which parts of your code are composeable with other, and how to make them
composeable with each other.  Composing functions is really **the** hallmark of functional programming the more I think
about it.  Immutable data, functions as first class citizens, isolating impure from pure code, all of these things are
really about being able to compose functions together.

Hopefully you read the blog post from Adam Bard above, and saw that haskell code is more concise and succinct than a
core.typed annotated clojure code.  In fact, I'd say it's as concise as unannotated code.

## Dipping the toes into purescript

So now that I've gotten out of the way why I want to use purescript, let's start taking some notes!!

# Getting purescript and dependencies

I had a problem trying to install purescript using **sudo npm install -g purescript** due to some problem with a flag.
So instead, I built purescript using stack.  Of course, that means you will need to [install stack][-stack] on your
system

```bash
stack install purescript --nightly
```

This will install several tools for you including the compilter, the interpreter, and tools to help your editor.  You
also need to install a couple of other tools.  First, install node on your system.  I use Fedora 23, and it didn't have
the latest 6.x release of node yet.  I recommend that you **not** use the yum repo to install node.  So instead go to
the https://nodejs.org/en/site and download the latest binary or msi.  If on linux, extract it and put the path to bin
in your $PATH.  

This will give you node, the javascript interpreter, and npm the node package manager.  A quick word on npm, when you
install something via npm, it will install the module plus dependencies in a folder called node_modules in the directory
where you ran the command.  That basically means that you  have a local "installation" of the packages.  You can also
install globally via the -g flag and that will install the packages to a well defined location.

Once that is done, you can install your other purescript dependencies:

```bash
sudo npm install -g pulp bower
```

Pulp is the main build tool for purescript.  Generate a project like this

```bash
mkdir my-new-project
cd my-new-project
pulp init
```

This will create a directory structure for pulp to work with.  I'm still trying to figure out the best layout hierarchy
for purescript code.

# Editor for purescript

I've been using [atom][-atom] with the language-purescript and ide-purescript plugins.  It doesn't seem to have an easy
way to refactor however.   It also seems to be a decent haskell editor as well.

# Purescript and Javascript types

Since purescript compiles to javascript, it's good to understand what primitive types purescript maps to.

- Strings: a javascript string
- Number: A non Integer type (a float for example)
- Boolean: a javascript boolean
- Record: maps to a javascript object
- Array: a javascript array with the constraint that all elements are of the same type
- Int: a javascript integer with the restriction that it only holds 32bits (there's a 53bit integer)
- function: purescript functions are themselves types, and they map to a javascript function

# How (native) modules work in purescript

Even though purescript uses npm and has wrappers for several node modules, purescript uses slightly different tools

- [pulp][-pulp]: the build tool that wraps around psc
- [bower][-bower]: uses bower for packagement management instead of npm

When a purescript module is compiled, it compiles down to a javascript file which in turn can be generated as a
CommonJS (eg node style) package.

# The javascript package/module mess

I'm still learning about how javascript actually finds and loads modules.  There seems to be several competing styles
of creating modules since pre ECMAScript 2015 doesn't standardize on a module system.  So the prevailing kinds of
modules are:

1. [CommonJS][-common-js]
2. [AMD][-amd]
3. [ES6][-es6]

A good explanation for the differences can be found on the [webpack][-webpack] and [requirejs][-requirejs].

Worse, not only are the above different formats for javascript packages, there's also a plethora of tools for loading
packages:

- [webpack][-webpack]: dependency manager for more than javascript (focused on browser-side projects)
- [gulp][-gulp]
- [npm][-npm]: the node package manager that uses CommonJS style modules
- bower: manages more than javascript dependencies (eg static assets)


# The two kinds of javascript programs

Thanks to node, javascript isn't relegated to just browser programming.  Javascript can now be in the same position
that was mostly relegated to languages like bash, python or ruby.  However, there are some fundamental differences
between programming for the browser, and programming for the local machine.

- Node runs in a single thread
  - Need to use async code for concurrency (callbacks, promises, or generators)
  - Can use cluster for parallelism (or multiple node processes talking to each other via IPC)
- Web code also runs in a single thread but
  - Like node, can use callbacks, promises or generators (both ES6) for concurrency
  - Can use Web Worker API to make the browser run script in a new thread for parallelism
- How packages are found and loaded seems to be different

# Tooling for purescript

Purescript lives in a more opinionated world.  For dependency management, it uses bower, and it generates CommonJS style
packages.  Because of this, for purescript packages use

```
    bower install purescript-package --save
```  

Instead of npm install.  However, that doesn't mean you can't use npm to download packages, but it means you'll have to
do FFI and perhaps load/require the code differently.

# Differences with haskell

There's already a good write up of the [differences between purescript and haskell][-diff] on the purescript wiki, but
here's some of my own encounters.

- No syntactic sugar for lists
- No syntactic sugar for tuples (and tuples are pairs only...no 3+ element tuples)
- purescript does all evaluation eagerly
- You have to use an explicit forall x constraint if your function takes types
- When declaring a class, the fat arrow is reversed if you have a type constraint
- When defining an instance of a class, you have to give the instance a name

# No syntax sugar for lists

In haskell, it's pretty common to do something like this

{% highlight haskell %}
headNtail :: [a] -> (Maybe a, Maybe [a])
myhead [] = (Nothing, Nothing)
myhead (h:[]) = (Just h, Nothing)  
myhead (h:t) = (Just h, Just t)
{% endhighlight %}

This example will not work in purescript however.  For starters, there's no syntax sugar for lists.  Secondly,
what looks like a list (from a haskell perspective) is actually a javascript array.  So you would have to do something
like this

{% highlight haskell %}
import Prelude
import Data.Maybe
import Data.List
import Data.Tuple

-- There's also no syntax sugar for tuples, and purescript Tuple is only an ordered pair (no 3+ element Tuples)
-- This is also an example of pattern matching on a List
headNtail :: forall a. List a -> Tuple (Maybe a) (Maybe (List a))
headNtail Nil = Tuple Nothing Nothing
headNtail (Cons h Nil) = Tuple (Just h) Nothing
headNtail (Cons h t) = Tuple (Just h) (Just t)
{% endhighlight %}

# Imports, imports and more Imports

It seems like purescript doesn't include much by default even in its Prelude.  Also, it's a little weird trying to
find where for example Array or Maybe definitions are.  So here's what I've been doing so far.

If you know you want an Array or Maybe, look in the [pursuit][-pursuit] page for a purescript-x package (where x is
something like purescript-lists for List or purescript-maybe for Maybe).  If you find it, look in their documentation
and see what the module name is.  For example, if you want to use the Maybe type, you need to install purescript-maybe
and import the Data.Maybe module

# Record syntax vs. Record vs. Javascript object

{% highlight haskell %}
-- This is a Record type
data Person = {name :: String, age :: Int, company :: String}

-- This is an alias for a Record type
type OtherPerson = {name :: String, age :: Int, company :: String}

-- This is a literal for a Record instance (which under the covers is a javascript object)
let p = {name: "Sean", age: 44}
{% endhighlight %}

# Functions in purescript

The functions in purescript are pretty much what they look like in haskell

# Recursion tricks

{% highlight haskell %}
fact :: Int -> Int
fact 0 = 1
fact n = n * fact (n - 1)
{% endhighlight %}

While the above code will work, it will also blow the stack because the recursion is not occurring in the tail call
position.  So one way to solve this is to have an accumulator

{% highlight haskell %}
fact :: Int -> Int
fact n = fact' n n
  where
  fact' acc 0 = acc
  fact' acc n = fact' (acc * n) (n - 1)
{% endhighlight %}

What is the above doing?  It's making a helper function fact' that uses an accumulator.  We call it like we normally
think of when doing a factorial (Eg fact 5), but by using the helper fact' functions we can make it tail call recursive.

## Things I still need to investigate

1. How to debug javascript if it's not in the browser (ie client side code)
2. How to debug purescript
   - Remember, you cant add a log or print function in purescript as that will render it impure
3. Need to figure out Applicatives and Monads
4. How to do FFI since I will be doing a lot of stuff client side.
5. Websockets (especially client API) in purescript


[-purescript]: http://purescript.org
[-ps-book]: https://leanpub.com/purescript/read
[-mdn-javascript]: https://developer.mozilla.org/en-US/docs/Web/JavaScript
[-lyah]: http://learnyouahaskell.com/chapters
[-clojure]: http://clojure.org
[-node]: https://nodejs.org/en/
[-frege]: https://github.com/Frege/frege
[-jdbus]: https://dbus.freedesktop.org/doc/dbus-java/
[-dbus-native]: https://github.com/sidorares/node-dbus
[-phei]: https://github.com/rarebreed/pheidippides
[-boot]: http://boot-clj.com
[-lein]: http://leiningen.org
[-pypy]: http://pypy.org
[-metaspace]: https://dzone.com/articles/java-8-permgen-metaspace
[-ss]: http://crunchify.com/jvm-tuning-heapsize-stacksize-garbage-collection-fundamental/
[-common-js]: http://www.commonjs.org
[-npm]: https://docs.npmjs.com
[-amd]: https://github.com/amdjs/amdjs-api
[-es6]: http://exploringjs.com/es6/ch_modules.html
[-webpack]: http://webpack.github.io/docs/
[-requirejs]: http://requirejs.org
[-gulp]: http://gulpjs.com
[-pulp]: https://github.com/bodil/pulp
[-bower]: http://bower.io
[-elm]: http://elm-lang.org
[-cont-monad]: https://leanpub.com/purescript/read#leanpub-auto-callback-hell
[-async]: https://github.com/clojure/core.async
[-cljscript]: https://github.com/clojure/clojurescript/wiki
[-cljtype]: https://github.com/clojure/core.typed
[-direct-linking]: http://clojure.org/reference/compilation#directlinking
[-stack]: http://docs.haskellstack.org/en/stable/README/
[-atom]: http://atom.io
[-diff]: https://github.com/purescript/purescript/wiki/Differences-from-Haskell
[-cockpit]: http://cockpit-project.org
