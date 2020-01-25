---
layout: post
title:  "A reckoning for Open Source"
date:   2020-01-23 18:21:40 -0500
categories: rust ml
---

# Is it time to reconsider Open Source?

A few days ago, [something bad happened][-sad-day] in the rust community over the actix-web project.
You can read Steve Klabnik's blog post, but in a nutshell, the actix-web project has had a stormy
history in the rust community for awhile.  Ultimately, the lead maintainer of the project threw his
hands up in despair and pulled actix-web from github.

In Steve's post, he talks about what he saw as a mismatch between what the rust community expects
and what the lead maintainer was doing.  The rust community has an unwritten and unsaid expectation
that the premiere benefit of rust is its safety.  The lead maintainer on the other hand felt that
perhaps sacrificing a small amount of risk was worth increased speed.  I believe this was important
to the developer since actix-web seems to crop up in the top 3 performing web server frameworks from
Tech Empower.

The rust community, generally known as being very nice and friendly, had a few members say some
rather caustic things.  The maintainer appeared to be flippant, but one should take into
consideration that the developer's native language is not English. So perhaps he was not trying to
come off this way.  When members would submit Pull Requests to get fixes that were just as
performant, but also didn't require undefined behavior (eg, using unsafe) or exhibit unsoundness (eg
using cells inappropriately), some in the community took the author's non-commital badly.

The end result of the bickering is that the maintainer said he had enough, was leaving open source
entirely, and pulled the project with him (though it has since been [forked][-actix-web] under a new
team now).

## What does this have to do with Open Source in general?

One might say that one of the beauties of open source is that you can never really remove it.  All
you need to do is fork the project if you don't like the decisions of the maintainer, or as long as
someone has an up to date local copy of the repo, they can just start their own repo on github or
whatnot.

This is however not the point.  I will elaborate on several points that I believe is heading open
source projects towards a yawning chasm:

- A sense of entitlement by the community to fix bugs, put in enhancements, etc
- Developer discouragement
- Security issues (eg, who will fix CVE's in a timely manner?)
- Lack of vision or direction without a BDFL

### Entitlement

While reading through the comments on the Reddit post from Steve Klabnik's post, I was struck by how
many people felt that it was the developer's ethical duty and responsibility to fix problems.
Although most FOSS licenses state that liabilities for using it are at the user's own risk, this is
not sufficient for a substantial number of users.

The argument for some was akin to saying, if you loan out a car to people, but you know there's a
.0001% chance that the breaks might fail, you have a duty and moral obligation to fix it before
loaning it out.  It's not good enough to inform the person borrowing the car "hey, the chance is
pretty slim, but the brake's might give out".  Many did not buy that it's up to the user to decide
whether they want to use the borrowed car or not.  Several people were quite adamant that the moral
imperative, and thus the burden to fix the issue was on the lender of the car, not on the decision
of the person to not use the freely offered car.

This sense of entitlement leads to the next problem.

### Developer discouragement

Fortunately, I have not experienced this myself.  But reading from many other comments or reading
articles from other sites, this appears to be a big problem.

Many developers of large projects sympathized with the lead maintainer of actix-web, even if they
felt like there might have been a better way to handle the situation.  This problem is not unique to
the rust community.

If people using the (free) software become demanding, or worse insulting, how would that make you
feel? The flippant answer is to "grow a pair" or "get a thicker skin", but these kinds of attacks
don't make anyone feel better.  The whole idea of open source was originally so that if someone did
spot a problem or area for improvement, they could jump in and add the fix or improvement
themselves.  But it seems like people would rather complain than help out.

The argument could be made that they don't have the time to investigate and learn how to implement
the fix/improvement.  The maintainers, being familiar with the code, should have an easier time.
However, this neglects the fact that many open source projects are done in the spare time of a
handful of users (often just one).  Even company sponsored projects often only have one to a few
developers actively working on the project.

If project leads are getting burned out by an abusive and ingrateful community, what can you do
about it?

### Security problems

One of the problems that's becoming harder and harder to ignore about open source projects is
security issues and how they are addressed.  Not too long ago, a big stink was made about how one
hacker stole several million dollars of crypto currency by uploading a malicious package on npm.

There's a [a new consortium called the Bytecode Alliance][-bytecode] that is trying to address this
problem by working on webassembly technologies.  Why not just make code safer by running in isolated
VM's or with cgroup/namespace protections via containers?  While VM's are more secure by having very
rigid boundaries, they are also extremely heavyweight.  Moreover, if your process running in your VM
needed to interact with another process, it'd have to do so using network IPC.  Not even named pipes
or unix sockets would work.  What about containers?  Containers are more lightweight, but then you
would have to guarantee that every program/process has a common base image (which is what Red Hat is
doing), otherwise it will get just as bloated as running as a VM.  Furthermore, containers can't
isolate system calls and even doing IPC across container boundaries is relatively expensive.

What the Bytecode Alliance is doing is creating "nanoprocesses" via the sandboxing that webassembly
provides so that it can even lock down syscalls. Solomon Hykes, the founder of Docker, said that had
webassembly existed back in 2008, [he never would have invented Docker][-no-docker].

I've always been a little bothered by the idea of docker.  Cgroups and namespaces I can understand.
Those are very lightweight, and you can create rules for certain processes.  But bringing along a
filesystem/runtime for your executable? I always felt like docker existed because the majority of
programs were written in languages that didn't ship you a binary.  So docker was invented to ship
everything you needed inside images.  And while they are lighter weight than VM's (VM's are
abstractions around the hardware that you can install an OS on, while containers are abstractions
around processes which are instances of images that are analogous to binary executables and
filesystems), I always felt the crux of the problem was simply portability.  I didn't think much of
the security angle until recently.

But even once these wasm technologies are ready for prime time, how many open source packages are
going to convert their code to webassembly?  How long will it take for every language to have
compilers that compile down to wasm?

From the article about the Bytecode Alliance project, it is clear that a large number of open source
projects suffer from security vulnerabilities (that have been reported).  Even more alarming is how
long it takes CVE's to be addressed, if at all.  As the article states, some developers might not
have the security know-how to even fix the vulnerability.  An [article they link][-vulnerable] to
shows that almost 40% of npm projects have a vulnerable dependency.

Open source can no longer take security for granted.  The rate at which hackers are taken advantage
of trusting open source packages is growing.

### Lack of vision or direction

Not all open source projects suffer from this problem. For example, linux has Linus Torvalds who
sheperds the changes as he sees fit.  Python used to have Guido van Rossum as its Benevolent
Dictator for Life (BDFL).

Some people think the lack of a BDFL is a good thing.  I for one do not.  And with this, I do have
personal experience having worked on the Openstack project while working at Red Hat.

Unlike most projects Red Hat works on, Red Hat is not the lead contributor/committer to Openstack.
Since there is a foundation for Openstack, and there is no one overall BDFL or head architect for
Openstack, what goes into the project becomes a political battle.

Whatever company/developer has the most commits gets the most voting power.  Whomever has the most
voting power gets the say so for what PR's gets merged in.  This leads to an unfortunate "design by
committee" style of design with all the bad connotations that has.

I remember one time, there was an issue in Openstack where a race condition was found when bare
metal machines were coming up asynchronously and sending data back up to one of the Openstack
services (can't recall if it was Nova or Ironic).  Because machines were coming up possibly
simultaneously, the data structure that was being updated could have been written partially by two
or more machines.  One solution was to force all the updates into a queue, and then have the service
pull the data from the queue one by one.

This was deemed too complicated, and so a timeout was issued instead.  Guess what...the problem
unsurprisingly reared its head again.  Because there was no guiding vision for what the project was
supposed to do or be, each company that had a stake in Openstack (Dell, HP, Cisco, Red Hat, etc)
wanted to prioritize bug fixes and enhancements that would benefit their use case the most.
Whatever company had developers (on a certain project) with the most commits would have more voting
power in gerrit to merge code in.

Again, some people think this non-hierarchical system is great.  At Netflix, there's a huge mantra
of being a bottom up driven culture and that we do things like a flocking behavior.  In a flock of
birds, or a school of fish, there's no leader bird or fish that decides where the rest of the flock
is going.  So they pat themselves on the back saying how great their culture is that the engineers
themselves get to figure out what to work on, while not realizing all the problems this entails.

I always thought this was a huge fallacy and illogical analogy.  Flocking behavior has some rules
baked into it that guides the behavior of each individual.  For example, flocking behavior exists
because it helps in hunting, or to confuse predators.  The "goal" of flocking is baked in.  The goal
with non-hierarchical systems often either doesn't exist, or is so vague and nebulous as to be
interpreted by each invidual or team however they want.  In other words, the shared communal goal is
known by each member in a real flock, and this goal sets up rules for the direction.  This shared
goal does not exist in software projects.

The second very flawed analogy of the flocking behavior is that in a real flock, each individual has
perfect and near instantaneous knowledge of what its neighbors are doing.  If an individual notices
that its immediate neighbors are starting to veer left, it knows it needs to start going left too.
The slight delay is what makes flocks or schools of fish fascinating to watch, but in a real world
software project, the individuals and teams do not have anywhere near real-time knowledge of what
their neighbors are doing.

I often felt like my views on finding balance between non-hierarchical structure and needing a
central orchestrator rubbed people the wrong way at Netflix.  The problem in any large structure as
it grows and scales in size is not just "doing your own thing to cut out the red tape" or finding
and hiring the best.  For example, there is a [slide][-slide] in the Netflix culture deck that shows
that as a company grows, the talent density decreases, and this creates chaos.  To account for the
chaos, process and procedure is used to give "guard rails" for the less talented.

The argument goes that these processes works ok to curb the chaos, but:

1. It limits or handicaps the truly gifted
2. If the market shifts, all those hard coded rules are ingrained and hard to change

The real problem, is that growth and scaling is not solved by hiring more "stunning" colleagues.
Two of the biggest factors that causes chaos as an entity grows are:

- Contention of resources
- Consensus of decisions

I find it funny how we understand these limitations technologically.  If we have a web service that
starts getting hit more and more, or we have many threads waiting on a mutex, or we have consensus
algorithms used in Raft or Blockchain, we go "of course as a software system grows in size and
scale, we obviously have contention of resources and consensus problems".  So why can't we see this
is true outside of software systems?

As any entity grows in size or complexity, it becomes more specialized.  These specialized sub
groups often rely on other groups.  What happens when Team A and Team B both need Team C?  I don't
care how smart your engineers are: you have a problem.  The second problem is reaching agreement on
what to do.  The more individuals and more teams you have, the more they think they have the right
idea on how to solve a problem.  Some will say, well Team A has the right to solve problems the
best way they know how.  At Netflix, this was called "Freedom and Responsibility".  You were free to
do what you wanted, but you were responsible for the consequences (good or ill) of what you did.
But F&R as we called it had it's own problems.

In your own company how often have you been blind sided by what a sister team does to some project
that you rely on, and now the API changed, or some new dependency was added, or worse, they abandon
some project?  At some point, someone has to understand "the big picture" and guide everything
along.  I have been at two companies during their "growth spurts" when they went from medium to
large companies, and both of them suffered through these scaling and cultural pains.

When your company is small, and a start up, the free wheeling, no process culture works great.  It
keeps the company agile and able to act and react quickly.  But as it grows, or even as your project
scales in size and complexity, this doesn't work.  Constraints control chaos.  

What does this have to do with open source?  As an open source project grows larger and more
popular, it is going to face contention of resources.  There might not be enough maintainers or
contributors to keep up with bug fixes or new features.  More crucially, without some kind of vision
for the direction of a project, the harder reaching consensus of decisions becomes.  Also, the wants
of the community can become out of synch with the developers because in a way, the community also
often affects the decision making process of what happens in the project.

## What can we do?

Businesses have come to rely on open source projects for the projects that they build.  What would
you do if all of a sudden a major library just vanishes overnight?  What would you do if there was a
heartbleed like vulnerability in a dependency, but the maintainers don't know how to fix it?  How do
you even know that some dependency isn't a malicious package intentionally designed that way?

The vast majority of open source work is done by volunteers.  Some major projects have sponsors
(like Facebook with react), but many projects do not.  Magnify this by the transitive
dependencies...the dependencies of your dependencies and so on.  If an open source project is mainly
done as a labor of love, how do you answer the above questions?

The internet, being the internet, is almost impossible to control from a social stand point.  If you
start moderating reddit feeds, or deleting github issues, you might get called out on it by
like-minded individuals, who in turn can taint the reputation of a project (I recall hearing rumors
about how rude and superior the Scala community was...and whether true or not....it colored my
wanting to learn and use the language).

How can you make sure that someone can take care of code as quickly as possible?

### Should open source be treated as national/world infrastructure?

We have real highways, and we have information highways.  Should open source code be considered in
the same light as infrastructure, and you have paid federal employees people working on it?

America's economy is, to a significant degree, floating upon the success of information technology.
Could open source be considered a national resource in the same way that interstate highways are?
If so many companies are relying on open source projects, then maybe these things need to be
supported nationally.  Just as the highways facilitate trade and commerce, the same argument could
be made for open source software that is standardized, audited, and regulated.

Of course, then the question is, why should America be the only one supporting all these free
projects that other countries can use?  It's a fair question, and one I don't have an answer to.

Other questions follow:

- What languages would be supported?
- Would there only be one choice per category (ie, only one open source front end library)?
- How would we pay for it?  Is it fair to tax say, West Virginians for something that primarily only
  Californians would use?
- If the pay for these engineers is not competitive with private industry, how good will the code be?
	
The government is a big user of open source since they don't particularly like the idea of some
proprietary company having potential backdoors in their closed source code.  It would seem like it
would be in the government's best interests to make sure that the codebase they are relying on is
as bug-free and secure as possible.

### Technical solution

I quite frankly don't think this will work, or at least, it won't work for a long time.  I could be
wrong though, and I do admire the work that the Bytecode Alliance is doing in this area.

I already pointed out the problems with the webassembly nanoprocess based solution. How long will it
take to create wasm compilers for every language out there?  Containers are already more safe than a
stand alone solution, but not all software that is deployed uses containers.  Even the Bytecode
Alliance solution requires some level of manual auditing to ask "hey, why does package X require
write access to the filesystem?".

How often are you vigilant when installing android apps when it asks if you want to give an app
permission to read/write to the filesystem, or have access to the videocam or microphone?  Most
often, we don't care, or we don't think about the ramifications.  For some, it may be an obvious red
flag ("why does an app that retweets need read access of my filesystem?"), but for other apps, it
may not be clear why the app needs certain privileges.

I've also often said that technology doesn't really solve problems, it just creates new ones.  It
solves one problem but trades it for another.  The question is, can you live with the new problem?
And for how long?  You may think, "but Sean, json or yaml are clearly better than XML.  Isn't it
great that we've moved from XML to json or yaml?  What are the downsides?".  The problem it
introduced was having to learn a new way to do things, and also, json and yaml didn't really have a
way to specify schemas which lead to other problems.

Some will say that technology has provided us with new medicines that save lives or technologies
like compostable one use utensils or electric cars.  I would retort that, often, medicines may have
increased the longevity of life, but the cost (monetary or otherwise) to maintain that life
increases.  Or the side effects of the medicine or treatments degrades the quality of life.  For
environmental technology, compostable utensils still produce waste which can actually be harder to
compost than recycling is.  Worse, it makes people feel better about throwing things away instead of
reuse. And electric cars may not emit greenhouse gases, but the construction of the batteries and
other elements along with their disposal can be just as damaging to the environment as a very fuel
efficient car.

Everything in life is a trade off.  Some times technology has more payoff than liabilities, but then
the new technology can be a victim of its own success.  Clearly, automobiles were superior to horse
drawn carriages right?  Except that horse drawn carriages don't emit green house gases.

I believe that the real challenge is often social or cultural rather than technological.  In the
case of open source, I believe that the social solution (treating open source as a national
resource) is a better solution than technological.

In fact, technologies often just open up a can of worms, and more sadly, many in the software
engineering profession don't consider the ramifications of their actions.  A few weeks ago, I was
listening to a talk on National Public Radio about deep fake technology.  The technology to generate
a person's voice after listening to just 40min of data, and being able to manipulate video to match
the lip movement has become scary.  If you think there's "fake news" now...just wait.  What truly
disturbed me though, was when the host of the talk asked one of the inventors of the technology if
she thought about what it could be used for and what kind of damage it could do.  Her answer was
basically, "well, it's not my problem, and if I didn't do it, someone else would have".  Look at
Facebook's old "move fast and break things" motto (which they changed by the way).  I have come to
feel now, that software engineers need to take an ethics course in college just like health and
business majors do.

As engineers, I believe we have become conditioned to think of technology as our go to tool to solve
a problem.  We rarely, if ever, see technology _as_ the problem.

But, technology is here to stay.  Could we combine a technological solution with a social one.  For
example, if we went the route of making open source a publicly funded resource like highways,
schools or libraries, we could mandate having webassembly as a standard.  This way, you could
consume the libraries from whatever language you are most comfortable with.

## Conclusion

Clearly, open source is vital to today's information technology sector, and by extension, not just a
business's economic health, but an entire nation.

Open source is under attack not just from hackers, but often by demanding users who believe they are
owed something for free.  The risk of abandoned software is often high.  There aren't many good
solutions out there, and I seriously doubt (at least in America) open source will be treated as a
national resource that gets publicly funded.  Or for that matter, I don't know if people will even
like that idea as it's too socialistic for American tastes I think.

The other solution, technological, is I think chasing after a unicorn.  Engineers, being technical,
tend to think of problems as technical first, and cultural or social second.  But everyone would
have their own idea on how to solve this problem technologically, but how would you enforce this
system in a "weakest link" scenario?  It just takes one bad link to break the chain, so unless
everything is onboard with the technology solution it won't work.

Given the plethora or programming languages, operating systems, frameworks and libraries that all do
the same thing, I personally think it's a fools errand without also thinking about a social solution.

[-bytecode]: https://hacks.mozilla.org/2019/11/announcing-the-bytecode-alliance/
[-no-docker]: https://twitter.com/solomonstre/status/1111004913222324225
[-vulnerable]: https://arxiv.org/abs/1902.09217
[-sad-day]: https://words.steveklabnik.com/a-sad-day-for-rust
[-actix-web]: https://github.com/actix/actix-web
[-slide]: https://www.slideshare.net/reed2001/culture-1798664/49-Chaos_Emerges_High_Performance_EmployeesChaos