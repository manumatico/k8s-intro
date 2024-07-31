# Introduction to Kubernetes - Lab

## Pre-requisites
The following tools are required in order to complete the lab. Follot the instructions to install the version that correspond to your system.

* Docker (https://docker.com)
* Kind (https://kind.sigs.k8s.io)
* Helm (https://helm.sh)
* k9s (https://k9scli.io)

## Deploy a kind cluster
Once Docker is instaed, you may proceed to install a kind cluster.

```bash
kind create cluster --config 00-kind-cluster-config.yaml
```
After the cluster is created, verify it with kubectl.
```
% kubectl get nodes
NAME                 STATUS   ROLES           AGE     VERSION
kind-control-plane   Ready    control-plane   4m55s   v1.30.0
kind-worker          Ready    <none>          4m34s   v1.30.0
kind-worker2         Ready    <none>          4m34s   v1.30.0
```

## Deploy a simple manifest
To verify your cluster, deploy the manifest in this folder.
```bash
% kubectl apply -f 01-nginx-deployment.yaml
deployment.apps/nginx-deployment created
service/nginx-service created
```
Forward service port
```bash
k port-forward  service/nginx-service 8080:80
```
Open browser at http://localhost:8080

## Deploy a basic helm chart
Add the example chart repository and install your first chart
```bash
helm repo add examples https://helm.github.io/examples
helm repo update

helm install -n hello-world-namespace --create-namespace hi-there examples/hello-world
```
Follow the instructions on the chart notes
```bash
helm get notes -n hello-world-namespace hi-there
```

## Deploy a more complex chart
Let's deploy Jupyter.

```
helm repo add jupyterhub https://hub.jupyter.org/helm-chart/
helm repo update

helm upgrade --install -njupyter --create-namespace  -f 02-jupyter-config.yaml jupyter jupyterhub/jupyterhub
```
Forward the service port 
```bash
kubectl --namespace=jupyter port-forward service/proxy-public 8080:http
```
Open browser at http://localhost:8080

## Deploy a Spark cluster
```bash
helm install kind-spark -nspark --create-namespace oci://registry-1.docker.io/bitnamicharts/spark
```
Test the spark cluster following the notes
```bash
kubectl get notes -nspark kind-spark
```

## Clean up
To clean up, you may destroy the cluster, and all the resources will be released back to the system.
```bash
kind delete cluster
```

