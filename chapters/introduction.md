# Kubernetes Introduction
Kubernetes, or k8s for short, is an open-source container orchestrator. Originally developed by engineers at Google, Kubernetes solves many problems involved with running a microservice architecture in production. Kubernetes automatically takes care of scaling, self-healing, load-balancing, rolling updates, and other tasks that used to be done manually by dev ops engineers. 

Since Kubernetes was open-sourced and managed by Cloud Native Computing Foundation in 2014, the development community has embraced its benefits to orchestrate container-based systems. This book will detail the basics of Kubernetes and some of the lessons that the engineers at Leverege have learned in production. 

## From Docker to Kubernetes
Docker and Kubernetes are complementary technologies. When Kubernetes orchestrates a new deployment of a container, it will instruct Docker to fire up specified containers. The key advantage of Kubernetes is that it will automatically determine how to schedule and distribute pods (we will go over the terminology shortly), instead of having a devops engineer manually asking Docker to do these tasks. 

Kubernetes is the orchestrator, and Docker is the container runtime engine. 
<Image Here>

### Kubernetes vs. Docker Swarm vs. Apache Mesos
With [Docker now natively supporting Kubernetes](https://www.docker.com/kubernetes), the debate between Kubernetes vs. Docker Swarm has now largely been reduced to personal preference. However, if you are starting from scratch, you may want to read up into how each of these container orchestration software differ in their mission and also play well with each other. 

#### Docker Swarm
Docker is most well-known for standardizing the container format that solved the issue of managing multiple depedencies and binaries across many application environments. Docker images play well with its git-like semantics and come to influence Cloud Native Computing Foundation (CNCF) and Open Container Initiative (OCI). As more and more of the community began to embrace Docker, the need for container orchestration naturally emerged. Before Docker Swarm was introduced, Mesos and Kubernetes were actually recommended by Docker to work with Docker management in production. Since then Docker has moved to become more  than just the Docker file format provider. It has been adding Docker hub, registry, cloud, and other services to truly become a Platform-as-a-Service. One of the biggest strengths of Docker Swarm is that if you were already using Docker-compose files, it's really easy to get setup and start using it. 

#### Kubernetes



Further Reading:
- [Kubernetes vs. Mesos vs. Swarm - An Opinionated Discussion](https://blog.outlyer.com/kubernetes-vs.-mesos-vs.-swarm)
- [Docker vs. Kubernetes vs. Apache Mesos: Why What You Think You Know is Probably Wrong](https://blog.outlyer.com/kubernetes-vs.-mesos-vs.-swarm)
