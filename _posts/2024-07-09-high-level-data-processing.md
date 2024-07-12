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

There's a few approaches you can use when dealing with data that has the above characteristics (see 'Approaches')

### Approaches

* Time-agnostic
* Approximation
* Windowing by processing time
* Windowing by event time

#### Time-agnostic

Time-agnostic processing is used in cases where time is irrelevant (i.e. all relevant logic is data driven).

An example of a time-agnostic processing is __filtering__, where you look at web traffic logs and look for a specific domain.
If it's a domain you're interested in, then allow that in. The processing does not rely on the data being unbounded, unordered, and the event time skew is irrelevant.

Another example of a time-agnostic processing is __inner-joins__ (aka __hash-join__), where you join two unbounded data sources.
If you only care about the results of a join when an element from both sources arrive, there's no temporal element to the logic.
You would buffer a value if it's seen in one source, then emit the joined record once the second value from the other source arrives.

If we thought about an outer join, we now get into the data completeness problem (once you've seen one side of the join,
how do you know whether the other side is ever going to arrive or not?). The answer is that you don't; you have to introduce
some notion of a timeout, which introduces an element of time.

#### Approximation

Approximation is where we take an unbounded data source and provide output (where if you squint at them, look more or less like what you were hoping for).

Pros: They are low overhead and designed for unbounded data.
Cons: Limited set of them exist, the algorithms themselves are complicated, and approximations limit their utility

#### Windowing (Overview)

__Windowing__ means to take a data source (either unbounded or bounded) and chopping it along temporal boundaries into finite chunks for processing.
There's a lot of windowing strategies, including:

* __Fixed Windows__ - slice up time into segments with a fixed-size temporal length. The segments for fixed windows are applied
  uniformly across the entire data set.
  - __Aligned windows__ have a start and end time that is synchronized with a reference point (e.g. a global clock).
    All windows across all different streams start and end at the same time.
    E.g. 5 minute window that starts at the top of the hour, event at 12:02 fits into window from 12:00 to 12:05
  - __Unaligned fixed windows__ do not have a global clock; they are defined relative to the time of the first event in the data stream
    or some arbitrary reference point. Each window starts after the previous window ends, but the start and end times are not synchronized across different data streams.
   Processing may be shifted to avoid processing peaks and distribute the load more evenly.

__Sliding Windows__ - a generalization of fixed windows; fixed length and a fixed period.
  - if the period is less than the length, then the windows overlap
  - if the period is equal to the length, you have __fixed windows__
  - if the period is greater than the length, you have a weird sampling window that only looks at subsets of the data over time

__Dynamic Windows__ - Session data from the above is an example of dynamic windows. Lengths cannot be defined prior.

#### Windowing by Processing Time

The system essentially buffers up incoming data into windows until some amount of processing time has passed.
E.g. say we have five-minute fixed windows, the system would buffer up data for five minutes of processing time,
after which it would treat all the data it had observed in those five minutes as a window and send them downstream for processing

Pros:

* Simple - Straightforward. You never worry about shuffling data within time. Just buffer as data arrives and send them downstream when the window closes.
* Judging window completeness is straightforward - No need to label data as "late" since the system has perfect knowledge of whether
  all inputs for a window have been seen or not
* For inferring information about the source as it is observed - processing time window is ideal for cases like tracking
  the number of requests per second sent to a global-scale web service. Calculating a rate of these requests for detecting outages
  is the perfect use case of processing time windows

Cons:

* if the data has event times associated with them, that data must arrive in event time order if the processing time windows
  are to reflect the reality of when those events actually happened.
* Unfortunately, event-time ordered data is uncommon in many real-world, distributed input sources

An example is where a mobile app gathers usage statistics for later processing. The data recorded won't be uploaded
until the device comes online again, which means data might arrive with a skew of minutes to weeks or more.

#### Windowing by Event Time

Windowing by Event Time is the gold standard of windowing. Most data processing systems lack native support for it,
although any system with a decent consistency model like Hadoop or Spark Streaming could be used to help build a windowing system).
Event time windows have a few drawbacks due to the fact that windows often live longer (in processing time) than the actual length of the window itself.

* Buffering - due to extended window lifetimes, more buffering of data is required
  Any well-designed data-processing system with a strongly consistent persistent state should be able to handle.
* Completeness - There is no good way of knowing when we've seen all the data for a given window

### Watermarks

A __watermark__ is a notion of input completeness with respect to event times. A watermark with a value of time X
makes the statement "all input data with event times less than X have been observed". Watermarks act as a metric of
progress when observing an unbounded data source with no known end.

Watermarks help answer the first half of the question "__When__ in processing time are results materialized?".
Watermarks are temporal notions of input completeness in the event-time domain (i.e. the way the system measures
progress and completeness relative to the event times of the records being processed in a stream of events).

You can look for two types of watermarks (perfect or heuristic):

* __Perfect watermarks__ - For the case where we have perfect knowledge of all of the input data, it's possible to
  have a perfect watermark. In this case, there is no such thing as late data; all data are early or on time.
* __Heuristic watermarks__ - For many distributed input sources, perfect knowledge of the input data is impractical.
  The next best option is to provide a heuristic watermark, which uses whatever information is available about the inputs
  (partitions, ordering within partitions, growth rate of files, etc) to provide an estimate of progress that is as
  accurate as possible. The use of heuristic watermarks mean it may sometimes be wrong, which can lead to late data.

Windows are materialized as the watermark passes the end of the window.

### Triggers

A __trigger__ is a mechanism for declaring when the output for a window should be materialized relative to some external signal.

* Triggers provide flexibility in choosing when outputs should be emitted.
* Triggers make it possible to observe the output for a window multiple times as it evolves

Triggers help answer the second half of the question "__When__ in processing time are results materialized?".
Triggers declare when output for a window should happen in processing time.

### Accumulation

An __accumulation__ mode specifies the relationship between multiple results that are observed for the same window.
These results might be completely disjointed (i.e. distinct, non-overlapping piece of information, no overlap or accumulation
of previous results within the same window) or the results might have overlap between them.

### Questions

We want to answer these questions about the data:

* __What__ results are calculated? We can answer by the types of __transformations__ within the pipeline
  (e.g. are we computing sums, training ML models)
* __Where__ in event time are results calculated? We can answer by the use of event-time __windowing__ within the pipeline
  This includes common examples of windowing (e.g. fixed, sliding, sessions), use cases that have no notion of windowing
  (e.g. time-agnostic processing, classic batch processing), and other more complex types of windowing
* __When__ in processing time are results materialized? We can answer with the use of __watermarks__ and __triggers__.
  The most common pattern is to use the watermark to delineate when input for a given window is complete, with
  triggers allowing the specification of early results (for speculative, partial results emitted before the window is complete)
* __How__ do refinements of results relate? We can answer by the type of __accumulation modes__ used: discarding (where results
  are all independent and distinct), accumulating (where later results build upon prior ones), or accumulating and
  retracting (where both the accumulating value plus a retraction for the previously triggered values are emitted)



