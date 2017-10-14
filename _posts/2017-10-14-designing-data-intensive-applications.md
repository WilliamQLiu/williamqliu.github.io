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

#### Performance

Let's look at what happens when load increases.

* When you increase a load parameter and keep the system resources (CPU,
  memory, bandwidth)

### Maintainability


