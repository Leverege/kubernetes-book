# An Introduction to Kubernetes
Kubernetes (k8s) is one of the fastest growing open-source projects that is reshaping production-grade container orchestration. Born out of the Borg project, which ran and managed billions of containers at Google, Kubernetes solves various technical challenges related to managing microservices, including service discovery, self-healing, horizontal scaling, automated upgrades and rollbacks, and storage orchestration. 

But what does Kubernetes have to do with IoT? Why should I care about an infrastructure tool?

It turns out that the benefits of Kubernetes—abstracting away cloud infrastructure and managing a microservice architecture—also helps alleviate the unique problems IoT solutions pose. By standardizing an interface for containers to run with little overhead at a low cost, Kubernetes can smooth over the operational burdens of deploying on the edge or the cloud. 

This eBook starts with an overview of Kubernetes and walks through some of the lessons that the engineers at Leverege have learned running Kubernetes in production on some of the largest IoT deployments in North America. If you are considering a switch to using Kubernetes, or looking to spin up a new infrastructure practice, read on to evaluate the benefits of Kubernetes for your IoT deployment. 

## Table of Contents
**Chapter 1**: Introduction to Kubernetes
- What is Kubernetes and how does it relate to Docker? In this chapter, we examine the evolution from Docker to Kubernetes, as well as a comparison of other container orchestrator products. 

**Chapter 2**: Kubernetes Concepts
- Before diving into lessons learned with running Kubernetes in production, we walk through key Kubernetes concepts to illustrate why and how they are useful. 

**Chapter 3**: Useful Tools
- Kubernetes has garnered a rich ecosystem of tools that make working with Kubernetes easier. Here’s a list of useful tools that we’ve personally used. 

**Chapter 4**: Monitoring
- One of the challenges of running a massive microservice architecture is how complicated monitoring can be. This chapter provides options as well as installation tips to bootstrap a monitoring system in minutes. 

**Chapter 5**: Deploying to Cloud Providers
- Many cloud providers offer a managed instance of Kubernetes. This chapter compares the top three clouds’ Kubernetes products and recommendations for choosing one. 

**Chapter 6**: Running GKE in Production
- Leverege chose GKE to run some of the largest IoT systems to date. We share our rationale behind choosing GKE and some hard lessons learned along the way. 


## About

This repository holds free, open-sourced book about Kubernetes and lessons learned from deploying massive IoT systems using Kubernetes. For more information about [Leverege](https://www.leverege.com/), check out the [IoT Platform](https://www.leverege.com/iot-platform) page on the website. 

## Contributing

We appreciate any efforts to improve the book. Please feel free to submit pull requests against relevant markdown files in 'chapters'
