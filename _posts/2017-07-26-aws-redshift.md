---
layout: post
title: Amazon Redshift and Redshift Spectrum
---


# {{ page.title }}

# Summary

## Amazon Redshift

__Amazon Redshift__ is a fully managed petabyte-scaled data warehouse service.
An Amazonn Redshift data warehouse is a collection of computing resources called
__nodes__, that are organized into a group called a __cluster__. Each cluster
runs an Amazon Redshift engine and contains one or more databases. Redshift
manages all the work of provisioning capacity, monitoring, and backup of the cluster.

Redshift creates one database when you provision a cluster and you're able to add
additional databases from your initial database. Each db can create tables, load data,
and run queries. Note that Redshift has roots that are based off of Postgres.

## Amazon Redshift Spectrum

**Amazon Redshift Spectrum** allows you to run SQL queries against unstructured data
in AWS S3. You can use open data formats like CSV, TSV, Parquet, Sequence, and
RCFile. AWS Redshift is Amazon's data warehouse solution. Most databases store data in
**rows**, but Redshift is a **column** datastore. You're able to create Redshift
tables and query data using __Redshift Spectrum__.

# Redshift Data Warehouse System Architecture

In an Amazon Redshift data warehouse, we have the following architecture:

* Client Applications connect (with JDBC or ODBC) to a Leader Node
* The Leader Node then connects to a Data Warehouse Cluster (e.g. Compute Node 1 with many nodes,
  Compute Node 2 with many nodes)
* Redshift uses a distributed architecture (Leader Node and various Compute Nodes)
* The Leader Node is responsible for doing the query planning, handling the load, handling data
  agggregation from multiple nodes, planning the queries, and passing that query to compute nodes

Amazon Redshift is based on PostgreSQL so most existing SQL client applications
will work with minimal changes. Communication is done through Amazon Redshift's
PostgreSQL JDBC and ODBC.

## Types of Clusters/Nodes

A redshift __cluster__ consists of __nodes__. Different node types determine the CPU, RAM,
and storage capacity. Types of nodes include:

* __DC2__ - compute-intensive data warehouse with local SSD storage included. Recommended
  for datasets under 1TB (compressed) for the best performance at the lowest price.
  As your data grows, increase to RA3 nodes.
* __RA3__ - has managed storage so you can optimize your warehouse by scaling and paying
  for compute and manaaged storage independently. You pay for the number of nodes based on your
  performance requirements and only pay for the managed storage you use. Size based on daily
  amount of data processed.

## Leader Node

The Leader Node distributes SQL to the compute nodes when a query references user-created tables
or system tables (e.g. tables with an `STL` or `STV` prefix and system views with an `SVL` or
`SVV` prefix). Some queries are only run on the leader node, like referencing catalog tables (
tables with a `PG` prefix or if it does not reference any tables).

Some of the leader-node only functions include:

* `CURRENT_SCHEMA`
* `CURRENT_SCHEMAS`
* `HAS_DATABASE_PRIVILEGE`
* `HAS_SCHEMA_PRIVILEGE`
* `HAS_TABLE_PRIVILEGE`

So really, the leader node parses and develops execution plans to carry out database operations
(some series of steps) and distributes the compiled code to the compute nodes and assigns a portion
of the data to each compute node.

## Metadata with `PG_TABLE_DEF`

With Amazon Redshift, metadata (table definition information) is stored in the `PG_TABLE_DEF` table.
The `PG_` part is leftover from PostgreSQL.

    SELECT
      *
    FROM
      PG_TABLE_DEF;

    # Returns
    # schemaname, tablename, column, type, encoding, distkey, sortkey, notnull

You normally don't want to return everything.

    SELECT
      DISTINCT tablename
    FROM
      PG_TABLE_DEF
    WHERE
      schemaname = 'public';

To list schemas in Redshift:

    select s.nspname as table_schema,
           s.oid as schema_id,
           u.usename as owner
    from pg_catalog.pg_namespace s
    join pg_catalog.pg_user u on u.usesysid = s.nspowner
    order by table_schema;

    -- Example
    table_schema    schema_id   owner
    my_schema               1    will

    -- Returns us
    databases
      my_database1
        schemas
          my_schema1
          my_schema2
      my_database2
        schemas
          my_schema3

## Metadata from `PG_NAMESPACE`

    SELECT
      *
    FROM
      PG_NAMESPACE;

    # returns nspname, nspowner, nspacl

## Searchpath

Remember that you can `set search_path TO <myschema>`

## External Databases, Schemas, Tables

With Amazon Redshift Spectrum, you can query external data.

    SELECT * FROM SVV_EXTERNAL_DATABASES;
    # eskind, esoptions, databasename, location, parameters

    SELECT * FROM SVV_EXTERNAL_SCHEMAS;
    # esoid, eskind, schemaname, esowner, databasename, esoptions

    SELECT * FROM SVV_EXTERNAL_TABLES;
    # schemaname, tablename, location, input_format, output_format, serialization_lib, serde_parameters, compressed, parameters

You have to create an external table in an external schema. An external schema references a database in the
external data catalog and provides the IAM role ARN that authorizes your cluster to access S3.

### Create External Schema

To create an external schema, you can use Amazon Athena, AWS Glue Data Catalog or an Apache Hive metastore like
Amazon EMR. It might look like:

    CREATE EXTERNAL SCHEMA [IF NOT EXISTS] schema_name
    FROM { [DATA CATALOG] | HIVE METASTORE }
    DATABASE 'database_name'
    [ REGION 'aws-region' ]
    [ URI 'hive_metastore_uri' [ PORT port_number ] ]
    IAM_ROLE 'iam-role-arn-string'
    [ CATALOG ROLE 'catalog-role-arn-string' ]
    [ CREATE EXTERNAL DATABASE IF NOT EXISTS ]

### Create External Table

To create an external table, it might look like this:

Say we have S3 data in: s3://my-bucket-name/tickit/spectrum/sales

    CREATE EXTERNAL TABLE spectrum.sales(
      salesid integer,
      listid integer,
      sellerid integer,
      dateid smallint,
      pricepaid decimal(8,2),
      saletime timestamp)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE
    LOCATION 's3://my-bucket-name/tickit/sales/'
    TABLE PROPERTIES ('numRows'='172000');

Additional Details here: https://docs.aws.amazon.com/redshift/latest/dg/c-spectrum-external-tables.html

### View External Table Metadata

You can see what external tables are available by checking the system view here:

     SELECT * FROM SVV_EXTERNAL_TABLES;

Some important columns are:

* __schemaname__ - the name of the Redshift external schema for the external table
* __tablename__ - the name of the external table
* __location__ - the location (e.g. where in s3)
* __input format__ - e.g. org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat
* __output format__ - e.g. org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat

You can create this source table with AWS Glue Data Catalog so that you can use the data in Athena and Redshift.

You can format shift to Parquet with an AWS Glue Job (or do this outside of the CLI tools by reading from the S3
location, then writing to another location as parquet using some code).

### Query External Tables

You can query your data in S3 using Athena with a query like:

    SELECT COUNT(*) FROM spectrum.sales;

There's pseudocolumns `$path` and `$size` that lets you see the size of the data files for each row returned by
a query. You have to explicitly specify these columns; they won't return from a `SELECT *`.

    SELECT "$path", "$size"
    FROM myschema.mytable
    WHERE myid = 1;

## SQL Commands Reference

https://docs.aws.amazon.com/redshift/latest/dg/c_SQL_commands.html

## SQL Functions Reference

https://docs.aws.amazon.com/redshift/latest/dg/c_SQL_functions.html

## Redshift Utils

If you are running a lot of Admin functionality (e.g. want the DDL for tables, views, etc), then consider
looking at the scripts here: https://github.com/awslabs/amazon-redshift-utils/tree/master/src/AdminViews

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

## Workload Management (WLM)

__Workload Management__ (WLM) is used to define multiple query queues and to route queries to the appropriate
queues at runtime. The idea is that you might have many people running queries at the same time and some
queries might use more cluster resources for longer periods of time that might affect the performance of
other queries.

## Database Security

You manage database security by controlling which users have access to which database objects. You can grant
access to objects to either user accounts or groups.

* By default, privileges are granted only to the object owner
* Amazon Redshift database users are named user accounts that can connect to the database
  You either have to assign the privileges directly to the account or by being a member of the group that has privileges
* Schemas are collections of database tables and other database objects. Schemas are similar to file system directories,
  but cannot be nested. Users can be granted access to a single schema or to multiple schemas

### Users

To create and modify users, you can run:

    CREATE USER
    ALTER USER
    DROP USER

To get a list of current users, run:

    SELECT * FROM pg_user;

    #usename, usesysid, usecreatedb, usesuper, usecatupd, passwd, valuntil, useconfig
    #rdsdb          1

You can make a user a SUPERUSER by:

    CREATE USER adminuser myuser password 'mypassword';
    ALTER USER adminuser myuser;

### Groups

Groups are collections of users that can be collectively assigned privileges for easier security maintenance.

To create groups:

    CREATE GROUP mywebusers;

To list groups:

    SELECT * FROM pg_group;

    # groname, grosysid, grolist
    # my_group,     100,   {109}

### Schemas

A database has one or more named schemas. Each schema in a database contains tables and other kinds of named objects.
By default, a database a has single schema named `PUBLIC`. You can use schemas to group database objects under a
common name. Schema commands include:

    CREATE SCHEMA
    ALTER SCHEMA
    DROP SCHEMA

To list all your schemas, you can query `pg_namespace`:

    SELECT * FROM pg_namespace;
    # nspname, nspowner, nspacel

To list all of your tables that belong to a schema, query `pg_table_def`:

    SELECT DISTINCT(tablename) FROM pg_table_def
    WHERE schemaname = 'my_schema';

### Giving Usage

You can grant usage with:

    GRANT USAGE ON SCHEMA myschema TO GROUP mywebgroup;
    GRANT ALL ON SCHEMA myschema to GROUP mywebgroup;

## Redshift Performance

Some performance features include:

* Massive Parallel Processing
* Columnar Data Storage
* Query Optimizer
* Result Caching
* Compiled Code

Work Load Manager (WLM) - flexibly manage priorities with workloads so that short, fast-running queries
won't get stuck in queues behind long-running queries


