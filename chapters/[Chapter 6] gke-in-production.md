# Running Google Kubernetes Engine (GKE) in Production

After comparing the managed offerings from Amazon Web Services (AWS), Microsoft Azure, and Google Cloud Platform (GCP), Leverege chose Google Kubernetes Engine (GKE) to run its large-scale IoT solutions in production. Every organization’s needs are different, but given the unique constraints that the current IoT landscape poses, here’s why Leverege chose GKE and what we’ve learned. 

## Best Price
First and foremost, GKE provides the best price for any managed Kubernetes service. Unlike Amazon Elastic Container Service for Kubernetes (Amazon EKS), which charges for the management of master nodes ($0.20/hr), GKE and Azure Kubernetes Service (AKS) only charge for the virtual machines (VMs) running the K8 nodes. This means that master node usage, cluster management (auto-scaling, network policy, auto-upgrades), and other add-on services are provided free-of-charge. Due to Google’s automatic sustained use discount and the option to use pre-emptibles, we found that GKE ends up being slightly cheaper than AKS. 

**Disclaimer**: Pricing comparisons across the three vendors are tricky since enterprise discounts, exact configuration of machines, and storage/GPU needs vary significantly. But in general for a standard setup, Leverege found GKE to be the cheapest.

Low cost is extremely important for price-sensitive IoT projects, especially for those using a low-power wide-area (LPWA) network. Let’s take a tank monitoring solution for example. A typical deployment would have a small sensor reporting to the network a few times a day with a  very small data payload to conserve power. From our experience, customers are only willing to pay around $1/month per sensor to achieve an adequate return on investment (ROI) for a simple IoT system. Once the hardware and network costs are factored in, the budget left for cloud infrastructure becomes increasingly constrained. And this is before factoring in operational costs unrelated to pure technical support. 

## Superior Onboarding & Management Experience
When you are paying for a managed service, you expect a seamless onboarding experience and minimal intervention on your part to continually manage the infrastructure. With GKE, you get the best experience regarding Kubernetes management. 

When you begin, you can easily spin up a cluster with a few clicks. After enabling the Kubernetes Engine API, you can specify the VM type,the number of nodes, and then click create. GKE takes care of regional deployment, auto-scaling, auto-upgrades of both master and child nodes, as well as network policy configuration via Calico. While these are all things you can self-manage and deploy on AKS and Amazon EKS, it’s a free service provided and managed by Google so you can focus on application development on top of Kubernetes. 

The best part of GKE, in our experience, is the [cluster autoscaler](https://cloud.google.com/kubernetes-engine/docs/concepts/cluster-autoscaler) feature. GKE will automatically add or delete nodes when Kubernetes can no longer schedules Pods on the existing nodes. Cluster autoscaler is tied to a node pool, so you can autoscale different node pools when needed. This feature really shines when you run a mixed node pool of pre-emptibles and nodes reserved for intensive data workloads. Perhaps you elect to run a node pool of pre-emptibles for non-critical workflow that can scale on demand, while keeping some data-intensive or stateful sets on another node pool. GKE will be Kubernetes-aware and scale automatically for you, which is not provided by default on Amazon EKS or AKS. 

Another feature we like about GKE is its close integration with Stackdriver, which has come a long way in recent years. With Stackdriving agents already installed onto GKE nodes, you can easily use preset GKE monitoring charts and logging before electing to set up more custom systems (e.g. Prometheus/Grafana, ELK). 

## Leading Contributor to K8 & Related Work
Before Google open-sourced Kubernetes, it was running Borg and Omega internally for years. Back in 2014, Google shared that they were already firing up over two billion containers per week. No other player can match Google’s 10+ years of container management at that scale. This is most evident in the number of new features that get rolled into GKE before Amazon EKS or AKS. The newest versions of Kubernetes are often available on GKE only, not to mention the beta features including tensor processing unit (TPU) support. 

Google’s work also shines in Kubernetes (K8)-related technologies. Google has been an important contributor to Spinnaker, a battle-tested Continuous Deployment (CD) provider, as well as Istio, a cloud-native service mesh developed with IBM and Lyft. Google is also active on toolkits and extensions for machine learning on K8 (kubeflow) as well as supporting serverless workloads. As the announcement from Google NEXT 2018 showed, Google is committed to growing the Kubernetes community and is a great platform to start that journey. 

## Lessons Learned
After deploying some of the largest IoT systems in North America over the past few years Leverege learned some lessons with Kubernetes the hard way. Due to a huge community that Kubernetes has garnered, you can now easily find lots of tips online. However, we recommend that you start simple. Before messing around with complex namespace configurations, Spinnaker/Istio, and pod policies, start with the default namespace and learn the basics before you secure your cluster for production. 

### Use Preemptibles and Node Pools
Google provides preemptible VMs that provide no availability guarantees and last for 24 hours maximum at a heavy discount (~5x cheaper). Although preemptibles were initially marketed for running short term batch jobs, with multiple node pools running on GKE, you can run production loads at scale on a mix of preemptibles and reserved instances. Using node pools, we can leverage the power of Kubernetes to schedule pods based on need. 

As long as stateless applications can gracefully exit when the preemptible VMs send a SIGTERM signal, we can allow Kubernetes to schedule pods to another available preemptible VM. In the case when another preemptible VM is unavailable (either during an auto-scale event or after the termination signal was given to a prior node), we can schedule these workloads to a reserved instance. For critical or stateful sets, we can define node affinity values to always schedule them on reserved instances. Just by setting node affinity configs, you can start saving cost on your clusters. 

### Always Know Your Context
Whether you are using Helm or explicitly using kubectl commands, it is easy to forget your context and accidentally push the wrong environment to the wrong cluster. Safeguard yourself from these accidents by always specifying the context/cluster to which you want to push changes. You can also use command line helpers like this one when you start to have more than several clusters. 

### Manage Complexities
One of the downsides to Kubernetes, or perhaps microservice architectures more generally, is how quickly complexities can grow. You’ll need a good plan to manage complexities introduced by more layers or monitoring of your cluster, nodes, pods, and applications will suffer. 

Start out with consistent logging practices. Either pass context between services or use a tracing system for better observability. Needless to say, this will be extremely helpful when a cluster enters an unknown state and you’ll be able to more easily track down the message flow. 

There are various tools to help tame Kubernetes. At Leverege, we use Helm as a templating tool to package Kubernetes manifests as charts. Until you have a CD system set up, Helm can help with rolling updates and easy rollbacks. If you don’t have a monitoring system set up, become familiar with the tools Google provides by default. Familiarize yourself with cloud shell and kubectl commands to quickly diagnose and modify your manifests. 

### Static IPs for Ingress 
When creating your Deployment or Ingress templates on Kubernetes, it’s easy to forget to reserve static IPs for your public endpoints. The best practice is not to use the type LoadBalancer and use a small number of Ingress endpoints, but if IPs are not reserved or specified, Kubernetes might assign them to a new endpoint that you might not expect. 

### Set Update Windows
One of the nice features of GKE is auto-upgrades. If you’re running a regional deployment, you can upgrade your master nodes without downtime. However, always set your update windows so you can monitor changes in production. There might be hidden, unintended consequences to your key management, logging, or authentication schemes that might break due to an upgrade. 
