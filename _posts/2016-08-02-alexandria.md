---
layout: post
title:  "A document management system"
date:   2016-08-01 16:41:00 -0400
categories: purescript parsing
---
I decided that there needs to be a tool for companies to store documents necessary for software projects.  Having used
tools like Rational Tools and Polarion, there had to be something that was enterprise quality, but still more nimble
and easy to use for open source projects.

# What kind of documents?

In a software project of moderate size, it becomes very useful to document in a formal manner various information.

- User stories
- Feature requirements
- Design notes or blueprints
- Test Case documentation
- Test Plan information
- Test results
- How-Tos and FAQs
- Wiki pages
- Graphs, charts, whiteboard gliffys or other visual documentation
- Blog information
- Bug or defect information.  Post mortem analysis
- Chat logs
- email threads
- Logging files

## Rationale

There are basically 2 problems to tackle:

1. **Generating information useable for others**
2. **Finding information a user needs**

I really haven't seen any product that addresses these issues that can be used for both enterprise and open source
projects.

The two points above are somewhat interrelated.  First off, it can be a pain for developers, test engineers, product
owners, upstream community members or any other number of people to simple author some documentation.  Is it possible to
make documentation actually....gasp...fun?  Or at least a little less agony or boredom inducing?  The second problem is
that documentation is often buried all over the place.  It could be in email threads, chat logs, comments in code, etc
etc.  What good is the information age if you can't find the info you're looking for??  That's like having a vault of
billions in gold with no key to unlock it.  Useless.

The idea behind alexandria is to address the key points above and to be a one stop shop for all your information needs.
Note that there are subtle implications for the points above.  For example, once a user finds information they were
looking for, in what format should they get the information back?

### For developers

Generally speaking developers hate to write documentation.  Tools like reStructuredText and markdown help however.
Javadocs are often less than helpful, but how do you encourage developers to code in a literate style? Having plugins or
modules that can generate documentation from code would be useful.  Tools that analyze comments in code such as flagging
a comment which had its related code changed, but the comment itself did not change.

Other than the 3rd party modules (like polarize) to help generate documentation, this is admittedly a hard task to take
on.  Moreover, getting developers to document more would be like pulling teeth.

### For upstream community

Another consideration is making it easier for other types of contributors.  While wikis are capable of doing this, there
should be an easier and better model than wiki pages.  I've analogized Wiki pages as trying to find 2 matching pairs of
socks in a messy drawer.  Ideally, documentation for a project should be like a book.  It should have a table of
contents, an index, and some kind of logical structure.  But how does a group author a "book"?

For starters, one can define "chapters".  This would be akin to "tags", with the exception that chapters have a stronger
ordering associated with them.  Many blogs for example have tags, but these are just categories.  When a reader wants
to know about a project, the first thing he will do is try to find documentation.  When a project has a "Start here"
page, that a good start, but often that's just an introduction.  Alexandria should help people author good documentation
by helping them structure the logical presentation of documents.

Another important aspect for "outside" contributors is the ability to send in files which can be imported.  The idea
for alexandria is that even though it will be open source, a company may decide to not allow public access.  But the
upstream community still needs a way to be able to review and collaborate on documents which are publicly available.
Therefore, the ability to import well known file formats is paramount.

### For product owners or end users

From a user story or requirements point of view, often, there is little collaboration between Product Owners, developers
and testers.  One goal of alexandria is to foster better communication between these groups.  BDD style feature files
are an excellent way of both generating a formal requirement, hashing out user stories, and creating a basis for tests.

alexandria should support a modified gherkin syntax to allow for metadata about the requirements to be stored.

### For testers

How many test case management systems have you used that shoe horn you into that tools way of thinking what a test case
is?  Maybe it didn't support data driven tests.  Maybe it assumed all tests were unit tests.  Or integration tests.  

The test case documentation system has to be flexible enough to allow user defined fields.  It shouldn't assume that
only the QA organization will be interested in the test case definitions.  Whether they are black box, white box, or
something in between, it shouldn't matter.

### For end users

How many times have you tried to find some tidbit of knowledge, but it took you hours to find it?  Google is great,
especially if the documentation is on the web on a public server.  But what about email threads?  Chat logs?  Wikis
behind some firewall?  Code comment?  Who knows, maybe the info was buried in a log file statement.

How many times have you tried to find some tid bit of knowledge, but it took you hours to find it?  Google is great,
especially if the documentation is on the web on a public server.  But what about email threads?  Chat logs?  Or wikis
behind some firewall?

Lucene comes in handy, but sometimes it's still too overwhelming.  For power users, there needs to be a way to search
for information.  Searching by string is one way to do it (and lucene works great for that), but what if what you are
seeking is relational in some form?  Perhaps your first thought is to perform some kind of SQL query.  But there's an
even better way.  By storing related information as graphs, you can get much better performance, not to mention visual
information about relationships.

What if there was a way to automatically link information in email threads and to give them categories and store them
as graphs?  What about chat logs?  What if we could sort chat logs based on keywords and graph the conversations?

## What would it do?

This document system I propose should be able to do the following:

- Have a configurable markup syntax (or at least choose from several styles)
- Allow generation of documents from within alexandria or import external files
  - Import Word, doc, rst, md, csv and other common text based formats
  - Create a universal document formal data structure that can be imported
    - Create XML, YAML, JSON, EDN and msgpack representation
- Allow upstream community to collaborate on or review documents
- Link documents to source code
- Plugins for popular testing frameworks to generate documents
- Easy and advanced querying facilities (based on graph theory or simple lucene searching)
- whiteboard space
- *real time collaboration*?  
- *natural language processing* for email threads, chat conversations and logging files

\* Not sure about these since (especially NLP due to difficulty) but these would be very nice to have


Generally, alexandria is about plain text documentation with a few exceptions.  First, simply due to its ubiquity, it
will need to support importing of Word docs.  The same is true for google docs.  It must also support storage of visual
formats like pdfs, svgs, pngs, and perhaps gliffy files.

However, internally, it will only edit or author plain text.  A proposed whiteboard format will be a graphviz style
format and thus still be text based. Binary or proprietary formats will be read and link only.  Conversion of a
proprietary format to some plain text format is possible though.

# The technology stack

This isn't rocket science.  It's basically a database with a web front end.  

- Arangodb or orientdb for database backend
  - Documents will be linked as a graph database
  - Allow configuration support for clustering HA mode
- Web front end
  - Front end in purescript using halogen (react-like FRP)
  - Websocket (either do FFI using node's socket.io or purescript-websocket)
  - *templating engine* (haven't looked into template support in purescript/javascript)
  - The back button in the browser should be a back button!!!
- Server in haskell (or purescript since TLS support is sketchy in haskell)
  - Websockets for aysnchronous communication to central server
    - No REST API! More like RPC
  - Requests will first go to a message bus and server will pull them off
- Hooks into git and mercurial
  - Documents are plain text and under revision control
    - Documents can either live in their own repository or as part of the project code
  - Allow linking document version to revision

## Architecture

**Entities:**

Document- A user story, requirement, test case, test plan, article, design note, etc.  A document will be a graph node
  - Content- the actual information of a document
  - Edge- A Document type will have a link, which is just a reference to another document, or to a project
  - Project- the code repository
  - Author- the user who generates, edits or reads documents

Library- alexandria's data server.  It holds the actual content data files
Catalog- alexandria's database.  These hold all the data entities (not the content but the graphs and edges)
Librarian- The service that performs querying and searching of different types of data
Requester- The message bus that connects clients to the Librarian
