# Grafana Customization in Restricted Network

## Disclaimer
> **Community Operators are operators which have not been vetted or verified by Red Hat. Community Operators should be used with caution because their stability is unknown. Red Hat provides no support for Community Operators.**

## Configure OLM on restricted network

Follow this documentation to [configure Operator Lifecycle Manager in a restricted network].  To deploy Grafana from OperatorHub, you will use the `community-operators` catalog.

## Use mirrored Grafana images

Deploy the Grafana community operator from OperatorHub.  The operator pod will throw an ImagePullBackOff error, but this will allow you to note the image tag supplied.

Patch the images with your mirror repository.  At the time of this writing, Grafana operator is using tag version `v3.9.0`.
```
oc patch deployment grafana-operator -p '{"spec":{"template":{"spec":{"containers":[{"args":["--grafana-image=nexus.example.com/grafana/grafana", "--grafana-plugins-init-container-image=nexus.example.com/integreatly/grafana_plugins_init"],"image":"nexus.example.com/integreatly/grafana-operator:v3.9.0","name":"grafana-operator"}]}}}}'
```

Mirror the following Grafana images to your mirror repository.  Tag versions change over time, so note the versions that are attempting to be pulled:
- quay.io/integreatly/grafana-operator
- quay.io/integreatly/grafana_plugins_init
- docker.io/grafana/grafana

The Grafana operator and deployment pods should now be running.

## License
GPLv3

## Author
Kevin Chung

[configure Operator Lifecycle Manager in a restricted network]: https://docs.openshift.com/container-platform/latest/operators/admin/olm-restricted-networks.html
