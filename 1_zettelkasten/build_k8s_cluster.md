# build_k8s_cluster
## 2022-07-10

## Status: #idea
## Tags:
[[k8s]] [[cka]]
## References:
[[CKA Course]]

---

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

## Index
```toc
 style: bullet 
 min_depth: 3 
 max_depth: 6
```
---

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

### Cluster Setup

> [!question] kube-adm
>Tool that simplify the setup of the cluster.

> [!summary] Cluster Setup
> 3 Cloud Guru's Servers
> 2 Worker Nodes:
> 	 - Ubuntu 20.04
> 	 - 2 Virtual CPUs
> 	 - 4 GiB Memory
> 	 - TAG: k8s_worker
> 	 - pass: 0G11084l
> 1 Control Plane Nodes:
> 	 - Ubuntu 20.04
> 	 - 2 Virtual CPUs
> 	 - 4 GiB Memory
> 	 - TAG: k8s_cp
> 	 - password: 0G11084l

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

### Runbook

#### Set machine's hostname for something more meaningful:
~~~shell
sudo hostnamectl set-hostname k8s-control
~~~ 

~~~shell
sudo hostnamectl set-hostname k8s-worker1
~~~

~~~shell
sudo hostnamectl set-hostname k8s-worker2
~~~

> [!important]
> Next steps must be done on contol-plane and workers nodes

#### Setup hosts file adding hostname <-> private ips mapping
For my _cloud_guru playground_ 

~~~
172.31.112.115   k8s-control
172.31.126.193   k8s-worker1
172.31.122.191   k8s-worker2
~~~

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

#### Install containerd as Container Runtime:

##### Enable _overlay_ and _br_netfilter_ kernel modules on containerd conf (ensure that those 2 modules starts when the container runtime do so)

~~~shell
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
~~~

~~~shell
overlay
br_netfilter
EOF
~~~

##### Enable both modules:

~~~shell
sudo modprobe overlay
~~~

~~~shell
sudo modprobe br_netfilter
~~~

#### System level config for kubernetes-cri setup

##### Create k8s CRI conf on sysctl.d

~~~shell
cat << EOF | sudo tee /etc/sysctl.d/99-kubernets-cri.conf 
~~~

~~~shell
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
~~~

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

##### Reload sysctl

~~~shell
sudo sysctl --system
~~~

##### Install containerd packages

~~~shell
sudo apt-get update && sudo apt-get install -y containerd
~~~

##### Create default containerd config file

~~~shell
sudo mkdir -p /etc/containerd/
~~~

~~~shell
sudo containerd config default | sudo tee /etc/containerd/config.toml
~~~

##### Restart containerd

~~~shell
sudo systemctl restart containerd
~~~

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

#### Check Swap Config

##### DISABLE swap

~~~shell
sudo swapoff -a
~~~

##### Check /etc/fstab

> [!warning]
> This file must NOT contain any swap partition.

~~~shell
sudo cat /etc/fstab
~~~

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

#### Install K8s Packages

##### Install Pre-required packages

~~~shell
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
~~~

##### add GPG key 

~~~shell
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
~~~

##### add repositories

~~~shell
cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
~~~

~~~shell
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
~~~

~~~shell
sudo apt-get update
~~~

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

##### Install K8s 

> [!important]
> version must be on the same version


~~~shell
sudo apt-get install -y kubelet=1.23.0-00 kubeadm=1.23.0-00 kubectl=1.23.0-00
~~~

##### Mark k8s packages as hold to be sure that can't be updated automatically

~~~shell
sudo apt-mark hold kubelet kubeadm kubectl
~~~

> [!important]
> Previous steps must be done on contol-plane and workers nodes

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

#### Initialize the Cluster

##### Setup the Cluster

> [!important]
> You only have to run this on `control-plane` node

~~~shell
sudo kubeadm init --pod-network-cidr 192.168.0.0/16 --kubernetes-version 1.23.0
~~~

##### Setup kubeconfig for kubectl

> [!hint]
> You can copy this commands from the output of `kubeadm init` command 

~~~shell
mkdir -p $HOME/.kube
~~~

~~~shell
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
~~~

~~~shell
sudo chown $(id -u):$(id -g) $HOME/.kube/config
~~~

<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

##### Check you can use kubectl

~~~shell
kubectl get nodes
~~~

> [!hint]
> At this point you will only see your `k8s-control` node and in _NotReady_ status since we didn't add network config and added the `k8s-worker` nodes to the cluster

##### Setup network plugin

> [!hint]
> For the playground we gonna use [Calico Network](https://projectcalico.docs.tigera.io/about/about-calico)

~~~shell
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
~~~


<div style="page-break-after: always; visibility: hidden"> \pagebreak </div>

#### Add workers to cluster

##### Get kubeadm token

On `k8s-control` node run:

~~~shell
kubeadm token create --print-join-command
~~~

##### Add workers to the cluster

> [!todo]
> On `k8s-worker` node run the output of previous command as sudo

##### Check Nodes actually join to the cluster

On `k8s-control`:

~~~shell
kubectl get nodes -w
~~~

---
