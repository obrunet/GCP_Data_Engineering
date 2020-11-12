# Buidling & Operationalizing Storage Systems

- [Cloud SQL](Build_storage.md#cloud-sql)
- [Cloud Spanner](Build_storage.md#cloud-spanner)
- [Cloud Bigtable](Build_storage.md#cloud-bigtable)
- [Cloud Firestore](Build_storage.md#cloud-firestore)
- [BigQuery](Build_storage.md#bigquery)
- [Cloud Memorystore](Build_storage.md#cloud-memorystore)
- [Cloud Storage](Build_storage.md#cloud-storage)
- [Retention & lifecycle management](Build_storage.md#retention--lifecycle-management)


# Unmanaged Databases
= self-managed. You will be responsible for
- OS Updates & patch
- DB systme Updates & patch
- Backing up & recovery
- Network access conf
- Disk space management
- Monitoring performance & resource utilization
- HA & failovers conf / replicas management
The two Stackdriver components that are used with unmanaged databases.
Instances have built-in monitoring (CPU, RAM, I/O metrics) and logging (audit logs...)

You should install: 
- Stackdriver Monitoring (perf) 
- and Stackdriver Logging (apps / db logs) agents.

MySQL & PostgreSQL require a specific plug-in.



# Managed Databases

Does not require administaration & operational support. Google'll take careof core opetaional tasks including scaling.


## Cloud SQL

1. Characteristics
    - fully managed
    - relational DB : support MySQL, PostgreSQL, SQL Server (beta)
    - Storage : up to 30TB for one instance (if more consider Cloud Spanner)
    - Region : single (not multi) otherwise consider Cloud Spanner
    - Configuration
        - choose a RDBMS
        - instance Id & pw
        - region & zone
        - DB version
        - machine type (vCPUs, RAM, HDD or SSD)
        - public (by default) or private IP address
    - Backup : daily, user specify a 4 hour-tim window of maintenance
    - High Availability 
        - By default one instancein a single zone
        - Option : create a 2nd instancein a 2nd zone & synchronously replicate data from the primary instance to the standby one
    - Good Practice : shard your data into smaller DB rather than have a single monolithic DB (the way to shard depend on the use case)

2. Read performance Improvement
    -   By using replicas (a copy of the primary instance's data that is maintained in the same region), apps can read rom the read replica and allow the primary instance to handle write operations.
    - Read replicas not for HA
    - No load balancing between multiple replicas of an instance
    - No backup on a read replica
    - A replica can be promoted to a standalone Cloud SQL (irreversible op)
    - The primary instance cannot be restored from backup if a replica existas (it has to be promoted or deleted)

3. Import & export
    - Generally from & to Cloud Storage buckets (CSV or SQL dump files)
    - Export can be compressed : mysqldump, pg_dump (for postgre) & bcp (bulk copy program) for SQL Server. 


## Cloud Spanner

1. Characteristics
    - fully managed __???????????????__
    - relational DB with strong consistency
    - Scalable : horizontally
    - Configuration :
        - instance name & Id, pw
        - single or multi (more costly) regional - region in which to create the instance
        - Number of nodes (dictated by your workload, keep CPU < 65% a single region and < 45% for multi)
        - machine type (vCPUs, RAM, HDD or SSD)
        - public (by default) or private IP address
    - Backup : __???????????????__
    - High Availability : yes, but doesn't require failover instance in the same way Cloud SQL does. Automatic replication.

    - Good Practice : __???????????????__
    
2. Replication
    - Maintains multiple replicas of rows of data in multiple locations (implements globally synchronous replication)
    - You can read the latest data in a row from any replica
    - One of the replica is designated leader & is responsible for writing.
    - Use replicas for availability as well as reducing latency
    - But its distributed nature creates challenges for writing data : a voting mechanism is used for synchronization.
    - 3 types of replicas
        - read-write: maintain full copies & serve read ops, can vote on write ops
        - read-only: maintain full copies & serve read ops, can not vote
        - witness: do not keep full copies but participate in write votes (for the quorum)
    - regional instances use only read-only repl, multi-regional instances use all 3

3. DB design & considerations
    - can have hostposts where many I/O ops are happening on the smae node instead of multiple ones. When using sequentially primary keys (auto-incrementing counters or timestamps) -> Use hash of the sequentially value instead to distribute work.
    - relational DB are often normalized (you can make joins) __???????????????__
    Cloud Spanner allow for interleaving data form different talbes (advantage of parent-child relationship between talbes when creating the DB schema) __???????????????__


3. Import & export
    - from or to Cloud Storage
    - export in Apache AVRO or CSV formats, implemented by Coud Dataflow connector
    - performance factors
        - DB size
        - Nb of secondary indexes
        - Location
        - Load & number of nodes

## Cloud Bigtable

1. Characteristics
    - managed service but not a NoOps service like Cloud SQL or Cloud Spanner
    - wide-column NoSQL DB used for high-volume DB that require low latency (few ms) IoT, time-series, finance... 
    - Configuration :
        - instance name & Id, pw
        - storage type (HDD or SSD)
        - development or production cluster (with HA & requires a least 3 nodes)
        - cluster ID, region, zone, and number of nodes (scale linearly with the nodes nb)
        - for multi regional HA, create a replicated cluster in another region (automatic or manual failover)
    - expensive service
    - use for migration from HBase
    - can be accessed from BigQuery (in beta)
    
2. DB design & considerations
    - fundamentally different form relational db : denormalized, with 1000s of columns, no support for joins or for secondary indexes
    data stored lexicographically by row-key: it's the one indexed col
    - Keeps related data in ajdacent rows to help make reads more efficient
    all operation are atomic at the row level, not a transaction level

3. Import & export
fffffffffffffffffffffffffffffffffffffffffffffff

## Cloud Firestore

1. Characteristics
    - fully managed???
    - relational DB : ??
    - Configuration : ??

    - Backup : ??
    - High Availability 

    - Good Practice : ??
    
2. Read performance Improvement

3. Import & export

## BigQuery

1. Characteristics
    - fully managed???
    - relational DB : ??
    - Configuration : ??

    - Backup : ??
    - High Availability 

    - Good Practice : ??
    
2. Read performance Improvement

3. Import & export

## Cloud Memorystore

1. Characteristics
    - a managed Redis service, you should still monitor : memory usage, duration periods of memory overload, cache-hit ratio & the number of expirable keys
    - commonly used for caching
    - Configuration (creation with the Cloud Console or gcloud commands) :
        - Instance ID.
        - Size specification.
        - Region & zone.
        - Redis version : 3.2 or 4.0 (recommended)
        - Memory from 1 to 300 GB
        - Maximum memory policy & an eviction policy
    - High Availability / Instance tier : basic (not HA) or standard (failover replica in a different zone)
    - Good Practice : to avoid memory pressure, scale up, lower the max memory limit, modify eviction policy, set time-to-live TTL parameters on volatile key (how long it's kept before becoming leigible for eviction), or manually delete data.
    
2. Read performance Improvement

3. Import & export
    - in beta
    - export = backup file of the cache in a Cloud Storage bucket.
        
        During export : I/O can occur, but administration ops like scaling are not allowed. 
    - Import = reads export files & overwrites the cache contents

        During import : instance unavailable for I/O

4. Scaling (to use more or less mem)
    - Basic Tier : I/O are blocked. When resizing is over: all data is flushed 
    - Standard Tier: can scale while continuing to support I/O : the replica is resized first and then synchronized with the primary. The primary then fails over.
    - over 80% = memory pressure => scale up



## Cloud Storage

1. Characteristics
    - fully managed __???????????????__
    - object storage system : files are treated as atomic, no presumed structure within the file that can be exploited to access a specific part of it.
    - for persisting unstructured data (files, images, videos, backup files)
    - use cases : 
        - Storage of data shared among multiple instances does does not need to be on persitent attached storage (log files)   
        - Backup and archival
        - Staging area (uploaded files) a Cloud Function can trigger the next step  
    - Configuration, backup & HA : see below
    
2. Objects organization in Namespace
- CS uses bucket = a group of objects that share access controls. Individual objects within buckets can have thier own access controls as well.
- CS uses a global namespace for bucket names.
    - All bucket names must have unique names
    - Object names do not have to be unique.
    - A bucket is named at creation & can not be renamed (or copy to a new bucket with desired name & delete the old one)
- Suggestions for bucket naming :
    - no personnal infos for security reasons
    - follow DNS naming conventions
    - use GUIDs
    - do not use sequential names or timstamps (to avoid hotspots)
    - can also be subdomain names
- no use of filesystem = no ability to navigate a path through a hierachy of dirs & files. Otherwise use Cloud Storage FUSE open source project (provides a mechanism to map from object storage systems to filesystems)

3. Four storage tiers or storage types
- Regional : copies an object in multiple zones in one regions
- Multi-regional : stores replicas of objects in mutiple regions (can improve access time & latency)
    
    2 network tiers
    - Standard : data routed ebtween ergions using public internet infra
    - Premium : data routed over Google's global high-speed network.
- Nearline : access once a month
- Coldline : access once a year

# Retention & lifecycle management

Lifecycle = creation, active use, infrequent access but kept oneline, archived, deleted (not all steps are mandatory)

__The choice of the storage system impacts how policies are implemented.__
Policies can be used to move objects from Nearline to Coldline storage after some period of time. When partitioned tables are used in BigQuery, partitions can be deleted without affecting other partitions or running time-consuming jobs that scan full tables for data that should be deleted.

__How frequently & how fast the data must be accessed:__
- submillisecond access time : use cache / Cloud Memorystore

- frequent access with updates & persistent storage : use a database, based on the structure : relational or NoSQL (flexible) ? 
- if data is less likely to be accessed the older it gets : use time-partitioned tables (if supported by the db). In BigQuery & Bigtable tables can be organized by time as well.

- infrequent access &  query language not required : Cloud Storage.
    - Data can be exported from a db & stored in Cloud Storage. 
    - If needed, it can be re-imported back into the db for querying it

- Not likely to be accessed but must still be stored : Coldline storage in Cloud Storage (less expensive than multi-regional, regional, or Nearline)

With Cloud Storage :
1. __Lifecycle management__ based on policies, the way that objects are stored is changed automatically. These rules
    - are assigned to buckets & apply to / manipulates objects in those buckets.
    - implement lifecycle actions (deletion, setting the storage class...)
    - can be triggered based on the age of the object, its creation, the versions number &  its storage class

2. __Retention policies__ : it uses the Bucket Lock feature to enforce object retention, to ensure that any object in the bucket wil not be deleted until they reach the specified age (useful for compliance with gov. or industry regulations.



