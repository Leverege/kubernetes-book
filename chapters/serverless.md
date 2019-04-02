# Serverless on Kubernetes
At first glance, serverless on Kubernetes sounds like a oxymoron. Aren't serverless and containers supposed to be competing and opposite technologies? 

The confusion lies in the unfortunate term "serverless." Afterall, serverless systems are actually made up of servers. As Alex Ellis explains in his [great talk at GOTO 2018](https://www.youtube.com/watch?v=yOpYYYRuDQ0), serverless is an *architectural pattern*. In that context, serverless framework should work for both Functions-as-a-Service (FaaS) platforms like AWS Lambda and on Kubernetes. 

> By 2020, in the cloud platform, serverless & k8s will essentially converge: "Kubernetes, just run my code" <br>
> \- Alexis Richardson (CEO Weaveworks)

One may then question why serverless even relevant in the context of Kubernetes. Can't we just use Google Cloud Functions with GKE by triggering off Pub/Sub or HTTP calls? That is true, but what happens if we want long running jobs that can scale to zero or want to interface with Redis or want to support jobs written in languages not supported by Cloud Functions? Lastly, what about the concerns with vendor lock-in? 

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
Google announced Knative at NEXT in 2018. Perhaps as the logo suggests, Knative seems more like an addon to Kubernetes, rather than a FaaS-centric approach leveraging Kubernetes technology. Knative requires Istio, so if you are looking for a lightweight options, Knative may not be for you. From a beta tester, Knative seemed like an easy add on to GKE users and an attempt to merge the gap between GCF and GKE. Expect some big news at NEXT 2019, but if you simply want functions, then Knative will be too much. 

### OpenFaaS
If Knative had Google's backing from the beginning, OpenFaaS was driven by one man's dream. Alex Ellis built a huge community around OpenFaaS with a very lightweight system compatible with Docker Swarm and Kubernetes. Out of all the services we evaluated, OpenFaaS had the easiest setup and great tutorials to get started. 

### Fn & Fission
Have not tried yet

### Kubeless

### OpenWhisk 
More Lambda replacement 


## Other Resources
- [Examining the FaaS on K8s Market](https://blogs.cisco.com/cloud/examining-the-faas-on-k8s-market)
- [Today in Serverless and Open Source](https://hackernoon.com/serverless-and-open-source-where-do-we-stand-today-dff8aec67026)
- [A Comparison of Serverless Frameworks for Kubernetes: OpenFaas, OpenWhisk, Fission, Kubeless and more](https://winderresearch.com/a-comparison-of-serverless-frameworks-for-kubernetes-openfaas-openwhisk-fission-kubeless-and-more/)
- [Guide to Serverless Technologies](https://thenewstack.io/guide-to-serverless-technologies-free-ebook-on-the-new-stack/)
- [Kubedex Serverless](https://kubedex.com/serverless/)
- [The Leading Open Source Serverless Solutions for Kubernetes](https://gravitational.com/blog/serverless-on-kubernetes/)
