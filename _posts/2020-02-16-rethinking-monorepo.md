---
layout: post
title:  "Rethinking monorepos"
date:   2020-02-13 15:00:00 -0500
categories: rust wasm
---

# Are monorepos worth it?

At Netflix, our team (well, actually one member from another team) really wanted us to use
monorepos.  The argument was that monorepos cut down development headaches, because all your
dependencies are in one gigantic repo.

The classic case where a monorepo helps is you are working on some library, say an npm package, and
it's somewhat heavy development.  You decide to split this library into its own package, because
either other teams might make use of it, or it's useful for other general tasks.  The problem you
will soon face is that as you make modifications to this package, now your dependent project has a
problem getting the latest version.

One solution is to perpetually bump up the version, and constantly publish your library to some
repository (ie, npm, pypi, crates.io, maven, etc).  But that's a big headache.  Another solution
might be to somehow _link_ in a local version of the library.  In Java, you can pass in your package
or module on the classpath or modulepath.  In node, you can use `npm link`.  In rust, you can just
tell in Cargo.toml to use a file path.  But this is all language dependent, and some languages make
this an easier alternative than others.  But what if you've got a polyglot language?  And in my
particular case, `npm link` can have subtleties that you need to be aware.

So, a monorepo is a way to just have all your dependencies in one place.  Google basically does this
with all their software.  That way, your main project can always just do a link to the local
library.  For example, in the javascript world, there's `lerna` which does some symlinking magic to
fool npm into thinking that modules are installed in the `node_modules` folder.

## Monorepos makes development easier, but CI/CD harder

I'm beginning to realize that having a monorepo makes CI/CD harder.  How so?

When I make a git commit or a Pull Request, since the monorepo is one gigantic repository, it's
going to kick off the tests for _everything_.  Also, it's going to build _everything_.  If you have
multiple stages, or you need docker builds, container deployments, etc, all of that testing and
building can come with a big cost.

In my case, noesis hasn't been worked on in awhile.  It's relatively static so far.  But each time I
want to trigger a build and test, if I make a change to khadga, it also forces a build and test for
the other projects.  As a consequence, I'm seriously considering splitting khadga out into 3
separate projects.