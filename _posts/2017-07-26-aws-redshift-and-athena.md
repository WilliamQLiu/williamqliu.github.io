---
layout: post
title: AWS Redshift and AWS Athena
---


# {{ page.title }}


# Summary

AWS Redshift vs AWS Athena

**AWS Redshift Spectrum** allows you to run SQL queries against unstructured data
in AWS S3. You can use open data formats like CSV, TSV, Parquet, Sequence, and
RCFile. AWS Redshift is Amazon's data warehouse solution. Most databases store data in
**rows**, but Redshift is a **column** datastore.

**AWS Athena** is an interactive query service that makes it easy to analyze
data in S3 using standard SQL. You don't need to setup a server. Simply point to an S3,
define the schema, and start querying using standard SQL. Athena is more for
very simple reporting. Athena also supports CSV, JSON, Gzip files, and columnar formats
like Apache Parquet

__Presto__ - a distributed SQL query engine for big data

## Row vs Columns

### Data

A relational database provides data that usually gets represented in a two-dimensional
table of columns and rows. E.g.

    RowId EmpId Lastname Firstname Salary
    1     10    Smith    Joe       25000
    2     12    Jones    Mary      40000
    3     11    Johnson  Cathy     55000
    4     20    Jones    Bob       30000

The main idea is that the two-dimensional format is an abstraction. In an
actual implementation, storage hardware serializes the data into some form.
Hard disks are organized into series of fixed size blocks (usually large enough to fix several rows of a table).

### Row-Oriented Database

As data is inserted into the table, that row of data is assigned an internal
id (RowId). This matches the common use-case of a system retriving information
about a specific object (e.g. a user in a rolodex). A row-based system is
designed to efficiently return data for an entire row.

A row-oriented table might serialize each row of data like this:

    1:10,Smith,Joe,25000
    2:12,Jones,Mary,40000
    3:11,Johnson,Cathy,55000
    4:20,Jones,Bob,30000

Row-base systems are not efficient at performing set-wide operations on the
whole table (as opposed to a small number of specific records). For example, if
we want to find salaries between 30,000 and 45,000, the database has to scan
the entire table.

### Indexes

To improve the performance of these sort of operations (e.g. find salaries
between x and y), relational databases use **database indexes** to store all
the values from a set of columns along with rowid pointers back to the original
table. An index on salary might look like:

    1:25000
    2:40000
    3:55000
    4:30000

Indexes help with lookups, but maintaining indexes adds overhead to the system
(like when new data is written to the database, then any attached indexes also
have to be updated).

### Column-Oriented Database

A column-oriented database serializes the values of a column together. For
example:

    10:1,12:2,11:3,20:4
    Smith:1,Jones:2,Johnson:3,Jones:4
    Joe:1,Mary:2,Cathy:3,Bob:4
    25000:1,40000:2,55000:3,30000:4

One big difference between the systems is that with the row-based system, the primary 
key is the rowid field. In the column-based system, the primary key is the
data. So what does that mean? If we look in detail on the column-based system,
we see that for duplicate data (e.g. 'Jones'), we're able to store the same
value only once, like in this serialized version:

    ;Smith:1,Jones:2,4;Johnson3;

### So which one is better (row or column)?

The type of data store (row or column) depends on the type of data you're
working with.

* Find all the people with the last name Jones? Answer is retrieved in asingle
  operation so this would be a better fit for column-based data stores.
* Get the first, middle, last name, phone numbers, and address from a rolodex? These
  would be better suited for a row-based data store.

## Storage Options (Local vs S3 and Redshift Spectrum)

So for data storage, you can store data locally on a regular server or on the
'cloud' with S3 and Redshift-Spectrum.

### Local Storage

For local disk storage (say a traditional ssd), the storage is tied to
computation. You have to make full read-only copies to scale.

### S3 with Redshift Spectrum and Athena

Uses AWS Athena Meta-data to understand files in S3. This decouples storage
from compute. Still must make read-only copies, but of meta-data only, so
smaller and faster to scale.

### Redshift - Load data

Redshift isn't meant to be a transactional database (i.e. no updates, deletes)
The preferred way of working with Redshift is to COPY to load data from files
from any of the following:

* S3
* EMR (Elastic Map Reduce) e.g Hadoop
* Remote EC2 Host
* DynamoDB Tables
* Not preferred method: DML

### Redshift vs Athena

So what's different between Redshift and Athena?

* Athena uses S3 as storage; Redshift uses attached SSD disks
* Athena automatically scales; Redshift you have to add instances/size
* Athena automatically parallel; Redshift only as parallel as you configure
* Athena data can be stored in multiple formats per table; Redshift can be
  loaded from files in multiple formats

# Presto

https://github.com/prestodb/presto

## Build Presto from Source

### Install Maven

    # Check for Maven
    sudo apt policy maven

    maven:
    Installed: 3.5.2-2
    Candidate: 3.5.2-2
    Version table:
    *** 3.5.2-2 500
            500 http://archive.ubuntu.com/ubuntu bionic/universe amd64 Packages
            500 http://archive.ubuntu.com/ubuntu bionic/universe i386 Packages
            100 /var/lib/dpkg/status

    sudo apt install maven

    # Find Maven Install
    ls -lsa /usr/share/maven
    total 32
     4 drwxr-xr-x   6 root root  4096 Oct  9 07:07 .
    12 drwxr-xr-x 382 root root 12288 Jan 27 11:23 ..
     4 drwxr-xr-x   2 root root  4096 Oct  9 07:07 bin
     4 drwxr-xr-x   2 root root  4096 Oct  9 07:07 boot
     0 lrwxrwxrwx   1 root root    10 Feb 23  2018 conf -> /etc/maven
     4 drwxr-xr-x   2 root root  4096 Oct  9 07:07 lib
     4 drwxr-xr-x   2 root root  4096 Oct  9 07:07 man

    # Find Maven properties
    ls -lsa /etc/maven
    total 40
     4 drwxr-xr-x   3 root root  4096 Oct  9 07:07 .
    12 drwxr-xr-x 160 root root 12288 Jan 27 11:23 ..
     4 drwxr-xr-x   2 root root  4096 Oct  9 07:07 logging
     4 -rw-r--r--   1 root root   220 Oct 18  2017 m2.conf
    12 -rw-r--r--   1 root root 10211 Oct 18  2017 settings.xml
     4 -rw-r--r--   1 root root  3645 Oct 18  2017 toolchains.xml

### Install JDK

    sudo apt-get update
    sudo apt-get install default-jre  # install Java Runtime Environment (JRE)
    sudo apt-get install default-jdk  # has JRE in it as well, slightly larger than JRE only

My jre that I wanted to use was in: `/usr/lib/jvm/java-1.11.0-openjdk-amd64`
My `.bashrc` has

    export JAVA_HOME="/usr/lib/jvm/java-1.11.0-openjdk-amd64"
    export PATH=$PATH:$JAVA_HOME

    
## Installing and Deploying Presto Server

Follow the instructions here: https://prestodb.github.io/docs/current/installation/deployment.html

    #Copy the latest release
    cd /tmp && wget https://repo1.maven.org/maven2/com/facebook/presto/presto-server/0.216/presto-server-0.216.tar.gz
    tar -xvf presto-server-0.216.tar.gz

## Installing and using Presto CLI

Install the CLI for Presto here: https://prestodb.github.io/docs/current/installation/cli.html

The downloaded file can be renamed to `presto` and `chmod +x`, then moved to `/usr/local/bin`

Can run with: `presto` or say `presto --server localhost:8080 --catalog hive --schema default`

## Presto JDBC Driver

https://prestodb.github.io/docs/current/installation/jdbc.html

# Athena

Setup a database, table, schema and run your queries

## DDL

__Data Definition Language (DDL)__ is a standard for commands that define the different structures in a database.
DDL statements create, modify, and remove database objects such as tables, indexes, and users.
Common DDL statements are CREATE, ALTER, and DROP.

    /* Create a table */
    CREATE EXTERNAL TABLE IF NOT EXISTS default.elb_logs (
      `request_timestamp` string,
      `elb_name` string,
      `request_ip` string,
      `request_port` int,
      `backend_ip` string,
      `backend_port` int,
      `request_processing_time` double,
      `backend_processing_time` double,
      `client_response_time` double,
      `elb_response_code` string,
      `backend_response_code` string,
      `received_bytes` bigint,
      `sent_bytes` bigint,
      `request_verb` string,
      `url` string,
      `protocol` string,
      `user_agent` string,
      `ssl_cipher` string,
      `ssl_protocol` string 
    )
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
    WITH SERDEPROPERTIES (
      'serialization.format' = '1',
      'input.regex' = '([^ ]*) ([^ ]*) ([^ ]*):([0-9]*) ([^ ]*):([0-9]*) ([.0-9]*) ([.0-9]*) ([.0-9]*) (-|[0-9]*) (-|[0-9]*) ([-0-9]*) ([-0-9]*) \"([^ ]*) ([^ ]*) (- |[^ ]*)\" (\"[^\"]*\") ([A-Z0-9-]+) ([A-Za-z0-9.-]*)$'
    ) LOCATION 's3://athena-examples-us-east-1/elb/plaintext/'
    TBLPROPERTIES ('has_encrypted_data'='false');
