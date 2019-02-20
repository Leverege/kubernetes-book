# Kubernetes Introduction
Kubernetes, or k8s for short, is an open-source container orchestrator. Originally developed by the engineers at Google, Kubernetes solves many problems involved with running a microservice architecture in production. Kubernetes automatically takes care of scaling, self-healing, load-balancing, rolling updates, and other tasks that used to be done manually by DevOps engineers. 

Since Kubernetes was open-sourced and managed by Cloud Native Computing Foundation in 2014, the development community has embraced its benefits to orchestrate container-based systems. This book will detail the basics of Kubernetes and some of the lessons that the engineers at Leverege have learned in production.

## From Docker to Kubernetes
Docker and Kubernetes are complementary technologies. When Kubernetes orchestrates a new deployment of a container, it will instruct Docker to fire up specified containers. The key advantage of Kubernetes is that it will automatically determine how to schedule and distribute pods (we will go over the terminology shortly), instead of having a DevOps engineer manually asking Docker to do these tasks.

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%201/K8Docker.jpg" height="150">
</p>

**Kubernetes is the orchestrator, and Docker is the container runtime engine.**

### Kubernetes vs. Docker Swarm vs. Apache Mesos
With [Docker now natively supporting Kubernetes](https://www.docker.com/kubernetes), the debate between Kubernetes vs. Docker Swarm has now largely been reduced to personal preference. However, if you are starting from scratch, you may want to read up on how each of these container orchestration solutions differ in their mission, and also how they play well with each other.

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%201/DockerK8Mesos.png" height="300">
</p>

Image Credit - [Mesosphere](https://mesosphere.com/blog/docker-vs-kubernetes-vs-apache-mesos/)

#### Docker Swarm
Docker is best-known for standardizing the container format which solved the issue of managing multiple dependencies and binaries across many application environments. With git-like semantics, Docker images work intuitively and have come to influence the Cloud Native Computing Foundation (CNCF) and Open Container Initiative (OCI).

As more and more of the community began to embrace Docker, the need for a container orchestration solution naturally emerged. Before Docker Swarm was introduced, Mesos and Kubernetes were actually recommended by Docker as a container management solution in production. 

Since then, Docker has become more than a mere Docker file format provider. It has added Docker hub, registry, cloud, and other services to become a true Platform-as-a-Service. One of the key strengths of Docker Swarm is that if you’re already using Docker-compose files, it’s really easy to get setup and start using it. However, if you don’t want to depend on the entire Docker suite of tools, then Docker Swarm isn’t as "open" as Kubernetes or Mesos.

#### Kubernetes
No other company can match Google's extensive experience running billions of containers. So when Google decided to donate Kubernetes—based on their proprietary tool called Borg—  to Cloud Native Computing Foundation (CNCF) in 2014, Kubernetes quickly became the de facto standard interface to abstract away the underlying infrastructure. 

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%201/Survey.jpg" height="500">
</p>

Image Credit - [NewStack](https://thenewstack.io/data-says-kubernetes-deployment-patterns/)

The main strength of Kubernetes is the thriving open-source community led by Google, Red Hat, and CoreOS.; Kubernetes one of the [top open-source projects on GitHub](https://github.com/kubernetes/kubernetes). The rich set of features provided by the core Kubernetes API—as well as tools built on top, such as Helm, Istio, and Spinnaker—allow application developers to focus on code and not as much on infrastructure. Because Kubernetes grew out of Borg to fit containers and Docker into its existing orchestration plans (as opposed to Docker Swarm, which built on top of its Docker container experience to build orchestration), the focus of Kubernetes is less on superior "zero-to-dev" experience than on reliability and portability. To date, Kubernetes is the only solution to enable cluster federation and hybrid cloud models to avoid vendor lock-in.

#### Mesos
Mesos is the oldest technology out of the three. It isn’t a true container orchestrator but rather a cluster manager originally designed to abstract away data center resources and scale diverse workloads that may include stateless microservices (containers), legacy monoliths, batch jobs, or data services. 

Mesos shines in managing stateful data workloads like HDFS or Cassandra, where a typical blue/green deployment for microservices may result in data loss. So when people compare Docker Swarm or Kubernetes to Mesos, they’re really comparing it to a service within Mesos called Marathon that does orchestration.

## Why is Kubernetes Useful to Me
Very few companies run at the scale of Google, Netflix, and Spotify, which cycle through millions of containers. But even for the rest of us, as we scale the number of containers from a handful to several tens or hundreds of containers, the need for a container orchestrator becomes clear. 

Coupled with the continued rise of cloud computing, Kubernetes serves as a single interface through which to request data center resources. The tangible value of Kubernetes for Leverege manifested in cost-savings for running production loads in spot/preemptible machines while reducing the operational costs of dedicated DevOps resources.

## Further Reading:
- [Kubernetes vs. Mesos vs. Swarm - An Opinionated Discussion](https://blog.outlyer.com/kubernetes-vs.-mesos-vs.-swarm)
- [Docker vs. Kubernetes vs. Apache Mesos: Why What You Think You Know is Probably Wrong](https://mesosphere.com/blog/docker-vs-kubernetes-vs-apache-mesos)
- [Orchestration Platforms in the Ring: Kubernetes vs. Docker Swarm](https://www.nirmata.com/2018/01/15/orchestration-platforms-in-the-ring-kubernetes-vs-docker-swarm/)
