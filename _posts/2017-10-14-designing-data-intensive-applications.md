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

### Reliability

### Scalability

### Maintainability



