# Why Kubernetes?
- Kubernetes is a portable, extensible, open-source platform for managing contenarized workloads
- Out of the Box Features 
    - Service Discovery and Load Balancing
    - Storage Orchestration
    - Automated rollouts and roll backs
    - Automatic Bin packing
    - Self Healing
    - Secret and Configuration Management

# Kubernetes Architecture

![Kubernetes Architecture](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)

## Master
### etcd: 
        It is a key-value store, which stores information about the master and the node
### kube-scheduler:
        It is responsible for distributing containers across multiple nodes.
        It watches for newly created pods with no assigned node, and selects a node for them to run on
### kube-apiserver: 
        It acts as a fornt-end for Kubernnetes control plane. It exposes the kubernetes API
        CLI tool like kubectl, Users and even Master components (scheduler, controller manager, etcd) and worker-node components like (Kubelet) everything talk with API Server.
### kube Controller Manager: 
        Controllers are responsible for noticing and responding when nodes, containers or endpoints go down.
        They make decisions to bring up new containers in such  cases
### Cloud Control manager:


## Worker Node
### kubelet: 
### kube-Proxy: 

