# Multi-Cluster Kustomize Multi-Namespace Example

This example will deploy the [Kubernetes sample guestbook](https://github.com/kubernetes/examples/tree/master/guestbook/) application
using kustomize. The app will be deployed into multiple namespaces in multiple environments. A new `fleetFile` GitRepo 
configuration property is proposed to implement this functionality.

The application will be customized as follows per environment:

* Dev clusters: Only the redis leader is deployed and not the followers. The application is deployed to namespaces
  `fleet-mc-kustomize-dev1-example` and `fleet-mc-kustomize-dev2-example`.
* Test clusters: Scale the front deployment to 3. The application is deployed to namespaces
  `fleet-mc-kustomize-test1-example` and `fleet-mc-kustomize-test2-example`.
* Prod clusters: Scale the front deployment to 3 and set the service type to LoadBalancer. The application is deployed to
  namespace `fleet-mc-kustomize-prod-example`.

```yaml
kind: GitRepo
apiVersion: fleet.cattle.io/v1alpha1
metadata:
  name: kustomize
  namespace: fleet-default
spec:
  repo: https://github.com/rancher/fleet-examples
  paths:
  - path: multi-cluster/kustomize-multi-namespace
    fleetFile: fleet-dev-dev1.yaml
  - path: multi-cluster/kustomize-multi-namespace
  - fleetFile: fleet-dev-dev2.yaml
  - path: multi-cluster/kustomize-multi-namespace
    fleetFile: fleet-test-test1.yaml
  - path: multi-cluster/kustomize-multi-namespace
  - fleetFile: fleet-test-test2.yaml
  - path: multi-cluster/kustomize-multi-namespace
    fleetFile: fleet-prod.yaml

  targets:
  - name: dev
    clusterSelector:
      matchLabels:
        env: dev

  - name: test
    clusterSelector:
      matchLabels:
        env: test

  - name: prod
    clusterSelector:
      matchLabels:
        env: prod
```
