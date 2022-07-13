
# Istio
## 2022-05-16

### Status: #MOC
### Tags:
[[k8s]] [[service_mesh]] [[cfk]] [[Istio]]

#### Linked Pages
[[Confluent for Kubernetes]]
[[{TO_REFACTOR} Service Mesh]]

## Docs

#docs [Official Page](https://istio.io/latest/docs/)

#docs [Gateways](https://istio.io/latest/docs/tasks/traffic-management/ingress/ingress-control/#determining-the-ingress-ip-and-ports)

#docs [TCP Metrics](https://istio.io/latest/docs/tasks/observability/metrics/tcp-metrics/)

## Installation

### Install [[Istioctl]]
[install](https://istio.io/latest/docs/ops/diagnostic-tools/istioctl/#install-hahahugoshortcode-s2-hbhb)

#### create a cluster and connect to [[Kubectl]]

### Install Istio
Will try `demo` profile. `istioctl install --set profile=demo` 
This will install Istio on new namespace `istio-system`: 
```bash
kubectl -n istio-system get deploy
```

> NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
  istio-egressgateway    1/1     1            1           2m52s
  istio-ingressgateway   1/1     1            1           2m52s
  istiod                 1/1     1            1           3m9s
				  
Generate Manifest and verify installation:
```bash
istioctl manifest generate --set profile=demo > generated_manifest.yaml
istioctl verify-install generated_manifest.yaml
```

### Install Telemetry Services

#### [[Prometheus]]
[install](https://istio.io/latest/docs/ops/integrations/prometheus/)

#### [[Grafana]]
[install](https://istio.io/latest/docs/ops/integrations/grafana/#option-1-quick-start)

#### [[Jaeger]]
 [install](https://istio.io/latest/docs/ops/integrations/jaeger/#installation)
 ```Tracing.yaml
apiVersion: install.istio.io/v1alpha1
  kind: IstioOperator
  spec:
    meshConfig:
      enableTracing: true
      defaultConfig:
        tracing:
          sampling: 50
```

#### [[Kiali]]
[install](https://kiali.io/docs/installation/quick-start/)

### Expose Telemetry services to external:

##### Grafana Gateway
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: grafana-gateway
  namespace: istio-system
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http-grafana
      protocol: HTTP
      hosts:
      - "istio.grafana"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: grafana-vs
  namespace: istio-system
spec:
  hosts:
   - "istio.grafana"
  gateways:
   - grafana-gateway
   http:
   - route:
     - destination:
	     host: grafana
	     port:
	       number: 3000
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: grafana
  namespace: istio-system
spec:
  host: grafana
  trafficPolicy:
    tls:
      mode: DISABLE
```
##### Prometheus Gateway:
```yaml 
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: prometheus-gateway
  namespace: istio-system
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http-prom
      protocol: HTTP
  hosts:
    - "istio.prometheus"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: prometheus-vs
  namespace: istio-system
spec:
  hosts:
  - "istio.prometheus"
  gateways:
  - prometheus-gateway
  http:
    - route:
       - destination:
            host: prometheus
            port:
              number: 9090
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
   name: prometheus
   namespace: istio-system
spec:
   host: prometheus
   trafficPolicy:
     tls:
       mode: DISABLE
```
##### Kiali Gateway:
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: kiali-gateway
  namespace: istio-system
  spec:
    selector:
      istio: ingressgateway
    servers:
    - port:
        number: 80
        name: http-kiali
        protocol: HTTP
    hosts:
      - "istio.kiali"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: kiali-vs
  namespace: istio-system
spec:
 hosts:
 - "istio.kiali"
 gateways:
 - kiali-gateway
 http:
   - route:
      - destination:
          host: kiali
          port:
          number: 20001
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
   name: kiali
   namespace: istio-system
spec:
   host: kiali
   trafficPolicy:
     tls:
       mode: DISABLE
```
##### Jaeger Gateway:
```
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: tracing-gateway
  namespace: istio-system
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
     number: 80
     name: http-tracing
     protocol: HTTP
  hosts:
  - "istio.jaeger"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: tracing-vs
  namespace: istio-system
spec:
 hosts:
 - "istio.jaeger"
 gateways:
 - tracing-gateway
   http:
   - route:
	 - destination:
	     host: tracing
	     port:
	       number: 80
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: tracing
  namespace: istio-system
spec:
  host: tracing
  trafficPolicy:
    tls:
      mode: DISABLE
```

 ##### /etc/hosts`
```urls
35.242.190.10 istio.bookinfo-gateway
35.242.190.10 istio.grafana
35.242.190.10 istio.prometheus
35.242.190.10 istio.kiali
35.242.190.10 istio.jaeger
```

# [[DEMO]]

## Istio Http Micro Services Demo

We will use the [Book Info Demo](https://istio.io/latest/docs/examples/bookinfo/) released with on Istio Samples
#### Create a separate namespace for the app:
```book_info_namespace.yaml
kind: Namespace
apiVersion: v1
 metadata:
   name: book-info
   labels:
     name: book-info
```

#### Label the namespace to automatically inject istio sidecars: 
```bash
kubectl label namespace book-info istio-injection=enabled
````

#### Create services using the same CRs of the original but adding the namespace:
```book_info_app.yaml

			  # Copyright Istio Authors
			  #
			  #   Licensed under the Apache License, Version 2.0 (the "License");
			  #   you may not use this file except in compliance with the License.
			  #   You may obtain a copy of the License at
			  #
			  #       http://www.apache.org/licenses/LICENSE-2.0
			  #
			  #   Unless required by applicable law or agreed to in writing, software
			  #   distributed under the License is distributed on an "AS IS" BASIS,
			  #   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
			  #   See the License for the specific language governing permissions and
			  #   limitations under the License.
			  
			  ##################################################################################################
			  # This file defines the services, service accounts, and deployments for the Bookinfo sample.
			  #
			  # To apply all 4 Bookinfo services, their corresponding service accounts, and deployments:
			  #
			  #   kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
			  #
			  # Alternatively, you can deploy any resource separately:
			  #
			  #   kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml -l service=reviews # reviews Service
			  #   kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml -l account=reviews # reviews ServiceAccount
			  #   kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml -l app=reviews,version=v3 # reviews-v3 Deployment
			  ##################################################################################################
			  
			  ##################################################################################################
			  # Details service
			  ##################################################################################################
			  apiVersion: v1
			  kind: Service
			  metadata:
			    name: details
			    namespace: book-info
			    labels:
			      app: details
			      service: details
			  spec:
			    ports:
			    - port: 9080
			      name: http
			    selector:
			      app: details
			  ---
			  apiVersion: v1
			  kind: ServiceAccount
			  metadata:
			    name: bookinfo-details
			    namespace: book-info
			    labels:
			      account: details
			  ---
			  apiVersion: apps/v1
			  kind: Deployment
			  metadata:
			    name: details-v1
			    namespace: book-info
			    labels:
			      app: details
			      version: v1
			  spec:
			    replicas: 1
			    selector:
			      matchLabels:
			        app: details
			        version: v1
			    template:
			      metadata:
			        labels:
			          app: details
			          version: v1
			      spec:
			        serviceAccountName: bookinfo-details
			        containers:
			        - name: details
			          image: docker.io/istio/examples-bookinfo-details-v1:1.16.2
			          imagePullPolicy: IfNotPresent
			          ports:
			          - containerPort: 9080
			          securityContext:
			            runAsUser: 1000
			  ---
			  ##################################################################################################
			  # Ratings service
			  ##################################################################################################
			  apiVersion: v1
			  kind: Service
			  metadata:
			    name: ratings
			    namespace: book-info
			    labels:
			      app: ratings
			      service: ratings
			  spec:
			    ports:
			    - port: 9080
			      name: http
			    selector:
			      app: ratings
			  ---
			  apiVersion: v1
			  kind: ServiceAccount
			  metadata:
			    name: bookinfo-ratings
			    namespace: book-info
			    labels:
			      account: ratings
			  ---
			  apiVersion: apps/v1
			  kind: Deployment
			  metadata:
			    name: ratings-v1
			    namespace: book-info
			    labels:
			      app: ratings
			      version: v1
			  spec:
			    replicas: 1
			    selector:
			      matchLabels:
			        app: ratings
			        version: v1
			    template:
			      metadata:
			        labels:
			          app: ratings
			          version: v1
			      spec:
			        serviceAccountName: bookinfo-ratings
			        containers:
			        - name: ratings
			          image: docker.io/istio/examples-bookinfo-ratings-v1:1.16.2
			          imagePullPolicy: IfNotPresent
			          ports:
			          - containerPort: 9080
			          securityContext:
			            runAsUser: 1000
			  ---
			  ##################################################################################################
			  # Reviews service
			  ##################################################################################################
			  apiVersion: v1
			  kind: Service
			  metadata:
			    name: reviews
			    namespace: book-info
			    labels:
			      app: reviews
			      service: reviews
			  spec:
			    ports:
			    - port: 9080
			      name: http
			    selector:
			      app: reviews
			  ---
			  apiVersion: v1
			  kind: ServiceAccount
			  metadata:
			    name: bookinfo-reviews
			    namespace: book-info
			    labels:
			      account: reviews
			  ---
			  apiVersion: apps/v1
			  kind: Deployment
			  metadata:
			    name: reviews-v1
			    namespace: book-info
			    labels:
			      app: reviews
			      version: v1
			  spec:
			    replicas: 1
			    selector:
			      matchLabels:
			        app: reviews
			        version: v1
			    template:
			      metadata:
			        labels:
			          app: reviews
			          version: v1
			      spec:
			        serviceAccountName: bookinfo-reviews
			        containers:
			        - name: reviews
			          image: docker.io/istio/examples-bookinfo-reviews-v1:1.16.2
			          imagePullPolicy: IfNotPresent
			          env:
			          - name: LOG_DIR
			            value: "/tmp/logs"
			          ports:
			          - containerPort: 9080
			          volumeMounts:
			          - name: tmp
			            mountPath: /tmp
			          - name: wlp-output
			            mountPath: /opt/ibm/wlp/output
			          securityContext:
			            runAsUser: 1000
			        volumes:
			        - name: wlp-output
			          emptyDir: {}
			        - name: tmp
			          emptyDir: {}
			  ---
			  apiVersion: apps/v1
			  kind: Deployment
			  metadata:
			    name: reviews-v2
			    namespace: book-info
			    labels:
			      app: reviews
			      version: v2
			  spec:
			    replicas: 1
			    selector:
			      matchLabels:
			        app: reviews
			        version: v2
			    template:
			      metadata:
			        labels:
			          app: reviews
			          version: v2
			      spec:
			        serviceAccountName: bookinfo-reviews
			        containers:
			        - name: reviews
			          image: docker.io/istio/examples-bookinfo-reviews-v2:1.16.2
			          imagePullPolicy: IfNotPresent
			          env:
			          - name: LOG_DIR
			            value: "/tmp/logs"
			          ports:
			          - containerPort: 9080
			          volumeMounts:
			          - name: tmp
			            mountPath: /tmp
			          - name: wlp-output
			            mountPath: /opt/ibm/wlp/output
			          securityContext:
			            runAsUser: 1000
			        volumes:
			        - name: wlp-output
			          emptyDir: {}
			        - name: tmp
			          emptyDir: {}
			  ---
			  apiVersion: apps/v1
			  kind: Deployment
			  metadata:
			    name: reviews-v3
			    namespace: book-info
			    labels:
			      app: reviews
			      version: v3
			  spec:
			    replicas: 1
			    selector:
			      matchLabels:
			        app: reviews
			        version: v3
			    template:
			      metadata:
			        labels:
			          app: reviews
			          version: v3
			      spec:
			        serviceAccountName: bookinfo-reviews
			        containers:
			        - name: reviews
			          image: docker.io/istio/examples-bookinfo-reviews-v3:1.16.2
			          imagePullPolicy: IfNotPresent
			          env:
			          - name: LOG_DIR
			            value: "/tmp/logs"
			          ports:
			          - containerPort: 9080
			          volumeMounts:
			          - name: tmp
			            mountPath: /tmp
			          - name: wlp-output
			            mountPath: /opt/ibm/wlp/output
			          securityContext:
			            runAsUser: 1000
			        volumes:
			        - name: wlp-output
			          emptyDir: {}
			        - name: tmp
			          emptyDir: {}
			  ---
			  ##################################################################################################
			  # Productpage services
			  ##################################################################################################
			  apiVersion: v1
			  kind: Service
			  metadata:
			    name: productpage
			    namespace: book-info
			    labels:
			      app: productpage
			      service: productpage
			  spec:
			    ports:
			    - port: 9080
			      name: http
			    selector:
			      app: productpage
			  ---
			  apiVersion: v1
			  kind: ServiceAccount
			  metadata:
			    name: bookinfo-productpage
			    labels:
			      account: productpage
			  ---
			  apiVersion: apps/v1
			  kind: Deployment
			  metadata:
			    name: productpage-v1 
			    namespace: book-info
			    labels:
			      app: productpage
			      version: v1
			  spec:
			    replicas: 1
			    selector:
			      matchLabels:
			        app: productpage
			        version: v1
			    template:
			      metadata:
			        labels:
			          app: productpage
			          version: v1
			      spec:
			        serviceAccountName: bookinfo-productpage
			        containers:
			        - name: productpage
			          image: docker.io/istio/examples-bookinfo-productpage-v1:1.16.2
			          imagePullPolicy: IfNotPresent
			          ports:
			          - containerPort: 9080
			          volumeMounts:
			          - name: tmp
			            mountPath: /tmp
			          securityContext:
			            runAsUser: 1000
			        volumes:
			        - name: tmp
			          emptyDir: {}
			  ---
```

Describing any pod we can see the `istio sidecar` deployed
```bash
kubectl describe pod productpage-v1-6b746f74dc-7trfv -n book-info
```

```text
Name:         productpage-v1-6b746f74dc-7trfv
Namespace:    book-info
Priority:     0
Node:         gke-ogomez-cfk-default-pool-96fb3da7-xjr3/10.0.0.24
Start Time:   Mon, 27 Dec 2021 17:06:18 +0100
Labels:       app=productpage
              pod-template-hash=6b746f74dc
              security.istio.io/tlsMode=istio
              service.istio.io/canonical-name=productpage
              service.istio.io/canonical-revision=v1
              version=v1
Annotations:  kubectl.kubernetes.io/default-container: productpage
              kubectl.kubernetes.io/default-logs-container: productpage
              prometheus.io/path: /stats/prometheus
              prometheus.io/port: 15020
              prometheus.io/scrape: true
              sidecar.istio.io/status:
                {"initContainers":["istio-init"],"containers":["istio-proxy"],"volumes":["istio-envoy","istio-data","istio-podinfo","istio-token","istiod-...
Status:       Running
IP:           10.176.8.5
IPs:
IP:           10.176.8.5
Controlled By:  ReplicaSet/productpage-v1-6b746f74dc
Init Containers:
  istio-init:
    Container ID:  containerd://0a7def356a46e63309108838860e52945d96d2b6cc12509ce7265b1c782f1b79
    Image:         docker.io/istio/proxyv2:1.12.1
Image ID:      docker.io/istio/proxyv2@sha256:4704f04f399ae24d99e65170d1846dc83d7973f186656a03ba70d47bd1aba88fPort:          <none>
Host Port:     <none>
Args:
  istio-iptables
    -p 15001
	-z
	  15006
			        -u
			        1337
			        -m
			        REDIRECT
			        -i
			        *
			        -x
			  
			        -b
			        *
			        -d
			        15090,15021,15020
			      State:          Terminated
			        Reason:       Completed
			        Exit Code:    0
			        Started:      Mon, 27 Dec 2021 17:06:25 +0100
			        Finished:     Mon, 27 Dec 2021 17:06:25 +0100
			      Ready:          True
			      Restart Count:  0
			      Limits:
			        cpu:     2
			        memory:  1Gi
			      Requests:
			        cpu:        10m
			        memory:     40Mi
			      Environment:  <none>
			      Mounts:
			        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-58b6s (ro)
			  Containers:
			    productpage:
			      Container ID:   containerd://99349c039ef2af8f9e43b9562b7acf8b80646db70f263277cd288571625d38ac
			      Image:          docker.io/istio/examples-bookinfo-productpage-v1:1.16.2
			      Image ID:       docker.io/istio/examples-bookinfo-productpage-v1@sha256:63ac3b4fb6c3ba395f5d044b0e10bae513afb34b9b7d862b3a7c3de7e0686667
			      Port:           9080/TCP
			      Host Port:      0/TCP
			      State:          Running
			        Started:      Mon, 27 Dec 2021 17:06:33 +0100
			      Ready:          True
			      Restart Count:  0
			      Environment:    <none>
			      Mounts:
			        /tmp from tmp (rw)
			        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-58b6s (ro)
			    istio-proxy:
			      Container ID:  containerd://ce9a08121ae14033f47e09d15e0035fedf6012a6aa540a17ae1b56f081a61f87
			      Image:         docker.io/istio/proxyv2:1.12.1
			      Image ID:      docker.io/istio/proxyv2@sha256:4704f04f399ae24d99e65170d1846dc83d7973f186656a03ba70d47bd1aba88f
			      Port:          15090/TCP
			      Host Port:     0/TCP
			      Args:
			        proxy
			        sidecar
			        --domain
			        $(POD_NAMESPACE).svc.cluster.local
			        --proxyLogLevel=warning
			        --proxyComponentLogLevel=misc:error
			        --log_output_level=default:info
			        --concurrency
			        2
			      State:          Running
			        Started:      Mon, 27 Dec 2021 17:06:33 +0100
			      Ready:          True
			      Restart Count:  0
			      Limits:
			        cpu:     2
			        memory:  1Gi
			      Requests:
			        cpu:      10m
			        memory:   40Mi
			      Readiness:  http-get http://:15021/healthz/ready delay=1s timeout=3s period=2s #success=1 #failure=30
			      Environment:
			        JWT_POLICY:                    third-party-jwt
			        PILOT_CERT_PROVIDER:           istiod
			        CA_ADDR:                       istiod.istio-system.svc:15012
			        POD_NAME:                      productpage-v1-6b746f74dc-7trfv (v1:metadata.name)
			        POD_NAMESPACE:                 book-info (v1:metadata.namespace)
			        INSTANCE_IP:                    (v1:status.podIP)
			        SERVICE_ACCOUNT:                (v1:spec.serviceAccountName)
			        HOST_IP:                        (v1:status.hostIP)
			        PROXY_CONFIG:                  {}
			  
			        ISTIO_META_POD_PORTS:          [
			                                           {"containerPort":9080,"protocol":"TCP"}
			                                       ]
			        ISTIO_META_APP_CONTAINERS:     productpage
			        ISTIO_META_CLUSTER_ID:         Kubernetes
			        ISTIO_META_INTERCEPTION_MODE:  REDIRECT
			        ISTIO_META_WORKLOAD_NAME:      productpage-v1
			        ISTIO_META_OWNER:              kubernetes://apis/apps/v1/namespaces/book-info/deployments/productpage-v1
			        ISTIO_META_MESH_ID:            cluster.local
			        TRUST_DOMAIN:                  cluster.local
			      Mounts:
			        /etc/istio/pod from istio-podinfo (rw)
			        /etc/istio/proxy from istio-envoy (rw)
			        /var/lib/istio/data from istio-data (rw)
			        /var/run/secrets/istio from istiod-ca-cert (rw)
			        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-58b6s (ro)
			        /var/run/secrets/tokens from istio-token (rw)
			  Conditions:
			    Type              Status
			    Initialized       True
			    Ready             True
			    ContainersReady   True
			    PodScheduled      True
			  Volumes:
			    istio-envoy:
			      Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
			      Medium:     Memory
			      SizeLimit:  <unset>
			    istio-data:
			      Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
			      Medium:
			      SizeLimit:  <unset>
			    istio-podinfo:
			      Type:  DownwardAPI (a volume populated by information about the pod)
			      Items:
			        metadata.labels -> labels
			        metadata.annotations -> annotations
			    istio-token:
			      Type:                    Projected (a volume that contains injected data from multiple sources)
			      TokenExpirationSeconds:  43200
			    istiod-ca-cert:
			      Type:      ConfigMap (a volume populated by a ConfigMap)
			      Name:      istio-ca-root-cert
			      Optional:  false
			    tmp:
			      Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
			      Medium:
			      SizeLimit:  <unset>
			    kube-api-access-58b6s:
			      Type:                    Projected (a volume that contains injected data from multiple sources)
			      TokenExpirationSeconds:  3607
			      ConfigMapName:           kube-root-ca.crt
			      ConfigMapOptional:       <nil>
			      DownwardAPI:             true
			  QoS Class:                   Burstable
			  Node-Selectors:              <none>
			  Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
			                               node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
			  Events:
			    Type    Reason     Age   From               Message
			    ----    ------     ----  ----               -------
			    Normal  Scheduled  92s   default-scheduler  Successfully assigned book-info/productpage-v1-6b746f74dc-7trfv to gke-ogomez-cfk-default-pool-96fb3da7-xjr3
			    Normal  Pulling    91s   kubelet            Pulling image "docker.io/istio/proxyv2:1.12.1"
			    Normal  Pulled     87s   kubelet            Successfully pulled image "docker.io/istio/proxyv2:1.12.1" in 4.059823467s
			    Normal  Created    85s   kubelet            Created container istio-init
			    Normal  Started    85s   kubelet            Started container istio-init
			    Normal  Pulling    85s   kubelet            Pulling image "docker.io/istio/examples-bookinfo-productpage-v1:1.16.2"
			    Normal  Pulled     79s   kubelet            Successfully pulled image "docker.io/istio/examples-bookinfo-productpage-v1:1.16.2" in 6.010299768s
			    Normal  Created    77s   kubelet            Created container productpage
			    Normal  Started    77s   kubelet            Started container productpage
			    Normal  Pulled     77s   kubelet            Container image "docker.io/istio/proxyv2:1.12.1" already present on machine
			    Normal  Created    77s   kubelet            Created container istio-proxy
			    Normal  Started    77s   kubelet            Started container istio-proxy
```
#### Check
```bash
			  kubectl exec -n book-info "$(kubectl get pod -n book-info -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
```

#### Set up book-info gateway
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway
  namespace: book-info
  spec:
    selector:
      istio: ingressgateway
    servers:
    - port:
        number: 80
        name: http
        protocol: HTTP
      hosts:
      - "istio.bookinfo"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
   name: bookinfo
   namespace: book-info
     spec:
	   hosts:
	   - "istio.bookinfo"
	   gateways:
	   - bookinfo-gateway
	    http:
	    - match:
	     - uri:
	         exact: /productpage
	      - uri:
	         prefix: /static
	       - uri:
	         exact: /login
	       - uri:
	         exact: /logout
	      - uri:
	          prefix: /api/v1/products
	      route:
	      - destination:
	          host: productpage
	          port:
	            number: 9080
```

#### Check Ips/port
```bash
export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')

export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')

export TCP_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')

export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
```

#### add `/etc/hosts`: `$INGRESS_HOST bookinfo-gateway`

