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
* OpenShift 4.15
* Grafana Operator 5.6.3 from OperatorHub
* OpenShift GitOps 1.11.1

## GitOps Approach

A GitOps approach using OpenShift GitOps (ArgoCD) is recommended over executing the above instructions manually.  Install OpenShift GitOps from OperatorHub, and then deploy the ArgoCD application into the openshift-gitops namespace as follows:

```
oc apply -f custom-grafana.application.yaml
```

## Manual Deployment

### 1. Deploy Grafana operator from OperatorHub

Create a new project (i.e. my-grafana) and deploy the community-supported Grafana operator from OperatorHub.

To create a Grafana resource from the UI, navigate to Installed Operators -> Grafana Operator -> Grafana -> Create Grafana.  Configure your Grafana resource as desired, and press Create.

### 2. Deploy GrafanaDataSource for Prometheus

The grafana-sa service account was created alongside the Grafana instance.  We will grant it the cluster-monitoring-view cluster and openshift-cluster-monitoring-view roles, as well as project edit role to access secrets in the my-grafana project.

```
oc adm policy add-cluster-role-to-user cluster-monitoring-view -z grafana-sa
oc adm policy add-cluster-role-to-user openshift-cluster-monitoring-view -z grafana-sa
oc adm policy add-role-to-user edit -z grafana-sa -n my-grafana
```

The bearer token for this service account is used to authenticate access to Prometheus in the openshift-monitoring namespace.  The following command will display this token.

```
oc create token grafana-sa --duration=8760h -n my-grafana
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

[example GrafanaDatasource]: ./datasources/prometheus-grafanadatasource.yaml
[example GrafanaDashboards]: ./dashboards/crds/
[JSON dashboards]: ./dashboards/json_raw/

