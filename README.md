# SRE Chaos Engineering & Observability

A brief description of what this project does and who it's for

<!-- TOC -->

- [SRE Chaos Engineering & Observability](#sre-chaos-engineering--observability)
  - [Motivation Chaos Engineering](#motivation-chaos-engineering)
  - [Let's deploy our Chaos Demo Cluster](#lets-deploy-our-chaos-demo-cluster)
    - [Deploy your minikube cluster](#deploy-your-minikube-cluster)
  - [Chaos Engineering Observability](#chaos-engineering-observability)
    - [Install Splunk Observability](#install-splunk-observability)
  - [Install litmus chaos](#install-litmus-chaos)
  - [verify](#verify)

<!-- /TOC -->

## Motivation Chaos Engineering


## Let's deploy our Chaos Demo Cluster

### Deploy your minikube cluster

```bash
minikube start --cpus 4 --memory 8192 --container-runtime=cri-o --disk-size=80G --driver=parallels --kubernetes-version v1.21.3
😄  minikube v1.23.2 auf Darwin 11.6
✨  Using the parallels driver based on user configuration
👍  Starting control plane node minikube in cluster minikube
💾  Downloading Kubernetes v1.21.3 preload ...
    > preloaded-images-k8s-v13-v1...: 546.39 MiB / 546.39 MiB  100.00% 3.01 MiB
🔥  Creating parallels VM (CPUs=4, Memory=8192MB, Disk=81920MB) ...
🎁  Vorbereiten von Kubernetes v1.21.3 auf CRI-O 1.22.0...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```


## Chaos Engineering Observability

### Install Splunk Observability

```bash
export splunkAccessToken=''

kubectl create ns splunk

helm install chaos-otel-collector --values splunk/values.yaml --set splunkAccessToken=$splunkAccessToken splunk-otel-collector-chart/splunk-otel-collector --namespace=splunk

NAME: chaos-otel-collector
LAST DEPLOYED: Sat Oct  2 08:41:01 2021
NAMESPACE: splunk
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

## Install litmus chaos
```bash
helm repo add litmuschaos https://litmuschaos.github.io/litmus-helm/
helm repo list

kubectl create ns litmus
```

```bash
helm install chaos litmuschaos/litmus --namespace=litmus

NAME: chaos
LAST DEPLOYED: Sun Oct  3 20:20:53 2021
NAMESPACE: litmus
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Thank you for installing litmus 😀

Your release is named chaos and it's installed to namespace: litmus.

Visit https://docs.litmuschaos.io to find more info.
```


## verify
```bash
kubectl get pods -n litmus -w                      
NAME                                     READY   STATUS    RESTARTS   AGE
chaos-litmus-frontend-5cf9b794cf-5pr4q   1/1     Running   0          3m1s
chaos-litmus-mongo-0                     1/1     Running   0          3m1s
chaos-litmus-server-6577bd685d-cv2k4     2/2     Running   0          3m1s
```

```bash
kubectl get svc -n litmus                     
NAME                            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                         AGE
chaos-litmus-mongo              ClusterIP   10.108.45.222   <none>        27017/TCP                       2m34s
litmusportal-frontend-service   NodePort    10.98.212.119   <none>        9091:30084/TCP                  2m34s
litmusportal-server-service     NodePort    10.109.51.58    <none>        9002:31659/TCP,9003:30482/TCP   2m34s
```

minikube ip
10.211.55.63

http://10.211.55.63:30084/

login
u: admin
p: litmus

```bash
kubectl get pods -n litmus -w  

NAME                                     READY   STATUS      RESTARTS   AGE
chaos-exporter-54c9645b9d-gsqvj          1/1     Running     0          11m
chaos-litmus-frontend-5cf9b794cf-kfzkk   1/1     Running     0          15m
chaos-litmus-mongo-0                     1/1     Running     0          14m
chaos-litmus-server-6577bd685d-s4rts     2/2     Running     0          15m
chaos-operator-ce-6b7dd9f95b-27lxz       1/1     Running     0          11m
event-tracker-c657946d-dm8vw             1/1     Running     0          11m
subscriber-5557867f8b-q7z8n              1/1     Running     0          11m
workflow-controller-78dbff5b5d-7wnlq     1/1     Running     0          11m
```


## deploy nginx demo app
```bash
kubectl apply -f https://k8s.io/examples/application/deployment.yaml

```

