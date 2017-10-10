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
 

