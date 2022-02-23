# Basics

## Pre-Requisites

- Kubernetes Local Cluster (Docker for Desktop, Minikube, Kind (DiD), k3s/k3d, Microk8s, kubeadm (Vagrant))
- `Kubectl` binary (Package manager, Releases WebSite)

Other dependencies:

- helm, istioctl, k9s, etc..

## Basic Commands

```bash
# Get kubernetes Version
kubectl version

# Get help
kubectl --help
# Get help for get
kubectl get --help
# Get help for create
kubectl create --help

#Get Cluster Info
kubectl cluster-info

# Get Nodes
kubectl get nodes
# Get Nodes (expanded)
kubectl get nodes -o wide
# Get Namespaces
kubectl get namespaces
# Get Namespaces (short)
kubectl get ns

# Get Pods
kubectl get pods
# Get Pods
kubectl get pods -w
# Get Pods (All namespaces, check the left side with the namespace)
kubectl get pods --all-namespaces
kubectl get pods -A
kubectl get pods -A -o wide
# Get Deployments
kubectl get deployments -A
# Get Services
kubectl get services -A

# Get composed
kubectl get pods,deployments,services --all-namespaces
# Get All
kubectl get all --all-namespaces

#Events
kubect get events

# Describe
# Get nodes description
kubectl get nodes
# Get the node description
kubectl describe node docker-desktop
# Get all the configmap
kubectl get pods -A
# Describe a configmap (check the namespace)
kubectl describe -n kube-system pods etcd-docker-desktop
# Get all the configmap
kubectl get configmap -A
# Describe a configmap
kubectl describe -n kube-system configmap kube-proxy

# Logs
kubectl logs -n kube-system kube-apiserver-docker-desktop
# Logs in realtime (follow)
kubectl logs -n kube-system kube-apiserver-docker-desktop -f
```

## Run containers

```bash
# Create Deployment

# Test like Docker (Will be deprecated)
kubectl run mytest -it --image=busybox sh
# Get the name of the pod and wait(watch)
kubectl get pods -w
kubectl get deployment
# Connect to the pod again
kubectl exec -it mytest-5b7dbdf84-8hpqx sh
# Delete previous deployment
kubectl delete deployment mytest 
# WAit until it has been deleted
kubectl get pods -w

# Create Pod
# k run mytest --image=busybox --rm
kubectl run mytest -it --image=busybox -- sh
# Connect to the pod again
kubectl exec -it mytest sh
# Use ls to list all the files and folders
> ls

# Put files into the pod
kubectl cp README.md mytest:/
# Check if the file is inside
kubectl exec -it mytest sh
# Check if the file is inside
> ls
> cat README.md
> echo "This is a test" > TEST.txt

# Get files from the pod
kubectl cp mytest:/TEST.txt TEST.txt

# Delete previous deployment
kubectl delete pod mytest
```

## Create Deployment

In the example we are going to use the following

```test
replicas: 3
port: 8080
paulbouwer/hello-kubernetes:1.8
type: LoadBalancer
Config env MESSAGE value
    env:
        - name: MESSAGE
            value: I just deployed this on Kubernetes!


NodePort
```

1. Create a single file yaml `helloworld.yaml` and configure all the properties, using load balancer
2. Create several resources:
   1. Service
   2. Deployment
3. Modify the message using the environmental variable MESSAGE using deployment 

```yaml
env:
- name: MESSAGE
    value: I just deployed this on Kubernetes!
```

1. Modify the message using the environmental variable MESSAGE using ConfigMap `helloworld-config`

```yaml
envFrom:
    - configMapRef:
        name: helloworld-config
```

Deploy into

``` bash
# Deploy resources into kubernetes
kubectl create -f helloworld.yaml

# Get al resources
kubectl get all
# Connect to the pod using the load balancer
http://localhost:8080/

# Following create it must by used apply method
kubectl apply -f helloworld.yaml
# Delete all resources contained in the file
kubectl delete -f helloworld.yaml

# Scale Up the instances to 5
kubectl scale --replicas=5 deployment/helloworld-deployment
# Scale Down the instances to 2
kubectl scale --replicas=2 deployment/helloworld-deployment
```

Also Check the Kubernetes Networking configuring NodePort

```bash
# Get the service
kubectl get service

curl -s http://localhost:8080/ | grep '<td>helloworld-deployment' 

for i in {1..15}; do curl -s http://localhost:8080/ | grep '<td>helloworld-deployment'; done

```
