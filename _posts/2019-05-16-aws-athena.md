---
layout: post
title: AWS Athena
---


# {{ page.title }}

**AWS Athena** is an interactive query service that makes it easy to analyze
data in S3 using standard SQL. You don't need to setup a server. Simply point to an S3,
define the schema, and start querying using standard SQL. Athena is more for
very simple reporting. Athena also supports CSV, JSON, Gzip files, and columnar formats
like Apache Parquet.

Note that Athena is based off of __Presto__, a distributed SQL query engine for big data.

# Presto

Athena uses Presto.

https://github.com/prestodb/presto

If you want to explore Presto more:

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

## Partitions

### Add partitions for a table

```
ALTER TABLE orders ADD
  PARTITION (dt = '2016-05-14', country = 'IN') LOCATION 's3://mystorage/path/to/INDIA_14_May_2016/'
  PARTITION (dt = '2016-05-15', country = 'IN') LOCATION 's3://mystorage/path/to/INDIA_15_May_2016/';
```

### Drop partitions for a table

```
LTER TABLE orders
DROP PARTITION (dt = '2014-05-14', country = 'IN'), PARTITION (dt = '2014-05-15', country = 'IN');
```

### Show the partitions for a table

```
SHOW PARTITIONS my_schema.my_table;

run_date=2022-10-01
run_date=2022-10-02
```

### Select the partitions

```
SELECT * FROM "my_table$partitions" LIMIT 100;
```

### Show the Locations

Assuming you are partitioning on something like `run_date`

```
SELECT DISTINCT run_date, "$path" FROM my_schema.my_table
ORDER BY run_date DESC
LIMIT 1000

run_date, $path
2022-10-12, s3://mybucket/something/here/run_date=2022-10-12/files
2022-10-13, s3://mybucket/something/here/run_date=2022-10-13/files

```

### Change the Partition Location

If you don't want to drop and add, can just change the partition location

```
ALTER TABLE customers PARTITION (zip='98040', state='WA') SET LOCATION 's3://mystorage/custdata/';
```

### Debugging a failed query

```
❯ aws athena get-query-execution --query-execution-id a6475246-xxxx-xxxx-xxxx-xxxxxxxxxxx

{
    "QueryExecution": {
        "QueryExecutionId": "a6475246-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "Query": ...
```

## Athena Integration with AWS Glue

__AWS Glue__ is a fully managed ETL service that can categorize your data, clean it, enrich it, and move it reliably
between various data stores. __AWS Glue crawlers__ automatically infer database and table schema from your dataset,
storing the associated metadata in the __AWS Glue Data Catalog__.

So how does Athena work with Glue? Athena supports querying datasets and data sources that are registered with the
AWS Glue Data Catalog.

* When you run __Data Manipulation Language (DML)__ queries in Athena with the Data Catalog
as your source, you are using the Data Catalog shcmea.
* When you run __Data Definition Language ((DDL)__ queries, the schema you define are defined in the AWS Glue Data Catalog.

### AWS Glue Schema Registry

AWS Glue Schema Registry is a feature that lets you discover, control, and evolve data stream schemas.
A __schema__ defines the structure and format of a data record. With Glue Schema Registry, you can manage
and enforce schemas on your data streaming applications. Since Glue Schema Registry is in AWS, there's convenient
integrations with MSK, Kinesis Data Streams, Kinesis Data Analytics for Apache Flink, and AWS Lambda.

So what does Schema Registry solve? You can use a schema as a data contract between producers and consumers to
ensure resilience for cases like upstream changes. The idea is that you can have many systems share a schema for
serialization and de-serialization.

#### What is a schema?

A __schema__ defines the structure and format of a data record at a specific version. Formats can be in AVRO and
has Java language support.

#### What is a registry?

A __registry__ is a logical container of schemas. Registries allow you to organize your schemas as well as manage
access for which applications can access which schemas. A registry has an Amazon Resource Name (ARN) that lets you
organize and set different access permissions to different schema operations.

#### What are Open Source Serde Libraries?

Serde libraries are serializer and deserializer libraries used for parsing data from different
formats, such as CSV, JSON, Parquet, Apache AVRO, Apache Parquet, and ORC.

#### Versioning and Compatibility

Each schema can have multiple versions. Versioning is determined by a compatibility rule
that is applied on a schema. When you try to register a new schema version, rules are
checked based off the previous compatibility rule. There are 8 compatibility modes:

* `NONE` - No compatibility mode applies; use for development.
  Any new version will be accepted and there are no compatibility checks.
* `DISABLED` - Prevents versioning for a particular schema. No new versions can be added.
* `BACKWARD` - Recommended; allows consumers to read both the current and previous schema version.
  Use this to check compatibility against the previous schema version when you delete fields
  or add optional fields.
* `BACKWARD_ALL` - Allows consumers to read both current and all previous schema versions.
  Use this to check compatibility against all previous schema versions when you delete fields
  or add optional fields.
* `FORWARD` - Allows consumers to read both current and the subsequent schema versions, but not
  the later versions. A use case is when your application has been created for a previous schema
  and should be able to process a more recent schema.
* `FORWARD_ALL` - Allow consumers to read data written by producers of any new registered schema.
  Use this choice when you need to add fields or delete optional fields, and check compatibility
  against all previous schema versions.
* `FULL` - Allows consumers to read data written by producers using the previous or next version
  of the schema, but not earlier or later versions. You can use this choice to check compatibility
  against the last schema version when you add or remove optional fields.
* `FULL_ALL` - Allows consumers to read data written by producers using all previous schema versions.
  Use this choice to check compatibility against all previous schema versions when you add or remove
  optional fields.

#### How does the Schema Registry work?

The Schema Registry works as:

1. Register a schema. The schema name should equal to the name of the destination (e.g. `test_topic`, `test_stream`)
   or the producer can provide a custom name for the schema. Producers can also add key-value pairs to the schema
   as metadata (e.g. `source: msk_kafka_topic_A`).
   Once registered, the Schema Registry returns the schema version id to the serializer.
   Note: Compatibility checks are then done on the schema to ensure the new version is compatible before being registered
   as the new version.
   You can register the schema in two ways: __manually__ or __auto-registration__.
   * Manual is done through the AWS Glue Console or CLI/SDK
   * Auto-Registration is done if you turn this setting on in the producer configurations.
     If a `REGISTRY_NAME` is not provided, then the new schema version will be under the default
     registry of `default-registry`.
2. The serializer validates the data records against the schema. If the schem of the record does not
   match a registered schema, the serializer will return an exception and the application will fail to deliver the
   record to the destination.
3. Serialize and deliver the records (if the record compiles with the schema with optional compression).
4. Consumers deserialize the data; consumer reads the data using the Schema Registry deserializer library
5. Deserializer may request the schema from the Schema Registry; the schema may also be cached locally on the consumer
   If the Schema Registry cannot deserialize the record, the consumer can log the data from the record and move on or
   half the application
6. The deserialize uses the schema to deserialize the record (with optional decompressing if record was compressed)

Note: There is also resource-level permissions and identity-based IAM policies you can apply

