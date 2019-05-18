---
layout: post
title: Amazon Redshift
---


# {{ page.title }}

# System Overview

We have the following:

* Client Applications connect (with JDBC or ODBC) to a Leader Node
* The Leader Node then connects to a Data Warehouse Center

Amazon Redshift is based on PostgreSQL so most existing SQL client applications
will work with minimal changes. Communication is done through Amazon Redshift's
PostgreSQL JDBC and ODBC.

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

## SQL Commands Reference

https://docs.aws.amazon.com/redshift/latest/dg/c_SQL_commands.html

## SQL Functions Reference

https://docs.aws.amazon.com/redshift/latest/dg/c_SQL_functions.html
