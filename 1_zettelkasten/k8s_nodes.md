# k8s_nodes
## 2022-07-09

### Status: #idea
### Tags:
[[K8s Architecture Big Picture]] [[cka]]
### References:
[[CKA Course]]

---

> [!cite]
> AKA as _worker nodes_, _k8s nodes_ or simple _machines where the containers actually runs_

## K8s Nodes Componentes

> [!summary] kubelet
> _K8s Agent_ that runs on each node.
> It communicated with _control plane_ and ensure that containers are run on its node as instructed by the _control plane_
> Also communicate back to _control plane_ container status and other data about containers

> [!summary] Container Runtime
> Not part as K8s itself. It is a separate piece of software that is responsible for actually running the containers on the machine.
> K8s supports multiple container runtime implementations. The most popular are [[docker]] and [[containerd]].
> 
> In K8s `1.23` the default Runtime is `containerd` since `dockershim` will be removed on release 1.24 as [[container_runtime_interface|Container Runtime Interface (CRI)]] 

> [!summary] kube-proxy
> Network proxy that runs on each node and handle some tasks related to providing networking between `containers` and `services` int he cluster

