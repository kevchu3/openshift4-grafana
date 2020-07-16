# Grafana Customization for OpenShift 4

![GitHub release (latest by date)](https://img.shields.io/github/v/release/kevchu3/openshift4-grafana?color=blue&style=plastic)
![GitHub](https://img.shields.io/github/license/kevchu3/openshift4-grafana?color=blue&style=plastic)

## Background

The prepackaged Grafana operator that ships with OpenShift 4 in the openshift-monitoring namespace is a read-only implementation.  To import a custom dashboard, this project uses the Grafana operator from OperatorHub provided by the community.

## Disclaimer
> **Community Operators are operators which have not been vetted or verified by Red Hat. Community Operators should be used with caution because their stability is unknown. Red Hat provides no support for Community Operators.**

In addition, this project modifies the Prometheus operator, which is [unsupported].

## Requirements

This was deployed and tested on:
* OpenShift 4.2
* OpenShift 4.1

For OpenShift 4.3, the built-in Prometheus operator is inaccessible through the steps outlined in this repository.  I have tested [hatmarch's cdc-and-debezium-demo repo] with success, and was able to deploy by running [this script].

## Deployment

### 1. Create namespace

Create a new project (i.e. my-grafana) and install the community-supported Grafana operator and Grafana instance.  In the Grafana instance YAML, make a note of the default username and password to log in.

### 2. Configure prometheus-k8s StatefulSet

In the openshift-monitoring project, we need to configure prometheus-k8s to listen on all addresses, not just localhost.  Edit the YAML for prometheus-k8s StatefulSet and change `'--web.listen-address=127.0.0.1:9090'` to `'--web.listen-address=:9090'` as follows:
```
spec:
  template:
    spec:
      containers:
        - name: prometheus
          args:
            - '--web.listen-address=:9090'
```

### 3. Configure Datasource and import dashboards

#### 3a. Configure via Grafana UI

Log into the Grafana UI in the browser, using the default authentication configured in step 1.

Navigate to Configuration -> Data Sources -> Add Data Source -> Prometheus.  Use the following:
```
Name: Prometheus
HTTP URL: http://prometheus-operated.openshift-monitoring.svc.cluster.local:9090
```

Save and Test.

I have provided some example [JSON dashboards] in this repository.  Save the example custom dashboard in this Git repository's `/dashboards` folder to your local machine.  From the Grafana UI, Create -> Import -> Upload .json File -> select the saved file

#### 3b. Configure via Grafana operator CRDs

The Grafana operator creates Custom Resource Definitions (CRDs) for both `grafanadatasources.integreatly.org` and `grafanadashboards.integreatly.org`.

You can view the GrafanaDatasources in the cluster with: `oc get grafanadatasources`.  Import the [example GrafanaDatasource] with: `oc create -f <datasource>`

You can view the GrafanaDashboards in the cluster with: `oc get grafanadashboards`.  Import the [example GrafanaDashboard] with: `oc create -f <dashboard>`

## License

GPLv3

## Author

Kevin Chung

[unsupported]: https://docs.openshift.com/container-platform/4.3/monitoring/cluster_monitoring/configuring-the-monitoring-stack.html#maintenance-and-support_configuring-monitoring
[hatmarch's cdc-and-debezium-demo repo]: https://github.com/hatmarch/cdc-and-debezium-demo#custom-grafana-dashboard-for-debezium
[this script]: https://github.com/hatmarch/cdc-and-debezium-demo/blob/master/scripts/04-setup-custom-grafana.sh
[JSON dashboards]: ./dashboards/json_raw/
[example GrafanaDatasource]: ./datasources/
[example GrafanaDashboard]: ./dashboards/crds/

