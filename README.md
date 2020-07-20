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
