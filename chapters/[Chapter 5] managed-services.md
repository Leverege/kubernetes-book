# State of Managed Kubernetes

Since Amazon Elastic Container Service for Kubernetes (Amazon EKS) became generally available (GA) in June, all three major cloud providers now support a managed Kubernetes service in production. All three—Amazon ​EKS, Azure Kubernetes Service (AKS), and Google Kubernetes Engine (GKE)—​now also support Kubernetes version 1.10.x, reducing the gap GKE held since Google released what was then called Google Container Engine in 2015.

While the major differences in managed Kubernetes services extend to cloud-level differences from the providers (e.g. Azure is obviously more compatible with Windows-based products), there are several key billing and functionality differences in the three offerings. We will start with Amazon EKS, since Amazon Web Services (AWS) dominates most cloud computing marketing. We will then work our way backward in terms of date released, namely AKS followed by GKE.

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%205/ComparisonTable.png" height="500">
</p>

## Amazon Elastic Container Service (Amazon EKS)
Although Amazon EKS was the latest service to become GA on the market, according to a CNCF Survey in 2017, 63 percent of companies utilizing Kubernetes were already self-hosting Kubernetes on AWS. This high figure means that the barrier to adoption should be low for those existing Kubernetes users. Amazon EKS also accelerates the adoption process for those who were not running self-hosted Kubernetes. 

The main advantage and potential disadvantage of Amazon EKS (and AWS more generally) is the sheer volume of AWS products. If you or your company is already using other AWS services—e.g. RedShift, Shield, Cloud Watch, or IoT products like Greengrass—integrating with Amazon EKS is a huge plus. 
 
Amazon EKS also provides high-availability (HA) modes as well as the highest number of Availability Zones. However, because AWS had previously focused on its non-Kubernetes version of container orchestration service, Amazon Elastic Container Service (ECS) and Fargate, the community support and involvement is neither as large nor as focused as it is with the other cloud offerings. If you were already using ECS, the incentive to switch to Amazon EKS may be small, unless you are exploring hybrid cloud solutions.

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%205/AmazonSurvey.png" height="400">
</p>

Amazon Elastic Container Service for Kubernetes (Amazon EKS) is not as intuitive as Azure Kubernetes Service (AKS) or Google Kubernetes Engine (GKE) for beginners. Instead of using a click-and-deploy model, Amazon EKS requires a more custom setup. Namely, IAM role setup must be done outside the Amazon EKS initial setup, as well as needing kubectl or CloudFormation setup to add worker nodes to the master node instead of specifying this by default. All of this may be fine for experienced Kubernetes or AWS users, but for a beginner looking to deploy a managed Kubernetes solution, the setup time is significantly higher than AKS or GKE.

Lastly, AWS charges for master node usage. It charges $0.20/hr for the master node plus usage for worker nodes for the cluster. Pricing is always tricky to compare across cloud providers since billing is counted slightly differently (not to mention heavily discounted enterprise deals), but at face-value, Amazon EKS is significantly more expensive than AKS or GKE since master node usage is not covered by the service.

**Summary**:
- **Pros**: Integration with other AWS tools, high number of Availability Zones, and easy to migrate or integrate if you’re already using AWS container options
- **Cons**: Expensive, steep learning curve and long setup time, and relatively new so lacking certain Kubernetes-specific features

## Azure Kubernetes Service (AKS)
Azure released AKS in October 2017, providing managed Kubernetes service for free to Azure users. Unlike AWS, which charges for master node management fee, Azure only charges for Kubernetes virtual machine (VM) usage. Similarly to AWS, however, Azure originally had pushed an ECS-equivalent service called [Service Fabric](https://azure.microsoft.com/en-us/services/service-fabric/). Although there is a common misconception that Azure is only compatible with Windows-stack (.NET, C#, etc.), AKS works seamlessly with Azure-hosted Windows and Linux VMs.

Despite an earlier release date than Amazon, AKS seems to lag behind Amazon EKS and GKE in terms of Kubernetes upgrade, support, and adoption numbers. While Amazon EKS and GKE supported Kubernetes 1.10.x relatively quickly since its release, AKS only recently started supporting it. Additionally, unlike Amazon EKS and GKE, master nodes are not offered in high-availability (HA) mode. Considering pre-existing usage of Kubernetes on AWS and GKE’s early lead in Kubernetes development, community involvement in AKS seems to be low so far.

That is not to say that AKS features are lagging in all domains. AKS is currently the only provider for an incubator feature called Service Catalog, which allows for applications inside Kubernetes to use externally managed services (e.g. Azure databases). Microsoft has also been active in maintaining a popular Kubernetes package manager, Helm.

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%205/DeploymentSurvey.png" height="400">
</p>

**Summary**:
- **Pros**: Works well with Windows VMs, and only charges for VM usage (not master node)
- **Cons**: Newest features/releases lag behind and there is a smaller Kubernetes community on Microsoft Azure

## Google Kubernetes Engine (GKE)

With the exception of perhaps Netflix and Spotify, no other company has as much experience running containers in production as Google. As the original creator of Kubernetes and a huge open-source contributor along with Redhat, GKE brings the best user experience and added features. Unlike Amazon EKS and AKS, on GKE, you can easily customize a GKE deployment in various node pools and in HA by clicking a few buttons. Like AKS, GKE only charges for VM usage and GKE generally comes out as the cheapest managed Kubernetes service of the three (factoring in Google’s automatic sustained usage discount).

Aside from lower cost, the main advantage of GKE will always be fast access to the newest features and tools. GKE dashboard—along with Stackdriver logging and monitoring agents already embedded on its VMs—allows for easy monitoring of cluster health and usage. GKE also auto-scales your nodes for you, which is a really nice feature that otherwise requires custom setup when on Amazon EKS and AKS—this feature is great for quick prototyping usage or load testing. Overall, GKE abstracts away a lot of the infrastructure setup, simplifying the onboarding experience for new users.

Ultimately, the downsides of GKE come not from the technology, but from the general lack of other tools and community support on Google Cloud Platform (GCP) itself. Whereas Amazon Web Services (AWS) boasts IAM and Microsoft Azure touts Active Directory, GKE has no real comparative product. In terms of enterprise support, GCP still lags behind AWS, whose obsession with customers led them to the top, and Microsoft Azure, whose roots in the Windows ecosystem enabled it to leverage deep ties to enterprise IT infrastructure.

**Summary**:
- **Pros**: Has the lowest cost, Kubernetes development is driven by deep experience, and provides access to the newest features
- **Cons**: Google Cloud Platform (GCP) usage is lowest amongst the big three cloud providers

