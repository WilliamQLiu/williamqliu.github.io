---
layout: post
title: Google Cloud Platform (GCP)
---


# {{ page.title }}


## History of data technologies from Google

* **Google File System (GFS)** (2002, w/ newer version of **Colossus** in 2010) is the basis for **HDFS** and **MapReduce** (2004) is the basis for **Hadoop**; these allow a distributed file and compute system using large clusters of commodity hardware. No longer really used due to coupling of storage and compute.
    Advantages: Can scale compute and storage 
    Disadvantages: Need to shard your data across many compute nodes (tight
    coupling between compute and storage)
* **Dremel** (aka **BigQuery**) and **Flume** (aka **DataFlow**) is the new
  data storage and processing systems of choice.


## GCP Products

Foundational Level

* __Compute Engine__
* __Cloud Storage__

Databases

* __Datastore__
* __Cloud SQL__
* __Cloud Bigtable__

Analytics and Machine Learning

* __BigQuery__
* __Cloud Datlab__
* __Translate API__
* ...

Data-handling Frameworks for moving data from one place to another

* __Cloud Pub/Sub__
* __Cloud Dataflow__
* __Cloud Dataproc__

## Interfaces

You can access any of the Google resources a few different ways including:

* __gsutil__ `gsutil cp`
* command line (e.g. cp, rsync)
* REST API
* GCP Console (a web console)


## Google Compute Engine

Basically like AWS EC2 server. Disk is ephemeral/not persistent (just like EC2).
Usually first step of data processing is to get data from compute to cloud storage.

## Google Cloud Storage

Basically like AWS S3. Use Cloud Storage as a staging area (i.e. after Compute
Engine, before import to BigQuery for analysis).

We do not want Google Cloud Storage for:

* Anything that requires high throughput (e.g. real time sensors)
* For storing data that would be frequently read or written from a compute node
  (because we want caching on local discs)

We want Google Cloud Storage for:

* Durable way to store data for long periods of time
* Storing data that may be imported into a cluster for analysis (because Google
  Cloud Storage supports multi-threaded uploads, multi-part uploads, resumable
  uploads)

### Data handling in Cloud Storage

Sample Scenario:

* Ingest data into a Compute Engine instance
* Transform data on the Compute Engine instance
* Store the transformed data on Cloud Storage
* Publish Cloud Storage data on the web

## Google Cloud Launcher

Ready to use virtual machine configurations for common use deployments (e.g. Redis). Similar to DockerHub.

## Cloud SQL

Relational SQL Database, basically MySQL. Good for relatively small data.

## Dataproc

* Google managed Hadoop, Pig, Hive, Spark.
* Instead of storing data on HDFS, use Google Cloud Storage.
* Think as a 'job' that you would spin up clusters for compute

## Cloud Datastore

### Object Oriented Programming and Relational Databases

If you use a relational database to persist an object hierarchy, you get into
an __object relational impedance mismatch__; so objects (instances) reference 
one another, forming a graph (in the mathematical sense, a network including
loops and cycles), which are in contrast to relational schemas (tabular and
based on relational algebra) that groups data fields into a 'row' with
different types for each field.

For example, if you have a hierarchical data structure for Players, you have 
some players that are footballers, some that are baseball players, some that
are bowlers, and so forth. We need the players data to persist so we store our
data in a relational database. We now have football players with batting averages.
We put a null batting average for a football player, but this doesn't make sense.
Converting linked tabular rows to graph structures is hard (this
Object/relational mapping is sometimes called the 'The Vietnam of Computer
Science').

What we can do is store objects directly with **Cloud Datastore** (scales up to
terabytes of data). What you're storing is like a persistent __Hashmap__
(there's a key/id to the object and the entire actual object). When you write,
you write an entire object. When you read, you're searching for the key or
a property of the object (e.g. baseball players with batting average greater
than 10 runs a game).

For example, we create an Author, add Properties, then save the entity.

