# Data Pipelines Design

= sequences of operations that copy, transform, load & analyze data

- [Overview](Theory/Pipelines.md#overview)
    - [Stages](Pipelines.md#stages)
    - [Types of pipelines](Pipelines.md#types)
- [Components](Pipelines.md#components)
    - [Cloud Pub/Sub](Pipelines.md#cloud-pubsub)
    - [Cloud Composer](Pipelines.md#cloud-composer)
    - [Cloud DataFlow](Pipelines.md#dataflow)
    - [Cloud DataProc](Pipelines.md#dataproc)


## Overview

- abstract concept = dataflow from one stage of processing to another
- DAG : a set of nodes linked by edges, cycles are not allowed

### Stages

stage = one or more nodes / an edge = a flow 

4 types of stages : 
- ingestion : in either batch or streaming mode
- transformation : mapping data from one structure to another (conversion, substitution, aggregation, augmentation, selection / filtering, addition...)Dataflow & Dataproc are used for transformation, Dataprep for interactive review) 
- storage : in a staging area, for long-term, for analysis...
- analysis : analytics, ML, database  filling...

Example

![Example](pictures/pipeline_example.png "Example")

### Types

- Data warehousing pipelines
for storing data from multiple sources, organized in a dimensional data model ie denormalized intentionally because the purpose of a DW is to anwzer analytic.
    - ETL : "clean" data is stored
    - ELT : data is loaded into a db before transformation, the db'll contain the original data, SQL queries can be used for transformation
    - EL : when no change is required
    - Change data capture : each change is caputred and recodred in a data store : helpful in cases where it is important to know all transformation over time.


- Steaming processing pipelines
undeing, continuously, may not need to process & anlayze the data as soon as possible. The difference with batch is the way that you group the data. You need to pick subsets of the stream to work on at any one time. Consider serveral factors :
    - __Event time__ (the time that the event occurs) & __processing time__ (when data arrives at endpoint for ingestion) -> it is important to use one of these times consistently for ordering the streams
    - __Sliding__ (a set of consecutive points) & __tumbling__ (move forward by the width of the window) __windows__
    - __Late arriving data & watermarks__ : you've to decide how long you'll wait for data to arrive. A watermark is basically a timestamp indicating taht no data older than that timestamp will ever appear. Windows are just small batches & watermarks indicate a boundary on the lateness of data. 
    - Missing data

- Hot path (make data available as oon as possible) & Cold path ingestion :

    ![Example](pictures/hot_path.png "Example")

- ML pipelines
    - ingestion
    - preprocessing (equivalent to transformation in data warehousing pipes)
    - feature engineering
    - model training & evaluation
    - deployment

--- 

## Components


### CLOUD PUB/SUB
- a real time messaging service that suppors both push & pull suscription models
- managed service : requires no provisionning of servers or clusters
- automatically scale and partition load as needed

Messages queues are used in distributed systems to decouple services in a pipeline -> allow one service to produce more output than the consuming service can process which is not affected (helpfull when one process is subject to spikes in workload)

Working with Pub/Sub - in order :
- Topic creation (logical structure for organizing your messages)
- Subscription creation to a topic (logical structure for organizing the reception of messages by consuming processes)
- Then message publication to the topic

subscription models:
- push deliver the msg to an endpoint (Cloud Functions, App Engine, and Cloud Run services...)
- pull : when you want the the consuming app to control when msg are retrieved from a topic - you send a request asking for N msgs & Pub/Sub responds with the next N or fewer msgs

Processes that write messages are publishers or producers; services that read messages are subscribers or consumers.

Client libraries includes C#, Go, Java, Node.js, PHP, Python, and Ruby... 

Also supports REST APIs and gRPC APIs.

The command-line tool is useful for testing your ability to publish and consume messages from a topic.

Acknowledgments (for instance "auto-ack") indicate to the subscription that the message has been read & processed so that it can be removed from the topic. 

When a message is sent but before it is acknowledged, the message is considered outstanding. While a message is outstanding to that subscriber, it will not be delivered to another subscriber on the same subscription. If the message is outstanding for a period of time greater than the time allowed for a subscriber to acknowledge the message (ackDeadline parameter), then it is no longer considered outstanding and will be delivered to another subscriber. Messages can stay in a topic for up to seven days.

- no guarantees that the order of message reception is the same as the publish order. 
- messages can be delivered more than once -> your processing logic should be idempotent

If you need guaranteed exactly once processing, use Cloud Dataflow PubsubIO, which de-duplicates based on a message ID. Cloud Dataflow can also be used to ensure that messages are processed in order.

__Open Source Alternative: Kafka__

Kafka is used to publish and subscribe to streams of messages and to reliably store messages in a fault-tolerant way. It runs in a cluster of one or more servers. You interact with Kafka programmatically 

- Pub/Sub can be used for migrating an on-premises service that uses Kafka
- if you plan to continue to use Kafka, you can link it to Cloud Pub/Subusing the CloudPubSubConnector (a bridge between the two messaging systems using Kafka Connect)


### CLOUD COMPOSER

- a managed service implementing Apache Airflow
- used for scheduling and managing workflows (framework for more resilient and complex pipelines handling errors & other exceptional cases).
- automates the scheduling & monitoring of workflows
- workflows are defined in Python with DAGs
- built-in integration with BigQuery, Dataflow, Dataproc, Datastore, Storage, Pub/Sub & AI Platform.


First you will need to create :
- an environment in GCP (environments run on the GKE -> specify a nb of nodes, location, machine type, disk size, & other node and network configuration parameters). 
- a Cloud Storage bucket as well.


### DATAFLOW
- a managed stream & batch processing service
- core component for building pipelines that collect, transform, and output data
- pipelines are written using the Apache Beam API
- Apache Beam incorporates Beam runners. The Cloud Dataflow runner is commonly used in GCP. Apache Flink is another commonly used Beam runner.
- does not require you to configure instances or clusters (no-ops service) - - it directly integrates with Cloud Pub/Sub, BigQuery, and the Cloud ML Engine. Cloud Dataflow integrates with Bigtable and Apache Kafka.
- much of your work : coding transformations in Java or Python

__Cloud Dataflow concepts__

See also the Windowing concepts & watermarks described earlier.
- Pipelines:
    - a series of computations applied to data
    - result of the previous computations become the input for the next one
    - represent a job that can be run repeatedly.
- PCollection = abstraction of a dataset (batch processing = bounded / streaming data = unbounded)
- Transforms
    - operations that map input data (source) to some output data (sink).
    - operate on one or more PCollections as input & can produce one or more output PCollections
    - math calculations, type conversions, grouping/splitting, read & write ops.
- ParDo = a parallel processing op on each element in a PCollection
    - may also receive a side input used to perform joins
    - similarly, side outputs can be useful when you want to have additional processing paths (for example for data that does not pass some validation check).
- Pipeline I/O = transforms for reading data into a pipeline from a source and writing data to a sink
- Aggregation = process of computing a result from multiple input values
- User-defined functions (UDF) = user-specified code for performing some operation, typically using a ParDo
- Runner = software that executes pipelines as jobs
- Triggers = functions that determine when to emit an aggregated result. In batch = when all the data has been processed / On a stream, you have to specify a window over the stream to define a bounded subset (in the window conf)

__Jobs & Templates__

Jobs can be run from the cmd line or through the use of APIs.

job = an executing pipeline. 2 ways:
- the traditional method : developers create a pipeline in a dev env & run the job from that env.
- the template method separates dev from staging & execution. Devs still create pipelines in a dev env, but also a template (a configured job specification). Google provides a number of templates & you can create your own)


### CLOUD DATAPROC

- makes it easy to migrate from on-premises
- a managed Hadoop & Spark service - a preconfigured cluster is created in 90s with commonly used components including: Hadoop / Spark / Pig (a compiler that produces map reduce programs from a high-level language) / Hive
- you can use “ephemeral” clusters i.e destroyed once the task is over in order to save costs

__Managing Data in Cloud Dataproc__

On premise, you store data on the Hadoop cluster using HDFS (good approach when you have dedicated hardware). In the cloud, instead of having a single, long-running Hadoop cluster, you typically start a Cloud Dataproc cluster for each job and shut it down when the job completes. This is a better option.

No need to copy your data from Cloud Storage to HDFS each time. A better approach is to use Cloud Storage as the data store (it saves time & money).

__Configuration__

2 types of nodes: 
- master nodes : responsible for distributing & managing workload distribution. Runs YARN
- worker nodes : can include some preemptible machines without HDFS

You can specify a different machine conf for both the master & worker nodes. Several modes :

- dev mode = a single node 
- standard mode = one master & some number of worker nodes
- H.A mode = 3 master nodes & some number of workers. 

Once created, a cluster can be scaled up or down. Only the nb of workers nodes can change (the masters are fixed) either manually command line or automatically by creating a policy : a YAML file includes various parameters (
maxInstances, scaleUpFactor, scaleDownFactor, cooldownPeriod, itworks by checking Hadoop YARN metrics)

- Submitting a Job : you can use an API, a gcloud command, or in the console. 
- Ggood practice to keep clusters in the same region as the Cloud Storage buckets (better I/O perfs).


__Migrating Hadoop & Spark to GCP__

It can happen incrementally, especially since you will be using ephemeral clusters configured for specific jobs. 

- First step, __Migrating data__ to Cloud Storage
- Then __Migrating jobs__ : deploy ephemeral clusters to run jobs that use that data (start with low-risk jobs to get familiar)

In some cases you will have to keep an on-premises cluster while migrating some jobs and data to GCP --> keep data synchronized between environments with dedicated workflows.

You should have a way to determine which jobs and data move to the cloud and which stay on premises.

- __Migrating HBase to Bigtable__ (a good practice)
    - export HBase tables to sequence files and copy those to Cloud Storage
    - next, import the sequence files using Cloud Dataflow 
        - if > 20 TB use the Transfer Appliance
        - if < 20 TB &  network bandwidth > 100 Mbps, then distcp (a Hadoop distributed copy command)
        - it is important to know how long the transfer will take to keep data in sync with a mechanism.