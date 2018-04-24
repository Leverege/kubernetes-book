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

## Kubernetes Object Management Model 
Before jumping into Kubernetes workloads (Pods, Controllers), one key thing to understand is Kubernetes's declarative model. Kubernetes actually also has imperative modes, but we will focus on the declarative model and desired states. If you want to learn more, Sebastien Goasguen, the Kubernetes lead at Bitnami, has a great [Medium article](https://medium.com/bitnami-perspectives/imperative-declarative-and-a-few-kubectl-tricks-9d6deabdde) on the difference between the imperative vs. declarative modes. 

In essence, when we write YAML or JSON files, we describe the desired state of the application: what Docker image should run, what scaling strategy to use, and what ports/services to expose. This information is posted to the kube-api-server, and the master node distributes the work to make sure the cluster matches the desired state. This configuration is stored in etcd and the workload is deployed onto the cluster. Finally, Kubernetes will constantly check to see if the current state of the cluster matches the desired behavior as defined by the programmer. So if a pod dies, Kubernetes will fire up another one to match the desired state. 

While this all sounds simple (and that was part of the intent), it's a powerful scheme to make Kubernetes useful. You the programmer only has to specified the desired state, and Kubernetes will take care of the rest (instead of having you run specific commands to achieve this like in imperative models). 

## Kubernetes Workloads 
Kubernetes workloads are divided into two major components: pods (the basic building block) and controllers (e.g ReplicaSet, Deployment, StatefulSet, CronJob, etc). 

### Pods

### Controllers
