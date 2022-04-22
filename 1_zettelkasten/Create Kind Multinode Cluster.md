# Create Kind Multinode Cluster
## 2022-04-21

### Status: #idea
### Tags:
[[kind]] [[k8s]] [[cfk]] [[docker]]
### References:
[[KIND Docs]]

---

##### Kind Config file

~~~yaml
kind: Cluster  
apiVersion: kind.x-k8s.io/v1alpha4  
nodes:  
  - role: control-plane  
  - role: worker  
  - role: worker  
~~~

~~~bash
kind create cluster --name k8s-playground --config kind-config.yaml
~~~

To be able to got external access to your pod 