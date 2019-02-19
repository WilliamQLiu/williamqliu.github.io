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

## Database

If you setup LocalExecutor and change out the `sql_alchemy_conn`, you can get going pretty quickly with Airflow.
It might look like this:

    sql_alchemy_conn = mysql://myusername:mypassword@mydb:3306/airflow

## Run Webserver and Scheduler

airflow webserver  # shows GUI
airflow scheduler  # sends tasks (and picks up tasks if you're LocalExecutor)
airflow worker # picks up tasks only if you're using Celery

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

My advice is to name your `dag_id` with versions (e.g. `my_dag_v1`) because when certain things change about your
DAG (like `schedule_interval`), bump up the version (e.g. from v1 to v2).

You can also pass in presents into the `schedule_interval`, including:

* `None` - don't schedule, use for "externally triggered" DAGs
* `@once` - schedule once and only once
* `@hourly` - run once an hour at the beginning of the hour
* `@daily` - run once a day at midnight

etc.

Keep in mind if you're running a distrubted system that your DAG is sent to a `/tmp` folder so if you have code
that uses say `os.path`, make sure that path is there for all your servers.

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
* `SSHOperator` - run a command through SSH (beware that ENV variables don't transfer)

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

#### Run even if previous task failed

You can set an Operator to run, even if the previous job failed using `trigger_rule="all_done"`.

    t2 = BashOperator(
        task_id='sleep',
        bash_command='sleep 5',
        retries=3,
        trigger_rule="all_done",
        dag=dag) 

#### Task Running and Clearing

Run - Run a single task instance (can only be done if using something other than SequentialExecutor)

### Clear

Clear - Clear a set of task instances, as if they never ran

Say a DAG needs to be completely rerun, then just run clear:

    airflow clear my_dag -s 2018-09-21 -e 2018-09-22

Clear only dags that have failed

    airflow clear my_dag_id -f

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

### TriggerDagRunOperator - Trigger another Dag from current Dag

If branching isn't enough (i.e. based off some conditional logic, execute a set of tasks), you might want to 
consider `TriggerDagRunOperator` so you can kick off entire DAGs from your current DAG. To do this, you need:

* A Controller DAG - the DAG that conditionally executes a trigger
* A Target DAG - the DAG that is being triggered

You might want to do a Trigger job if you want to do some safety checks/testing before running your real operation.

#### Example Controller DAG

The Controller DAG conditionally executes the trigger.

    # -*- coding: utf-8 -*-
    #
    # Licensed to the Apache Software Foundation (ASF) under one
    # or more contributor license agreements.  See the NOTICE file
    # distributed with this work for additional information
    # regarding copyright ownership.  The ASF licenses this file
    # to you under the Apache License, Version 2.0 (the
    # "License"); you may not use this file except in compliance
    # with the License.  You may obtain a copy of the License at
    #
    #   http://www.apache.org/licenses/LICENSE-2.0
    #
    # Unless required by applicable law or agreed to in writing,
    # software distributed under the License is distributed on an
    # "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    # KIND, either express or implied.  See the License for the
    # specific language governing permissions and limitations
    # under the License.
    """This example illustrates the use of the TriggerDagRunOperator. There are 2
    entities at work in this scenario:
    1. The Controller DAG - the DAG that conditionally executes the trigger
    2. The Target DAG - DAG being triggered (in example_trigger_target_dag.py)
    This example illustrates the following features :
    1. A TriggerDagRunOperator that takes:
      a. A python callable that decides whether or not to trigger the Target DAG
      b. An optional params dict passed to the python callable to help in
         evaluating whether or not to trigger the Target DAG
      c. The id (name) of the Target DAG
      d. The python callable can add contextual info to the DagRun created by
         way of adding a Pickleable payload (e.g. dictionary of primitives). This
         state is then made available to the TargetDag
    2. A Target DAG : c.f. example_trigger_target_dag.py
    """

    import pprint
    from datetime import datetime

    from airflow import DAG
    from airflow.operators.dagrun_operator import TriggerDagRunOperator

    pp = pprint.PrettyPrinter(indent=4)


    def conditionally_trigger(context, dag_run_obj):
        """This function decides whether or not to Trigger the remote DAG"""
        c_p = context['params']['condition_param']
        print("Controller DAG : conditionally_trigger = {}".format(c_p))
        if context['params']['condition_param']:
            dag_run_obj.payload = {'message': context['params']['message']}
            pp.pprint(dag_run_obj.payload)
            return dag_run_obj


    # Define the DAG
    dag = DAG(
        dag_id='example_trigger_controller_dag',
        default_args={
            "owner": "airflow",
            "start_date": datetime.utcnow(),
        },
        schedule_interval='@once',
    )

    # Define the single task in this controller example DAG
    trigger = TriggerDagRunOperator(
        task_id='test_trigger_dagrun',
        trigger_dag_id="example_trigger_target_dag",
        python_callable=conditionally_trigger,
        params={'condition_param': True, 'message': 'Hello World'},
        dag=dag,
    )

#### The Target DAG

The DAG that is triggered (externally instead of scheduled regularly) and run

    # -*- coding: utf-8 -*-
    #
    # Licensed to the Apache Software Foundation (ASF) under one
    # or more contributor license agreements.  See the NOTICE file
    # distributed with this work for additional information
    # regarding copyright ownership.  The ASF licenses this file
    # to you under the Apache License, Version 2.0 (the
    # "License"); you may not use this file except in compliance
    # with the License.  You may obtain a copy of the License at
    #
    #   http://www.apache.org/licenses/LICENSE-2.0
    #
    # Unless required by applicable law or agreed to in writing,
    # software distributed under the License is distributed on an
    # "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    # KIND, either express or implied.  See the License for the
    # specific language governing permissions and limitations
    # under the License.

    import pprint
    from datetime import datetime

    from airflow.models import DAG
    from airflow.operators.bash_operator import BashOperator
    from airflow.operators.python_operator import PythonOperator

    pp = pprint.PrettyPrinter(indent=4)

    # This example illustrates the use of the TriggerDagRunOperator. There are 2
    # entities at work in this scenario:
    # 1. The Controller DAG - the DAG that conditionally executes the trigger
    #    (in example_trigger_controller.py)
    # 2. The Target DAG - DAG being triggered
    #
    # This example illustrates the following features :
    # 1. A TriggerDagRunOperator that takes:
    #   a. A python callable that decides whether or not to trigger the Target DAG
    #   b. An optional params dict passed to the python callable to help in
    #      evaluating whether or not to trigger the Target DAG
    #   c. The id (name) of the Target DAG
    #   d. The python callable can add contextual info to the DagRun created by
    #      way of adding a Pickleable payload (e.g. dictionary of primitives). This
    #      state is then made available to the TargetDag
    # 2. A Target DAG : c.f. example_trigger_target_dag.py

    args = {
        'start_date': datetime.utcnow(),
        'owner': 'airflow',
    }

    dag = DAG(
        dag_id='example_trigger_target_dag',
        default_args=args,
        schedule_interval=None,
    )


    def run_this_func(ds, **kwargs):
        print("Remotely received value of {} for key=message".
              format(kwargs['dag_run'].conf['message']))


    run_this = PythonOperator(
        task_id='run_this',
        provide_context=True,
        python_callable=run_this_func,
        dag=dag,
    )

    # You can also access the DagRun object in templates
    bash_task = BashOperator(
        task_id="bash_task",
        bash_command='echo "Here is the message: '
                     '{{ dag_run.conf["message"] if dag_run else "" }}" ',
        dag=dag,
    )


### SubDAGs

__SubDAGs__ are used for __repeating patterns__. Defining a function that returns a DAG object is a nice design pattern
when using Airflow. Airflow uses the __stage-check-exchange__ pattern when loading data. Data is staged in a temporary
table, after which data quality checks are performed against that table. Once all the checks pass, the partition
is moved into the production table.



### SLAs

__Service Level Agreements__ is the time that a task or DAG should have succeeded (by setting a task level as 
a `timedelta`).  If one or more instances haven't succeeded by that time, you can send out an alert email.
You can set the SLA like this example here by adding `sla` to your arguments:

https://github.com/apache/airflow/blob/master/airflow/example_dags/tutorial.py#L49

    # These args will get passed on to each operator
    # You can override them on a per-task basis during operator initialization
    default_args = {
        'owner': 'airflow',
        'depends_on_past': False,
        'start_date': airflow.utils.dates.days_ago(2),
        'email': ['airflow@example.com'],
        'email_on_failure': False,
        'email_on_retry': False,
        'retries': 1,
        'retry_delay': timedelta(minutes=5),
        # 'queue': 'bash_queue',
        # 'pool': 'backfill',
        # 'priority_weight': 10,
        # 'end_date': datetime(2016, 1, 1),
        # 'wait_for_downstream': False,
        # 'dag': dag,
        # 'adhoc':False,
        'sla': timedelta(hours=2),
        # 'execution_timeout': timedelta(seconds=300),
        # 'on_failure_callback': some_function,
        # 'on_success_callback': some_other_function,
        # 'on_retry_callback': another_function,
        # 'trigger_rule': u'all_success'
    }

    dag = DAG(
        'tutorial',
        default_args=default_args,
        description='A simple tutorial DAG',
        schedule_interval=timedelta(days=1),
    )

### Email

If you want to setup sending email, make sure to edit your `airflow.cfg` file under the `[smtp]` section:

    [smtp]
    smtp_host = smtp.gmail.com
    smtp_starttls = True
    smtp_ssl = False
    smtp_user = YOUR_EMAIL_ADDRESS
    smtp_password = 16_DIGIT_APP_PASSWORD # Or password in raw text for testing if you don't want to generate a Google App Password
    smtp_port = 587
    smtp_mail_from = YOUR_EMAIL_ADDRESS

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

### User Defined Libraries

Say you install a library of your own with:

    python setup.py build
    python setup.py install

When you run your library through say a Bash Command, you'll want to make sure
that your files have:

    if __name__ == '__main__':
        pass

Otherwise for some reason, files might not get picked up.

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


## Logging

The log format by default is a little too granular for my tastes. I like to log into one file and just grep from there.
In airflow.cfg, just modify the `log_filename_format`. Default is:

    log_filename_template = {{ ti.dag_id }}/{{ ti.task_id }}/{{ ts }}/{{ try_number }}.log

I prefer something like:

    log_filename_template = {{ ti.dag_id }}//{{ ti.task_id }}/loggy.log

It makes it easier to grep and clean out logs

## Systemctl

You can run your airflow commands in something like `screen` or `nohup` for quick testing, but if you want a production
environment, you might want to consider using `systemctl` to handle airflow restarts.

Basically, check out this: https://github.com/apache/airflow/tree/master/scripts/systemd

As an example, on a CentOS machine, I have the following (where `will` is my user and airflow is installed in `/home/will`)

### /etc/sysconfig/airflow

    # Licensed to the Apache Software Foundation (ASF) under one
    # or more contributor license agreements.  See the NOTICE file
    # distributed with this work for additional information
    # regarding copyright ownership.  The ASF licenses this file
    # to you under the Apache License, Version 2.0 (the
    # "License"); you may not use this file except in compliance
    # with the License.  You may obtain a copy of the License at
    # 
    #   http://www.apache.org/licenses/LICENSE-2.0
    # 
    # Unless required by applicable law or agreed to in writing,
    # software distributed under the License is distributed on an
    # "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    # KIND, either express or implied.  See the License for the
    # specific language governing permissions and limitations
    # under the License.

    # This file is the environment file for Airflow. Put this file in /etc/sysconfig/airflow per default
    # configuration of the systemd unit files.
    #
    AIRFLOW_CONFIG=/home/will/airflow/airflow.cfg
    AIRFLOW_HOME=/home/will/airflow/

### /usr/lib/tmpfiles.d/airflow.conf

    #Licensed to the Apache Software Foundation (ASF) under one
    # or more contributor license agreements.  See the NOTICE file
    # distributed with this work for additional information
    # regarding copyright ownership.  The ASF licenses this file
    # to you under the Apache License, Version 2.0 (the
    # "License"); you may not use this file except in compliance
    # with the License.  You may obtain a copy of the License at
    # 
    #   http://www.apache.org/licenses/LICENSE-2.0
    # 
    # Unless required by applicable law or agreed to in writing,
    # software distributed under the License is distributed on an
    # "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    # KIND, either express or implied.  See the License for the
    # specific language governing permissions and limitations
    # under the License.

    D /run/airflow 0755 airflow airflow

### airflow webserver

    #
    # Licensed to the Apache Software Foundation (ASF) under one
    # or more contributor license agreements.  See the NOTICE file
    # distributed with this work for additional information
    # regarding copyright ownership.  The ASF licenses this file
    # to you under the Apache License, Version 2.0 (the
    # "License"); you may not use this file except in compliance
    # with the License.  You may obtain a copy of the License at
    # 
    #   http://www.apache.org/licenses/LICENSE-2.0
    # 
    # Unless required by applicable law or agreed to in writing,
    # software distributed under the License is distributed on an
    # "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    # KIND, either express or implied.  See the License for the
    # specific language governing permissions and limitations
    # under the License.

    [Unit]
    Description=Airflow webserver daemon
    After=
    Wants=

    [Service]
    EnvironmentFile=/etc/sysconfig/airflow
    User=will
    Group=will
    Type=simple
    ExecStart=/bin/sh -c 'source /home/will/.virtualenvs/airflow/bin/activate && airflow webserver'
    Restart=on-failure
    RestartSec=5s
    PrivateTmp=true

    [Install]
    WantedBy=multi-user.target

     
### /usr/lib/systemd/system/airflow-scheduler.service

    # Licensed to the Apache Software Foundation (ASF) under one
    # or more contributor license agreements.  See the NOTICE file
    # distributed with this work for additional information
    # regarding copyright ownership.  The ASF licenses this file
    # to you under the Apache License, Version 2.0 (the
    # "License"); you may not use this file except in compliance
    # with the License.  You may obtain a copy of the License at
    # 
    #   http://www.apache.org/licenses/LICENSE-2.0
    # 
    # Unless required by applicable law or agreed to in writing,
    # software distributed under the License is distributed on an
    # "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    # KIND, either express or implied.  See the License for the
    # specific language governing permissions and limitations
    # under the License.

    [Unit]
    Description=Airflow scheduler daemon
    After=
    Wants=

    [Service]
    EnvironmentFile=/etc/sysconfig/airflow
    User=will
    Group=will
    Type=simple
    ExecStart=/bin/sh -c 'source /home/will/.virtualenvs/airflow/bin/activate && /home/will/.virtualenvs/airflow/bin/airflow scheduler'
    Restart=always
    RestartSec=5s

    [Install]
    WantedBy=multi-user.target
     
### Gotchas

Airflow has a few gotchas:

* In a DAG, I found that `pendulum` would work on versions 1.10, but in version 1.9 I had to use `from airflow.utils.dates import days_ago`.
* Make sure your `airflow scheduler` and if necessary, `airflow worker` is running
* Make sure your dag is unpaused
* If using a virtualenv with systemctl, make sure to set ExecStart to activate the virtualenv, e.g. `ExecStart=/bin/sh -c 'source /home/will/.virtualenvs/airflow/bin/activate && /home/will/.virtualenvs/airflow/bin/airflow scheduler'`
* For `priority_weight`, set this to `0` otherwise your `airflow.task_instance` table will stack up. E.g. with a `priority_weight` of 10
  on the first task, the next task will be `priority_weight` of 20, next is 30, etc. With a value of `0`, all `priority_weight` is 0.

