---
layout: post
title: Apache Airflow
---


# {{ page.title }}

Apache Airflow is an open source job scheduler made for data pipelines.

## Setup

While the installation is pretty straightforward, getting it to work is a little more detailed:

export AIRFLOW_HOME=~/airflow
pip install apache-airflow
airflow initdb
airflow webserver -p 8080

pip install apache-airflow[devel]
pip install apache-airflow[async]
pip install apache-airflow[crypto]
pip install apache-airflow[mysql]
pip install apache-airflow[rabbitmq]

I had issues with this rabbitmq so I built rabbitmq: 
git clone git://github.com/celery/librabbitmq.git
cd rabbitmq
make install


Setup Gunicorn:
pip install gunicorn==19.3.0  # needed for python 3
Then in my bashrc add:
export PATH=$PATH:~/.local/bin

Generating an RSA public/private-key pair
openssl genrsa -out private.pem 2048

Generating a self-signed certificate
openssl req -new -x509 -key private.pem -out cacert.pem -days 1095

Then you can access the web gui with https:localhost:8080

# In your airflow.cfg under [webserver]

web_server_ssl_cert = path/to/cacert.pem
web_server_ssl_key = path/to/private.pem

### Airflow Admin Connections

In the Airflow Admin (e.g. https://localhost:8079/admin/connection/), select your connection (e.g. 'mysql_default')
and edit the info. Change the following to your connection information, e.g:

    host : my_db 
    Schema : airflow
    Login : root 
    Password : root 

Now you can run ad hoc queries! Make sure you're not really using user root on a production system. You should be
able to run a command like this:

    mysql> show tables in airflow;
    +-------------------+
    | Tables_in_airflow |
    +-------------------+
    | alembic_version   |
    | chart             |
    | connection        |
    | dag               |
    | dag_pickle        |
    | dag_run           |
    | dag_stats         |
    | import_error      |
    | job               |
    | known_event       |
    | known_event_type  |
    | log               |
    | sla_miss          |
    | slot_pool         |
    | task_fail         |
    | task_instance     |
    | users             |
    | variable          |
    | xcom              |
    +-------------------+


## Run Webserver and Scheduler

airflow webserver  # shows GUI
airflow scheduler
airflow worker # picks up tasks

## TODO: Distributed Mode

Setup distributed mode using the celery executor

## Concepts

### Dags

A __DAG__ (__Directed Acyclic Graphic)__ is a collection of all the tasks you want to run, organized by their
relationships and dependencies. A sample DAG can be three tasks: A, B, C. A DAG describes _HOW_ you want to carry
out your workflow, but not say anything about exactly _WHAT_ we actually want to do (A, B, C can be anything).
DAGS are defined in Python and placed into the `DAG_FOLDER`.

### Operators

__Operators__ describe _HOW_ to run a workflow, with `Operators` determining what actually gets done. Operators
describe a single task in a workflow, with rommon operators including:

* `BashOperator` - run a bash command
* `PythonOperator` - call python code
* `EmailOperator` - to send email
* `HTTPOperator` - send an HTTP Request
* `MySqlOperator`, `PostgresOperator`, etc. - execute a SQL command
* `SparkSqlOperator`
* `SparkSQLHook`
* `SparkSubmitOperator`
* `SparkSubmitHook`
* `Sensor` - waits for a certain time, file, database row, S3 key, etc.

### Tasks

When an operator is instantiated, it is then a __task__. The parameterized task becomes a node in the DAG.
__Task Instances__ are a specific run of a task and is a combination of a dag, a task, and a point in time.
Task Instances have a __state__, which could be 'running', 'success', 'failed', 'skipped', etc.

### XComs

__XComs__ let tasks exchange messages, allowing you to have more control and share state across systems.
An XCom is defined by a key, value, and timestamp, as well as tracks attributes like the task/DAG that created the
XCom. XComs can be "pushed" (sent) or "pulled" (received).

#### XComs Pushing and Pulling

Tasks can push XComs at any time by calling the `xcom_push()` method.
Tasks can pull XComs at any time by calling the `xcom_pull()` method, optionally applying filters based on the
criteria like `key`, source `task_ids`, and source `dag_id`.

#### Variables

__Variables__ are a generic way to store and retrieve arbitrary content or settings as a simple key value store.
Variables can be listed, created, updated, and deleted from the UI, code, or CLI. Note that under the hood, a Variable
is a SQLAlchemy Model.

    from airflow.models import Variable
    foo = Variable.get("foo")
    bar = Variable.get("bar", deserialize_json=True)

### SubDAGs

__SubDAGs__ are used for repeating patterns. Defining a function that returns a DAG object is a nice design pattern
when using Airflow. Airflow uses the __stage-check-exchange__ pattern when loading data. Data is staged in a temporary
table, after which data quality checks are performed against that table. Once all the checks pass, the partition
is moved into the production table.

### SLAs

__Service Level Agreements__ is the time that a task or DAG should have succeeded (by setting a task level as 
a `timedelta`).  If one or more instances haven't succeeded by that time, you can send out an alert email.

### Trigger Rules

The normal workflow is to trigger tasks when all their directly upstream tasks have succeeded. However, an
operator can set a `trigger_rule` argument and set this to say:

* `all_success` (default)
* `all_failed`
* `all_done` - all parents are done with their execution
* `one_failed` - fires as soon as at least one parent has failed (does not wait for all parents to be done)
* `one_success` - fires as soon as at least one parent succeeds (does not wait for all parents to be done)

### Latest Run Only

Standard workflows involve running jobs for a particular date/time range. Some workflows perform tasks that are
independent of last run time, but need to be run on a schedule, basically like a cron job; for these situations, use
the __LatestOnlyOperator__ to skip tasks that are not being run during the most recent scheduled run for a DAG.

### Zombies and Undeads

__Zombies__ are tasks without a heartbeat (emitted by the job periodically) and a `running` status in the database.
They occur when a worker node can't reach the database, when Airflow processes are killed externally, or when
a node gets rebooted. Zombies are killed periodically by the scheduler's process.

__Undead__ processes have a process and a matching heartbeat, but Airflow isn't aware of this task as `running` in
the database. This mismatch occurs as the state of the database is altered (e.g. from deleting rows in the 'Task 
Instances' view in the UI. During the heartbeat routine, tasks verify their state and terminate themselves if they
are in this 'undead' state.

