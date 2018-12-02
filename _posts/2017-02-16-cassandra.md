---
layout: post
title: Cassandra
---


# {{ page.title }}

# Summary

If you’re looking into a NoSQL database, Cassandra is a solid choice. I’m barely a month into using Cassandra 
with Django, but these are my notes on what to look for (and hopefully pitfalls to avoid).

# Install

Cassandra is pretty stable, but that doesn’t mean you should install the latest version (which is v3 at the time 
of this writing). Check version compatibility; meaning check the versions of the programs and libraries that 
you’ll be using (hint: it’s usually not the latest version). For example, if you’re doing Spark Streaming 
and need to write to Cassandra immediately, look at the versions for the below and find the highest matching version:

    Spark + Spark Streaming library
    Cassandra
    Spark Streaming library connector to Cassandra (https://github.com/datastax/spark-cassandra-connector#version-compatibility)

Speaking of which, DataStax is the company that supports a lot of Cassandra’s tools. That connector library that 
was mentioned above, DataStax open sourced it. They’ll be your go to source for many Cassandra related topics.

I learned the above install lesson the hard way by doing installs first, writing some Scala code, 
then realizing the libraries I needed didn’t support the version of Cassandra I had. Some common systems used 
with Cassandra include Spark, Spark Streaming, and Kafka.

Also, most of the features that are in Cassandra v3 aren’t needed; I only ran into one issue that was in v3 that 
was not in v2 (Queryset filtering IN on a partition key), but that was an inefficient query and should have been 
redesigned better. Some of the new features in v3 (like joins) kind of go against the philosophy of NoSQL databases 
(more on that later - see Data Modeling further down).

## Install (and run with Docker)

You can download cassandra and `bash cassandra` to run it, or if you want, you can try docker.

* Install docker
* Get the docker image: `docker pull cassandra`
* Run docker of cassandra and available on port 9042 (the default port): `docker run --name cassandra -p 9042:9042 -d cassandra`
* To connect to the cassandra container into sqlsh: `docker exec -it cassandra cqlsh`
* To connect to the cassandra container into shell: `docker exec -it cassandra bash`

# DataStax

I want to mention DataStax again because they are the go-to resource for Cassandra.
They offer great short videos that cover the basics and design philosophy of Cassandra
A lot of tech videos are boring, but these really just get to the point.

* If you’re a GUI person, download the DevCenter here (it’s like MySQLWorkbench or pgAdmin3)
* If you want to see drivers (e.g. connect to Cassandra using Python, Java, etc.), check out DataStax’s Github repos here (https://github.com/datastax/)

The main driver we’ll be using (and what other Django libraries base their connectors off of) is this Python driver repo
In particular, the documentation is great and you’ll mainly use their Object Mapper (they have Models and Queries that are very similar to Django)(http://datastax.github.io/python-driver/api/index.html#object-mapper)

# Cassandra Concepts

Here’s some notes about why we want to use Cassandra and its advantages/weaknesses:
At least watch this intro video here (https://academy.datastax.com/resources/ds101-introduction-cassandra) about what 
Cassandra does.

## Cassandra Data Modeling

Cassandra's basics of data modeling (https://academy.datastax.com/courses/ds220-data-modeling/) make the following main ideas:

* With large data, ACID doesn’t hold up (no consistency due to slave database replication lag)
* Third Normal Form doesn’t scale well
* Sharding makes data everywhere (issue because we need to hit all shards for even a simple query aka scatter / gather is no good; 
  queries should ideally hit a single machine or a small set of machines). 
  Re-sharding is a manual process (nightmare for lots of things to go wrong)

## Cassandra Cassandra Architecture

Cassandra's Architecture is:

* Not a drop in replacement for relational databases
* CAP tradeoff (cannot be both consistent and highly available); Cassandra focuses on being highly available and 
  does not try to be as consistent (it’s not all black and white; you can tune these features in the config)
* All nodes (i.e. virtual machines) are all equal; each node owns a range of hashes
  -  Each primary key (partition key) is run through a hashing function and determines which range of hashes (thus which node to talk to)
* Data is replicated, all nodes are equal; no master/slave/replica (they can all read and write)
* No config servers (e.g. zookeeper) - you can config Cassandra, but you don’t need a config server
* __Replication Factor (RF)__ means data is always replicated to each replica. By default we have a replication factor of 3 
  (meaning 3 servers will have this same data)
* __Consistency Levels (ONE, QUORUM, ALL)__: How many replicas do I need to hear from before it’s considered good to go 
  (saying here’s the data we queried and that looks right or that we wrote the data in enough servers to say it’s safe).
  Note: Quorum means majority. This field is huge because we can use this option in our config settings and that determines 
  how much of the CAP tradeoff you’re willing to sacrifice.
*  For a given request, we can write to any node (the Coordinator node). Writes are written to a __commit log__, then to __memtable__, 
  memtable flushed to disk periodically (__sstable__). Compaction merges together multiple sstables and uses the last timestamp.
  Updates are immutable (deletes are a special write called a __tombstone__)

# Django Integration with Cassandra

So you’ve installed and started running Cassandra locally (i.e. `bash cassandra`), and can look around the system using the DevCenter GUI or through their command line `cqlsh`. You can write some CQL (their version of SQL) to look around or create some data (CQL syntax is mostly the same as SQL, but you can look over it here for the details) Now how do you read and write data in programmatically with Django?

Let’s look at the libraries (i.e. how to access data without the cql shell or GUI):
*  If you want to interface with Cassandra programmatically through plain Python, use the DataStax library mentioned above
    https://datastax.github.io/python-driver/index.html
    `pip install cassandra-driver`
* If you want to use Django with Cassandra, use this library (it integrates and uses a lot of the DataStax python library)
    https://github.com/r4fek/django-cassandra-engine
    `pip install django-cassandra-engine`

Let’s talk a bit more about the Django Cassandra Engine. Here’s what to look out for:

## Data Modeling with Django and Cassandra

For Data Modeling, we replace the Django Models with the Cassandra Models.

* Instead of fields you have columns (just a name difference)
* There is no ‘Meta’ field in the Cassandra Models (this affects a lot of things, mainly DRF and unable to really make database migrations)
* Fields/Columns are similar (e.g. columns.Text, columns.Integer, columns.Boolean. The only exception are the field options (due to Cassandra being a NoSQL database, there are no relations like Foreign Key). Instead, you’ll see options for the below (see ‘Details about Cassandra Data Modeling’ for more info).
  - primary_key
  - partition_key
  - Clustering_order
* The main takeaway is that unless you set your field with a partition_key, that field will not be searchable (e.g. you can’t do a SomeModel.objects.get(searchable_field=’stuff’))

### Additional Details about Cassandra Data Modeling - Keys

Additional Notes about Data Modeling and definitions of keys and here:

* __Primary Key__ - primary key is one or more columns used to retrieve data from a table
    - We must define a Primary Key (Note this can be made up of multiple columns)
    - A __Compound Primary Key__ is a Primary Key that uses two or more columns together to create the Primary Key (aka Composite Key below).
    - We need to create a Primary Key / Composite Key that is __unique__ (or else multiple rows will erase the existing data) and 
      how we’re __looking something up__
* __Composite Key__ (aka __Compound Primary Key__) - is a primary key that is generated from two or more columns to 
  create the Primary Key. This can be broken down into:
    - __Partition Key__ - First part of the Composite Key (note that this can be multiple columns). Think of this as a giant GROUP BY (e.g. partition by movie year so you can get all movies by Year 2014, 2015, etc, you can search by this)
    - __Clustering Key__ - Second part of the Composite Key (note that this can be multiple columns), aka Clustering Columns - this is where you want fields that can do SORT or ORDER BY
    - If you want to SORT or ORDER BY, you need to add that field to the clustering-key (and a WHERE by Range)
    - If you want to search by WHERE, you need to have the field in the primary key 
    - For example, we can have PRIMARY KEY( (partitionKey1, partitionKey2), clusteringKey1, clusteringKey2, clusteringKey3)
      Note: the python drivers is kinda weird  - basically the partition_key is the ‘primary_key’ and the primary_key is a primary key OR cluster key

So how do keys affect things like indexes? There’s __primary__ and __secondary indexes__.

* When to use an index: Best used on a table that has many rows with that index (i.e. the more unique values you have in that indexed column, the more overhead you will have). For example, a good index would be the User’s Address State (e.g. CA, NY, TX) since there are a lot of Users that share that info and it is rare for a State to be updated or deleted
* When NOT to use an index: Do not use an index for a column that has many unique values, like a User’s email address 
  (i.e. email address is a __high-cardinality__ column index)
* Secondary indexes are used to query a table column that is not normally queryable, but has the disadvantage of 
  being possibly a performance nightmare.

Example Model:

    import uuid
    from cassandra.cqlengine import columns
    from cassandra.cqlengine.models import Model

    class ExampleModel(Model):
        example_id    = columns.UUID(primary_key=True, default=uuid.uuid4)
        some_category = columns.Text(primary_key=True, partition_key=True, clustering_order=”DESC”)
        example_type  = columns.Integer(index=True)
        created_at    = columns.DateTime()
        description   = columns.Text(required=False)

# Django REST Framework

DRF needs some modifications to use:

## Serializers

* You cannot use ModelSerializers (due to Meta missing in Models)
* You can use serializers.Serializer instead
* You will need to override the create method (i.e. `def create` method must return `Model.objects.create(**validated_data)`)

## Views

* You can use generic views like `generics.RetrieveUpdateDestroyAPIView`
* You will need to override the `get_object` method (i.e. `get_object` method must return `Model.objects(pk==self.kwargs[‘pk’])  # or whatever the object selection is

## Database

Cassandra didn’t work well with DRF (e.g. updating the built-in documentation on DRF on a Model update) unless we explicitly declared the options. This setting worked:

    DATABASES = {
       'sqlite': {
           'ENGINE': 'django.db.backends.sqlite3',
           'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
       },

       'default': {  # Run 'manage.py sync_cassandra'
           'ENGINE': 'django_cassandra_engine',
           'NAME': 'cassdb',
           'USER': 'test',
           'PASSWORD': 'test',
           'TEST_NAME': 'test_test',
           'HOST': 'localhost',
           'OPTIONS': {
               'replication': {
                   'strategy_class': 'SimpleStrategy',
                   'replication_factor': 1
               },
               'connection': {
                   'consistency': ConsistencyLevel.LOCAL_ONE,
                   'retry_connect': True,
                   #'port': 9042,
                   # + All connection options for cassandra.cluster.Cluster()
               },
               'session': {
                   'default_timeout': 10,
                   'default_fetch_size': 10000
                   # + All options for cassandra.cluster.Session()
               }
           }
       }

Some things to know about Django with databases:
Having multiple databases in the same project worked

# Random Notes 

## Apache Cassandra / CQL

Can run the SQL in command line using `cqlsh` using: `$cqlsh`

* This is called the: Cassandra Query Language
* `pip install cql`
* http://www.datastax.com/2012/01/working-with-apache-cassandra-on-mac-os-x
* Run in terminal using: `$cqlsh`
* Visually inspect with DataStaX
* Looks into Cassandra with a GUI
* There’s two programs: DevCenter and OpsCenter
    - opscenter is under bin/opscenter (to run in background), bin/opscenter -f (to run in foreground)

## Apache Spark

Apache Spark is a cluster computing system; can be used to process live data streams (e.g. sources like Kafka) and push this data out (e.g. to files, databases, or dashboards)

* `brew install scala`, `brew install apache-spark`
* Start in terminal with: `$spark-shell` to use Scala, or `pyspark` to use Python

Added jar file under: /Users/williamliu/jars/spark-streaming-kafka-assembly_2.10-1.6.1.jar

*  Adding here messes things up so don’t add to here: /Library/Java/Extensions/
*  spark-streaming-kafka-assembly_2.10-1.6.1.jar
*  Sample usage: spark-submit --jars ~/jars/spark-streaming-kafka-assembly_*.jar /Users/williamliu/GitHub/python-examples/pyspark/spark_streaming_kafka_example.py 

## Apache Kafka

Apache Kafka is a high-throughput distributed messaging system (can handle lots of reads and writes from many clients).  Zookeeper is used to coordinate processes of distributed applications like group messaging, shared registers, distributed lock servers. This is done using an efficient replicated state machine to guarantee that updates to nodes are ordered.

* `brew install kafka`
* Start Zookeeper: `zkserver start` (Note: Need to start Zookeeper before Kafka starts)
* You can connect to Zookeeper with the client using: `zkCli`
     * Intro to ZooKeeper: http://zookeeper.apache.org/doc/trunk/zookeeperStarted.html
     * More About ZooKeeper: https://www.elastic.co/blog/found-zookeeper-king-of-coordination
* Go to ‘$/usr/local/bin'
* Start Kafka server: `$kafka-server-start /usr/local/etc/kafka/server.properties`
* Create a topic: `$kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test`
  # Created topic "test".
* List topics `$kafka-topics --list --zookeeper localhost:2181`

Kafka settings (e.g. for Producer and Consumer) are saved in: /usr/local/etc/kafka/ e.g. under consumer.properties and producer.properties

We have producers that send data to consumers
     * Create data from the standard input and put into a Kafka topic: `$kafka-console-producer --broker-list —localhost:9092 --topic test`   Nothing appears Yet; can start typing in some messages
     * Read data from a Kafka topic: `$kafka-console-consumer --zookeeper localhost:2181 --topic test --from-beginning`   Then consume
         
     * Kafka is a distributed publish-subscribe messaging system meant to scale
     * Kafka has feeds of messages in topics. Producers write data to topics. Consumers read from topics. Topics are partitioned 

## Apache Zookeeper

* in /usr/local/bin/  `zkServer start` to run the server
* ‘zkCli’ to run the Client
* Zookeeper config file is under /usr/local/etc/zookeeper

## AWS SNS
     
Amazon SNS (Simple Notification Server) to deliver push messages to applications and/or users

* Can be used with applications that generate or consume notifications
* Can be used for time-sensitive updates
* Can be used to relay events among distributed computer applications
* How it works:
     - Create a ‘Topic’ (i.e. an ‘Access Point’); a topic is a communication channel to send messages and subscribe to notifications
     - We can ‘Subscribe’ to the ‘Topic’.  To subscribe, we need a protocol (e.g. email, mobile app, web server), and an ‘endpoint’ (e.g. the email address) that can receive notification messages.
     - We can ‘Publish’ to the ‘Topic’.  To publish, we need a message (e.g. an email message) and AWS will try to send that message to every subscribed endpoint.
Examples include:
     - SMS Notifications using Amazon SNS

## Kafka Tool (kafkacat)

https://github.com/edenhill/kafkacat

## IP/Port

netcat (e.g. nc -z 127.0.0.1 8080)
telnet 127.0.0.1 8080
list any process listening to a specific port (e.g. 8080): $lsof -i:8080

## Scala

'Project Structure' to add new dependencies

Get Twitter Examples
https://dev.twitter.com/oauth/tools/signature-generator/6799311?nid=813

## CQL

    COPY table1 (column1, column2, column3) FROM ‘table1data.csv’ WITH HEADER=true;

    sed "s/$/,/g” myfilename.csv > newfilename.csv  // add comma to end of each line

brew switch cassandra 3.5

    datetime.datetime.now()
    2016-05-06 13:53:07.387654
    UUID: fdd0ba00-13b2-11e6-88a9-0002a5d5c51b

Imports/Exports data from CSV

    COPY table1 (column1, column2, column3) FROM ‘table1data.csv’ WITH HEADER=true;

## Notes about Cassandra Keys and Clustering

Don’t mess with the Primary Key
TRUNCATE <sometable>  to drop that table
Cannot nest Collection in a Collection
describe <sometable>   # to get info about the table

Be explicit to say this is what you’re partitioning off of, then clustering off of (order matters, we first order by added_year, then video_id). If we don’t have video_id, we’ll have duplicates
e.g. PRIMARY KEY ((tag), added_year, video_id)
) WITH CLUSTERING ORDER BY (added_year DESC);

Remember to use UPSERTS to update say a table with new column data

Counters
INTS have concurrency issues so we’ll need a ‘counter'
UPDATE moo_counts SET moo_count = moo_count + 8
* Must be the only non partitioned key columns in the table (can have multiple counters); primary key can be any data type

Source
Executes a file containing CQL statements
SOURCE ‘./myscript.cql’; 

