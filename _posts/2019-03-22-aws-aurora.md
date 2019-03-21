---
layout: post
title: AWS Aurora
---


# {{ page.title }}

Amazon Aurora is a fully managed relational database that's compatible with MySQL and PostgreSQL.

Notes from: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html

## Clustering

Aurora can cluster so that it's one or more DB instances with a cluster volume that manages the
data for those DB instances. This 'Cluster Volume' is a virtual database storage volume that spans
multiple Availability Zones, with each AZ having a copy of the DB cluster data.

There are two types of DB instances that make up an Aurora Cluster:

* Primary DB instance - read and write operations, each db cluster has one primary db instance
* Aurora Replica - Connects to the same storage volume as the primary DB instance and supports only read operations

## Connection Management

Since Aurora is normally a cluster of DB instances (instead of a single instance), each connection is
handled by a specific DB instance. When you connect to an Aurora cluster, the host name and port that you
specify point to an intermediate handler called an __endpoint__. Think of the endpoint as an abstraction
from these connections, that way you don't have to write your own logic for load-balancing or rerouting
connections.

## Storage

Aurora data is stored in the __cluster volume__, letting us have a distributed and shared storage architecture.
This cluster volume is a single, virtual volume that uses solid state drives. A cluster volume consists of
copies of the data across multiple availability zones in a single AWS region.
Data is automatically replicated across Availability Zones so your data is more durable.

Storage maxes out at 64TB.

## DB instances

You can add a db instance quickly in Aurora because your data is independent from the DB instances in the cluster.
The new DB instance simply connects to the shared volume that already contains all of your data.
You can remove a DB instance from a cluster without removing any of the underlying data.
Only when you delete the entire cluster does Aurora remove the data.
You can't stop an individual Aurora DB instance (only delete).
You can't modify the configuration of a db instance (or the db cluster) while the cluster is stopped

## Cloning Databases in an Aurora DB Cluster

Database cloning with Aurora uses a `copy-on-write` protocol, meaning data is copied at the time that
data changes, either on the source databases or the clone databases.
You can make multiple clones from the same DB cluster.

### Copy-on-write Protocol for Cloning

After database cloning, there is no additional storage required at the point of clone creation.
However, as changes occur in the source database and clone database, only the changed pages are created.

## Backups

Aurora backs up your cluster volume automatically and retains restore data for the length of the
__backup retention period__ (1 to 35 days). You can create a snapshot if you want to retain data beyond
this point in time.

There's also __backtracking__, which is "rewinding" a DB cluster to a specific time without restoring data from a
backup.

## Restoring Data

You can recover your data by creating a new Aurora DB cluster from the backup data that Aurora retains.
You can see the earlier recoverable time under `Earliest restorable time` (`EarliesetRestorableTime`) 
and the latest restore time under `Latest restore time` (`LatestRestorableTime`).
You can see this under `aws rds describe-db-clusters --region myregion` and grep for the above fields.

