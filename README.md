# Grafana Customization for OpenShift 4

![GitHub release (latest by date)](https://img.shields.io/github/v/release/kevchu3/openshift4-grafana?color=blue&style=plastic)
![GitHub](https://img.shields.io/github/license/kevchu3/openshift4-grafana?color=blue&style=plastic)

## Background

The prepackaged Grafana operator that ships with OpenShift 4 in the openshift-monitoring namespace is a read-only implementation.  To import a custom dashboard, this project uses the Grafana operator from OperatorHub provided by the community.

Inspiration for this customization was taken from [hatmarch's cdc-and-debezium-demo repo].

## Disclaimer
> **Community Operators are operators which have not been vetted or verified by Red Hat. Community Operators should be used with caution because their stability is unknown. Red Hat provides no support for Community Operators.**

This project deploys a self-supported Grafana operator.  It does not modify the existing Prometheus operator, and so it leaves it in a supported state.

## Requirements

This was deployed and tested on:
* OpenShift 4.5
* Grafana Operator v3.5.0 from OperatorHub

## Deployment

### 1. Deploy Grafana operator from OperatorHub

Create a new project (i.e. my-grafana) and deploy the community-supported Grafana operator from OperatorHub.  The Grafana operator creates Custom Resource Definitions (CRDs) for the following objects:
* grafanas.integreatly.org
* grafanadatasources.integreatly.org
* grafanadashboards.integreatly.org

To create a Grafana resource from the UI, navigate to Installed Operators -> Grafana Operator -> Grafana -> Create Grafana.  Configure your Grafana resource as desired, and press Create.

### 2. Deploy GrafanaDataSource for Prometheus

A GrafanaDataSource must be created with a bearer token for the `grafana-serviceaccount` service account which authenticates to Prometheus in the `openshift-monitoring` namespace.  The following command will deploy a GrafanaDataSource and ConfigMap simultaneously, both with this bearer token.

```
oc process -f ./datasources/prometheus-grafanadatasource-template.yaml \
  PROJECT_NAME="my-grafana" \
  DATASOURCE_NAME=Prometheus \
  BEARER_TOKEN=$(oc serviceaccounts get-token grafana-serviceaccount -n my-grafana) | oc apply -f -
```

### 3. Import dashboards

The Grafana operator will look for GrafanaDashboard resources and deploy them.  I have provided some [example GrafanaDashboards], which can be deployed with:

```
oc create -f <dashboard>
```

If you are unable to deploy the GrafanaDashboard custom resources, I have also provided [JSON dashboards] which can be imported directly from within the Grafana console.


## License

GPLv3

## Author

Kevin Chung

[hatmarch's cdc-and-debezium-demo repo]: https://github.com/hatmarch/cdc-and-debezium-demo#custom-grafana-dashboard-for-debezium
[this script]: https://github.com/hatmarch/cdc-and-debezium-demo/blob/master/scripts/04-setup-custom-grafana.sh
[example GrafanaDashboards]: ./dashboards/crds/
[JSON dashboards]: ./dashboards/json_raw/

