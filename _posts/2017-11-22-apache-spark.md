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
For example, the python `sbin` doesn't have `start-master.sh` scripts.

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

