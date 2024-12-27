---
layout: post
title: Data Engineering
---


# {{ page.title }}

# Overview

1.) Dimensional Data Modeling
  * Know your consumer
  * OLTP vs OLAP data modeling
  * Cumulative Table design
  * Compactness vs Usability tradeoff
  * Temporal cardinality explosion
  * Run-length encoding compression gotchas

2.) Slowly Changing Dimensions and Idempotent Queries in Iceberg
  * Idempotent pipelines
  * Slowly-Changing Dimensions

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

## Cumulative Table Design

You want to track dimensions over time to hold onto history (not counting users that ask to be deleted)

* Core components
  - 2 dataframes (yesterday and today)
  - `FULL OUTER JOIN` the two data frames together
  - `COALESCE` values to keep everything around
  - Hang onto all of history
* Usages
  - Growth analytics at Facebook (`dim_all_users`)
  - State transition tracking

### Cumulative Table vs Snapshot Dimensions

Daily Snapshot (`name, is_active`)

```
2023-01-01, Will, False
2023-01-02, Will, True
2023-01-03, Will, True
2023-01-04, Will, True
2023-01-05, Will, False
```

Rolling Snapshots (`name, is_active_array`)

```
2023-01-01, Will, [False]
2023-01-02, Will, [True, False]
2023-01-03, Will, [True, True, False]
2023-01-04, Will, [True, True, True, False]
2023-01-05, Will, [False, True, True, False]
```

You normally don't want this array to go on forever, normally just limit (e.g. to 30 days, `is_monthly_active`)

### Cumulative Table Design

1. Yesterday + Today ->

2. FULL OUTER JOIN
   COALESCE ids and unchanging dimensions
   Compute cumulation metrics (e.g. days since x)
   Combine arrays and changing values

3. -> Cumulated Output

With the array, is able to check if `is_active` in last 90 days

Strengths:

* Historical analysis without shuffle (i.e. very efficient)
* Easy "transition" analysis

Drawbacks:

* Can only be backfilled sequentially (can't be many days at a time, e.g. 365 days all at once. However this depends on other day so needs sequential backfill)
* Handling PII data can be a mess since deleted/inactive users get carried forward

Can do "transition analysis" since all data is rammed into one row, e.g. definitions of active status:

* __Churn__ - Active yesterday and not active today
* __Resurrected__ - Not active yesterday and active today
* __New__ - Didn't exist yesterday and now active
* __Deleted__ - Active yesterday and deleted today

### Compactness vs Usability Tradeoff

* The most usable tables usually:
  - Have no complex data types
  - Easily can be manipulated with `WHERE` and `GROUP BY`
* The most compact tables (not human readable)
  - Are compressed to be as small as possible and can't be queried directly until they're decoded
* The middle-ground tables
  - Use complex data types (e.g. `ARRAY`, `MAP` and `STRUCT`), making querying tricker but also compacting more

When would you use each type of table?

* Most compact
  - Online systems where latency and data volumes matter a lot
  - Consumers are usually highly technical
* Middle-ground
  - Upstream staging / master data where the majority of consumers are other data engineers
* Most usable
  - When analytics is the main consumer and the majority of consumers are less technical

### Struct vs Array vs Map

* Struct
  - A table within a table (ROW data type in Trino)
  - Keys are rigidly defined, compression is good
  - Values can be any type
  - Can nest Struct of Struct, etc (but not usable)
* Array
  - Ordinal
  - List of values that all have to be the same type
* Map
  - Keys are loosely defined, compression is okay
  - Keys have to be a primitive (int, string)
  - Values all have to be the same type

### Temporal Cardinality Explosions of Dimensions

* When you add a temporal aspect to your dimensions and the cardinality increases by at least 1 order of magnitude
* Example:
  * Airbnb has ~6 million listings
    - If we want to know the nightly pricing and available of each night for the next year
      * 365 * 6 million or about ~2 billion nights
    - Should this dataset be:
      * Listing-level with an array of nights?
      * Listing night level with 2 billion rows?
    - If you do the sorting right, Parquet will keep these about the same size

### Badness of Denormalized Temporal Dimensions

If you explode it out and need to join other dimensions, Spark shuffle will ruin your compression!

### Run-length Encoding Compression

Probably the most important compression technique in big data right now
* It's why Parquet file format has become so successful
Shuffle can ruin this. BE CAREFUL!
* Shuffle happens in distributed environments when you do JOIN and GROUP BY

When a value is repeated, it nulls it out and says "this value is repeated 5 times". Now big values are nulled out
because you can compress it down in Parquet

After a join, Spark (or any distributed compute engine) may mix up the ordering of the rows and mix up the order.
In the end, the listing-level with an array of nights is more efficient (since downstream data engineers or users
can join that data). If your downstream consumers are producing datasets, the shuffling will cause the compression to change.

# 2. Slowly Changing Dimensions and Idempotent Queries in Iceberg

Create a Slowly Changing Dimension table that is __idempotent__(same result no matter how many times you execute).

## Idempotent Pipelines are critical

__Idempotent__ means your pipelines produce the same results regardless of when it's ran.

* Regardless of the day you run it
* Regardless of how many times you run it
* Regardless of the hour that you run it

When a pipeline is not idempotent, it's difficult to work with because:

* Silent failure
* You only see it when you get data inconsistencies and a data analyst yells at you

### What can make a Pipeline not Idempotent

* `INSERT INTO` without `TRUNCATE`
  - E.g. INSERT INTO a table twice now doubles the data
  - Use `MERGE` or `INSERT OVERWRITE` every time
* Using `start_date > ` without a corresponding `end_date <`
* Not using a full set of partition sensors
  - Pipeline might run when there is no/partial data
* Not using `depends_on_past` for cumulative pipelines

## Code

```
CREATE TABLE williamliu.nba_player_scd (
    player_name VARCHAR,
    is_active BOOLEAN,
    start_season INTEGER,
    end_season INTEGER,
    current_season INTEGER
)
WITH (
    format = 'PARQUET',
    partitioning = ARRAY ['current_season']
)
```

## LAG function

Load data that has a slowly changing dimension (track user activity changes using the `LAG` function)
`LAG` is used to implement Type 2 SCDs, which track historical changes by adding a new row for each change.
`LAG` lets you access data from the previous row within the same result set, based on a defined order.

```
LAG(column_name, offset, default_value) OVER (PARTITION BY partition_column ORDER BY order_column)
```

For example, you want to track when a Customer dimension changes its Address.
```
----- Original Table
CustomerID	      Name	Address	UpdateDate
1	John Doe	  123   Elm St	2024-01-01
1	John Doe	  456   Oak St	2024-06-01
2	Jane Smith	  789   Pine St	2024-03-01
-----

WITH Changes AS (
    SELECT
        CustomerID,
        Name,
        Address,
        UpdateDate,
        LAG(Address) OVER (PARTITION BY CustomerID ORDER BY UpdateDate) AS PreviousAddress
    FROM
        Customer
)
SELECT
    CustomerID,
    Name,
    Address,
    UpdateDate,
    CASE
        WHEN Address != PreviousAddress OR PreviousAddress IS NULL THEN 'New'
        ELSE 'No Change'
    END AS ChangeFlag
FROM
    Changes;

----- New Result
CustomerID	Name	Address	UpdateDate	ChangeFlag
1	John Doe	123 Elm St	2024-01-01	New
1	John Doe	456 Oak St	2024-06-01	New
2	Jane Smith	789 Pine St	2024-03-01	New
-----

# Note: With a Type 2 SCD, you want Start and End Dates

WITH Changes AS (
    SELECT
        CustomerID,
        Name,
        Address,
        UpdateDate,
        LAG(UpdateDate) OVER (PARTITION BY CustomerID ORDER BY UpdateDate) AS PreviousUpdateDate,
        LAG(Address) OVER (PARTITION BY CustomerID ORDER BY UpdateDate) AS PreviousAddress
    FROM
        Customer
)
SELECT
    CustomerID,
    Name,
    Address,
    COALESCE(PreviousUpdateDate, '1900-01-01') AS StartDate,
    UpdateDate AS EndDate
FROM
    Changes
WHERE
    Address != PreviousAddress OR PreviousAddress IS NULL;


----- Type 2 SCD with 1.) Start and End Date and 2.) current flag
WITH Changes AS (
    SELECT
        CustomerID,
        Name,
        Address,
        UpdateDate,
        LAG(Address) OVER (PARTITION BY CustomerID ORDER BY UpdateDate) AS PreviousAddress,
        ROW_NUMBER() OVER (PARTITION BY CustomerID ORDER BY UpdateDate DESC) AS RowNumber
    FROM
        Customer
)
SELECT
    CustomerID,
    Name,
    Address,
    UpdateDate AS StartDate,
    LEAD(UpdateDate) OVER (PARTITION BY CustomerID ORDER BY UpdateDate) AS EndDate,
    CASE WHEN RowNumber = 1 THEN 'Y' ELSE 'N' END AS CurrentFlag
FROM
    Changes;

----- With Current Flag
CustomerID	Name	Address	StartDate	EndDate	CurrentFlag
1	John Doe	123 Elm St	2024-01-01	2024-06-01	N
1	John Doe	456 Oak St	2024-06-01	NULL	Y
2	Jane Smith	789 Pine St	2024-03-01	NULL	Y

```

Example LAG function:

```
SELECT
  player_name,
  is_active,
  LAG(is_active, 1) OVER (PARTITION BY player_name ORDER BY current_season) AS is_active_last_season,
  current_season
FROM williamliu.nba_players

-- PLAYER_NAME  IS_ACTIVE   IS_ACTIVE_LAST_SEASON   CURRENT_SEASON
-- Antoine Carr    true    null    1996
-- Antoine Carr    true    true    1997
-- Antonio McDyess true    null    1996
```

```
WITH lagged AS (
SELECT
  player_name,
  CASE WHEN is_active THEN 1 ELSE 0 END AS is_active,
  CASE WHEN LAG(is_active, 1) OVER (PARTITION BY player_name ORDER BY current_season) THEN 1 ELSE 0 END AS is_active_last_season,
  current_season
FROM williamliu.nba_players
)

SELECT
  *,
  CASE WHEN is_active <> is_active_last_season THEN 1 ELSE 0 END AS did_change
FROM lagged

----
--PLAYER_NAME IS_ACTIVE   IS_ACTIVE_LAST_SEASON   CURRENT_SEASON  DID_CHANGE
--Ben Wallace 1   0   1996    1
--Ben Wallace 1   1   1997    0
--Boban Marjanovic    1   0   2020    1
```


Rolling Sum
```
WITH lagged AS (
  SELECT
    player_name,
    CASE WHEN is_active THEN 1 ELSE 0 END AS is_active,
    CASE WHEN LAG(is_active, 1) OVER (
      PARTITION BY player_name ORDER BY current_season)
    THEN 1 ELSE 0 END AS is_active_last_season,
    current_season
  FROM williamliu.nba_players
),
streaked AS (
  SELECT
    *,
    SUM(
      CASE WHEN is_active <> is_active_last_season THEN 1 ELSE 0 END
    ) OVER (
      PARTITION BY
        player_name
      ORDER BY
        current_season
    ) AS streak_identifier
  FROM lagged
)
SELECT
  player_name,
  streak_identifier,
  MAX(is_active) AS is_active,
  MIN(current_season) AS start_season,
  MAX(current_season) AS end_season
FROM streaked
GROUP BY player_name, streak_identifier
```


Notes:
* Want to use INSERT OVERWRITE for file based systems (e.g. Spark, Hive) instead of INSERT INTO
* Ideally you use MERGE instead of INSERT INTO for database systems (e.g. Snowflake, Redshift)
* Want to look at columns that can change for Type 2 SCDs
* Create Temporary Tables to load new and existing data into a staging table
