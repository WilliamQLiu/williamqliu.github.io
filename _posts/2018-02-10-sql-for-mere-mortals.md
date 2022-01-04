---
layout: post
title: SQL Queries for Mere Mortals
---


# {{ page.title }}

'SQL Queries for Mere Mortals' shows you how to write queries using a variety of databases,
including SQL Server, MySQL, and PostgreSQL

## Overview

The book is broken into six sections:

1. How Databases are mathematical models - Chapter 1
2. SQL Basics (e.g. select, where, order by) - Chapter 4
3. Working with multiple tables (e.g. joins, subqueries) - Chapter 7
4. Summarizing and Grouping Data (e.g. group by, having) - Chapter 12
5. Modifying sets of data (e.g. update, insert, delete) - Chapter 15
6. Complex problems (e.g. not, and, case) - Chapter 18

## Entity Relationship Diagram (ERD)

An __Entity Relationship Diagram__ (aka __ERD__, __ER Diagram__, __ER Model__) is a structural diagram for use
in database design. An ERD contains two different symbols and connectors that visualize two important
information: the major entities within the system scope and the inter-relationships among these entities.

### Sample Databases

Get the code samples here: http://www.informit.com/store/sql-queries-for-mere-mortals-a-hands-on-guide-to-data-9780134858333

* Bowling League - tracks bowling teams, members, matches they played
* Entertainment Agency - manage event bookings for entertainers, agents, and customers
* Recipes - save and manage recipes
* Sales Orders - manage orders for a store that sells bicycles
* School Scheduling - register students at a school

## Schemas

A __database schema__ is the skeleton structure that represents the logical view of the entire database.
If defines how the data is organized and how the relations among them are associated. It formulates all
the constraints that are to be applied on the data.

Here are some example Schemas we'll be using from the book:

### Bowling League

This database tracks bowling teams, team members, the matches they played, and the results.

#### Bowler Scores

Table `Bowler_Scores`

    Field           Data Type       Size        Required    Default     Primary Key     Foreign Key
    ----------------------------------------------------------------------------------------------------------
    MatchID         int             4           Yes         0           Yes             Match_Games.MatchID
    GameNumber      smallint        2           Yes         0           Yes             Match_Games.GameNumber
    BowlerID        int             4           Yes         0           Yes             Bowlers.BowlerID
    RawScore        smallint        2                       0
    HandiCapScore   smallint        2                       0
    WonGame         bit             1

Indexes for `Bowler_Scores`

    Index Name                  Attributes/Field Names
    --------------------------------------------------
    BowlersBowler_Scores
                                BowlerID

    Match_GamesBowler_Scores
                                MatchID
                                GameNumber

    PrimaryKey                  Primary
                                MatchID
                                GameNumber
                                BowlerID

#### Bowlers

Table `Bowlers`

    Field               Data Type       Size        Required    Default     Primary Key     Foreign Key
    ----------------------------------------------------------------------------------------------------------
    BowlerID            int             4           Yes         0           Yes
    BowlerLastName      nvarchar        50
    BowlerFirstName     nvarchar        50
    BowlerMiddleInit    nvarchar        1
    BowlerAddress       nvarchar        50
    BowlerCity          nvarchar        50
    BowlerState         nvarchar        2
    BowlerZip           nvarchar        10
    BowlerPhoneNumber   nvarchar        14
    TeamID              int             4                                                   Teams.TeamID

Indexes for `Bowlers`

    Index Name                  Attributes/Field Names
    --------------------------------------------------
    BowlerLastName
                                BowlerLastName

    PrimaryKey                  Primary
                                BowlerID

    TeamBowlers
                                TeamID

#### Match Games

Table `Match_Games`

    Field               Data Type       Size        Required    Default     Primary Key     Foreign Key
    ----------------------------------------------------------------------------------------------------------
    MatchID             int             4           Yes         0           Yes             Tourney_Matches.MatchID
    GameNumber          smallint        2           Yes         0           Yes
    WinningTeamID       int             4                       0

Indexes for `Match_Games`

    Index Name                  Attributes/Field Names
    --------------------------------------------------
    PrimaryKey                  Primary
                                MatchID
                                GameNumber

    Tourney_MatchesMatch_Games
                                MatchID

#### Teams

Table `Teams`

    Field               Data Type       Size        Required    Default     Primary Key     Foreign Key
    ----------------------------------------------------------------------------------------------------------
    TeamID              int             4           Yes                     Yes
    TeamName            nvarchar        50          Yes
    CaptainID           int             4                                                   Bowlers.BowlerID

Indexes for `Teams`

    Index Name                  Attributes/Field Names
    --------------------------------------------------
    BowlersTeams                Unique
                                CaptainID

    CaptainID                   Unique
                                CaptainID

    PrimaryKey                  Primary
                                TeamID

    TeamID                      Unique
                                TeamID

#### Tournaments

Table `Tournaments`

    Field               Data Type       Size        Required    Default     Primary Key     Foreign Key
    ----------------------------------------------------------------------------------------------------------
    TourneyID           int             4           Yes         0           Yes
    TourneyDate         date            8
    TourneyLocation     nvarchar        50

Indexes for `Tournaments`

    Index Name                  Attributes/Field Names
    --------------------------------------------------
    PrimaryKey                  Primary
                                TourneyID

#### Tourney Matches

Table `Tourney_Matches`

    Field               Data Type       Size        Required    Default     Primary Key     Foreign Key
    ----------------------------------------------------------------------------------------------------------
    MatchID             int             4           Yes         0           Yes
    TourneyID           int             4                       0                           Tournaments.TourneyID
    Lanes               nvarchar        5
    OddLaneTeamID       int             4                       0                           Teams.TeamID
    EvenLaneTeamID      int             4                       0                           Teams.TeamID

Indexes for `Tourney_Matches`

    Index Name                  Attributes/Field Names
    --------------------------------------------------
    PrimaryKey                  Primary
                                MatchID

    TeamsTourney_Matches
                                OddLaneTeamID

    TeamsTourney_Matches1
                                EvenLaneTeamID

    TournamentsTourney_Matches
                                TourneyID

#### ztblBowlerRatings

Table `ztblBowlerRatings`

    Field               Data Type       Size        Required    Default     Primary Key     Foreign Key
    ----------------------------------------------------------------------------------------------------------
    BowlerRating        nvarchar        15          Yes
    BowlerLowAvg        smallint        2
    BowlerHighAvg       smallint        2

Indexes for `ztblBowlerRatings`

    Index Name                  Attributes/Field Names
    --------------------------------------------------
    PrimaryKey                  Primary
                                BowlerRating

#### ztblSkipLabels

Table `ztblSkipLabels`

    Field               Data Type       Size        Required    Default     Primary Key     Foreign Key
    ----------------------------------------------------------------------------------------------------------
    LabelCount          int             4           Yes                     Yes

Indexes for `ztblSkipLabels`

    Index Name                  Attributes/Field Names
    --------------------------------------------------
    PrimaryKey                  Primary
                                LabelCount

#### ztblWeeks

Table `ztblWeeks`

    Field               Data Type       Size        Required    Default     Primary Key     Foreign Key
    ----------------------------------------------------------------------------------------------------------
    WeekStart           date            8           Yes                     Yes
    WeekEnd             date            8

Indexes for `ztblWeeks`

    Index Name                  Attributes/Field Names
    --------------------------------------------------
    PrimaryKey                  Primary
                                WeekStart

#### Relationship Constraints

    Name                            Parent          Parent Fields       Child           Child Fields
    ------------------------------------------------------------------------------------------------
    BowlersBowler_Scores            Bowlers         BowlerID            Bowler_Scores   BowlerID
    BowlersTeams                    Bowlers         BowlerID            Teams           CaptainID
    Match_GamesBowler_Scores        Match_Games     MatchID_GameNumber  Bowler_Scores   MatchID_GameNumber
    TeamsBowlers                    Teams           TeamID              Bowlers         TeamID
    TeamsTourney_Matches            Teams           TeamID              Tourney_Matches OddLaneTeamID
    TeamsTourney_Matches1           Teams           TeamID              Tourney_Matches EvenLaneTeamID

## Types of Databases

A database is an organized collection of data used to model some type of organization or organizational process.

There are two types of databases:

* __Operational Databases__ - used to collect, modify, and maintain data on a day-to-day basis. The data stored
                              is _dynamic_, meaning it changes constantly and always reflect up-to-the-minute information.
* __Analytical Databases__ - stores and tracks historical and time-dependent data that is _static_ (data is not really modified)
                             New data might often be added and is used for tracking trends, viewing statistical data over
                             a long period, or making tactical or strategic business projections.

### Relational Database Systems

A __relational database management system (RDBMS)__ is a software application program used to create, maintain,
modify, and manipulate a relational database.

A __data warehouse__ is a large store of data accumulated from a wide range of sources and would enable organizations
to access data stored in any number of nonrelational databases.

## Anatomy of a Relational Database

Data in a relational database is stored in __relations__, which appear as __tables__.
Each relation is made up of __tuples__ (records of rows) and __attributes__ (fields or columns).

### Tables

__Tables__ are the main structures in the database. Each table represents a single, specific object.
Each table contains at least one column that uniquely identifies each of its rows (aka __primary key__).

The subject that a given table represents is usually either an __object__ or an __event__.

#### Table representing Objects

When the subject of a table is an __object__, the table represents something tangible; a person, place, or thing.
Example objects are Students, Buildings, Equipment, Customers

    CustomemrID     FirstName   LastName    StreetAddress   City        State   ZipCode
    ---------------------------------------------------------------------------------
    1010            Angel       Kennedy     667 Red Rd      Austin      TX      78710
    1011            Alaina      Hallmark    2114 Longview   San Diego   CA      92199

#### Table representing Events

When the subject of a table is an __event__, the table represents something that occurs at a given point in
time and has characteristics that you wish to record. Example events are Judicial Hearings, Lab Test Results,
Patient Visits, and Geological Surveys.

    PatientID   VisitDate   VisitTime   Physician   BloodPressure   Temperature
    ---------------------------------------------------------------------------
    92001       2006-05-01  10:30       Ehrlich     120/80          98.8
    96106       2006-05-02  11:00       Hallmark    160/90          99.1

## Columns and Rows

A __column__ is the smallest structure in the database. It represents a characteristic of the
subject of the table. Every column in a properly designed database contains one and only one value.
The name of the column identifies the type of value it holds, that way entering data is intuitive.
Example column names might be FirstName, LastName, City, State, ZipCode

A __row__ represents a unique instance of the subject of a table. It is composed of the entire set
of columns in a table, regardless of whether or not the columns contain any values.
Because of how a table is defined, each row is identified throughout the database by a unique value
in the __primary key__ column(s) of that row.

## Keys

__Keys__ are special columns that play very specific roles in a table; depending on the type
of key, there's a different purpose. The two most important ones help describe the relationships
between tables using the _primary key_ and the _foreign key_.

### Primary Key

A __primary key__ is one or more columns that uniquely identify each row within a table.
When a primary key is made up of two or more columns, it is known as a __composite primary key__.

A primary key is important for two reasons:

* its _value_ identifies a _specific row_ throughout the entire database
* its _column_ identifies a _given table_ throughout the entire database

Primary keys also enforce table-level integrity and help establish relationships with other tables.
Every table should have a primary key.

Here's an example:

    'Agents' Table
    AgentID     AgentFirstName      AgentLastName   DateHired   AgentHomePhone
    --------------------------------------------------------------------------
    1           William             Thompson        15-May-01   555-2681
    2           Scott               Bishop          10-Feb-03   555-2666
    3           Carol               Viescas         09-Sep-00   555-2571

    'Entertainers' Table
    EntertainerID   AgentID     EntertainerName     EntertainerPhone
    ----------------------------------------------------------------
    1001            1           Carol Peacock Trio  555-2691
    1002            3           Topazz              555-2591
    1003            3           JV & the Deep Six   555-2511

In the 'Agents' table, we have the Primary Key of 'AgentID'.
In the 'Entertainers' table, we have the Primary Key of 'EntertainerID' and Foreign Key of 'AgentID'
that references 'Agents' table 'AgentID'.

### Foreign Key

When you determine that a pair of tables have a relationship to each other, you establish
the relationship by taking a copy of the primary key from the first table and insert it
into the second table, where it becomes the __foreign key__.

The term foreign key comes from the fact that the second table already has a primary key of its
own, and the primary key you are introducing from the first table is foreign to the second table.

Foreign keys are important for establishing relationships between pairs of tables and
also for ensuring relationship-level integrity. This means that the rows in both tables
will always be properly related because the values of a foreign key MUST be drawn from the
values of the primary key from which it refers (i.e. helps you avoid 'orphaned rows')

## Views

A __view__ is a virtual table composed of columns from one or more tables in the database.
The tables that comprise the view are known as __base tables__. The view is virtual because
it draws data from base tables rather than storing any data on its own.

## Relationships

If rows in a given table can be associated in some way with rows in another table, the tables
have a relationship between them. There's three types of relationships that can exist between
a pair of tables:

* one-to-one
* one-to-many
* many-to-many

### One-to-One

A pair of tables has a __one-to-one__ relationship when a single row in the first table is
related to _only one_ row in the second table AND a single row in the second table is related
to _only one_ row in the first table.

The first table is usually referred to as the __primary table__.
The second table is usually referred to as the __secondary table__.

    'Agents' Table (the primary table)
    AgentID     AgentFirstName      AgentLastName   DateHired   AgentHomePhone
    --------------------------------------------------------------------------
    1           William             Thompson        15-May-01   555-2681
    2           Scott               Bishop          10-Feb-03   555-2666
    3           Carol               Viescas         09-Sep-00   555-2571

    'Compenstation' Table (the secondary table)
    AgentID     Salary      CommissionRate
    --------------------------------------
    1           $35,000     4.00%
    2           $27,000     4.00%
    3           $30,000     5.00%

'Compensation' is the secondary table because it doesn't make sense to add a salary without
having an associated 'Agent'.
You cannot add a row to the secondary table ('Compensation') unless a row exists in the primary table.
These one-to-one types of tables are uncommon. You might split this data into two parts for
confidentiality purposes.

### One-to-Many

When a pair of tables has a __one-to-many__ relationship, a single row in the first table can
be related to _many rows_ in the second table, but a single row in the second table can only be
related to _only one_ row in the first table.

So how does this work? You take the primary key of the primary table and insert it into the
secondary table as a foreign key.

    'Entertainers' Table
    EntertainerID   AgentID     EntertainerName     EntertainerPhone
    ----------------------------------------------------------------
    1001            1           Carol Peacock Trio  555-2691
    1002            3           Topazz              555-2591
    1003            3           JV & the Deep Six   555-2511

    'Engagements' Table
    EngagementID    EntertainerID   CustomerID  StartDate   EndDate
    ------------------------------------------------------------------
    5               1003            10006       2007-09-11  2007-09-14
    7               1002            10004       2007-09-11  2007-09-19
    10              1003            10005       2007-09-17  2007-09-26
    12              1001            10014       2007-09-18  2007-09-26

In the above example, a single row in the Entertainers table can be related to many rows in
the Engagements table, but a single row in the Engagements table can be related to only one row
in the Entertainers table. EntertainerID is a foreign key in the Engagements table.

### Many-to-Many

A pair of tables is a many-to-many relationship when a single row in the first table can be
related to _many_ rows in the second table, and a single row in the second table can be related
to _many_ rows in the first table.

In order to establish this many-to-many relationship correctly, you must create a __linking table__,
which provides an easy way to associate rows from one table with those of the other.
You define a linking table by taking a copy of the primary key of each table and using them
to form the structure of the new table. Together, they form the composite primary key of the
linking table, and separately they each serve as a foreign key.

Example:

    'Customers' Table
    CustomerID      CustFirstName       CustLastName        CustPhone
    10001           Doris               Hartwig             555-2671
    10002           Deb                 Waldal              555-2496
    10003           Peter               Brehm               555-2501

    'Entertainers' Table
    EntertainerID   AgentID     EntertainerName     EntertainerPhone
    ----------------------------------------------------------------
    1001            1           Carol Peacock Trio  555-2691
    1002            3           Topazz              555-2591
    1003            3           JV & the Deep Six   555-2511

The above many-to-many relationship is __unresolved__ because the many-to-many relationship has
not been properly established. The issue is: How do you associate rows from the first table
to the second table? The solution is to create and use a __linking table__.

    Engagements Table (as a 'linking table')
    EngagementID    CustomerID      EntertainerID   StartDate
    ---------------------------------------------------------
    43              10001           1001            2007-10-21
    58              10001           1002            2007-12-01
    62              10003           1005            2007-12-09
    71              10002           1003            2007-12-22
    125             10001           1003            2008-02-23

In the above, we create a new table 'Engagements' as a linking table. Here you can store additional
information in the linking table like the StartDate or Cost.

## Database Theory vs Database Design

__Database Theory__ is the principles and rules that formulate the basis of the relational database
model. Database theory guarantees that the relational database is structurally sound and that all
actions taken on the data in the database have predictable results.

__Database Design__ is the structured, organized set of processes used to design a relational
database. A good database design methodology helps ensure the integrity, consistency, and accuracy
of the data in the database. Consider books like 'Database Design for Mere Mortals' or
'An Introduction to Database Systems'.

## Database Structure

In order to make sure that your database structure is sound, let's take a look at:

* Columns
* Tables

### Database Structure (Columns)

Since columns are our smallest data structure, let's look at them first:

* Is the column name descriptive and meaningful for everyone in the entire organization?
  Remember that several departments might use the database
* Is the column name clear and unambiguous?
  PhoneNumber is a column name that can be misleading; is it a home phone? a work phone?
* Do not use the same column name in several tables. If you have a City column, add a prefix
  of the Table name (e.g. Table Customers with City column will be 'CustCity')
* Do not use abbreviations as a column name; they're hard to read and understand
* Do not put more than one characteristic into a column (e.g. Phone/Fax)
* Give columns a singular name (e.g. Category instead of Categories); table names should have plural
* Make sure the column represents a specific characteristic of the subject of the table
* A column should only contain a single value (a __multivalued__ column can potentially store
  several instances of the same type of value; a __multipart__ column can potentially store
  two or more distinct values); these wreck havoc in a database
* Make sure a column does not store the result of a calculation or concatentation.
  A column (unlike a cell in a spreadsheet) does not store an actual calculation.
  If you do store calculated values, when the value of any part of the calculation changes,
  the result value stored in the column is not updated.

#### Multipart Columns

__Multipart__ columns occur where you can say yes to the question: "Can I take the current value
of this column and break it up into smaller, more distinct parts?". Here's an example:

    CustomerID      CustomerName        StreetAddress
    ----------------------------------------------------------------------------
    1001            Suzanne Viescas     15127 NE 24th, #383 Redmond, WA 98052
    1002            William Thompson    122 Spring River Drive, Duvall, WA 98019
    1003            Gary Hallmark       Route 2, Box 203B, Auburn, WA 98002

In the above, you can see that columns `CustomerName` and `StreetAddress` can be broken up into
more distinct columns. CustomerName can be CustFirstName and CustLastName while StreetAddress can
be broken up into CustAddress, CustCity, CustZipCode, CustState, etc.

More complicated multipart examples might be:

    'Instruments' Table
    InstrumentID    Manufacturer    InstrumentDescription
    -----------------------------------------------------
    GUIT2201        Fender          Fender Stratocaster
    MFX3349         Zoom            Player 2100 Multi-Effects
    AMP1001         Marshall        JCM 2000 Tube Super Lead

In the above, you can see that `InstrumentID` has an ID as well as the type (e.g. GUIT for Guitar).
To resolve this, split InstrumentID to an InstrumentID as well as InstrumentType column.

#### Multivalued Columns

To identify a multivalued column, you can see that the data is stored in our columns with some
commas, semicolons, or some other delimiter.

    'Pilots' Table
    PilotID     PilotFirstName      PilotLastName       Certifications
    -----------------------------------------------------------------------
    25100       Sam                 Alborous            727, 737, 757, MD80
    25101       Jim                 Wilson              737, 747, 757
    25102       David               Smith               757, MD80, DC9

In the above, the `Certifications` column will cause data integrity issues (e.g. difficult to
update). The values in multivalued columns have a many-to-many relationship with every row
in its parent table: one specific value (e.g. `737`) can be associated with any number of rows
in the parent table and a single row in the parent table can be associated with any number of
rows in the multivalued column. In order to solve any many-to-many relationships, you need a
__linking table__.

    'Pilots' Table
    PilotID     PilotFirstName      PilotLastName
    ---------------------------------------------
    25100       Sam                 Alborous
    25101       Jim                 Wilson
    25102       David               Smith

    'Pilot_Certifications' Table (the 'linking table')
    PilotID     CertificationID
    ---------------------------
    25100       8102
    25100       8103
    25100       8105
    25100       8106
    25101       8103
    25101       8104
    25101       8105

    'Certifications' Table
    CertificationID     TypeofAircraft
    ----------------------------------
    8102                Boeing 727
    8103                Boeing 737
    8104                Boeing 747
    8105                Boeing 757

So that's how you resolve a multivalued column using a linking table.

### Database Structure (Tables)

Tables are the basis for every SQL query you create. Poorly designed tables will create data
integrity problems. Look out for:

* Table names should be plural form (instead of singular for columns)
  A table stores a collection of instances of the subject of the table.
* Is the Table name unique and descriptive enough for the entire organization?
* Is the Table name clear in identifying the subject of the table? Remember that the subject of
  the table is either an __object__ or an __event__.
* Does the Table name contain words that convey physical characteristics? Avoid using words like
  'File', 'Record', and 'Table' in the Table name since they add confusion. Instead consider say
  `Employee_Record`
* Don't use an acronym or abbreviation as a Table name
* Does your Table name identify more than one subject? If you have AND OR in the table name,
  it's a pretty good sign that you have more than one subject.
* Make sure that the table represents a single subject
* Make sure that each table has a __primary key__; we use this to unique identify each row within
  a table as well as using it to establish table relationships
* Make sure there are not any multipart or multivalued columns
* Make sure there are no calculated columns in the table
* Make sure the table is free of any unnecessary duplicate columns

#### Remove unnecessary Duplicate Columns

You don't want duplicate columns because this will cause data integrity errors.

Example 1:

    Staff Table
    StaffID     StaffFirstName      StaffLastName
    ---------------------------------------------
    98014       Peter               Brehm
    98109       Mariya              Sergienko

    Classes Table
    ClassID     Class       StaffID     StaffLastName   StaffFirstName
    ------------------------------------------------------------------
    1031        Art History 98014       Brehm           Peter
    1030        Art History 98014       Brehm           Peter

Here we have duplicate columns of 'StaffLastName' and 'StaffFirstName'. Remove these from
Classes Table because we can use StaffID to establish the relationship (and since Staff Names
belong better with the Staff Table).

Example 2:

Another example is if you have duplicate columns as different column names. For example:

    'Employees' Table
    EmployeeID  EmpLastName     EmpFirstName    Committee1  Committee2  Committee3
    ------------------------------------------------------------------------------
    7004        Gehring         Darren          Steering
    7005        Kennedy         John            ISO 9000    Safety

The issue is that what if there's a Committee4 or what if you need to search through all these columns?
You'll realize that instead we should have a many-to-many relationship between employees and committees and that
is solved with a linking table.

    'Employees' Table
    EmployeeID  EmpLastName     EmpFirstName
    ----------------------------------------
    7004        Gehring         Darren
    7005        Kennedy         John
    7006        Thompson        Sarah

    'Committee_Members' Table
    EmployeeId      CommitteeID
    ---------------------------
    7004            103
    7005            104
    7005            102
    7006            102

    'Committees' Table
    CommitteeID     CommitteeName       MeetingRoom     MeetingDay
    --------------------------------------------------------------
    100             Budget              11-C            Tuesday
    101             Christmas           9-F             Monday
    102             Safety              12-B            Monday
    103             Steering            12-D            Tuesday
    104             ISO 9000            Main-South      Wednesday

Example 3:

We might also run into issues of repeating phone numbers when we have multiple home phones or work phones.

    'Employees' Table
    EmployeeID  EmpLastName     EmpFirstName    EmpHomePhone    EmpWorkPhone    EmpCellPhone
    ----------------------------------------------------------------------------------------
    7004        Gehring         Darren          555-1234        556-1234
    7005        Kennedy         John            555-2345
    7006        Thompson        Sarah           555-3456

We can solve that with having a one-to-many relationship:

   'Employees' Table
    EmployeeID  EmpLastName     EmpFirstName
    ----------------------------------------
    7004        Gehring         Darren
    7005        Kennedy         John
    7006        Thompson        Sarah

    'Phone_Numbers' Table
    EmployeeID  PhoneID     PhoneType       PhoneNumber
    ---------------------------------------------------
    7004        1           Home            555-1234
    7005        2           Home            555-2345
    7006        3           Home            555-3456

## Identification is the Key

### Primary Key

Every table in your database needs a __primary key__, which helps with:

* uniquely identifying each row within a table
* establish a relationship between a pair of tables

A primary key is either:

* a __simple primary key__ (aka __primary key__) when it is made up of a single column
* a __composite primary key__ when it is made up of two or more columns

Define a simple primary key over a composite primary key whenever you can; it's more efficient and it's easier to
use when establishing a table relationship

To check if your primary key columns are sound:

* Do the columns uniquely identify each row in the table? A good primary key ensures that we have a means of accurately
  identifying or referencing each row in this table from other tables in the database.
* Does this column or combination of columns have unique values? We cannot have duplicates, since that won't let us
  uniquely identify the object or event.
* Will the column ever contain unknown values? We cannot have unknown values in your primary key column(s)
* Can the value of these columns ever be optional? Your column(s) for primary key has to be required
* Can the value of these columns ever be modified? Your primary key columns should remain static and never change.

If you don't have column(s) for a primary key, you can create an artificial primary key (e.g. an EmployeeID that auto
increments).

## Establish Solid Relationships

You can learn how to diagram relationships in: Database Design for Mere Mortals

Diagram One-to-One Relationship

    Employees                                 Employee_Confidential
    ----------------                          ---------------------
    |EmployeeID  PK| -|-------------------|-  |EmployeeID    PK   |
    ----------------                          ---------------------

Diagram One-to-Many Relationship

    Students                                  Instruments
    ----------------                          -------------------
    |StudentID   PK| -|-------------------    |InstrumentID   PK|
    ----------------                     |  / |                 |
                                         |--- |StudentID      FK|
                                            \ |------------------

Diagram Many-to-Many Relationship

    Pilots                                                         Certifications
    ---------------                                                ---------------------
    |PilotID    PK| -|--|                                   |---|- |CertificationID PK |
    ---------------     |     Pilot_Certifications          |      ---------------------
                        |   / |----------------------|      |
                        |---- |PilotID          CPK  |      |
                            \ |                      | \    |
                              |CertificationID  CPK  | ------
                              ------------------------ /

Note: Remember that these relationships need the matchings keys to have the same data type.

So in order to make sure that your database has __referential integrity__, we need to make sure
that there aren't any orphaned data. There's a few things we can do:

### Establish a Deletion Rule

A __deletion rule__ says that when a user makes a request to delete a row in the primary table,
then we don't leave any __orphaned rows__ in the secondary table. There are two types of deletion rules:

* __restrict deletion rule__ - does not allow you to delete the requested row unless any related rows
    are deleted _before_ deleting your requested row. This option is usually the default rule
* __cascade deletion rule__ - delete the row on the primary table causes automatic deletion of
    any related rows in the secondary table; use this rule very carefully or else you might delete everything!

Basically, ask yourself "If a row in my primary / one-sided table is deleted, should related rows
in the secondary / many side table be deleted as well?

In our above diagrams, a `(C)` is usually next to cascading and `(R)` for restrict

### Type of Participation

When you establish a relationship between a pair of tables, each table participates in a
particular manner. The _type of participation_ assigned to a table determines whether a row
must exist in that table before you can enter a row into another table. These two types are:

* __Mandatory Participation__ - at least one row must exist in this table before you can enter
  any rows into the other table (indicated by a `|` in a diagram)
* __Optional Participation__ - there is no requirement for any rows to exist in this table before
  you enter any rows in the other table (indicated by a `O` in a diagram)

### Degree of Participation

After you determine how each table will participate in the relationship, you need to then figure out
__to what degree__ each will participate. You do this by determining the min and max number of rows in one
table that can be related to a single row in the other table. This is called the table's __degree of participation__.

You can see the degree of participation indicated by values like `(x, y)` where x can be associated with a minimum
and maximum number of associations. E.g. `(1, 1)` means can only be associated with one and only one agent.
`(0, 6)` means there doesn't have to be an association, but cannot have more than six associations.

### Automatic key rules

Some database systems allow you to define a rule that cascades the key value from the 'one' table to the 'many' table
if a value of the primary key in the 'one' table changes. Other systems allow automatic deletion of rows in the
'many' table when you change the value of the primary key in the 'one' table.

## SQL Standards

__ANSI/ISO__ SQL Standard is the most widely accepted standard to date.

__ODBC__ is an Open Database Connectivity (ODBC) specification, based off the __Call-Level Interface (CLI)__ specification published.
It's basically a way to bind an SQL Database to a user-interface language.

# Part II - Chapter 4 (Create a Simple Query)

## Data vs Information

There is a distinct difference between __data__ and __information__.

* __Data__ is what you store in the database.
* __Information__ is what you retrieve from the database.

This will help you keep things in perspective. Remember that a database is designed to provide meaningful
information to someone in your organization. However, the information can only be provided if the data is structured
to support that information.

The values that you store in the database are data. This data is static (remains in the same state until you modify
it by some manual or automated process). Say we have the following data:

    Katherine   Ehrlich 89931   Active  79915

The above data is useless until it's been proecssed. After you process the data so that it's meaningful, we get information.
You can present this data in a variety of ways. The main idea is that you must process your data in a way that
returns meaningful information.

    First Name: Katherine
    Last Name: Ehrlich
    ID: 89931
    Status: Active
    Zip: 79915

## SQL SELECT

The `SELECT` operation in SQL retrieves data and manipulates that into information.

The `SELECT` operation can be broken down into three smaller operations:

1. the SELECT statement
2. the SELECT expression
3. the SELECT query

Each of the above smaller operations has its own set of keywords (aka __clauses__).

### Saving a Select

You can save a SELECT statement as a:

* __query__
* __view__
* __function__
* __stored procedure__

### Results of a SELECT

When you execute a SELECT statement, it usually retrieves one or more rows of information, which we call a __result set__.

So what happens when you request information from the database?

* You ask a question, like "What cities do our customers live in? Give me a list of employees and their info"
* Translate your question into a form like `Select <item> from the <source data>`
* Translate the above question into a query statement like `Select city from customers`

### Building your SELECT Statement

* You can specify multiple columns
* Use `*` to specify all columns (only use this as a 'quick and dirty' way to get data; explicit better than implicit)
* Use `DISTINCT` as an optional keyword to eliminate duplicate rows; this evaluates the values of all the columns as
  a __single unit__ on a row-by-row basis and eliminates any redundant rows it finds (otherwise shows every occurrence)
  Add `DISTINCT` before the list of columns specified in a `SELECT` clause

## SQL Sorting / Ordering

By default, the result set of a SQL statement is unordered. You can use the `ORDER BY` clause to specify the sequence
of rows in the final result set.

* You can only order by any columns that have been returned in the result set
* You can specify order type with either `ASC` or `DSC`; default is ascending
* Check your database's __collating sequences__ to determine the order of precedence for every character
  (e.g. if lowercase is before uppercase characters)
* If there are multiple columns to order by, the database will evaluate the columns from ORDER BY from left to right

# Chapter 5 - Getting More Than Simple Columns

## Expressions

If you want to select more than simple columns, you'll need to create an __expression__, which is some form of
operation involving numbers, strings, or dates and times. Expressions are used to broaden or limit the scope of
the information you want to retrieve.

### Data Types

Every column in the database has an assigned __data type__, which determines the type of values the column can store.
There are seven general categories of types of data, which include:

* __character__ - `CHAR` (fixed length characters, better performance, roughly +50%) or `VARCHAR` (varying length character).
  Most common is `varchar`. Maximum this data type can hold is also `65,535` characters per row (and not just 255 char per column).
  If larger than say 255 or 1024 chars, then use `TEXT` (aka __character large object__, __clob__)
  This is the one I've seen used most commonly (`varchar` for small fields, `text` for large).
* __national character__ - similar to char, but characters now draw from ISO-defined foreign language. I haven't
  needed to use this.
* __binary__ - store binary data like images, sounds, videos, or complex embedded documents. Consider a static file
  once you're past a certain size and if you have to, try to put this in a separate table.
* numeric (__exact numeric__ and __approximate numeric__) - with __exact numeric__ we store whole numbers and numbers
  with decimal places (e.g. `INT`, `DECIMAL`, `SMALLINT`). With __approximate numeric__ we store numbers with decimal
  places and exponential numbers that don't have a precision and scale per se (e.g. `FLOAT`, `REAL`, `DOUBLE PRECISION`)
* __boolean__ - stores true and false values, usually in a single binary bit (e.g. `BIT`, `INT`, `TINYINT`)
* __datetime__ - stores dates, times, and combinations of both. The SQL standard defines the date format as year-month-day
  and time values as 24-hour clock.
* __interval__ - stores the quantity of time between two datetime values, expressed as either year, month; year/month;
  time; or day/time. Not all major databases support the `INTERVAL` data type. For MySQL, I usually use something like:
  `DATE(NOW()) - INTERVAL 1 MONTH`

### CAST function

The `CAST` function converts a literal value or the value of a column into a specific data type. This helps
ensure that the data types of the values in the expression are compatible.

When you do convert a value in one column into another, make sure you:

* "don't put a ten-pound sack in a five-pound box" - if you put a varchar into a char that is smaller, you'll get truncation issues
* "don't put a square peg in a round hole" - you can't put a string into a number, say zip code with letters going into number only
* "ten-pound sack v2" - make sure that the target data type fits (otherwise you might get rounding issues)
* "put a square peg in a round hole w/ limitations" - sometimes you can fit a numeric into say a char data type, but you'll
  get unexpected outcomes like padding blanks

#### Types of Literals

* __Character String Literal__ is a sequence of individual characters enclosed in single quotes (e.g. 'This is an example')
* __Numeric Literal__ is made up of an optional sign and a number and can include a decimal place, exponent symbol, and exponential number.
* __Datetime Literal__ is made up of __date literals__, __time literals__, and __timestamp literals__.

Examples:

    CAST('2019-01-21' AS DATE)
    CAST('03:30:25' AS TIME)
    CAST('2019-01-22 14:25:00' AS DATETIME)

#### Types of Expressions

There are three types of SQL Expressions:

* __Concatentation__ - combine two or more columns into one (e.g. 'Mike' || 'Liu' will retur 'MikeLiu'
* __Mathematical__ - Add, subtract, multiply, divide numeric columns or literals. You can also apply absolute value, log, etc.
* __Date and Time Arithmetic__ - Add, subtract to dates and times

### AS

Most calculated columns require a name. You an use the `AS` keyword to supply an alias for a real column name.

    SELECT expression AS my_column_name

    SELECT FirstName || ' ' || LastName AS MyName FROM People

### NULL

A __NULL__ represents a missing or unknown value. Null does NOT represent a zero, a character string of one
or more blank spaces, or a zero-length character string.

A Null might occur legitimately for a number reasons, including:

* a specific value you need for a column is yet undefined
* the values are truly unknown
* none of its values apply to a particular row

#### Problem with Nulls

Nulls do not work nicely with mathematical operations. Any operation involving a Null evaluates to Null.
It kinda makes sense; if a number is unknown, then the result of that operation is unknown.

Examples:

    (Null * 3) + 4 = Null

# Chapter 6 - Filtering your Data

## WHERE

Use a __WHERE__ clause in a SELECT statement to filter the data. You can use basic predicates like:

* Comparison (e.g. =, <>, <, >, <=, >=)
* BETWEEN (Range) (e.g. BETWEEN 10 AND 20)
* IN (e.g. IN ('a', 'b', 'c'))
* LIKE (Pattern Match)
* IS NULL

### ESCAPE

The __ESCAPE__ option allows you to designate a single character string literal to be the __escape character__.
It tells the database how to interpret a percent sign or an underscore character.

    SELECT ProductName, ProductCode
    FROM Products
    WHERE ProductCode LIKE 'G\_00' ESCAPE '\'

## NOT

Exclude rows from a result set by using the __NOT__ operator.

    SELECT StaffID, Title
    FROM Faculty
    WHERE Title
    NOT IN ('Professor', Associate Professor')

You can apply NOT before a search condition, e.g.

    SELECT FirstName, LastName, City
    FROM People
    WHERE NOT City = 'Reedley'

## AND and OR

You can combine two or more conditions using the __AND__ operator. __All__ conditions must be met in order for a
row to be included in a result set.

You can combine two or more conditions using the __OR__operator. __Either__ conditions must be met in order for a
row to be included in a result set.

## SQL Order of Precedence

Use parenthesis to combine and prioritize certain conditions. Otherwise, the evaluation order is:

    Evaluation Order    |   Type of Operator
    1                   |   Positive sign (+), Negative sign (-)
    2                   |   Multiplication (*), division (/)
    3                   |   Addition (+), subtraction (-)
    4                   |   =, <>, <, >, <=, >=, BETWEEN, IN, LIKE
    5                   |   NOT
    6                   |   AND
    7                   |   OR

# Part III - Working with Multiple Tables

## Set

What is a __set__? Each table in your database is a set of information about one subject. A set of data can be
as small as one column from one row in one table. There can even be empty sets.

Each row in a result set is a __member__ of the set.
The values in the columns are specific __attributes__ of each member.

We use result sets of information to solve more complex problems that require linking data from two or more tables.

### Set Operations

The three most common set operations are:

* __Intersection__ - find the common elements in two or more different sets (e.g. Show me recipes that contain both lamb and rice)
* __Difference__ - find items that are in one set but not another (e.g. Show me recipes that contain lamb, but do not contain rice)
* __Union__ - combine two or more similar sets (e.g. Show me all the recipes that contain either lamb or rice)

Think of a __Venn diagram__ (aka __Euler__ diagram).

#### Intersection

One of the limitations of using a pure intersection is that the values must match in all the columns in each result set.
If you only want to match on one or only a few column values from each set, then you will need a __JOIN__.

Use the __INTERSECT__ keyword to do set intersection and find the common values

Example:

E.g. Show me orders with a specific product number that has a Quantity greater than zero.

```SQL
SELECT DISTINCT OrderNumber
FROM Order_Details
WHERE ProductNumber IN (1, 2, 6, 11)
INTERSECT
SELECT DISTINCT OrderNumber
FROM Supplier_Details
WHERE Quantity > 0;
```

#### Difference

Remove from the first set all the matching members you find in the second set, and the result is the __difference__.

Use the __EXCEPT__ keyword to do set difference and find the not common values

E.g. Show me the orders that contain a bike but not a helmet.

```SQL
SELECT DISTINCT OrderNumber
FROM Order_Details
WHERE ProductNumber IN (1, 2, 6, 11)
  AND ProductNumber NOT IN (10, 25, 26)

#### Union

A __UNION__ lets you select the _rows_ from two or more similar result sets and combine them into a single
result set. Notice that we said _rows_ and not columns.

If you run a __UNION__, you combine two sets of similar information into one set. Duplicates are lost.
If you want to keep duplicates, use a __UNION ALL__.

In order to run a UNION, you need to make sure that:

* Each of the two SELECT statements that you are linking with a UNION must have the same number of output columns
* Each corresponding column must be 'comparable'.

UNION and UNION ALL combines the result of two or more SELECT statements (with each statement having the same number of columns, data types, and order).

* UNION selects distinct values only.
* UNION ALL allows duplicates.

```SQL
SELECT column_name(s) FROM table1 UNION SELECT column_name(s) FROM table2;

SELECT column_name(s) FROM table1 UNION ALL SELECT column_name(s) FROM table2;
```

## Chapter 8 - INNER JOINs

### JOINS

Use __JOIN__ to link multiple tables together.
A Join is like an INTERSECT, except instead of matching on all the columns, you specify the columns you want to join on.

You specify a JOIN as part of the FROM clause in a SQL statement. A JOIN defines a 'logical table' that is the result
of linking two tables or results sets.

Since we are joining multiple tables that might have the same column name, we need to provide a __column reference__
that includes the table name. For example, here is a single table select that includes the table name:

    SELECT Employees.FirstName, Employees.LastName, Employees.PhoneNumber
    FROM Employees

To solve the JOIN, the database system combines every row in the first table with every row in the second
table; this combination of all rows from one table with every row in the second talbe is called a __Cartesian product__.

### Correlation Name

You can create a __correlation name__ to any table you list in your FROM clause.
Follow the table name with the optional keyword `AS` and then the correlation name you want to assign.

This can be confusing because you usually write the SELECT clause before you write the FROM clause.
If you plan to give a table an alias in the FROM clause, you have to use that alias when you qualify columnn names
in the SELECT clause.

Example:

    SELECT R.RecipeTitle, R.Preparation, RC.RecipeClassDescription
    FROM Recipe_Classes AS RC
      INNER JOIN Recipes AS R
    ON RC.RecipeClassID = R.RecipeClassID

### INNER JOIN

__INNER JOIN__ is the default join. It is the same as saying __JOIN__, but say __INNER__ to be explicit.
Examples:

  Displaying bowling teams and the name of each team captain
  Return only students that have registered for a class and classes for which a student has registered

Note: Remember that when you join, provide a column reference that includes the table name. E.g.

```
SELECT Employees.FirstName, Employees.LastName, Employees.PhoneNumber
FROM Employees
```

### Derived Tables

An embedded SELECT statement is a __derived table__, meaning you can substitute an entire SELECT statement
for any table name in your FROM clause. What we're doing is deriving a subset of data from one or more tables.
You need to assign a correlation name so that the result of evaluating your embedded query has a name.

    SELECT R.RecipeTitle, R.Preparation, RCFiltered.ClassName
    FROM
      (SELECT RecipeClassID, RecipeClassDescription AS ClassName
       FROM Recipe_Classes AS RC
       WHERE RC.ClassName = 'Main course' OR
        RC.ClassName = 'Dessert') AS RCFiltered
    INNER JOIN Recipes AS R
      ON RCFiltered.RecipeClassID = R.RecipeClassID

If your database does not let you embed a SELECT statement inside a FROM clause, consider using a VIEW.

### Chapter 9 - OUTER JOINs

Examples:

  List all the classes and the students who have registered for those classes

Use an OUTER JOIN with a test for Null values is an alternate way of discovering the difference between two sets.
Examples:
  The rows with a Null value in the columns from the Classes table represent the difference between the set of
  all students and the set of students who have registered for a class

```SQL
SELECT column_name(s) FROM table1 FULL OUTER JOIN table2 ON table1.column_name=table2.column_name;
```


### LEFT / RIGHT OUTER JOIN

The first table you name is the one on the 'left'.
The second table you name is the one on the 'right'.

An outer join returns the unmatched rows from either the 'left' or 'right' table.

    SELECT Recipe_Classes.RecipeClassDescription, Recipes.RecipeTitle
    FROM Recipe_Classes
    LEFT OUTER JOIN Recipes
    ON Recipe_Classes.RecipeClassID = Recipes.RecipeClassID

#### JOIN WITH Nulls

List the recipe classes that do not yet have any recipes.

    SELECT Recipe_Classes.RecipeClassDescription
    FROM Recipe_Classes
    LEFT OUTER JOIN Recipes
     ON Recipe_Classes.RecipeClassID =
    Recipes.RecipeClassID
    WHERE Recipes.RecipeID IS NULL

This results in doing a difference or EXCEPT operation using a JOIN. We get back 'all the `Recipe_Classes` except
the ones that already appear in the `Recipes` table'. We created an EXCEPT!

### FULL OUTER JOIN

The __FULL OUTER JOIN__ includes all the rows from both of the tables. When no matching rows exist for
rows on the 'left' side of the JOIN, you see Null values from the result set on the 'right'.

# Chapter 11 - Subqueries

A __subquery__ is a SELECT expression that you embed inside one of the clauses of a SELECT statement to form
your final query statement.

Why would you need this? You can build complex filters that do not rely on the tables in your `FROM` clause.
Using a subquery in a `WHERE` clause is the only way to get the correct number of rows in your answer when you
want rows from one table based on the filtered contents from other related tables.

There are three different types of subqueries:

* __Row subquery__ - embedded SELECT expression that returns more than one column and only one row.
  Use to build a __raw value constructor__ (comparison)
* __Table subquery__ - embedded SELECT expression that returns one or more columns and zero to many rows
* __Scalar subquery__ - embedded SELECT expression that returns only one column and no more than one row


You can use subqueries to generate an output column or to perform a complex comparision in a `WHERE` clause.
Subqueries can often be replaced more effectively with a join, but are often used to fetch the results of a function
calculation (aggregate). E.g.

* "List vendors and a count of the products they sell to us"
* "Display products and the latest date the product was ordered"
* "List all staff members and the count of classes each teaches"

## Row Value Constructor

You can use a row subquery to build a __row value constructor__. What happens is that when you create a WHERE
clause, you build a search condition that is typically some sort of comparison of one column from one of your
tables with another column or a literal. However, you can build a search condition that compares multiple values
as a logical row with another set of values as a logical row (two row value constructors).

Example

    SELECT SKUClass, SKUNumber, ProductName
    FROM Products
    WHERE
    (SKUClass, SKUNumber)
    >= ('DSK', 9775)

This is equivalent to:

    SELECT SKUClass, SKUNumber, ProductName
    FROM Products
    WHERE
    (SKUClass > 'DSK')
    OR ((SKUClass = 'DSK')
     AND (SKUNumber >= 9775))

## Scalar Subqueries

A __scalar subquery__ lets you fetch a single column or calculated expression from another table that does not have
to be in the FROM clause of the main query. You can use this single value fetched by a scalar subquery in the
list of columns you request in a SELECT clause or you can use it as a comparison value in a WHERE clause.

So what is happening? You are substituting the subquery where you would normally enter a single column name
or expression that results in a single column. That is the reason why a scalar subquery needs to return exactly
one column and no more than one row.

This lets you pluck a single value from some other table or query to include in the output of your query.

Example:

    SELECT Orders.OrderNumber, Orders.OrderDate, Orders.ShipDate,
     (SELECT Customers.CustLastName FROM Customers
      WHERE Customers.CustomerID = Orders.CustomerID)
    FROM Orders
    WHERE Orders.ShipDate = '2017-10-03'

### Special Predicate Keywords for Subqueries

There are special predicate keywords for use in a `WHERE` clause with a subquery, including:

* Set Membership: `IN` - use the `IN` keyword in a `WHERE` clause to compare a column or expression to a list of values.
  Each value in the `IN` list could be a scalar subquery.
* Quantified Predicate: `ALL`
* Quantified Predicate: `SOME`
* Quantified Predicate: `ANY`
* Existence: `EXISTS`

#### Set Membership: `IN`

Select the recipe title from the recipes where the recipe ID is in the recipe ingredients from the inner joined
with the ingredient classes that have a recipe class of 'Seafood'.

```SQL
SELECT RecipeTitle
FROM Recipes
WHERE Recipes.RecipeID IN
  (SELECT RecipeID
   FROM Recipe_Ingredients
   WHERE Recipie_Ingredients.IngredientID IN
   (SELECT IngredientID
    FROM Ingredients
    INNER JOIN Ingredient_Classes
      ON Ingredients.IngredientClassID = Ingredient_Classes.IngredientClassID
    WHERE Ingredient_classes.IngredientClassDescription = 'Seafood'
   )
  )
```

Notice how we do not want to use a complex JOIN in the outer query since we might get duplicates (more than one row per recipe
that has more than one seafood ingredient). You can use a `DISTINCT`, but your database will have to do more work then.
`DISTINCT` to remove

#### Quantified Predicates (`ALL`, `SOME`, `ANY`)

You have to SELECT as a table subquery that returns exactly one column and zero or more rows. If the subquery
returns more than one row, the values in the row make up a list.

* `ALL` comparison must be true for all the values returned by the subquery
* `SOME` or `ANY` then the comparison only needs to be true for only one value in the list

If the subquery returns no rows, then any comparison predicate with the `ALL` keyword is true, any with the `SOME` or `ANY` keyword is false.

```
SELECT Recipes.RecipeTitle
FROM Recipes
WHERE Recipes.RecipeID IN
  (SELECT Recipe_Ingredients.RecipeID
   FROM Recipe_Ingredients
   WHERE Recipe_Ingredients.IngredientID = ANY
   (SELECT Ingredients.IngredientID
    FROM Ingredients
    WHERE Ingredients.IngredientName
    IN ('Beef', 'Garlic')
   )
  )
```

#### Existence: `EXISTS`

Use `EXISTS` to check if a related row exists in the result set returned by a subquery.

E.g. Find all the customers who ordered a bicycle

```SQL
SELECT Customers.CustomerID, Customers.CustFirstName, Customers.CustLastName
FROM Customers
WHERE EXISTS
  (SELECT *
   FROM (Orders
         INNER JOIN Order_Details
           ON Orders.OrderNumber = Order_Details.OrderNumber
        )
   INNER JOIN Products
     ON Products.ProductNumber = Order_Details.ProductNumber
   WHERE Products.CategoryID = 2
     AND Orders.CustomerID = Customers.CustomerID)
```

## Subqueries as Filters

### `HAVING`

Use a `HAVING` clause to filter out groups of information from a subquery.

# Part IV - Summarizing and Grouping Data

## Chapter 12 - Simple Totals

Aggregates have a simple syntax.

```
COUNT() <DISTINCT> expression AS alias
SUM
AVG
MAX
MIN
```

Each aggregate calculates a single value from the rows in a result set.

Except `COUNT(*)`, all aggregate functions automatically disregard Null values.

## Aggregate Functions

Standard SQL defines many functions that calculate values in a query. A subclass of functions are
__aggregate functions__, which lets you calculate a single value for a group of rows in a result set.
You can use an aggregate function to count the rows, find the largest or smallest value within a set of rows,
or calculcate the average or total of some value or expression across a result set.

### COUNT

Use __COUNT__ to determine the number of rows or the number of non-Null values in a result set.
Use `COUNT(*)` to find out how many rows are in the entire set. Note: this is the only aggregate function to count Nulls.
Use `COUNT(my_column_name)` to count the number of rows with non-Null values in that column.
Use `COUNT DISTINCT` to count only the unique values.

e.g. Select the number of students

```SQL
SELECT COUNT(*) AS CountOfStudents
FROM Students
```

e.g. Count the number of non-Null county values
```SQL
SELECT COUNT(CustCounty) AS NumberOfKnownCounties
FROM Customers
```

e.g. How many unique county names are there in the customers table
```SQL
SELECT COUNT(DISTINCT CustCounty) AS NumberOfUniqueCounties
FROM Customers
```

