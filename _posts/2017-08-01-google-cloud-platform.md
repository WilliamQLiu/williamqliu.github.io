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

For example, we create a Player, add Properties, then save the entity.
The Properties can all be different (e.g. if person is a baseball player, will
have batting average, if a football player, will have yards ran).

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

### gcloud SDK

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

## Running Dataproc Jobs

With Dataproc, you can submit Hadoop, Spark, Spark SQL, PySpark, Pig and Hive
jobs. You can submit these parallel computing jobs via high levels APIs.

### Create a Cluster

Create a cluster with Google Cloud Shell, copy files over to Cloud Storage

    # Create a cluster
    gcloud dataproc clusters create my-cluster --zone us-central1-a \
        --master-machine-type n1-standard-1 --master-boot-disk-size 50 \
        --num-workers 2 --worker-machine-type n1-standard-1 \
        --worker-boot-disk-size 50 --network=default

### Get data to/from a bucket

To get data to/from a bucket, you can do:

    # Get data from google bucket using gsutil
    gsutil -m cp gs://williamqliu/unstructured/pet-details.* .

    # Copy files to Cloud Storage from GitHub
    git clone https://github.com/GoogleCloudPlatform/training-data-analyst
    cd training-data-analyst/courses/unstructured
    ./replace_and_upload.sh <YOUR-BUCKET-NAME>

#### Files

Here are some files (e.g pet-details, pet-details.pig) that will be used later for Pig and Spark Jobs

Pet-details.txt has some simple data

    #pet-details.txt
    Dog,Noir,Schnoodle,Black,21
    Dog,Bree,MaltePoo,White,10
    Dog,Pickles,Golden Retriever,Yellow,30
    Dog,Sparky,Mutt,Gray,13
    Cat,Tom,Alley,Yellow,11
    Cat,Cleo,Siamese,Gray,22
    Frog,Kermit,Bull,Green,1
    Pig,Bacon,Pot Belly,Pink,30
    Pig,Babe,Domestic,White,150
    Dog,Rusty,Poodle,White,20
    Cat,Joe,Mix,Black,15


### SSH into cluster (for experimentation)


You can ssh into master nodes to run jobs to do quick experimentation. You can
start the pyspark interpreter here too.

### PySpark with HDFS

After sshing into the master node, run `pyspark` and create some PySpark jobs

    # Run a simple PySpark job
    data = [0, 1, 2, 3, 4, 5]  # range(6)
    distData = sc.parallelize(data)
    squares = distData.map(lambda x : x*x)
    res = squares.reduce(lambda a, b : a + b)
    print res

    # PySpark program to compute the square root of the sum of the first 1000
    terms of this series
    import numpy as np
    data = range(1000)
    distData = sc.parallelize(data)  # get data ready for parallel processes
    terms = distData.map(lambda k : 8.0/((2*k+1)*(2*k+1)))  # map a function
    res = np.sqrt(terms.sum())
    print res

You can run PySpark programs using the REPL (like the above), as a Python
notebook, or execute PySpark programs to submit a Python file.

### Pig Jobs

**Apache Pig** is a platform for analyzing large data sets.

You can execute a Pig job and view its results. Pig needs a HDFS cluster, but
first we should load the data into HDFS so that all worker nodes can also
access the file (so it can help with processing)


To copy a text file (pet-details.txt) into HDFS, we can run:

    hadoop fs -mkdir /pet-details
    hadoop fs -put pet-details.txt /pet-details
    # 'fs' means to run a generic filesystem user client

Under the master node (connect using the browser and paste in the ip address,
then add in port `:50070`) to open the Hadoop management site.

#### Run Pig, view Pig results

Pet-details.pig shows how we transform the data. We basically broke up the data
file we needed into Hadoop, then Pig is able to use this across a lot of
computing nodes.

    #pet-details.pig
    rmf /GroupedByType
    x1 = load '/pet-details' using PigStorage(',') as (Type:chararray,Name:chararray,Breed:chararray,Color:chararray,Weight:int);
    x2 = filter x1 by Type != 'Type';
    x3 = foreach x2 generate Type, Name, Breed, Color, Weight, Weight / 2.24 as Kilos:float;
    x4 = filter x3 by LOWER(Color) == 'black' or LOWER(Color) == 'white';
    x5 = group x4 by Type;
    store x5 into '/GroupedByType';

To run the entire Pig job (pet-details.pig) as a script:

    pig < pet-details.pig

To run Pig line-by-line, you can run `pig`:

    $pig
    grunt>x1 = load '/pet-details' using PigStorage(',') as (Type...)
    grunt>describe x1
    grunt>illustrate x1
    grunt>dump show only x3

This only runs when you add in `illustrate` or `dump` so that it can queue up
a lot of jobs.

So what does the Pig file do? Each line in the script is a hadoop job.

* x1 will load the file and cast a given schema
  (Type:chararray,Name:chararray, etc). Not much has changed:

    # Command
    x1 = load '/pet-details' using PigStorage(',') as (Type:chararray,Name:chararray,Breed:chararray,Color:chararray,Weight:int);

    # Result
    (Dog,Noir,Schnoodle,Black,21)
    (Dog,Bree,MaltePoo,White,10)
    (Dog,Pickles,Golden Retriever,Yellow,30)
    (Dog,Sparky,Mutt,Gray,13)
    (Cat,Tom,Alley,Yellow,11)
    (Cat,Cleo,Siamese,Gray,22)
    (Frog,Kermit,Bull,Green,1)
    (Pig,Bacon,Pot Belly,Pink,30)
    (Pig,Babe,Domestic,White,150)
    (Dog,Rusty,Poodle,White,20)
    (Cat,Joe,Mix,Black,15)

* x2 will run a filter to remove the header; this is just in case we have a header
line that says ('Type', 'Name'), etc.

* x3 will generate a new calculation, which is basically putting in much of the
  same schema, but also adding in a new Weight calculation as a new column
  'Kilos'.

    # Command
    x3 = foreach x2 generate Type, Name, Breed, Color, Weight, Weight / 2.24 as Kilos:float;

    # Result
    (Dog,Noir,Schnoodle,Black,21,9.37499999)
    (Dog,Bree,MaltePoo,White,10,4.4642857)
    (Dog,Pickles,Golden Retriever,Yellow,30,13.39285714)
    ...

* x4 will do another data transformation (filter only for color that is 'black'
  or 'white')

    # Command
    x4 = filter x3 by LOWER(Color) == 'black' or LOWER(Color) == 'white';

    # Result
    (Dog,Noir,Schnoodle,Black,21,9.37499999)
    (Dog,Bree,MaltePoo,White,10,4.4642857)
    (Pig,Babe,Domestic,White,150,66.9642857)
    ...

* x5 will group by type

    # Command
    x5 = group x4 by Type;

    # Result
    (Cat,((Cat,Joe,Mix,Black,15,6.6864285)))
    (Dog,((Dog,Rusty,Poodle,White,20,8.928571),(Dog,Bree,MaltedPoo,White,10,4.4642857),(Dog,Noir,Schnoodle,Black,21,9.27499999)))
    (Pig,((Pig,Babe,Domestic,White,150,66.964285714)))


* The last command will store the data into the directory '/GroupedByType'

    # Command
    store x5 into '/GroupedByType' in the HDFS (not on the local directory of
    the master node)

    # Result
    Now that the data is transformed by Pig, we need to load the data from
    Hadoop with:

    hadoop fs -get /GroupedByType/part* .

    Check in '/GroupedByType' dir and you should see a file 'part-r-00000'

The results of 'part-r-00000' should look like this:

    Cat     {(Cat,Joe,Mix,Black,15,6.696428571428571)}
    Dog     {(Dog,Rusty,Poodle,White,20,8.928571428571427),(Dog,Bree,MaltePoo,White,10,4.4642857142857135),(Dog,Noir,Schnoodle,Black,21,9.374999999999998)}
    Pig     {(Pig,Babe,Domestic,White,150,66.96428571428571)}

## Installing Software on Dataproc

### Apache Bigtop

__Apache Bigtop__ is a good baseline place to get started with installing comprehensive
big data components (like Hadoop, HBase and Spark).

### Installing Custom Software

You can also install additional software on a Dataproc cluster by:

* Write an executable program (bash, python, etc)
* Upload it to Cloud Storage
* Specify GCS location in Dataproc creation command

Make sure to write the program so that it runs as root. E.g.

* **Shebang** (#!) to speciy what language interpreter to invoke (e.g.
  `#!/bin/bash`)
* Make sure to include `-y` when running something like `apt-get install -y
  python-numpy python-scipy python-matplotlib python-panads`

### Installing Custom Software (specify if only on Master Node, Worker Nodes)

By default, Dataproc installs on both masters and workers. If we only want an
install on master or worker only, we can look at the metadata to determine
where installs go.

    #!/bin/bash
    apt-get update || true

    ROLE=$(/usr/share/google/get_metadata_value attributes/dataproc-role)
    if [[ "${ROLE}" == 'Master' ]]; then
        apt-get install -y vim
    else
        # something that goes only on worker
    Fi

    # things that go on both
    apt-get install -y python-num python-scipy

### Pre-made initialization scripts

If you want to use some pre-built initialization scripts, check this out:

https://github.com/GoogleCloudPlatform/dataproc-initialization-actions

There's also a read-only storage bucket here:

    # Bucket
    gs://dataproc-initialization-actions

    # Command line to see bucket
    gsutil ls gs://datproc-initialization-actions

    # Command line to create dataproc cluster using initialization-actions
    gcloud dataproc clusters create mycluster \
        --initialization-actions gs://mybucket/init-actions/my_init.sh \
        --initialization-action-timeout 3m

### Cluster Properties

If you want to modify configuration properties, you can do:
`file_prefix:property=value` in the gcloud SDK


## Hadoop and HDFS

Hadoop and the Hadoop File System were built on Google's whitepapers on the
Google File System and MapReduce. The open source community took and built
Hadoop, Hadoop File System that later turned into Spark. Spark will read input
files, process them, and then output the data after the job is completed.
We need the data before the compute and after the compute so it ends up so we
need data to persist.

### YARN

**YARN** stands for 'Yet Another Resource Negotiator' and is the framework
responsible for providing the resources (CPU, memory, etc) needed for
applications to execute. There are two important pieces:

* __Resource Manager__ (one per cluster) is the master. It knows where the
  slaves are located and how many resources they have. It runs several
  services, including the __Resource Scheduler__, which decides how to assign
  the resources.
* __Node Manager__ (many per cluster) is the slave of the infrastructure. When
  it starts, it announces itself to the Resource Manager. Periodically, it
  sends a heartbeat to the Resource Manager. Each Node Manager offers some
  resources to the cluster.


## Spark

Spark runs on top of Hadoop (and uses YARN and HDFS).

## Apache Beam

For Pipelines, used if you want to submit a job to a serverless platform and
let Google handle all of the scale. We could feed that data in via Google
Pub/Sub, process the data with Dataflow, then store the results out to BigQuery
or BigTable.

## Serverless Infrastructures

In a serverless world, we process data with Cloud Dataflow, then we store data
with Cloud Storage (files), BigQuery (tables), and Bigtable (NoSQL).

### Bigtable

Bigtable is a drop-in replacemnet for HBase. HBase was a database that often
utilized a Hadoop file system type infrastructure.

### BigQuery Analytics

Analytics Data Warehouse

## Dataproc

### Monitoring Dataproc

You can monitor dataproc through logs from say the web console or through a web
UI called **Stackdriver**.

### Running Dataproc Jobs

Create a Cluster

    gcloud dataproc clusters create my-cluster --zone us-central1-a \
        --master-machine-type n1-standard-1 --master-boot-disk-size 50 \
        --num-workers 2 --worker-machine-type n1-standard-1 \
        --worker-boot-disk-size 50 --network=default


Create a Cloud Storage Bucket with the same name as your project ID in the same
region as the above cluster

    gsutil mb -c regional -l us-central1 gs://$DEVSHELL_PROJECT_ID

Copy files over to your bucket

    git clone https://github.com/GoogleCloudPlatform/training-data-analyst
    cd training-data-analyst/courses/unstructured
    ./replace_and_upload.sh williamqliu  # williamqliu is the bucket name

Submit a Spark Job and view its results without copying anything (code or data)
to the cluster

    # lab2-input.txt
    Dog,Noir
    Dog,Bree
    Dog,Pickles
    Dog,Sparky
    Cat,Tom
    Cat,Alley
    Cat,Cleo
    Frog,Kermit
    Pig,Bacon
    Pig,Babe
    Dog,Gigi
    Cat,George
    Frog,Hoppy
    Pig,Tasty
    Dog,Fred
    Cat,Suzy

    # lab2.py - organizes input file by key and total number for each type of
    # pet
    #!/usr/bin/env python
    from pyspark import SparkContext
    sc = SparkContext("local")

    file = sc.textFile("gs://williamqliu/unstructured/lab2-input.txt")
    dataLines = file.map(lambda s: s.split(",")).map(lambda x : (x[0], [x[1]]))
    print dataLines.take(100)

    databyKey = dataLines.reduceByKey(lambda a, b: a + b)
    print databyKey.take(100)

    countByKey = databyKey.map(lambda (k,v): (k, len(v)))
    print countByKey.take(100)

So we have the input file (lab2-input.txt) and the code (lab2.py) on Cloud
Storage.


#### Submit Dataproc Job

To submit the job, under 'Cloud Dataproc' > 'Jobs' click 'Submit Job'. Make
sure to select the 'Job Type' as 'PySpark', then enter the 'Main python file'
path (e.g. gs://williamqliu/unstructured/lab2.py), where 'williamqliu' was the
bucket name.

The output looks like:

    17/08/20 04:56:08 INFO org.spark_project.jetty.util.log: Logging initialized @4233ms
    17/08/20 04:56:09 INFO org.spark_project.jetty.server.Server: jetty-9.3.z-SNAPSHOT
    17/08/20 04:56:09 INFO org.spark_project.jetty.server.Server: Started @4476ms
    17/08/20 04:56:09 INFO org.spark_project.jetty.server.AbstractConnector: Started ServerConnector@7e62866a{HTTP/1.1,[http/1.1]}{0.0.0.0:4040}
    17/08/20 04:56:10 INFO com.google.cloud.hadoop.fs.gcs.GoogleHadoopFileSystemBase: GHFS version: 1.6.1-hadoop2
    17/08/20 04:56:14 INFO org.apache.hadoop.mapred.FileInputFormat: Total input files to process : 1
    [(u'Dog', [u'Noir']), (u'Dog', [u'Bree']), (u'Dog', [u'Pickles']), (u'Dog', [u'Sparky']), (u'Cat', [u'Tom']), (u'Cat', [u'Alley']), (u'Cat', [u'Cleo']), (u'Frog', [u'Kermit']), (u'Pig', [u'Bacon']), (u'Pig', [u'Babe']), (u'Dog', [u'Gigi']), (u'Cat', [u'George']), (u'Frog', [u'Hoppy']), (u'Pig', [u'Tasty']), (u'Dog', [u'Fred']), (u'Cat', [u'Suzy'])]
    [(u'Cat', [u'Tom', u'Alley', u'Cleo', u'George', u'Suzy']), (u'Dog', [u'Noir', u'Bree', u'Pickles', u'Sparky', u'Gigi', u'Fred']), (u'Frog', [u'Kermit', u'Hoppy']), (u'Pig', [u'Bacon', u'Babe', u'Tasty'])]
    [(u'Cat', 5), (u'Dog', 6), (u'Frog', 2), (u'Pig', 3)]
    17/08/20 04:56:17 INFO org.spark_project.jetty.server.AbstractConnector: Stopped Spark@7e62866a{HTTP/1.1,[http/1.1]}{0.0.0.0:4040}
    Job output is complete

You can submit the job again by 'Clone' and 'Submit'

If we want to submit the job using only the command line, we can run:

    gcloud dataproc jobs submit pyspark \
      --cluster my-cluster gs://williamqliu/unstructured/lab2.py

## Datalab

Create a Dataproc cluster, install Datalab, then you basically have a Jupyter
Notebook that can utilize the Dataproc cluster. You're able to get data from
Google Cloud Storage, run BigQuery to query data, and also run PySpark jobs to
do parallel jobs.

We made use of the initialization scripts that told the cluster what to install
and setup. We also opened an IP Address and port on the firewall so that our IP
Address is able to access the cluster IP Addresses.


## BigQuery w/ Hadoop and Spark

BigQuery integrates with Spark Clusters so you don't have to do something like
Spark SQL. You can use Spark SQL for SQL statements, but if you want to run
a Spark machine learning program, BigQuery might be good.

Say we're on a Jupyter Notebook and we submit a query over to BigQuery.

## Machine Learning APIs

Google has a few APIs available for Machine Learning, including:

* Vision API - Use Vision API to search for something in photos
* Natural Language API - Run sentiment analysis (e.g. look at Hacker News
  stories, gives numeric rating per story, if positive or negative)
* Speech API
* Translation API - Take a file (e.g. Alice in Wonderland, then translate to
  Spanish)

## Google Big Data Stack 2.0

In Big Data v1, if we had to process very large datasets, we couldn't do them
on one machine. You'd have to MapReduce, so that computations are doing with
a map operation and data is sharded across many machines. The more machines you
have, the more you can store, but also the more machines you have to search to
find your data. This doesn't scale very well because we tied in storage and
compute.

Big Data v2 is looking at products like PubSub and BigQuery where its
completely serverless, autoscaled ways of doing data analysis.

## What is BigQuery

BigQuery lets you run large queries (petabyte scale) from a cold start. There's
no need to create indexes, provision clusters, etc. You can have data in
regular tables or in denormalized form (with the denormalized form being much
more efficient).

You can access BigQuery console at https://bigquery.cloud.google.com

* Data Storage is inexpensive. You are billed based off of the amount of data processed.
* No-ops
* Near real time (not milliseconds, but seconds)
* Immutable audit logs
* Durable (replicated) inexpensive storage
* Can give anyone the data you have access to

### How BigQuery fits in an xample Architecture for Data Analytics

Here's how BigQuery fits into a Data Analytics Architecture

1. Real-time Events (someone clicked this, etc.)
2. Game Server Authentication
3. Cloud Pub/Sub - Asynchronous messaging
4. Cloud Dataflow - Paralle Data Processing (writes aggregates to BigQuery)
5. BigQuery Analytics Engine.

If you did not want to stream data, then you can batch load to Cloud Storage
After BigQuery, you can do data exploration with DataLab

### Project Terminology for BigQuery

A Project is what sets billing (who sends the bill to). In a project, there
are:

* Users - people who belong to a project
* Dataset - collection of tables and views (use views to limit access, like
  read only)
* Table - data with schema
* Job - query, import, export, copies, some job that interacts with the dataset

### BigQuery Tables and Jobs

So BigQuery is a columnar database; because of this, tables are a collection of
columns.

A typical datbase is a relational database, with record-oriented storage that
supports transactional updates.

For BigQuery Storage, each column is in a separate, compressed, encrypted file
that is replicated 3+ times. There are no indexes, keys or partitions required.
BigQuery is a great use for immutable, massive datasets.

### Querying BigQuery

Run the web console to query BigQuery. Basic commands are:

* Run - Run the Query
* Save Query - Saves the Query
* Validate - Shows how much data will be processed (gives an idea of cost)
* Explanation - Analyze Query Performance
* Export - Allows exports to CSV, JSON, Table, Google Sheets

### BigQuery SQL Syntax

Here's an example of the query syntax, basically SQL 2011 + extensions.

Sample Query:

    SELECT
      airline,
      SUM(IF(arrival_delay > 0, 1, 0)) AS num_delayed,
      COUNT(arrival_delay) AS total_flights
    FROM
      `bigquery-samples.airline_ontime_data.flights`
    WHERE
      arrival_airport='OKC'
      AND departure_airport='DFW'
    GROUP BY
      airline

Query Results

    Row     airline     num_delayed     total_flights
    1       AA          10312           23060
    2       OO          198             552
    3       EV          756             1912
    4       MQ          3884            7903

SQL Syntax is pretty typical:

    SELECT
      <BUILT-IN FUNCTIONS: SUM, IF, COUNT>
    FROM
      <PROJECT>.<DATASET>.<TABLE>
    WHERE
      <CLAUSE, BOOLEAN OPERATIONS>
    GROUP BY
      <FIELDS>

#### BigQuery SQL using Subqueries

You can also do standard SQL subqueries in BigQuery.

Original Data

    Row     airline     departure_airport   num_delayed     total_flights
    1       OH          MCO                 33              76
    2       XE          SAN                 317             759
    3       XE          EWR                 1985            3698
    4       WN          DAL                 9117            19555
    5       NW          MSP                 17              35

SQL Query with Subquery

    SELECT
      airline, departure_airport, num_delayed, total_flights,
      num_delayed/total_flights AS delayed_frac
    FROM
        # SubQuery Begins
        (SELECT
           airline, departure_airport,
           SUM(IF(arrival_delay > 0, 1, 0)) AS num_delayed,
           COUNT(arrival_delay) AS total_flights
         FROM
           `bigquery-samples.airline_ontime_data.flights`
         WHERE
           arrival_airport='OKC'
         GROUP BY
           airline, departure_airport)
    WHERE total_flights > 5
    ORDER by delayed_frac
    DESC LIMIT 5

Results

    Row airline departure_airport   num_delayed total_flights   delayed_frac
    1   OO      ATL                 260         360             0.72222222
    2   OH      ATL                 251         373             0.67282222
    3   EV      EWR                 191         303             0.63036303

#### BigQuery query multiple tables

You can also do SQL queries across multiple tables in BigQuery.

An example of a UNION ALL. Notice that we have a Wildcard `_` to match the same
sete of tables as the `FROM`

    SELECT
      FORMAT_UTC_USEC(event.timestamp_in_usec) AS time, request_url
    FROM
      [myproject-1234:applogs.events_20120501],
      [myproject-1234:applogs.events_20120502],
      [myproject-1234:applogs.events_20120503],
    WHERE
      event.username = 'root' AND
      NOT event.source_ip.is_internal;
    FROM
      TABLE_DATE_RANGE(myproject-1234:applogs.events_,
                       TIMESTAMP('2012-05-01'),
                       TIMESTAMP('2012-05-03'))

#### BigQuery JOIN ON fields across Tables

You can JOIN ON fields across Tables like standard SQL. Here the Inner Select
returns the days it rained at a station


    SELECT
      f.airline,
      SUM(IF(f.arrival_delay > 0, 1, 0)) AS num_delayed,
      COUNT(f.arrival_delay) AS total_flights
    FROM
      `bigquery-samples.airline_ontime_data.flights` AS f
    JOIN (
      SELECT
        CONCAT( CAST(year AS STRING), '-', LPAD(CAST(month AS STRING),2,'0'), '-', LPAD(CAST(day as STRING),2,'0')) AS rainyday
      FROM
        `bigquery-samples.weather_geo.gsod`
      WHERE
        station_number = 725030
        AND total_precipitation > 0) AS w
    ON
      w.rainyday = f.date
    WHERE f.arrival_airport = 'LGA'
    GROUP BY f.airline

### How does BigQuery fit in?

BigQuery acts as storage and as a data warehouse. Its important to see that we
have the following roles:

* Ingest/Capture with
  - Google App Engine
  - Google Cloud Logging
  - Google Cloud Pub/Sub
* Porcess with
  - Cloud DataFlow (Batch or Stream)
  - Cloud Dataproc
* Store with
  - BigQuery Storage (tables)
  - Cloud Storage (objects)
* Analyze
  - BigQuery Analytics (SQL)
* Visualize
  - 3rd Party Tools (Tableau, etc.)
  - Cloud DataLab

### Loading data with BigQuery

You can load data into BigQuery using a few different ways, including:

* `gsutil` tool in command line
* web interface
* python API
* `bq` command-line tool to load data

#### Using bq command-line

Load data using the `bq` (BigQuery) command. This looks at a Google Cloud
Storage, grabs all json type files, then loads them to BigQuery.

    bq load --source_format=NEWLINE_DELIMITED_JSON $DEVSHELL_PROJECT_ID:cpb101_flight_data.flights_2014 gs://cloud-training/CPB200/BQ/lab4/domestic_2014_flights_*.json ./schema_flight_performance.json

Verify with:

    bq ls $DEVSHELL_PROJECT_ID:cpb101_flight_data

You can export tables using the command line as well. Export formats include
CSV, JSON, and AVRO:

    bq extract cpb101_flight_data.AIRPORTS gs://williamqliu/bq/airports2.csv

### BigQuery Data Types

BigQuery supports all of the standard SQL types.

    Data Type,  Possible Value
    STRING      Variable-length character (Unicode) data
    INT64       64-bit integer
    FLOAT64     Double-precision (approximate) decimal values
    BOOL        True or False (case insensitive)
    ARRAY       Ordered list of zero or more elements of any non-ARRAY type
    STRUCT      Container of ordered fields each with a type (required) and
    field name (optional)
    TIMESTAMP   Absolute point in time with precision up to microseconds

### Advanced BigQuery

An example use of advanced SQL is here https://medium.com/@hoffa/the-top-weekend-languages-according-to-githubs-code-6022ea2e33e8

* `WITH` clause in SQL
* `ARRAY`
* `STRUCT`

## Dataflow

**Dataflow** is a way to execute Apache Beam data pipelines on the Google Cloud
Platform. We use dataflow to write data pipleines, carry out MapReduce
programs, deal with side inputs, and streaming.

An example would be a pipeline that reads from BigQuery and writes to Cloud
Storage. Dataflow runs through **steps** (aka **transforms**) that can be
executed in parallel.

### Apache Beam

**Apache Beam** (Batch + strEAM) is an open source model and set of APIs for
doing both batch and stream data processing. You can use Apache Beam for
Dataflow, as well as Apache Flink and Spark (so its pretty agnostic about the
execution engine, similar to how SQL is a unified language for databases).

#### How does it work?

The general idea is that we create our Pipeine, then do a series of applies.

    Pipeline p = Pipeline.create();
    p.apply(...)
     .apply(...)
     .apply(...)
    p.run()

#### Batch and Streams

So Apache Beam allows you to write code that processes both historical batch
data (data that is complete - bounded sources and syncs) to data that is
unbounded in nature.

If you are working with unbounded data (i.e. streaming), you apply a sliding
window still of say 60 minutes (so if you want an average, the calculation is
a moving average).

The idea is that it doesn't matter if you work with batch data (complete data)
or streaming data (sliding windows of data), you apply the same code to both.

#### Pipeline Terminology

* A **Pipeline** is a set of steps
* A **Transform** is a single step
* A **PCollection** (aka Parallel **Collection**) is a list of items that get the apply
  function run across the items from a Transform. Each transform creates a new PCollection.
* A **Runner** is an execution framework (e.g. Dataflow, Flink, Spark) that
  runs your pipeline
    - A **Direct Runner** executes locally on your laptop
    - A **Dataflow Runner** executes on the cloud
* A **Source** is where data comes from (e.g. from BigQuery, the sink, to Cloud
  Storage)
* A **Sink** is where data goes to

#### Python API

We read from a Source, apply our Transforms, then data goes to the Sink. In
Apache Beam, the pipe operator means apply. We first create the graph, then we
run it.

    import apache_beam as beam

    if __name__ == '__main__':
        # create a pipeline parameterized by commandline flags
        p = beam.Pipeline(argv=sys.argv)

        (p
            | beam.io.ReadFromText('gs://...') # read input
            | beam.FlatMap(lambda line: count_words(line)) # do something
            | beam.io.WriteToText('gs://...') # write output
        )

        p.run()  # run the pipeline

#### Applying Transform to PCollection

The data in a pipeline is represented by a Parallel Collection. Every transform
is applied to a Parallel Collection. A parallel collection does not have to be
in-memory, it can be unbounded / streaming data. Remember that P stands for
parallel processing.

Apply Transform to PCollection, then return PCollection. Here we give the
PCollection the name 'Length'.

    lines = p | ...
    sizes = lines | 'Length' >> beam.Map(lambda line: len(line))

You can replace a running pipeline. This is important so you don't lose any
data.

#### Read data into a Pipeline and Write data out of a Pipeline

##### Reading data into a Pipeline

To get data into a pipeline, we need to read data. We can read data from text,
from BigTable, BigQuery, Pub/Sub or a variety of different sources. E.g. You can
read from multiple files, that then make up your PCollection.

##### Writing data out of a Pipeline

To write data out of a pipeline, you can use TextIO.Write.to/data/output with
a suffix. Most writes are meant to be across multiple machines/files (to handle larger
data), but you can force a single machine only (beware much slower) by
specifying `withoutSharding`. 

#### Running a Pipeline

To run locally, run `main()` to run the pipeline locally

    python ./mypipe.py

To run on the cloud, specify the cloud parameters.

    python ./mypipe.py --project=$PROJECT --job_name=myjob
    --staging_location=gs://$BUCKET/staging/
    --temp_location=gs://$BUCKET/staging --runner=DataflowRunner

Notice you need a Job Name; these names should be unique (datetime might be good to be included)

### MapReduce in Dataflow

A common problem with larger data is taking a map reduce approach. You break up
the data set into pieces so that each compute node processes data that is local
to it. The map operations happen in parallel on chunks of the original input
data. The result of these maps are sent to one or more reduce nodes.

#### ParDo for parallel processing

**ParDo** stands for Parallel Do. Say you have 20 instances processing your map
operations. You do the processing on one item at a time, then emit the data
out.

##### ParDo in Python - Map vs FlatMap

Let's say our example eis to lambda a word, get the input and then return the
length of the word.

Use **Map** for a 1:1 relationship between input and output.

    'WordLengths' >> beam.Map( lambda word: (word, len(word))

Use **FlatMap** for non 1:1 relationships, usually with a generator. A good example
is a filtering operation. We choose whether we return a value or not. If it
matches, we yield, otherwise we don't return anything.

    def my_grep(line, term):
        if term in line:
            yield line

    'Grep' >> beam.FlatMap(lambda line: my_grep(line, searchTerm))


