# Kubernetes Concepts
At a high level, Kubernetes works similarly to many cluster architectures. It consists of one or more masters and multiple nodes that they control. The master nodes orchestrate the applications running on nodes, and they monitor them constantly to ensure that they match the desired state the programmer has declared. In this chapter, we will dive deeper into the key concepts underlying the Kubernetes Architecture. This is by no means a comprehensive overview of every detail; for that, you should read up on Kubernetes’s [Official Documentation](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/).

## Kubernetes Components
As mentioned previously, Kubernetes can largely be divided into Master and Node Components. There are also some add-ons such as the Web UI and DNS that are provided as a service by managed Kubernetes offerings (e.g. GKE, AKS, EKS).

### Master Components
Master components globally monitor the cluster and respond to cluster events. These can include scheduling, scaling, or restarting an unhealthy pod. Five components make up the Master components: kube-apiserver, etcd, kube-scheduler, kube-controller-manager, and cloud-controller-manager.

- **kube-apiserver**: REST API endpoint to server as the frontend for the Kubernetes control plane
- **etcd**: key value store for the cluster data (regarded as the single source of truth) 
- **kube-scheduler**: watches new workloads/pods and assigns them to a node based on several scheduling factors (resource constraints, anti-affinity rules, data locality, etc)
- **kube-controller-manager**: central controller that watches the node, replication set, endpoints (services), and service accounts
- **cloud-controller-manager**: interacts with the underlying cloud provider 

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%202/Cluster.jpg" height="300">
</p>

Image Credit: [Linux.com](https://www.linux.com/news/learn/chapter/intro-to-kubernetes/2017/4/what-makes-kubernetes-cluster)

### Node Components 
Unlike Master components that usually run on a single node (unless High Availability Setup is explicitly stated), Node components run on every node.

- **kubelet**: agent running on the node to inspect the container health and report to the master as well as listening to new commands from the kube-apiserver
- **kube-proxy**: maintains the network rules 
- **container runtime**: software for running the containers (e.g. Docker, rkt, runc) 

## Kubernetes Object Management Model 
Before jumping into Kubernetes workloads (Pods, Controllers, etc.), it’s important to understand Kubernetes's declarative model. Kubernetes actually also has imperative modes, but we will focus on the declarative model and desired states. If you want to learn more, Sebastien Goasguen, the Kubernetes lead at Bitnami, has a great [Medium article](https://medium.com/bitnami-perspectives/imperative-declarative-and-a-few-kubectl-tricks-9d6deabdde) on the difference between the imperative vs. declarative modes. 

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%202/States.jpg" height="300">
</p>

Image Credit: The Kubernetes Book

In essence, when we write YAML or JSON files, we describe the desired state of the application: what Docker image should run, what scaling strategy to use, and what ports/services to expose. This information is posted to the kube-api-server, and the master node distributes the work to ensure that the cluster matches the desired state. This configuration is stored in etcd, and the workload is deployed onto the cluster. Finally, Kubernetes will constantly check to see whether the current state of the cluster matches the desired behavior the programmer has defined. So, if a pod dies, Kubernetes will fire up another one to match the desired state.

While this all sounds simple (and that was part of the intent), it’s a powerful scheme that makes Kubernetes very useful. You (the programmer) only have to specify the desired state, and Kubernetes will take care of the rest (instead of having you run specific commands to achieve this like in imperative models).

## Kubernetes Workloads 
Kubernetes workloads are divided into two major components: pods (the basic building block) and controllers (e.g. ReplicaSet, Deployment, StatefulSet, CronJob, etc.).

### Pods
A Pod for Kubernetes is what a container is for Docker: the smallest and simplest unit in its object model. It’s helpful to conceptualize Pods as a single instance of an application—or a container. In reality, a Pod encapsulates one or more containers as well as storage resources, an IP address, and rules on how the container(s) should run.

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%202/Pods.jpg" height="300">
</p>

Image Credit: [Kubernetes.io](https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/)

As Pods are meant to be an atomic unit in Kubernetes, each Pod should really run a single instance of a given application. So, if you need to run multiple copies of a container, each container should run within its own unique Pod instead of having all of those containers be in a single Pod. However, sometimes a multi-container Pod makes sense if they are closely-related (a common example is some logging component). One key thing to note is that all the containers in a Pod will share the same environment: memory, volumes, network stack, and most importantly, the IP address.

In a typical deployment, Pods are not directly created by the programmer. Instead, the Controller will schedule a Pod to run on a Node. Some important things to know about Pods:

- A Pod can only exist on a single Node.
- A Pod can never be in a partially-deployed state. If a part of the Pod never comes up, it is considered unhealthy and fails. 
- A Pod is not "healed" but rather treated as a disposable component. In other words, if a Pod becomes unhealthy, Controller elements will kill the Pod and start up another Pod, replacing rather than healing it.

### Controllers
As mentioned earlier, Pods are usually deployed indirectly via Controllers. The one used most frequently is Deployments, but we will quickly cover some other types of Controllers.

#### ReplicaSet
ReplicaSet, as the name suggests, deploys the specified replicas of the Pod. Unless you require custom updates to the Pods, it’s recommended that you use Deployments, which are higher level objects that wrap around ReplicaSets.

#### Deployments
Deployments allow for rolling updates and easy rollbacks on top of ReplicaSets. You can define the desired state in the Deployment model, including scaling, rolling updates in canary or blue/green fashion, and Deployments will take care of it for you.

#### StatefulSets
StatefulSets are similar to Deployments, but maintain a "sticky identity" for each of the Pods. It’s useful for applications in which a stable network identifier or persistent storage is required. A common example would be ElasticSearch.

#### CronJobs
As the name suggests, CronJob manages time-based jobs. Going with our ElasticSearch example, one common task would be sending out daily reports or cleaning up old data.
