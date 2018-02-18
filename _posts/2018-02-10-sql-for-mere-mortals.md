---
layout: post
title: SQL Queries for Mere Mortals 
---


# {{ page.title }}

'SQL Queries for Mere Mortals' shows you how to write queries using a variety of databases, 
including SQL Server, MySQL, and PostgreSQL

## Sample Databases

* Sales Orders - manage orders for a store that sells bicycles
* Entertainment Agency - manage event bookings for entertainers, agents, and customers
* School Scheduling - register students at a school
* Bowling League - tracks bowling teams, members, matches they played
* Recipes - save and manage recipes

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


