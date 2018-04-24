# Kubernetes Concepts
At a high level, Kubernetes works similar to many cluster architectures consisting of one or more masters and multiple nodes that they control. The master nodes orchestrate the applications running on nodes and constantly monitor them to match the specified state declared by the programmer. In this chapter, we'll dive deeper into the important concepts underlying the Kubernetes Architecture. This is by no means a comprehensive overview of every detail; for that, you should read up on their [Official Documentation](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/).

## Kubernetes Components
As mentioned earlier, Kubernetes can largely be divided into Master and Node Components. There are also some addson such as the Web UI and DNS that are provided as a service by managed Kubernetes offerings (e.g. GKE, AKS, EKS). 

### Master Components
Master components globally monitors the cluster and responds to cluster events. These can include scheduling, scaling, or restarting an unhealthy pod. Five components make up the Master components: kube-apiserver, etcd, kube-schduler, kube-controller-manager, and cloud-controller-manager. 

- kube-apiserver: REST API endpoint to server as the frontend for the Kubernetes control plane
- etcd: key value store for the cluster data (regarded as the single source of truth) 
- kube-scheduler: watches new workloads/pods and assigns them to a node based on several scheduling factors (resource constraints, anti-affinity rules, data locality, etc)
- kube-controller-manager: central controller that watches the node, replication set, endpoints (services), and service accounts
- cloud-controller-manager: interacts with the underlying cloud provider 

### Node Components 
Unlike Master components that usually run on a single node (unless High Availability Setup is explicitly stated), Node components run on every node. 

- kubelet: agent running on the node to inspect the container health and report to the master as well as listening to new commands from the kube-apiserver
- kube-proxy: maintains the network rules 
- container runtime: software for running the containers (e.g. Docker, rkt, runc) 

