# Monitoring

blurb about importance of monitoring and what to monitor

## Stackdriver

If you are using Google Kubernetes Engine, event exporter for Stackdriver Monitoring is enabled by default if cloud logging is enabled. For instructions on deploying to existing clusters, please see the official documentation [here](https://kubernetes.io/docs/tasks/debug-application-cluster/events-stackdriver/).

On Stackdriver, you can monitor several things:
- Incidents
- Events
- CPU Usage
- Disk I/O
- Network Traffic
- Pods

Building a dashboard using the above metrics was simple and straightforward, but making adjustments to the default settings became difficult. If you are looking for more robust monitoring solution, we recommend a combination of Prometheus + Grafana. It seems like [others](https://thenewstack.io/5-tools-monitoring-kubernetes-scale-production/) agree as well. 

If you would like to use a combination of Stackdriver and Prometheus, there are tools to export data from one another. We will cover exporting Stackdriver metrics to monitor GCP pieces with Prometheus below. 

- [Stackdriver to Prometheus Exporter](https://github.com/frodenas/stackdriver_exporter)
- [Prometheus to Stackdriver Exporter](https://github.com/GoogleCloudPlatform/k8s-stackdriver/tree/master/prometheus-to-sd)

## Prometheus + Grafana

The following guide is heavily inspired by Sergey Nuzhdin's post on [LWOLFS BLOG](https://blog.lwolf.org/post/going-open-source-in-monitoring-part-i-deploying-prometheus-and-grafana-to-kubernetes/). There he does a fantastic job laying out how to deploy Prometheus and Grafana to Kubernetes using Helm charts. However, it does not cover deploying the newer version of Prometheus (2.1) and configuring alerts. 

### Prometheus Setup
To begin, we need to download the values.yaml template to modify.

``` wget https://raw.githubusercontent.com/kubernetes/charts/master/stable/prometheus/values.yaml ```

This massive file consists of several things, but we are concerned with server, serFiles, and alertmanagerFiles. 

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

### Monitoring Custom Metrics
The Helm chart for Prometheus will set up the default monitoring metrics using kube-state-metrics (if enabled). What if you want to monitor your node.js app? You can use a [prom-client](https://github.com/siimon/prom-client) to format the metrics to be scraped by Prometheus. By default, Prometheus will scrape the '/metrics' endpoint, and the various client libraries will format the outputs to be read by Prometheus. 

The key to allowing Prometheus running on Kubernetes to reach this endpoint is to add the annotations in the Deployment. For example, to expose port 5111 to be scrape by Prometheus, add the following annotations in the metadata:

```
apiVersion: apps/v1beta1
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
Since event exporting is enabled by default with GKE, Stackdriver is a great option to monitoring GCP metrics. These include mature products such as PubSub, Compute, and BigQuery, as well as newer products in Cloud IoT, ML, and Firebase. (For a complete list of available metrics, see this [documentation](https://cloud.google.com/monitoring/api/metrics_gcp).) 

Exporting these events to Prometheus follows a similar procedure as exporting custom metrics. In fact, github user frodenas has made a [Docker image](https://github.com/frodenas/stackdriver_exporter) of an exporter available to easily deploy via Helm/Kubernetes. 

For example, if you want to scrape all PubSub related metrics, you need to create a Helm chart with the following deployment file:

```
apiVersion: extensions/v1beta1
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



