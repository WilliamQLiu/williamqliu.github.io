---
layout: post
title: Data Engineering
---


# {{ page.title }}

# Overview

1. Dimensional Data Modeling
  * Know your consumer
  * OLTP vs OLAP data modeling
  * Cumulative Table design
  * Compactness vs Usability tradeoff
  * Temporal cardinality explosion
  * Run-length encoding compression gotchas

# 1 - Dimensional Data Modeling

Dimensions are attributes of an entity (e.g. user's birthday, user's favorite food)

* Some of these dimensions may IDENTIFY an entity (e.g. a user's ID)
* Others are just attributes

Dimensions are either:

* Slowly-changing
* Fixed (e.g. birthday)

## Knowing your consumer

There's different types of consumers. Meet people where they are (not everyone wants to learn).

* Data analysts / Data scientists
  - Should be very easy to query, not many complex data types
* Other data engineers (upstream of other data engineers)
  - Should be compact and probably harder to query
  - Nested types are okay (e.g. assume they can access a struct, explode an array, know to unnest, etc)
* ML Models
  - Depends on the model and how its trained
* Customers
  - Should be a very easy to interpret chart (most distilled data)

## OLTP vs Master data vs OLAP

There's three ways you can model your data:

* __OLTP__ (online transaction processing) - optimizes for low-latency, low-volume queries ()
  - Examples: MySQL, PostgreSQL
  - Single record access is fast
* __OLAP__ (online analytical processing) - optimizes for large volume, GROUP BY queries, minimizes JOINs
  - Example: dimensional data modeling
  - Getting single record access is a lot slower
* __Master Data__ - optimizes for completeness of entity definitions, deduped
  - In the middle between OLTP and OLAP

Notes:

* Mismatching Needs = Less Business Value
* Some of the biggest problems in data engineering occur when data is modeled for the wrong consumer!
* OLTP and OLAP is a Continuum
  - Production Database Snapshots
    * Once a day, takes picture at 6pm PT (1 hour after UTC), then places into Data Lake
    * Why can't we just use the snapshot and build off of that? We have all of the raw data, why can't we just query on snapshots?
      We get a lot of inconsistencies, we compute the metrics differently (e.g. 6 data scientists making their own pipelines and computing metrics slightly differently)
      Never have data scientists or analysts query the Master Data
  - Master Data
    * Dedupe and conform data
    * Layer where Truth and Trust is
  - OLAP Cubes
    * Lots of GROUP BYs, aggregates
  - Metrics
    * Feed into Metrics that are usually a single number for a day


