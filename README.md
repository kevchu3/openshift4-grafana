Grafana Customization for OpenShift 4
=====================================

Background
----------

The prepackaged Grafana operator that ships with OpenShift 4 in the openshift-monitoring namespace is a read-only implementation.  To import a custom dashboard, this project uses the Grafana operator from OperatorHub provided by the community.  **Disclaimer: Community Operators are operators which have not been vetted or verified by Red Hat. Community Operators should be used with caution because their stability is unknown. Red Hat provides no support for Community Operators.**

Deploy
-----

1. Create a new project (i.e. my-grafana) and install the community-supported Grafana operator and Grafana instance.  In the Grafana instance YAML, make a note of the default username and password to log in.

2. In the openshift-monitoring project, we need to configure prometheus-k8s to listen on all addresses, not just localhost.  Edit the YAML for prometheus-k8s StatefulSet and change `'--web.listen-address=127.0.0.1:9090'` to `'--web.listen-address=:9090'` as follows:
```
spec:
  template:
    spec:
      containers:
        - name: prometheus
          args:
            - '--web.listen-address=:9090'
```

3. Log into the Grafana UI in the browser, using the default authentication configured in step 1.

4. Navigate to Configuration -> Data Sources -> Add Data Source -> Prometheus.  Use the following:
```
Name: Prometheus
HTTP URL: http://prometheus-operated.openshift-monitoring.svc.cluster.local:9090
```

Save and Test.

Dashboards
----------

Save the custom dashboard in this Git repository's `/dashboards` folder to your local machine.  From the Grafana UI, Create -> Import -> Upload .json File -> select the saved file

License
-------

GPLv3

Author
------

Kevin Chung
