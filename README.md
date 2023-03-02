# Bookinfo GitOps

Repository with the necessary to deploy Bookinfo using the Bookinfo Operator and the Bookinfo Helm Charts with a GitOps approach.

## Deploy Bookinfo with Helm Charts

### Prerequisites

- Having ArgoCD installed in the cluster.
- If using Service Mesh, having it installed in the cluster.

### Procedure

> **NOTE:** If you are not using `bookinfo-charts` as the namespace to deploy the app, and your ArgoCD instance is not in the `openshift-gitops` namespace. Change the namespaces where corresponding.

Create the namespace:

```
oc new-project bookinfo-charts
```

Add the ArgoCD label to the namespace for ArgoCD to be able to manage it:

```
oc label namespace bookinfo-charts argocd.argoproj.io/managed-by=openshift-gitops
```

Add the namespace to the Service Mesh
```
echo "apiVersion: maistra.io/v1
kind: ServiceMeshMember
metadata:
  name: default
  namespace: bookinfo-charts
spec:
  controlPlaneRef:
    name: basic
    namespace: istio-system" | oc apply -f -
```

Deploy the ArgoCD application:

```
oc apply -f argo/application-chart.yaml
```

## Deploy Bookinfo with the Bookinfo Operator

### Prerequisites

- Having ArgoCD installed in the cluster.
- If using Service Mesh, having it installed in the cluster.
- Having the Bookinfo Operator installed in the cluster. Follow the instructions at: https://github.com/ernesgonzalez33/bookinfo-operator

### Procedure

> **NOTE:** If you are not using `bookinfo-operator-sample` as the namespace to deploy the app, and your ArgoCD instance is not in the `openshift-gitops` namespace. Change the namespaces where corresponding.

Give ArgoCD the ability to deploy `bookinfoes`:

```
oc apply -f argo/clusterrole.yaml
oc adm policy add-cluster-role-to-user bookinfo-editor-role system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller
```

Create the namespace:

```
oc new-project bookinfo-operator-sample
```

Add the ArgoCD label to the namespace for ArgoCD to be able to manage it:

```
oc label namespace bookinfo-operator-sample argocd.argoproj.io/managed-by=openshift-gitops
```

Add the namespace to the Service Mesh
```
echo "apiVersion: maistra.io/v1
kind: ServiceMeshMember
metadata:
  name: default
  namespace: bookinfo-operator-sample
spec:
  controlPlaneRef:
    name: basic
    namespace: istio-system" | oc apply -f -
```

Deploy the ArgoCD application:

```
oc apply -f argo/application-operator.yaml
```