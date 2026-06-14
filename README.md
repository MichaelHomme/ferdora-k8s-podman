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


## ReplicaSet 

Next-geneartion ReplicationController.

Examples for `kubectl`-commands:

```bash
kubectl create -f redis-rs.yaml
kubectl apply -f redis-rs.yaml
kubectl get replicasets
kubectl get rs
kubectl scale rs frontend --replicas=4
kubectl get rs frontend -o yaml
kubectl get rs frontend -o json
kubectl describe rs frontend
kubectl delete rs frontend
```


## Deployments


```bash
kubectl create -f def-deploy.yaml


kubectl create deployment nginx-deployment \
--image=nginx:1.20.2 --port=80 --replicas=3


kubectl create deployment nginx-deployment \
--image=nginx:1.20.2 --port=80 --replicas=3 \
--dry-run=client -o yaml > nginx-deploy.yaml


kubectl create deployment nginx-deployment \
--image=nginx:1.20.2 --port=80 --replicas=3 \
--dry-run=client -o json > nginx-deploy.json


kubectl create -f nginx-deploy.yaml
kubectl create -f nginx-deploy.json
```


Deployments and rollouts:

```bash
kubectl apply -f nginx-deploy.yaml --record
kubectl get deployments
kubectl get deploy -o wide
kubectl scale deploy nginx-deployment --replicas=4
kubectl get deploy nginx-deployment -o yaml
kubectl get deploy nginx-deployment -o json
kubectl describe deploy nginx-deployment
kubectl rollout status deploy nginx-deployment
kubectl rollout history deploy nginx-deployment
kubectl rollout history deploy nginx-deployment --revision=1
kubectl set image deploy nginx-deployment nginx=nginx:1.21.5 --record
kubectl rollout history deploy nginx-deployment --revision=2
kubectl rollout undo deploy nginx-deployment --to-revision=1
kubectl get all -l app=nginx -o wide
kubectl delete deploy nginx-deployment
kubectl get deploy,rs,po -l app=nginx
```


## DaemonSets

```bash

kubectl create -f fluentd-ds.yaml
kubectl apply -f fluentd-ds.yaml --record
kubectl get daemonsets
kubectl get ds -o wide
kubectl get ds fluentd-agent -o yaml
kubectl get ds fluentd-agent -o json
kubectl describe ds fluentd-agent
kubectl rollout status ds fluentd-agent
kubectl rollout history ds fluentd-agent
kubectl rollout history ds fluentd-agent --revision=1
kubectl set image ds fluentd-agent fluentd=quay.io/fluentd_elasticsearch/fluentd:v4.6.2 --record
kubectl rollout history ds fluentd-agent --revision=2
kubectl rollout undo ds fluentd-agent --to-revision=1
kubectl get all -l k8s-app=fluentd-agent -o wide
kubectl delete ds fluentd-agent
kubectl get ds,po -l k8s-app=fluentd-agent
```
