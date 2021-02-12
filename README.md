# Grafana Customization for OpenShift 4

![GitHub release (latest by date)](https://img.shields.io/github/v/release/kevchu3/openshift4-grafana?color=blue&style=plastic)
![GitHub](https://img.shields.io/github/license/kevchu3/openshift4-grafana?color=blue&style=plastic)

## Background

The prepackaged Grafana operator that ships with OpenShift 4 in the openshift-monitoring namespace is a read-only implementation.  To import a custom dashboard, this project uses the Grafana operator from OperatorHub provided by the community.

## Disclaimer
> **Community Operators are operators which have not been vetted or verified by Red Hat. Community Operators should be used with caution because their stability is unknown. Red Hat provides no support for Community Operators.**

This project deploys a self-supported Grafana operator.  It does not modify the existing Prometheus operator, and so it leaves it in a supported state.

## Requirements

This was deployed and tested with:
* OpenShift 4.5
* Grafana Operator 3.5.0 from OperatorHub

## Deployment

### 1a. Deploy Grafana operator from OperatorHub

Create a new project (i.e. my-grafana) and deploy the community-supported Grafana operator from OperatorHub.  The Grafana operator creates Custom Resource Definitions (CRDs) for the following objects:
* grafanas.integreatly.org
* grafanadatasources.integreatly.org
* grafanadashboards.integreatly.org

To create a Grafana resource from the UI, navigate to Installed Operators -> Grafana Operator -> Grafana -> Create Grafana.  Configure your Grafana resource as desired, and press Create.

### 1b. Additional instructions for deploying in a restricted network

Follow these additional instructions to [deploy Grafana operator in a restricted network].

### 2. Deploy GrafanaDataSource for Prometheus

The grafana-serviceaccount service account was created alongside the Grafana instance.  We will grant it the cluster-monitoring-view cluster role.

```
oc adm policy add-cluster-role-to-user cluster-monitoring-view -z grafana-serviceaccount
```

The bearer token for this service account is used to authenticate access to Prometheus in the openshift-monitoring namespace.  The following command will display this token.

```
oc serviceaccounts get-token grafana-serviceaccount -n my-grafana
```

From the Grafana Data Source resource, press Create Instance, and navigate to the YAML view.  Create the [example GrafanaDataSource], substituting `${BEARER_TOKEN}` with the output of the command above.

```
oc create -f ./datasources/prometheus-grafanadatasource.yaml
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

[deploy Grafana operator in a restricted network]: restricted-setup.md
[example GrafanaDatasource]: ./datasources/prometheus-grafanadatasource.yaml
[example GrafanaDashboards]: ./dashboards/crds/
[JSON dashboards]: ./dashboards/json_raw/

