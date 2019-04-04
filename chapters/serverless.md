# Serverless on Kubernetes
At first glance, serverless on Kubernetes sounds like a oxymoron. Aren't serverless and containers supposed to be competing and opposite technologies? 

The confusion lies in the unfortunate term "serverless." Afterall, serverless systems are actually made up of servers. As Alex Ellis explains in his [great talk at GOTO 2018](https://www.youtube.com/watch?v=yOpYYYRuDQ0), serverless is an *architectural pattern*. In that context, serverless framework should work for both Functions-as-a-Service (FaaS) platforms like AWS Lambda and on Kubernetes. 

> By 2020, in the cloud platform, serverless & k8s will essentially converge: "Kubernetes, just run my code" <br>
> \- Alexis Richardson (CEO Weaveworks)

One may then question why serverless is even relevant in the context of Kubernetes. Can't we just use Google Cloud Functions with GKE by triggering off Pub/Sub or HTTP calls? That is true, but what happens if we want long running jobs that can scale to zero or want to interface with Redis or want to support jobs written in languages not supported by Cloud Functions? Lastly, what about the concerns with vendor lock-in? 

In the last few years, various open source frameworks for serverless gained traction to provide alternative solutions for FaaS offerings. It is hard to conclude whether or not any of these solutions are truly production ready, but from our initial evaluation, several have proven to be useful. 

## Open Source Options
According to a [2018 NewStack serverless survey](https://thenewstack.io/guide-to-serverless-technologies-free-ebook-on-the-new-stack/), Kubeless and OpenFaaS led the way in terms of popularity, closely followed by Apache OpenWhisk. In terms of Github interactions and forum activity, NewStack's survey seem to be fairly accurate. The analysts at [Winder also did an independent assessment](https://winderresearch.com/a-comparison-of-serverless-frameworks-for-kubernetes-openfaas-openwhisk-fission-kubeless-and-more/) and concluded that OpenWhisk was slightly more popular than OpenFaaS. 

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/v1.1/images/Draft/serverless-survey.png" height="500">
</p>

When Leverege began to evaluate these tools, it was helpful to orient the landscape in the following manner: 

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/v1.1/images/Draft/serverless-comparison.jpg" height="300">
</p>

Because our infrastructure heavily utilized Kubernetes, most of our experiments focused on comparing Knative and OpenFaaS. If you are looking for a product that is more of a Lambda replacement, you may want to start the evaluation process from the right. 

### Knative
Google announced Knative at NEXT in 2018, and introduced [GKE serverless add-on](https://cloud.google.com/blog/products/containers-kubernetes/knative-bringing-serverless-to-kubernetes-everywhere) in Decemeber. As the name and the logo suggests, Knative is targeting Kubernetes users for an alternative way to add serverless workloads onto their existing architecture. Leverege participated in the beta tests for GKE serverless add-on (now renamed to Cloud Run on GKE) and found Knative to be less of a full-fledged FaaS runtime than an underlying engine to support other serverless workloads. This means that you can run OpenFaaS functions with Knative if desired. 

If you follow GIFEE (Google Infrastructure for Everyone Else), Knative will be a natural progression from Kubernetes to Istio to Knative. Because Knative uses Istio extensively, it is not a lightweight solution as compared to OpenFaaS for example. However, given Google's backing and vision to tighten the integraiton between GCP and GKE, expect some more big news at NEXT 2019 for quickly enabling serverless workloads onto existing GKE infrastructure. 

### OpenFaaS
If Knative had Google's backing from the beginning, OpenFaaS was driven by one man's dream. Alex Ellis built a huge community around OpenFaaS with a very lightweight system compatible with Docker Swarm and Kubernetes. Out of all the services we evaluated, OpenFaaS had the easiest setup and great tutorials to get started. 

OpenFaaS can easily be installed via Helm or via an Operator using CRDs (openfaas functions). The default set up uses Prometheus for autoscaling, but [Stefan Prodan](https://stefanprodan.com/#blog) has numerous blog posts on modifying OpenFaaS setup to fit your preference on Kubernetes reliance (e.g. using Kubernetes HorizontalPodAutoscaler instead of Prometheus). 

### Fn & Fission
Admittedly we have not evaluated Oracle's Fn or Platform 9's Fission. Fn is as old as OpenWhisk, but many have commented on how opinionated the framwork is. As for Fission, the momentum seemed to have died down quite a bit so decieded to stick to the more supported projects. 

### Kubeless
Kubeless, backed by Bitnami, utilizes CRDs heavily to provide more FaaS functionalities. It supports Kafka and HTTP for event triggers and is compliant with the Serverless Framework and even AWS Lambda CLI. Unlike Knative which aims to add serverless features to Kubernetes, Kubeless seemed like it wanted to customize Kubernetes behavior to replicate FaaS services. Despite its popularity, currently the project seems to be in maintenance mode with one of the original contributors leaving the project for a [new startup built on Knative](https://triggermesh.com/). 

### OpenWhisk 
Finally, OpenWhisk was the first open source project to enter the market with IBM's backing. IBM claims to use OpenWhisk in production for its FaaS offering. OpenWhisk also has the backing of Adobe and Red Hat, leading to tons of documentation and support to work with OpenShift. It looked like a promising and perhaps the only battle tested framework in the market, but it leveraged Kubernetes the least. If FaaS-only service is what you are looking for, OpenWhisk may very well be a great choice. But if your infrastructure is already built on top of Kubernetes, OpenWhisk may not be a simple addon like clicking enable for Knative. 

## Other Resources
- [Examining the FaaS on K8s Market](https://blogs.cisco.com/cloud/examining-the-faas-on-k8s-market)
- [Today in Serverless and Open Source](https://hackernoon.com/serverless-and-open-source-where-do-we-stand-today-dff8aec67026)
- [A Comparison of Serverless Frameworks for Kubernetes: OpenFaas, OpenWhisk, Fission, Kubeless and more](https://winderresearch.com/a-comparison-of-serverless-frameworks-for-kubernetes-openfaas-openwhisk-fission-kubeless-and-more/)
- [Guide to Serverless Technologies](https://thenewstack.io/guide-to-serverless-technologies-free-ebook-on-the-new-stack/)
- [Kubedex Serverless](https://kubedex.com/serverless/)
- [The Leading Open Source Serverless Solutions for Kubernetes](https://gravitational.com/blog/serverless-on-kubernetes/)
