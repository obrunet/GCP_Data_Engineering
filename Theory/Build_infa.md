# Buidling & Operationalizing Processing Infrastructure

- [Provisioning & Adjusting Resources](Build_infa.md#provisioning--adjusting-resources)
- [Monitoring Processing Resources](Build_infa.md#monitoring-processing-resources)

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

Can be configured using individual VMs or instance groups. The others are configured as clusters of VMs, sometimes with VMs taking ond different roles within the cluster. 
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
### Abstractions:
- Pods
- Services
- ReplicaSets
- Deployments
- PersistentVolumes
- StatefulSets
- Ingress

__TO BE CONTINUED__
### Provisioning
using either the cloud console, the command line or REST API
- cluster name
- single or multi-region
- version of GKE & a set of node pools
- machine type, disk conf & number of nodes
- autoupgrade & autorepair options
### Adjusting resources - two ways
- __Autoscaling applications__

__TO BE CONTINUED__

- __Autoscaling clusters__

__TO BE CONTINUED__

### YAML Configuration
__TO BE CONTINUED__



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
- supports up to 4 replicated clusters
- 

## Cloud Dataproc



## Cloud Dataflow



## App engine



## Cloud Functions

# Monitoring Processing Resources
