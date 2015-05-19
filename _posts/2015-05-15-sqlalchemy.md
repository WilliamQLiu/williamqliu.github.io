---
layout: post
title: SQLAlchemy
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Python DBAPI](#dbapi)
*  [SQLAlchemy Core](#sqlalchemycore)
    - [SQLAlchemy Engine](#engine)
    - [SQLAlchemy SQL Expression Language](#sqlexpressionlanguage)
        + [insert()](#sqlalchemyexpinsert)
        + [select()](#sqlalchemyexpselect)
        + [operators: where(), like()](#sqlalchemyexpoperators)
        + [conjunctions: and_, or_, not_](#sqlalchemyexpconjunctions)
        + [execute()](#sqlalchemyexpexecute)
        + [result object](#resultobject)
    -  [SQLAlchemy Schema](#sqlalchemyschema)
        + [MetaData](#metadata)
        + [MetaData Types](#metadatatypes)
        + [DDL](#ddl)
        + [Database Abstraction, Reflection, Introspection](#abstractionreflectionintrospection)
*  [SQLAlchemy Object Relational Mapper (ORM)](#orm)
    - [Types of ORMs](#ormtypes)
        + [Active Record](#ormactiverecord)
        + [Data Mapper](#ormdatamapper)
    - [ORM Configuration Patterns](#ormconfigurationpatterns)
        + [All-at-once (aka Declarative)](#ormdeclarative)
        + [Separate](#ormseparate)
        + [SQLAlchemy ORM Pattern](#ormsqlalchemypattern)
    - [SQLAlchemy ORM Usage](#ormsqlalchemyuse)
        + [](#)

##<a id="summary">Summary</a>

__SQLAlchemy__ is a set of tools for working with databases using Python.  There are many layers to this system and you can pick which one(s) you want to use.  As for the layers, going from lowest to highest level, we have:

*  Python DBAPI; lowest level of using Python to interact with the database
*  SQLAlchemy Core
    - SQLAlchemy Engine; this gives SQLAlchemy and the DBAPI the ability to interact
    - SQLAlchemy Schema; this lets you see the database metadata
    - SQL Expression Language - slight abstraction by converting SQL statements to Python objects and vice versa.  This approach is more like the literal schema and SQL expressions.
*  SQLAlchemy Object Relational Mapper (ORM) - database is fully abstracted, we work with Python classes instead of SQL for everything including mapping tables and relationships.

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

##<a id-"sqlalchemycore">SQLAlchemy Core</a>

One layer above the Python DBAPI is the SQLAlchemy Core.  The Core is made up of the Engine (used for connecting to Python DBAPI), the SQL Expression Language (for running queries), and the Schema (for inspecting the database or the SQLAlchemy objects).

###<a id="engine">SQLAlchemy Engine</a>

SQLAlchemy Engine is the piece that interfaces between SQLAlchemy and the Python DBAPI.  Here is how to setup an engine:

    import sqlalchemy
    
    engine = sqlalchemy.create_engine('sqlite:///:memory:', echo=True)
    
    # You can specify different engine dialect and drivers
    #engine = create_engine("postgresql+psycopg2:////username:password@localhost/test")

###<a id="sqlexpressionlanguage">SQLAlchemy SQL Expression Language</a>

The SQL Expression Language builds on top of the Metadata above.  The idea is that SQLAlchemy has built Python objects that represent individual SQL strings (statements) that we would send to the database.  It works behind the scenes by overriding Python callable operators (e.g. `__eq__()`).  These are Python objects until we `execute` them (or `print` them), which then gets created into the SQL strings.

An example setup:

    from sqlalchemy import create_engine, Table, Column, Integer, String, MetaData, ForeignKey
    
    engine = create_engine('sqlite:///:memory:', echo=True)
    metadata = MetaData()
    
    users = Table('users', metadata,
            Column('id', Integer, primary_key=True),
            Column('name', String(50),
            Column('fullname', String(50)))
    addresses = Table('addresses', metadata,
            Column('id', Integer, primary_key=True),
            Column('user_id', None, ForeignKey('users.id')),
            Column('email_address', String(50), nullable=False))
    
    metadata.create_all(engine)  # Check presence of each table before creating
    


####<a id="sqlalchemyexpinsert">insert()</a>

We look at a Table and automatically create an INSERT statement.  By default, INSERT lists every column name.  If you want to specify the column names, add in the `values()` method.

    ins = users.insert()
    print str(ins) # See what the INSERT statement creates
    #'INSERT INTO users (id, name, fullname) VALUES (:id, :name, :fullname)'
    
    ins = users.insert().values(name='jack', fullname='Jack Jones')
    #'INSERT INTO users (name, fullname) VALUES (:name, :fullname)'
    
    ins.compile().params
    #{'fullname': 'Jack Jones', 'name': 'jack'}
    
    result = conn.execute(ins)
    # INSERT INTO users (name, fullname) VALUES (?, ?) ('jack', 'Jack Jones')
    # COMMIT

####<a id="sqlalchemyexpselect">select()</a>

We look at data in an existing database using the SELECT statement.

    from sqlalchemy.sql import select
    
    # SELECT all columns
    s = select([users])
    result = conn.execute(s)
    # SELECT users.id, users.name, users.fullname FROM users
    # COMMIT
    
    # SELECT specific columns
    s = select([users.c.name, users.c.fullname])

We have a few different ways we can access the data.  The simplest is to just iterate and return rows as a tuple-like object.

    for row in result:
        print row
    
    #(1, u'jack', u'Jack Jones')
    #(2, u'wendy', u'Wendy Williams')

You can also use methods like `fetchone()` or `fetchall()` to return results.
    
    result = conn.execute(s)
    row = result.fetchone()
    print "name:", row['name'], "| fullname:", row['fullname']
    
    # name: jack | fullname: Jack Jones

You can access results through a dictionary using the string names of the columns.

    result = conn.execute(s)
    row = result.fetchone()
    print "name:", row['name'], "| fullname:", row['fullname']
    
    # name: jack | fullname: Jack Jones

You can access results with integer indexes.

    row = result.fetchone()
    print "name:", row[1], "| fullname:", row[2]
    
    # name: wendy | fullname: Wendy Williams

You can also access results using the `Column` objects directly as keys.

    for row in conn.execute(s):
        print "name:", row[users.c.name], "| fullname:", row[users.c.fullname]
    
    # name: jack | fullname: Jack Jones
    # name: wendy | fullname: Wendy Williams

####<a id="sqlalchemyexpoperators">Operators: where(), like()</a>

We can add a WHERE clause to a statement by appending it to our method.  For example, here we are selecting two tables.  Note that in our WHERE clause, we are using the Python `==`, which normally returns True or False.  We can also add the LIKE clause to a statement.

    s = select([users, addresses]).where(users.c.id == addresses.c.user_id)
    
    s = select(addresses).where(addresses.c.email_address.like('%@gmail.com'))

####<a id="sqlalchemyexpconjunctions">Conjunctions: and_, or_, not_</a>

We can add AND, OR, NOT similar to a SQL statement.

    from sqlalchemy.sql import and_, or_, not_
    
    s = select([users.c.fullname + ", " 
        + addresses.c.email_address).label('title').\
        where(users.c.id == addresses.c.user_id).\
        where(
            or_(
                addresses.c.email_address.like('%@aol.com'),
                addresses.c.email_address.like('%@msn.com')
                )
            )
        ])
    #

####<a id="sqlalchemyexpexecute">execute()</a>

The most basic and useful method is `execute()`, which takes a string (like the above INSERT statement) and passes this through to the DBAPI.  This makes use of the `:emp_id` as the __bound parameter__ (notice the pattern is `:`variable_name.

    result = engine.execute(
            "select emp_id, emp_name from "
            "employee where emp_id=:emp_id",
            emp_id=3)
    
    result = engine.execute(
            "insert into users (name, fullname) "
            "values (:name, :fullname)',
            (name='will', fullname='William Liu'))

At this level, we are using the SQL Expression Language; its as direct a line to the DBAPI as you can get.

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

When you get all the rows, it will automatically close the connection; otherwise you can manually close with: `result.close()`, which is recommended to be explicit.

###<a id="schema">SQLAlchemy Schema</a>

The SQLAlchemy schema goes into the metadata, ddls, and database abstraction, reflection, and introspection.

####<a id="metadata">Metadata</a>

The __metadata__ describes the structure of the database (i.e. the tables, columns, constraints) in terms of Python data structures.  Metadata is data about the data.  This metadata is the basis for mapping SQL to Python objects.

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

__Database Reflection__ means getting metadata from the database table and pulling that into SQLAlchemy/Python objects.  Behind the scenes, a column is represented as a `Column` object, which is associated with a `Table` object.  This is useful if you need to migrate from one database to another.

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

##<a id="orm">SQLAlchemy Object Relational Mapper (ORM)</a>

The SQLAlchemy Object Relational Mapper is another layer on top of the SQLAlchemy SQL Expression Language.  At this level, the database is abstracted so that it has more reliable compatibility with a greater range of database systems.  The our ORM we have:

*  __domain model__ is the set of object oriented classes (i.e. our Python objects).  We have our entire Application and individual __domain objects__ (e.g. our Columns, Tables)
*  __database__ is all of our database tables and rows.

In general, ORMs translate between the domain model (e.g. our Python objects) and the Database data (e.g. our Tables and Row).  This might include:

*  Represent the basic composititons of the objects (e.g. one-to-many, many-to-one tables using foreign key associations)
*  Provides a mean of querying the database in terms of the domain model structure
*  Can handle data sharding (i.e. store a domain model across multiple schemas or databases)
*  Provide patterns for data validation (i.e. checks that data going in is what you want) and coercion (i.e. is this the right data type)

###<a id="ormtypes">Types of ORMs</a>

There are two types of approaches to designing ORMs, __active record__ and __data mapper__.

####<a id="ormactiverecord">Active Record</a>

__Active Record__ ORM means the domain objects handle their own persistence.  Basically this means if we want to make a new user, we declare a new User object and we do an INSERT statement right when it does a `save()`.

    user_record = User(name="ed", fullname="Ed Jones")
    user_record.save()
    
    user_record = User.query(name="ed").fetch()
    user_record.fullname = "Edward Jones"
    user_record.save()

####<a id="ormdatamapper">Data Mapper</a>

__Data Mapper__ tries to keep the details of persistence separate from the object being persisted.  Basically this means that if we want to make a new user, we first create an object that talks to the database (the dbsession).  We then create the new user by adding to the object.  We then do an INSERT statement with a `commit()`.  This normally means a little more configuration.

    dbsession = start_session()
    
    user_record = User(name="ed", fullname="Ed Jones")
    dbsession.add(user_record)
    user_record = dbsession.query(User).filter(name="ed").first()
    user_record.fullname = "Edward Jones"
    
    dbsession.commit()

###<a id="ormconfigurationpatterns">ORM Configuration Patterns</a>

ORMs have many configuration patterns, most use __all-at-once__ (aka __declarative__) and others are __separate__ (i.e. keep the declaration of domain model and table metadata separate).

####<a id="ormdeclarative">All-at-once (aka Declarative)</a>

__All-at-once__ (aka __Declarative__ ) style is where the class and table information is together.  This is like the Django ORM and similar to SQLAlchemy.

    # a fake declarative system
    class User(ORMObject):
        tablename = 'user'
        
        name = String(length=50)
        fullname = String(length=100)
    
    class Address(ORMObject):
        tablename = 'address'
    
        email_address = String(length=100)
        user = many_to_one('User')

####<a id="ormseparate">Separate</a>

__Separate__ - the other option is to keep Model and Table metadata separate.  This is not seen very often.

    # class that is declared without any awareness of database
    class User(object):
        def __init__(self, name, username):
            self.name = name
            self.username = username
    
    # elsewhere, it's associated with a database table
    mapper(
        User,
        Table("user", metadata,
            Column("name", String(50)),
            Column("fullname", String(100)
            )
        )
    )

####<a id="ormsqlalchemypattern">SQLAlchemy ORM Pattern</a>

SQLAlchemy ORM is essentially a __data mapper__ style ORM that has a __declarative__ configuration.  The SQLAlchemy ORM is slightly different than the SQLAlchemy SQL Expression Language.  With the ORM, we have:

*  __Unit of Work__ - objects maintained by a system that tracks changes over the course of a transaction and flushes pending changes periodically.  Basically, just give me your objects and we will handle it automatically (e.g. what order to create Tables that are linked to each other).
*  __Identity Map__ - objects are tracked by their primary key within the unit of work.  These are _unique_ on that primary key identity.
*  __Lazy Loading__ - some attributes of an object may emit additional SQL queries when they are accessed.  You do not access this additional information unless you explicitly say to load it.  This means if you access a user table, it will not load other table data.  This is more efficient if you have all the data you want.
*  __Eager Loading__ - multiple tables are queried at once in order to load related objects and collections.  E.g. give me this user table, but also give me the related objects and collections.  This saves a trip to the database, but grabs a lot more data.

####<a id="ormsqlalchemyuse">SQLAlchemy Use</a>

Basic Example

    from sqlalchemy import create_engine, Column, Integer, String
    from sqlalchemy.ext.declarative import declarative_base
    
    engine = create_engine('sqlite:///:memory:', echo=True)
    base = declarative_base()
    
    class User(Base):
        __tablename__ = 'users'
        
        id = Column(Integer, primary_key=True)
        name = Column(String)
        fullname = Column(String)
        password = Column(String)
        
        def __repr__(self):
            return "<User(name='%s', fullname='%s', password='%s')>" % \
            (self.name, self.fullname, self.password)
