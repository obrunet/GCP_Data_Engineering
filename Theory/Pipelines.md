# Data Pipelines Design

= sequences of operations that copy, transform, load & analyze data

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

## Components

### Cloud Pub/Sub
- a real time messaging service that suppors both push & pull suscription models
- managed service : requires no provisionning of servers or clusters
- automatically scale and partition load as needed

Messages queues are used in distributed systems to decouple services in a pipeline -> allow one service to produce more output than the consuming service can process which is not affected (helpfull when one process is subject to spikes in workload)
- Topic creation (logical structure for organizing your messages)
- Subscription creation to a topic (logical structure for organizing the reception of messages by consuming processes)
- Then message publication to the topic

subscription models:
- push deliver the msg to an endpoint
- pull : when you want the the consuming app to control when msg are retrieved from a topic.

NOT FINISHED !!!!







Messaging queues are used in distributed systems to decouple services in a pipeline. This allows one service to produce more output than the consuming service can process without adversely affecting the consuming service. This is especially helpful when one process is subject to spikes in workload.

When working with Cloud Pub/Sub, you create a topic, which is a logical structure for organizing your messages. Once a topic is created, you create a subscription to the topic and then publish messages to the topic. Subscriptions are a logical structure for organizing the reception of messages by consuming processes.

When messaging queues receive data in a a message, it is considered a publication event. Upon publication, push subscriptions deliver the message to an endpoint. Some common types of endpoints are Cloud Functions, App Engine, and Cloud Run services. Pull subscriptions are used when you want the consuming application to control when messages are retrieved from a topic. Specifically, with pull subscriptions you send a request asking for N messages, and Cloud Pub/Sub responds with the next N or fewer messages.

Topics can be created in the console or the command line. The only required parameter is a topic ID, but you can also specify whether the topic should use a Google-managed key or a customer-managed key for encryption. The command to create a topic is gcloud pubsub topics create; for example:


gcloud pubsub topics create pde-topic-1
creates a topic called pde-exam-topic-1. Subscriptions can be created in the console, or with the gcloud pubsub subscriptions create command and specifying a topic ID and a subscription ID; for example:


gcloud pubsub subscriptions create --topic pde-topic-1 pde-subscripton-1
Messages can be written to topics using APIs and client libraries as well as a gcloud command. Processes that write messages are called publishers or producers; services that read messages are called subscribers or consumers.

Client libraries are available for a number of languages, including C#, Go, Java, Node.js, PHP, Python, and Ruby. (Additional languages may have been added by the time you read this.) Cloud Pub/Sub also supports REST APIs and gRPC APIs. The command-line tool is useful for testing your ability to publish and consume messages from a topic. For example, to publish a message with a string, you could issue the following:


gcloud pubsub topics publish pde-topic-1 --message "data engineer exam"
This command inserts or publishes the message to the pde-topic-1 topic, and the message is available to be read through a subscription. By default, when a topic is created, it is done so as a pull subscription. The gcloud command to read a message from a topic is gcloud pubsub subscriptions; for example:


gcloud pubsub subscriptions pull --auto-ack pde-subscripton-1
The auto-ack flag indicates that the message should be acknowledged automatically. Acknowledgments indicate to the subscription that the message has been read and processed so that it can be removed from the topic. When a message is sent but before it is acknowledged, the message is considered outstanding. While a message is outstanding to that subscriber, it will not be delivered to another subscriber on the same subscription. If the message is outstanding for a period of time greater than the time allowed for a subscriber to acknowledge the message, then it is no longer considered outstanding and will be delivered to another subscriber. The time allowed for a subscriber to acknowledge a message can be specified in the subscription command using the ackDeadline parameter. Messages can stay in a topic for up to seven days.

Pub/sub makes no guarantees that the order of message reception is the same as the publish order. In addition, messages can be delivered more than once. For these reasons, your processing logic should be











### Dataproc

Use Dataproc (in Spark) if you're migrating from Hadoop

### Dataflow
- a managed stream and batch processing service.
- core component for building pipelines taht collect, transform, and output data
- pipelines are written using the Apache Beam API




---
or Datafow (similar to Apache Beam) for new processes
serverless
provides a unified batch and stream processing model 
Java or Python
Piplines
PCollection
Ptransform