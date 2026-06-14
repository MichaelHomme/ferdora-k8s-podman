# Setting up K8s on Fedora 44 using podman

## Minikube setup

```bash
minikube start --driver=docker --nodes=4 --disk-size=10g --cpus=4 --memory=10g -p dataplatform
```
## Setup token for the cluster

```bash
export TOKEN=$(kubectl create token default)
echo $TOKEN
kubectl create clusterrole api-access-root --verb=get --non-resource-url=/*
kubectl create clusterrolebinding api-access-root --clusterrole api-access-root --serviceaccount=default:default
export APISERVER=$(kubectl config view | grep https | cut -f 2- -d ":" | tr -d " ")
echo $APISERVER
kubectl cluster-info
curl $APISERVER --header "Authorization: Bearer $TOKEN" --insecure
curl $APISERVER/api/v1 --header "Authorization: Bearer $TOKEN" --insecure
```

## Creating file from imperative run

Create a file usin `kubectl run ...`
```bash
kubectl run nginx-pod --image=nginx:1.22.1 --port=80 --dry-run=client -o yaml > nginx-pod.yaml
```
