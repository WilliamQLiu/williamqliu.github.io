---
layout: post
title: Apache Spark 
---


# {{ page.title }}


Apache Spark is a fast and general engine for large-scale data processing.

## Background

### Compared to MySQL

So Spark is focused on processing (with the ability to pipe data directly from/to external datasets like S3), 
whereas you might be familiar with a relational database like MySQL, where you have storage and processing built in.

## Installation

Install Steps:

1. Download the latest pre-built version from the Apache Spark site: e.g. spark-2.2.0-bin-hadoop2.7 
2. tar -zxvf spark-2.2.0-bin-hadoop2.7 
3. Extract this over to a place like /opt/spark-2.2.1
4. Setup environment variables in your .bashrc like:
	`export SPARK_HOME=/opt/spark-2.2.1` and
    `export PATH=$SPARK_HOME/bin:$PATH` 
5. Access the shell under `/opt/pyspark` or `/bin/spark-shell` (for scala)
   SPARK_./bin/spark-submit --master local[8] <python_file.py>

OR if you're using python, run `pip install pyspark` and `pip install py4j`

Note that if you install using the python route, the features are a little behind. 
For example, the python `sbin` doesn't have `start-master.sh` scripts. I recommend installing regularly instead
of through pip

## Run


### Exploration

If you want to explore around in a REPL, run `pyspark`
If you want to submit a regular job, run: `bin/spark-submit`
If you want to run spark sql, run `/bin/spark-sql`

### Standalone Cluster

Run a server with `sbin/...` is where you run say hadoop or yarn

Example, a standalone master server:

	./sbin/start-master.sh

You'll get a web ui and a url: http://localhost:8080/
This will show you details like the master's url (e.g. Spark Master at spark://xps:7077)

To connect workers to the master, run `./sbin/start-slave.sh spark://xps:7077`
You should now see workers connected

Now if you want to submit jobs to the master, simply do `bin/spark-submit spark://xps:7077` (or add that to your conf)
For example: `spark-submit --master spark://xps:7077 --packages
org.mongodb.spark:mongo-spark-connector_2.11:2.2.1 --jars
/usr/share/java/mysql-connector-java.jar ~/GitHub/automatic-guacamole/parsy.py`

## Setup Hadoop

Spark can read a few different file formats and systems, from csv to hadoop.

Configure:
https://www.digitalocean.com/community/tutorials/how-to-install-hadoop-in-stand-alone-mode-on-ubuntu-16-04

Environment Variables:
http://codesfusion.blogspot.com/2013/10/setup-hadoop-2x-220-on-ubuntu.html


## Make sure to install

sudo apt-get install ssh
sudo apt-get install openssh-client openssh-server


Run this otherwise you'll get permission denied when trying to ssh into your
own server
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

Hadoop Cluster on:

http://localhost:8088/cluster

50070:50070 -p 8088:8088 -p 8080:8080

## Submitting a job

Say you have spark-submit on `/usr/local/bin/spark-submit` and you want to submit a job. You can run with:

    spark-submit my_python_application.py --master <insert> -v

Let's explain the options:

    * `my_python_application.py` is the python spark app that should be executed
    * `-v` is for verbose
    * `--master` the master url specifying where jobs should be submitted to

### --master

There's a few different ways to specify the master url, including:

    * Specific master hostname (e.g. Spark Master at spark://13eb10996287:7077)
        URL: spark://13eb10996287:7077
        REST URL: spark://13eb10996287:6066 (cluster mode)
      So this would look like `spark-submit my_app.py --master spark://13eb10996287:7077`)
      To find the hostname, just run `hostname -f` or find hostname from the Spark UI at `:8080`
      The default port is `:7077`
    * `yarn` managed from YARN based on `YARN_CONF_DIR` or `HADOOP_CONF_DIR` variables

### --deploy-mode

There's a few different deploy modes, meaning we either deploy the driver on the worker nodes (`cluster`) or
locally as an external client (`client`, the default is client). Options are:

    * `cluster` - cluster mode makes everything run inside the cluster. You can start a job from your laptop
        and the job will continue running even if you close your computer. In this mode, the Spark Driver
        is encapsulated inside the YARN Application Master. Cluster mode is more suited for __long running__ jobs.
    * `client` - client mode makes the Spark Driver run on a client, such as your laptop. If the client is shut down,
        the job fails. Spark Executors still run on the cluster, and to schedule everything, a small YARN application
        master is created. Client Mode is more suited for __interactive__ jobs.

Sample job might look like:

    spark-submit my_job.py --master <master_url> --deploy-mode cluster -v

### Python Additional Files

Say you're using a Python library like Shapely or Pandas. You can build out a Python egg, but that will be specific
to the client machine's CPU architecture (because of the required C compilation). Libraries that need eggs include
Pandas, NumPy, SciPy. Distributing an egg is a brittle solution, but since we're doing distributed computing, we
still need a way to get our libraries.

So instead of distributing an egg, we're going to distrubte a `.zip` file using the `--py-files` parameter in 
`spark-submit`. It'll look like `spark-submit --py-files dependencies.zip my_spark_job.py`

Assuming you have a requirements.txt file, run the following to create a dependencies.zip:

    pip install -t dependencies -r requirements.txt
    cd dependencies
    zip -r ../dependencies.zip .

Now run with: `spark-submit --py-files dependencies.zip my_spark_job.py`. This `--py-files` sends the zip file to
the Spark workers, but does not add it to the `PYTHONPATH`. To add the dependencies to the `PYTHONPATH`, add the
following line to the `my_spark_job.py`:

    from pyspark.sql import SparkSession

    my_spark = SparkSession.builder.appName("willApp").config("py-files", "/root/airflow/dependencies.zip").getOrCreate()

    my_spark.sparkContext.addPyFile("/root/airflow/dependencies.zip")

You can then use the dependencies like:

    from mylibrary import some_file

Assuming that in your dependencies folder there's a `mylibrary` directory with a `some_file`

Note that you need to import your libraries after your sparkContext runs `addPyFile`.

### Jobs UI

You should be able to see all __currently running jobs__ through the UI here: 

    localhost:4040/jobs

If there are multiple SparkContexts running, it'll bind to successive ports, e.g. `4041`, `4042`

https://spark.apache.org/docs/latest/monitoring.html

Note that as soon as the application is done, the UI disappears.

#### Jobs History Server

It is possible to view previous jobs through Spark's history server on:

    ./sbin/start-history-server.sh

This creates a web interface at `http://<server-url>:18080`

Note that in order for it work, you might have to create this directory:

    mkdir /tmp/spark-events

You can also log events here in the file `spark-defaults.conf` (`cp spark-defaults.conf.template spark-defaults.conf`)

    spark.eventLog.enabled true

    # can also specify a file outside local
    spark.eventLog.dir hdfs://namenode/shared/spark-logs

## Spark Architecture

https://spark.apache.org/docs/latest/cluster-overview.html

### Spark Application

A Spark Application consists of a __driver__ process and a set of __executor__ processes.

There is also a __SparkSession__ that gets created with a Spark Application: there is a one-to-one correspondence
between a SparkSession and a Spark Application.

From Spark 2.0 and later, a SparkSession, you want to just use a SparkSession to access everything since a
SparkSession includes all APIs.

#### Driver Process

The Spark application that we're sending to run is an independent set of processes on a cluster that is coordinated
by the `SparkContext` object in your main program (i.e. the __driver program__). The driver process runs your `main()`
function. The driver program is responsible for three things:

* Maintain information about the Spark Application
* Respond to a user's program or input
* Analyzes, Distributes, and Schedules work across __executors__

#### Cluster Manager

To run an application on a cluster, the SparkContext can connect to different __cluster managers__. These cluster
managers can be standalone cluster managers, Mesos or YARN, which allocate resources across applications.

#### Executors

Once connected, Spark acquires __executors__ on nodes in the cluster, which are processes that run computations and
store data in your application. Once Spark has executors, it sends your application code (e.g. Python file passed to
SparkContext) to the executors. Finally SparkContext sends __tasks__ to the executors to run.

### Spark Components

Some things to note about each of the above Spark Architecture:

* Each application gets its own executor processes, which stay up for the duration of the whole application (i.e.
  each application is isolated from each other). Data cannot be shared across different Spark applications (instances
  of SparkContext)
* The driver program must listen for and accept incoming connections from its executors

### Spark APIs

There are two fundamental sets of APIs:

* the low level "unstructured" APIs - Resilient Distributed Datasets (__RDD__) - don't use these
* the higher level "structured" APIs - DataFrames, SparkSQL, Datasets (only available to Java and Scala)

### Spark Master

Start Master server iwth:
    
    ./sbin/start-master.sh

### Spark Slaves

Connect Slaves to Master with:

    ./sbin/start-slave.sh <master_url>

Make sure to start this on a separate container (don't have master and slave on the same container)

### Spark's MapPartition

With Spark, you want your objects to be serializable so that they can be transmitted across
the cluster to worker nodes.

Use a mappartition when you want an operation that requires a database connection. 
You want your regular code to be serializable, so with a mappartition, you can
process a complete partition in one go (i.e. just called once per partition)

## Anatomy of a Spark Job

1.) Spark Context / Spark Session Object (i.e. our Spark Application)
2.) Actions e.g. collect, saveAsTextFile (i.e. Job)
3.) Wide Transformations like `sort`, `groupByKey` (i.e. Stage, Stage)
4.) Computation to evaluate one partition, to combine narrow transforms (i.e. Task, Task)

### How Jobs Are Submitted and Run

Driver Program (i.e. SparkContext)
Driver Program is managed by a 'Cluster Manger'
There are many Worker Nodes that get jobs from the Driver Program
Worker Nodes have Executors that run many Tasks

### Actions

Below are some sample __Actions__ by Spark. 

https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html

* `reduce(func)` - aggregate elements of a dataset using a function (func). Make sure the function is
  commutative and associative so that it can be computed correctly in parallel.
* `collect()` - return all the elements of the dataset as an array at the driver program. Usually this is done
  after a filter or other operation that returns a small subset of the data.
* `count()` - return the number of elements in the dataset.
* `first()` - return an array with the first element of the dataset (same as `take(1)`
* `take(n)` - return an array with the first _n_ elements of the dataset
* `takeSample()` - return an array with a random sample of _num_ elements of the dataset, with or without replacement
* `saveAsTextFile(path)` - write the elements of the dataset as a text file (or set of text files) in a given
  directory in a local filesystem, HDFS or any other Hadoop-supported file system. Spark will call `toString` on each
  element to convert it to a line of text in the file
* `countByKey()` - only available on RDDs of type (K, V)
* `foreach(func)` - Run a function `func` on each element of the dataset.

## Parquet

__Parquet__ files are a __columnar__ format supported by many data processing systems. Advantages include automatically
preserving the schema of the original data. When writing Parquet files, all columns are automatically converted
to be nullable (for compatibility reasons). Dataframes can be saved as Parquet files. Note that Parquet is good about
adding new columns, but not so great about deleting columns. Parquet is more suitable for high read intensive data.

### Why Parquet?

Parquet is a column oriented format vs your regular record oriented format (e.g. csv, tsv)
Parquet (and other columnar formats) can handle a lot more columns than you can in a well-designed relational database.

The data is _denormalized_ (instead of most relational formats where we try to have _third normal form_).
You have a lot of repeated values and tables flattened into a single format.

### Schema Evolution w/ Parquet

One of the main advantages of Parquet is that it supports __schema evolution__ (similar to ProtocolBuffer, Avro, Thrift).
We can start with a simple schema, then gradually add more columns to the schema as needed. You might end up with
multiple Parquet files with different, but mutually compatible schemas.

Files are stored with `.parquet` appended (e.g. `my_file.parquet`)

### Parquet Files

You can read or write a single Parquet file, which can be represented in say a table or a pandas dataframe.
You can read a single file back with say `pq.read_table('my_file.parquet')`

#### Parquet Dataset

In reality, a Parquet __dataset__ can consist of many files in many directories. A Parquet dataset can be presented
in a few ways, including:

* a list of Parquet absolute file paths
* a directory name containing nested directories defining a partitioned dataset

An example dataset partitioned by year and month might look like:

    dataset_name/
      year=2017/
        month=01/
          0.parq
          1.parq
        month=02/
      year=2018/

An example might look like:

    # Local dataset write
    pq.write_to_dataset(table, root_path='dataset_name', partition_columns=['one', 'two'])

You can also write to another filesystem (e.g. HDFS, S3)

## Avro

__Avro__ is a row-based storage format (instead of column based like Parquet). If your use case is to scan or
retrieve all of the fields in a row in each query, Avro is usually the best choice. Avro supports adding columns
and deleting columns. Avro is best fit for write intensive operations.

Schemas give a mechanism for reasoning about format changes (what will be a compatible change, meaning we don't need
to do reprocessing and which ones will require reprocessing).

### PyArrow

__PyArrow__ is an in-memory transport layer for data that is being read or written with Parquet files.
You can also use PyArrow for reading and writing Parquet files with pandas.

    import pyarrow.parquet as pq

## Logging with Python

    # Get Spark's Logger
    log4jLogger = sc._jvm.org.apache.log4j
    LOGGER = log4jLogger.LogManager.getLogger(__name__)
    LOGGER.info("INFO - pyspark script logger initialized!!!")  # appears in logs

## Debugging w/ Java Tools

Spark runs executors in individual Java Virtual Machines (JVMs). If you want to really debug, you can look at the
individual virtual machines (VMs) to understand how your code runs. If you can't get the information from the
Spark UI, you can use some of the following JVM utilities for low level debugging:

* `jstack` for providing stack traces of a given JVM process; use if you think there is a deadlock
    `jstack -F -l <process_id>` with `-F` to force the dump (use on hanged processes) and `-l to print info`
* `jmap` for creating heap-dumps (without needing to cause any `OutOfMemoryErrors`) - prints shared object memory maps
    or heap memory details of a given process or core file
* `jstat` for reporting time-series statistics
* `jconsole` for visually exploring various JVM properties
* `jvisualvm` to help profile Spark jobs

### Java Install

Before debugging, you might need to install Java.

    sudo apt-get update
    sudo apt-get install default-jre  # install jre or jdk, jdk has some more
    sudo apt-get install default-jdk

    # check with
    java -version
    java version "10.0.1" 2018-04-17
    Java(TM) SE Runtime Environment 18.3 (build 10.0.1+10)
    Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10.0.1+10, mixed mode)

### OS tools

The JVMs run on a host operating system (OS) and it's important to check that these machines are healthy.
You want to check on things like CPU, network, and I/O. Usually cluster-level monitoring solutions provide this,
but if you want to run specific tools on a machine, you can use:

#### dstat

__dstat__ allows you to view all of your system resources instantly. You can compare the network bandwidth to
CPU usage, memory, and disk usage.

Install with `sudo apt-get install dstat`

    $ dstat
    You did not select any stats, using -cdngy by default.
    ----total-cpu-usage---- -dsk/total- -net/total- ---paging-- ---system--
    usr sys idl wai hiq siq| read  writ| recv  send|  in   out | int   csw 
     11   4  73  11   0   0|  24k  574k|   0     0 |  53B   81B|1288  5263 
      9   4  64  22   0   0|   0   532k|  71k   67k|   0     0 |1508  3923 
     10   5  64  21   0   0|   0   816k| 139k  137k|   0     0 |1592  5350 

#### iostat

__iostat__ reports on CPU statistics and input/output statistics for devices, partitions, and network filesystems (NFS).

Install with: `sudo apt-get install sysstat`

#### iotop

__iotop__ shows current input/output (I/O) usage by process or threads. 

Install with `sudo apt-get install iotop`

The __I/O wait__ measurement is the canary for an I/O bottleneck. Wait is the percentage of time your processors
are waiting on the disk. E.g. say it takes 1 second to grab 10,000 rows and the disk access takes 700ms, so
I/O wait is 70% (because the processor is idle while waiting for disk)

`cat /proc/cpuinfo` to see the number of cpu cores

If your I/O wait percentage is greater than (`1 / # of CPU cores`) then your CPUs are waiting a significant
amount of time for the disk to catch up. In the example above, the I/O wait is very large (say server has 8 cores 
so 1/8 cores = 0.125).

#### IOPS

You should focus on how many input/output operations can be performed per-second (__IOPS__). To calculate how
close you are to your maximum I/O throughput is to use your theoretical IOPs and compare it to your actual IOPs.
If the numbers are close, there might be an I/O issue. Use this equation for calculating the __theoretical IOPs__:

    I/O Operations Per Second = (number of disks * Average I/O Operations on 1 disk per second / % of read workload + (Raid Factor * % of write workload)

Compare your theoretical IOPS to the `tps` given from `iostat`:

    $iostat
    07/17/2018 	_x86_64_	(4 CPU)

    avg-cpu:  %user   %nice %system %iowait  %steal   %idle
              10.78    0.01    4.44   11.39    0.00   73.38

    Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
    sda              42.98        23.81       573.90   31017832  747705100

### Cluster Monitoring Tools

If your cluster is not working, you want to know. Use a monitoring solution like __Ganglia__ or __Prometheus__.

## Pyspark Data Types

Python has data types that eventually get converted over to Spark's Catalyst system.

http://spark.apache.org/docs/latest/api/python/pyspark.sql.html?highlight=types#module-pyspark.sql.types

There's many Python data types including:

* DataType - base class for data types
* NullType - i.e. `None`
* StringType - Strings
* BinaryType - Binary (byte array) data type
* BooleanType - Boolean
* DateType - Date (datetime.data)
* TimestampType - Timestamp (datetime.datetime)
* DecimalType - Decimal (decimal.Decimal)
* DoubleType - Double precision floats
* FloatType - Single precision floats
* ByteType - Byte (a signed integer in a single byte)
* IntegerType - Int (signed 32-bit integer)
* LongType - Long (signed 64-bit integer)
* ShortType - Short (a signed 16-bit integer)
* ArrayType - Array (with a DataType for each element in the array)
* MapType - keys and values in a map data type
* StructField - your own schema, specify the field
* StructType - your own schema, specify the entire schema w/ StructFields

### How are data types used?

Say you make a user defined function, you can specify a returned field type.

    from pyspark.sql.types import FloatType

    def square_float(x):
        return float(x**2)

    square_udf_float2 = udf(lambda z: square_float(z), FloatType())


## Debugging with Python

You can set your Log Level (Spark is by default verbose)
Errors come showing the JVM stack trace and Python.

Make sure you have good tests: `spark-testing-base` is on pip

When launching a jupyter notebook, add any additional packages:

    jupyter install --spark_opts="--packages com.databricks:spark-csv_2.10:1.3.0"

## Dataframe

### Rows and Columns

A __row__ is a record of data.

A __column__ can be a __simple type__ (e.g. integer, string) or a __complex type__ (e.g. array, map, null value)

To get the types:

    from pyspark.sql.types import *

    # example
    b = ByteType()

### Setup

    my_spark = SparkSession.build.appName("MyAppName") \
                   .config("jars", "/usr/local/spark-2.2.2-bin-hadoop2.7/jars/mysql-connector-java-5.1.45-bin.jar")
                   .getOrCreate()

### Read from DB from JDBC

How to read from a database into a dataframe using a MySQL connector

    my_df = my_spark.read.jdbc(url="jdbc:mysql://my_hostname:3306/my_server",
                               table="(SELECT my_field FROM my_table) AS my_table_name",
                               properties={'user': 'myusername', 'password': 'mypassword'})

### Write to DB using JDBC

    my_df.write.format('jdbc').options(
        url="jdbc:mysql://my_hostname:3306/my_table",
        driver="com.mysql.jdbc.Driver',
        dbtable="my_table",
        user="username",
        password="password"
    ).mode("overwrite").save()

#### Write to DB (Partitioning)

You can add in additional options like `partitionColumn`, `lowerBound`, `upperBound`, `numPartitions` to
increase the throughput of the database write. For example, if you specify the number of partitions, then
each executor can parallelize the ingestion of the data.

##### Shuffle

Certain operations in Spark trigger an event called the __shuffle__. The shuffle is Spark's way of re-distributing
data so it's grouped differently across partitions. A shuffle typically involves copying data across executors
and machines, making the shuffle a complex and costly operation.

An example of this is the `reduceByKey` operation, where a new RDD is generated and all values for a single key
are combined to a tuple. The issue is that all values for a single key might not be on the same partition, or even
the same machine, but they have to be co-located to compute the result.

This requires that Spark do an all-to-all operation, meaning it must read from all partitions to find all values
for all keys, then bring together values across partitions to compute the final result for each key. 
This is called the shuffle.

Operations that can cause shuffles include:

* `repartition`
* `coalesce`
* `ByKey` operations (except for counting), like `groupByKey`, `reduceByKey`, join operators like `cogroup`, `join`


##### Repartition

You can repartition (to increase or decreate the number of partitions) with `repartition`. A __repartition__
does a full shuffle of the data, then creates equal sized partitions of the data.

    # See how many partitions
    #print(joined_df.rdd.getNumPartitions())

    # Set more partitions
    #joined_df.repartition(5)
    #print(joined_df.rdd.getNumPartitions())

When repartitioning with a column, there will be a minimum of 200 partitions by default. There may be say
two partitions with data (if a key only has say 'red' and 'blue') and 198 empty partitions. Partitioning by
a column is similar to creating an index on a column in a relational database.

In general, the rough number for number of partitions is to multiply the number of CPUs in the cluster by
2, 3, or 4.

    number_of_partitions = number_of_cpus * 4

If you are writing data out to a file system, a reasonable partition size is if your files are reasonably sized
(roughly 100MB each).

Usually you'll want to do a repartition after a join or a filter; it'll increase downstream operations tremendously.

##### Batch Size

If you find that your jdbc connection is taking a long time to write, consider using the `batchSize` option.
I needed to write 2 million records into a MySQL database and it would take anywhere from 30 minutes to over an
hour to write. However, when I set the `batchSize` option, the process completed in about 4 minutes.

    my_df.write.format('jdbc').options(
        url='jdbc:mysql://my_server:3306/my_db',
        driver='com.mysql.jdbc.Driver',
        dbtable='my_table',
        user='my_username',
        password='my_password',
        numPartitions='4',
        batchSize='10000'  # <- this guy!
    ).mode('overwrite').save()

##### Coalesce

You can reduce the number of partitions in a Dataframe with `coalesce`.

    new_df = joined_df.coalesce(2)  # consolidate data into 2 partitions

Coalesce combines existing partitions to avoid a full shuffle (whereas repartition algorithm does a full shuffle
of the data and creates equal sized partitions of data)

### Alias

Make sure to set an alias to your dataframe, otherwise you'll get an error after you join your dataframes (if
two columns are named the same thing and you reference one of the named columns).

    my_df = my_df.alias("my_df") 

### Join

You can join with:

    df_left.join(df_right, df_left.my_field == df_right.my_field, how='inner')

    # Multiple Joins
    joined_df = df_left.join(df_right, "my_field", "left") \
                       .join(df_another, "my_field", "left")

You can specify the how with:

    inner, cross, outer, full, full_outer, left, left_outer, right, right_outer, left_semi, and left_anti

### Modes

mode - specifies the behavior of the save operation when data already exists.

* `append`: Append contents of this DataFrame to existing data.
* `overwrite`: Overwrite existing data.
* `ignore`: Silently ignore this operation if data already exists.
* `error` or `errorifexists` (default case): Throw an exception if data already exists.

### collect() and take()

To print all elements on the driver, one can use the collect() method to first bring the RDD to the driver node 
thus: rdd.collect().foreach(println). This can cause the driver to run out of memory, though, because collect() 
fetches the entire RDD to a single machine; if you only need to print a few elements of the RDD, 
a safer approach is to use the take(): rdd.take(100).foreach(println).

## Spark Broadcast and Accumulators

### Broadcast

With distributed computing, sometimes you want to share variables and dataframes using __broadcast__. Keep in mind
that these variables and dataframes have to fit in memory on a single machine so keep it small. Broadcast
variables are immutable (if you need something that changes, then use an __accumulator__). Basically, use broadcast
variables as "static lookup tables".

An example use case might be if you had an app like Foursquare, you might need to know what neighborhoods are
around. You can ship around the small 'Neighborhood' table to each node in the cluster (instead of say doing a join
on the checkin table with the neighborhood table because of __shuffle__).

The broadcast variable is handled by a torrent-like protocol, where the variable is distributed as quickly and
efficiently as possible by downloading what they can and uploading what they can (as opposed to one node
having to try to do everything and push the data to all other nodes).

### Accumulators

__Accumulators__ are variables that are "added" to through an associative and commutative "add" operation (meaning
operations like `sum` and `max` satisfy the conditions, but not `average`). They are a container for accumulating 
partial values across multiple tasks (running on executors). Basically, accumulators aggregate information across executors.
For example, we might have:

    Accumulable     Value
    counter         45

So why do we need them? Why not just have a variable? Remember that we're doing distributed computing so when Spark 
ships code to every executor, the variables become local to that node. Any updates to that variable is not relayed
back to the driver. We need to make accumulators so we can get the values back from each executor.

You can see each executor running in the Web UI and what those accumulators are (e.g. Counter: 1, Counter: 17).
Workers only have write-only data access. Accessing the value in the accumulator is only allowed by the driver.

One thing to note is that since Spark is evaluated lazily, we need an __action__ to happen otherwise the
transformation will not be executed (e.g. a `map()` is a lazy transformation so accumulator update is not 
guaranteed if inside a map). Spark guarantees to update accumulators inside actions only once.
If a task is restarted, there may be undesirable side effects like accumulators being updated more than once.

### Apache Toree

TODO: Get this part working

https://toree.apache.org/docs/current/user/installation/

Apache Toree is a kernel for the Jupyter Notebook platform providing interactive access to Spark

    pip install toree

This installs a jupyter application called `toree`, which can be used to install and configure
different Apache Toree kernels.

    jupyter toree install --spark_home=/usr/local/bin/apache-spark/

Check that it's installed with:

    jupyter kernelspec list
    Available kernels:
      python2                 /home/will/.local/share/jupyter/kernels/python2
      apache_toree_pyspark    /usr/local/share/jupyter/kernels/apache_toree_pyspark
      apache_toree_scala      /usr/local/share/jupyter/kernels/apache_toree_scala
      spark_pyspark           /usr/local/share/jupyter/kernels/spark_pyspark
      spark_scala             /usr/local/share/jupyter/kernels/spark_scala

Toree is started using the `spark-submit` script. You can add in configurations with:

    --jar-dir where your jar directory is

    jupyter toree install --replace --spark_home=$SPARK_HOME --kernel_name="Spark" --spark_opts="--master=local[*]" --interpreters PySpark

I actually went and git cloned the project since I had a more recent release:

    git clone git@github.com:apache/incubator-toree.git
    cd incubator-toree
    APACHE_SPARK_VERSION=2.2.2 make pip-release
    pip3 install dist/toree-pip/toree-0.3.0.dev1.tar.gz


