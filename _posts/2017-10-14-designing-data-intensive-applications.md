---
layout: post
title: Designing Data Intensive Applications 
---


# {{ page.title }}

## Chapter 1: Reliable, Scalable, and Maintainable Applications

Most applications today are data-intensive instead of compute-intensive. A data
intensive application might perform some of the following basic tasks:

* Store data, usually through a type of __database__
* Remember results of expensive operations, usually a __cache__ to speed up
  reads
* Send a message to another process to be handled asynchronously (__stream
  processing__)
* Periodically crunch a large amount of data (__batch processing__)

While these are the basic tasks, there's a lot of different types of databases
with different characteristics. In order to determine the right types of
systems, we need to look at the fundamentals of what it means to be:

* __reliable__ - system should continue to work correctly (performing the
  correct function at the desired level of performance) even in the face of
  adversity (hardware or software faults)
* __scalable__ - as the system grows (in data volume, traffic volume,
  complexity), there should be reasonable ways of dealing with growth
* __maintainable__ - Over time, many people will work on the system and they
  should all be able to work on it productively

### Types of Data Systems

Traditionally data systems have been relational databases and message queues.
They both store data for some time, but they're different due to their very
different __access patterns__, meaning broadly that we'll have much different
performance characteristics and implementations.

With newer tools like Redis (a datastore that can also be a message queue) and
Apache Kafka (message-queue with database-like durability guarantees), the
traditional categories have been blurred.

We also don't just use a single tool for all our tasks anymore. If we have 
additional pieces like an application-managed caching layer (e.g. Memcached) 
or a full-text search server (e.g. Elasticsearch or Solr), we create application
code whose job is to stitch these systems together (i.e. application code is
responsible for keeping those caches and indexes in sync with the main
datbase). The service's interface or API hides the details from others
(e.g. the cache will be correctly invalidated or updated on writes, but those 
details don't need to be seen.)

### Reliability

So reliability can be broken down into a few things, including:

* Application performs the function that the user expects
* The application can tolerate the user making mistakes or or using the
  software in unexpected ways
* The performance is good enough for the required use case, under the expected
  load and data volume
* The system prevents any unauthorized access and abuse


#### Not Reliable

So what happens when things go wrong? Some general definitions are:

* __faults__ are the things that can go wrong. Note that a fault is not the
  same as a failure (a fault is failure of a single component, whereas a 
  failure is the entire system)
* __fault-tolerant__ or __resilient__ means that the system can anticipate
  and cope with faults. Note that there is no system that is entirely
  tolerant of every possible kind of fault (e.g. Earth is destroyed)
* a __failure__ is when the _system as a whole_ stops providing the required
  service to the user (not just a single piece)

Some places like Netflix have the Chaos Monkey that deliberately introduces 
faults so we can ensure a fault-tolerant system. Some things we want to prevent
rather than cure because there is no cure (e.g. a data breach).

##### Hardware Faults 

Hardware fails, whether its a hard disk crashing, RAM becoming faulty, the
power grid has a blackout, or a network cable is unplugged. Hard disks have
a mean time to failure (MTTF) of about 10 to 50 years. If we have 10,000 disks,
there is an average of a failure of one disk to die per day. 

We can add redundancy to individual hardware components. When one system dies,
then another takes its place. Having additional systems also has operational
advantages so a server can reboot with patches (i.e. can do a 
__rolling upgrade__ by patching one server at a time).

#### Software Errors

Another class of faults is a __systematic error__ within the system. These
might be a software bug that causes every instance of an application server to
crash when given a bad input or a runaway process that uses up some shared
resource like CPU time, memory, disk space, or network bandwidth. These issues
are harder to catch and there is no quick solution. You have to try to think
through assumptions and interactions in the system, try to isolate the process,
measure and monitor the system behavior in production.

#### Human Errors

Humans are unreliable, even though we have the best intentions. We instead need
to approach our system designs with this in mind:

* Our system should minimize opportunities for error. Create well-designed
  abstractions, APIs, and admin interfaces to make it easy to do the right
  thing and discourage the wrong thing.
* Decouple places where people make the most mistakes from the places where
  they can cause failures. Provide fully featured non-production sandbox
  environments where people can explore and experiment safely using real data.
* Test thoroughly at all levels, from unit tests to system integration tests
  and manual tests
* Make it easy to recover from a human failure. E.g. Make it fast to roll back
  configuration changes or to roll out new code gradually and provide tools to
  recompute data in case an old computation is incorrect.
* Set up detailed and clear monitoring, including performance metrics and error
  rates (aka __telemetry__).

### Scalability

__Scalability__ is how a system is able to cope with increased load. It is not
a plain "X is scalable" or "Y does not scale". Instead, we need to describe how
a system copes with growth, whether that's computing resources, memory, network
bandwidth, etc.

#### Load

__Load__ can be described with a few numbers called __load parameters__; these
load parameters depend on the system architecture. You might have requests per
second on a web server, ratio of reads to writes in a database, or the hit rate
on a cache. 


##### Twitter Load Example

Twitter's two main operations are posting a tweet and reading from their home
timeline.

* Post tweet - A user can publish a new message to their followers (4.6k 
  requests/sec on average, over 12k requests/sec at peak)

* Home timeline - A user can view tweets posted by the people they follow (300k
  requests/sec).

The issue Twitter faced was __fan-out__, where each user follows many people
and each user is followed by many people. A couple ways to handle this problem
is to:

1 Posting a tweet inserts the new tweet into a global collection of tweets.
  When a user requests their home timeline, look up the people they follow,
  find all their tweets, and merge them (sorted by time).

  Code Example:

    SELECT tweets.*, users.* FROM tweets
      JOIN users ON tweets.sender_id = users.id
      JOIN follows on follows.followee_id = users.id
      WHERE follows.follow_id = current_user

2 Maintain a cache for each user's home timeline (a mailbox of tweets for each
  recipient user). When a user posts a tweet, look up all the people who follow
  that user, and insert the new tweet into each of their home timeline caches.
  Reads to the home timeline are then cheap.

The tradeoffs are that for the first approach, the systems struggled to keep up
with the load of home timeline queries due to the joins. The second approach
reads much quick, but requires to do more work at write time. There was an edge
case scenario with the second approach due to high number of followers per user
(e.g. Justin Beiber). Since he has over 30 million followers, a single tweet
results in over 30 million writes to home timelines. This a good scenario where
we should look beyond the _average_ load parameters and look at say the 99th
percentile since they're higher influencers. In the end, Twitter is using
a hybrid approach depending on whether you have a large number of followers or
not.

#### Describing Performance

Let's look at what happens when load increases in a system through two ways:

1. When you increase a load parameter and keep the same system resources 
   (CPU, memory, bandwidth), how is the performance of the system affected?
2. When you increase a load parameter, how much do you need to increase the
   resources if you want to keep performance unchanged?

#### Latency and Response Time

__Response Time__ is what the client sees: besides the actual time to process
the request (the __service time__), it includes network delays and queueing
delays.

__Latency__ is the duration that a request is waiting to be handled, during
which it is __latent__, awaiting service.

In reality when clients make requests over and over, we'll get slightly
different response times each time. That means we can't measure response times
as a single number, but a distribution of values that you can measure.
Sometimes you'll see the average response time, but this is not a very good
metric since it hides the delays of your outliers.

Instead, we want to use __percentiles__ to see say the 50th, 95th, 99th, 
and 99.9th percentiles. You want to pay special attention to response times
in the 99.9th percentile (__tail latencies__) because the customers with 
the slowest requests are usually the ones with the most data in their 
accounts (and thus most valuable customers). Percentiles are used in 
__service level objectives (SLOs)__ and __service level agreements (SLAs)__ 
that define the expected performance and availability of a service.

Since a server can only process a small number of things in parallel (e.g.
limited by number of CPU cores), it only takes a small number of slow requests
to hold up the processing of subsequent requests (aka __head-of-line
blocking__).

If you have a microservice architecture where a single end-user request hits
multiple backend calls, the end-user needs to wait for the slowest of the
calls to complete. Even if a small percentage of this is slow, you can end with
very high wait times due to __tail latency amplification__.

You can check response times for all requests within a time window and sort
that list every minute or you can get approximations of percentiles with
algorithms like __forward decay__, __t-digest__, or __HdrHistogram__.

#### Coping with Load

Now that we can describe and measure load, we can try to architect for an
appropriate level of load. This means that if we architect for say 2 or 3 times
the load, it might not be the same as architecting for 10 times that load.

When coping with load, you can __scale up__ (aka __vertical scaling__) by
making a machine more powerful or __scale out__ (aka __horizontal scaling__,
__shared-nothing__ architecture) by spreading the load across multiple 
smaller machines. A single machine can often be simpler, but very costly.

Some systems are __elastic__, meaning automatic computing resources are added
when they detect a load increase while other systems require manual
intervention for adding resources.

Moving from a single machine into a distributed stateless service across
multiple machines can introduce a lot of additional complexity. You should
consider the problems you are trying to solve before rearchitecting your
system; the problem(s) might be the volume of reads, the volume of writes, the
volume of data to store, the complexity of the data, the response time
requirements, the access patterns, or some mix.

For example, a system designed to handle 100k requests per second, each 1kb in
nsize, looks very different from a system that is designed for 3 requests per
minute, each 2GB in size, even though the two systems have the same data
__throughput__ (the number of records we can process per second, or the total
time it takes to run a job on a dataset of a certain size).

### Maintainability

The majority of the cost of software isn't on the initial development, but in
its ongoing maintenance like fixing bugs, investigating failures, modifying
the existing system for new use cases, and adding new features. We should
design software meant to minimize pain during maintenance by looking at three
design principles in particular:

* __Operability__ - make it easy for operations teams to keep the system
  running smoothly
* __Simplicity__ - make it easy for new engineers to understand the system 
* __Evolvability__ (aka __extensibility__, __modifiability__, __plasticity__) - 
  make it easy for engineers to make changes to the system in the future

#### Operability

"Good operations can often work around the limitations of bad or incomplete
software, but good software cannot run reliably with bad operations". A good
operations team keeps a software system running by:

* Monitoring the health of a system
* Tracks down causes of problems such as system failures or poor performance
* Keeps software up to date
* Keeps tabs on how different systems affect each other
* Capacity Planning
* Establish good practices and tools for deployment, configuration management
* Avoid dependency on individual machines

#### Simplicity

Small projects have simple code, but as projects get larger, they become
complex and difficult to understand. Complexity slows down everyone who needs
to work on the system and increases the cost of maintenance and increases the
number of bugs when changes happen. We want to remove __accidental
complexity__, which is defined as: complexity is accidental if it is not inherent
in the problem that the software solves (as seen by the users) but arises only
from the implementation. We can usually use __abstraction__ to help with complexity
by hiding the implementation details behind a clean, simple to understand facade.

#### Evolvability

Make changes easy. Your system requirements probably change frequently from new
business requiremnets or new cases emerge.

## Chapter 2: Data Models and Query Languages

Data models are the one of the most important pieces of developing software
because it is not only how the software is written, but also how we think about
the problem that we are solving. We usually layer one data model on top of
another, with each layer thinking "how is it represented in terms of the
next-lower layer"?

1. As an application developer, you look at the real world (e.g. people,
   organizations, actions) and model it in terms of objects or data structures
   with APIs that manipulate those data structures.
2. When you want to store those data structures, we express them in
   general-purpose data models like JSON or XML documents, tables in
   a relational databases or a graph model.
3. The engineers who built the database software decided on representing the
   JSON/XML/relational/graph data in terms of bytes in memory, on disk, or on
   a network. The representation allows the data to be queried, searched,
   manipulated, processed in different ways.
4. On lower levels, hardware engineers represent bytes in terms of electrical
   currents, pulses of light, etc.

The idea is that each layer hides complexity of the layers below it by
providing a clean data model.

### Relational Model vs Document Model

The best known relational model is __SQL__; data is organized into
__relations__ (aka __tables__ in SQL), where each relation is an unordered
collection of __tuples__ (aka __rows__ in SQL).

#### NoSQL

__NoSQL__ isn't a particular technology, it just stands for Not Only SQL. NoSQL
stands to do:

* Scale better than relational databases, with more focus on very large
  datasets and very high write throughput
* Specialized query operations that are not suppported by the relational model
* More dynamic data models than those of the relational schemas

Since SQL and NoSQL solve different problems, these two technologies can be
used together (aka __polygot persistence__).

#### Object-Relational Mismatch

Most application development is done using object-oriented programming
languages, but data is stored using the SQL data model in relational tables.
There is this awkward mismatch between transitioning objects to tables/rows/columns
called __impedance mismatch__.

