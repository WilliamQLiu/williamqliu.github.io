---
layout: post
title: Google Cloud Platform (GCP)
---


# {{ page.title }}

## Intro to Google Cloud Platform

### History of data technologies from Google

* **Google File System (GFS)** (2002, w/ newer version of **Colossus** in 2010) is the basis for **HDFS** and **MapReduce** (2004) is the basis for **Hadoop**; these allow a distributed file and compute system using large clusters of commodity hardware. No longer really used due to coupling of storage and compute.
    Advantages: Can scale compute and storage 
    Disadvantages: Need to shard your data across many compute nodes (tight
    coupling between compute and storage)
* **Dremel** (aka **BigQuery**) and **Flume** (aka **DataFlow**) is the new
  data storage and processing systems of choice.


### GCP Products

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

### Interfaces

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

#### Data handling in Cloud Storage

Sample Scenario:

* Ingest data into a Compute Engine instance
* Transform data on the Compute Engine instance
* Store the transformed data on Cloud Storage
* Publish Cloud Storage data on the web

### Google Cloud Launcher

Ready to use virtual machine configurations for common use deployments (e.g. Redis). Similar to DockerHub.

## Storage based on Access Pattern

You want to choose storaged based on access pattern

* Cloud Storage
    Capacity: Petabytes+
    Access: Like files in a file system
    Read: Have to copy to local disk
    Write: One file
    Update granularity: An object (a 'file')
    Usage: Store blobs
* Cloud SQL
    Capacity: Gigabytes
    Access: Relational Database
    Read: SELECT rows
    Write: INSERT row
    Update granularity: Field
    Usage: No-ops SQL database on the cloud
* Datastore
    Capacity: Terabytes
    Access: Persistent Hashmap
    Read: filter objects on property
    Write: put object
    Update granularity: Attribute
    Usage: Structured data from AppEngine apps
* Bigtable
    Capacity: Petabytes
    Access: Key-value(s), HBase API
    Read: scan rows
    Write: put row
    Update granularity: Row (write new row instead)
    Usage: No-ops, high throughput, scalable, flattened data
* BigQuery
    Capacity: Petabytes
    Access: Relational
    Read: SELECT rows
    Write: Batch/stream
    Update granularity: Field
    Usage: Interactive SQL querying fully managed warehouse

## Cloud SQL

Relational SQL Database, basically MySQL. Good for relatively small data.

## Dataproc

Data Processing.

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

For example, we create a Player, add Properties, then save the entity. The Properties can all be different (e.g. if person is a baseball player, will have batting average, if a football player, will have yards ran). 
### Why use (or don't use) Cloud Datastore?

### Disadvantages

Note that when you use Datastore, you're locked in to Google's platform because
you're then writing custom code for transactions (unlike say Cloud SQL).
Queries are a bit more restrictive due to using previously built indexes; you
won't have join operations, inequality filtering on multiple properties,
or filtering on data based on results of a subquery.

### Advantages

Datastore supports transactions allowing you to read and write structured data. 
Datastore is a good option if you still need to do transactions like a SQL
database and need to scale to very large data sets. Cloud Datastore is good for
highly available structured data at scale. 

## Bigtable

Bigtable is a NoSQL database made for low latency, high throughput workflows at
a very large scale. Good use cases include IoT, user analytics, and financial
data analysis. You can write very wide columns of data with high throughput at
the cost of sometimes duplicating data.

## BigQuery

Used for data warehousing, can still use SQL for querying.

## Data Processing Architectures

For data processing architectures, we have:

* Message-Oriented Architectures (i.e. Asynchronous Processing with Pub/Sub)
* Serverless Data Pipelines

As an example, here's a real-time data analysi of Twitter data using a pipeline
built on Google Compute Engine, Kubernetes, Google Cloud Pub/Sub, and BigQuery.

https://cloud.google.com/solutions/real-time/kubernetes-pubsub-bigquery

### Asynchronous Processing

Asynchronous processing is a way for absorbing shock and change.
Say you have an application built for 100 users, then there is a sudden influx
of 4,000 users. Your application can just crash or queue things up so responses
are sent, but delayed.

Asynchronous processing is able to help by separating the receiving code and
the processing code with a message system. When a new request comes in, they go
to a message queue, then you have consumers of this message queue do the actual
processing of the request. An example would be RabbitMQ. This will make sure
that your application is Available, so any request sent will be sooner or later
be processed. You can also balance load across multiple workers so this can help with throughput.

## Cloud Pub/Sub

Google's **Cloud Pub/Sub** is a good way to handle asynchronous processing;
Pub/Sub offers reliable, real-time messaging that is accessible through HTTP.
You have __decoupled sources__ (e.g. an HR System, a Vendor Office), __publishers__ that send independent events (HR has a New Hire Event, Vendor Office has a New Contractor Event) that goes to a Pub/Sub __Topic__ (e.g. HR). We have __decoupled workers__ that are subscribed to this Topic and __consume__ these messages.

## Serverless Data Pipelines

__Dataflow__ is the execution framework for __Apache Beam__ pipelines. Apache
Beam is an open-source API that lets you define a data pipeline. Apache Beam
can be executed on Flink, Spark, etc. Dataflow does the data ingestion,
transformation, and loading. You can process batch and stream data the same
way.

* Job is submitted to Cloud, its executed
* No need to spin up extra servers
* `ParDo` is a Parallel Do
* Instead of being able to just read from a text file, can do `SlidingWindows`
  (e.g. every minute, every 60 minutes) to get real time data (from say
  Pub/Sub)


## Data Analysis with Cloud Datalab

Basically ipython/jupyter on the cloud.

## Useful Machine Learning APIs

* Translate API - Language translation
* Vision API - Image recognition
* Speech API - Speech to Text

# Cloud Dataproc

## On-premise applications for Big Data

Dataproc is a managed service for creating clusters of computers that can be
used to run Hadoop and Spark applications.

* Spark - general purpose cluster computing
* Hadoop - Hadoop and Hadoop File System
* Pig - for data transformations on Hadoop HDFS
* Hive - querying on Hadoop HDFS

## bdutil

bdutil is a free OSS Toolkit, (a command-line script) used to manage Apache Hadoop and Apache
Spark instances on GCE.

## Setup Dataproc Cluster

### Location

Setting up Compute and Storage by location (__Zones__ and __Regions__) is very important; this affects transfer speed.

Match your data location with your compute location (put in same region and
zone ideally).

* Same region and zone means petabit scale bandwidth
* Between zones in the same regions means different buildings or different
  segments of buildings, so fast, but not petabit scale
* Between regions means fiber optic connections across the globe.

### Cluster Configurations

There are three cluster modes.

* Single Node - use for experimentation
* Standard - 1 master only; if something happens on master and not stored in
  cluster, its lost
* High Availability - 3 masters


For storage, do not use HDFS (its available, just don't use it). The reason is
that it will allow you to separate compute from storage. If you use storage on
the Compute Engine, you can't spin down these machines.

Consider using 'Preemptible' nodes for cheaper worker nodes. These are cheaper,
but can be taken away at any minute. Good for compute.

You can also setup additional configs like firewalls and what image versions
you want (e.g. Spark, Hadoop, Pig, Hive).

## gcloud SDK

You can use the Cloud SDK to do many of the same operations as the GUI.

### Firewall Configuration

We want to make sure that our clusters allow our IP Address, Protocols, and
Ports. You might want to open:

* IP Address (find from http://ip4.me) and add '/32' (e.g. 174.51.93.17/32)
* tcp:8088  # Hadoop uses this
* tcp:50070  #  HDFS uses this
* tcp:8080  #  Datalab uses this

You can then access the web GUIs at your master node's IP Address, then
followed by the port. E.g. say we have an master node of 104.154.142.41

* http://104.154.142.41:8088  # shows Hadoop GUI (what jobs are submitted)
* http://104.154.142.41:50070  # shows HDFS GUI

