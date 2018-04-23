# Helm
When you start using Kubernetes in production, you will quickly realize that managing yaml files is a pain. This is where [Helm](https://github.com/kubernetes/helm) comes in. Helm is a package and deployment manager for Kubernetes just like npm or yarn is for node. It helps you organize various yaml files into "charts" and makes it easier to update/roll-back changes as well as share applications as charts with others. 

## Quickstart
Helm consists of tiller (installed on your Kubernetes cluster) and a command line interface. The official [Helm documentation](https://docs.helm.sh/using_helm/#quickstart) does a great job getting you set up with Helm once you have a Kubernetes cluster up and running. It really is as simple as:

```
brew install kubernetes-helm
helm init
```

One thing we noticed were when working with multiple Helm environments, sometimes the Server and Client Helm version will diverge. In that case, you need to run ```helm init --upgrade``` or upgrade the kubernetes-helm version with brew (or equivalent) to align Helm and Tiller versions. 

## Helm Charts
Helm cli has a command to create a generic chart directory with common files in the following format: (for ```helm create foo```)

```
foo/
  |
  |- .helmignore   # Contains patterns to ignore when packaging Helm charts.
  |
  |- Chart.yaml    # Information about your chart
  |
  |- values.yaml   # The default values for your templates
  |
  |- charts/       # Charts that this chart depends on
  |
  |- templates/    # The template files
```

- Chart.yaml holds metatdata about the chart (name, version, and description) 
- values.yaml holds the default values for your chart templates 
- charts directory holds any dependency charts. Unless you are building a chart from component charts, this can be empty.
- templates directory holds all the yaml files you are used to push via kubectl cli (deployment, rbac, service, etc files) 

## Writing .yaml Files 
(flesh it out with an actual example later) 

## Testing the Charts
Before pushing the charts to the cluster, Helm allows you to do a dry run to simulate an install/upgrade. If you don't have a yaml linter installed with your text-editor, you can run ```helm lint <PATH>``` before doing the dry-run. 

```helm install --dry-run --debug <path to chart directory>```

We highly suggest doing a dry-run as error emitted during installation can leave ghost releases/versions of the chart on the machine. You can do ```helm delete --purge <release-name>``` to get rid of those files, but that also means you will be deleting your current release. 

## Deploy, Upgrade, Rollback, Delete
Once the dry-run is complete, deploying the chart is as simple as:

```helm install <path to chart directory>```

To upgrade a release, you can do 

```helm upgrade <release name> <path to chart directory>```

To rollback to a previous version, you can do

```helm rollback <release name> <revision version>```


