# k8s_control_plane
## 2022-07-09

### Status: #idea
### Tags:
[[K8s Architecture Big Picture]] [[cka]]
### References:
[[CKA Course]]
---

> [!cite]
> Control Plane is a collection of multiple componentes reponsible for managing the cluster itself globally.
> Usually control plane components run on dedicated _controller_ machines.
> When we talk about _control plane_ we don't talk about servers, since it can run across multiple ones.

## Control Plane Components

> [!summary] kubernetes-api-server
> Serves and provide the Kubernetes API.
> Kubernetes API is the main way to interact with _control plan_ and by extension with _K8s cluster_

> [!summary] Etcd
> Backend datastore for K8s API
> Provides HA distributed storage for data that is relating to _K8s cluster_ state

> [!summary] kube-scheduler
> Handles _scheduling_, the process of selecting an available node in the cluster on which to run containers

> [!summary] kube-controller-manager
> Runs a collection of multiple _controller utilities_ in a single process. These controllers carry out a variety of automation-related tasks within the _k8s cluster_

> [!summary] cloud-controller-manager
> Provide interfaces between K8s and cloud platforms
> Only comes to play when you integrate _k8s_ with cloud services such as _AWS_, _Azure_ or _GCP_
