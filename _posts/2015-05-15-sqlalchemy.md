---
layout: post
title: SQLAlchemy
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Python DBAPI](#dbapi)
*  [SQLAlchemy Engine](#engine)
    - [execute()](#engineexecute)
    - [result object](#resultobject)
*  [SQLAlchemy Schema](#sqlalchemyschema)
    - [MetaData](#metadata)
    - [MetaData Types](#metadatatypes)
    - [DDL](#ddl)
    - [Database Abstraction, Reflection, Introspection](#abstractionreflectionintrospection)
*  [SQLAlchemy SQL Expression Language](#sqlexpressionlanguage)
*  [SQLAlchemy Object Relational Mapper (ORM)](#orm)

##<a id="summary">Summary</a>

__SQLAlchemy__ is a set of tools for working with databases using Python.  There are many layers to this system and you can pick which one(s) you want to use.  As for the layers, going from lowest to highest level, we have:

*  Python DBAPI; lowest level of using Python to interact with the database
*  SQLAlchemy Engine; this gives SQLAlchemy and the DBAPI the ability to interact
*  SQLAlchemy Schema; this lets you see the database metadata
*  SQL Expression Language - converts SQL statements to Python objects
*  SQLAlchemy Object Relational Mapper (ORM) - database is fully abstracted

##<a id="dbapi">Python DBAPI</a>

Python has numerous PEPs, which are basically features to Python (whether it is a style guide or wsgi interface).  PEP-0249 is the Python Database API and is a de-facto system for providing Python database interfaces.  This PEP makes a lot of suggestions of what to do, but does not say you should do this.  An example DBAPI is psycopg2, an adapter for Postgresql databases.

    import psycopg2
    connection = psycopg2.connect("myusername", "mypassword", "test")
    
    cursor = connection.cursor()
    cursor.execute(
            "select emp_id, emp_name from employee"
            "where emp_id=%(emp_id)s",
            {'emp_id':5})  # this substitues parameter
    emp_name = cursor.fetchone()[1]
    cursor.close()
    
    cursor = connection.cursor()
    cursor.execute(
            "insert into employee_of_month "
            "(emp_name) values (%(emp_name)s)",  # this substitues parameter
            {"emp_name": emp_name})
    cursor.close()
    
    connection.commit()

A __bound parameter__ is where you can bind a Python variable (e.g. `5`, `emp_name`) to a corresponding SQL statement parameter (e.g. `emp_id`, `emp_name`).  Be careful because this is where SQL injections can happen.

By default, the DBAPI does not autocommit.  That means you have to explicitly say to commit.

##<a id="engine">SQLAlchemy Engine</a>

One layer above the Python DBAPI is the SQLAlchemy Engine.  The SQLAlchemy Engine is the interface between SQLAlchemy and the Python DBAPI.  Here is how to setup an engine:

    import sqlalchemy
    
    engine = sqlalchemy.create_engine('sqlite:///:memory:', echo=True)
    
    # You can specify different engine dialect and drivers
    #engine = create_engine("postgresql+psycopg2:////username:password@localhost/test")

####<a id="engineexecute">execute()</a>

The most basic and useful method is `execute()`, which takes a string and goes through to the DBAPI.  This uses the `:emp_id` as the __bound parameter__ (notice the pattern is `:`variable_name.

    result = engine.execute(
            "select emp_id, emp_name from "
            "employee where emp_id=:emp_id",
            emp_id=3)
    
    result = engine.execute(
            "insert into users (name, fullname) "
            "values (:name, :fullname)',
            (name='will', fullname='William Liu'))

At this level, its as direct a line to the DBAPI as you can get.

####<a id="resultobject">Result Object</a>

Running the above `execute()` returns a __result object__ (more precisely, a ResultProxy object).  This result object is like a DBAPI cursor and is basically a Python dict, but with more additional features like methods and you can iterate over it.  You can access fields like a dictionary (i.e. key-value pairs), use methods like `fetchone()`, `fetchall()`, and iterate through the object.

    result = engine.execute(
            "select users from"
            "employee where users=:users",
            users='Will')
    
    row = result.fetchone()
    print row.keys()
    #[u'emp_id', u'emp_name']
    
    print result.fetchall()
    #[((1, u'ed'), (2, u'jack'), (3, u'fred'))]
    
    for row in result:
        print row #Sample Data: (1, u'jack', u'Jack Jones')

When you get all the rows, it will automatically close the connection; otherwise you can manually close with: `result.close()`.

##<a id="schema">SQLAlchemy Schema</a>

The SQLAlchemy schema goes into the metadata, ddls, and database abstraction, reflection, and introspection.

####<a id="metadata">Metadata</a>

The next layer is the __metadata__, which describes the structure of the database (i.e. the tables, columns, constraints) in terms of Python data structures.  Metadata is data about the data.  This metadata is the basis for mapping SQL to Python objects.

    from sqlAlchemy import MetaData, Table, Column, Integer, String, Numeric, DateTime, Enum, ForeignKey
    
    metadata = MetaData()
    
    user_table = Table('users', metadata,
                    Column('id', Integer, primary_key=True),
                    Column('name', String(),
                    Column('fullname', String()))
                    )
    
    print user_table.name  #user
    
    print user_table.c.name  # shows columns
    #Column('name', String(), table=<users>)
    
    print user_table.columns.name  # can say columns instead of c
    #Column('name', String(), table=<users>)
    
    print user_table.columns.keys()
    #['id', 'name', 'fullname']
    
    print user_table.columns.fullname.name
    #'fullname'
    
    print user_table.columns.fullname.type
    #String()
    
    print user_table.primary_key
    #PrimaryKeyConstraint(Column('id', Integer(), table=<user>, primary_key=True, nullable=False))

####<a id="metadatatypes">MetaData Type Conversions</a>

|Python Type|Database Type        |
|-----------|---------------------|
|Integer()  |INT                  |
|String()   |VARCHAR              |
|Unicode()  |VARCHAR,NVARCHAR     |
|Boolean()  |BOOLEAN,INT,TINYINT  |
|DateTime() |DATETIME,TIMESTAMP   |
|Float()    |floating point values|
|Precision()|precision            |

####<a id="ddl">Data Definition Language (DDL)</a>

Metadata was designed to look like a __Data Definition Language__ (aka __Data Description Language (DDL)__); this defines data structures (i.e. is the database schema).  Notice how SQLAlchemy makes the output look like a `CREATE TABLE` in SQL.  We can use our metadata from earlier to generate a schema as well as to be generated from a schema.

    ###Using SQLAlchemy, we generate a SQL schema
    fancy_table = Table('fancy', metadata,
                    Column('mykey', String(50), primary_key=True),
                    Column('mytimestamp', DateTime),
                    Column('amount', Numeric(10, 2)),
                    Column('type', Enum('a', 'b', 'c'))
                )
    fancy_table.create(engine)
    
    #The above Python code generates the following SQL code:
    CREATE TABLE fancy(
            mykey VARCHAR(50) NOT NULL,
            mytimestamp DATETIME,
            amount NUMERIC(10,2),
            type VARCHAR(1),
            PRIMARY KEY (mykey),
            CHECK (type IN ('a', 'b', 'c'))
        )

We can use metadata to link tables too.  You can do even more advanced things like creating composite foreign keys, which will create a ForeignKeyConstraint.

    #We can link to different tables we create too
    address_table = Table('address', metadata,
                        Columns('id', Integer, primary_key=True),
                        Column('email_address', String(100), nullable=False),
                        Column('user_id', Integer, ForeignKey('user.id'))
                    )
    address_table.create(engine)
    
    #The above Python code generates the following SQL code:
    CREATE TABLE address(
            id INTEGER NOT NULL,
            email_address VARCHAR(100) NOT NULL,
            user_id INTEGER,
            PRIMARY_KEY (id),
            FOREIGN KEY(user_id) REFERENCES user (id)
        )

####<a id="abstractionreflectionintrospection">Database Abstraction, Reflection, Introspection</a>

__Database Abstraction__ is an application programming interface (API) that unifies the communication between a computer application (e.g. using Python) and databases (e.g. MySQL, PostgreSQL)

__Database Reflection__ means getting metadata from the database table and pulling that into SQLAlchemy.  This is useful if you need to migrate from one database to another.

     metadata = MetaData()
     user_reflected = Table('user', metadata, autoload=True, autoload_with=engine)
     print user_reflected
     #Table('user', MetaData(bind=None), Column(u'id', INTEGER(), table=<user>, primary_key=True, nullable=False,))

__Database Introspection__ means looking at the database structure in even more detail.

    from sqlalchemy import inspect
    
    inspector = inspect(engine)
    
    inspector.get_table_names()
    #SELECT name FROM (SELECT * FROM sqlite_master UNION ALL
    #                   SELECT * FROM sqlite_temp_master)
    #                   WHERE type='table' ORDER BY name
    #[u'address', u'fancy', u'network', u'published', u'story', u'user']
    
    inspector.get_columns('address_table')  # returns as a dict
    #PRAGMA table_info('address_table')
    #[{'primary_key':1, 'nullable':False, 'default':None, 'autoincrement':True, 'type':INTEGER(), 'name': u'id'}, {'primary_key':0, 'nullable':False, 'default':None, 'autoincrement':True, 'type':VARCHAR(length=100), 'name':u'email_address'}, {'primary_key':0, 'nullable':True, 'default':None, 'autoincrement':True, 'type':INTEGER(), 'name':u'user_id'}]

##<a id="sqlexpressionlanguage">SQLAlchemy SQL Expression Language</a>

The SQL Expression Language builds on top of the Metadata above.  The idea is that SQLAlchemy has built Python objects that represent individual SQL strings (statements) that we would send to the database.  This is done by overriding Python callable operators (e.g. `__eq__()`).  These are Python objects until we `execute` them (or `print` them).

##<a id="orm">SQLAlchemy Object Relational Mapper (ORM)</a>

The SQLAlchemy Object Relational Mapper is another layer on top of the SQLAlchemy SQL Expression Language.  At this level, the database is abstracted so that it has more reliable compatibility with a greater range of database systems.

