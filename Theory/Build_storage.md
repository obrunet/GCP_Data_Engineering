# Buidling & Operationalizing Storage Systems

- [Cloud SQL]()
- [Cloud Spanner]()
- [Cloud Bigtable]()
- [Cloud Firestore]()
- [BigQuery]()
- [Cloud Memorystore]()
- [Cloud Storage]()

## Key Concepts

does not require as much administaration & operational support as an unmanaged DB : Google'll take car of core opetaional tasks, backups, OS updates, scaling, failover configuration, monitoring, authorizing network conections.


## Cloud SQL

1. Characteristics
    - fully managed
    - relational DB : support MySQL, PostgreSQL, SQL Server (beta)
    - supports regional-level DB up to 30TB (if you need more or multi-regional support --> consider Cloud Spanner)
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

## Cloud Spanner
## Cloud Bigtable
## Cloud Firestore
## BigQuery
## Cloud Memorystore
## Cloud Storage


![Example](pictures/decision_tree.svg "Example")

