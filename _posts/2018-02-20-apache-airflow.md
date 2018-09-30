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

## Docker

A good Docker container setup is here:

https://github.com/puckel/docker-airflo://github.com/puckel/docker-airflow

## Resources

* https://medium.com/@rchang/a-beginners-guide-to-data-engineering-part-i-4227c5c457d7
* https://github.com/jghoman/awesome-apache-airflow

### RabbitMQ

I had issues with this rabbitmq so I built rabbitmq: 

    git clone git://github.com/celery/librabbitmq.git
    cd rabbitmq
    make install

Setup Gunicorn:

    pip install gunicorn==19.3.0  # needed for python 3
    Then in my bashrc add:
    export PATH=$PATH:~/.local/bin

Generating an RSA public/private-key pair

### Self Sign Certificates

    openssl genrsa -out private.pem 2048

Generating a self-signed certificate

    openssl req -new -x509 -key private.pem -out cacert.pem -days 1095

Then you can access the web gui with `https:localhost:8080`

In your airflow.cfg under [webserver]

    web_server_ssl_cert = path/to/cacert.pem
    web_server_ssl_key = path/to/private.pem

## Important Configs

Under `airflow.cfg`, there's a few important settings, including:

* `parallelism` - the amount of parallelism as a setting to the executor. This defines the max number of task
  instances that should run simultaneously on this airflow installation. Basically, if I have two computers running 
  as airflow workers, this is the "maximum active tasks"
* `dag_concurrency` - the task concurrency per worker - think of it as the "max active tasks per worker".
* `non_pooled_task_slot_count` - when not using pools, the size of the 'default pool'.
* `max_active_runs_per_dag` - the maximum number of active DAG runs per DAG. E.g. Try to run 16 active DAGs instances
  at the same time

### Airflow Admin Connections

In the Airflow Admin (e.g. `https://localhost:8079/admin/connection/`), select your connection (e.g. 'mysql_default')
(or define your own), then create or edit this information. E.g. change the following to your connection information:

    host : my_db 
    Schema : airflow
    Login : root 
    Password : root 

You can also add an environment variable with this connection info if you want to start it up by default:

    ENV AIRFLOW_CONN_MYSQL_DEFAULT="mysql://root:root@my_db:3306/airflow"

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

## Distributed Mode

Setup distributed mode using the celery executor

With Distributed Mode you need a backend like RabbitMQ. We'll modify the `airflow.cfg` file to:

    executor = CeleryExectuor # instead of SequentialExecutor

    celery_result_backend = db+mysql://root:root@my_db:3306/airflow

    # By default, the `airflow.cfg` specifies a default celery queue of `default_queue`
    # Make sure to specify what `queue` to use (from BaseOperator)

    # Remember to kick off some workers then with:
    airflow worker

    # To monitor your workers, check out 'Celery Flower' from
    airflow flower

## Systemd

Airflow can integrate with __systemd__ based systems, allowing systemd to watch restarting a daemon on failure.
In the `scripts/systemd` directory, there's unit files that you can copy over to `/usr/lib/systemd/system`.
Airflow tasks will run under user `airflow:airflow`.

Environment configuration is picked up from `/etc/sysconfig/airflow`. Make sure to specify `SCHEDULER_RUNS` and
`AIRFLOW_HOME` and `AIRFLOW_CONFIG`.

## Concepts

### DAGs 

A __DAG__ (__Directed Acyclic Graphic)__ is a collection of all the tasks you want to run, organized by their
relationships and dependencies. A sample DAG can be three tasks: A, B, C. A DAG describes _HOW_ you want to carry
out your workflow, but not say anything about exactly _WHAT_ we actually want to do (A, B, C can be anything).
DAGS are defined in Python and placed into the `DAG_FOLDER`.

So I think of this as a __DAG__ can only go down the line in tasks. The tasks might split so that some run in
parallel, but they can't circle back to another task (because that'll cause an infinite loop for that task),
which will only cause more issues when that task scheduled to run at regular intervals (say every 15 minutes, it
creates another infinite loop). So yeah, that's why DAGs just go down the line in tasks instead of circling back.

Important Note: For DAGs, these should be atomic, idempotent items.

#### DAGs and default arguments

When we create a task, we can define a dictionary of default parameters that we can use. These parameters depend
on the type of Operator you're choosing. Remember that all our tasks are nested into a DAG object. Here we create the
DAG and pass in a default argument dictionary.

    from datetime import datetime, timedelta
    
    # Define a dictionary of default arguments
    default_args = {
        'owner': 'airflow',
        'depends_on_past': False,
        'start_date': datetime(2015, 6, 1),
        'email': ['airflow@example.com'],
        'email_on_failure': False,
        'email_on_retry': False,
        'retries': 1,
        'retry_delay': timedelta(minutes=5),
        # 'queue': 'bash_queue',
        # 'pool': 'backfill',
        # 'priority_weight': 10,
        # 'end_date': datetime(2016, 1, 1),
    }

    # Instantiate a DAG
    dag = DAG(
        'tutorial', default_args=default_args, schedule_interval=timedelta(1))

#### DAG `schedule_interval`

You can run a DAG on a schedule using the `schedule_interval`, which happens to take in CRON like times.

    dag = DAG(dag_id='dag_do_stuff',
          default_args=default_args,
          schedule_interval='*/5 * * * *',  # Run every 5 minutes
          dagrun_timeout=timedelta(seconds=120))

You can also pass in presents into the `schedule_interval`, including:

* `None` - don't schedule, use for "externally triggered" DAGs
* `@once` - schedule once and only once
* `@hourly` - run once an hour at the beginning of the hour
* `@daily` - run once a day at midnight

etc.

#### How DAG schedule runs

The airflow scheduler monitors all tasks and all DAGs, triggering the task instances whose dependencies have been met.

If you run a DAG on a `schedule_interval` of one day, then the run stamped `2016-01-01` will trigger after 
`2016-01-01T23:59`. This means that the job instance is started once the period it covers has ended.

It's very important to note: The scheduler runs your job one __schedule_interval__ AFTER the start date,
at the END of the period.

#### Dag Views

You can view DAGs through the GUI, with the following views:

* `Graph View` shows you how your DAG runs from left to right. I found this the most intuitive for order of operations.
* `Tree View` is not as intuitive, but shows you the run times and whether those were successful. It's not as
  intuitive to me because your first task starts on the bottom (like a tree root) and goes upwards for downstream
  tasks. Basically your first tasks start on the bottom and your last tasks appear on top.
* `Gantt View` shows each task and how long it took to complete

### Operators 

While __DAGS__ describe _HOW_ to run a workflow, __Operators__ determining what actually gets done. 
Operators describe a single task in a workflow, with common operators including: 

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
* `SqlSensor` - runs a sql statement until a criteria is met; it will keep trying while sql returns no row, or if
  the first cell is one of the following values: `(0, '0', '')`
* `S3FileTransferOperator`
* `ShortCircuitOperator` - derived from the Python operator, the workflow continues only if a condition is met,
  otherwise the workflow "short circuits" and downstream tasks are skipped. Any downstream tasks are marked as
  a state of "skipped". If the condition is True, downstream tasks proceed as normal. Useful if you have heavy tasks
  that you want to skip to save compute resources

#### When to split out multiple operators

Operators are usually atomic; they should be able to stand on their own without sharing resources with any other
operators. At scale, two operators may run on completely different machines so you need to be careful on how to 
share information. If two operators need to share information, like a file or some data, consider combining them
into a single operator. If it absolutely can't be avoided at all, then consider using operator cross-communication
called __XCom__.

### Tasks

When an __operator__ is instantiated, it is then a __task__. The parameterized task becomes a node in the DAG.
__Task Instances__ are a specific run of a task and is a combination of a dag, a task, and a point in time.
Task Instances have a __state__, which could be 'running', 'success', 'failed', 'skipped', etc.

The arguments from an operator depend on the type of Operator. (e.g. `bash_command` comes from BashOperator and 
`retries` comes from BaseOperator)

Example tasks might look like:

    t1 = BashOperator(
        task_id='print_date',
        bash_command='date',
        dag=dag)

    t2 = BashOperator(
        task_id='sleep',
        bash_command='sleep 5',
        retries=3,
        dag=dag) 
    
The precedence rules are:

1. Explicitly passed arguments
2. Values that exist in the `default_args` dictionary
3. The operator's default value, if one exists

The following fields are required:

* `task_id` - this is __important__ because if you want to run a function independently, this is what gets called on the command line
* `owner`

#### Task Running and Clearing

Run - Run a single task instance (can only be done if using something other than SequentialExecutor)

### Clear

Clear - Clear a set of task instances, as if they never ran

Say a DAG needs to be completely rerun, then just run clear:

    airflow clear my_dag -s 2018-09-21 -e 2018-09-22

### Hooks

Hooks are interfaces to external platforms and databases like S3, MySQL, Postgres, HDFS.
Hooks keep authentication code and information out of pipelines, centralized in the metadata database.
Hooks use the `airflow.models.Connection` model.

For example:

    conn = MySqlHook(conn_name_attr='my_db')
    conn.bulk_load(table_name, local_filepath)

#### Hooks and Connections

If we want to store a password in a Hook (encrypted with fernet key), you can do the following:

Create a Connection (with encrypted password) like so:

    from airflow.models import Connection
    def create_conn(username, password, host=None):
        new_conn = Connection(conn_id=f'{username}_connection',
                                      login=username,
                                      host=host if host else None)
        new_conn.set_password(password)

Access the Connection (and password) like so:

    from airflow.hooks.base_hook import BaseHook

    connection = BaseHook.get_connection("username_connection")
    # this connection gives you the host, login, and password for your db connection

    password = connection.password # This is a getter that returns the unencrypted password.

We do this over an Airflow variable so that we get an encrypted password.

### Pools

Some systems can get overwhelmed when too many processes hit them at the same time. Use an Airflow __pool__ to
__limit the execution parallelism__ on specific tasks. A task can be associated to a pool with the `pool` paramter
when creating the task (i.e. when setting up the Operator, e.g. BashOperator).

You can also define `priority_weight` to define priorities in the queue and which tasks get executed first as slots
open up in the pool. The default `priority_weight` is 1.

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

### Branching

If you need to setup a workflow that goes down a certain path based on an arbitrary condition (e.g. if something
happened on an upstream task), then use __branching__.

### SubDAGs

__SubDAGs__ are used for __repeating patterns__. Defining a function that returns a DAG object is a nice design pattern
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


## Real Life Scenario

### Running a DAG

Let's list the active dags

    # print the list of active DAGs
    airflow list_dags

    # print the list of tasks from the DAG that has a dag_id of "tutorial"
    airflow list_tasks tutorial

    # print the hierarchy of tasks in the tutorial DAG
    airflow list_tasks tutorial --tree

### Testing a DAG

You can test out a DAG with `airflow test` followed by the command layout:

    command subcommand dag_id task_id date

An example is:

    # test print_date function from the tutorial dag
    airflow test tutorial print_date 2016-01-01

    airflow test tutorial sleep 2016-01-01

### Backfill

You can run a __backfill__ to rerun parts of your tasks from a certain time period (e.g. say you have a DAG that 
consists of 5 operations, but only the last 2 operations failed and that these tasks normally run once 
a day. You can backfill and say only run these last two operations for the last 7 days).

    airflow backfill my_dag -s 2018-09-10 -e 2018-09-11

### Catchup

If your DAG Run has not been run for a specific interval (or has been cleared), we can rerun those times with backfill.
An exception would be if your DAG has its own catchup written (e.g. schedule is not limited to an interval, but instead
uses `Now` for instance), then we'll want to turn off catchup (either on the DAG itself with `dag.catchup = False`
inside the configuration file with `catchup_by_default = False`).

With catchup turned off, you tell the scheduler to only create a DAG run for the most current instance of the DAG
interval series. You should only turn this off if your DAG runs perform backfill internally.

### Macros

The airflow engine passes a few variables by default that are accessible in templates.

You might need say the execution datetime in your command that you're running, so it might look like the following
where we have a templated command (and the `macro.ds_add`)

    templated_command = """
    {% for i in range(5) %}
        echo "{{ ds }}"
        echo "{{ macros.ds }}"
        echo "{{ next_execution_date }}"
        echo "{{ params.my_param }}"
    {% endfor %}
    """

    t3 = BashOperator(
        task_id='templated',
        depends_on_past=False,
        bash_command=templated_command,
        params={'my_param': 'Parameter I passed in'},
        dag=dag)

### Params

Params is a dictionary that you can pass in. This is useful if you have say a variable like `MY_CONSTANT`
that is used in multiple Operators. You can just pass in that param now. E.g.

    TIMEZONE_SETTING='EST'
    
    t3 = BashOperator(
        ...
        params={'timezone': TIMEZONE_SETTING},
        bash_command="python myprogram --timezone_setting {{ params.timezone }}"
    )

#### User Defined Macros

You can create your own macros, say you have your own custom function. One advantage to this is that
if you need a value from your rendered template (e.g. the `execution_date`), then calling the function
through the DAG's `user_defined_macro` will give you the actual value.

    def compute_next_execution_date(dag, execution_date):
        return dag.following_schedule(execution_date)

    dag = DAG('my_simple_dag', schedule_interval='@hourly',
              user_defined_macros={
                  'next_execution_date': compute_next_execution_date
              })

    task = BashOperator(
        task_id='bash_operation',
        bash_command='echo "{{ next_execution_date(dag, execution_date) }}"',
        dag=dag
    )

Note that since an item like `next_execution_date` is a python object (datetime), you can also call methods on it.
For example:

    echo '{{ next_execution_date }}'  # '2018-08-04 16:05:00'
    echo '{{ next_execution_date.isoformat() }}'  # '2018-08-03T16:05:00'
    echo '{{ next_execution_date.strftime("%d-%m-%Y") }}'

## Command Line Interface

If you're running Airflow on a server, sometimes it might be easier to just jump into the command line.
You can run command line airflow with:

    airflow <command>

Commands include:

    __resetdb__ - burn down and rebuild the metadata database
    __render__ - render a task instance's template(s)
    __variables__ - CRUD operations on variables (e.g. --set, --get, --delete)
    __connections__ - list/add/delete connections
    __pause__ - pause a DAG
    __unpause__ - unpause a DAG
    __trigger_dag__ - trigger a DAG run
    __dag_state__ - get the status of a dag run
    __task_state__ - get the status of a task instance
    __run__ - run a single task instance
    __list_tasks__ - list the tasks within a DAG
    __list_dags__ - list all the DAGs
    __initdb__ - initialize the metadata database
    __test__ - test a task instance (run a task without checking for dependencies or recording state in db)
    __scheduler__ - run scheduler as a persistent service
    __clear__ - clear a previous run


### Example Bash Command

https://github.com/trbs/airflow-examples/blob/master/dags/tutorial.py

## Database Tables

So if you're using a relational database like MySQL to store airflow database, the main tables might look like this:

    mysql> use airflow

    mysql> show tables;
    +--------------------+
    | Tables_in_airflow  |
    +--------------------+
    | alembic_version    |
    | celery_taskmeta    |
    | celery_tasksetmeta |
    | chart              |
    | connection         |
    | dag                |
    | dag_pickle         |
    | dag_run            |
    | dag_stats          |
    | import_error       |
    | job                |
    | known_event        |
    | known_event_type   |
    | log                |
    | sla_miss           |
    | slot_pool          |
    | task_fail          |
    | task_instance      |
    | users              |
    | variable           |
    | xcom               |
    +--------------------+
    21 rows in set (0.00 sec)

### airflow.task_instance

The task instance table shows you what dags have been executed, when, the state, etc.

    mysql> describe task_instance;
    +-----------------+---------------+------+-----+---------+-------+
    | Field           | Type          | Null | Key | Default | Extra |
    +-----------------+---------------+------+-----+---------+-------+
    | task_id         | varchar(250)  | NO   | PRI | NULL    |       |
    | dag_id          | varchar(250)  | NO   | PRI | NULL    |       |
    | execution_date  | datetime(6)   | NO   | PRI | NULL    |       |
    | start_date      | datetime(6)   | YES  |     | NULL    |       |
    | end_date        | datetime(6)   | YES  |     | NULL    |       |
    | duration        | float         | YES  |     | NULL    |       |
    | state           | varchar(20)   | YES  | MUL | NULL    |       |
    | try_number      | int(11)       | YES  |     | NULL    |       |
    | hostname        | varchar(1000) | YES  |     | NULL    |       |
    | unixname        | varchar(1000) | YES  |     | NULL    |       |
    | job_id          | int(11)       | YES  | MUL | NULL    |       |
    | pool            | varchar(50)   | YES  | MUL | NULL    |       |
    | queue           | varchar(50)   | YES  |     | NULL    |       |
    | priority_weight | int(11)       | YES  |     | NULL    |       |
    | operator        | varchar(1000) | YES  |     | NULL    |       |
    | queued_dttm     | datetime(6)   | YES  |     | NULL    |       |
    | pid             | int(11)       | YES  |     | NULL    |       |
    | max_tries       | int(11)       | YES  |     | -1      |       |
    +-----------------+---------------+------+-----+---------+-------+

    mysql> select * from task_instance;
    +----------------------+---------------------+----------------------------+----------------------------+----------------------------+----------+---------+------------+--------------+----------+--------+------+---------+-----------------+--------------+----------------------------+------+-----------+
    | task_id              | dag_id              | execution_date             | start_date                 | end_date                   | duration | state   | try_number | hostname     | unixname | job_id | pool | queue   | priority_weight | operator     | queued_dttm                | pid  | max_tries |
    +----------------------+---------------------+----------------------------+----------------------------+----------------------------+----------+---------+------------+--------------+----------+--------+------+---------+-----------------+--------------+----------------------------+------+-----------+
    | my_task_id_abcdefghi | dag_download_abcdef | 2018-08-02 00:05:00.000000 | 2018-08-03 15:46:14.788703 | 2018-08-03 15:46:18.545962 |  3.75726 | success |          1 | 3d3a39269fbd | root     |      2 | NULL | default |               2 | BashOperator | 2018-08-03 15:46:13.419580 |  965 |         2 |
    | my_task_id_abcdefghi | dag_download_abcdef | 2018-08-02 01:05:00.000000 | 2018-08-03 15:46:23.427211 | 2018-08-03 15:46:26.416335 |  2.98912 | success |          1 | 3d3a39269fbd | root     |      4 | NULL | default |               2 | BashOperator | 2018-08-03 15:46:22.171445 | 1000 |         2 |
    | my_task_id_abcdefghi | dag_download_abcdef | 2018-08-02 02:05:00.000000 | 2018-08-03 15:46:32.067906 | 2018-08-03 15:46:35.113323 |  3.04542 | success |          1 | 3d3a39269fbd | root     |      5 | NULL | default |               2 | BashOperator | 2018-08-03 15:46:30.859648 | 1043 |         2 |

