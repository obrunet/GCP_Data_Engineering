# Data Processing Solutions

- [Infrastructure Design](Processing.md#infrastructure-design)
- [Scalability, reliability, availability & maintainability](Processing.md#scalability-reliability-availability--maintainability)
- [Hybrid & Edge cloud](Processing.md#hybrid--edge-cloud)
- [Distributed processing systems](Processing.md#distributed-processing-systems)
- [Migration of data warehouse from on-premises DC to GCP](Processing.md#migration-of-data-warehouse-from-on-premises-dc-to-gcp)

---

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

Availability = ability of a user to access a resource at a specific time - usually measured as the percentage of time that a system is operational.

it is a function of reliability = the probability that a system'll meet service-level objectives for some duration of time - measured as the mean time between failures.

Scalability = ability of a system to handle de/increases in workload by adding/removing resources.

### Compute Engine

- You are responsible for ensuring high availability & scalability. 
- Done with MIGs (MIGs are defined using a template including all specs)
- When an instance in a MIG fails, it is replaced identically 
- Load balancers direct traffic only to responsive instances using health checks - they are either global or regional
- Autoscalers add & remove instances according to workload - you create a policy that specifies the criteria for adjusting the size of the group (CPU utilization & other metrics collected by Stackdriver...)
- you have the greatest level of control over your instances, but you are also responsible for configuring MIGs, load balancers, & autoscalers.

### Kubernetes Engine
- K8bs is designed to support HA, reliability & scalability for containers & apps running in a cluster.
- When pods fail, they are replaced much like failed instances in a MIG. 
- Nodes belong to a pool : the autorepair feature turned on, failed nodes'll be reprovisioned automatically.

There are other ways Kubernetes Engine ensures high availability and reliability. When using Kubernetes Engine, you can specify whether the endpoint for accessing the cluster is zonal or regional. In the latter case, you can access the cluster even if there is a failure in a zone. Also, you can specify a high availability cluster configuration that replicates master and worker nodes across multiple zones.

### App Engine and Cloud Functions
A key advantage of serverless, managed services like App Engine and Cloud Functions is that they are designed to be highly available, scalable, and reliable.

In App Engine, when the scheduler has a request, it can send it to an existing instance, add it to a queue, or start another instance, depending on how you have configured App Engine. With App Engine, you do have the option of configuring policies for scaling. This is done by specifying values for target CPU utilization, target throughput utilization, and maximum concurrent requests.

Cloud Functions are designed so that each instance of a cloud function handles one request at a time. If there is a spike in workload, additional function instances can be created. You have some control over this with the ability to set a maximum number of concurrently running instances.

### Making Storage Resources Available, Reliable, and Scalable

GCP provides a range of storage systems, from in-memory caches to archival storage. Here are some examples.

Memorystore is an in-memory Redis cache. Standard Tier is automatically configured to maintain a replica in a different zone. The replica is used only for high availability, not scalability. The replica is used only when Redis detects a failure and triggers a failover to the replica.

Persistent disks are used with Compute Engine and Kubernetes Engine to provide network-based disk storage to VMs and containers. Persistent disks have built-in redundancy for high availability and reliability. Also, users can create snapshots of disks and store them in Cloud Storage for additional risk mitigation.

Cloud SQL is a managed relational database that can operate in high-availability mode by maintaining a primary instance in one zone and a standby instance in another zone within the same region. Synchronous replication keeps the data up to date in both instances. If you require multi-regional redundancy in your relational database, you should consider Cloud Spanner.

Cloud Storage stores replicas of objects within a region when using standard storage and across regions when using multi-regional storage.

Making Network Resources Available, Reliable, and Scalable

Networking resources requires advanced planning for availability, reliability, and scalability.

You have the option of using Standard Tier or Premium Tier networking. Standard Tier uses the public Internet network to transfer data between Google data centers, whereas Premium Tier routes traffic only over Google’s global network. When using the Standard Tier, your data is subject to the reliability of the public Internet.

Network interconnects between on-premises data centers and Google Cloud are not rapidly scaled up or down. At the low end of the bandwidth spectrum, VPNs are used when up to 3 Gbps is sufficient. It is common practice to use two VPNs to connect an enterprise data center to the GCP for redundancy. HA VPN is an option for high-availability VPNs that uses two IP addresses and provides a 99.99 percent service availability, in contrast to the standard VPN, which has a 99.9 percent service level agreement.

For high-throughput use cases, enterprises can use Cloud Interconnect. Cloud Interconnect is available as a dedicated interconnect in which an enterprise directly connects to a Google endpoint and traffic flows directly between the two networks. The other option is to use a partner interconnect, in which case data flows through a third-party network but not over the Internet. Architects may choose Cloud Interconnect for better security, higher speed, and entry into protected networks. In this case, availability, reliability, and scalability are all addressed by redundancy in network infrastructure.


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
yeah


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


https://www.slideshare.net/phamphuongtu/big-data-on-google-cloud

https://docs.google.com/presentation/d/1aGOTpNdCoO4GXZ2es38ZFO5qPGEAjTtDSVeHaDpwsas/edit#slide=id.g5e923c6224_162_0

https://cloud.google.com/about/locations/

https://medium.com/@srivatsan88/data-and-analytics-on-google-cloud-platform-13bc92a4596f