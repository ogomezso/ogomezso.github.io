#CFK
#k8s
#service_mesh
 
### Docs
#docs https://docs.confluent.io/operator/current/overview.html
#docs https://docs.confluent.io/operator/current/co-manage-cp.html#co-manage-cp

### Examples Repo:
#repos https://github.com/confluentinc/confluent-kubernetes-examples

### Quick Start video:
#videos https://www.youtube.com/watch?v=qepFNPhrL08

### Operator configuration:
##### Create a namespace call confluent:
```yaml
kind: Namespace
  apiVersion: v1
  metadata:
    name: confluent
    labels:
      name: confluent
```

```
kubectl apply -f confluent-namespace.yml
```

##### Set up Kubectl credential:
With Confluent Jfrog credentials (use Okta )
id:: 6163fed5-999b-49ca-8f6c-a9bc1f4eb2c3
```bash
kubectl -n confluent create secret docker-registry confluent-registry \                                                                                                                                        
		  --docker-server=confluent-docker.jfrog.io \
		  --docker-username=$USER \
		  --docker-password=$APIKEY \
		  --docker-email=$EMAIL  		  
```

##### Set up helm repo:
```bash
 helm repo add confluentinc https://packages.confluent.io/helm
```

##### download crd's:
```bash
helm pull confluentinc/confluent-for-kubernetes \
   --untar --untardir=<cfk-dir>
```

##### Install:
```bash
helm upgrade --install confluent-operator confluentinc/confluent-for-kubernetes
```

##### Delete:
```bash
kubectl delete deploy confluent-operator --force 
kubectl delete svc confluent-operator 
kubectl delete secret docker-registry confluent-registry
```
##### Non secure CP:
```YAML
---
apiVersion: platform.confluent.io/v1beta1
kind: Zookeeper
metadata:
  name: zookeeper
  namespace: confluent
spec:
  replicas: 3
  image:
     application: confluentinc/cp-zookeeper-operator:6.1.0.0
     init: confluentinc/cp-init-container-operator:6.1.0.0
  dataVolumeCapacity: 10Gi
  logVolumeCapacity: 10Gi
---
  apiVersion: platform.confluent.io/v1beta1
  kind: Kafka
  metadata:
    name: kafka
    namespace: confluent
  spec:
    replicas: 3
    image:
      application: confluentinc/cp-server-operator:6.1.0.0
      init: confluentinc/cp-init-container-operator:6.1.0.0
    dataVolumeCapacity: 10Gi
    metricReporter:
      enabled: true
---
apiVersion: platform.confluent.io/v1beta1
  kind: Connect
  metadata:
    name: connect
    namespace: confluent
  spec:
    replicas: 1
    image:
      application: confluentinc/cp-server-connect-operator:6.1.0.0
      init: confluentinc/cp-init-container-operator:6.1.0.0
    dependencies:
      kafka:
        bootstrapEndpoint: kafka:9071
  
---
apiVersion: platform.confluent.io/v1beta1
  kind: KsqlDB
  metadata:
    name: ksqldb
    namespace: confluent
  spec:
    replicas: 1
    image:
      application: confluentinc/cp-ksqldb-server-operator:6.1.0.0
      init: confluentinc/cp-init-container-operator:6.1.0.0
    dataVolumeCapacity: 10Gi
---
apiVersion: platform.confluent.io/v1beta1
  kind: ControlCenter
  metadata:
    name: controlcenter
    namespace: confluent
  spec:
    replicas: 1
	   image:
         application: confluentinc/cp-enterprise-control-center-operator:6.1.0.0
          init: confluentinc/cp-init-container-operator:6.1.0.0
	    dataVolumeCapacity: 10Gi
	    dependencies:
	      schemaRegistry:
	        url: http://schemaregistry.confluent.svc.cluster.local:8081
	      ksqldb:
	      - name: ksqldb
	        url: http://ksqldb.confluent.svc.cluster.local:8088
	      connect:
	      - name: connect
	        url: http://connect.confluent.svc.cluster.local:8083
---
apiVersion: platform.confluent.io/v1beta1
kind: SchemaRegistry
   metadata:
     name: schemaregistry
     namespace: confluent
   spec:
    replicas: 1
    image:
      application: confluentinc/cp-schema-registry-operator:6.1.0.0
      init: confluentinc/cp-init-container-operator:6.1.0.0
```
##### Deploy 
```bash
kubectl apply -f <yml name>
```

##### Port fordward Control Center: 
```bash
kubectl port-forward controlcenter-0 9021:9021
```

##### Expose through LoadBalancer:
```bash
kubectl expose svc controlcenter --type=LoadBalancer --name=control-center
```

##### K8s Events filter
``` bash
kubectl get events --sort-by='.metadata.creationTimestamp'
```
