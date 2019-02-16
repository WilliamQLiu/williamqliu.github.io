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


