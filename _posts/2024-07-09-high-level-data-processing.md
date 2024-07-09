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

