---
layout: post
title: High Level Data Processing Concepts
---


# {{ page.title }}

High Level Goal: Lower latency for data processing to get more timely data

Definitions:

* Streaming = Execution Engine designed for infinite data sets
* Unbounded Data = Infinite data set, (aka "streaming data") vs bounded data (aka "batch data")
* Unbounded data processing = Data processing on Unbounded Data

## Architectures

###Lambda Architecture

You run a streaming system alongside a batch system, both doing the same calculation.
You basically do a dual-mode execution.

* Streaming system gives you low-latency, inaccurate results (streaming system does not provide correctness or you use an approximation alogirthm)
* Some time later a batch system rolls along and provides you with the correct output

Pros:

* Get lower latency approximate results and later get accurate results

Cons:

* Build and maintain two independent versions of the pipeline
* Complicated merge process for the two pipelines

###Kappa Architecture

Run a single pipeline using a well-designed system that's built for the job at hand.

## Requirements

You need two things:

1. Correctness = consistent storage; streaming systems need a way for checkpointing persistent state over time.
  There needs to be consistency in light of machine failures. Strong consistency is required for __exactly-once processing__,
  which is required for correctness, which is a requirement for any system that wants to meet the capabilities of a batch system.
    - See [MillWheel: Fault-Tolerant Stream Processing at Internet Scale](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/41378.pdf)
    - See [Spark Streaming: An Efficient and Fault-Tolerant Model for Stream Processing on Large Clusters](https://people.eecs.berkeley.edu/~matei/papers/2012/hotcloud_spark_streaming.pdf)
2. Tools for Reasoning about Time = Need good tools for working with unbounded, unordered data of varying event-time skew

### Event Time vs Processing Time

* __Event Time__ = time at which events actually __occurred__
* __Processing Time__ = time at which events are __observed__ in the system

Not all use cases care about event times (if yours doesn't, life is easier). Most use cases care (e.g. fraud detection, user behavior).
In an ideal world, event time and processing time would equal, but that's not real life; instead you get a skew between the two.

### Windowing

To work with the infinite nature of unbounded data sets, systems typically provide some notion of 'windowing' the incoming data.
i.e. we chop up the data set into finite pieces along temporal boundaries
We have a couple options:

#### Use the processing time

Do not use the processing time if you care about correctness or if you care about the context of the event time.
Your event time data may end up in the wrong processing time window (e.g. due to the lag of distributed systems), throwing correctness out the window.

#### Use the event time

Since data is unbounded, we can have disorder and __variable skew__, causing a __completeness problem__ for event time windows.

## Data Processing Patterns

Let's look at the core types of usage patterns across:

* bounded and unbounded data processing
* two main types of engines (batch and streaming)

### Bounded Data

Processing bounded data is straightforward. We take a set of data and run it through a data processing engine (typically batch, e.g. MapReduce).
We end up with a useful output.

### Unbounded Data

#### Unbounded Data (Batch)

Batch systems have been used to process unbounded data sets. You slice up the unbounded data into a collection of
separate bounded data sets that are appropriate for batch processing.
Usually these events can be written into directory and file hiearchies with names that specify the window they correspond to.

Usually you'll still encounter a a completeness problem. What if a batch fails? What if there's a delay in one batch?
This may mean delaying processing until you're sure all events have been collected.

Depending on that data you're processing (e.g. say Session data, where it's defined as a period of activity for a user),
you might end up with sessions that are split across batches. You can try to reduce splits by increasing your batch size,
but that increases latency. You can add logic to stitch up sessions from previous runs, but that means more complexity.

#### Unbounded Data (Streaming)

Streaming systems are built for Unbounded Data. You should also consider streaming systems for data that is:

* Highly unordered with respect to event times - you need some sort of time-based shuffle in your pipeline if you want
  to analyze the data in the context in which they occurred
* Of varying event time skew - you can't just assume you'll always see most of the data for a given event X within
  some constant epsilon of time Y


