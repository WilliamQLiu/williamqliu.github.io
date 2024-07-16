---
layout: post
title: PostgreSQL
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [PSQL Basic Commands](#basiccommands)
    -  [Login](#login)
    -  [List Databases](#listdb)
    -  [Create Database](#createdb)
    -  [Access Database](#accessdb)
    -  [List Databases (inside psql)](#listdbinside)
    -  [See Roles)](#seeroles)
    -  [Change Database Password](#changepw)
    -  [View Current Connection Info](#conninfo)
    -  [View Database Tables](#dt)
    -  [View Tables, Views, and Sequences](#viewall)
    -  [Switch Databases](#switchdb)
    -  [Expanded Display](#expandeddisplay)
    -  [Display Triggers](#displaytriggers)
    -  [Display Functions](#displayfunctions)
    -  [Queries](#queries)
    -  [Exit](#exit)
    -  [SQL Help](#sqlhelp)
    -  [PSQL Help](#psqlhelp)
*  [SQL Basic Examples](#sqlbasics)
    -  [Example Schema](#exampleschema)
    -  [Basic Selects](#basicselects)
    -  [Select All](#selectall)
    -  [Select specific Columns](#selectcolumns)
    -  [Select specific Rows](#selectrows)
    -  [Select by filtering text](#selectfiltertext)
    -  [Select in list](#selectfilterin)
    -  [Select into categories](#selectcategories)
    -  [Select by dates](#selectdates)
    -  [Select CONVERT_TIMEZONE](#selectconverttimezone)
    -  [Select COALESCE function](#selectcoalesce)
    -  [Select with Order, Limit, Distinct](#selectproperties)
    -  [Select combine queries with Union](#selectunion)
    -  [Select with Max and Min](#selectmaxmin)
    -  [Select with a basic Subquery](#selectsubquery)
*  [SQL Joins and Subqueries](#joinsubquery)
    -  [From](#from)
    -  [Inner Join](#innerjoin)
*  [Views](#views)
    -  [Materialized Views](#materializedviews)
*  [Functions](#functions)
    -  [JSON Functions](#jsonfunctions)
    -[SQL Window Functions](#windowfunctions)
*  [Tips](#tips)
    -  [Explain](#explain)
    -  [Monitoring](#monitoring)
    -  [Optimization - Sargable Query vs Nonsargable Query](#sargable)
*  [Resources](#resources)


## <a id="summary">Summary</a>

__PostgreSQL__ is an open source relational database.

## <a id="basiccommands">Basic Commands</a>

#### <a id="login">Login</a>

In the command line, just enter `psql` to see your options

    $psql --help

#### <a id="listdb">List Databases</a>

In the command line, to see what databases are available for access, do:

    $psql --list

#### <a id="createdb">Create Database</a>

Create a database called `mydb`

    createdb mydb

#### <a id="accessdb">Access Database</a>

Login to psql

    psql -U myusername

Connect to a database (e.g. db is named mydb)

    psql mydb [optional user name]

#### <a id="listdbinside">Lists all Databases</a>

Now that you're in psql, you can also list all databases

    # \list
    or
    # \l

                                 List of databases
       Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
    -----------+----------+----------+------------+------------+-----------------------
     airflow   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
     postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
     template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
     template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres

#### <a id="showtablesizes">Show Table Size</a>

You can run a query like this to see disk usage:

    SELECT nspname || '.' || relname AS "relation",
        pg_size_pretty(pg_total_relation_size(C.oid)) AS "total_size"
      FROM pg_class C
      LEFT JOIN pg_namespace N ON (N.oid = C.relnamespace)
      WHERE nspname NOT IN ('pg_catalog', 'information_schema')
        AND C.relkind <> 'i'
        AND nspname !~ '^pg_toast'
      ORDER BY pg_total_relation_size(C.oid) DESC
      LIMIT 5;
    Example output:

    COPY
           relation       | total_size
    ----------------------+------------
     public.snapshots     | 823 MB
     public.invoice_items | 344 MB
     public.messages      | 267 MB
     public.topics        | 40 MB
     public.invoices      | 35 MB
    (5 rows)

#### <a id="seeroles">See Roles</a>

Prints out a list of roles and attributes (e.g. name, superuser, member of)

     \du

#### <a id="changepw">Change Database Password</a>

Change the password of a database

    \password mydb

#### <a id="conninfo">View current connection info</a>

View current connection information

    \conninfo

#### <a id="dt">View Database Tables</a>

View database tables and their owners

    \dt

#### <a id="viewall">View Tables, Views, and Sequences</a>

View all of the tables, views, and sequences

    \z

#### <a id="switchdb">Switch Databases</a>

To connect to another database (e.g. db 'jobwaffle_prod')

    \connect jobwaffle_prod
    or
    \c jobwaffle_prod

#### <a id="expandedoutput">Expanded Output</a>

To use expanded output, do `\x`

#### <a id="displaytriggers">Display Triggers</a>

To display triggers, do `\dy+`

#### <a id="displayfunctions">Display Functions</a>

To display functions, do `\df+`

#### <a id="queries">Queries</a>

While in the interactive session, remember to terminate queries with a __;__

    mydb=# SELECT * FROM mytable;

#### <a id="exit">Exit</a>

Exits out of the PostgreSQL command prompt

    \q

#### <a id="sqlhelp">SQL Help</a>

To get help for SQL Commands

    \h

#### <a id="psqlhelp">PSQL Help</a>

To get help for psql commands

    \?

## <a id="sqlbasics">SQL Examples</a>

Here's some examples of SQL queries taken from [pgexercises](https://pgexercises.com).

### <a id="exampleschema">Example Schema</a>

Given the following tables:

    cd.members
    memid               | integer
        surname             | character varying(200)
        firstname           | character varying(200)
        address             | character varying(300)
        zipcode             | integer
        telephone           | character varying(20)
        recommendedby       | integer
        joindate            | timestamp

    cd.bookings
        facid               | integer
        memid               | integer
        starttime           | timestamp
        slots               | integer

    cd.facilities
        facid               | integer
        name                | character varying(100)
        membercost          | numeric
        guestcost           | numeric
        initialoutlay       | numeric
        monthlymaintenance  | numeric

### <a id="basicselects">Basic Selects</a>

#### <a id="selectall">Select All</a>

Retrieve everything from a table (all columns, all rows):

    SELECT *
    FROM cd.facilities;

    facid	name	        membercost	guestcost	initialoutlay	monthlymaintenance
    0	    Tennis Court 1	5	        25	        10000	        200
    1	    Tennis Court 2	5	        25	        8000	        200
    2	    Badminton Court	0	        15.5        4000            50
    3	    Table Tennis	0	        5           320             10
    4	    Massage Room 1	35	        80	        4000	        3000
    5	    Massage Room 2	35	        80	        4000	        3000
    6	    Squash Court	3.5	        17.5        5000            80
    7	    Snooker Table	0	        5	        450	            15
    8	    Pool Table      0           5           400             15

#### <a id="selectcolumns">Select Specific Columns</a>

To select specific **columns** from a table, we use **SELECT**.

    SELECT name, membercost
    FROM cd.facilities;

    name	            membercost
    Tennis Court 1	    5
    Tennis Court 2	    5
    Badminton Court	    0
    Table Tennis	    0
    Massage Room 1	    35
    Massage Room 2	    35
    Squash Court	    3.5
    Snooker Table	    0
    Pool Table	        0

#### <a id="selectrows">Select Specific Rows</a>

To select specific **rows** from a table, we use SQL's **WHERE**:

    SELECT facid, name, membercost, monthlymaintenance
    FROM cd.facilities
    WHERE membercost > 0 AND (membercost < monthlymaintenance/50.0);

    facid	name	        membercost	monthlymaintenance
    4	    Massage Room 1	35	        3000
    5	    Massage Room 2	35	        3000

#### <a id="selectlike">LIKE </a>

You can filter strings in tables (e.g. list all facilities with the word
'Tennis' in the name).

    SELECT *
    FROM cd.facilities
    WHERE name LIKE '%Tennis%';

    acid	name	            membercost	guestcost	initialoutlay	monthlymaintenance
    0	    Tennis Court 1	    5	        25	        10000	        200
    1	    Tennis Court 2	    5	        25	        8000	        200
    3	    Table Tennis	    0	        5	        320             10

#### <a id="selectsimilar">SIMILAR TO</a>

`SIMILAR` is similar to `LIKE`, except you can run a regular expression.

    'abc' SIMILAR TO 'abc'      # true
    'abc' SIMILAR TO 'a'        # false
    'abc' SIMILAR TO '%(b|d)%'  # true

https://www.postgresql.org/docs/8.3/functions-matching.html

We have:

    `|` to denote alternation (either of two alternatives)
    `*` to denote repetition of the previous item zero or more times
    `+` to denote repetition of the previous item one or more times
    `()` to group items into a single logical item
    `[]` to specify a character class, like in a POSIX regular expression

#### <a id="selectfilterin">Filter In List</a>

You can filter to see if an item is in a list (e.g. facilities with ID 1, 3, 5)

    SELECT *
    FROM cd.facilities
    WHERE facid IN (1, 3, 5);

If you return a result that is a **single column**, you can feed those results into
an **IN** operator. This is called a **subquery**. For example:

    SELECT *
    FROM cd.facilities
    WHERE facid IN (
        SELECT facid FROM cd.facilities
    );

#### <a id="selectcategories">Select into Categories</a>

If you want to select results into categories (e.g. facilities labeled 'cheap'
or 'expensive' depending on monthly maintenacne costs > $100). You want to then
use **SQL CASE** (the equivalent of an if/switch statement)

    SELECT name,
           CASE WHEN monthlymaintenance > 100 THEN 'expensive'
                ELSE 'cheap'
           END AS cost
    FROM cd.facilities

#### <a id="selectdates">Select Dates</a>

To select specific dates:

    SELECT memid, surname, firstname, joindate
    FROM cd.members
    WHERE joindate > '2012-09-01';

    memid	surname	            firstname	joindate
    24	    Sarwin	            Ramnaresh	2012-09-01 08:44:42
    26	    Jones	            Douglas	    2012-09-02 18:43:05
    27	    Rumney	            Henrietta	2012-09-05 08:42:35
    28	    Farrell	            David	    2012-09-15 08:22:05
    29	    Worthington-Smyth	Henry	    2012-09-17 12:27:15
    30	    Purview	            Millicent	2012-09-18 19:04:01
    33	    Tupperware	        Hyacinth	2012-09-18 19:32:05
    35	    Hunt	            John	    2012-09-19 11:32:45
    36	    Crumpet	            Erica	    2012-09-22 08:36:38
    37	    Smith	            Darren	    2012-09-26 18:08:45

#### <a id="selectconverttimezone">Select CONVERT_TIMEZONE</a>

`CONVERT_TIMEZONE` converts a timestamp from one time zone to another.

Format:

    CONVERT_TIMEZONE ( ['source_timezone',] 'target_timezone', 'timestamp')

Example 1:

    select listtime, convert_timezone('US/Pacific', listtime) from listing where listid = 16;

     listtime           |   convert_timezone
    --------------------+---------------------
    2008-08-24 09:36:12 | 2008-08-24 02:36:12

Example 2:

    select convert_timezone('EST', 'PST', '20080305 12:25:29');

     convert_timezone
    -------------------
    2008-03-05 09:25:29

#### <a id="selectcoalesce">Select COALESCE function</a>

`COALESCE` returns the first non-null argument with `COALESCE(arg1, arg2, ...)` with an unlimited number of args
and returns the first non-null (evaluating left to right). If all arguments are null, then it will return null.

    SELECT COALESCE(1, 2);  # 1
    SELECT COALESCE(NULL, 2, 1)  # 2

Often this is used as a default value. For example, say you have a blog post excerpt. If there is no excerpt provided,
then use the first 150 characters from the content post.

    SELECT COALESCE(excerpt, LEFT(CONTENT, 150)) FROM blog_posts;

#### <a id="selectproperties">Select Order, Limit, and Distinct</a>

Produce an ordered list of the first 10 surnames with no duplicates.
We make use of **ORDER**, **DISTINCT**, **LIMIT**.

    SELECT DISTINCT(surname)
    FROM cd.members
    ORDER BY surname
    LIMIT 10;

    surname
    Bader
    Baker
    Boothe
    Butters
    Coplin
    Crumpet
    Dare
    Farrell
    GUEST
    Genting

#### <a id="selectunion">Combine results from multiple queries with Union</a>

Create a combined list of all surnames and all facility names into a single
column (for whatever reason). Use **UNION**.

    SELECT surname
    FROM cd.members
    UNION
    SELECT name
    FROM cd.facilities

    surname
    Tennis Court 2
    Worthington-Smyth
    Badminton Court
    Pinker
    Dare
    Bader
    Mackenzie
    Crumpet
    Massage Room 1
    Squash Court
    Tracy
    Hunt
    Tupperware
    Smith
    Butters
    Rownam
    Baker
    Genting
    Purview
    Coplin
    Massage Room 2
    Joplette
    Stibbons
    Rumney
    Pool Table
    Sarwin
    Boothe
    Farrell
    Tennis Court 1
    Snooker Table
    Owen
    Table Tennis
    GUEST
    Jones

#### <a id="selectmaxmin">Select Max and Min</a>

Select the largest value of an expression.

    SELECT MAX(joindate) AS latest
    FROM cd.members;

    latest
    2012-09-26 18:08:45

Select the smallest value of an expression.

    SELECT MIN(joindate) AS earliest
    FROM cd.members;

    earliest
    2012-07-01 00:00:00

#### <a id="selectsubquery">Select with Subquery</a>

Get the first and last name of the last member(s) who signed up.

    SELECT firstname, surname, joindate
    FROM cd.members
    WHERE joindate = (
        SELECT MAX(joindate) FROM cd.members
    );

Since our subquery returns a table with a single column and single row,
we can substitute this value in our WHERE clause.

Another method that isn't quite as efficient and doesn't handle the case where
two people joined at the exact same time.

    SELECT firstname, surname, joindate
    FROM cd.members
    ORDER BY joindate desc
    LIMIT 1;

### <a id="joinsubquery">Joins and Subqueries</a>

Relational databases are relational because of joins.

#### <a id="from">From</a>

Remember that the output of a **FROM** is another table. That's why you can do this:

    SELECT _bookings.starttime
    FROM cd.bookings _bookings,
         cd.members _members
    WHERE
         _members.firstname = 'David'
         AND _members.surname = 'Farrell'
         AND _members.memid = _bookings.memid;

#### <a id="innerjoin">Inner Join</a>

Say we want a list of the start times for bookings by members named 'David
Farrell'.

Use an **INNER JOIN** to combine two tables with matching values on both
tables. You can do this a couple different styles:

    # Style 1
    SELECT _bookings.starttime
    FROM cd.bookings _bookings
    INNER JOIN cd.members _members
               ON _members.memid = _bookings.memid
    WHERE _members.firstname = 'David'
          AND _members.surname = 'Farrell';

    # Style 2
    SELECT _bookings.starttime
    FROM cd.bookings _bookings,
         cd.members _members
    WHERE
         _members.firstname = 'David'
         AND _members.surname = 'Farrell'
         AND _members.memid = _bookings.memid;

    starttime
    2012-09-18 09:00:00
    2012-09-18 17:30:00
    2012-09-18 13:30:00
    2012-09-18 20:00:00
    2012-09-19 09:30:00
    2012-09-19 15:00:00
    2012-09-19 12:00:00
    2012-09-20 15:30:00
    2012-09-20 11:30:00
    2012-09-20 14:00:00
    2012-09-21 10:30:00
    2012-09-21 14:00:00
    2012-09-22 08:30:00
    2012-09-22 17:00:00
    2012-09-23 08:30:00
    2012-09-23 17:30:00
    2012-09-23 19:00:00
    2012-09-24 08:00:00
    2012-09-24 16:30:00
    2012-09-24 12:30:00
    2012-09-25 15:30:00
    2012-09-25 17:00:00
    2012-09-26 13:00:00
    2012-09-26 17:00:00
    2012-09-27 08:00:00
    2012-09-28 11:30:00
    2012-09-28 09:30:00
    2012-09-28 13:00:00
    2012-09-29 16:00:00
    2012-09-29 10:30:00
    2012-09-29 13:30:00
    2012-09-29 14:30:00
    2012-09-29 17:30:00
    2012-09-30 14:30:00

Another example is to produce a list of the start times for bookings for tennis
courts for the date '2012-09-21' and return the start time and facility name
pairings, ordered by the time.

    SELECT books.starttime AS start,
       facil.name AS name
    FROM cd.bookings books
    INNER JOIN cd.facilities facil
        ON books.facid = facil.facid
    WHERE
        DATE(books.starttime) = '2012-09-21'
        AND facil.name LIKE '%Tennis Court%'
    ORDER BY
        books.starttime;

    start	            name
    2012-09-21 08:00:00	Tennis Court 1
    2012-09-21 08:00:00	Tennis Court 2
    2012-09-21 09:30:00	Tennis Court 1
    2012-09-21 10:00:00	Tennis Court 2
    2012-09-21 11:30:00	Tennis Court 2
    2012-09-21 12:00:00	Tennis Court 1
    2012-09-21 13:30:00	Tennis Court 1
    2012-09-21 14:00:00	Tennis Court 2
    2012-09-21 15:30:00	Tennis Court 1
    2012-09-21 16:00:00	Tennis Court 2

##<a id="views">Views</a>

###<a id="materializedviews">Materialized Views</a>

__Materialized Views__ in Postgres are like views, but they persist the results in a table-like form.

    CREATE MATERIALIZED VIEW myview AS SELECT * FROM mytable;

The main difference between a materialized view and a created table is that the materialized view
cannot be directly updated after it is created. The query used to create the materialized view is stored exactly
the same way that a view's query is stored. The only way to get fresh data is with:

    REFRESH MATERIALIZED VIEW myview;

So why use a materialized view?

* Accessing the data stored in a materialized view is often faster than accessing the underlying tables directly or through a view
* However, the data is not always current (you make that tradeoff)

## <a id="Aggregates">Aggregates</a>

You can select aggregates using `COUNT`, `SUM`, and `AVG`, `MAX`, `MIN`. `DISTINCT` can be used with aggregates.

# Notes

https://cloud.google.com/bigquery/docs/reference/standard-sql/data-types

#### <a id="arrays">Arrays</a>

ARRAY    Ordered list of zero or more elements of any non-ARRAY type.
An ARRAY is an ordered list of zero or more elements of non-ARRAY values. ARRAYs of ARRAYs are not allowed. Queries that would produce an ARRAY of ARRAYs will return an error. Instead a STRUCT must be inserted between the ARRAYs using the SELECT AS STRUCT construct.

Currently, BigQuery has two following limitations with respect to NULLs and ARRAYs:
BigQuery raises an error if query result has ARRAYs which contain NULL elements, although such ARRAYs can be used inside the query.
BigQuery translates NULL ARRAY into empty ARRAY in the query result, although inside the query NULL and empty ARRAYs are two distinct values.

Declaring an ARRAY type
ARRAY types are declared using the angle brackets (< and >). The type of the elements of an ARRAY can be arbitrarily complex with the exception that an ARRAY cannot directly contain another ARRAY.

Format
ARRAY<T>

#### <a id="structs">Structs</a>

STRUCT    Container of ordered fields each with a type (required) and field name (optional).
Declaring a STRUCT type
STRUCT types are declared using the angle brackets (< and >). The type of the elements of a STRUCT can be arbitrarily complex.

Format
STRUCT<T>

## <a id="jsonfunctions">JSON Functions</a>

https://www.postgresql.org/docs/9.3/functions-json.html

### `json_extract_path_text`

Format:
    json_extract_path_text(from_json json, VARIADIC path_elems text[])

Example:

    json_extract_path_text('{"f2":{"f3":1},"f4":{"f5":99,"f6":"foo"}}','f4', 'f6')  # returns 'foo'

## <a id="windowfunctions">Window Functions</a>

The standard window function syntax looks like:

    function (expression) OVER (
    [ PARTITION BY expr_list ]
    [ ORDER BY order_list [ frame_clause ] ] )

Let's break this apart:

A `function` is any function like `AVG`, `COUNT`, `MIN`, `MEDIAN`, `MAX`, `NTH_VALUE`, `FIRST_VALUE`, `LAST_VALUE`,
`ROW_NUMBER`, ``SUM`, etc. For Redshift, see list here: https://docs.aws.amazon.com/redshift/latest/dg/c_Window_functions.html

`OVER` defines the window specification. This is required and tells us that we're looking at a window function
instead of a normal function.

`PARTITION BY <expr_list>` is optional and means that we want to subdivide the result set into partitions, kinda
like a `GROUP BY` clause. If a partition clause is present, the function is calculated for the rows in each
partition. If no partition clause is specified, a single partition contains the entire table and the function
is computed for the entire table.

An `expr_list` is further broken down into

    expression | column_name [, expr_list ]

`ORDER BY <order_list>` is optional and means that the window function is applied to the rows within each partition
sorted according to the order specification in `ORDER BY`.

An `order_list` is further broken down into

    expression | column_name [ ASC | DESC ]
    [ NULLS FIRST | NULLS LAST ]
    [, order_list ]

A `frame_clause` is used in aggregate functions to further refine the set of rows in a function's window when using
`ORDER_BY`. It provides the ability to include or exclude sets of rows within the ordered result. The frame clause
does not apply to ranking functions and is not required when no `ORDER BY` clause is used in the `OVER` clause
for an aggregate function. If an `ORDER BY` clause is used for an aggregate function, an explicit frame clause is required.


`ROWS` - the rows clause defines the window frame by specifying a physical offset from the current row.
This clause specifies the rows in the current window or partition that the value in the current row is to be combined
with. It uses arguments to specify the row position, which can be before or after the current row. The reference
point for all window frames is the current row. Each row becomes the current row in turn as the window frame
slides forward in the partition. We have two variations:

1. The frame is a simple set of rows up to and including the current row

    { UNBOUNDED PRECEDING | unsigned_value PRECEDING | CURRENT ROW } |

2. The frame can be a set of rows between two boundaries

    {BETWEEN
    { UNBOUNDED PRECEDING | unsigned_value { PRECEDING | FOLLOWING } |
    CURRENT ROW}
    AND
    { UNBOUNDED FOLLOWING | unsigned_value { PRECEDING | FOLLOWING } |
    CURRENT ROW }}

`UNBOUNDED PRECEDING` indicates that the window starts at the first row of the partition.
`offset PRECEDING` indicates that the window starts a number of rows equivalent to the value of offset before
the current row
`UNBOUNDED PRECEDING` is the default.

`CURRENT ROW`

What does that mean exactly?

* Window functions perform an operation across a set of rows that are somehow related to the current row
* Similar to `GROUP BY` aggregate functions, but all rows remain in the output

Use cases:

* Fetch values from preceding or following rows (e.g. fetching the previous row's value)
* Assign ordinal ranks (1st, 2nd, etc) to rows based on their values' positions in a sorted list
* Running totals, moving averages

#### `ROW_NUMBER`

Example Table `Summer_Medals`

```
Year, Medals, Medals_RT
2004, 116, 116
2008, 125, 241
2012, 147, 388
```

Fetch Previous Row's Calculations (e.g. previous rows values, running totals):

```
Year, Champion, Last_Champion, Reigning_Champion
1996, GER, null, false
2000, LTU, GER, false
2004, LTU, LTU, true
2008, EST, LTU, false
2012, GER, EST, false
```

Example `ROW_NUMBER()`:

```
SELECT
  Year, Event, Country,
  ROW_NUMBER() OVER () AS Row_N
FROM Summer_Medals
WHERE
  Medal = 'Gold';
```

Example 'Numbering Rows'

```
SELECT
 *,
 ROW_NUMBER() OVER () AS Row_N
FROM Summer_Medals
ORDER BY Row_N ASC;
```

Example Ordering by Year in descending order (i.e. adding `ORDER BY` within `OVER`)

```
SELECT
  Year, Event, Country,
  ROW_NUMBER() OVER (ORDER BY Year DESC) AS Row_N
FROM Summer_Medals
WHERE
  Medal = 'Gold';
```

### Four Functions (`LAG`, `LEAD`, `FIRST_VALUE`, `LAST_VALUE`)

There are four functions:

__Relative__

* `LAG(column, n)` returns a `column`'s value at the row `n` rows before the current row
* `LEAD(column, n)` returns a `column`'s value at the row `n` rows after the current row

__Absolute__

* `FIRST_VALUE(column)` returns the first value in the table or partition
* `LAST_VALUE(column)` returns the last value in the table or partition

#### LAG()

`LAG` is a window function that takes a column and a number n and returns the column's value n rows before the current row.
E.g. passing in `1` as `n` returns the previous row's value

Syntax: `LAG(column, n) OVER ()`

Example Current and Last Champions

```
WITH Discus_Gold AS(
  SELECT
    Year, Country AS Champion
  FROM Summer_Medals
  WHERE
    Year IN (1996, 2000, 2004, 2008, 2012)
    AND Gender = 'Men' AND Medal = 'Gold'
    AND Event = 'Discus Throw')

SELECT
  Year, Champion,
  LAG(Champion, 1) OVER
    (ORDER BY Year ASC) AS Last_Champion
FROM Discus_Gold
ORDER BY Year ASC;
```

Results in:

```
Year, Champion, Last_Champion

```

#### `FIRST_VALUE` and `LAST_VALUE`



```
SELECT
  Year, City,
  FIRST_VALUE(City) OVER
    (ORDER BY Year ASC) AS First_City,
  LAST_VALUE(City) OVER (
    ORDER BY Year ASC
    RANGE BETWEEN
      UNBOUNDED PRECEDING AND
      UNBOUNDED FOLLOWING
    ) AS Last_City
  FROM Hosts
  ORDER BY Year ASC;
```


#### PARTITION BY

`PARTITION BY` splits the table into partitions based on a column's unique values

* Similar to `GROUP BY`, but the results aren't rolled into one column
* Operated on separately by the window function
  - `ROW_NUMBER` will reset for each partition
  - `LAG` will only fetch a row's previous value if its previous row is in the same partition

Example Adding partition by one column

```
WITH Discus_Gold AS (...)

SELECT
  Year, Event, Champion,
  LAG(Champion) OVER
    (PARTITION BY Event
     ORDER BY Event ASC, Year ASC) AS Last_Champion
FROM Discus_Gold
ORDER BY Event ASC, YEAR ASC;
```

Example Partitioning by multiple columns

```
WITH Country_Gold AS (
  SELECT
    DISTINCT Year, Country, Event
  FROM Summer_Medals
  WHERE
    Year IN (2008, 2012)
    AND Country IN ('CHN', 'JPN')
    AND Gender = 'Women' AND Medal = 'Gold')

SELECT
  Year, Country, Event
  ROW_NUMBER() OVER (PARTITION BY Year, Country)
FROM Country_Gold;
```

Results in the Row Number resetting per partition:

```
Year, Country, Event, Row_N
2008, CHN, +78KG (Heavyweight), 1
2008, CHN, -49KG, 2
...
2008, JPN, 48 - 55KG, 1
2008, JPN, 48 - 55KG, 2
```

## <a id="tips">Tips</a>

### <a id="explain">Explain</a>

In SQL Server (not Postgres), you can kinda see how much work is going on behind the scenes with something like:

    SET STATISTICS IO ON
    SET STATISTICS TIME ON
    <my_query>
    SET STATISTICS IO OFF
    SET STATISTICS TIME OFF

In Postgres, we don't have that exact command, but we do have explain:

    EXPLAIN (ANALYZE ON, BUFFERS ON) SELECT ...

### <a id="monitoring">Monitoring</a>

esides `EXPLAIN`, we have monitoring stats in `pg_statio_*` for IO stats.
The data isn't scoped to a session, but it can help in monitoring efficient queries in clean environments.

https://www.postgresql.org/docs/current/monitoring-stats.html

### <a id="sargable">Optimization - Sargable Query vs Nonsargable Query</a>

__Non-sargable__ stands for __Non Search Argument__. When we do this type of query, SQL is unable to use an index.
An example of this is when a function is used in the WHERE clause or a JOIN clause.

    -- Non-sargable Query
    SELECT EmployeeName
    FROM EmployeeTest
    WHERE LEFT(EmployeeName, 1) = 'A';
    -- Results in performing the `LEFT` function on every single record

A __sargable query__ stands for __Searchable Argument__. When we do this type of query, SQL is able to use indexes.
An example of the above SQL that is faster.

    -- Sargable Query
    SELECT EmployeeName
    FROM EmployeeTest
    WHERE EmployeeName LIKE 'A%';

Other Examples of Sargable:

    -- Non-Sargable
    WHERE YEAR(DateOfBirth) = '1952'

    -- Sargable Query
    WHERE DateOfBirth >= '19520101' AND DateOfBirth < '19530101'

Summary: Use a __sargable query__ by NOT including a function in your WHERE or JOIN clause so you can utilize indexes

So what do I do? Instead of using functions on your field (i.e. it shows up on the LEFT side of your field, instead we
want this on the right hand side)

### <a id="">Optimization - SELECT specific columns'</a>

Do not run `SELECT *` if you do not need all columns. If you just select the columns you need, it'll be faster
and you probably don't (and shouldn't) have an index on all columns.

### <a id="">Optimization - LIMIT</a>

LIMIT your results if you do not need everything

### <a id="">Optimization - LEFT vs RIGHT calculation</a>

If you do a calculation on the LEFT side vs the RIGHT side, this changes our speed:

    # Non-Sargable Query because Calculation done on left side
    WHERE Salary / 2 = 554677;

    # Sargable Query because Calculation done on right side
    WHERE Salary = (277338.5 * 2);

### <a id="indexes">Indexes<a>

In Postgres, you cannot just run a `SHOW INDEXES` command to the list the index information of a table or database.
Instead, we have the `pg_indexes` view that you can query or you can run `\d` to command on psql to view the index
information for a table.

See all indexes of a specific schema (using the `pg_indexes` view)

    SELECT
        tablename,
        indexname,
        indexdef
    FROM
        pg_indexes
    WHERE
        schemaname = 'myschema'
    ORDER BY
        tablename,
        indexname;

See all indexes for a table

    SELECT
        indexname,
        indexdef
    FROM
        pg_indexes
    WHERE
        tablename = 'mytable';

#### <a id="indexes-create">Create an Index</a>

You can create an index on specific column(s) for a table or a materialized view. There's pros and cons to an index.
It comes down to:

* Con: Indexes slow down inserts (need to add additional data for indexes, figure out what indexes to add, increase storage)
* Pro: Indexes speed up searches for getting data

#### <a id="indexes-include">Include Column in Index</a>

A lot of times when we create an index, we just think about the keyed columns.
There is the optional __INCLUDE__ clause that lets you specify a list of columns that will be included in the index
as a __non-key__ column.

Say you're searching based off an indexed column and return just that index column. This is optimal since it hits the
index (DateOfBirth) and return only that data (DateOfBirth).

    SELECT DateOfBirth
    FROM EmployeeTest
    WHERE DateOfBirth >= '19520101' AND DateOfBirth < '19530101';

However, if you change the above to `SELECT DateOfBirth, EmployeeName` and `EmployeeName` is not an index, we now
do a search for the index, then we go back and need to pull information for the `EmployeeName` column. If you are
regularly pulling this information back, you might want to `INCLUDE` a column into the index.

Just take care to not add many non-key columns to an index when you have really wide columns.
Also keep in mind that the index tuple cannnot exceed a certain size, otherwise data insertion fails.
A non-key column duplicates data from the index's table and increases the size of the index.

https://www.postgresql.org/docs/11/sql-createindex.html

#### <a id="indexes-stale">Stale and Fragmented Indexes</a>

Indexes store statistics (see monitoring) and uses estimations of what the data looks like in order to optimize itself.
These statistics get old and the distribution of data may get old. You would need to redinex then.

https://www.postgresql.org/docs/9.3/sql-reindex.html

    REINDEX

Update statistics with: https://www.postgresql.org/docs/9.3/sql-analyze.html

    ANALYZE [VERBOSE} [table_name]

Updating statistics store information into the `pg_statistic` system catalog

## <a id="resources">Resources</a>

Good basic and common usage: https://popsql.com/learn-sql/postgresql/

