# State of Managed Kubernetes

Since Amazon Elastic Container Service for Kubernetes (Amazon EKS) went GA in June, all three cloud providers now support a managed Kubernetes service in production. All three—​EKS, Azure Kubernetes Service (AKS), and Google Kubernetes Engine (GKE)—now also support Kubernetes version 1.10.x, reducing the gap GKE held since Google released what was then called Google Container Engine in 2015.

While the major differences in managed Kubernetes services extend to cloud-level differences from the providers (e.g. Azure is obviously more compatible with Windows-based products), there are several key billing and functionality differences in the three offerings. Since most cloud computing marketing is dominated by AWS, we’ll start with EKS, then work our way backward in terms of date released, namely AKS followed by GKE.

<image here> 

## EKS
Although EKS was the latest service to become GA on the market, according to a CNCF Survey in 2017, 63% of companies utilizing Kubernetes were already self-hosting Kubernetes on AWS. This high figure means that the barrier to adoption should be low for those existing Kubernetes users. EKS also accelerates the adoption process for those who were not running self-hosted Kubernetes. The main advantage and potential disadvantage of EKS (and even AWS) is the sheer volume of AWS products. If you or your company is already using other AWS services such as RedShift, Shield, Cloud Watch, or even some IoT products like Greengrass, integrating with EKS is a huge plus. EKS also provides high-availability (HA) modes as well as the most zones available based on the underlying AWS architecture. However, because AWS had previously focused on its non-Kubernetes version of container orchestration service, Amazon Elastic Container Service (ECS) and Fargate, the community support and involvement is neither as huge nor as focused compared to the other cloud offerings. If you were already using ECS, the incentive to switch to EKS may be small, unless you are thinking of hybrid cloud solutions.

Also, EKS is not as intuitive as AKS or GKE for beginners. Instead of using a click-and-deploy model, EKS requires a more custom setup. Namely, IAM role setup must be done outside the EKS initial setup, as well as needing kubectl or CloudFormation setup to add worker nodes to the master node instead of specifying this by default. All of this may be fine for experienced Kubernetes or AWS users, but for a beginner looking to deploy a managed Kubernetes solution, the setup time is significantly higher than AKS or GKE.

Lastly, AWS charges for master node usage. It charges $0.20/hr for the master node plus usage for worker nodes for the cluster. Pricing is always tricky to compare across cloud providers since billing is counted slightly differently (not to mention heavily discounted enterprise deals), but at face-value, EKS is significantly more expensive than AKS or GKE since master node usage is not covered by the service.

In summary:
Pros: Integration with other AWS tools, high number of Availability Zones, easy to migrate or integrate if already using AWS container options
Cons: Expensive, steep learning curve + set up time, relatively new and lacking other Kubernetes-specific features

## AKS
Azure released AKS in October 2017, providing managed Kubernetes service for free for Azure users. Azure only charges for Kubernetes VM usage, unlike AWS which charges for master node management fee. Similar to AWS, Azure originally had pushed an ECS-equivalent service called Service Fabric. Contrary to a popular misconception that Azure is only compatible with Windows-stack (.NET, C#, etc), AKS works seamlessly with Azure-hosted Windows and Linux VMs.

Despite an earlier release date than AWS, AKS seems to lag in terms of Kubernetes upgrade, support, or adoption numbers. While EKS and GKE supported Kubernetes 1.10.x relatively quickly since its release, AKS only recently started supporting it. Also, unlike EKS and GKE, master nodes are not offered in HA mode. It seems that Kubernetes adoption numbers on AKS are not as high as those on EKS and GKE. Considering pre-existing usage of Kubernetes on AWS and GKE’s early lead in Kubernetes development, community involvement in AKS seems to be low so far.

That is not to say that AKS features are lagging in all regards. AKS is currently the only provider for an incubator feature called Service Catalog, which allows for applications inside Kubernetes to use externally managed services (e.g. Azure databases). Microsoft has also been active in maintaining a popular Kubernetes package manager, Helm.

In summary:
- Pros: Works well with Windows VMs, only charged for VM usage (not master node) 
- Cons: Lags newest features/releases, smaller Kubernetes community on Azure

## GKE

With the exception of perhaps Netflix and Spotify, no other company has as much experience running containers in production as Google. As the original creator of Kubernetes and a huge open-source contributor along with Redhat, GKE brings the best user experience and added features. Unlike EKS and AKS, on GKE, you can easily customize a GKE deployment in various node pools and in HA by clicking a few buttons. Like AKS, GKE only charges for VM usage; generally, with sustained usage fees, GKE comes out to be the cheapest managed Kubernetes service of the three.

Aside from cost, the main advantage of GKE will always be fast access to the newest features and tools. GKE dashboard— along with Stackdriver logging and monitoring agents already embedded on its VMs— allows for easy monitoring of cluster health and usage. GKE also auto-scales your nodes for you, which is a really nice feature that requires custom setup on EKS and AKS— this feature is great for quick prototyping usage or load testing). Overall, GKE abstracts away a lot of the infrastructure setup, simplifying the onboarding experience for new users.

Ultimately the downsides of GKE come not from the technology, but more so in general lack of other tools and community on GCP itself. Whereas AWS boasts IAM and Azure with Active Directory, GKE has no real comparative product. In terms of enterprise support, GCP still lags behind AWS whose obsession with customers led them to the top, and Azure, whose roots in Windows has deep identities tied to enterprise.

In summary:

- Pros: lowest cost, experienced team-leading Kubernetes development, access to the newest features
- Cons: GCP usage is lowest amongst the big 3 cloud providers

