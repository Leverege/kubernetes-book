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

## Prometheus + Grafana

The following guide is heavily inspired by Sergey Nuzhdin's post on [LWOLFS BLOG](https://blog.lwolf.org/post/going-open-source-in-monitoring-part-i-deploying-prometheus-and-grafana-to-kubernetes/). He does a fantastic job of laying out how to deploy Prometheus and Grafana to Kubernetes using Helm charts. However, at the time of writing this, Nuzhdin does not cover deploying the newer version of Prometheus (2.1) and configuring alerts. To fill this knowledge gap, we created a Prometheus + Grafana setup guide that goes through the setup process step-by-step. 

### Prometheus

Prometheus is an open-source, time-series monitoring tool developed by the Cloud Native Computing Foundation (CNCF) project—the foundation behind Kubernetes. It’s a flexible system that can collect metrics, run complex queries, display graphs, and trigger alerts based on custom rules. Default deployment of Prometheus on Kubernetes scrapes all the aforementioned metrics exposed by probes, cAdvisor, heapster, and kube-state-metrics. 

Additional benefits of Prometheus kick in when annotations are added to individual applications to expose custom metrics. As long as an endpoint can be reached with a predefined Prometheus-like format, Prometheus can scrape the metrics and aggregate with other information. The Prometheus server can also be linked to a persistent memory to store all the monitoring metrics. Lastly, Prometheus comes with an Alertmanager that can trigger alerts based on Prometheus-defined rules and notify DevOps engineers via email or Slack.

### Prometheus Setup
To begin, we need to download the values.yaml template to modify.

``` wget https://raw.githubusercontent.com/kubernetes/charts/master/stable/prometheus/values.yaml ```

This massive file consists of several things, but we are concerned with server, serverFiles, and alertmanagerFiles. 

First, let's set the server volume size and resource limits. 

Under server, find persistentVolume configuration. Make sure the persistentVolume is enough to have logs saves for however long you want. We kept it at 50Gi to fit under our node resource limits. 

```    
persistentVolume:
enabled: true
accessModes:
  - ReadWriteOnce
annotations: {}
existingClaim: ""
mountPath: /data
size: 50Gi
storageClass: ""
subPath: ""
```

Next, set the max memory limit based on the default heap size. 

```    
resources:
limits:
  memory: 1.5Gi
requests:
  memory: 1.5Gi
```

Now, you can create ingress, but since Prometheus does not provide basic auth out of the box, unless you plan to set one up for yourself, I would leave it at ClusterIP. Once you deploy Prometheus, you can forward the port to access the UI. 

#### Alert Configuration 
To trigger alerts, we need to modify the serverFiles.rules and alertmanagerFiles.alertmanager.yml. The alerts shown below were taken from [kayrus/prometheus-kubernetes](https://github.com/kayrus/prometheus-kubernetes/tree/master/prometheus-rules) github repo, and reformatted to match the newer Prometheus syntax. 

For example, to add the two memory usage alerts, you would add the following to the values.yaml file:
```
rules:
      groups:
      - name: MemoryUsage
        rules:
        - alert: NodeSwapUsage
          expr: (((node_memory_SwapTotal-node_memory_SwapFree)/node_memory_SwapTotal)*100) > 75
          for: 2m
          labels:
            severity: page
          annotations:
            summary: "{{$labels.instance}}: Swap usage detected"
            description: "{{$labels.instance}}: Swap usage usage is above 75% (current value is: {{ $value }})"
        - alert: NodeMemoryUsage
          expr: (((node_memory_MemTotal-node_memory_MemFree-node_memory_Cached)/(node_memory_MemTotal)*100)) > 75
          for: 2m
          labels:
            severity: page
          annotations:
            summary: "{{$labels.instance}}: High memory usage detected"
            description: "{{$labels.instance}}: Memory usage usage is above 75% (current value is: {{ $value }})"
```

Now Prometheus will trigger alerts when the expressions you defined above equal true. To deliver those alerts to email or Slack notifications, you need to modify the alertmanager.yml. For a basic Slack template, you can copy the configuration file described on the [Prometheus docs](https://prometheus.io/docs/alerting/notification_examples/). 

You can reach the Prometheus server at localhost:9090 by running the following command:
```
export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace default port-forward $POD_NAME 9090
```

If you are looking for common Prometheus alert rules (e.g. Postgres, kube-state metrics, etc), you can adopt some example rules compiled on the [Aweomse Prometheus alerts](https://awesome-prometheus-alerts.grep.to/rules.html) page. 

### Monitoring Custom Metrics
The Helm chart for Prometheus will set up the default monitoring metrics using kube-state-metrics (if enabled). But what if you want to monitor your node.js app? You can use a [prom-client](https://github.com/siimon/prom-client) to format the metrics to be scraped by Prometheus. By default, Prometheus will scrape the '/metrics' endpoint, and the various client libraries will format the outputs to be read by Prometheus.

The key to allowing Prometheus running on Kubernetes to reach this endpoint is to add the annotations in the Deployment. For example, to expose port 5111 to be scrape by Prometheus, add the following annotations in the metadata:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-awesome-app
spec:
  template:
    metadata:
      labels:
        app: example-app
      annotations:
        prometheus.io/port: '5111'
        prometheus.io/scrape: 'true'
```

You can read more about the configurable annotations more on the [Prometheus examples blob](https://github.com/prometheus/prometheus/blob/master/documentation/examples/prometheus-kubernetes.yml).

### Monitoring GCP Metrics
Since event exporting is enabled by default with GKE, StackDriver is a great option to monitoring GCP metrics. These include mature products such as PubSub, Compute, and BigQuery, as well as newer products in Cloud IoT, ML, and Firebase. (For a complete list of available metrics, see this [documentation](https://cloud.google.com/monitoring/api/metrics_gcp).) 

Exporting these events to Prometheus follows a similar procedure as exporting custom metrics. In fact, github user frodenas has made a [Docker image](https://github.com/frodenas/stackdriver_exporter) of an exporter available to easily deploy via Helm/Kubernetes. 

For example, if you want to scrape all PubSub related metrics, you need to create a Helm chart with the following deployment file:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: stackdriver-exporter
  annotations:
    prometheus.io/port: '9255'
    prometheus.io/scrape: 'true'
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: stackdriver-exporter
    spec:
      containers:
        - name: stackdriver-exporter
          image: frodenas/stackdriver-exporter
          ports:
            - containerPort: 9255
              protocol: TCP
          args:
            - "-google.project-id=<YOUR-GCP-PROJECT>"
            - "-monitoring.metrics-type-prefixes=pubsub.googleapis.com"
```

The important thing to notice here is the prometheus annotations to create an endpoint for Prometheus to scrape and specifying Google Project ID and monitoring metrics to export. 

### Grafana

Grafana is an open-source software for time-series analysis and visualization that has native plugins for Prometheus and other popular libraries (Elasticsearch, InfluxDB, Graphite, etc.). While Prometheus provides a rudimentary visualization functionality, it is limited to a simple time-series graph format. Grafana allows for easier visualization of all of the metrics exported by Prometheus to be consumed in various formats: status checks, histograms, pie charts, trends, and complex graphs. 

### Grafana Setup

The setup process for Grafana is very similar to Prometheus setup. You can enable ingress so you can easily access Grafana:

```
# update ingress to use SSL
ingress:
  enabled: true
  annotations:
    kubernetes.io/ingress.class: nginx
    kubernetes.io/tls-acme: 'true'
  hosts:
    - grafana.example.com
  tls:
    - secretName: grafana-server-tls
      hosts:
        - grafana.example.com
```

Next you need to add your Prometheus Deployment as your data source. To add Prometheus, choose Prometheus Data Source and set the url to be http://<your-release_name-prometheus-server>.default.svc.cluster.local via proxy access. 

Now, you can add dashboards to start monitoring your Kubernetes cluster. Sergey Nuzhdin has compiled a list of some great pre-made dashboards on [Medium](https://medium.com/@SergeyNuzhdin/going-open-source-in-monitoring-part-iii-10-most-useful-grafana-dashboards-to-monitor-kubernetes-7d22ac4645db).

If the default settings don't work, you may need to edit the aggregation/rate time from 1m to a higher number like 5m. 

