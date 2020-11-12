# Data Processing Solutions

Processing starts with data collection and ends with exploration & visualization.
- How to choose the appropriate compute service depending on the use case
    - Compute Engine / Kubernetes Engine / App Engine / Cloud Functions
    - Cloud Run & Anthos are not covered
- Scalability, reliability, availability & maintainability
- Hybrid & Edge cloud
- Distributed processing systems
- Migration of data warehouse from on-premises DC to GCP

## Infrastructure Design

### Compute Engine
- IaaS
- You have the greatest amount of control over your infrastructure : full acces to VMs instances
- Good option if you need max control over the conf & are willing to manage instances
- Configuration:
    - OS, software, conf
    - machine type: vCPU, RAM, GPU, storage
    - security features : Shielded VMs & accelerators
    - region & zone
    - VMs can be grouped into cluster for HA & scalability : an instance group (VMs with identical conf.) is managed as a single unit.

### Kubernetes Engine
- managed service : Google maintains the cluster, the kube's installation & conf on instance groups
- kube is deployed on a cluster of servers
- pro : 
    - users can precisely tune the allocation of resources to each container. 
    - For apps designed as a set of microservices. App components separated into microservices in their own containers : easier to allocate resources & to maintain
    - allow mlutiple env : other cloud provider & on-prem
- cons : 
    - microservices should have the same liefcycle (maintenance can disrupt other microservices)
    - apps must be containerized

### App Engine
- PaaS
- allow devs to focus an app development.
- 2 versions :
    - Standard: serverless environnement, support Go, Java? PHP, Nodejs & Python.
    - Flexible: runs Docker containers & allows devs to customize their runtime env --> avantage  of a PaaS with flexibility

### Cloud Functions
- serverless, managed compute service
- for running code in response to events that occur in the cloud. Writing a msg to a Cloud Pub/Sub or uploading files can triger the execution of a Cloud Function
- Funcs are coded in JavaScript, Python 3 & Go

## Scalability, reliability, availability & maintainability

__TO BE CONTINUED__

## Hybrid & Edge cloud

### Hybrid Cloud
Combines on-premises transaction processing systems with cloud-base analytics platforms. 
Data is extracted & transferred to the cloud for analytics processing.
OLTP systems have predictable workloads with little variance
Whereas Analytic workloads are predictable but can be highly variable


### Edge Cloud
A variation of Hybrid Cloud. Used when 
 - connectivity / network is not reliable or 
 - bandwidth not sufficient. 
 - low-latency processing is required (IoT, manufacturing...)
 A combo of cloud & ege base computed may be used.
 CI / CD process ensures consitency accross edge devices. When the full apps is run at the edge consider using containers.

## Distributed processing systems
__TO BE CONTINUED__

### Messaging
__TO BE CONTINUED__

### Services
__TO BE CONTINUED__

## Migration of data warehouse from on-premises DC to GCP

DW = repositories of enterrprise data organized for BI reporting & analysis
- DW include extraction, tranformation & load scripts, view & enbedded UDF, reports & dataviz.
- Identity mngmt & access control are used to protect confidentialiyt, integrity & availability
- 2 scenarii
    - off-loading a DW migration: copying data & scheames, when BI needs the extra storage & compute capacity of the cloud
    - a full DW migration = off-loading + moving pipelines : allows to take advantage of the cloud ETL tools.
- 4 stages

### Assessing the current state of a DW
    - Technical requirements :
      identifying existing use cases: data needed, source, update frequency
      ETL jobs, access controls, metadata, reports, dataviz
    - Buissness benefits of a migration
      cost-savings, increased agility, flexibility

### Future state design
    - definition of KPIs to measure the migration process effciency vs objectives
      amount of data migrated, dataviz available...
    - how you can take advantage of BigQuery 
      (no need to plan for resources/serverless, Colossus FS to ensure availability...)

### Migration of data, jobs & access controls
    Alternative ways to prioritize, depending on your needs - iterative process
    - migration of analytical workloads first
    - focusing on the UX first
    - prioritize low-risk use case first

### Cloud DW validation
    - Testing & validating all aspects (schema correctly defined, all data actually loaded, pipelines, queries, 
    dataviz working flawlessly...)
