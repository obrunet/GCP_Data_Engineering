# Data Processing Solutions

Processing starts with data collection and ends with exploration & visualization.
- How to choose the appropriate compute service depending on the use case
    - Compute Engine
    - Kubernetes Engine
    - App Engine
    - Cloud Functions

    Cloud Run & Anthos are not covered
- Scalability, reliability, availability & maintainability
- Hybrid & Edge computing architectures
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
    - cons : 
    
### App Engine


### Cloud Functions




x       | Structured        | Semi-structured           | Unstructured  |
--------| ------------- |-------------| -----|
schema  | fixed      |  attributes can vary | no defined |
flexibiity / search   | low/easy      | good compromise      |   high/difficult |
scalibity   | difficult | feasible      |    easy |
examples   | tables, Sheets, SQL, customer data, phone records, transaction history | JSON, text with an apparent pattern, XML      |    text (NLP), audio, video files, BLOBs |


## Decision tree
    
![Example](pictures/decision_tree.svg "Example")



