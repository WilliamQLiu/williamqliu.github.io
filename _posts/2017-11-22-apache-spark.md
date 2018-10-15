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


Run this otherwise you'll get permission denied when trying to ssh into your own server

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

This is a good post to get your pyfiles working:

    https://stackoverflow.com/questions/36461054/i-cant-seem-to-get-py-files-on-spark-to-work

#### Python Additional Files (eggs)

If you're sending an egg, you can build it with:

    python setup.py bdist_egg

You'll see this in a `dist` folder with a name like `myprogram-0.0.1-py3.6egg`.
Make sure that the computer running the egg is using the same version of python 
as the computer that built the egg.

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

### Parquet with Spark

When you're writing parquet for use by Spark, make sure to have the option `use_deprecated_int96_timestamps=True`.

    pq.write_to_dataset(my_pyarrow_table, root_path=root_path, partition_cols=['year', 'month', 'day'], use_deprecated_int96_timestamps=True)

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

An example code where you write data to a Pandas DataFrame, then convert this to a PyArrow Table and write to Parquet is:

    import arrow
    import numpy as np
    import pandas as pd
    import pyarrow as pa
    import pyarrow.parquet as pq
    import requests
    from pandas import Series


    def get_api_endpoint(url: str) -> pd.DataFrame:
        """ Get API endpoint and convert to DataFrame
        Example:
        url: https://swapi.co/api/starships/9/
        data:
            +(Pdb) print(d)
            {'MGLT': '10', 'cargo_capacity': '1000000000000',
            'films': ['https://swapi.co/api/films/1/'],
            'manufacturer': 'Imperial Department of Military Research, Sienar Fleet Systems',
            'starship_class': 'Deep Space Mobile Battlestation',
            'created': '2014-12-10T16:36:50.509000Z',
            'model': 'DS-1 Orbital Battle Station',
            'url': 'https://swapi.co/api/starships/9/',
            'consumables': '3 years', 'hyperdrive_rating': '4.0',
            'crew': '342953', 'name': 'Death Star', 'max_atmosphering_speed': 'n/a',
            'edited': '2014-12-22T17:35:44.452589Z', 'length': '120000',
            'pilots': [], 'cost_in_credits': '1000000000000', 'passengers': '843342'}
        df:
            df.columns
            Index(['MGLT', 'cargo_capacity', 'consumables', 'cost_in_credits', 'created',
                  'crew', 'edited', 'films', 'hyperdrive_rating', 'length',
                  'manufacturer', 'max_atmosphering_speed', 'model', 'name', 'passengers',
                  'pilots', 'starship_class', 'url'],
                 dtype='object')
            +(Pdb) df.head()
             MGLT cargo_capacity consumables cost_in_credits  \
            0   10  1000000000000     3 years   1000000000000 ...
        """
        r = requests.get(url)
        d = r.json()
        return pd.DataFrame(dict([(k, Series(v)) for k, v in d.items()]))


    def df_add_partition_columns(df, date_field):
        """ Return a dataframe with new columns used for partitioning by datetime
        Example: 2018-03-04T14:12:15.653Z returns with new df columns of 'year', 'month', day'
        """
        df[date_field] = df[date_field].map(lambda t: pd.to_datetime(t, format="%Y-%m-%dT%H:%M:%S.%fZ"))
        df['year'], df['month'], df['day'] = df[date_field].apply(lambda x: x.year), df[date_field].apply(lambda x: x.month), df[date_field].apply(lambda x: x.day)
        return df


    def df_to_parquet_table(df: pd.DataFrame) -> pa.Table:
        """ Convert DataFrame to Pyarrow Table
        Example:
        pyarrow.Table
        MGLT: string
        cargo_capacity: string
        consumables: string
        cost_in_credits: string
        created: string
        crew: string
        edited: string
        films: string
        hyperdrive_rating: string
        length: string
        manufacturer: string
        max_atmosphering_speed: string
        model: string
        name: string
        passengers: string
        pilots: double
        starship_class: string
        url: string
        __index_level_0__: int64
        metadata
        --------
        {b'pandas': b'{"columns": [{"field_name": "MGLT", "pandas_type": "unicode", "m'
                b'etadata": null, "name": "MGLT", "numpy_type": "object"}, {"field'
                b'_name": "cargo_capacity", "pandas_type": "unicode", "metadata": '
                b'null, "name": "cargo_capacity", "numpy_type": "object"}, {"field'
                b'_name": "consumables", "pandas_type": "unicode", "metadata": nul'
                b'l, "name": "consumables", "numpy_type": "object"}, {"field_name"'
                b': "cost_in_credits", "pandas_type": "unicode", "metadata": null,'
                b' "name": "cost_in_credits", "numpy_type": "object"}, {"field_nam'
                b'e": "created", "pandas_type": "unicode", "metadata": null, "name'
                b'": "created", "numpy_type": "object"}, {"field_name": "crew", "p'
                b'andas_type": "unicode", "metadata": null, "name": "crew", "numpy'
                b'_type": "object"}, {"field_name": "edited", "pandas_type": "unic'
                b'ode", "metadata": null, "name": "edited", "numpy_type": "object"'
                b'}, {"field_name": "films", "pandas_type": "unicode", "metadata":'
                b' null, "name": "films", "numpy_type": "object"}, {"field_name": '
                b'"hyperdrive_rating", "pandas_type": "unicode", "metadata": null,'
                b' "name": "hyperdrive_rating", "numpy_type": "object"}, {"field_n'
                b'ame": "length", "pandas_type": "unicode", "metadata": null, "nam'
                b'e": "length", "numpy_type": "object"}, {"field_name": "manufactu'
                b'rer", "pandas_type": "unicode", "metadata": null, "name": "manuf'
                b'acturer", "numpy_type": "object"}, {"field_name": "max_atmospher'
                b'ing_speed", "pandas_type": "unicode", "metadata": null, "name": '
                b'"max_atmosphering_speed", "numpy_type": "object"}, {"field_name"'
                b': "model", "pandas_type": "unicode", "metadata": null, "name": "'
                b'model", "numpy_type": "object"}, {"field_name": "name", "pandas_'
                b'type": "unicode", "metadata": null, "name": "name", "numpy_type"'
                b': "object"}, {"field_name": "passengers", "pandas_type": "unicod'
                b'e", "metadata": null, "name": "passengers", "numpy_type": "objec'
                b't"}, {"field_name": "pilots", "pandas_type": "float64", "metadat'
                b'a": null, "name": "pilots", "numpy_type": "float64"}, {"field_na'
                b'me": "starship_class", "pandas_type": "unicode", "metadata": nul'
                b'l, "name": "starship_class", "numpy_type": "object"}, {"field_na'
                b'me": "url", "pandas_type": "unicode", "metadata": null, "name": '
                b'"url", "numpy_type": "object"}, {"field_name": "__index_level_0_'
                b'_", "pandas_type": "int64", "metadata": null, "name": null, "num'
                b'py_type": "int64"}], "column_indexes": [{"field_name": null, "pa'
                b'ndas_type": "unicode", "metadata": {"encoding": "UTF-8"}, "name"'
                b': null, "numpy_type": "object"}], "pandas_version": "0.22.0", "i'
                b'ndex_columns": ["__index_level_0__"]}'}
        """
        pyarrow_deathstar_table = pa.Table.from_pandas(df)  # Create PyArrow Table from Pandas DF
        print(pyarrow_deathstar_table)
        pq.write_table(pyarrow_deathstar_table, 'deathstar.parquet')  # Convert PyArrow Table to Parquet Table / File
        parquet_table = pq.read_table('deathstar.parquet')  # Read back Parquet File as a Table
        parquet_table = pq.ParquetFile('deathstar.parquet')  # Read back Parquet File as a ParquetFile for finer-grained read and write
        print(parquet_table.metadata)
        #<pyarrow._parquet.FileMetaData object at 0x7fb755c29458>
        #  created_by: parquet-cpp version 1.4.1-SNAPSHOT
        #  num_columns: 19
        #  num_rows: 1
        #  num_row_groups: 1
        #  format_version: 1.0
        #  serialized_size: 4574

        print(parquet_table.schema)
        #<pyarrow._parquet.ParquetSchema object at 0x7efc80565408>
        #MGLT: BYTE_ARRAY UTF8
        #cargo_capacity: BYTE_ARRAY UTF8
        #consumables: BYTE_ARRAY UTF8
        #cost_in_credits: BYTE_ARRAY UTF8
        #created: BYTE_ARRAY UTF8
        #crew: BYTE_ARRAY UTF8
        #edited: BYTE_ARRAY UTF8
        #films: BYTE_ARRAY UTF8
        #hyperdrive_rating: BYTE_ARRAY UTF8
        #length: BYTE_ARRAY UTF8
        #manufacturer: BYTE_ARRAY UTF8
        #max_atmosphering_speed: BYTE_ARRAY UTF8
        #model: BYTE_ARRAY UTF8
        #name: BYTE_ARRAY UTF8
        #passengers: BYTE_ARRAY UTF8
        #pilots: DOUBLE
        #starship_class: BYTE_ARRAY UTF8
        #url: BYTE_ARRAY UTF8
        #__index_level_0__: INT64
        return parquet_table


    def write_parquet_table_as_partitioned_dataset(parquet_file) -> pq.ParquetDataset:
        """ Write a parquet table as a parititioned dataset (i.e. multiple Parquet files)
        An example of a dataset partitioned by year and month on disk might look like:
            dataset_name/
                year=2018/
                    month=09/
                        0.parq
                        1.parq
                    month=10/
                        0.parq
                        1.parq
        """
        parquet_table = pq.read_table(parquet_file)  # Read back Parquet File as a Table
        #pq.write_to_dataset(parquet_table, root_path='starships', partition_cols=['created'])
        pq.write_to_dataset(parquet_table, root_path='starships', partition_cols=['year', 'month', 'day'], flavor='spark')
        dataset = pq.ParquetDataset('starships')
        return dataset


    if __name__ == '__main__':

        # Basics of get request, save to DataFrame, PyArrow Table, Parquet File
        df_deathstar = get_api_endpoint('https://swapi.co/api/starships/9/')
        df_deathstar = df_add_partition_columns(df_deathstar, 'created')
        parquet_deathstar_table = df_to_parquet_table(df_deathstar)

        # Write to and Read from Partitioned Datasets
        write_parquet_table_as_partitioned_dataset('deathstar.parquet')

Note: use "flavor='spark'" on writing data so that the data will work with Spark's constraints on types of files it can read.
It'll automatically sanitize field characters unsupported by Spark SQL.

### Parquet Tools

https://github.com/apache/parquet-mr

Java version:

    https://github.com/apache/parquet-mr/tree/master/parquet-tools
    sudo apt-get install maven
    cd parquet-tools && mvn clean package -Plocal

Python version:

    sudo apt-get install libsnappy-dev
    pip3 install python-snappy
    pip3 install parquet

Can then run with:

`parquet 7026c2af982a4564a92c7602b43976cf.parquet` - see file
`parquet --metadata 7026c2af982a4564a92c7602b43976cf.parquet` -- see metadata about the file (e.g. schema)
`parquet --limit 2 7026c2af982a4564a92c7602b43976cf.parquet` -- see data (limited)

### Parquet with S3

With a dataframe, just write your parquet to an S3 bucket like so:



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

### PySpark Schema

So how does data types work with schemas? You might setup a `StructType` as your own schema,
with a bunch of `StructFields`.

	from pyspark.sql.types import StringType, StructField, StructType, BooleanType, ArrayType, IntegerType, DateType, TimestampType	
	
    schema = StructType([  # StructType is a data type for a collection of StructFields
	            StructField(name='my_field', dataType=IntegerType(), nullable=False, metadata={'master': 'a_field'}),
				StructField(name='another_field', dataType=StringType(), nullable=False, metadata={'master': 'b_field'})
	])

Be aware that for different connectors, e.g. JDBC, the defaults might not be what you're expecting.
For example, a StructField with `dataType=StringType()` might have a default of `text` instead of
a `varchar`. You can change these settings, but will have to modify your jdbc connection config
and then enter in a maxlength into your field's metadata.

### How are data types used?

Say you make a user defined function, you can specify a returned field type.

    from pyspark.sql.types import FloatType

    def square_float(x):
        return float(x**2)

    square_udf_float2 = udf(lambda z: square_float(z), FloatType())

You can also use field types in things like schemas.

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

You can join dataframes with:

	# inner join on one field
    df_left.join(df_right, df_left.my_field == df_right.my_field, how='inner')

	# inner join with multiple columns (use `&` for 'and' logic, `|` for 'or' logic)
	df_left.join(df_right, (df_left.my_field_a == df_right.my_field_a) &
                           (df_left.my_field_b == df_right.my_field_b))

    # Multiple Joins
    joined_df = df_left.join(df_right, "my_field", "left") \
                       .join(df_another, "my_field", "left")

You can specify the how with:

    inner, cross, outer, full, full_outer, left, left_outer, right, right_outer, leftsemi, and leftanti

If you joined with `left`, `right`, you'll get columns from both dataframes, even
if the column names match. In terms of SQL, it looks like this:

	SELECT ta.*, tb.*
	FROM ta
	INNER JOIN tb
	ON ta.name = tb.name

If you want to select one of these fields, you'll have to run something like:

	left_join = ta.join(tb, ta.name == tb.name, how='left')
	left_join.filter(col('tb.name').isNull()).show()  # notice how we have to specify which table alias

Make sure to apply an alias to the dataframe, otherwise you’ll receive an error
after you create your joined dataframe. With two columns named the same thing,
referencing one of the duplicate named columns returns an error that
essentially says it doesn’t know which one you selected.

    df_m = df_master.alias('df_m')
    df_n = df_new.alias('df_n')

Without an alias, you'll get an error like:

	TaskSetManager: Lost task 8.0 in stage 14.0 (TID 215) on 172.23.0.7, executor 0: java.sql.BatchUpdateException (Column 'my_field_a' specified twice) [duplicate 1]

If you didn't want columns from both dataframes, then you can use `how` = `leftsemi` or `rightsemi` (if you're doing
a join where you want rows only if criteria meets both dataframes).

Another way is that you can run your regular join, then select columns from a specific dataframe.

    joined_df = df_n.join((df_n.other_id == df_m.other_id), how='left_outer').select('df_m.*')

You can also run your regular join, then select certain columns from both dataframes.

    joined_df = df_n.join((df_n.other_id == df_m.other_id), how='left_outer').select('df_m.*', 'df_n.some_field')

### Modes

mode - specifies the behavior of the save operation when data already exists.

* `append`: Append contents of this DataFrame to existing data.
* `overwrite`: Overwrite existing data (i.e. rewrite the entire table) - be careful with this,
  as sometimes it'll be just a `truncate table`, while other dbs might drop the table and completely
  recreate with indices and foreign keys lost (see option `truncate`)
* `ignore`: Silently ignore this operation if data already exists.
* `error` or `errorifexists` (default case): Throw an exception if data already exists.

#### Append Mode

So the most common mode is `append`. But what happens if we run into the following scenarios?

* say Table X has columns `a, b, c` and we try to write `a, b, c, d`, we get an error saying col `d` is not in the schema
* If we `append` our dataframe (say we have `a, b, c` columns) and our table has `a, b, c, d`, we're okay and will
  only write `a, b, c` (e.g. say `d` is a timestamp field with update now)
* If we `append` to a nonexistent table, a table will be created (but be warned, if you don't have types
  created yet, everything will be a `text` type if you're using MySQL)
* If we have a database constraint on say MySQL (e.g. needs a default value) and our dataframe does not
  have it, then the job will fail with an error message like 'Field `my_required_field` does not have a default value)

#### Upserts

One of the things you'll notice is that there is no Spark mode for UPSERTS. You'll
need to handle that logic either in the app code or through the database.

* App Code

For App Code, I run logic like this:

* Check if we need to run the upsert logic (e.g. if you're appending to an empty DataFrame, you can just append and
  ignore the upsert logic): `if (df_master.limit(1).count() > 0):`
* If there is existing data, we'll do a join:


    # Create an alias otherwise you'll have difficulty using the joined dataframe
    df_m = df_master.alias('df_m')
    df_n = df_new.alias('df_n')

    # Join and keep only the columns from the new dataframe (df_n), but keep rows that are in both (df_m and df_n),
    df_joined = df_n.join(df_m,
        (df_n.my_id == df_m.my_id) &
        (df_n.other_id == df_m.other_id), how='left') \
        .filter(df_m.my_id.isNull()) \
        .select(*[col('df_n.' + c) for c in df_n.columns])  # can also select('df_n.*')

    # Or: left outer join and only keep columns from the df_m dataframe
    df_joined = df_n.join(df_m,
        (df_n.my_id == df_m.my_id) &
        *df_n.other_id == df_m.other_id), how='left_outer').select('df_m.*')

    # Drop Duplicates, keeps first row (i.e. the one with the latest data based on the orderBy)
    df_joined = df_joined.orderBy("date_entered", ascending=False) \
                         .drop_duplicates(subset=['my_id', 'other_id'])

    # Write the joined dataframe
    df_joined.write.format('jdbc').options(
        url='jdbc:mysql://my_db_url',
        driver='com.mysql.jdbc.Driver',
        dbtable='my_db_table',
        user=db_'my_user',
        password='my_db_password') \
        .mode('append').save()

### collect() and take()

To print all elements on the driver, one can use the collect() method to first bring the RDD to the driver node 
thus: rdd.collect().foreach(println). This can cause the driver to run out of memory, though, because collect() 
fetches the entire RDD to a single machine; if you only need to print a few elements of the RDD, 
a safer approach is to use the take(): rdd.take(100).foreach(println).

### show()

To show what's inside your dataframe, just run: `df.show(n=5)`

## User Defined Functions

There's a lot of pyspark functions in the `pyspark.sql` module, from built-in functions to DataFrame methods.
However, there will be a time when you need to run some custom code on say a DataFrame; that's when you'll need
a __user defined function__ (aka __udf__).

A simple example might be:

    from pyspark.sql.functions import udf
    from pyspark.sql.types import IntegerType


    def squared(s):
        """ Simple Squared Method used for testing out UDFs """
        return s * s


    squared_udf = udf(squared, IntegerType())
    df= df.withColumn("my_number_squared", squared_udf("my_number"))  # Add a new column 'my_number_squared' and fill with udf


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

## Configuration Tuning

One of the issues with Map-Reduce apps is that data needs to be stored somewhere between each stage, which
wastes a lot of time on I/O operations. With Spark, everything is in memory, making it much quicker.

### Memory

As a developer, you need to understand how to make proper adjustments for spark's workflow, meaning you need
to understand how to allocate memory across:

* passing data between execution and storage
* passing data across tasks, running in parallel
* passing data across operators, running with the same task

#### Executor Memory

So let's look at an __executor__, which is Spark's JVM process that is launched on a worker node. Executors run tasks
in _threads_ and is responsible for keeping relevant partitions of data. Each process has an allocated _heap_ with
available memory (executor/driver).

With default executor configurations, we have `spark.executor.memory=1GB, spark.memory.fraction=0.6`, meaning we
will have about 350MB allocated for execution and storage regions. The other 40% is reserved for storing meta-data,
user data structures, safeguarding against OOM errors, etc. There is also a dedicated hard-coded portion of
_reserved memory_ (300 MB * 1.5) used for storing internal spark objects.

A Spark task operates in two main memory regions:

* __execution__ - used for shuffles, joins, sorts, and aggregations - 'short lived' memory, immediately evicted after
                  each operation, making space for the next ones
* __storage__ - used to cache partitions of data - handle the persistence of data (an RDD's `cache()` and `persist()` functions)
                Partitions can exist in memory or on the disk (can view in the 'Storage' tab in the Spark UI)

Beware that if you allocate too much memory, this will result in excessive garbage collection dleays. 64GB is a good
rough upper limit for a single executor. Also remember that a server's OS will take some room (e.g. about 1GB) so
don't overallocate the memory you have.

#### Off-heap

## Apache Toree

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


