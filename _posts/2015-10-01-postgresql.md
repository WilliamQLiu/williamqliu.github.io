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
    -  [Select with Order, Limit, Distinct](#selectproperties)
    -  [Select combine queries with Union](#selectunion)
    -  [Select with Max and Min](#selectmaxmin)
    -  [Select with a basic Subquery](#selectsubquery)
*  [SQL Joins and Subqueries](#joinsubquery)
    -  [From](#from)
    -  [Inner Join](#innerjoin)


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

#### <a id="selectfiltertext">Filter Text</a>

You can filter strings in tables (e.g. list all facilities with the word
'Tennis' in the name).

    SELECT *
    FROM cd.facilities
    WHERE name LIKE '%Tennis%';

    acid	name	            membercost	guestcost	initialoutlay	monthlymaintenance
    0	    Tennis Court 1	    5	        25	        10000	        200
    1	    Tennis Court 2	    5	        25	        8000	        200
    3	    Table Tennis	    0	        5	        320             10

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

## <a id="Aggregates">Aggregates</a>

You can select aggregates using `COUNT`, `SUM`, and `AVG`, `MAX`, `MIN`. `DISTINCT` can be used with aggregates.


# Notes

https://cloud.google.com/bigquery/docs/reference/standard-sql/data-types

## Arrays

ARRAY    Ordered list of zero or more elements of any non-ARRAY type.
An ARRAY is an ordered list of zero or more elements of non-ARRAY values. ARRAYs of ARRAYs are not allowed. Queries that would produce an ARRAY of ARRAYs will return an error. Instead a STRUCT must be inserted between the ARRAYs using the SELECT AS STRUCT construct.

Currently, BigQuery has two following limitations with respect to NULLs and ARRAYs:
BigQuery raises an error if query result has ARRAYs which contain NULL elements, although such ARRAYs can be used inside the query.
BigQuery translates NULL ARRAY into empty ARRAY in the query result, although inside the query NULL and empty ARRAYs are two distinct values.

Declaring an ARRAY type
ARRAY types are declared using the angle brackets (< and >). The type of the elements of an ARRAY can be arbitrarily complex with the exception that an ARRAY cannot directly contain another ARRAY.

Format
ARRAY<T>

## Structs

STRUCT    Container of ordered fields each with a type (required) and field name (optional).
Declaring a STRUCT type
STRUCT types are declared using the angle brackets (< and >). The type of the elements of a STRUCT can be arbitrarily complex.

Format
STRUCT<T>

