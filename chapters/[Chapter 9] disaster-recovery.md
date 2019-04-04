# Disaster Recovery & Back Ups
Although Kubernetes (and especially managed Kubernetes services) provide inherent reliability and resiliency with self-healing capabilities, disaster recovery plans are still needed in production to protect against both human error (e.g accidentally deleting a namespace/cluster)and failures of infrastructure outside of Kubernetes (e.g. persistent volumes). 

## Back Ups are Needed in HA Clusters Still
A key point that is often lost when running services in high availability (HA) mode is that HA (and thus replication) is not the same as having backups. HA protects against zonal failures, but it will not protect against data corruption or accidental removals. It is very easy to mix up context or namespaces and accidentally delete or update the wrong resources. Since Kubernetes is a declarative system, you can easily revert back in terms of Kubernetes resources, but attached persistent volumes may not recover easily. 

## Backups
The cluster state for Kubernetes is stored in the etcd datastore. Fortunately, managed Kubernetes services take care of etcd maintenance so Kubernetes does not suffer a catastrophic failure on the master plane. If a managed service is not an option, [Kubernetes document](https://kubernetes.io/docs/getting-started-guides/ubuntu/backups/) has detailed instructions for backing up etcd data. 

As for Kubernetes resources, backing up resource state can easily be achieved by using git with Kubernetes manifest files. For example, if you have Helm charts, you can store Helm Charts via Chart Museum and check in values.yaml files to git. Another option is to use a tool like [kube-backup](https://github.com/pieterlange/kube-backup) to sync Kubernetes resource states to git. 

This leaves us with persistent volumes for stateful workloads to back up. Perhaps you are running elastic search as a StatefulSet or have custom databases for a specific need. For example, Leverege uses a self-managed TimescaleDB for a timeseries database within Kubernetes. Since data is stored in an attached persistent disk, we needed to automate backups for it in the case of failures. 

## Velero/Ark
[Velero](https://github.com/heptio/velero/) is an open-source back up tool from Heptio that works well with persistent disks. Velero is compatible with popular data storage options (e.g. S3, GCS, Azure Storage) and that bucket to be set up prior to deployment. There is a [Helm chart](https://github.com/helm/charts/tree/master/stable/ark) available for easy installs, but it currently lags behind the official release version (it also still uses the old name, ark, to add to the confusion). Both the Helm chart and the manual installation steps listed on the documentation website detail how to create snapshots, restore from backups, and also schedule backups. 

Although Leverege has not seen the need to use Velero in production to recover from failures, we have also found it a good tool to use in testing and replay capabilities to troubleshoot and debug issues with backed up data. Velero can also be used to migrate data between projects as well. 