#### Tags
#containers 
#CFK 
#service_mesh 
#data_mesh
#### Linked Pages
[[Confluent for Kubernetes]]
[[Istio]]


# Papers

#papers https://assets.confluent.io/media/?mediaId=D2CFEAC9-AAA0-4A3D-AE2C1AAFFC6881D7

# Talks

### Confluent:

#### Gwen Saphira: Kafka and the Service Mesh

#videos #talks  https://www.youtube.com/watch?v=Fi292CqOm8A
- Some ideas for protocool
- 4 ways to integrate

#### Kai Waehner: Microservice Architectures with Apache Kafka, Kubernetes and Service Mesh (Envoy, Istio, Linkerd)

#videos  https://www.youtube.com/watch?v=Us_C4RFOUrA

##### Key Ideas:
4 Patterns:
- Proxys on both sides, Confluent Components and Client ones, Control plane rules them all
- Producers talks rest and the proxy will redirect to Kafka
	- Confluent will no need of proxy???
	- Consumers need to know Kafka!
	- Producers have point to point with other services via proxy but redirects to Kafka too.

- The talk just mention it for logging but we can have to make this info available for other tools too (????)
	- Use Proxies to communicate with Kafka,
	- Kafka itself doesn't have proxy?
 
### Istio Conn

#videos #talks https://events.istio.io/istiocon-2021/sessions/the-benefits-of-integrating-apache-kafka-with-istio-on-kubernetes/

#videos #talks https://www.youtube.com/watch?v=Exbs37vToZU

### Blog Posts

#### Kai Waiehner

#blog https://www.kai-waehner.de/blog/2019/09/24/cloud-native-apache-kafka-kubernetes-envoy-istio-linkerd-service-mesh/
#blog  https://banzaicloud.com/blog/kafka-on-istio-performance/

# Docs

[[Istio]]

[[Envoy]]


# Installation

## [[GKE]]

## [[Confluent for Kubernetes]]
 #repos #CFK #service_mesh  Istio Operator Resources : https://github.com/confluentinc/confluent-operator/tree/master/playbooks/features/istio

#repos #CFK #service_mesh  Playground: https://github.com/ogomezso/cfk-istio-playground.git

### Basic Install

1. Place on `GKE-Demo` folder

2. Create a _namespace_  called _confluent_
```bash
 kubectl apply -f confluent-namespace.yaml
```

3. Set namespace fot istio sidecar autocreation:
```bash 
kubectl label namespace confluent istio-injection=enabled
```

4. Enable ==Default Istio mutual TLS== for all confluent namespace.
```bash
kubectl apply -f istio/peer-tls.yaml
```

	 
This enable all comms between ==envoy proxies== to be secured by _mTls_

5. Apply `Envoy HTTP Filter`
```bash
kubectl apply -f istio/envoy-filter.yaml
```


This avoid the http2 conversion (by default in Istio) and create the correct http routes between component proxies.
6.  Install `CP-Platform` using  the specific component CR yaml on recommended order:
```bash
kubectl apply -f components/zookeeper.yaml
kubectl apply -f components/broker.yaml
kubectl apply -f components/schemaregistry.yaml
kubectl apply -f components/connect.yaml
kubectl apply -f components/ksqldb.yaml
kubectl apply -f components/rest-proxy.yaml
kubectl apply -f components/controlcenter.yaml
```

7. Then apply istio ==destination rules==:
```
kubectl apply -f istio/destination-rules.yaml
```

Rules for retry or timeouts, etc

8. Create Gateways:
```bash
kubectl apply -f istio/cp-gateway.yaml
```

9. Create external connections for http endpoints
On local testing you need to add:
```text 
istio.schemaregistry
istio.ksqldb
istio.c3
istio.connect
istio.replicator
istio.kafka-rest
istio.restproxy
```

 to your `/etc/hosts` file pointing to `istio-ingress-gateway` host
 
```bash
export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

### Iterative Install

We will use iterative approach to add functionality to our cluster

