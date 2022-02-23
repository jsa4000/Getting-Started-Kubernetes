# Distributions

## Pre-Requisites

- Kubernetes Local Cluster (Docker for Desktop, Minikube, Kind (DiD), k3s/k3d, Microk8s, kubeadm (Vagrant))
- `Kubectl` binary (Package manager, Releases WebSite)

Other dependencies:

- helm, istioctl, k9s, etc..

## [Docker For Desktop](https://www.docker.com/products/docker-desktop)

**Docker Desktop** is consider the fastest way to containerize applications on your desktop.

Docker Subscription Service Agreement includes a change to the terms for Docker Desktop

- It remains free for small businesses (fewer than 250 employees AND less than $10 million in annual revenue), personal use, education, and non-commercial open source projects.
- It requires a paid subscription (Pro, Team or Business), for as little as $5 per user per month, for professional use in larger businesses.
- The effective date of these terms is August 31, 2021. There is a grace period until January 31, 2022 for those that will require a paid subscription to use Docker Desktop.
- The Docker Pro, Docker Team, and Docker Business subscriptions now include commercial use of Docker Desktop.
- Check out our FAQ for more information. Or read our latest blog.

## [Rancher Desktop](https://github.com/rancher-sandbox/rancher-desktop)

**Rancher Desktop** is an open-source desktop application for Mac and Windows. It provides Kubernetes and container management. You can choose the version of Kubernetes you want to run. You can build, push, pull, and run container images. The container images you build can be run by Kubernetes immediately without the need for a registry.

**Rancher Desktop** provides the following features in the form of a desktop application:

- The version of Kubernetes you choose
- Ability to test upgrading Kubernetes to a new version and see how your workloads respond
- Run containers, and build, push, and pull images (powered by `nerdctl` or `dockerd`)
- Expose an application in Kubernetes for **local** access

All of this is wrapped in an open-source application.

## [Rancher Server](https://rancher.com/docs/rancher/v2.6/en/)

**Rancher** was originally built to work with multiple orchestrators, and it included its own orchestrator called Cattle. With the rise of Kubernetes in the marketplace, **Rancher 2** exclusively deploys and manages Kubernetes clusters running anywhere, on any provider.

Rancher can provision Kubernetes from a hosted provider, provision compute nodes and then install Kubernetes onto them, or import existing Kubernetes clusters running anywhere.

- DigitalOcean
- AWS
- Azure
- GCP
- Custom (on-prem)

One Rancher server installation can manage thousands of Kubernetes clusters and thousands of nodes from the same user interface.

Rancher adds significant value on top of Kubernetes, first by centralizing authentication and role-based access control (RBAC) for all of the clusters, giving global admins the ability to control cluster access from one location.

It then enables detailed monitoring and alerting for clusters and their resources, ships logs to external providers, and integrates directly with Helm via the Application Catalog. If you have an external CI/CD system, you can plug it into Rancher, but if you don’t, Rancher even includes Fleet to help you automatically deploy and upgrade workloads.

Rancher is a complete container management platform for Kubernetes, giving you the tools to successfully run Kubernetes anywhere.

## [OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift)

**Red Hat® OpenShift®** is an enterprise-ready Kubernetes container platform built for an open hybrid cloud strategy. It provides a consistent application platform to manage hybrid cloud, multi-cloud, and edge deployments.

## [Kind](https://github.com/kubernetes-sigs/kind/)

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
# Create single cluster 
kind delete cluster 
```

## [K3s](https://github.com/k3s-io/k3s)

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

### Installation

The **install.sh** script provides a convenient way to download K3s and add a service to systemd or openrc.

To install k3s as a service, run:

```bash
# Install using edge version from Github
curl -sfL https://get.k3s.io | sh -

# Install using specified version and set kubeconfig permissions (/etc/rancher/k3s/k3s.yaml)
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" INSTALL_K3S_VERSION=v1.22.5+k3s1 sh - 

# Uninstall k3s
/usr/local/bin/k3s-killall.sh
```

A `kubeconfig` file is written to `/etc/rancher/k3s/k3s.yaml` and the service is automatically started or restarted. The install script will install K3s and additional utilities, such as `kubectl`, `crictl`, `k3s-killall.sh`, and `k3s-uninstall.sh`.

`K3S_TOKEN` is created at `/var/lib/rancher/k3s/server/node-token` on your server. To install on worker nodes, pass `K3S_URL` along with `K3S_TOKEN` or `K3S_CLUSTER_SECRET` environment variables, for example:

```bash
# Join workers to the cluster
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=XXX sh -
```

### Verify

```bash
# Get current context status
kubectl cluster-info

# Get Node Status
kubectl get nodes

# Get Node current capacity
kubectl top nodes

# Get all Pods status (Including control plane)
kubectl get pods --all-namespaces
```

### Kubeconfig

Copy or get the content from the created kubeconfig file at `/etc/rancher/k3s/k3s.yaml`

```bash
# Copy the content into a local file ~/.kube/k3s
cat /etc/rancher/k3s/k3s.yaml
# Replace the loopback address with IP Address of the host machine (use 'sed' or 'gsed')
sed -i 's/127.0.0.1/192.168.56.10/g' ~/.kube/k3s

# export current kubeconfig (or combine context)
export KUBECONFIG=$KUBECONFIG:~/.kube/k3s

# Verify Connection
kubectl cluster-info
kubectl version
kubectl get nodes

# Verify LoadBalanacer created by traefic (EXTERNAL-IP is local)
kubectl -n kube-system get svc
telnet 192.168.56.10 443
```

## [K3d](https://github.com/rancher/k3d/)

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
k3d --help
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

## [kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

Using **kubeadm**, you can create a minimum viable Kubernetes cluster that conforms to best practices. In fact, you can use kubeadm to set up a cluster that will pass the Kubernetes Conformance tests. kubeadm also supports other cluster lifecycle functions, such as bootstrap tokens and cluster upgrades.

The kubeadm tool is good if you need:

- A simple way for you to try out Kubernetes, possibly for the first time.
- A way for existing users to automate setting up a cluster and test their application.
- A building block in other ecosystem and/or installer tools with a larger scope.

You can install and use kubeadm on various machines: your laptop, a set of cloud servers, a Raspberry Pi, and more. Whether you're deploying into the cloud or on-premises, you can integrate kubeadm into provisioning systems such as Ansible or Terraform.

### Vagrant

Create a `Vagrantfile` with the following content

```ruby
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.hostname = "ubuntu-focal"
  config.vm.network :private_network, ip: "192.168.56.10"
  config.vm.synced_folder "files/", "/vagrant"

  # Virtual Box
  config.vm.provider :virtualbox do |vb|
    vb.memory = 2046
  end

  # KVM: vagrant up --provider=libvirt
  #config.vm.provider :libvirt do |libvirt|
  #  libvirt.memory = 2048
  #  libvirt.cpus = 2
  #end

  config.vm.provision :shell, path: "provision/ubuntu_update.sh"
  config.vm.provision :shell, path: "provision/addons_install.sh"
end

```

```bash
# Start the Virtual Machine
vagrant up

# login in to the machine
vagrant ssh

# Destroy the Virtual Machine
vagrant destroy

# Current VMs managed by vagrant
vagrant global-status
```

### Installation

https://computingforgeeks.com/deploy-kubernetes-cluster-on-ubuntu-with-kubeadm/ 

Install dependencies and `kubeadm`

```bash
# Update the apt package index and install packages needed to use the Kubernetes apt repository:
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

# Download the Google Cloud public signing key:
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

# Add the Kubernetes apt repository:
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

# Check the installation
kubectl version --client && kubeadm version
```

Disable Swap

```bash
# Turn off swap.
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
sudo swapoff -a
```

Enable kernel modules and configure sysctl.

```bash
# Enable kernel modules
sudo modprobe overlay
sudo modprobe br_netfilter

# Add some settings to sysctl
sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

# Reload sysctl
sudo sysctl --system
```

Install Container runtime

```bash
# Configure persistent loading of modules
sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF

# Install required packages
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates

# Add Docker repo
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Install containerd
sudo apt update
sudo apt install -y containerd

# Configure containerd and start service
sudo su -
mkdir -p /etc/containerd
containerd config default>/etc/containerd/config.toml

# restart containerd
sudo systemctl restart containerd
sudo systemctl enable containerd
systemctl status  containerd
```

Verification

```bash
# Make sure that the br_netfilter module is loaded
lsmod | grep br_netfilter

# Enable kubelet service.
sudo systemctl enable kubelet

# Pull container images:
sudo kubeadm config images pull

# f you have multiple CRI sockets, please use --cri-socket to select one:
sudo kubeadm config images pull --cri-socket /run/containerd/containerd.sock

# Bootstrap without shared endpoint, for Containerd
sudo kubeadm init \
  --pod-network-cidr=192.168.0.0/16 \
  --cri-socket /run/containerd/containerd.sock \
  --upload-certs \
  --control-plane-endpoint=192.168.56.10

#Configure kubectl using commands in the output:
mkdir -p $HOME/.kube
sudo cp -f /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Check cluster status:
kubectl cluster-info

# Set simpler alias for kubectl
alias k=kubectl

# join other nodes
kubeadm join 192.168.56.10:6443 --token bm9gfw.jeuz7l61ep9jeowl \
	--discovery-token-ca-cert-hash sha256:1b6a784255c0332a3d6c52a37131947a03e9f7142c717c68652b3f49375bff12
```

Install Kubernetes Network Plugin CNI

```bash
# Install Calino Network
kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml 
kubectl create -f https://docs.projectcalico.org/manifests/custom-resources.yaml

# Confirm that all of the pods are running
kubectl get pods --all-namespaces

# Confirm master node is ready
kubectl get nodes -o wide
```

Scheduling Pods on Kubernetes Control plane (Master) Nodes

```bash
# Remove taint to master nodes
kubectl taint nodes --all node-role.kubernetes.io/master-
```

Copy the kubeconfig into `~/.kube/vm-config`

```bash
# Copy the kubeconfig into the shared folder
cp ~/.kube/config /vagrant/vm-config

# Export the KUBECONFIG using the vm-config path
export KUBECONFIG=$KUBECONFIG:~/.kube/vm-config 

# Deploy the Metrics Server with the following command:
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# Get the current status of the cluster
kubectl top nodes
```
