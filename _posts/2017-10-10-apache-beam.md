---
layout: post
title: Apache Beam
---


# {{ page.title }}

## Summary

Apache Beam is a way to create data processing pipelines that can be used on
many execution engines including Apache Spark and Flink. Beam provides these
engines abstractions for large-scale distributed data processing so you can
write the same code used for batch and streaming data sources and just specify
the Pipeline Runner.

## Overview

An overview of our abstractions include:

* __Pipeline__ to encapsulate your entire data processing task, from start to
  finish. You tell the Pipeline where and how to run.
* __PCollection__ represents a distributed data set that your Beam Pipeline
  operates on. The data set can be __bounded__ (from a fixed source like
  a static file) or __unbounded__ (coming from a continuously updating source
  via a subscription or other mechanism). PCollections are the inputs and
  outputs for each step in your pipeline.
* __Transform__ represents a data processing operation, a step, in your
  pipeline. Each transform takes a one or more PCollection objects as input,
  performs a processing function that you provide on the elements of that
  PCollection, then produces one or more output PCollection objects.
* I/O __Source__ and __Sink__ represents reading and writnig data respectively.
  __Source__ is the code necessary to read data into your Beam pipeline from an
  external source and __Sink__ is the code that writes the elements of
  a PCollection to an external data sink.

### Typical Beam driver Workflow

A typical Beam driver program might look like:

* Create a __Pipeline__ object and set the Pipeline execution options, including
  the Pipeline Runner
* Create an initial PCollection for pipeline data, either using the __Source__
  API to read data from an external source or using a __Create__ transform to
  build a PCollection.
* Apply __Transforms__ to each __PCollection__ to change, filter, group,
  analyze or process the elements of a PCollection; this creates a new output
  PCollection without consuming the input collection. Subsequent transforms are
  applied to each PCollection until processing is complete.
* Output the final transformed PCollection, typically using the __Sink__ API to
  write data to an external source.
* Run the Pipeline using the designated Pipeline Runner

When you run your Beam driver program, the Pipeline Runner that you designated
constructs a __workflow graph__ of your pipeline based on the PCollection
objects that you created and transforms that you applied. The graph is then
executed using the appropriate distributed processing back-end, becoming an
asynchronous job on that back-end.

## Pipeline

To use Beam, your first step is to create a __Pipeline__ and set some
__configuration options__.

    import apache_beam as beam
    from apache_beam.options.pipeline_options import PipelineOptions

    p = beam.Pipeline(options=PipelineOptions())

### Configuring Pipeline Options

Your Pipeline will want to know a few configuration options including the
__Pipeline Runner__, or things like a Project ID, or a location for storing
files. You can set the options through __PipelineOptions__, which can be
passed into __command-line arguments__.

    import apache_beam as beam
    from apache_beam.options.pipeline_options import PipelineOptions
     
    p = beam.Pipeline(options=PipelineOptions())

You can also customize additional arguments:

    class MyOptions(PipelineOptions):

        @classmethod
        def _add_argparse_args(cls, parser):
            parser.add_argument('--input',
                                help='Input for the pipeline',
                                default='gs://my-bucket/input')
            parser.add_argument('--output',
                                help='Output for the pipeline',
                                default='gs://my-bucket/output')


### PCollections

The **PCollection** abstraction is a potentially distributed, multi-element data
set; think of a PCollection as a "pipeline" data. If you want to work with data
in your pipeline, it must be in the form of a PCollection. You can either
create a PCollection from in-memory data or through an external source.

The elements of a PCollection may be of any type, but must be of the same type.
Beam is able to encode each individual element as a byte string. The Beam SDK
provides a data encoding mechanism that includes built-in encoding for
commonly-used types as well as support for custom encodings. A PCollection is
also __immutable__ (once created, cannot add, remove, or change individual
elements).

#### Creating PCollection from in-memory data

You can create a PCollection from an in-memory object like a list by using
Beam's `Create` transform.

    with beam.Pipeline(options=pipeline_options) as p:
        lines = (p
                 | beam.Create([
                     'To be, or not to be: that is the question: ',
                     'Whether \'tis nobler in the mind to suffer ',
                     'The slings and arrows of outrageous fortune, ',
                     'Or to take arms against a sea of troubles, ']))


#### Creating PCollection from an external source

You can create a PCollection from an external source including reading text
from a file.

    lines = p | 'ReadMyFile' >> beam.io.ReadFromText('gs://some/inputData.txt')

You can also create a PCollection from in-memory data, like using `beam.Create`
to make a `list`.

    with beam.Pipeline(options=pipeline_options) as p:
        
        lines = (p
                 | beam.Create([
                    'To be, or not to be: that is the question: ',
                    'Whether \'tis nobler in the mind to suffer',
                    'The slings and arrows of outrageous fortune']))

#### PCollection Characteristics

A PCollection is owned by a Pipeline object; multiple pipelines cannot share
a PCollection.

* Element type must be the same type - each individual element is encoded as
  a byte string
* Immutable - once created, you cannot add, remove, or change individual
  elements. A Beam Transform does not consume or modify the original input
  collection
* A PCollection is a large, immutable "bag" of elements; there is no upper
  limit on the number of elements.
* A PCollection can be either __bounded__ or __unbounded__ in size. The size is
  determined whether the PCollection is reading from a streaming or
  continuously-updating data source (e.g. Pub/Sub or Kafka creates an unbounded
  PCollection)
* If the PCollection is __unbounded__, there is __windowing__ to divide
  a continuously updating data set into logical windows of finite size. Beam
  processes each window as a bundle. These windows are determined by some
  characteristic associated with a data element, like a __timestamp__.
* Each element in a PCollection has an associated intrinsic timestamp; the
  timestamp for each element is initially assigned by the __Source__ that
  creates the PCollection. These timestamps are useful for a PCollection that
  has an inherent notion of time, like reading a stream of events where each
  event was posted at the element timestamp. You can manually assign these
  timestamps.

## Transforms

A __Transform__ is the operations in your pipeline. You provide processing
logic as a function (aka __user code__) and this user code is applied to each
element of the input PCollection (or more than one PCollection). Depending on
the pipeline runner, different workers across a cluster can execute instances
of your user code in parallel. The output of the workers is added to
a separate, final output PCollection. Core transforms include __ParDo__ and
__Combine__, and __Composite Transforms__.

### Core Beam Transforms

The core transforms include:

* `ParDo`
* `GroupByKey`
* `CoGroupByKey`
* `Combine`
* `Flatten`
* `Partition`

### ParDo

A __ParDo__ is a transform for generic parallel processing similar to a __Map__
in the Map/Shuffle/Reduce-style. A ParDo applies your user code on each element
in the input PCollection and emits zero, one, or more elements to an ouptut
PCollection. Example usage cases include:

* Filtering a data set - either output the element to a new PCollection or
  discard it
* Format or type-convert each elemnt in a data set - if elements in your
  PCollection are a different type, do a conversion and output the result to
  a new PCollection 
* Extract parts of each element in a data set - if you have a PCollection of
  records with multiple fields, use the ParDo to parse out the fields you want
* Computations on each element - perform computations on every element or
  certain elements of a PCollection to a new PCollection

#### DoFn w/ ParDo

User Code is in the form of a __DoFn__ object, which is the processing logic
that gets applied to each element in the input collection. Make sure this
implementation does NOT depend on the number of invocations (it may be called
multiple times in case of failure or retries).

    # the input PCollection of Strings
    words = ... 

    # Using DoFn that performs on each element in the input PCollection
    class ComputeWordLengthFn(beam.DoFn):
        def process(self, element):
            return [len(element)]

    # Apply a ParDo to the PCollection 'words' to compute lengths for each word
    word_lengths = words | beam.ParDo(ComputeWordLengthFn())

Here we assume an input PCollection of String values. We apply a ParDo
transform that specifies a function ComputeWordLengthFn to compute the length
of each string and outputs the result to a new PCollection of Integer values.

#### FlatMap w/ lambda

If you have a simple DoFn, you can just use a __FlatMap__ with a lambda
function. 

    # the input PCollection of Strings
    words = ...

    # Apply a lambda function to the PCollection 'words'
    word_lengths = words | beam.FlatMap(lambda word: [len(word)])


#### Map

If you have a ParDo that has a one-to-one mapping of input elemnts to output
elments, you can use the __Map__ transform.

    # the input PCollection of Strings
    words = ...

    # Apply a Map with a lambda function to the PCollection 'words'
    word_lengths = words | beam.Map(len)

### GroupByKey

The __GroupByKey__ transform is for processing collections of key/value pairs.
It's a parallel reduction operation, similar to the Shuffle phase of
Map/Shuffle/Reduce. The input to GroupByKey is a collection of key/value pairs
that represents a __multimap__, where the collection contains multiple pairs
that have the same key, but different values.

You would use GroupByKey to aggregate data that has something in common. For
example, a collection that stores records of customer orders, you might want to
group by the postal code key/field and the 'value' is the rest of the record.

Example

    # Input File
    cat, 1
    dog, 5
    and, 1
    jump, 3
    tree, 2
    cat, 5
    dog, 2
    and, 2
    ...


    # Grouped By
    cat, [1,5,9]
    dog, [5,2]
    and, [1,2,6]
    jump, [3]
    tree, [2]
    ...

We transform from a multiple (multiple keys to individual values) to
__uni-map__ (unique keys to collections of values)

#### CoGroupByKey

__CoGroupByKey__ joins two or more key/value PCollections that have the same
key type and emits a `KV<K, CoGbkResult>` pair, which is essentially a join.

    // collection 1
    user1, address1
    user2, address2
    user3, address3

    // collection 2
    user1, order1
    user1, order2
    user2, order3
    guest, order4

    // output of collection1 and collection2
    user1, [[address1], [order1, order2]]
    user2, [[address2], [order3]]
    user3, [[address3], []]
    guest, [[], [order4]]

### Combine

__Combine__ is a transform for combining collections of elements or values in
your data. Combine can be used on entire PCollection(s) as well as the values
for each key in PCollection(s). Pre-built combines include common numeric 
combination operations like sum, min, and max.

#### Simple Combine

A simple combine

    pc = [1, 10, 100, 1000]

    def bounded_sum(values, bound=500):
        return min(sum(values), bound)

    small_sum = pc | beam.CombineGlobally(bounded_sum)  # [500]
    large_sum = pc | beam.CombineGlobally(bounded_sum, bound=5000)  # [1111]

An advanced Combine uses __CombineFn__, which defines complex combine
functions. Here we have a custom subclass of CombineFn.

    pc = ...

    class AverageFn(beam.CombineFn):

        def create_accumulator(self):
            return (0.0, 0)

        def add_input(self, sum_count, input):
            (sum, count) = sum_count
            return sum + input, count + 1

        def merge_accumulators(self, accumulators):
            sum, counts = zip(*accumulators)
            return sum(sums), sum(counts)

        def extract_output(self, sum_count):
            (sum, count) = sum_count
            return sum / count if count else float('NaN')

### Flatten

__Flatten__ is a transform for PCollection objects that store the same data
type. Flatten will merge multiple PCollection objects into a single
PCollection.

    # Takes a tuple of PCollection objects and returns a single PCollection
    merged = (
        (pcoll1, pcoll2, pcoll3)

        # A list of tuples can be "piped" directly into a Flatten transform
        | beam.Flatten()
    )

The coder for the output PCollection is the same as the coder for the first
PCollection from the input list of PCollection.

### Partition

__Partition__ is a transform for Pcollection objects that splits a single
PCollection into a fixed number of smaller collections. You provide the
partitioning function logic that determines how to split up the elements of the
input PCollection into eaach resulting partition PCollection.

    # Provide an int value with the desired number of result partitions, and
    a partitioning function (partition_fn in this example)
    # Returns a tuple of PCollection objects containing each of the resulting
    partitoins as individual PCollection objects

    students = ...
    def partition_fn(student, num_partitions):
        return int(get_percentile(student) * num_partitions / 100)

    by_decile = students | beam.Partition(partitoin_fn, 10)

    # You can extract each partition from the tuple of PCollection objects as:
    fortieth_percentile = by_decile[4]

## Transform Requirements

User code for a Beam Transform must keep below points in mind due to the
distributed nature of the jobs:

* Your function object must be __serializable__
* Your function object must be __thread-compatible__ (Beam SDK is not
  thread-safe)
* Your function object is recommended to be __idempotent__

The above applies to subclasses of __DoFn__, __CombineFn__, and __WindowFn__.

### Serializability

Any function object provided to a transform must be fully serilizable. The
reason is that a copy of the function is serialized and sent to a remote worker
in a processing cluster.

### Thread-Compability

Any function object should be thread-compatible because each instance of the
function object is accessed by a single thread on aworker instance. The Beam
SDK is not thread-safe.

### Idempotence

It is recommended that your function object be idempotent, meaning it should be
able to be repeated/retried as many times as necessary without creating any
unintended side effects.

## Side Inputs

Besides a main input PCollection, you can also provide additional inputs to
a ParDo transform as a __side input__. A side input is an additional input that
your DoFn can access each time it processes an elemnet in the input
PCollection. This side input needs to be determined at runtime (not
hard-coded).

