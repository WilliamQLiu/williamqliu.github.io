---
layout: post
title: Dynamo DB
---


# {{ page.title }}

AWS Meetup for AWS Dynamo DB.

## New in AWS

* AWS SAM Local - Test your serverless application
* AWS Glue for ETL - ETL

## Summary

Amazon DynamoDB is a fast, NoSQL database server that supports both document
and key-value store models.

## Differences from No-SQL vs Traditional RDMBS Entities

NoSQL advantages

* Very good at storing ad-hoc data
* Good at horizontal scalability
* Fast storage and retrieval
* DynamoDB is good for piping from server less architectures
* Good fit for OLTP and data streams

NoSQL disadvantages

* Not good at ad hoc queries of data between entities
* Entity consistency usually maintained in the application

RDBMS advantages

* Good at maintaining enforcing relationships between entities
* Data only stored once (no data integrity issues) if third normal form
  followed

RDBMS disadvantages

* Limits on field length
* Must structure data

## Key Components

* Tables - like regular tables
* Items - items in a table
* Indexes - index
* Read Capacity/Commit Units (RCU) - How quickly you can read data (and get billed). One 'strongly' consistent read per second OR two 'eventually' consistent reads per second for items up to 4kb
* Write Capacity/Commit Units (WCU) - How quickly you can write data (and get billed). One write per second for an item up to 1kb

### Tables and Items

In a table, you need the following:

* 'Partition/Hash Key' (not sortable, used for grouping data and getting single
  items)
* 'Sort/Range Key' (sorts your data physically on disk and allows for 'RANGE'
  functions in queries)
* All other fields are 'Attributes'

### Dungeons and Dragons Example

Table: DungeonsDragonsPlayers

* Primary Key: Player Name
* Sort Key: Race (Dwarf, Elf, Tiefling)
* Character Names and races are globally unique

Example:

    Parition Key | Sort Key | Attributes

### Data Type Storage

You can store the following:

* Scalar Types (number, string, binary, boolean, null)
* Document Types (e.g. JSON doc)
* Set Types

### Partitions

Allocation of storage for a table is based on the Partition key (and when
needed, the range key).

Max parition size is 10 gigs. When a partition hits max capcaity, it splits.

### Indexes

We have two kinds of indexes:

* Local Secondary Indexes (created when table is created, used for sorting your
  data)
* Global Secondary Indexes (can be created any time, can choose your partition
  key)

#### Local Index

* Maximum of 5 per table
* Uses same partition key as the table
* Should have a different sort key
* E.g. sort DungeonDragonsPlayers by Sort Key (character level, so you can get
  characters by race and level)

#### Global Index

* Maximum of 5 per table
* Can be created any time
* Have separate RCU and WCU
* Must have a Partition Key
* E.g. sort DungeonsDragonsPlayers by CharacterName

#### Pre-Filter with Indexes

If the record does not contain a value that the GSI uses as its partition key,
then the item will not be created in the GSI.

### Capcity Planning

How can I know how many partitions I am going to need?

* Partition RCU - 3000, WCU 1000 and 10 gigs of data
* Formula: MAX( (RCU/3000) + (WCU/1000), (Data in Gigs)/10)
* Example: Data = 8 gigs, RCU = 1000, WCU = 500, MAX((1000,3000) + (1000/1000),
8/10) = 2

FYI: If you are querying very small data, you can use BATCH operations to keep
cost down.

#### Careful increasing RCU/WCU

Four partitions with 1,250 read capacity units and 500 write capacity units
each.

Will then split to:

Eight partitions with 1,000 read capacity units and 250 write capacity units
each.

## Querying Data

* __Get__ - gets an indivdiual record based on partition and/or sort keys
* __Queries__ - get one or more records based on partition and/or sort keys
  (one RCU per item, assuming an item is less than 4kb)
* __Scans__ - scans the entire table - don't do this since it will look at
  every item in the table (very expensive)

Note: Filters do not help! Filters are evaluated after the data is already
returned.

### Careful choosing Partition Key

High Cardinality means that a large percentage of values has lots of unique
values. You want your keys to be unique and evenly distributed.

Say you picked City, State as the Partition Key. New York may have a lot more
access than a smaller city.

### Advice on data

* Have partition keys with high cardinality
* Keep the items small

### Backups and Restores

You can use Data Pipeline to look at DynamoDB, can backup to S3 and restore as
a lambda event (create, delete) to DynamoDB. Be careful, this is expensive
since it is a LOT of RCUs and WCUs.

### Dynamo Stream

You can use Dynamo Stream to populate an S3 bucket. Can use with S3 versioning
to see exactly what changed. You can also use DynamoDB Stream to sync to out of
region.

### Monitoring

Monitor the following with Cloud Watch:

* Throttled requests - Any request that exceeds the WCU/RCU for a partition
* Get/Put latency - How long are the get and puts taking on average
* Scan and Query return counts - Lots of scans are BAD
* Conditional Checks failed - may need to engage the app team (e.g. a failed
  PUT still uses a WCU)

