# State of Managed Kubernetes

Since Amazon Elastic Container Service for Kubernetes (Amazon EKS) became generally available (GA) in June 2018, all three major cloud providers now support a managed Kubernetes service in production. All three—Amazon ​EKS, Azure Kubernetes Service (AKS), and Google Kubernetes Engine (GKE)—​now also support Kubernetes version 1.16.x, reducing the gap GKE held since Google released what was then called Google Container Engine in 2015. Since then, Kubernetes adoption exploded, and the managed Kubernetes offering from all the major cloud providers became standardized. According to [Cloud Native Computing Foundation (CNCF)’s](https://www.cncf.io/wp-content/uploads/2020/03/CNCF_Survey_Report.pdf) most recent survey released in March 2020, Kubernetes usage in production jumped from 58% to 78% with managed Kubernetes services from AWS and GCP leading the pack.

While the major differences in managed Kubernetes services extend to cloud-level differences from the providers (e.g. Azure is obviously more compatible with Windows-based products), there are several key billing and functionality differences in the three offerings. We will start with Amazon EKS, since Amazon Web Services (AWS) dominates most cloud computing marketing. We will then work our way backward in terms of date released, namely AKS followed by GKE.

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%205/ComparisonTable.png" height="500">
</p>

## Amazon Elastic Container Service (Amazon EKS)
Considering AWS’s dominance on the cloud, it’s not surprising to see huge usage numbers for EKS and kops. The obvious advantage for existing AWS customers is to move workloads from EC2 or ECS to EKS with minimal modification to other services. However, in terms of managed Kubernetes features, I generally found EKS to lag GKE and AKS. There is a [public roadmap](https://github.com/aws/containers-roadmap) on Github for all AWS container services (ECS, ECR, Fargate, and EKS), but the general impression I get from AWS is a push for more serverless offerings (e.g. Lambda, Fargate) more so than container usage.

That isn’t to say that support from Amazon hasn’t been amazing nor do I think EKS is not Amazon’s priority. In fact, EKS is the only service to provide a financially-backed SLA to encourage enterprise usage. With EKS making RBAC and Pod Security Policies mandatory, it beats out GKE and AKS in terms of base-level security. Finally, now that GKE is also charging $0.10/hour per master node management, the pricing differences between the two clouds are even more negligible with reserved instances and other enterprise agreements in place.

Like many other AWS services, EKS provides a large degree of flexibility in terms of configuring your cluster. On the other hand, this flexibility also means the management burden falls on the developer. For example, EKS provides support for Calico CNI for network policies but requires the users to install and upgrade them manually. Kubernetes logs can be exported to CloudWatch, but it’s off by default and leaves it up to the developer to deploy a logging agent to collect application logs. Finally, upgrades are also user-initiated with the responsibility of updating master service components (e.g. CoreDNS, kube-proxy, etc) falling on the developer as well.

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%205/AmazonSurvey.png" height="400">
</p>

The most frustrating part with EKS was the difficulty in creating a cluster for experimentation. In production, most of the concerns above are solved with Terraform or CloudFormation. But when I wanted to simply create a small cluster to try out new things, using the CLI or the GUI often took a while to provision, only to realize that I missed a setting or IAM roles later in the process.

I found eksctl to be the most reliable method of creating a production-ready EKS cluster until we perfected the Terraform configs. The [eksworkshop](https://eksworkshop.com/) website also provides excellent guides for common cluster setup operations such as deploying the Kubernetes dashboard, standing up an EFK stack for logging, as well as integrating with other AWS services like X-Ray and AppMesh.

Overall, until EKS introduced [managed node groups](https://docs.aws.amazon.com/eks/latest/userguide/managed-node-groups.html) with Kubernetes 1.14 and above, I found the management burden on EKS fairly high, especially in the beginning. AWS is quickly catching up to the competition, but EKS is still not the best place to start for new users.

## Azure Kubernetes Service (AKS)
Surprisingly, AKS surpassed GKE in terms of providing support for newer Kubernetes versions (preview for 1.18 on AKS vs 1.17 on GKE as of June 2020). Also, AKS remains as the only service to not charge for control plane usage. Like EKS, master node upgrades must be initiated by the developer, but EKS takes care of underlying system upgrades.

Personally, I have not used AKS in production, so I can’t comment on technical or operational challenges. However, many in the community cite [unclear high-availability support for master nodes](https://thenewstack.io/comparison-amazon-azure-and-googles-managed-kubernetes-services/) and [unavailable node health monitoring](https://www.stackrox.com/post/2020/02/eks-vs-gke-vs-aks/) as a few concerns.

Still, Azure’s continued investment in Kubernetes is apparent through contributions to Helm (Microsoft acquired Deis who created Helm) as it graduated from CNCF. As Azure continues to close the gap with AWS, I expect AKS usage to grow with increasing support to address community concerns.

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%205/DeploymentSurvey.png" height="400">
</p>

## Google Kubernetes Engine (GKE)

While Google’s decision to begin charging for control plane usage for non-Anthos clusters stirred some frustrations among the developer community, GKE undoubtedly remains the king of managed Kubernetes in terms of features, support, and ease of use. For new users unfamiliar with Kubernetes, the GUI experience of creating a cluster and default logging and monitoring integration via Stackdriver makes it easy to get started.

GKE is also the only service to provide a completely automated master and node upgrade process. With the introduction of cluster maintenance windows, node upgrades can occur in a controlled environment with minimal overhead. Node auto-repair support also reduces management burdens on the developers.

Similar to many GCP products, GKE’s excellent managed environment does mean that customization may be difficult or sometimes impossible. For example, GKE installs kube-dns by default, and to use CoreDNS, you need to [hack around the kube-dns settings](https://medium.com/@yitaek/using-coredns-on-gke-3973598ab561). Likewise, if Stackdriver does not suit your needs for logging and monitoring, then you’ll have to uninstall those agents and manage other logging agents yourself.

Still, my experiences with GKE have been generally pleasant, and even considering the price increase, I still recommend GKE over EKS and AKS. The more exciting part with GKE is the growing number of services built on top such as managed Istio and Cloud Run. Managed service mesh and a serverless environment for containers will continue to lower the bar for migration to cloud and microservices architecture.

While GCP lags AWS and Azure in terms of overall cloud market share, it still holds its lead for Kubernetes in 2020.