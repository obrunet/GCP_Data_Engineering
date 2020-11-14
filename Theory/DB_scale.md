# DB Design Databases for Reliability, Scalability, and Availability

- [Cloud Bigtable](DB_scale.md#cloud-bigtable-db---scalability--reliability)
- [Cloud Spanner](DB_scale.md#cloud-spanner-db-for-scalability--reliability)
- [Cloud BigQuery](DB_scale.md#bigquery-dbs-for-data-warehousing)

Cloud SQL which does not scale beyound the region level is not covered here.

#  Cloud Bigtable DB - Scalability & Reliability
a nonrelational DB based on a sparse 3D map (3D = rows, columns & cells).

## Data modeling
Bigtable DBs are deployed on a set of resources known as an instance :
- it's not the same thing as a VM instance in Compute Engine
- it consists of  a set of nodes, which are VMs, as well as a set of sorted string tables and log data stored in Google’s Colossus FS

![Example](pictures/Bigtable.png "Example")

- The nodes manage the metadata
- The actual data is stored on the Colossus FS & organized into sorted string tables (SSTables or tablets in GCP terminology) which are immutable.

Pros of separating metadata from data:
- Fast rebalancing of tablets from one node to another
- The ability to recover a failed node by migrating metadata only
- The scalability, reliability, and availability of the Colossus FS

A master process balances workloads in a cluster, including splitting and merging tablets.

1. Rows are indexed by a row-key (similar to a primary key in a relational db). Data is stored in a lexicographically sorted order by row-key
2. Columns are grouped into column families : frequently used together data are retrieved more efficiently
3. Cells are used to store multiple versions of a value over time. The value in the cell with the latest timestamp is returned by default

Cloud Bigtable tables are sparse : no data for a particular row/column/cell no storage used
- fewer large tables is preferred to many small tables (that require more backend connection overhead).
- different table sizes can disrupt the load balancing in background
- operations are atomic at the row level : multiple changes made to related data will all succeed or fail together (but size is limited: 10 MB in a single cell & 100 MB in a single row)

## Row-keys Design
Bigtable scales best when R/W operations are distributed across nodes and tablets.

### Best Practices
- avoid monotonically increasing values or lexicographically close strings:  this can cause hotspots (exception for reverse domain names: Bigtable can compress the repeated data efficiently)
- when a using a multitenant Bigtable db, use a tenant prefix in the row-key to keep all customer’s data together)
- String ID are good candidates for a row-key
- Timestamps not the entire row-key or the start of it (helpfull for range scans based on time)
- in general move toward the front of the key, values that are highly varied.
- to avoid hotspots use salting (adding a derived value to the key to make writes noncontiguous)

### Antipatterns (to be avoided)
- Domain names
- Sequential numeric IDs
- Frequently updated identifiers
- Hashed values

### Key Visualizer
a Bigtable tool for understanding usage patterns in a database, it generates hourly and daily scans (heatmap) & helps to identify:
- where hotspots exist
 -rows that may contain too much data
- the distribution of workload across all rows in a table

### Time Series
well suited for financial data & IoT data streams, low-latency writes & highly scalable, tables with many rows and few columns (ie tall & narrow tables), queries using time ranges

Recommandations
- keep names short (reduce the size of metadata) 
- favor tables with many rows and few columns (makes querying easier without exceeding max recommended row sizes)
- use one table per modeled entity
- design row-keys for looking up a single value or a range of values

## Replication for Availability & Scalability
Keeping multiple copies of your data in multiple regions or multiple zones can improve availability and durability, distribute workloads across multiple clusters.
Bigtable supports up to 4 replicated regional or multi-regional clusters:
- all changes are replicted in the other clusters
- there is no single primary cluster: all replicated clusters can perform R/W
- eventually consistent (all replicas should be updated within a few sec. or min.)
- specify _read-your-writes consistency_ to ensure that a cluster will never read data that is older than its most recent writes
- specify _single-cluster routing_ for strong consistency



# Cloud Spanner DB for Scalability & Reliability
- a globally scalable relational DB with the scalability benefits once limited to NoSQL db. 
- maintains key features of relational db: support for normalized data models & transaction 
Factors that influence Cloud Spanner databases performance:

## Relational DB Features
Its data model is centered around tables, which are composed of columns(attributes) & rows(values for attributes). Relational DBs are strongly typed. 

Cloud Spanner supported types:

```Array / Bool / Bytes / Date / Float64 / INT64 / String / Struct / Timestamp```

Spanner supports both primary (automatically created) & secondary indexes(other columns or combinations of cols). This reduces the need to denormalize & duplicate data to support multiple query patterns (such as in Cloud Bigtable)

## Interleaved Tables
Interleaving data from related tables is
- done through a parent-child relationship: 
    - parent data (a row of an other table) is stored with child data)
    - retrieving data simultaneously from both tables is more efficient than when stored separately 
- used for tables frequently joined: by storing the related data together, joins can be performed with fewer I/O ops and seeks than if stored independently

## Primary Keys & Hotspots
- horizontally scalable (Spanner shares some characteristics with Bigtable)
- uses multiple servers: data is divided among servers by key range (potential for hotspots)
Monotonically increasing keys, for example, can cause R/W ops to happen in a few servers at once instead of being evenly distributed

Recommandations :

- using a hash of the natural key (not recommended with Cloud Bigtable, but meaningless keys are regularly used in relational db)
- swapping the order of Columns in Keys
- using a UUID v4+
- using bit-revers sequential values

## Database splits
Cloud Spanner breaks data into chunks known as splits. A split is a range of rows (not an interleaved part of another table) ordered by primary key up to 4 GB. 
If Cloud Spanner detects a large number of R/W ops on a split, it will divide the rows into two splits so that they can be placed on different servers -> distribution to balance the R/W workload across nodes, not just the amount of data.

## Secondary indexes
An index is automatically created for the primary key.
You have to define any secondary indexes specifically on columns or groups of columns other than the primary key. Userful for
- filtering in a query : the index of the column in the WHERE clause is used rather than a full scan of the table 
- returning rows in a sort order other than the primary key order.
## Query best practices
1. Query Parameters

Cloud Spanner builds an execution plan (with statistics about the distribution of data, existing secondary indexes...). If the query is repeated with changes to only some filter criteria, no need to rebuild the whole plan each time the query is executed by using parameterized queries.

2. EXPLAIN PLAN to Understand Execution Plans

a plan = a series of steps designed to retrieve data and respond to a query. Splits are distributed across a cluster: the execution plans have to incorporate local execution of subplans executed on each node. The results are then aggregated.

3. Avoid Long Locks

When executing a transaction, the db may use locks to protect the integrity of data. When locks should be used:
- If a write depends on the result of one or more reads
- If several write operations have to be committed together
 During a lock, no other processes can modify those rows until the transaction commits or rolls back.



# BigQuery DBs for Data Warehousing
BigQuery is designed for DW, ML & analytics. It uses standard SQL language but it is not a relational db.

## Schema Design for Data Warehousing
DWing = the practice of creating and using DBs for analytic operations.
≠ DBs design for transaction processing.

1. Types of Analytical Datastores

    - Data Warehouses = centralized, organized repositories of analytical data for an organization.
    - Data Marts = subsets of DW that focus on particular business lines or departments.
    - Data Lakes = less structured data stores for raw and lightly processed data.

    BigQuery is designed to support DW & data marts. For Data lakes: Cloud Storage(object storage) or NoSQL db(Cloud Bigtable).

2. Projects, Datasets & Tables

    At the highest levels, BigQuery data is organized around 
    - projects: the high-level structure used to organize the use of GCP services & resources(use of APIs, billing & permissions). It contain datasets.
    - datasets: containers for tables and views. Its location is immutable. Access to tables & views are defined at the dataset level
    - tables: collections of rows & columns stored in a columnar format, written to the Colossus distributed FS.

    Limitations:
    - table names within a dataset must be unique.
    - the copy of a table must be done in the same location.
    - when merging multiple tables to a single one, all sources must have the same schema

    Supported data types: 

    ``` Array / Bool / Bytes / Date / Datetime / Numeric / Float64 / INT64 / Geography / String / Struct / Time / Timestamp``` 

    Although BigQuery supports joins like DWs, there are pros of denormalizing a data model & storing related data together : it's done with with nested & repeated columns (defined as a RECORD data type & accessed as a STRUCT in SQL).

## Clustered & partitioned tables
BQ is most cost-efficient when you can minimize the amount of data scanned to retrieve queries. BQ does not have indexes like relational or document DBs
but it does support:

### Partitioning
= the process of dividing tables into segments to take advantage of metadata about partitions to determine which of them should be scanned.
tables can be partitioned by:
- ingestion time : BQ loads data into a daily partition and creates new partitions each day.
- timestamp : based on a DATE or TIMESTAMP column in a table --> better performance than sharded tables.
- integer range :based on a column with an INTEGER data type

### Clustering
- When a table is clustered, 1 to 4 columns are specified along with an ordering of those columns, you can have data that is frequently accessed together collocated in storage. 
- supported only on partitioned tables & used when filters or aggregations are frequently used.

## Querying data
- BQ supports the use of parameterized queries (specified by an @param_name)
- BQ can query multiple tables at once (using the wildcard * in a table name)

2 types of queries: 
- interactive - by default - executed immediately 
- batch : queued up & run when resources are available (pro: resources are drawn from a shared resource pool and batch queries do not count) 

## External data access
Instead of first loading data into BQ, you can create a reference to an external source (known as federated sources) such as 
- Cloud Bigtable
- Cloud Storage
- Google Drive

You can create either permanent or temporary (available for 24 hrs, useful for one-time operations) external tables

## BigQuery ML
This allows users to build ML models in BigQuery rather than programming models & supports atm :
- Linear regression
- Binary logistic regression
- Multiclass logistic regression
- K-means clustering
- TensorFlow models
