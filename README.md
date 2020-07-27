# K3D local k8s cluster

## Install k3d on mac

```shell
brew install k3d
brew install helm
```

### Remove the cluster

```shell
k3d cluster delete localdevr
```

### Create a cluster

```shell
k3d cluster create localdevr --api-port 6550 -p 980:80@loadbalancer -p 9443:443@loadbalancer -p 9999:9090@loadbalancer --servers 1 --agents 1
kubectl cluster-info
```

## Invoke API

```shell
TOKEN=$(kubectl get secrets -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='default')].data.token}"|base64 --decode)
curl https://0.0.0.0:6550/api -k -H "Authorization: Bearer ${TOKEN}"
```

## Getting the cluster token

```shell
k3d cluster list --token
```

## Rancher

### Certmanager

From https://rancher.com/docs/rancher/v2.x/en/installation/options/helm2/helm-rancher/

```shell
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml
kubectl create namespace cert-manager
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace cert-manager --version v0.12.0  jetstack/cert-manager
kubectl -n cert-manager get pods -w # wait until they are running
```

### Rancher

From https://medium.com/@jyeee/rancher-2-3-on-macos-with-minikube-and-helm-e83d26fb9552

```shell
kubectl create namespace cattle-system
helm install rancher rancher-latest/rancher --namespace cattle-system --set hostname=rancher.localdev
kubectl -n cattle-system get services
kubectl -n cattle-system get pods
kubectl -n cattle-system describe pods # Show status of creating containers (pulling images, etc)
kubectl -n cattle-system get ingresses # See the internal ip of rancher
kubectl -n cattle-system exec -it rancher-59d9584c98-6vlvc -- bash # open a shell on one node
```

### Uninstall rancher

```shell
helm uninstall rancher --namespace cattle-system
```


# Tekton

From https://github.com/tektoncd/pipeline/blob/master/docs/install.md#installing-tekton-pipelines-on-kubernetes


```bash
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
kubectl get pods --namespace tekton-pipelines --watch
```