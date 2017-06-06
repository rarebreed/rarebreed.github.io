---
layout: post
title:  "What are Monoids, Functors, Applicatives and Monads good for? (UPDATED: 2016-12-19)"
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
also becomes impure.  The trick is that impure functions can have or contain pure computation or values.  Indeed, this
is what the goal is in pure functional languages: isolating the pure from the impure.

This isolation, or encapsulation if you will, of purity in a sea of impurity gives us some benefits.  It means that we
can prove our pure parts of the function and know that they are always correct.  It is the impure parts where we can
focus our testing efforts due to their non-determinism.

So what does this have to do with Monoids, Functions, Applicatives and Monads?  As I mentioned before, the purity of a
function is indicated by its type.  But how is a Functor impure?  Perhaps we can say that the Maybe type is impure as
it represents the possibility of not having an answer.  But what of a function?  A function is a Functor, but a function
can also be impure.  What about a List?  A List supports the Functor protocol, so how is it "impure"?  These notes are
in some ways an attempt to answer that question.

# Monoids

Technically, Monoids don't encapsulate any notion of impurity.  However, they are important to understanding the utility
and purpose of Applicatives and Monads, so I will cover a bit about them here.

Monoids are essentially a generalization around "smashing" two data types together.  Addition is a canonical example of
an operation on Monoids.  We can for example add numbers together, strings together, or lists together.  Monoids have
2 functions in their type class definition: append and mempty.  Monoids are an interesting usecase for newtypes since
it is possible for one data type to have multiple instances of Monoidal behavior.  Because of this, for a given data
type, it may not be known what the append behavior should do.  An example of this is that for the Number data type, it
is not clear if the append operation is supposed to do summation or multiplication, since both would be applicable.

A key point to make about Monoids is that they have a shape or structure.  The append operation may not preserve this
exact shape.  For example appending two strings creates a larger string.  Contrast this with the map operation of a
Functor which must preserve the shape of the data structure it works with.  Indeed, this is why Monoids are associated
with catamorphisms (_cata_ meaning to break down, as in catabolic, and _morphism_ or pertaining to shape) since with
Monoidal types and folds, you can break or reduce a structure down.

Another important aspect and perhaps its greatest use case is its involvement with folding.  Folding is most commonly
used on data types which are an instance of a Monoid.  This is because a Monoid's append operation can be thought of
as an accumulation, and typically that is what folds do to what they operate on.

# Functors

Functors at their core are a (type)class with which you can map a function over.   Usually when you see tutorials about
functors online, you'll hear them say that Functor is like a box that holds some value.  Problem is that doesn't hold up
that well when you think about 2 major applications for Functors:  IO and functions.

## How Functors aren't always "boxes"

To help think about Functors as a context, rather than as a "box", I'll try to illustrate how functions, which are also
Functors, don't really fit well into that model.

First, let me start off by saying I hadn't really thought of functions as types, but it makes perfect sense.  How else
would you be able to treat functions as first class citizens unless they had a type?  The **->** operator is actually
a type constructor for a function.  You will also see mention of _kinds_ from time to time.  Here are some examples of
different kind types:

{% highlight haskell %}
* -- a fully qualified, ie concrete, type
* -> * -- a function type.  Since a constructor is a function, this can be seen as a Type that takes one parameter
* -> * -> *  -- a type constructor that takes to parameters, etc
# !  -- (purescript only) a row of effect types
# * -- a row of types (ie, a record)
{% endhighlight %}

Since a function is a type (really, it's an element of a morphism in a category), that means that when a function takes
another function as an argument, the passed in function must have the same type required by the calling function.

But what's more interesting in some ways, is that a function _is_ a Functor.  This is where the analogy of Functors as a
box or burrito wears thin.  How is a function like a box or burrito?

**TODO** explain how a function is a functor

## The context of a functor

When you see it explained that functors are a "box" that holds some value, that analogy really only holds in some kinds
of functors.  For example, what is the "box" when you consider that a function type is a Functor?  What is the box when
you consider IO as a functor (is it the outside world)?  The better analogy is that a functor has or rather is a
context.  But what is a "context"?  Let's look at some examples.

Let's examine what a context is.  If you say "It depends on the context", then that means that "it" changes meaning
based on the surrounding environment or information.  In our case with functor types, "it" is our pure value, and the
surrounding environment or information is the functor type.  Moreover, each functor type is suited to encapsulate this
extra information.  For example, the Maybe type represents the context that might have a failure, the Either functor
says that the Left side is an error of some sort, and the Right is a valid value.  Remember that Monads _are_ Functors
as well, so all the above applies to Monads.

For example (+3) returns a value, depending on the parameter it is given.  The functor (Just "foo") may return a new
Just or Nothing, depending on the value.  In other words, the context is 2 things:

- The 2nd argument supplied to map will determine what kind of functor is returned
- The context **is** the type (in the above examples, a function type, and a Maybe type)

Recall the defintion of the map function:

{% highlight haskell %}
map :: (Functor f) => forall a b. (a -> b) -> f a -> f b
{% endhighlight %}

This says that map takes a function that takes some type _a_ and returns a type of _b_ (note that _a_ and _b_ can be of
the same type), and another argument which is a functor of type _a_, and it returns a functor of type _b_).  An
alternative way of seeing this function that might be more useful is to look at it like this:

{% highlight haskell %}
map :: (Functor f) => forall a b. (a -> b) -> (f a -> f b)
{% endhighlight %}

This says that map takes a regular function (not wrapped in a Functor) that takes a type of regular a and returns a
regular b, and returns a function which takes a Functor of a and returns a Functor of b. But why is that useful to think
of map like that?

Think again of what I said earlier that pure functional languages take islands of purity and surround it with a sea of
impurity.  So take a look at that alternative definition again.  It's saying "take a pure function of type a -> b, and
give me a new function of (f a) -> (f b)".  This (f a) -> (f b) sure looks a lot like a -> b doesn't it?  That's
because in many ways, they are the same.  What has changed is that the Functor f holds some extra information, but it
still carries the mapping of a to b.  The magic then is _what is this extra information_?  As explained earlier, the
type (which implements the Functor) _is_ the extra information (or context).

<<<<<<< Updated upstream
## Functions as types

First, let me start off by saying I hadn't really thought of functions as types, but it makes perfect sense.  How else
would you be able to treat functions as first class citizens unless they had a type?  The **->** operator is actually
a type constructor for a function.  You will also see mention of _kinds_ from time to time.  Here are some examples of
different kind types:

{% highlight haskell %}
* -- a fully qualified, ie concrete, type
* -> * -- a function type.  Since a constructor is a function, this can be seen as a Type that takes one parameter
* -> * -> *  -- a type constructor that takes to parameters, etc
# !  -- (purescript only) a row of effect types
# * -- a row of types (ie, a record)
{% endhighlight %}

Since a function is a type (really, it's an element of a morphism in a category), that means that when a function takes
another function as an argument, the passed in function must have the same type required by the calling function.

{% highlight haskell %}
getKey :: k -> Map k v -> Maybe v
getKey k (Map key )
{% endhighlight %}

# Applicatives

Applicatives are a combination of a Functor and a Monoid.  They have the mapping aspect of a Functor with the "smashing"
together aspect of a Monoid.  One difference though is that whereas a Monoid's append can change the shape of a data
type, Applicatives can not.  However, Applicatives do still combine or smush together two other values.

## Applicatives are a special kind of Functor

Because of the way it's defined, the function passed to map has to take a single arg.  But what if we pass a function
that takes more than one argument?

Recall that map takes a function that takes a single argument.  But what if we need to pass in a multi-arg function?
Remember that because purescript is curried, **all** functions are functions of a single argument!  Functions which seem
to take multiple arguments are in fact just returning a new function with the argument from the outermost function fixed
as a constant (ie, a closure).  We can see this like so:

```haskell
multi_arg :: forall a b c d. a -> (b -> (c -> d))
```

Applicatives are useful in the situation where you have a "regular" function (ie a pure function) taking regular value
types.  But what if instead of a pure value, you only have another FAM type (Functor, Applicative, Monad)?  This is
where lifting comes in handy.  You lift the pure function to be applied to "impure" values.  If your pure function only
has a single parameter, then you can simply map it.  But if the pure function takes multiple arguments, then this is
where Applicatives come in handy.  Recall the type definition of apply:

{% highlight haskell %}
apply :: forall f. (Applicative f) => f (a -> b) -> f a -> f b
(<\*>) = apply
{% endhighlight %}

Notice that this definition looks very similar to map, the difference being that instead of taking a regular function
type (a -> b), we have a function wrapped inside the Applicative.  How is that useful?  If you have a function that has
multiple arguments, say for example 2 args like foo :: a -> b -> c, that can also be expressed due to currying as
a -> (b -> c).

# Applicatives Use case

I think the major use of applicatives is that if a function is wrapped inside a Functor, we cant just use that
"wrapped inside a functor" function with map.  That's where Applicatives come into play.  You will often see them used
in combination with map.  This is because Applicatives have a function (of one argument) embedded inside some data type
(where that data type obeys the requirements to be an Applicative)

{% highlight haskell %}
-- map
(+10) <$> [10, 20]  -- [20, 30]
(+) <$> [10] <\*> [10, 20] -- [20, 30]
(+) <$> [10,20] <*> [3,4]
(replicate) <$> [2,4] <*> ["a","b"]
{% endhighlight %}

**TODO**
**Show examples of lifting in a real context**

## Lifting with Functors and Applicatives

You will often come across the term "lifting", such as lifting a function.  What this means is essentially lifting the
pure part of a computation into an impure one.  The "impure" part here meaning a Functor or Applicative of some sort.
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

- Monads are, in one way, an abstraction around the idea of flattening or concatenation
- Monads are one way to model side effects, but side effects aren't monads
- Monads have a relationship with *do* notation.
- There is a difference between computational side effects, and native side effects
- Monads are useful for sequential operations
- Monads allow you to chain or pipeline functions in a way Applicatives can not
  - The arguments which are lifted in an Applicative are independent of each other
  - Consequence: You can parallelize Applicatives with apply, but you can not with Monads

## What monads are not

- impure
- IO
- Only good for imperative Programming
- state
- effects
- A value

Rather, they are like this:

- Monads can take two structures, one pure and one impure, and mush them together
- IO has an instance of Monad (ie, it implements the operations of and obeys the Monad laws) but Monad is not IO
- Because monadic structure can be used for sequencing operations, it is well suited for imperative operations
- State can be implemented as an instance of Monad
- As above
- Monads are a typeclass, not a data value.
  - A data type can be instance of Monad, but a Monad is not a value
  - A monad is also parameterized on a type.  Eg (Maybe String), (Either Error), (StateT String (Either String))
  - Just as an interface in java is not a value, but it can be used polymorphically as a type (not a value)

## Playing with monads

Consider this code:

{% highlight haskell %}
-- map :: forall a b. (Functor f) => (a -> b) -> (f a -> f b)
map (\x -> Just x) (1 .. 5)
-- apply :: forall a b f. (Apply f) => f (a -> b) -> (f a -> f b)
apply [(\x -> x * 2)] [5, 10]
-- bind :: forall a b. (Monad m) => m a -> (a -> m b) -> m b
-- Intuitively, in the first arg, take out the a from the monadic context, and apply it to the 2nd arg.
-- Since the monad is an array (of type a), what we return has to be an array (of type b)
bind (1 .. 5) (\x -> [Just x])
(1 .. 5) >>= (\x -> [Just x])

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

## Useful examples

**TODO**
Show some real examples from the emissary project.  This should probably be its own blog post

## Eff: Carrying information along

**TODO**
Explain how the Eff monads carry information inside them.  Probably in its own blog post

## Monad Transformers

**TODO**
Explain why you need transformers.

Consider the type of bind again:

```haskell
bind :: forall m a b. (Monad m) => m a -> (a -> m b) -> m b
```

The m here says that it is a data type that must be an instance of a Monad.  But more importantly, it has to be the
_same_ monadic type.  In other words, you can't do something like this:

```haskell
-- lookupEnv :: String -> Eff (process :: PROCESS | e) (Maybe String)
bind ["PATH", "HOME"] \k -> lookupEnv k
-- or equivalently
test = do
  k <- ["PATH", "HOME"]
  val <- lookupEnv k
  case val of
    Nothing -> pure ""
    (Just x) -> pure x
```

So, how come I can't do that?  I said that my (m a) is an Array String, but my function returns a totally different
type of Monad.  Perhaps you are thinking you can extract the String from the (Maybe String), perhaps providing a default
if it doesn't exist, and then stuffing the results inside an Array String that is returned

### The continuation monad

A monad transformer takes a monad and transforms it into another monad.  The transformer is parameterized by both a type
and another type constructor.  For example we can have:

```haskell
let Async = StateT String         (Either String)        String
--                 type of state  underlying monad type  Return type
```

The (Either String) monadic type here is the additional monad that we are keeping track of in addition to StateT.  So
this type represents something which can use mutable state and return with a failure.

To use the outer type (the StateT type in this case), you can use do notation and bind as you would normally.  But to
access the inner type, you have to use the lift command:

```haskell
-- Takes a monad type constructor and lifts it to a monad transformer
lift :: forall m a. Monad m => m a            -> t             m a
--                            (Either String)    StateT String (Either String)
```

So what is t?  T is the monad transformer.  Recall StateT above.  A monad transformer takes a type, a monad, and a
return type.  We have the monad in (m a).  So that means that t must be a transformer with the first type.  From the
example above, t would be a StateT String (m a), where m a in this case was (Either String).

## The Writer Monad

There are several commonly used monads and the Writer monad is one of them.  The canonical example here is when you need
to log or save off data in addition to your main computation.  The extra context is the logging info or saved off data.

The Writer takes 2 args:  The type of the extra information, and the normal return type.  For example, suppose your
original pure function was like this Number -> Number.  So if you wanted to add logging information, you would create
something like Number -> Number -> Writer String Number
