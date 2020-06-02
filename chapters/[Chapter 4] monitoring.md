# Monitoring

One of the downsides of microservice architecture is increased complexity in monitoring. How does one monitor a cluster of distributed applications that are communicating with each other? First, we need to monitor the health of individual pods and applications. Is the pod scheduled and deployed as intended? Are the applications inside those pods running without errors and without  performance degradation? Second, we need to monitor the health of the entire Kubernetes cluster. Is Kubernetes properly handling the resource utilization of each node? What about the health of all of the nodes?

In this chapter, we’ll examine some of the native tools Kubernetes provides as well as some Google Cloud Platform (GCP)-specific and open-source tools that we’ve found useful in production. Please note that this chapter is by no means a comprehensive overview of all available monitoring solutions for Kubernetes users. However, a combination of StackDriver and Prometheus/Grafana have proved to be a robust and reliable tool for our IoT deployments on Google Kubernetes Engine (GKE).

<p align="center"> 
  <img src="https://github.com/Leverege/kubernetes-book/blob/master/images/Chapter%204/ToolSurvey.jpg" height="300">
</p>

Image Credit: The New Stack

## Kubernetes Native Tools

The most rudimentary level of monitoring is administered via native Kubernetes features such as probes, cAdvisor, heapster, and kube-state-metrics. Readiness probe checks the health of a container before a pod is pushed live. Liveness probe periodically monitors the pod by running a defined set of commands to ensure that the pod is running as intended. A failed probe initiates a restart of the pod in an attempt to escape the error state.

cAdvisor and heapster collect container resource usage. cAdvisor oversees all the pods in a node and collects overall machine resource utilization (CPU, memory, filesystem and network usage). Heapster aggregates this data across all the nodes in a cluster to provide an overview of the cluster’s health. Lastly, kube-state-metrics provides information via the Kubernetes API. This exports information such as the number of replicas the cluster has scheduled vs. currently available; number of pods running vs. stopped; and number of pod restarts. 

While these native tools provide a basic overview of the Kubernetes health, they do not store this data nor help visualize it in an easily consumable manner. Additionally, they cannot answer application level metrics (e.g. how fast is my ElasticSearch query, how many times did my API trigger an error, how many messages are being processed). To address this need, you will need more powerful tools.

## Stackdriver

If you’re using the Google Kubernetes Engine, event exporter for Stackdriver Monitoring is enabled by default if cloud logging is enabled. For instructions on deploying to existing clusters, please see the [Official Documentation](https://kubernetes.io/docs/tasks/debug-application-cluster/events-stackdriver/).

On Stackdriver, you can monitor several things:
- Incidents
- Events
- CPU Usage
- Disk I/O
- Network Traffic
- Pods

Building a dashboard using the above metrics is simple and straightforward, but making adjustments to the default settings to extract specific information is not well supported. If you’re looking for a more robust monitoring solution, we recommend a combination of Prometheus and Grafana. It seems like [others](https://thenewstack.io/5-tools-monitoring-kubernetes-scale-production/) agree as well. 

If you would like to use a combination of Stackdriver and Prometheus, there are tools to export data from each. We will cover exporting Stackdriver metrics to monitor GCP components with Prometheus below. One reason for doing this may be to monitor GCP-related metrics, such as the number of PubSub failed deliveries, BigQuery usage information, and Firebase status.

- [Stackdriver to Prometheus Exporter](https://github.com/frodenas/stackdriver_exporter)
- [Prometheus to Stackdriver Exporter](https://github.com/GoogleCloudPlatform/k8s-stackdriver/tree/master/prometheus-to-sd)

## Practical Monitoring with Prometheus & Grafana (Update June 2020)

There is an updated guide to Practical Monitoring with Prometheus & Grafana on Medium.

1. [Part I - Installing Prometheus + Grafana via Helm in 5 Minutes](https://medium.com/@yitaek/practical-monitoring-with-prometheus-grafana-part-i-22d0f172f993)
2. [Part II - Using Prometheus blackbox exporter for free uptime checks](https://medium.com/@yitaek/practical-monitoring-with-prometheus-grafana-part-ii-5020be20ebf6)

 
