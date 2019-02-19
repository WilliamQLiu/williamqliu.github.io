---
layout: post
title: SQL Queries for Mere Mortals 
---


# {{ page.title }}

'SQL Queries for Mere Mortals' shows you how to write queries using a variety of databases, 
including SQL Server, MySQL, and PostgreSQL

## Overview

The book is broken into six sections:

1. How Databases are mathematical models
2. SQL Basics (e.g. select, where, order by)
3. Working with multiple tables (e.g. joins, subqueries)
4. Summarizing and Grouping Data (e.g. group by, having)
5. Modifying data (e.g. update, insert, delete)
6. Complex problems (e.g. not, and, case)

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

### ERD 

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

There are two types of databases:

* __Operational Databases__ - used to collect, modify, and maintain _dynamic_ data on a day-to-day basis
* __Analytical Databases__ - stores and tracks historical and time-dependent data that is static (data is not really modified)

## Anatomy of a Relational Database

Data in a relational database is stored in __relations__, which appear as __tables__. 
Each relation is made up of __tuples__ (records of rows) and __attributes__ (fields or columns).

### Tables

Tables are the main structures in the database. Each table represents a single, specific object.
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
