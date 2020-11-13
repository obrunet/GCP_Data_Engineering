# Buidling & Operationalizing Processing Infrastructure

- [Provisioning & Adjusting Resources](Build_infra.md#provisioning--adjusting-resources)
- [Monitoring Processing Resources](Build_infra.md#monitoring-processing-resources)

Server-based resources require you to specify VMs or clusters

Serverless resources do not require you those specification but  still require some configuration.

# Provisioning & Adjusting Resources

    The server-based service described here are
    - Compute Engine
    - Kube Engine
    - Cloud Bigtable
    - Cloud Dataproc
    - Cloud Dataflow
    The serverless services covered here are
    - App engine
    - Cloud Functions



## Compute Engine

Can be configured using individual VMs or instance groups. The others are configured as clusters of VMs, sometimes with VMs taking on different roles within the cluster. 
Instance groups are either managed or unmanaged. Your should prefer MIGs (identically configured VMs) unless you need heterogeneous VMs.
### Provisionning a single instance
from the cloud console, the command-line SDK or REST API
- machine type (vCPUs, RAM)
- region & zone
- boot disk parameters
- network conf
- disk image
- service account for the VM
- metadata & tages
- options : Shielded VMs, GPUs, TPUs
### Provisionning a MIG (single logical resource)
from a template created using th _gcloud compute instances-templates create-command_ 
- autohealing based on app-specific health checks
- support for multiple zone (availability)
- load balancing (distribute workload among instances in the group)
- autoscaling
- automatic, incremental updates
### Adjusting resources
- single instance = for development or to run a small db for a local group of users
- HPC = scale VM vertically
- MIG = adapt quickly to changing workloads & provide HA, horizontal scaling automatically



## Kube Engine

Containers have less overhead than VMs & allow for finer-grained allocation of resources.
### Architecture
- the master runs 4 core services taht control the cluster: 
    - the __controller manager__ runs services that manage Kube abstract components 
    - the __API server__ is used by apps to make calls to the master, handles intercluster interactions
    - the __scheduler__ is responsible for determining where to run pods (lowest schedulable unit)
    - the __etcd__ service is a distributed key/value store to state info across a cluster
- the nodes execute workloads (Compute Engine VMs run within MIGs), communicate with the master throught an agent : _kubelet_
### Abstractions (facilitate the management of containers, applications, and storage)
- __Pods__ : 
    - the smallest computation unit, contain one (usually) or multiple containers(if functionally related & have similar scaling and lifecycle)
    - deployed to nodes by the scheduler (usually in groups or replicas for HA)
    - are ephemeral, terminated if not functioning properly (without disrupting the service) then replaced
    - their nb vary according to load / scaling
- __Services__ :
    - a stable API endpoint with stable IP address
    - app that need to use a service communicate with the API endpoints. 
    - keeps track of its associated pods to route calls to them
- __ReplicaSets__ : 
    - a controller that manages the nb of pods running for a deployment
    - will add or remove (unhealthy) pods until the desired state is reached
- __Deployments__ :
    - a higher-level concept that manages ReplicaSets and provides declarative updates  
    - pods in the same deployment are created with the same template
- __PersistentVolumes__
    - decoupling pods (computation) from persistent storage (pods are ephemeral)
    - storage allocated or provisioned for use by a pod
- __StatefulSets__: abstraction used to designate pods as stateful
- __Ingress__:
    - an object that controls external access to services of a cluster
    - must be running in that cluster
### Provisioning
using either the cloud console, the command line or REST API
- cluster name
- single or multi-region
- version of GKE & a set of node pools
- machine type, disk conf & number of nodes
- autoupgrade & autorepair options
### Adjusting resources - two ways
- __Autoscaling applications__

User specifies how many replicas of an application should run. When demand for a service increases/decreases, replicas can be added/removed.

        - kubectl command to manually adjust the nb of replicas OR
        - kubectl autoscale command to autoscale

- __Autoscaling clusters__

Adjust the number of nodes in a node pool (nodes are implemented as Compute Engine VMs & node pools as MIG)

    - based on resource requests, not actual utilization.
    - the autoscaler will add/remove nodes in the pool according to the max/min nb
    - by default, GKE assumes that all pods can be restarted on other nodes. Otherwise (app not tolerant to brief disruptions) do not use cluster autoscaling
    - for a cluster in multiple zones, the autoscaler tries to keep instance groups/types balanced when scaling up

### YAML Configuration
Kube makes extensive use of YAML files that contain configuration infos (cluster resources, deployment...)
Pro of a managed GKE service : those configuration files are generated for you when using command-line & cloud console commands.



## Cloud Bigtable

a managed wide-column NoSQL db used for high-volume, low-latency I/O (IoT) & 
for analytic/ML use cases with an HBase interface.
### Provisioning
using the lcoud console, command-line SDK & REST API
- instance name & ID
- instance type (PROD or DEV), storage type (HDD or SSD)
- cluster specifications
    - name
    - region & zone
    - nb of nodes
- can be changed afterward
    - the nb of nodes
    - the nb of clustes
    - app profiles (contains replicatin settings)
    - labels used to specify metafata attributes
### Replication
- supports up to 4 replicated clusters in their own zones (increase app latency but good for HA & read performances)
- eventual consistency : write ops are replicted across all clusters using a primary-primary conf
    - Or use _read-your-wirete consistency_ so that your app will not read data older tahn their lates writes.
    - Or _strong consitency_ routes all traffic to the same cluster configured with _read-your-wirete consistency_. Other clusters are used for failover.



## Cloud Dataproc

a managed Hadoop & Spark service. 
### Provisioning
configuration using the cloud console, the command-line SDK, or the REST API:
- name
- region & zone
- mode : 
    - standard (1 msater & some nb of workers) 
    - or single (1 master / no worker), 
    - HA (3 masters), 
- masters & workers are configured separately, for each : machine types
- autoscaling policy

Afterward:
- you can adjust the nm of worker nodes, including the nb of preemptible worker nodes. It can also be done automatically : the autoscalling policy specifies the max nb & scale up/down rates
- the nb of masters cannot be modified



## Cloud Dataflow
Executes streaming  & batch apps as an Apache Beam runner. 

Pipeline options when you run a Cloud Dataflow program:
- Job name & Project ID
- Runner = a DataflowRunner for cloud execution
- Staging locations = a path to a Cloud Storage location for code packages
- Temporary location for temporary job files
- Default & max nb of workers
- In option : dsik size & machine type (Compute Engine)

## App engine
PaaS serverless (no need to provision instances), however parameters configuration for the runtime environement :
- app.yaml : 3 required param (runtime, handlers & threadsafe)
- cron.yaml : configuration of the scheduled tasks for an app
- dispatch.yaml : routing rules to send incoming request to a specific service



## Cloud Functions
serverless service that runs code in response to events in Google Cloud. No need to provision instances, however specification of the code to run & options:
- memory
- timeout
- region where the func is executed
- max-instances : nb of func instances that will exist at any one time.
- amount of memory allocated to a func (from 128 MB to 2 GB).
- timeout from 1 (by default) to 9 min.



# Monitoring Processing Resources
both the resources & the app performance
## Stackdriver Monitoring
- collects metrics on the performance of infrastructure (GCP or AWS) & apps. 
- more than 1,000 metrics, which vary by service type:
    - App Engine: VMs utilization, active connections, total nb of reserved cores.
    - Compute Engine: capacity & current utilization
    - BigQuery: query counts, execution times, scanned bytes, and table count.
    - Cloud Bigtable: CPU & disk load, node count, bytes used, and storage capacity.
    - Cloud Functions: active instances, execution count & times
- can be viewed on dashboards & used for alerting
## Stackdriver Logging
- stores and searches log data about events in infrastructure and more than 150 common apps
- a log entry = a record about some event, created by an app, an OS or middleware, or other service. log = collection of log entries.
- the retention period varies by log type (admin activity, system, access, data access)
- search with a query language (pattern matching & boolean expr for filtering)
### Stackdriver Trace
- a distributed tracing system designed to collect data on how long it takes to process requests to services. 
- useful for microservices
- available in Compute Engine, Kubernetes Engine, and App Engine.

2 main components
- a client that collects data and sends it to GCP
- a UI to view & analyze the trace data:
    - Performance insights
    - Recent traces & times
    - Most frequent URIs
    - Daily analysis reports
    - A list to view traces in detail