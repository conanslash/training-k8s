# Kubernetes Dashboard

[![GitHub release](https://img.shields.io/github/release/kubernetes/dashboard.svg)](https://github.com/kubernetes/dashboard/releases/latest)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/kubernetes/dashboard/blob/master/LICENSE)

Kubernetes Dashboard is a general purpose, web-based UI for Kubernetes clusters. It allows users to manage applications running in the cluster and troubleshoot them, as well as manage the cluster itself.


### Install

To deploy Dashboard, execute following command:

```shell
kubectl apply -f https://raw.githubusercontent.com/conanslash/training-k8s/main/kubernetes-yaml/k8s-dashboard-alt.yaml
```

Alternatively, you can install Dashboard using Helm as described at [`https://artifacthub.io/packages/helm/k8s-dashboard/kubernetes-dashboard`](https://artifacthub.io/packages/helm/k8s-dashboard/kubernetes-dashboard).

## Create An Authentication Token (RBAC)

Create User for Cluster Admin

```shell
kubectl --namespace kube-system create serviceaccount k8s-admin
kubectl create clusterrolebinding k8s-admin --serviceaccount=kube-system:k8s-admin --clusterrole=cluster-admin

```

Generate token

```shell
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep k8s-admin | awk '{print $1}')

```

OR

```shell
kubectl -n kubernetes-dashboard create token k8s-admin

```




### Access

To access Dashboard from your local workstation you must create a secure channel to your Kubernetes cluster. Run the following command:

Change the existing service type to LoadBalancer:

```shell
kubectl -n kubernetes-dashboard edit service kubernetes-dashboard

```
For Virtualbox User:-
And then forward do Port-forwarding from Virtuablbox NatNetwork Using from port 8443 -> 80:

Now access Dashboard at:

[`http://localhost:8443/`](http://localhost:8443/)







