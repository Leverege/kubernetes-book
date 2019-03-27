# Securing Kubernetes
Open source software has many benefits, but unfortunately it is more suspectible to security attacks given its open nature. Kubernetes is no exception. Look no further than [RedLock's report](https://redlock.io/blog/cryptojacking-tesla) on how hackers infiltrated Aviva and Gemalto's Kubernetes clusters to mine cryptocurriencies. Even Tesla had insecure Kubernetes dashboard open to the internet, which contained credentials to its AWS environment. 

Leverege recommends working with a security company to harden and secure your Kubernetes cluster, but here is a list of best practices to fix the common issues. 

## Managed Services 
An added benefit to using a managed Kubernetes service is that the service provider handles most of the security issues for you. For example, [GKE manages the Kubernetes control plane](https://cloud.google.com/kubernetes-engine/docs/concepts/security-overview) (API server, scheduler, controller manager, and etcd) and addresses some of the CIS Kubernetes benchmarks by default. 

Often times, the default configuration is not secure for production. The good news is that fixing most of these vulnerabilities is a matter of clicking a button on the UI. [CIS Google Cloud Platform benchmarks](https://www.cisecurity.org/benchmark/google_cloud_computing_platform/) lists 18 different configurations with regards to Kubernetes. Most are enabled by default (Stackdriving logging/monitoring, using COS as the node image, etc) and others are addressed by simply clicking on enabling automatic node repairs and upgrades. 

Google also provides [further instructions](https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster) to harden the cluster's security by adding network and pod security policies for fine-grain control. 

## CIS Kubernetes Benchmarks
CIS also provides [a list of benchmarks for Kubernetes](https://www.cisecurity.org/benchmark/kubernetes/) separately. As mentioned above, a managed service will address most of these benchmarks, but if you are managing your own Kubernetes cluster, you can use these guidelines to secure the master and worker nodes. 

Fortunately, the folks at Aqua Security open sourced a tool to automatically run checks against these benchmarks. You can run [kube-bench](https://github.com/aquasecurity/kube-bench) on the cluster to generate a report along with recommended remediation steps. 

### Tools
Alternatively, you can use these other tools to detect and fix common issues:
- [kubeaudit](https://github.com/Shopify/kubeaudit) 
- [kube-hunter](https://github.com/aquasecurity/kube-hunter)

## Other Resources
- [Kubernetes Security Best-Practices](https://dev.to/petermbenjamin/kubernetes-security-best-practices-hlk)
- [Kubernetes Security - Best Practice Guide](https://github.com/freach/kubernetes-security-best-practice)
