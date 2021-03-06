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
reads much quicker, but requires to do more work at write time. There was an edge
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

* Scales better than relational databases, with more focus on very large
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

#### Resume Example

Let's look at how a resume might be expressed in a relational schema.

##### One-to-One Relationships 

For a resume, we have one-to-one fields like a user and their name.

* We have a unique identifier `user_id`
* We have fields `first_name` and `last_name` appear only once per user, so we
  create these columns in a `users` table

##### One-to-Many Relationships 

For a resume, we have one-to-many relationships. People have more than one job 
in their career and more than one periods of education or contact information 
(__one-to-many__ relationship). We can express this a few ways:

* one-to-many can be separate tables (e.g. education, contact, jobs)
* we can add support for structured datatypes like JSON or XML data so that 
  multi-valued data is stored in a single row (allowing the db to query and 
  index these documents)
* we can encode education, jobs, contact info as a JSON or XML document,
  store it in a text column in the database, and let the application interpret
  its structure and contents (usually unable to have the db query for values
  inside the encoded column)

If we go the JSON or XML representation, we have better __locality__ than the
multi-table schema, meaning when we access the data, we do not need to perform
multiple queries and joins between all the tables; instead we only have one
query with all the information in one place.

##### Many-to-One and Many-to-Many Relationships

In a resume, if there are free-text fields, it makes sense to store that as
plain text strings. However, for fields like `region_id` and `industry_id`, we
want to use an id instead of plain text like `Greater Seattle Area` and
`Technology`. Users can then choose from a drop down list to help with:

* Consistent style and spelling
* Avoid ambiguity (e.g. several cities with the same name)
* Ease of Updating (e.g. city changes name)
* Localization support
* Better Search (e.g. search for in state of WA can have Seattle listed as in WA)

You can technically store the text, but using an ID helps because it has no
meaning to humans so it never needs to change. Anything meaningful to humans
might need to be changed. Removing duplicated information is the idea of 
__normalization__ in databases.

With __many-to-one__ relationships (many people live in one region, many people
work in one industry), it won't fit nicely in a document model (unlike
a relational database where it's normal to refer to rows in other tables by
ID). In document databases, we don't need joins for one-to-many tree structures
(e.g. education, jobs). So what do we do? We can shift the joins from the
database to the application code (w/ multiple queries, then doing a join), but
then that really doesn't have the database solve the issue.

The same issue that document databases encounter for one-to-many relationships
is the same issue as for __many-to-many__ relationships. If there are many of 
these types of joins, then a relational model is better. However, if we want
more schema flexibility and better performance due to locality with one-to-many
relationships, the document database might work better.

In a relational model you can take a document-like structure (i.e. a tree of
one-to-many relationships) and split this document into multiple tables 
(aka technique of __shredding__), but can lead to cumbersome schemas and
unnecessarily complicated application code.

If data is very highly interconnected, the document model is not the way to go,
the relational model is okay, and __graph models__ are the most natural.

##### Relationship Summary

The main idea is that you want to have simple application code and in order to
accomplish this, we pick the right database depending on the types of
relationships that exist between data items. Usually:

* mostly one-to-many relationships with nested records, use document database
* mostly many-to-one and a few many-to-many relationships, use relational database
* highly interconnected data (i.e. lots of many-to-many relationships), use graph models

#### Schemas

Relational and Document databases handle schemas a bit differently.
Most document databases and relational databases that support JSON do not
enforce any schema on the data in documents. XML support in relational
databases usually have an optional schema validation.

* Document databases are sometimes called __schemaless__, but it really means
  that its __schema-on-read__, where the structure of the data is implicit and
  only interpreted when the data is read.
* Relational databases have __schemas__ and are __schema-on-write__, where the
  schema is explicit and the database ensures all written data conforms to it

##### schema-on-read vs schema-on-write

So to an application developer, this idea of schema-on-read vs schema-on-write
is comparable to type checking in programming languages.

* schema-on-read is like dynamic (runtime) type checking like python
* schema-on-write is like static (compile-time) type checking like java

So how does this affect things?

* On a document database (schema-on-read), if we have the name field and want 
  to store this into two separate fields of first name and last name, we write 
  the application code to create a new field.
* On a relational database (schema-on-write), we need to perform a database
  migration like (`ALTER TABLE users ADD COLUMN first_name text; ...`)

Schema changes are usually pretty quick (except for MySQL, which copies the
entire table, meaning minutes or even hours of downtime for larger tables).

So when is it good to have a schema?

* schema-on-read is good when items in the collection don't all have the same
  structure for some reason (i.e. data is heterogeneous). The structure of the
  data might be determined by external systems and may change at any time.
* schema-on-write is good when all records are expected to have the same
  structure so we can document and enforce that structure

### Imperative vs Declarative Languages

We have __imperative languages__ (most programming languages, like Python) that
tells the computer to perform certain operations in a certain order. We can
step through the code line by line.

On the opposite end, we have __declarative languages__ (like SQL, CSS, or relational
algebra), where you specify the pattern of the data you want, but not HOW to
achieve that goal. This type of language is more concise and easier to work
with and hides the implementation details of something like the database engine. 
This allows for automatic optimizations and usually allows parallel execution.

#### MapReduce

__MapReduce__ is kinda in the middle between an imperative and declarative
language. You run snippets of code repeatedly by a processing framework. This
code does a __map__ (aka __collect__) and __reduce__ (aka __fold__,
__inject__). An example of this might be to map how many sharks you saw per month.

In a relational database like Postgres, this might look like:

    SELECT date_trunc('month', observeration_timestamp) AS observation_month,
    SUM(num_animals) AS total_animals
    FROM observations
    WHERE family = 'Sharks'
    GROUP BY observation_month;

In a MapReduce like MongoDB, we have:

    db.observations.mapReduce(
        function map() {
            var year = this.observationTimestamp.getFullYear();
            var month = this.observationTimestamp.getMonth() + 1;
            emit(year + "-" + month, this.numAnimals);
        },
        function reduce(key, values) {
            return Array.sum(values);
        },
        {
            query: { family: "Sharks" },
            out: "monthlySharkReport"
        }
    );

We group by a key (in this case, the year and month combined as a key like
'2017-10') and emit the value (number of animals in that observation).
  
The __map__ and __reduce__ functions must be __pure functions__, meaning they
ONLY use the data passed to them as input and cannot do additional database
queries or have any side effects. This allows the database to run the functions
anywhere, in any order, and rerun them on failure in a distributed execution
environment on a cluster of machines.

### Graph Databases

If many-to-many relationships are very common in your data, then consider
a __graph database__. A graph has two kinds of objects: __verticies__ (aka
__nodes__, __entities__) and __edges__ (aka __relationships__, __arcs__).
Examples are:

* Social Graphs - vertices are people and edges are how people know each other
* Web Graphs - vertices are web pages and edges are links to other pages 
* Road or Rail Networks - vertices are junctions and edges are roads/highway
  lines

Graphs are not limited to __homogeneous data__ (same types of data, e.g. web
page link to other web pages). We can have a single graph link people to
locations, events, checkins, comments, etc.

We can structure and query data using the __property graph__ model (e.g. used
by Neo4J, Titan, and InfiniteGraph) and the __triple-store__ model (e.g. used 
by Datomic, AllegroGraph)

I don't have much experience with Graph databases, but I think the idea is that
they are the _opposite_ of document databases. In graph databases, anything is
potentially related to everything. In document databases, the target use case
is that data is self-contained documents and relationships between one document
and another are rare.

## Chapter 3: Storage and Retrieval

A database stores data and when you ask for it later, it returns data. How the
storage engine works internally will help you know what type of engine to
pick, with the reason being that there is a big difference between storage
engines that are optimized for __transactional__ workloads versus those
optimized for __analytics__. Since we talked about relational databases and 
NoSQL databases, we'll look at __log-structured__ and __page-oriented__ storage 
engines.

### First Principles Database

Let's look at the simplest database created using two bash functions to create
a key-value store. `db_set key value` will store key and value in the database.
`db_get key` will get the most recent value with that key. The idea is we have
a text file where each line is a key-value pair separated by a comma (ignoring
escape issues). When a new `db_set` is done, the old value is not overwritten,
we just look at the last occurrence of the key with `tail -n 1` in `db_get`.

    #!/bin/bash

    db_set () {
        echo "$1,$2" >> database 
    }

    db_get () {
        grep "^$1," database | sed -e "s/^$1,//" | tail -n 1
    }

Example Usage

    db_set 42 '{"name": "San Francisco", "attractions": ["Golden Gate
    Bridge"]}'

    db_get 42
    '{"name": "San Francisco", "attractions": ["Golden Gate
    Bridge"]}'
 
Performance

The `db_set` performance is good because appending to a file is very 
efficient. Many databases do something similar internally using a __log__, 
an append-only sequence of records.

The `db_get` performance is bad with a large number of records because we have
`O(n)` lookup costs; if we double our records, we double our search times.

#### Index

If we want to efficiently find the value for a specific key in a database, we
need an additional data structure called an __index__, with the general idea of
keeping additional metadata on the side, acting as a signpost to help you
locate the data you want. If you want to search data in different ways, you can
have different indexes on different parts of the data.

The benefit of indexes is that it helps with the performance of some read queries
at the cost of adding overhead on writes, with every write also needing to write 
to the indexes.

There are various types of indexes, including:

* __hash indexes__ 
* __sstables__ and __lsm-trees__
* __b-trees__

##### Hash Indexes

Hash indexes are indexes for key-value data. Key-value stores are basically 
like the dictionary data type in programming languages and are implemented 
as a hash map/hash table.

The idea is that you have an in-memory hash map where every key is mapped to
a byte offset in a data file (location where the value can be found).

An example storage engine with hash indexes is __Bitcask__ (default storage 
engine in __Riak__). Bitcask has high performance reads and writes, but the 
requirement is that all keys fit in RAM since the hash map is kept in memory.

When to use? If you have a situation where the value for each key is updated
very frequently and can fit in memory, then this storage engine would be a 
good fit. A key might be a URL of a link and the value is the number of times
it has been accessed (incremented each time there is a visit). There are a lot
of writes, but not too many distinct keys.

With our example we are only appending to a file, which would lead to running
out of disk space. We can break the log into segments when a segment reaches
a certain size, then make writes to a new segment. This allows __compaction__
on the segments, meaning we throw away duplicate keys in the log and keeping
only the most recent value for each key. Since compaction makes segments
smaller by removing duplicate values on keys, we can also merge segments
together (into new segments).

Each segment has its own in-memory hash table mapping keys to file offsets. To
find the value for a key, we check the most recent segment's hash map (and if
not present, then check the second-most recent segment, etc).

##### SSTables and LSM-Trees

Remember that each log-structured storage segment is a sequence of key-value pairs.
These pairs appear in the order that they were written and values later in the log
take precedence over values for the same key earlier in the log.

__Sorted String Table__ (aka __SSTable__) is where the sequence of key-value
pairs are sorted by key. There's some advantages over a regular hash index:

* merging segments is simple and efficient, even if files are larger than memory;
  you start reading the input files side by side, look at the first key in each file,
  copy the lowest key (according to sort order) to the output file, and repeat to
  get the final result of a new merged segment file that is sorted by key. If there
  are multiple segments containing the same key, we just keep the value from the most
  recent segment and discard older values
* to find a specific key, you don't have to keep an index of all the keys in
  memory; e.g. say a key is sorted alphabetically and you're looking for the key
  for 'cat'. If you have the keys and offsets for 'apple' and 'dog', you know that
  'cat' is somewhere inbetween.
* We can group records into a block and compress it before writing to disk. Each entry
  of the sparse in-memory index then points at the start of a compressed block.

###### Keeping a maintained order for SSTables

So the issue is that after you sort your data, how do you keep sorting for new writes?
You can look up data structures like __red-black trees__ or __AVL trees__. With these
data structures, you can insert keys in any order and read them back in sorted order.

* When a write comes in, add it to an in-memory balanced tree data structure (e.g.
  red-black tree) called a __memtable__.
* When the memtable gets larger past a threshold, write it out to disk as an SSTable file
* This new SSTable file is the most recent segment of the database
* When a read request comes in, look for the key in the memtable first, then on the most
  recent on-disk segment, then in the next-older segment, etc.
* Every now and then, run a merging and compaction process in the background to combine
  segment files and to discard overwrriten or deleted values

This works well except for database crashes since the most writes are in the memtable
and have not been written to disk yet. To solve this problem, we can keep a separate
log on disk where every write is immediately appended. Use this only to retore the memtable
after a crash. Discard this log every time the memtable is written to disk.

We can create __Log-Structured Merge-Tree__ (aka __LSM-Trees__) data structures out of
SSTables. There are storage engines built out of this concept, including:

* LevelDB
* RocksDB
* Cassandra
* HBase

###### SSTables and Full Text Searches

Lucene (used by Elasticsearch and Solr) use a similar method for storing its
__term dictionary__. A full-text index is more complex than a key-value index, but
runs on a similar idea: given a word in a search query, find all documents that
mention the word. The key is a word (a __term__) and the value is the list of IDs of all
the documents that contain the word (the __postings list__)

###### SSTables and Storage Engine Optimizations

* LSM-tree alogirthms can be slow when looking up keys that do not exist in the database (e.g.
  memtable, then most recent segment to oldest segment). You can use __Bloom filters__, an
  memory-efficient data structure for approximating the contents of a set to help tell if a
  key does not appear in the database
* There are different strategies for the order and timing of how SSTables are compacted 
  and merged, including __size-tiered__ and __leveled__ compaction.
* In __size-tiered compaction__, newer and smaller SSTables are merged into older and larger SSTables.
* In __leveled compaction__, the key range is split up into smaller SSTables and older data is moved into
  separate 'levels', which allows the compaction to proceed more incrementally and use less disk space.

##### B-Trees

The most widely used indexing structure is the __B-Tree__. They are the standard index
implementation in almost all relational databases and many nonrelational databases.

__B-Trees__ is maintaining a sorted structure on disk (opposed to memory),
where the database is broken down into fixed-size __blocks__ (aka __pages__) of
about 4KB in size and read or write one page at a time.

B-Trees need to maintain a write ahead log.

##### Comparing B-Trees and LSM-Trees

Usually LSM-Trees are typically faster for writes and B-Trees are faster for reads.

# Data Warehousing

So in a database, you have __transactions__ that mean a group of reads and writes. For transactions, you can have
__transaction processing__, which means allowing clients to make low-latency reads and writes. On the opposite side
are __batch processing jobs__, which only run periodically (e.g. once a day).


## OLTP - Transactions based

The basic access pattern for most businesses was to look up a small number of records by a key, using an index.
The records are inserted or updated based on the user's input; because these applications are interactive,
the access pattern became known as __online transaction processing__ (__OLTP__).

For OLTP, we have two main schools of thought for storage:

* __Log-structured__, which only permits appending to files and deleting obsolete files, but never updates a file that
                      has been written (e.g. Cassandra). These enable higher write throughput due.
* __Update-in-place__, which treats the disk as a set of fixed-size pages that can be overwritten (e.g. B-Trees, often
                       used in relational databases)

## OLAP - Analytics based

You can also use databases for data analytics, which has a much different access pattern than transactions.
Usually an analytic query scans over a large number of records, reads a few columns per record, and calculates
aggregate statistics (e.g. count, sum, average) instead of returning raw data back.

## OLTP vs OLAP

    PROPERTY                    OLTP                                            OLAP
    Main read pattern           Small number of records per query, uses key     Aggregate over large number of records
    Main write pattern          Low-latency writes from user input              Bulk import (ETL) or event stream
    Primarily used by           End user/customer, via web app                  Internal Business Analyst
    What data represents        Latest state of data (current point in time)    History of events that happened over time
    Dataset size                GB to TB                                        TB to PB 
    Bottleneck                  Usually disk seek time                          Issue is updates

When you run analytics on a separate database, this is called your __data warehouse__. It's separate from your
OLTP operations. The process of getting data into the warehouse is known as __Extract-Transform-Load (ETL)__.
So why use a separate data warehouse for OLTP vs OLAP? The indexing algorithms that work well for OLTP aren't very
good at analytical queries.

## Data Models

There's a wide range of data models used in transaction processing and fewer models used in data models for analytics.

Data models for analytics usually uses __star schema__ (aka __dimensional modeling__).

Other variations of the star schema is the __snowflake schema__, where dimensions are further
broken down into subdimensions.

### Star Schema

At the center of a schema is the __fact table__. Each row of the fact table represents
an event that occurred at a particular time. Each row of the fact table might be
analyzing website traffic (a page view or page click) or retail sales.

The name star schema comes from the fact that when the table relationships are visualized,
the fact table is in the middle, surrounded by its dimension tables.

__Fact Tables__

Usually facts are captured as individual events, allowing for maximum flexibility of analysis later.

`fact_sales` table

    date_key | product_sk | store_sk | promotion_sk | customer_sk | quantity | net_price | discount_price   |
    140102   | 31         | 3        | NULL         | NULL        | 1        | 2.49      | 2.49             |
    140102   | 69         | 5        | 19           | NULL        | 3        | 14.99     | 9.99             |
    140102   | 74         | 3        | 23           | 191         | 1        | 4.99      | 3.89             |
    140102   | 33         | 8        | NULL         | 235         | 4        | 0.99      | 0.99             |

Most of your data is in fact tables. Some of the columns in the fact table are attributes, but other columns
are foreign key references to other tables (dimension tables).

__Dimension Tables__

Dimension tables represent the who, what, where, when, how, and why of the event.

`dim_store` table

    store_sk | state | city
    1        | WA    | Seattle
    2        | CA    | San Francisco
    3        | CA    | Palo Alto

`dim_product` table

    product_sk  |  sku      | description   | brand     | category     |
    30          | OK4012    | Bananas       | Freshmax  | Fresh Fruit  |
    31          | KA9511    | Fish food     | Aquatech  | Pet supplies |
    32          | AB1234    | Croissant     | Dealicious| Bakery       |

`dim_date` table

    date_key    | year  |   month   | day   | weekend   | is_holiday
    140101      | 2014  |   jan     | 1     | wed       | yes
    140102      | 2014  |   jan     | 2     | thu       | no
    140103      | 2014  |   jan     | 3     | fri       | no

`dim_customer` table

    customer_sk |   name    | date_of_birth
    190         | Alice     | 1979-03-29    
    191         | Bob       | 1961-09-02
    192         | Cecil     | 1991-12-13

`dim_promotion` table

    promotion_sk    |   name        |   ad_type     | coupon_type
    18              | New Year Sale | Poster        | NULL
    19              | Aquarium deal | Direct mail   | Leaflet
    20              | Coffee Bundle | In-store sign | NULL

### Snowflake Schema

__Snowflake schema__ is where dimensions are further broken down into subdimensions.
The idea behind the snowflake schema is that the dimensions are normalized into
multiple related tables (instead of star schema's dimensions being denormalized with each
dimension represented by a single table).

Snowflake schemas are more normalized than star schemas, but star schemas are often
The principle behind snowflaking is normalization of the dimension tables by removing
cardinality attributes and forming separate tables.
preferred because they are simpler for analysts to work with.

### Row vs Column oriented storage

Once your fact tables become large (trillions of rows, hundred columns wide),
you'll find that a typical data warehouse query only accesses about 5 columns at a time.

In most OLTP databases, data is laid out in a __row-oriented__ fashion.
Even with indexes, you'll still be loading a lot of extra information
with a row-oriented storage engine.

The idea behind __column-oriented storage__ is that you don't store all the
values from one row together, but store all the values from each __column__ together.
An example of a column oriented storage is _Parquet_. Column oriented storage is
also better able to be compressed (with __bitmap encoding__ being particularly effective
in data warehousing). If there are a lot of zeros in most of the bitmaps, then
we say it is __sparse__. We get faster reads, but writes are more difficult. If you want
to insert a row in the middle of a sorted table, you most likely have to rewrite all
the column files.

### Aggregation with Materialized Views

Another data warehouse is __materialized aggregates__, where instead of having to
crunch through the raw data every time, we cache some of these counts/sums by
creating a __materialized view__. In a relational model, it is often defined like
a standard view (a table-like object whose contents are the results of some query).
The different is that a materialized view is an _actual copy of the query results
written to disk_.

#### Data Cube (aka OLAP cube)

A common special case of a materialized view is known as a __data cube__ or __OLAP cube__.
It is a grid of aggregates grouped by different dimensions.

Imagine that each fact has foreign keys to only two dimensional tables (`date` and `product`).
Each cell contains the aggregate (e.g. `SUM`) of an attribute (e.g. `net_price`) of all facts
with that date-product combination. Then you can apply the same aggregate along each row
or column and get a summary that has been reduced by one dimension (e.g. the sales by product
regardless of date, or the sales by date regardless of product)

In general, you often have more than two dimensions. If you had say five dimensions: date,
product, store, promotion, and customer, it's harder to imagine, but the idea is still the same:
each cell contains the sales for a particular date-product-store-promotion-customer combination.

Advantage of a materialized data cube is that queries are very fast
Disadvantage is that a data cube doesn't have the same flexibility as querying the raw data

Most data warehouses keep as much raw data as possible and use aggregates like data cubes
only as performance boost for certain queries.

