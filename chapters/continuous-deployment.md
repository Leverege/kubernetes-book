# Continuous Deployment (CD) on Kubernetes

At scale, manual deployments start to become a bottleneck and a source of constant human error. As easy as it is to update or rollback changes, it is equally as easy to mix up contexts or namespaces and deploy applications to the wrong location. Unfortunately, Leverege found that there is no single, perfect continous deployment (CD) system that solves the need for every use case. Unlike continuous integration (CI) wherein industry leaders have emerged (e.g. Jenkins, TravisCI, CircleCI, GitlabCI), tool usage was heavily driven by a specific use case. 

This seems to be a widespread trend. According to New Stack's Kubernetes User Experience Survey, popular deployment tools are tied to existing CI systems (Gitlab, Circle, Travis). More illuminating fact is that 14% of respondents had custom, homgrown tools and 12% used other vendors with single mention, reflecting the reality that there is no clear leader. 

<https://thenewstack.io/jenkins-kubernetes-cd-pipelines/> 

In this chapter, we will review some popular tools and explain our reasoning for choosing Keel and Spinnaker for each of the use cases. 

## CD Tools
https://engineering.opsgenie.com/cloud-native-continuous-integration-and-delivery-tools-for-kubernetes-e6ea34d308c

https://www.weave.works/blog/continuous-delivery-tools-for-kubernetes-part-2 

## Helm + CI Tool

## Spinnaker
Initially, we chose Spinnaker as our CD tool of choice given its support from Netflix and community support (especially from Google). However, we quickly found out that Spinnaker was very bloated for the use case we wanted to support. We simply wanted developers to quickly write code and deploy to a development/QA cluster for testing, and did not need rolling canary, red-black deployment, or A/B testing. 

<other blurb here> 

## Keel
After searching for a lightweight tool, we came across this [Medium post](https://medium.com/donna-legal/ci-cd-with-less-fluff-more-awesome-28af61288a03) by Rik Nauta at Donna. The requirements were very similar:

- Quick, lightweight setup
- No open endpoint
- Integration with Helm
- Integration with GCR

The following directions are modified from Rik's original post based on new updates to Keel. 

### Installation

To install Keel, deploy Keel's [official Helm chart](https://github.com/keel-hq/keel/tree/master/chart/keel). 

```helm upgrade --install keel --namespace keel keel/keel```

In order to enable PubSub triggers off GCR, add the following to values.yaml:

```
# Google Container Registry
# GCP Project ID
gcr:
  enabled: true
  projectId: <google-project-id>
  pubSub:
    enabled: true

# base64 encoded json of GCP service account
# more info available here: https://cloud.google.com/kubernetes-engine/docs/tutorials/authenticating-to-cloud-platform
# e.g. --set googleApplicationCredentials=$(cat <JSON_KEY_FIEL> | base64)
googleApplicationCredentials: <cloud-storage-editor service-account in base64> 
```

If you want to disable Keel from self-updates, you may want to turn it off from polling every 3 minutes. 

### Helm Deployment
Now to instruct Keel to respond to new PubSub triggers, add the following to each Helm deployment's `values.yaml` file:

```
image:
  repository: gcr.io/<your-docker-image>
  tag: "1.0.0"

keel:
  # keel policy (all/major/minor/patch/force)
  policy: all
  # images to track and update
  images:
    - repository: image.repository
      tag: image.tag 
```

When a new image is availble, Keel will now upgrade release values. 

### Misc Tips
Keel also provides approval methods with Slack. If default values are kept, approvals work seamlessly (i.e. Slack bot named Keel). However, when bot names are changed, you need to explicitly mention the Slack bot name (@your-bot-name approve default/docker-image:1.0.1) or the bot will not trigger. Also, if the bot lives in private Slack channels, it currently seems bit buggy. 

Also, in our specific use case, based on a multitenant system, sometimes we have multiple GCRs to host each customer Docker images. In this case, currently there is no method to listen to multiple GCR PubSub triggers so a separate Keel bot is needed per GCR context. 
