# Doing it all wrong - Testing

So, lately, I've come to realize that how my company views quality engineering just simply is too narrow minded.
And again, I realize that this is a cultural problem, not a technical one.

So, why do I think we are doing testing wrong?

## The old guard: SLC

I think we're still too stuck in the traditional life cycle model of testing.  Sure, we talk about CI and CD, 
and how awesome it is.  But when it comes to Agile Testing they dont have a clue.

Ideally, in Agile model, you have three groups of workers coming together:

- Product Managers: who interface with customer and gather requirements/features
- Developers: who mostly implement the requirements
- Testers: who makes sure that the product is doing what it is supposed to be doing

But, this is not a linear setup.  It's not PM hands off to Devs who then hand off to test.  All 3 are simultaneously
working together, working in a kind of feedback loop with each other.  Testers and Developers can become customer
advocates and ask why

### Mistaking a symptom of a problem as its cause

Very often, I get the feeling that a business or team wants to do metrics, thinking that somehow they will glean
some kind of causality out of this information.  I see it when scrum teams try to associate story points with 
time estimates, or in certain MBO (Management By Objective) goals.  

Let me give a little story that may not be popular with a particular group of people, but I'm going to tell it 
anyway.  I am half-Filipino (well, sorta...it's complicated :)), so I have been surrounded by Filipinos my entire
life and keep up with the news there.  The Philippines is a very poor country, and it's been wracked by poor 
leadership.  If you ask the average Filipino why they think the country is the way it is, the #1 cause will be
"Corrupt politicians".

The problem as I see it, is not that the Philippines is poor because its leaders are corrupt.  They haven't dug
deep enough yet.  As I tell Filipinos, corruption is a symptom of the disease, not its cause.  Unfortunately, 
there are some cultural aspects in the Philippines that lead to leadership that is like this.  For example, a 
common expression in Tagalog is "Bahala na", which loosely translates as "whatever" or "dont worry about it" or
"God will take care of it". Another common expression is "puede na yan", which essentially means "it's good 
enough". This lackadaisical attitude becomes a breeding ground for leadership that takes advantage of people.

You think the Filipinos I tell this to like it?  The majority don't, but a few understand the point I am trying
to make.  It's easy to blame politicians for problems, but it's not so easy when you have to look in the mirror 
and realize that maybe your ideas and behavior are part of the problem.  No one likes to do that. 

