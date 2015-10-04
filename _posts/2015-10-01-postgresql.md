---
layout: post
title: PostgreSQL
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Basic Commands](#basiccommands)

##<a id="summary">Summary</a>

__PostgreSQL__ is an open source relational database.  

##<a id="basiccommands">Basic Commands</a>

####<a id="createdb">Create Database</a>

Create a database called `mydb`

    createdb mydb

####<a id="accessdb">Access Database</a>

Connect to a database (e.g. db is named mydb)

    psql mydb [optional user name]

####<a id="listdb">Lists all Databases</a>

Lists all databases

     \list

####<a id="printroles">Print Roles</a>

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
