---
layout: post
title: Apache Bigtop
---


# {{ page.title }}

## Summary

Apache Bigtop is a way for developers to try out the Apache Hadoop ecosystem
quickly. 

[Apache Bigtop](https://github.com/apache/bigtop)

Bigtop can include components like:

* Hadoop
* HBase
* MapReduce
* Spark
* YARN
* Pig
* Hive
* Mahout

### Other Sandboxes

Bigtop is similar to other sandboxes like Hortonworks' data platform:

[Hortonworks Sandboxes](https://hortonworks.com/downloads/)

## Setup

### Sandbox

For a sandbox environment, head over to here:

[Bigtop Sandboxes](https://github.com/apache/bigtop/tree/master/docker/sandbox)

You can then build sandbox images using different os (e.g. ubuntu) and 
w/ components like hdfs, yarn, and spark:

    ./build.sh -a bigtop -o ubuntu-16.04 -c "hdfs, yarn, spark"

Run your sandbox with:

    docker run -d -p 50070:50070 -p 8088:8088 -p 8080:8080
    bigtop/sandbox:ubuntu-16.04_hdfs_yarn_spark

### Provisioner

Head over to the provisioner section and select your setup (e.g. Docker,
Vagrant). Here you can create your clusters.

[Bigtop Provisioners](https://github.com/apache/bigtop/tree/master/provisioner/docker)

You can create and destroy clusters or run commands on individual containers.

* Create a Bigtop Hadoop cluster (w/ three nodes): `./docker-hadoop.sh --create
  3`
* Destroy a Bigtop Hadoop cluster: `./docker-hadoop.sh --destroy`
* Login to first container: `./docker-hadoop.sh --exec 1 bash`
* Execute a command on the second container: `./docker-hadoop.sh --exec
  2 hadoop fs -ls /`

## Example Project

The BigPetStore is an example application for the Hadoop/Spark ecosystems.
BigPetStore generates and analyzes synthetic transaction data for a fictional
chain of petstores.

[Bigtop BigPetStore](https://github.com/apache/bigtop/tree/master/bigtop-bigpetstore)

The general idea is that this Spark application:

* Generates raw (dirty) data on the file system (a csv file w/ a few fields
  that can cover from Store ID, Store location, Customer ID, Customer name,
  Transaction ID, Transaction datetime, Transaction Product (e.g. category=dry
  cat food;brand=Feisty Feline;flavor=Chicken)
* Runs ETL component as a spark job to read the data, parse the data times and
  products, normalizes the data, and writes out the RDD for each type of 
  class (Store, Customer, Location, Product, Transaction) from the data model
  above.
* We can then run analytics on the data and use this to write out custom
  results (e.g. JSON results of the number of Total Transactions, Transaction
  Count by Month, Product, and Location)

