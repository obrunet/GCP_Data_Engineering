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




x       | Structured        | Semi-structured           | Unstructured  |
--------| ------------- |-------------| -----|
schema  | fixed      |  attributes can vary | no defined |
flexibiity / search   | low/easy      | good compromise      |   high/difficult |



## Decision tree
    
![Example](pictures/decision_tree.svg "Example")



