# Hadoop in Docker

This is a fork of [github.com/big-data-europe/docker-hadoop](https://github.com/big-data-europe/docker-hadoop)

[Original README file is here](README-copy.md)

## Quick Start

### Single Datanode Version

```bash
# start
$   docker-compose up -d

$   docker-compose ps

# stop
$   docker-compose down
```

### Multiple Datanodes

This one launches 3 datanodes.

```bash
# start
$   docker-compose -f docker-compose-multi-dn.yml  up -d

$   docker-compose -f docker-compose-multi-dn.yml  ps

# down
$   docker-compose -f docker-compose-multi-dn.yml  down
```

## UIs

* [Namenode UI](http://localhost:9870)  - port 9870
* [YARN Resource Manager UI](http://localhost:8088)  - port 8088
* [History Server UI](http://localhost:8188) - port 8188

## Reset

To completely reset the system, you need to shutdown and remove the volumes

```bash
# notice the -v flag
$    docker-compose -f docker-compose-multi-dn.yml   down -v
```

## Logging into Namenode

```bash
$   docker-compose -f docker-compose-multi-dn.yml  exec namenode bash
```

This will drop you into namenode

Hadoop is in `/opt`.  

```bash
$   echo $HADOOP_HOME
# /opt/hadoop-3.2.1/
```

You can try the following commands in Namenode container

```bash
# In NAMENODE> 

$   hdfs dfsadmin -report

$   hdfs dfs -ls /

$   hdfs  dfs -mkdir /a

$   hdfs dfs -put /etc/hosts   /a/hosts

$   hdfs dfs -ls /a/

# check the file
$   hdfs dfs -cat  /a/hosts
```

### Inspecting the files:

* Go to `Namenode UI --> Utilities --> Browse File System`
* Drill down to the file just copied `/a/hosts`
* Inspect the file block reports

## Running Example Programs

**Note: all the commands below are run on Namenode terminal**

### Step-1: See Example Programs

```bash
# this will show you all available examples
$   hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar
```

### Step-2: Let's calculate PI

```bash
$   hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar  pi  4 100
```

This will print out output like this

```console
2022-06-23 07:27:30,980 INFO mapreduce.Job:  map 0% reduce 0%
2022-06-23 07:27:36,032 INFO mapreduce.Job:  map 25% reduce 0%
2022-06-23 07:27:37,035 INFO mapreduce.Job:  map 50% reduce 0%
2022-06-23 07:27:38,039 INFO mapreduce.Job:  map 75% reduce 0%
2022-06-23 07:27:39,042 INFO mapreduce.Job:  map 100% reduce 0%
2022-06-23 07:27:41,056 INFO mapreduce.Job:  map 100% reduce 100%

...

Estimated value of Pi is 3.17000000000000000000
```

### Step-4: Inspect YARN UI

Go to [YARN Resource Manager UI](http://localhost:8088) and inspect the running applications.

### Step-5: Run wordcount example

**Copy a sample file**

```bash
$   hdfs dfs -mkdir -p  /wordcount/in
$   hdfs dfs -put   $HADOOP_HOME/README.txt   /wordcount/in/
$   hdfs dfs -ls /wordcount/in/
```

**Run wordcount**

```bash
$    hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar  wordcount  /wordcount/in  /wordcount/out
```

The output will look like 

```console
2022-06-23 07:41:48,948 INFO mapreduce.Job:  map 0% reduce 0%
2022-06-23 07:41:54,020 INFO mapreduce.Job:  map 100% reduce 0%
2022-06-23 07:41:58,049 INFO mapreduce.Job:  map 100% reduce 100%
2022-06-23 07:41:58,054 INFO mapreduce.Job: Job job_1655967836105_0003 completed successfully
```

**Inspect the output**

```bash
$   hdfs dfs -ls /wordcount/out/
```

The output will look like

```console
hdfs dfs -ls /wordcount/out/
Found 2 items
-rw-r--r--   3 root supergroup          0 2022-06-23 07:41 /wordcount/out/_SUCCESS
-rw-r--r--   3 root supergroup       1301 2022-06-23 07:41 /wordcount/out/part-r-00000
```

**Inspect the output**

```bash
$   hdfs dfs -cat '/wordcount/out/*'
```

Output may look like the follows:

```console
Administration  1
Apache  1
BEFORE  1
BIS     1
Bureau  1
Commerce,       1
...
```
