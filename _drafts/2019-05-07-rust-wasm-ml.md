---
layout: post
title:  "Rust + WASM + tensorflowjs"
date:   2019-05-07 18:21:40 -0400
categories: rust ml
---

# Real World Rust

I've been doing some rust on the side, so I thought I'd give a basic tutorial for folks who are curious
about rust, but might have been scared off, or who are having trouble finding some real-world examples
to sink their teeth into.

Just a warning up front, I'm very new at rust myself, and have written maybe under 2000 lines of rust
code.  So, there's probably a better way to do what I'm doing, in which case please tell me!

Whenever I learn a new language, it's always a challenge to come up with a learning project that is not
so trivial that you don't learn much from it, but also not so difficult that you throw up your hands in
despair.  So, I'd like to write a series of tutorials on some real world things you can do with rust.

- Launch a subprocess and gather the stdout of the child process in real time
- Write a small web service that talks to a database along with a small test client
- Show an example of interoping with libc to interact with a socket
- Write a wasm based front end app using Yew

Today, I'll focus on the first one: launching a subprocess and gathering the output in real time.

## But first, some prerequisites

I'm going to assume you know at least some parts of rust.  However, the point of these tutorials is to 
help you gain an actual practical understanding.  So, while I will go at least somewhat in-depth into 
topics like borrowing and ownership, it's a good idea to at least have read up on these concepts first.

## Launching a child process

I've managed to write libraries that handle launching a child process, getting the result of the process
and grabbing the stdout and stderr for basically every programming language I know.  For rust, learning
this project will help you gain insight into:

- How reading and writing work on some kind of type that supports the traits Read and Write
- How to extend an existing data type with a new trait
- How to do basic multithreading
- How to work with the std::Result type
- How crates work