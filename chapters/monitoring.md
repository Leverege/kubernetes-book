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

Building a dashboard using the above metrics was simple and straightforward, but making adjustments to the default settings became difficult. If you are looking for more robust monitoring solution, we recommend a combination of Prometheus + Grafana. It seems like others agree as well. 

[NewStack](https://thenewstack.io/5-tools-monitoring-kubernetes-scale-production/)

## Prometheus + Grafana

The following guide is heavily inspired by Sergey Nuzhdin's post on [LWOLFS BLOG](https://blog.lwolf.org/post/going-open-source-in-monitoring-part-i-deploying-prometheus-and-grafana-to-kubernetes/). There he does a fantastic job laying out how to deploy Prometheus and Grafana to Kubernetes using Helm charts. However, it does not cover deploying the newer version of Prometheus (2.1) and configuring alerts. 

### Prometheus Setup
To begin, we need to download the values.yaml template to modify.

``` wget https://raw.githubusercontent.com/kubernetes/charts/master/stable/prometheus/values.yaml ```

This massive file consists of several things, but we are concerned with server, serFiles, and alertmanagerFiles. 

First, let's set the server volume size and resource limits. 

Under server, find persistentVolume configuration. Make sure the persistentVolume is enough to have logs saves for however long you want. We kept it at 50Gi to fit under our node resource limits. 

```    persistentVolume:
      enabled: true
      accessModes:
        - ReadWriteOnce
      annotations: {}
      existingClaim: ""
      mountPath: /data
      size: 50Gi
      storageClass: ""
      subPath: ""```

Next, set the max memory limit based on the default heap size. 

```    resources:
      limits:
        memory: 1.5Gi
      requests:
        memory: 1.5Gi```

Now, you can create ingress, but since Prometheus does not provide basic auth out of the box, unless you plan to set one up for yourself, I would leave it at ClusterIP. Once you deploy Prometheus, you can forward the port to access the UI. 

#### Alert Configuration 
