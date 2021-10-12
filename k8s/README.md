# K8S DEMO

## Pre-Requisites

- Kubernetes Local Cluster (Docker for Desktop, Minikube, Kind (DiD), k3s/k3d, Microk8s, kubeadm (Vagrant))
- `Kubectl` binary (Package manager, Releases WebSite)

Other dependencies:

- helm, istioctl, k9s, etc..

## Installation

### [Docker For Desktop](https://www.docker.com/products/docker-desktop)

**Docker Desktop** is consider the fastest way to containerize applications on your desktop.

Docker Subscription Service Agreement includes a change to the terms for Docker Desktop

- It remains free for small businesses (fewer than 250 employees AND less than $10 million in annual revenue), personal use, education, and non-commercial open source projects.
- It requires a paid subscription (Pro, Team or Business), for as little as $5 per user per month, for professional use in larger businesses.
- The effective date of these terms is August 31, 2021. There is a grace period until January 31, 2022 for those that will require a paid subscription to use Docker Desktop.
- The Docker Pro, Docker Team, and Docker Business subscriptions now include commercial use of Docker Desktop.
- Check out our FAQ for more information. Or read our latest blog.

### [Rancher Desktop](https://github.com/rancher-sandbox/rancher-desktop)

**Rancher Desktop** is an open-source desktop application for Mac and Windows. It provides Kubernetes and container management. You can choose the version of Kubernetes you want to run. You can build, push, pull, and run container images. The container images you build can be run by Kubernetes immediately without the need for a registry.

**Rancher Desktop** provides the following features in the form of a desktop application:

- The version of Kubernetes you choose
- Ability to test upgrading Kubernetes to a new version and see how your workloads respond
- Run containers, and build, push, and pull images (powered by `nerdctl`)
- Expose an application in Kubernetes for **local** access

All of this is wrapped in an open-source application.

### [Kind](https://github.com/kubernetes-sigs/kind/)

Primarily designed to test Kubernetes, **Kind** (Kubernetes in Docker) helps you run Kubernetes clusters locally and in CI pipelines using Docker containers as “nodes”.

It is an open source CNCF certified Kubernetes installer that supports highly available multi-node clusters and builds Kubernetes release builds from its source.

- Kind Installation

    ```bash
    # Download and move to user binaries folder
    curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.11.1/kind-darwin-amd64"
    chmod +x ./kind
    mv ./kind /usr/local/bin/kind

    # Test the intallation
    kind --version
    ```

- Creating a Single Kind Cluster

    ```bash
    # Create single cluster
    kind create cluster 

    # KubeConfig has been updated with the credentials for the new cluster
    cat ~/.kube/config
    kubectl config get-contexts     

    # Verify and switch the context
    kubectl config use-context 
    kubectl cluster-info

    # Get the node created and status
    kubectl get nodes 
    ```

- Create custom cluster

```bash
# Create config.yaml file for Kind
echo 'kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: control-plane
- role: control-plane
- role: worker
- role: worker
- role: worker' > config.yaml

# Create cluster using previous configuration
kind create cluster --config=config.yaml
rm config.yaml
```

- Deleting a cluster

    ```bash
    # Create single cluster  `k3d cluster delete CLUSTER_NAME`
    kind delete cluster 
    ```


### [K3s](https://github.com/k3s-io/k3s)

K3s is a lightweight tool designed to run production-level Kubernetes workloads for low-resourced and remotely located IoT and Edge devices.
K3s helps you run a simple, secure, and optimized Kubernetes environment on a local computer using a virtual machine such as VMWare or VirtualBox.

1. Download `k3s` from latest [release](https://github.com/k3s-io/k3s/releases/latest), x86_64, armhf, and arm64 are supported.
1. Run the server.

```bash
# Create single server
sudo k3s server &
# Kubeconfig is written to /etc/rancher/k3s/k3s.yaml
sudo k3s kubectl get nodes

# On a different node run the below. NODE_TOKEN comes from
# /var/lib/rancher/k3s/server/node-token on your server
sudo k3s agent --server https://myserver:6443 --token ${NODE_TOKEN}
```

### [K3d](https://github.com/rancher/k3d/)

**k3s**  is a platform-agnostic, lightweight wrapper that runs K3s in a docker container. It helps run and scale single or multi-node K3S clusters quickly without further setup while maintaining a high availability mode.

There are several options for the installation;

- use the install script to grab the latest release:
  - wget: `wget -q -O - https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash`
  - curl: `curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash`
- [Recommended] use the install script to grab a specific release (via `TAG` environment variable):
  - wget: `wget -q -O - https://raw.githubusercontent.com/rancher/k3d/main/install.sh | TAG=v5.0.0 bash`
  - curl: `curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | TAG=v5.0.0 bash`

- use [Homebrew](https://brew.sh): `brew install k3d` (Homebrew is available for MacOS and Linux)
  - Formula can be found in [homebrew/homebrew-core](https://github.com/Homebrew/homebrew-core/blob/master/Formula/k3d.rb) and is mirrored to [homebrew/linuxbrew-core](https://github.com/Homebrew/linuxbrew-core/blob/master/Formula/k3d.rb)

- K3s Installation

    ```bash
    # Install k3d v5.0.0 using curl method
    curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | TAG=v5.0.0 bash

    # Test installation
    k3d --help'
    ```

- Creating a Single K8s Cluster

    ```bash
    # Create single cluster  `k3d cluster create CLUSTER_NAME`
    k3d cluster create k3s-local

    # KubeConfig has been updated with the credentials for the new cluster
    cat ~/.kube/config
    kubectl config get-contexts     

    # Verify and switch the context
    kubectl config use-context k3d-k3s-local
    kubectl cluster-info

    # Get the node created and status
    kubectl get nodes 
    ```

- Creating a HA K8s Cluster

    ```bash
    # Create HA Kubernetes cluster with 3 Master nodes and 3 Worker nodes
    k3d cluster create k3s-ha \
        --api-port 6550 \
        --servers 3 \
        --agents 3 \
        --port 443:443@loadbalancer

    # Get the nodes created and statuses
    kubectl get nodes 

    # Get all Pods
    kubectl get pods --all-namespaces

    # Get All Services (Special ook to LoanBalancer)
    kubectl get services --all-namespaces
    ```

- Deleting a cluster

    ```bash
    # Create single cluster  `k3d cluster delete CLUSTER_NAME`
    k3d cluster delete k3s-local
    ```

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
# k run mytest --image=busybox  
kubectl run --generator=run-pod/v1 mytest -it --image=busybox sh
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

## Tools

### [Lens](https://github.com/lensapp/lens)

**Lens** - The Kubernetes IDE ("Lens IDE") is a distribution of the OpenLens repository with Team Lens specific customizations released under a traditional EULA.

Lens IDE provides the full situational awareness for everything that runs in Kubernetes. It's lowering the barrier of entry for people just getting started and radically improving productivity for people with more experience.

Lens IDE a standalone application for MacOS, Windows and Linux operating systems. You can download it free of charge for Windows, MacOS, and Linux from Lens IDE website.

### [K9s](https://github.com/derailed/k9s)

K9s provides a terminal UI to interact with your Kubernetes clusters. The aim of this project is to make it easier to navigate, observe and manage your applications in the wild. K9s continually watches Kubernetes for changes and offers subsequent commands to interact with your observed resources.

K9s is available on Linux, macOS and Windows platforms.

- Binaries for Linux, Windows and Mac are available as tarballs in the release page.
- Via Homebrew for macOS or LinuxBrew for Linux

    `brew install k9s`

## References

[Kubernetes Essential Tools](https://itnext.io/kubernetes-essential-tools-2021-def12e84c572)
[Running Kafka in Kubernetes](https://www.youtube.com/watch?v=aO2pv8W6oZU)
[Kubernetes Minimal Distributions](https://faun.pub/k3d-vs-k3s-vs-kind-vs-microk8s-vs-minikube-6949ebb93d18)