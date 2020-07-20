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
helm install rancher --namespace cattle-system --set hostname=rancher.my.org rancher-latest/rancher
kubectl -n cattle-system rollout status deploy/rancher
```