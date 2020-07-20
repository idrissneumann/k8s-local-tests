# K3D local k8s cluster

## Install k3d on mac

```shell
brew install k3d
brew install helm
```

## Create a cluster

```shell
k3d cluster create localdev --servers 1 --agents 1
kubectl cluster-info
```

## Invoke API

```shell
TOKEN=$(kubectl get secrets -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='default')].data.token}"|base64 --decode)
curl https://0.0.0.0:51725/api -k -H "Authorization: Bearer ${TOKEN}"
```

## Getting the cluster token

```shell
k3d cluster list --token
```

## Rancher

From https://rancher.com/docs/rancher/v2.x/en/installation/options/helm2/helm-rancher/

```shell
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace cert-manager  --version v0.12.0  jetstack/cert-manager
kubectl get pods --namespace cert-manager
kubectl create namespace cattle-system
```

From https://medium.com/@jyeee/rancher-2-3-on-macos-with-minikube-and-helm-e83d26fb9552

```shell
helm install rancher rancher-latest/rancher  --namespace cattle-system  --set hostname=rancher.localdev
kubectl -n cattle-system get services
kubectl -n cattle-system get pods
kubectl -n cattle-system get ingresses # See the ip of rancher, add the ip in your /etc/hosts with domain "rancher.localdev"
kubectl -n cattle-system exec -it rancher-59d9584c98-6vlvc -- bash # open a shell on one node
```

# Tekton

From https://github.com/tektoncd/pipeline/blob/master/docs/install.md#installing-tekton-pipelines-on-kubernetes


```bash
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
kubectl get pods --namespace tekton-pipelines --watch
```