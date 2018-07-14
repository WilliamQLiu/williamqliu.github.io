---
layout: post
title: Apache Spark 
---


# {{ page.title }}


Apache Spark is a fast and general engine for large-scale data processing.

## Installation

Install Steps:

1. Download the latest pre-built version from the Apache Spark site: e.g. spark-2.2.0-bin-hadoop2.7 
2. tar -zxvf spark-2.2.0-bin-hadoop2.7 
3. Extract this over to a place like /opt/spark-2.2.1
4. Setup environment variables in your .bashrc like:
	`export SPARK_HOME=/opt/spark-2.2.1` and
    `export PATH=$SPARK_HOME/bin:$PATH` 
5. Access the shell under `/opt/pyspark` or `/bin/spark-shell` (for scala)
   SPARK_./bin/spark-submit --master local[8] <python_file.py>

OR if you're using python, run `pip install pyspark` and `pip install py4j`

Note that if you install using the python route, the features are a little behind. 
For example, the python `sbin` doesn't have `start-master.sh` scripts. I recommend installing regularly instead
of through pip

## Run

### Exploration

If you want to explore around in a REPL, run `pyspark`
If you want to submit a regular job, run: `bin/spark-submit`

### Standalone Cluster

Run a server with `sbin/...` is where you run say hadoop or yarn

Example, a standalone master server:

	./sbin/start-master.sh

You'll get a web ui and a url: http://localhost:8080/
This will show you details like the master's url (e.g. Spark Master at
spark://xps:7077)

To connect workers to the master, run `./sbin/start-slave.sh spark://xps:7077`
You should now see workers connected

Now if you want to submit jobs to the master, simply do `bin/spark-submit spark://xps:7077` (or add that to your conf)
For example: `spark-submit --master spark://xps:7077 --packages
org.mongodb.spark:mongo-spark-connector_2.11:2.2.1 --jars
/usr/share/java/mysql-connector-java.jar ~/GitHub/automatic-guacamole/parsy.py`


## Setup Hadoop

Spark can read a few different file formats and systems, from csv to hadoop.

Configure:
https://www.digitalocean.com/community/tutorials/how-to-install-hadoop-in-stand-alone-mode-on-ubuntu-16-04

Environment Variables:
http://codesfusion.blogspot.com/2013/10/setup-hadoop-2x-220-on-ubuntu.html


## Make sure to install

sudo apt-get install ssh
sudo apt-get install openssh-client openssh-server


Run this otherwise you'll get permission denied when trying to ssh into your
own server
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

Hadoop Cluster on:

http://localhost:8088/cluster

50070:50070 -p 8088:8088 -p 8080:8080

## Submitting a job

Say you have spark-submit on `/usr/local/bin/spark-submit` and you want to submit a job. You can run with:

    spark-submit my_python_application.py --master <insert> -v

Let's explain the options:

    * `my_python_application.py` is the python spark app that should be executed
    * `-v` is for verbose
    * `--master` the master url specifying where jobs should be submitted to

### --master

There's a few different ways to specify the master url, including:

    * Specific master hostname (e.g. Spark Master at spark://13eb10996287:7077)
        URL: spark://13eb10996287:7077
        REST URL: spark://13eb10996287:6066 (cluster mode)
      So this would look like `spark-submit my_app.py --master spark://13eb10996287:7077`)
      To find the hostname, just run `hostname -f` or find hostname from the Spark UI at `:8080`
      The default port is `:7077`
    * `yarn` managed from YARN based on `YARN_CONF_DIR` or `HADOOP_CONF_DIR` variables

### --deploy-mode

There's a few different deploy modes, meaning we either deploy the driver on the worker nodes (`cluster`) or
locally as an external client (`client`, the default is client). Options are:

    * `cluster` - cluster mode makes everything run inside the cluster. You can start a job from your laptop
        and the job will continue running even if you close your computer. In this mode, the Spark Driver
        is encapsulated inside the YARN Application Master. Cluster mode is more suited for __long running__ jobs.
    * `client` - client mode makes the Spark Driver run on a client, such as your laptop. If the client is shut down,
        the job fails. Spark Executors still run on the cluster, and to schedule everything, a small YARN application
        master is created. Client Mode is more suited for __interactive__ jobs.

Sample job might look like:

    spark-submit my_job.py --master <master_url> --deploy-mode cluster -v

### Jobs UI

You should be able to see all __currently running jobs__ through the UI here: 

    localhost:4040/jobs

If there are multiple SparkContexts running, it'll bind to successive ports, e.g. `4041`, `4042`

https://spark.apache.org/docs/latest/monitoring.html

Note that as soon as the application is done, the UI disappears.

#### Jobs History Server

It is possible to view previous jobs through Spark's history server on:

    ./sbin/start-history-server.sh

This creates a web interface at `http://<server-url>:18080`

Note that in order for it work, you might have to create this directory:

    mkdir /tmp/spark-events

You can also log events here in the file `spark-defaults.conf` (`cp spark-defaults.conf.template spark-defaults.conf`)

    spark.eventLog.enabled true

    # can also specify a file outside local
    spark.eventLog.dir hdfs://namenode/shared/spark-logs

## Spark Architecture

https://spark.apache.org/docs/latest/cluster-overview.html

The Spark application that we're sending to run is an independent set of processes on a cluster that is coordinated
by the `SparkContext` object in your main program (i.e. the __driver program__).

To run an application on a cluster, the SparkContext can connect to different __cluster managers__. These cluster
managers can be standalone cluster managers, Mesos or YARN, which allocate resources across applications.

Once connected, Spark acquires __executors__ on nodes in the cluster, which are processes that run computations and
store data in your application. Once Spark has executors, it sends your application code (e.g. Python file passed to
SparkContext) to the executors. Finally SparkContext sends __tasks__ to the executors to run.

### Spark Components

Some things to note about each of the above Spark Architecture:

* Each application gets its own executor processes, which stay up for the duration of the whole application (i.e.
  each application is isolated from each other). Data cannot be shared across different Spark applications (instances
  of SparkContext)
* The driver program must listen for and accept incoming connections from its executors

### Spark Master

Start Master server iwth:
    
    ./sbin/start-master.sh

### Spark Slaves

Connect Slaves to Master with:

    ./sbin/start-slave.sh <master_url>

Make sure to start this on a separate container (don't have master and slave on the same container)

### Spark's MapPartition

With Spark, you want your objects to be serializable so that they can be transmitted across
the cluster to worker nodes.

Use a mappartition when you want an operation that requires a database connection. 
You want your regular code to be serializable, so with a mappartition, you can
process a complete partition in one go (i.e. just called once per partition)

## Anatomy of a Spark Job

1.) Spark Context / Spark Session Object (i.e. our Spark Application)
2.) Actions e.g. collect, saveAsTextFile (i.e. Job)
3.) Wide Transformations like `sort`, `groupByKey` (i.e. Stage, Stage)
4.) Computation to evaluate one partition, to combine narrow transforms (i.e. Task, Task)

## How Jobs Are Submitted and Run

Driver Program (i.e. SparkContext)
Driver Program is managed by a 'Cluster Manger'
There are many Worker Nodes that get jobs from the Driver Program
Worker Nodes have Executors that run many Tasks

## Parquet

__Parquet__ files are a __columnar__ format supported by many data processing systems. Advantages include automatically
preserving the schema of the original data. When writing Parquet files, all columns are automatically converted
to be nullable (for compatibility reasons). Dataframes can be saved as Parquet files.

### Why Parquet?

Parquet is a column oriented format vs your regular record oriented format (e.g. csv, tsv)
Parquet (and other columnar formats) can handle a lot more columns than you can in a well-designed relational database.

The data is _denormalized_ (instead of most relational formats where we try to have _third normal form_).
You have a lot of repeated values and tables flattened into a single format.

### Schema Evolution w/ Parquet

One of the main advantages of Parquet is that it supports __schema evolution__ (similar to ProtocolBuffer, Avro, Thrift).
We can start with a simple schema, then gradually add more columns to the schema as needed. You might end up with
multiple Parquet files with different, but mutually compatible schemas.

Files are stored with `.parquet` appended (e.g. `my_file.parquet`)

### Parquet Files

You can read or write a single Parquet file, which can be represented in say a table or a pandas dataframe.
You can read a single file back with say `pq.read_table('my_file.parquet')`

#### Parquet Dataset

In reality, a Parquet __dataset__ can consist of many files in many directories. A Parquet dataset can be presented
in a few ways, including:

* a list of Parquet absolute file paths
* a directory name containing nested directories defining a partitioned dataset

An example dataset partitioned by year and month might look like:

    dataset_name/
      year=2017/
        month=01/
          0.parq
          1.parq
        month=02/
      year=2018/

An example might look like:

    # Local dataset write
    pq.write_to_dataset(table, root_path='dataset_name', partition_columns=['one', 'two'])

You can also write to another filesystem (e.g. HDFS, S3)

### PyArrow

__PyArrow__ is an in-memory transport layer for data that is being read or written with Parquet files.
You can also use PyArrow for reading and writing Parquet files with pandas.

    import pyarrow.parquet as pq

