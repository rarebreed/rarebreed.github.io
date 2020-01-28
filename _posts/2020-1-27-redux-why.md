---
layout: post
title:  "Why redux?"
date:   2020-01-27 17:30:00 -0500
categories: rust react redux
---

# Maybe I should have used mobx

For khadga, this is the first time I used redux in an app, and I am starting to question why it is
so popular.  Having used rxjs quite a bit, as well as RxJava, I'm really questioning how and why
redux became so popular.  Is rxjs that much harder for people to learn?

Maybe my background in FP helped me understand reactive and redux pretty easily.  But redux, while
it is functional in nature and design, has some pretty hefty disadvantages compared to Observables
in my opinion.  Here, I'll talk about the basics of redux and why it's a pain to use with react.

## Redux in a nutshell

Conceptually, redux isn't that terrible.  You have Action Creators, Actions, a dispatcher, Reducers
and a data store.  An Action is a data type that must have a field named `type`, and then an
arbitrary amount of data.  The Action is the new data that you want to somehow transform the
existing data to.  A reducer is a function that takes 2 arguments: the first argument is the
current data in the redux data store (all of it!), and the 2nd argument is an Action.  The reason
they are called reducers is because they act like the function that is passed into
Array.prototype.reduce function.  The data store is a unified singleton of all the data your
application cares about.  Finally, the dispatch is a function that takes an action and passes it to
a chain of reducers.

Basically, it's FP 101, but for Javascript.  To really grok what reducers do, you need to understand
what the reduce function does.  What a reduce (or in other FP languages they are often called
`fold`, `foldl`, `foldr` or `accum`) does is to walk through some kind of data that can be iterated
over and you pass each element in this iterable to a higher order function.  This higher order
function takes an initial set of data and some new piece of data, and then it performs some
operation to "merge" the new data with the accumulated data and finally it returns this new
accumulated piece of data.  The reduce then repeats this by passing in the new accumulated data and
the next iterable item and keeps repeating this process until there are no more elements in the
iterable.

The canonical example you see with reduce, which I hate, is summing up a series of numbers.

```javascript
let scores = [5, 7, 3, 10, 8]
let total = scores.reduce((total, next) => {
	acc += n
	return acc
}, 0);

console.log(total)  // 33
```

The reason I don't like this example, is it doesn't really show you the power of what you can do
with a `reduce` function.  Let's a more useful example of what you can do:

```javascript
let gradesSpring2020 = {
	sean: [83, 90],
	john: [79, 92],
	mary: [85, 92]
}

let finalExamScores = {
	sean: 88,
	john: 85,
	mary: 89
}

let finalGrades = Object.entries(finalExamScores).reduce((total, next) => {
	let [key, val] = next;
	total[key].push(val)
	return total
}, Object.assign({}, gradesSpring2020))

console.log(JSON.stringify(finalGrades, null, 2))
/*
 *{ sean: [ 83, 90, 88 ],
 *  john: [ 79, 92, 85 ],
 *  mary: [ 85, 92, 89 ] }
 */
```

In the second example, we have a starting set of data called `gradesSpring2020` and we have a new
set of data `finalExamScores`.  What we would like to do, is take the new data from
`finalExamScores` and accumulate them into our `gradesSpring2020` store of data.  What might be
interesting to note is that the 2nd argument I passed to reduce (not the 2nd argument of the
function passed _to_ reduce) is a copy of `gradesSpring2020`.  Had I not done this, the reduce
function would have mutated the `gradesSpring2020` on each walk through of the data.

So, let's see how the `reduce` here is similar to a redux reducer.  Again, a reducer in redux is
like the function that you pass into the `reduce` method.  In this example, `gradesSpring2020` would
be like our data store.  In a redux reducer, the first argument in your reducer is the current data
in your data store.  The second argument is the Action, or new piece of data that you want to
"merge" into your store.  Notice I didn't use the word _set_.  You could simply replace the current
value with the new value.  Sometimes however, you want to take the existing value and then do
something with the existing and new value to create a final value.  In the example above, we didn't
replace the data, we added the new value to the array based on the key.

In `Array.prototype.reduce`, you walk through each element of the array and pass the element to the
callback that is passed in as the first argument.  The optional 2nd argument to `reduce` is the
starting value.  If you do not pass it, the first element in the array is used.  So, in redux, you
are not literally walking through an array.  So what passes your data to the reducer?  That's
basically what the `dispatch` function does.  However, this is generally hidden from you, so it's
just good to know, but it's mostly taken care of for you behind the scenes.

Like the 2nd example I showed using the reduce, you need to be careful about not mutating the data
in your store, but instead returning a new value.  And this is where my big concern comes in.

Well, actually one of many

## Redux Problems

Let's talk about some of the problems I already see:

- Performance due to copying data and passing in the entire state store
- Reducers and async
- Passing data around to connected components

### Performance issues

The problem is that your reducer function needs to be pure.  This means it doesn't mutate the
arguments passed in, and implicitly, nothing outside of the reducers themselves should affect the
values in the data store.  This means you can't have and kind of IO in your reducer function.  You
can't make a call to the file system, or make a fetch API call or anything like that (which actually
leads to a second problem)

Why this is concerning to me is the impact on performance.  First off, you are always passing in the
entire data store.  This could be huge.  Because you can't mutate the data in place, you always have
to return a copy of new data.  I now see why react+redux benchmarks are pretty poor.  This alone is
pretty bad.  I guess however that javascript devs aren't too concerned about performance.  Or maybe
it's just redux users.

### Reducers and async data

Reducers should be pure.

What if the data you need to pass in is asynchronous?  For example, what if you need to make an API
fetch to get some data that in turn your Action needs? Well, there's a whole new middleware that you
have to learn called redux-thunk to take care of that.  So now you need to use [redux-thunk][-thunk]
in your code to handle this.

### Passing data around

Technically, this isn't a redux problem, but a react-redux problem.  I think it's fairly common that
one component might update state that a totally separate component needs to be made aware of so that
it can update itself.  In react, data is generally passed down only, from parent to direct child.  A
React Context makes it easier to pass data from a Parent to any descendant more easily, rather than
just a direct child.  But what if the components are way apart in the hierarchy?  Effectively two
3rd cousins needing to talk to each other?

That's where react-redux comes in, and yet...it's so complicated.  You need to create
ConnectedComponents which basically are decorated components that create the glue between themselves
and a new Provider component which now has to be the root Component.  I believe this alone is
perhaps the greatest complexity in getting react-redux to work.

## Why not rxjs?

All these problems feel like they would go away with rxjs and Observables.

First off, I hear people saying they like that there is a single data store that the application
uses.  That in itself isn't bad, but the fact that you have to pass the entire store around is what
I think is a bit dumb.  Why does ComponentA need the _entire_ data store?  It doesn't.  It is really
only interested in the parts that it needs.  In the single store model, you still have to extract
out the parts you need anyway (in react-redux, the `mapStateToStore` function that you pass to
`connect` is what does this).

What if a component only received the data it wanted?  This is what an Observable in rxjs could do.
If ComponentA needs to know what got updated in ComponentB, then ComponentB should be creating a
stream of data (an Observable or Subject) and ComponentA will subscribe to this data.  This kills
all three birds above with one stone:

- You only care about the data you need, you don't pass everything around
- You only get the data once it's ready, async is handled out of the box in rxjs
- Connecting components is as simple as subscribing to an Observable

The only real challenge is that last point.  How does ComponentA find the stream of data that it is
interested in?

## Mobx?

There's a part of me that wants to use mobx, but I will stick with redux for 3 reasons:

- There's a ton of documentation, helper libraries and tooling
- I already started work using redux
- Everyone else is using it

But man, I hate that last reason.