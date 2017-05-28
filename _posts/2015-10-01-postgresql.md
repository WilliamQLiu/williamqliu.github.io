---
layout: post
title: PostgreSQL
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Basic Commands](#basiccommands)
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


##<a id="summary">Summary</a>

__PostgreSQL__ is an open source relational database.  

##<a id="basiccommands">Basic Commands</a>

####<a id="login">Login</a>

In the command line, just enter `psql` to see your options

    $psql --help

####<a id="listdb">List Databases</a>

In the command line, to see what databases are available for access, do:

    $psql --list


####<a id="createdb">Create Database</a>

Create a database called `mydb`

    createdb mydb

####<a id="accessdb">Access Database</a>

Connect to a database (e.g. db is named mydb)

    psql mydb [optional user name]

####<a id="listdbinside">Lists all Databases</a>

Now that you're in psql, you can also list all databases

     \list

####<a id="seeroles">See Roles</a>

Prints out a list of roles and attributes (e.g. name, superuser, member of)

     \du

####<a id="changepw">Change Database Password</a>

Change the password of a database

    \password mydb

####<a id="conninfo">View current connection info</a>

View current connection information

    \conninfo

####<a id="dt">View Database Tables</a>

View database tables and their owners

    \dt

####<a id="viewall">View Tables, Views, and Sequences</a>

View all of the tables, views, and sequences

    \z

####<a id="switchdb">Switch Databases</a>

To connect to another database (e.g. db 'jobwaffle_prod')

    \connect jobwaffle_prod

####<a id="queries">Queries</a>

While in the interactive session, remember to terminate queries with a __;__

    mydb=# SELECT * FROM mytable;

####<a id="exit">Exit</a>

Exits out of the PostgreSQL command prompt

    \q

####<a id="sqlhelp">SQL Help</a>

To get help for SQL Commands

    \h

####<a id="psqlhelp">PSQL Help</a>

To get help for psql commands

    \?
