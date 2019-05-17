---
layout: post
title: AWS Redshift Spectrum and AWS Athena
---


# {{ page.title }}


# Summary

AWS Redshift Spectrum vs AWS Athena

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

## Optional: Build Presto from Source

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

Setup files:

    /presto-server-0.216/etc/config.properties
    coordinator=true
    coordinator=true
    node-scheduler.include-coordinator=true
    http-server.http.port=8081
    query.max-memory=5GB
    query.max-memory-per-node=1GB
    query.max-total-memory-per-node=2GB
    discovery-server.enabled=true
    discovery.uri=http://localhost:8081

    /presto-server-0.216/etc/node.properties
    node.environment=production
    node.id=ffffffff-ffff-ffff-ffff-ffffffffffff
    node.data-dir=/var/presto/data

    /presto-server-0.216/etc/log.properties
    com.facebook.presto=INFO 

Setup Catalogs:

    /presto-server-0.216/etc/catalog/localfile.properties
    connect.name=localfile

Run the UI:

    cd presto-server-0.216/bin && ./launcher
    # default, it's 8080, but my port for this was used
    You'll see a UI of the number of queries at: http://localhost:8081/ui/

Usage:

Create an `/etc/catalog` directory, where we'll put our __catalogs__, which all you to connect to different data sources
Example catalogs include: MySQL, Hive, LocalFile. When you add a catalog using `presto-admin`, make sure to restart
the presto server.


## Installing and using Presto CLI

Install the CLI for Presto here: https://prestodb.github.io/docs/current/installation/cli.html

The downloaded file can be renamed to `presto` and `chmod +x`, then moved to `/usr/local/bin`

Can run with: `presto` or say `presto --server localhost:8081 --catalog hive --schema default`

If you get into an error, make sure to run with a `debug` flag (e.g. `./presto-cli --server localhost:8081 --debug`
The errors that I've seen are issues like I setup a catalog incorrectly.

### Presto-cli Commands

#### Show catalogs

    presto> show catalogs;

    show catalogs;
      Catalog  
    -----------
     localfile 
     system    
    (2 rows)

    Query 20190220_235643_00000_jjy5y, FINISHED, 1 node
    http://will.data.lan:8081/ui/query.html?20190220_235643_00000_jjy5y
    Splits: 19 total, 19 done (100.00%)
    CPU Time: 0.1s total,     0 rows/s,     0B/s, 8% active
    Per Node: 0.0 parallelism,     0 rows/s,     0B/s
    Parallelism: 0.0
    Peak Memory: 0B
    0:02 [0 rows, 0B] [0 rows/s, 0B/s]

#### Show schemas from catalog

    #show schemas from <catalog> <like pattern>
    presto> show schemas from localfile;
           Schema       
    --------------------
     information_schema 
     logs               
    (2 rows)

    Query 20190221_000330_00011_jjy5y, FINISHED, 1 node
    http://will.data.lan:8081/ui/query.html?20190221_000330_00011_jjy5y
    Splits: 19 total, 19 done (100.00%)
    CPU Time: 0.0s total,   125 rows/s, 1.95KB/s, 5% active
    Per Node: 0.1 parallelism,     6 rows/s,   103B/s
    Parallelism: 0.1
    Peak Memory: 82.4KB
    0:00 [2 rows, 32B] [6 rows/s, 103B/s]

#### Show tables from catalog.schemas

    presto>show tables from localfile.logs;
    Table       
    ------------------
     http_request_log 
    (1 row)

    Query 20190221_185008_00029_jjy5y, FINISHED, 1 node
    http://will.data.lan:8081/ui/query.html?20190221_185008_00029_jjy5y
    Splits: 19 total, 19 done (100.00%)
    CPU Time: 0.0s total,   111 rows/s, 3.25KB/s, 7% active
    Per Node: 0.0 parallelism,     5 rows/s,   164B/s
    Parallelism: 0.0
    Peak Memory: 0B
    0:00 [1 rows, 30B] [5 rows/s, 164B/s]


## Setup Presto-Admin

__Presto-Admin__ is a tool for installing and managing the Presto query engine on a cluster.

https://github.com/prestodb/presto-admi://github.com/prestodb/presto-admin

I downloaded and ran `python setup.py develop` to get `presto-admin` to work.

    $ presto-admin
    Usage: presto-admin [options] <command> [arg]

    Options:
      --version             show program's version number and exit
      -h, --help            show this help message and exit
      -d, --display         print detailed information about command
      --extended-help       print out all options, including advanced ones
      -I, --initial-password-prompt
                            Force password prompt up-front
      -p PASSWORD, --password=PASSWORD
                            password for use with authentication and/or sudo


    Commands:
        catalog add
        catalog remove
        collect logs
        collect query_info
        collect system_info
        configuration deploy
        configuration show
        file copy
        file run
        package install
        package uninstall
        plugin add_jar
        server install
        server restart
        server start
        server status
        server stop
        server uninstall
        server upgrade
        topology show

### Presto JDBC Driver

https://prestodb.github.io/docs/current/installation/jdbc.html

## Apache Drill

Another program to query your data is __Apache Drill__.

* Run with `bin/drill-embedded`
* Stop with `!quit`

### Apache Drill Usage

    0: jdbc:drill:zk=local> show schemas;
    +---------------------+
    |     SCHEMA_NAME     |
    +---------------------+
    | cp.default          |
    | dfs.default         |
    | dfs.root            |
    | dfs.tmp             |
    | information_schema  |
    | sys                 |
    +---------------------+
    6 rows selected (0.23 seconds)

    0: jdbc:drill:zk=local> use cp;
    +-------+---------------------------------+
    |  ok   |             summary             |
    +-------+---------------------------------+
    | true  | Default schema changed to [cp]  |
    +-------+---------------------------------+
    1 row selected (0.082 seconds)

To query from a local file or directory, run your query like so for a file system (say there's Parquet files there):

    0: jdbc:drill:zk=local> select * from dfs.`/usr/local/airflow/data/`

Keep in mind that certain queries don't work (e.g. `DESCRIBE` to show columns will error out with an invalid schema)

A useful setting, especially for a lot of columns, is to:

    0: jdbc:drill:zk=local> !set maxwidth 10000

To quit, run `!quit`


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

