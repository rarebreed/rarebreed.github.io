---
layout: post
title:  "What are Functors, Applicatives and Monads good for?"
date:   2016-05-15 02:19:40 -0400
categories: purescript notes
---
I think I finally understand why haskell has Functors, Applicatives and Monads.  The a-ha moment came to me when I
realized that pure functional languages don't try to forbid impurity, they just make it very clear which functions are
pure, and which are not.  In fact, not just which functions are (im)pure, but which part(s) of the function are.  It is
not possible to have a useful program without side effects of some sort.  So what a pure functional language like
haskell or purescript does is tell you which parts of the program are pure or not.

This has several implications.  The first is that being pure or not is declared as part of its type.  In other words, by
looking at the type, you can tell if it is pure or not.  This means that we know how to compose functions together due
to their type.  Another consequence of this is that impurity is "contagious".  Any function calling an impure function
also becomes impure.  The trick is that impure functions can have or contain pure functions.  Indeed, this is what
the goal is in pure functional languages: isolating the pure from the impure.  

This isolation, or encapsulation if you will, of purity in a sea of impurity gives us some benefits.  It means that we
can prove our pure parts of the function and know that they are always correct.  It is the impure parts where we can
focus our testing efforts due to their non-determinism.

So what does this have to do with Functions, Applicatives and Monads (FAM)?  As I mentioned before, the purity of a
function is indicated by its type.  But how is a Functor impure?  Perhaps we can say that the Maybe type is impure as
it represents the possibility of not having an answer.  But what a function?  A function is a Functor, but a function
can also be impure.  What about a List?  A List supports the Functor protocol, so how is it "impure"?  These notes are
in some ways an attempt to answer that question.

# Functors

Functors at their core are a (type)class with which you can map a function over.   Usually when you see tutorials about
functors online, you'll hear them say that Functor is like a box that holds some value.  Problem is that doesn't hold up
that well when you think about 2 major applications for Functors:  IO and functions.

## The context of a functor

When you see it explained that functors are a "box" that holds some value, that analogy really only holds in some kinds
of functors.  For example, what is the "box" when you consider that a function type is a Functor?  What is the box when
you consider IO as a functor (is it the outside world)?  The better analogy is that a functor has or rather needs a
context.  But what is a "context"?  Let's look at some examples.

Let's examine what a context is.  If you say "It depends on the context", then that means that "it" changes meaning
based on the surrounding environment or information.  In our case with monads, "it" is our pure value, and the
surrounding environment or information is the monadic type.  Moreover, each monadic type is suited to encapsulate this
extra information.  For example, the Maybe type represents the context that might have a failure, the Either

For example (+3) returns a value, depending on the parameter it is given.  The functor (Just "foo") may return a new
Just or Nothing, depending on the value.  In other words, the context is 2 things:

- The 2nd argument supplied to fmap will determine what kind of functor is returned
- The context **is** the type (in the above example, a function type, and a Maybe type)

Recall the defintion of a map:

{% highlight haskell %}
map :: (Functor f) => forall a b. (a -> b) -> f a -> f b
{% endhighlight %}

This says that map takes a function that takes some type _a_ and returns a type of _b_ (note that _a_ and _b_ can be of
the same type), and another argument which is a functor of type _a_, and it returns a functor of type _b_).  An
alternative way of seeing this function that might be more useful is to look at it like this:

{% highlight haskell %}
fmap :: (Functor f) => forall a b. (a -> b) -> (f a -> f b)
{% endhighlight %}

This says that fmap takes a regular function (not wrapped in a Functor) that takes a type of regular a and returns a
regular b, and returns a function which takes a Functor of a and returns a Functor of b. But why is that useful to think
of map like that?  

Think again of what I said earlier that pure functional languages take islands of purity and surround it with a sea of
impurity.  So take a look at that alternative definition again.  It's saying "take a pure function of type a -> b, and
give me a new function of (f a) -> (f b)".  This (f a) -> (f b) sure looks a lot like a -> b doesn't it?  That's
because in many ways, they are the same.  What has changed is that the Functor f holds some extra information, but it
still carries the mapping of a to b.  The magic then is _what is this extra information_?  As explained earlier, the
type (which implements the Functor) _is_ the extra information (or context).

## Applicatives are a special kind of Functor

Because of the way it's defined, the function passed to map has to take a single arg.  But what if we pass a function
that takes more than one argument?  Or a partially applied function?

Recall that map takes a function that takes a single argument.  But what if we need to pass in a multi-arg function?
The easiest solution is to pass in a partially applied function.  But an even more useful trick is available if we
expand our definition of Functor by creating a new type class called an Applicative.  

Imagine that we have a function:

**TODO**
*Show how to lift a pure function which normally only takes pure values into a function which can take Applicatives.*

```haskell
type Company = String
type Employee = String
type Roster = Map Employee Company

setEmployee :: Employee -> Company -> Roster -> Roster

```

## Functions as types

First, let me start off by saying I hadn't really thought of functions as types, but it makes perfect sense.  How else
would you be able to treat functions as first class citizens unless they had a type?  The **->** operator is actually
a type constructor for a function.  You will also see mention of _kinds_ from time to time.  Here are some examples of
different kind types:

{% highlight haskell %}
* -- a fully qualified, ie concrete, type
* -> * -- a function type.  Since a constructor is a function, this can be seen as a Type that takes one parameter
* -> * -> *  -- a type constructor that takes to parameters, etc
\# !  -- (purescript only) a row of effect types
\# * -- a row of types (ie, a record)
{% endhighlight %}

Since a function is a type (really, it's an element of a morphism in a category), that means that when a function takes
another function as an argument, the passed in function must have the same type required by the calling function.

{% highlight haskell %}
getKey :: k -> Map k v -> Maybe v
getKey k (Map key )
{% endhighlight %}


## Why are functors useful?

So why bother with Functors?  If you think about it, when we first encountered the map function, it was only useful
when we used it with a list:

```haskell
map (\x -> x * 2) [1 .. 5]
```


# Applicatives

I think the major part with applicatives is that if a function is wrapped inside a Functor, we cant just use that
"wrapped inside a functor" function with fmap.  That's where Applicatives come into play

{% highlight haskell %}
(+) <$> [10,20] <*> [3,4]
(replicate) <$> [2,4] <*> ["a","b"]
{% endhighlight %}

## Lifting with Functors and Applicatives

You will often come across the term "lifting", such as lifting a function.  What this means is essentially lifting the
pure part of a computation into an impure one.  The "impure" part here meaning a Functor or Applicative of some sort.  
Look at the type of map in Functor again.  If you look at the alternative definition of map, it takes a regular function
of a -> b, and returns a new function of f a -> f b.  The original function's argument a and return type b, have been
lifted into a new type.  We have gone from a pure computation of a -> b, to a function of a different kind f a -> f b.

But why are Functors and its ilk impure?  Impurity here differs depending on the context, and the context is actually
just a type.  For example, the Maybe Functor means that there may not be an answer (this is common for example if you
have a partial function where a given input does not have a matching mapped to value).  An Array can be seen as "impure"
in the sense that you have non-determinism (instead of a 1-to-1 mapping, you have a 1 to many mapping).

But back to lifting, what does it have to do with purity?  Purely functional languages like haskell and purescript do
not try to eliminate all impurity or side effects.  Rather these languages clearly mark which parts of the computation
are pure from which parts are impure.  The advantage to this is that it becomes easier to find out where problems in the
code are.  If you know from a given unit test on a pure function that every possible choice in the domain is valid, then
if the program fails, the failure must have occurred in an impure part of the program.

By lifting pure parts of the computation into a larger context that is impure (for example with side effects like IO or
mutated data structures), we can still clearly delineate these pure and impure parts.  Let's look at some examples

{% highlight haskell %}
map (_ + 3) (Just 10)  -- Just 13
x 10 -- [20, 40, 60]
{% endhighlight %}

# Monads

Now we get to Monads!  Some quick notes:

- Monads are an abstraction (really a typeclass) for side effects
- Monads have a relationship with *do* notation.  
- There is a difference between computational side effects, and native side effects
- Monads are useful for sequential operations
- Monads allow you to chain or pipeline functions in a way Applicatives can not
  - The arguments which are lifted in an Applicative are independent of each other
  - Consequence: You can parallelize Applicatives with apply, but you can not with Monads

Consider this code:

{% highlight haskell %}
-- map :: forall a b. (Functor f) => (a -> b) -> f a -> fb  is equivalent to
-- map :: forall a b. (Functor f) => (a -> b) -> (f a -> f b)
map (\x -> Just x) (1 .. 5)
-- apply :: forall a b f. (Apply f) => f (a -> b) -> f a -> f b     is equivalent to
-- apply :: forall a b f. (Apply f) => f (a -> b) -> (f a -> f b)   
apply [(\x -> x * 2)] [5, 10]
-- bind :: forall a b. (Monad m) => m a -> (a -> m b) -> m b
-- Intuitively, in the first arg, take out the a from the monadic context, and apply it to the 2nd arg.
bind (1 .. 5) (\x -> [Just x]) -- Since the monad is an array (of type a), what we return has to be an array (of type b)
{% endhighlight %}

So, how come we can chain together Monads, but not Applicatives?  Because of the types.  As you can see, a Monad must
support the bind operation.  If you look at the type of bind, the return type is (or rather can be) of the same time
as the first argument to bind.

Monads allow lifting of values which are dependent in some way, but Applicatives can not do this.  Applicatives do not
(and can not) impose ordering on the computations.  Applicatives are thus useful for parallelism.  Note that Monads are
not required to perform computations in order in haskell.  Since purescript is single threaded, the distinction is not
really noticeable but in haskell it is possible for the ordering of statements in a do block to be performed out of
order unless one specifically designs the monad to do so.  In other words, it is possible for monads to be commutative:
a + b = b + a.

**TODO**
_why is this so?  Explain with some examples how Applicatives have no dependency on arguments but monads can_

## Relationship with do notation

Let's examine the code example given in the book:

{% highlight haskell %}
import Prelude
import Data.Array

countThrows :: Int -> Array (Array Int)
countThrows n = do
  x <- 1 .. 6
  y <- 1 .. 6
  if x + y == n
    then return [x, y]
    else empty

-- The above is equivalent to this:
countThrows' :: Int -> Array (Array Int)
countThrows' n =
  (1 .. 6) >>= \x ->
  (1 .. 6) >>= (\y -> if x + y == n
                          then return [x,y]
                          else [])
{% endhighlight %}