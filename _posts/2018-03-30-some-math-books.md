---
layout: post
title:  "Boning up on some math: rust and graphs"
date:   2018-03-30 15:40:00 -0400
categories: rust ml math
---
Consider this blog post a meandering stream of consciousness.  I will perhaps ask more questions than provide 
any kind of answers.  Once I do a bit more digging and practice, I'll revisit this blog, or perhaps add a second
one to dive deeper.

For the most part, this blog is mostly a set of notes and thoughts to myself to help reinforce my learning. It will
also be a placeholder for design and implementation questions with regards to building a rust math library.

# Something is wrong with me...I bought a bunch of math videos

There was a sale on Udemy a few days ago, and if you bought 3 or more courses, each course was only 9.99 each.  So,
I wound up buying 4 math courses:

- Discrete Mathematics 1
- Discrete Mathematics 2
- Number Theory
- Graph Theory

This now joins the list of some other math videos I bought a few months ago :D

- Calculus 1
- Calculus 2
- Calculus 3
- Linear Algebra 1
- Linear Algebra 2
- Probability and Statistics

So, what's up with all these math courses?  I mean, I didn't even bother watching all the other courses, so why buy
a bunch of other courses?  I kind of want to switch my career focus around a bit.  I got into Computer Science
because I was interested in AI.  I minored in Mathematics because I thought it would be a good complement to CS as
well as my interest in theoretical physics.  But my whole career, I feel like I haven't really made use of my CS 
degree honestly.

My whole career, I've basically made sure that either bits were in the right places, getting data from here to there
and back, and making sure products are doing what they are supposed to be doing.  Previously, I said that I thought
that Algorithm studies were kind of pointless.  That wasn't entirely true.

I think that one should know what Algorithms solve which problems.  For example, if you have a dependency resolution
problem, you should probably look up how topological sorting works (which in turn means you should have at least some
familiarity with graph theory).  If you are presented with an optimization problem, like maximizing cost to weight, 
then you should at least know what the knapsack problem is.  Maybe you have a couple of nodes that you need to hop
across to send some messaging through, so perhaps you should use one of Dijkstra's algorithms.  My point is that 
knowing how to do these things off the top of your head isn't really what I consider a sign of one's capabilities.

But, there are cases where you will need this kind of knowledge.  For example, writing your own library to handle
certain problems.  As I mentioned in the previous post, I want to kill two birds with one stone by boning up on my
math skills again, and then implementing certain algorithms in rust.

Now that Machine Learning and Data Scientist are two of the top most desired categories for companies, there is no
better time than now.  

# Graph Theory and rust

The first video course I started watching was on Graph Theory.  It's a very simplistic course, and it doesn't go into
any proofs.  Most of what I have seen so far (I'm not quite 30% through it) is definitional.  For example:

- size: Number of vertices in a graph
- order: Number of
- degree: Number of edges of a vertex
- Walk: An ordered list of Vertex and Edges where duplicates are allowed
  - closed: a Walk such that the beginning and ending Vertex are the same
  - open: a walk which is not closed
- Trail: is a Walk such that no edge is repeated (eg all edges are distinct)
  - Tour: a closed trail
- Path: os a Walk such that all Vertices and Edges are distinct (for a Trail, vertices can be repeated)
  - cycle: a Path such that the start and end Vertex are the same

I also skipped ahead and started looking at a couple theorems and algorithms for Graph Theory, such as the famous seven
bridges of Konigsberg problem, and the Euler algorithms to solve the problem.  Of course, while the 7 bridges has a
fascinating back story to it, I wonder what kind of real world problems would you need the Eulerian paths to solve?  It
took me a bit of thinking, but one possibility is traces on a circuit board.  Short of multi-layer boards, you dont 
want the circuit traces to intersect with each other.  

## Questions I had about implementing Graphs and Graph Algorithms

While I was wathing the videos, a number of implementation questions popped into my head.  These videos only considered
the mathematical notions of graphs; there was no programming implementations given.  So while watching these videos, I
had a log of questions.

- What data structure to use to represent a graph?
  - What about a vertex?
  - What about an edge?
- How to represent properties on vertices and edges
  - For example, weights on an edge or even a name
- How to represent directed edges

### Implementing a graph 

As I was watching the videos, I was trying to think of how to implement a Graph.  I think in the 4th or 5th video clip, 
they talked about an Adjency Matrix, and that immediately rung a bell about adjacency lists back in my CS days.  While
adjacency matrices are nice if you have dense graphs (lots of vertices connected to lots of other vertices) they start
to waste space if they are not too dense.

Adjacency lists are basically an array of lists.  From a more pragmatic point of view, I could create this in rust as a
vector of vectors, allowing the graph to grow over time. There's a bit of space wasting when the vector has to grow, but
I think that's a worthwhile tradeoff compared to using a vector of linked lists.  While growing the linked list would be
easy, the look up time would be O(n) (1 look up for the row, and up to n look ups in the linked list, as opposed to a 
O(1) look up for an edge with a vector of vectors).

**What about directed edges?**

![Undirected Grapgh](https://lh3.googleusercontent.com/EPVsH5sQafBNCpK7BjZZRE1j_2muqEk-NbI1yu37LqOp-prlyucTo1VJxfPcbNVBZ-dT3PYmRAsJ7KZn8jhECR8ZSET1FVi9bv_lDzBQ3GbkzKpdVvi7GFrK01KX2OGcM84yaX-VvyvRJeCsZIRvil6DnYFNBUhJrAx6NVa7FrxCSvOw6qm8AxdLmm4WpY10YLx5MsmK0alqHXQ7RsHvFllPjVA1dm4HDwfjgIiMKy1aJgnJ5uq46gA5DekmtEU1qTeSnMK4cozww2Qf88rMO57YRPctnd5B4wG5FDK3GOd41GojuYnAbgZWPMUaaphqrx5qc5DtkjNEIT3325s_F94Pessd0CqBGrXywZ6U8JElIvihFw1vP2b7MNv7xb-cbro6q9MIl0jQCjgYxvJLH76rkJ7wuwUHEypABt6555UGVwMf0aQObXC4BhBq3ZGI-kU-lz0H16k4DvZSOkNZj3HqXi7mEAS9qzAohHPq2uJC5hPj05cIHD_EN5yoTiPIAy33WkJGDbzBGqhiei8jhWsCbhQqZDVxR4mo64lNPTXZJgapb0-VESTIFKOIsu7dZFI6haIUMXRLw-eVcGiRl7V81VcvljbtOziX-Nw=w768-h612-no)

Let's assume we model a graph something like this (as a vector of lists):

```
+---+---+---+
| 0 | 2 | 5 |
|---|---|---+
| 1 | 2 |
|---|---|---+---+---+
| 2 | 0 | 1 | 3 | 4 |
|---|---|---|---+---+
| 3 | 2 | 4 |
|---|---|---|---+ 
| 4 | 2 | 3 | 5 |
|---|---|---+---+
| 5 | 0 | 4 |
+---+---+---+
```

This can represent an undirected graph, but what if we want to indicate a direction?  All the above does is say that
the 0 vertex has an edge to 2 and 5.  But what if I only want to say that 2 can go to 0, but 0 can not go to 2?  We 
could however state that the adjacency list only specify outdegrees, in other words, edges going out.  If you wanted to
say that there was only an incoming edge from 2 to 0, you could do this:

```
+---+---+
| 0 | 5 |
|---|---|
| 1 | 2 |
|---|---|---+---+---+
| 2 | 0 | 1 | 3 | 4 |
|---|---|---|---+---+
| 3 | 2 | 4 |
|---|---|---|---+ 
| 4 | 2 | 3 | 5 |
|---|---|---+---+
| 5 | 0 | 4 |
+---+---+---+
```

Notice that the only element in the list for 0 is 5, meaning there is an outgoing edge from 0 -> 5.  We do however have
in the list for 2 a 0, meaning there is an outgoing edge from 2 -> 0.  However, describing the graph only via its
outgoing edges makes finding what other vertices connect to it more difficult since you dont have any information about
incoming edges.  Asking the question "How many different ways can I get to X?" becomes more difficult

We could solve this problem with an adjacency matrix, and then specifying a numeric value for direction.  For example
if you look up from row to column, a 1 indicates an outgoing edge (from row Vertex to column Vertex) while a -1 shows 
an incoming edge from row to column.  Eg, in the graph below, 0 has an incoming edge from 2

```
    +---+---+---+---+---+---+
    | 0 | 1 | 2 | 3 | 4 | 5 |
+---+---|---|---|---|---|---|
| 0 | 0 | 0 |-1 | 0 | 0 | 1 |
|---|---|---|---|---|---|---|
| 1 | 0 | 0 | 1 | 0 | 0 | 0 |
|---|---|---|---|---|---|---|
| 2 | 1 | 1 | 0 | 1 | 1 | 0 |
|---|---|---|---|---|---|---|
| 3 | 0 | 0 | 1 | 0 | 1 | 0 |
|---|---|---|---|---|---|---|
| 4 | 0 | 0 | 1 | 1 | 0 | 1 |
|---|---|---|---|---|---|---|
| 5 | 1 | 0 | 0 | 0 | 1 | 0 |
+---+---+---+---+---+---+---+
```

Note how much more space this solution takes though.

Another possibility might be to have tuples to describe direction.  Ie, (0,2) is not the same as (2,0).  However, this
would also take up more space.  Again, it's a trade off.  But the adjacency matrix will still have the advantage of a
O(1) look up.  On the other hand, if you have a dense graph with thousands, and potentially millions of vertices, the
adjacency matrix will start consuming a lot of space (n*size of structure)^2.  With a more sparse graph, you will still
need n rows, but you will need less than n columns if the adjacency list is used..  The worst case is still n^2 however
if you can add to your graph.

**Multigraphs**

This doesn't even factor in multigraphs, where you can have _parallel edges_ (where two vertices A and B can have two
edges that connect them together).  Multigraphs are very useful and also model the real world.  For example, you might
be able to take two roads to get to the same destination.  I don't think I will support _hyper graphs_ which are graphs
such that an edge can "fork off" or split (eg, they could look like a Y...in other words an edge is no longer a one to
one mapping of one Vertex to another Vertex, it can be a mapping of one Vertex to many other Vertices).

For example given this graph:

![Digraph](https://lh3.googleusercontent.com/BCsUfWu_jsOPCJb3Wq9U7oE3wXSfqYfYIlygjnaSzPthkvg11X3mq50jGU72sYmIrXeSwMMH681q71dPoqgHu1qXFdmsE41zrZLw23TP3edRKboT6G8VchkKdQTcKP334fo1SrkR3QExbCPcQ-h7SgGjafS3Mr1hPAmcjxp54z0uaK-r84oCVjogxRFKMgkQ7WKcMPuIaUgtFP5yliW3czOPfqeFGhoWXvzUMpHzRbJeSsPizxzJss7B9niadaQz8D5onQW_hVNO1mwHyFOj7Ju9QIMCJXvY4ZIPiDRaSkFJ7rx1FR3ZWq8eb8jnZnTjmeWZMwK5D5PHh3KQoTKemj8AaJwJWAaLyAGkLsVDYB2SYNRP_4GnMBcKEHgEs2KbFF3wwFAOqtD6pYK5PHVsMld2qNIXL_ZV7Fzrzin9yVyPgfFvFhjZ0Oeg1EixxAfHvZPVvl59HPEZmbP6sXv1GioAhpFElBT9tapNvjOg_ikpfWigOCjsx2XarmfVtqZOsjH1p9ZCdfiXBYJ_c_JYervQm2CDYlaUuQwAQd0lX0Wb7cdyBmMEgURhkmWhS5_DJYTu5ERjcAI0H8CrTJnz0a8V_tmGXUIf16yZXeU=w700-h540-no)

How could we describe this data structure?  While we could still use an adjacency matrix for this, we would have to use
Edges for the columns, and then have different numeric values for the direction.  It also would lose the efficiency 
that an undirected graph written as an adjacency matrix would provide (an O(1) lookup).  This is because we would need 
to search the column for the matching number description.

**What about graph properties?**

Most graph databases let you attach data to vertices and edges.  If a vertex is simply a number, and an edge is just a 
lookup from the row index to another vertex (eg, in the above graph, an edge exists between 0 and 2, 0 and 5, 1 and 2, 
2 and 3, etc), this isn't much data other than the relationship between an edge and a vertex.

### First stab at a graph implementation

One idea might be to just create separate Vertex and Edge structs, and then link them together with references.

```rust
struct Edge {
    name: String,
    properties: Map<String, String>,
    vertices: (Vertex, Vertex)
}

struct Vertex {
    name: String,
    properties: Map<String, String>,
    edges: Map<String, Edge>
}
```

So, with an implementation like this, how hard would be to find out if two Vertices are connected to each other?  Eg,
given a Vertex A and B, I want to see if they have an edge?

I could take A, and look at its edges.  By iterating the A.edges(), I can examine the Edge object and see if it contains
the vertices.  This would be O(n).  If I know the name of the edge, this will be a very fast look up depending on the
underlying map implementation (hashed maps are nearly constant time lookup, while tree based maps have logarithmic time
lookups on average assuming balanced trees).

Another possibility is to sort the edges.  For example, if the key is an Edge itself rather than a String, then 

### Graph features and algorithms

So, what kind of data structure can we use that implements the following features?

- Directed edge graphs
- Multigraph support
- Vertices with metadata (eg properties like color, visited, age, etc)
- Edges with metadata (eg properties like relationships, weights etc)

Moreover, can we implement the underlying data structure such that

- We can traverse the graph
- Detect cycles
- Detect Eulerian Paths
- Implement Coloring?
- Implement Topological sort?
- Make insertion and deletion of vertices and edges as simple as possible?

How about reactive properties?  Should we notify observers when the graph has changed?  Rather than send the entire 
graph as the data that is emitted, why not just send whatever has changed?  Would this obviate the need for a persistent
data structure?

A lot has been made of persistent data structures, especially with the rise of clojure.  While haskell and even scala
were around longer, haskell's bread and butter were Lists, and their Maps were some form of 2-3 Trees (IIRC).  Clojure
came along and implemented some interesting persistent data structures.  But really....why do we need persistent data
types?

If you have multiple entities looking at some data, if even one of them starts writing to the data, that could become 
problematic to the other entities especially if the data structure is large and the updates are not atomic (so that not
only are the other entities looking at possibly stale data, they could be looking at inconsistent data).  The age old
solution was to use locks or transactions of some form.  The problem with this solution is that you get contention for 
access to the data since only one observer can mutate and everyone else has to look away while the data is updated.

If you have a persistent data structure, another entity can "update" his little view of the data, but everyone else 
will go merrily on their way.  It does not solve the staleness problem (all the other entities have a view of the data
that is still old) but it does solve the inconsistency problem.  In a reactive solution, you could use persistent data
structures with reactive so that you can notify all the other entities that the data has been updated.  Of course, what
happens if two entities simultaneously update the same data?  Then you have a conflict because whose data should be 
considered correct?  This is very similiar to a git merge conflict.  You and a coworker both modify some code, and in 
your local working copy, everything is fine.  However, once one of you does a pull you will get a conflict.

So even persistent data structures don't always solve our problems if two or more actors are modifying some data.  At 
the end of their update, each actor has a new "view" into the data, but how do they share this new data with others if 
there is a conflict?

The problem fundamentally lies with ordering.  Locks, transactions, even rust's ownership rules, are all about 
enforcing an ordering.  If a shared bank account balance is $100, and one person deposits $200 and another person 
debits $250, you really need to make sure the deposit goes first, otherwise they will pay a penalty fee!  Does a 
reactive solution give us a way out of this mess?

### Concurrency on a graph

So, for performance reasons, can we make the graph multithreaded?  Will reactive help us here to communicate across
threads?

This begs a deeper question about a reactive solution for rust.  Unfortunately, the rxrust implementation has been
abandoned.  This is perhaps another good learning project for me.  But that in itself would be a major project.  First
off, I would need to implement some kind of event loop like vertx in java or asyncio in python.  Only then would it 
make sense to have reactive with asynchronous data changes.

Once that was in place, how could two or more threads work on a graph?  For traversal, a new thread could be spawned
for each traversal down a new edge, but the threads would need know where the other threads have been so as not to 
duplicate work. Remember, if two threads are traversing a graph, each thread could wind up visiting the same Vertex.
Therefore, each thread has to make sure it isn't visiting the same Vertex as another thread.  

An algorithm would need to be devised that:

- Have a shared data structure that all threads could look at to know which vertices have been visited
- Doesn't have more overhead than a single threaded application

And that's just for a read-only concurrency.  What if one thread was inserting or deleting edges or vertices while 
another was traversing?

Rust's safety will actually prevent this from happening.  Since an insertion or deletion would require mutating the
graph (either mutating a Vertex or Edge struct), we would either:

- Need a mutable reference
- Or take ownership of the vertex or edge

But how can we have each thread communicate changes in state?  For example, let's say this was a reactive solution.
The shared object is a Subject as are each of the threads.  As each Observer thread can:

- Work on its local copy
  - Add or  

## Rust Mathematics with traits

So, I tried writing a simple little factorial in rust.  Easy-peasy right?  Let's look at my first stab at this:

```rust
fn factorial_(n: u32, acc: u64) -> u64 {
    if n == 0 {
        return acc
    }
    let n1: u64 = n as u64;
    acc = acc * n1;
    factorial_(n - 1, acc)
}

fn factorial(n: u32) -> u64 {
    factorial_(n, 1)
}
```

So, notice how I have to create a temporary variable n1 which is a u64 from the n argument.  The reason is that rust
will not implicitly do _any_ casting for you, even if otherwise it would be safe (eg casting a smaller number type to a
larger number type).  Had I not done this, the rustc compiler would complain that _acc * n1__ was not allowed, since 
there is no operation for * that can multiply a u32 and u34 and return a u64.

But, this just feels ugly doesn't it?  Wouldn't it be nice to have factorial work on other Integer types?  What if I
wanted to pass in a u8, or u16?  

Rust by itself contains no generic math library unlike the haskell prelude.  I did discover a crate called num which 
provides some math traits.  However, before I discovered that, I thought I would try to use some other traits.  Here 
was my first  stab at it:

```rust
use std::ops::Mul;
use std::ops::Sub;

fn factorial_<T, A>(n: T, acc: A) -> A
    where T: Mul + Sub + Copy + Eq {
    if n == 0 {
        return acc
    }
    let m = n - 1;
    factorial_(m, acc * n)
} 
```

Unfortunately this does not work.  The first error occurs because although I have specified the Eq trait on the n type, 
when  it gets to n == 0, the compiler says it was expecting a type of T, but got an {integer} instead.  My guess here 
is that although we have some operations available on n, rust is still seeing n as a T type.

I started browsing around rust to see how to resolve this issue.  If you think about, the error makes sense.  I am 
comparing n, which is a type of T, to 0 which is an integer.  Even though I specified that T must support the Eq Trait,
that only means that I can compare two T types (eg given t1: T and t2: T, I can do t1 == t2).  I believe I have come 
across the solution, but I need to digest it a bit.  The solution seems to be the From and Into traits.

## Think in traits not classes

If you've done OOP for any length of time, you've probably heard this mantra:

```
Program to interfaces, not implementations
```

At least back when I was learning OOP, they didn't really talk about that gem too much.  All the books and courses in 
college were busy teaching you about class inheritance and polymorphism on classes.  Most languages older than about
8 years old only do single dispatch polymorphism.  What this means basically is that when you have some method that
has the same signature, it uses only one argument in the method signature to know the actual implementation.  In most
classical OOP language that argument is the implicit _this_ or _self_.  Depending on what _this_ is, the runtime will
look up and see what method actually needs to be performed...for example a super's method or a child class's overridden
implementation (note that overridden and overloaded methods are similar but not the same:  overridden methods have the
same name and signature as a parent method, while overloaded functions all have the same name but must have at least 
one argument type which is not the return type that is different)

There's another kind of polymorphism in OOP languages though which often go by the name of Interfaces.  An Interface in 
Java for example can only describe methods and their signatures but no actual implementation (although this changed in
Java 8, and you can have a default implementation...plus you could also have static methods on interfaces, but I 
digress).  An interface is like a contract.  If a class implements an interface, it's saying it promises to fulfill not
just the implementation of the methods, but something more.  An interface also has an unspoken meaning.

For example, these two interfaces define the same methods, but will probably be used in two very different contexts:

```java
interface IGame {
    void play(String resourcePath);
}

interface IMedia {
    void play(String resourcePath);
}
```

Although these 2 interfaces have the same methods with the same signatures, most likely they will be used for very 
different purposes.  The IGame interface is probably meant to be implemented for computer games, or maybe even to keep
track of sporting events while the IMedia interface is probably meant to be implemented by software that can playback
mp3, ogg, m4v or other media related files.  This notion is very important to understand, and it explains I think why
the dynamic community has finally woken up to the fact that _Duck Typing_ is not enough.

In duck typing, as long as some object has the same name method, you can use it anywhere.  There are two problems with
this though:

- The type signature may be different (if you are using a dynamic language)
- Even if it has the correct type signature, you might not be using the function in the right context

For example, the term _duck typing_ comes from the saying:

```
If it walks like a duck, and it quacks like a duck, it's a duck
```

But is it?  What if I have this:

```python
class FakeDoctor:
    def quack(self):
        prescription = "Take this snake oil, and you will be fine"
        print(prescription)
        return prescription

mallard = Mallard()
charlatan = FakeDoctor()

for duck in (mallar, charlatan):
    duck.quack()
```

Yeah, doesn't make much sense does it?  But that's what duck typing lets you do.  And it's just one reason I do not
advocate dynamic programming (and don't get me started on monkey patching).

So interfaces are _not only_ a means of expressing _what_ methods should be implemented, but it's also a form of a type.
It says there is a logical relationship between any objects that implement this interface.

Traits in rust are a kind of interface, and they are actually more powerful than java's interface.  This is because rust
separates structs from their implementations.  In Java, when you declare a class, you must also specify then and there
what interfaces it implements.  If you discover after the fact that you need to implement a new interface to your class,
your only recourse is to create a child class, and then have that child class implement the interface.  But then you get
into the thorny issues of protected vs private, covariance/contravariance of generics, and potentially needing to type
cast.

In rust, you simply define whatever data the struct holds, and then you can attach implementations of traits on a 
struct later:

```rust
struct Employee {
    name: String,
    age: u8
    companies: Vector<String>
}

trait AsString<T> {
    String print(this: &T)
}

impl AsString for Employee {
    fn print()
}
```