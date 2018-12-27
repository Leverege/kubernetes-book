# Kubernetes Introduction
Kubernetes, or k8s for short, is an open-source container orchestrator. Originally developed by the engineers at Google, Kubernetes solves many problems involved with running a microservice architecture in production. Kubernetes automatically takes care of scaling, self-healing, load-balancing, rolling updates, and other tasks that used to be done manually by DevOps engineers. 

Since Kubernetes was open-sourced and managed by Cloud Native Computing Foundation in 2014, the development community has embraced its benefits to orchestrate container-based systems. This book will detail the basics of Kubernetes and some of the lessons that the engineers at Leverege have learned in production. 

## From Docker to Kubernetes
Docker and Kubernetes are complementary technologies. When Kubernetes orchestrates a new deployment of a container, it will instruct Docker to fire up specified containers. The key advantage of Kubernetes is that it will automatically determine how to schedule and distribute pods (we will go over the terminology shortly), instead of having a DevOps engineer manually asking Docker to do these tasks. 

Kubernetes is the orchestrator, and Docker is the container runtime engine. 
<Image Here>

### Kubernetes vs. Docker Swarm vs. Apache Mesos
With [Docker now natively supporting Kubernetes](https://www.docker.com/kubernetes), the debate between Kubernetes vs. Docker Swarm has now largely been reduced to personal preference. However, if you are starting from scratch, you may want to read up into how each of these container orchestration software differ in their mission and also play well with each other. 

#### Docker Swarm
Docker is most well-known for standardizing the container format that solved the issue of managing multiple depedencies and binaries across many application environments. Docker images play well with its git-like semantics and come to influence Cloud Native Computing Foundation (CNCF) and Open Container Initiative (OCI). As more and more of the community began to embrace Docker, the need for container orchestration naturally emerged. Before Docker Swarm was introduced, Mesos and Kubernetes were actually recommended by Docker to work with Docker management in production. Since then Docker has moved to become more than a mere Docker file format provider. It has been adding Docker hub, registry, cloud, and other services to truly become a Platform-as-a-Service. One of the biggest strengths of Docker Swarm is that if you were already using Docker-compose files, it's really easy to get setup and start using it. However, if you do not want to depend on the entire Docker suite of tools, then Docker Swarm is not as "open" as Kubernetes or Mesos. 

#### Kubernetes
No other company can match Google's extensive experience running billions of containers. So when Google decided to doante Kubernetes to CNCF based on their proprietary tool called Borg, it quickly became the de facto standard interface to abstract away the underlying infrastructure. The main strength of Kubernetes is the thriving open-source community led by Google, Red Hat, and CoreOS (it's one of the top open-source projects on GitHub). The rich set of features provided by the core Kubernetes API as well as tools built on top such as Helm, Istio, and Spinnaker allow application developers to focus on code and not as much on infrastructure. Because Kubernetes grew out of Borg to fit containers and Docker into its existing orchestration plans (as opposed to Docker Swarm which built on top of its Docker container experience to build orchestration), the focus of Kubernetes is less so on superior "zero-to-dev" experience but more so on reliability and portability. To date, Kubernetes is the only solution to enable cluster federation and hybrid cloud models to avoid vendor lock-in. 

<image here from https://thenewstack.io/data-says-kubernetes-deployment-patterns/> 

#### Mesos
Mesos is the oldest technology out of the three and isn't truely a container orchestrator. Rather it is a cluster manager originally designed to abstract away data center resources and scale diverse workloads that may include stateless microservices (containers), legacy monoliths, batch jobs, or data services. Mesos shines in managing stateful data workloads like HDFS or Cassandra where a typical blue/green deployment for microservices may result in data loss. So when people compare Docker Swarm or Kuberentes to Mesos, they are really comparing it to a service within Mesos called Marathon that does orchestration.

## Why is Kubernetes Useful to Me
Very few company run at the scale of Google, Netflix and Spotify, cycling through millions of containers. But even for the rest of us, as we scale the number of containers from a few handful to even several tens or hundres of containers, the need for a container orchestrator becomes clear. Coupled with the continued rise of cloud computing, Kubernetes serves as a single interface to request data center resources. Tangibly, the value of Kubernetes for Leverege manifested in cost-savings for running production loads in spot/pre-emptible machines and reducing operational costs of dedicated DevOps resources. Stay tuned to learn more about Kubernetes!

## Further Reading:
- [Kubernetes vs. Mesos vs. Swarm - An Opinionated Discussion](https://blog.outlyer.com/kubernetes-vs.-mesos-vs.-swarm)
- [Docker vs. Kubernetes vs. Apache Mesos: Why What You Think You Know is Probably Wrong](https://mesosphere.com/blog/docker-vs-kubernetes-vs-apache-mesos)
- [Orchestration Platforms in the Ring: Kubernetes vs. Docker Swarm](https://www.nirmata.com/2018/01/15/orchestration-platforms-in-the-ring-kubernetes-vs-docker-swarm/)
