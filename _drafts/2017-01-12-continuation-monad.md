---
layout: post
title:  "Studying the continuation monad"
date:   2017-01-12 00:55:40 -0400
categories: purescript notes monads callbacks
---
Last night I was digging through the chapter on Callback Hell in the purescript book.  I've now run into this problem in
the project I am working and need to find a solution for asynchronous functions.  First, let me describe my problem,
and why I need a solution for asynchronous functions.

I have a program which needs to call some child processes to do a few tasks.  For example, I need to run a git clone
command and run lein compile.  So to do this, I've been using the purescript-node-child-process module to do the actual
launching of the child processes.  The problem is that under the hood, this module is using node, and node's spawn
command is asynchronous.  Suppose for a second I have a launch function which will call git clone, followed by a lein
compile like this:

```haskell
main = do
  let newSO :: SpawnOptions -> String -> SpawnOptions
      newSO c@so cwd = {cwd: cwd, stdio: c.stdio, detached: c.detached, env: c.env, uid: c.uid, gid: c.gid}
  gitCP <- launch "git" ["checkout", "https://github.com/rarebreed/rhsm-qe.git"] defaultSpawnOptions
  leinCP <- launch "lein" ["compile"] (newSO defaultSpawnOptions "rhsm-qe")
  -- Rest of program....
```

The idea here is that I run git checkout and then run lein compile in the rhsm-qe directory that gets created by the
git checkout command.  But there's a problem.  While the git checkout is still running, the flow of control will then
pass to the lein compile!  That is what asynchronous programming is about.  If with lazy computing you have to think
"when will my function start?", with asynchronous functions, you have to think "when will my function end?".  I know for
me, having never really done asynchronous programming before, this was a bit mystifying.

So one well known solution is to use callbacks.  In that case, I would have to rewrite the launch function so that it
would take a function as another parameter.  What's a little confusing here is that usually when you see examples online
about callback programming and how to solve it, usually an earlier function has to generate some result, and this result
is then consumed by a later function.  In my case, the first launch command doesn't return anything needed by the second
launch.  While the two terms are basically synonymous, I think the term continuation is more applicable than callback in
in this scenario.  What I need to do is specify the "continuation of the rest of the program" from the launch function.

## The continuation monad
