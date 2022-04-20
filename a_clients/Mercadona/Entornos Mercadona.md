## Bastiones
```text
DEV → dv1ml0001.dev.cm.mercadona.com
ITG → iv1ml0001.itg.cm.mercadona.com
PRE DR → bastion.pre.cm1.mercadona.com
PRE → bastion.pre.cm2.mercadona.com
PRO → bastion.cm1.mercadona.com
PRO DR → bastion.cm2.mercadona.com
```
## Login OC
```bash
oc login https://api.ocp01.lab.cm.mercadona.com:6443 --username=e_ogomez@integracion.net
oc login https://api.ocp01.dev.cm.mercadona.com:6443 --username=e_ogomez@integracion.net
oc login https://api.ocp01.itg.cm.mercadona.com:6443 --username=e_ogomez@integracion.net
oc login https://api.ocp01.pre.cm1.mercadona.com:6443 --username=e_ogomez@premercadona.com
oc login https://api.ocp01.pre.cm2.mercadona.com:6443 --username=e_ogomez@premercadona.com
oc login https://api.ocp01.cm1.mercadona.com:6443 --username=e_ogomez@mercadona.com
oc login https://api.ocp01.cm2.mercadona.com:6443 --username=e_ogomez@mercadona.com
```

## Consolas
```text
https://console-openshift-console.apps.ocp01.itg.cm.mercadona.com/dashboards 
https://console-openshift-console.apps.ocp01.dev.cm.mercadona.com/k8s/cluster/projects 
https://console-openshift-console.apps.ocp01.lab.cm.mercadona.com/k8s/cluster/projects
https://console-openshift-console.apps.ocp01.pre.cm1.mercadona.com/k8s/cluster/projects
https://console-openshift-console.apps.ocp01.pre.cm2.mercadona.com/k8s/cluster/projects
https://console-openshift-console.apps.ocp01.cm1.mercadona.com/k8s/cluster/projects
https://console-openshift-console.apps.ocp01.cm2.mercadona.com/k8s/cluster/projects
```

## Gitlab: 
#repos https://gitlab.gcp.mercadona.com/

## Operator
##### Repo:
```bash
helm repo add virtual-helm https://artifactory.gcp.mercadona.com/artifactory/virtual-helm/ --username e_ogomez --password AKCp8kq2g2EDL5nWAVMsLNm4RrAc6M3STNdCC8hS7MrtmnYpAZuqQgsKL5xa6ozPdoYntaf7
```

##### Install:
```bash
helm upgrade --install confluent-operator virtual-helm/confluent-for-kubernetes --version=0.280.1 -f values.yaml --set podSecurity.enabled=false
```

## PRE

##### Usuarios de servicio
```text
srv_ldap_confluent
zx3h5K--P9ap394BZKdhDsYG&&
	  
srv_confluent_rest
3zb34sUo-XdKMZ$uPr29k8$$SN
	   
srv_confluent_broker
FnCPY$27sVDr77$$smN4zuu&M6
	  
srv_confluent_admin
9oUW&-8&T$6pYxHfAW7y3ehM7s
  
srv_confluent_c3
Do&sZ7&o86$T7Rc8n5ygADRS-y
	  
srv_confluent_sr
sw29B$a4r9vYk$5TT-2DMr-RpT
	   
srv_confluent_con
v-fc7t-C7YT$F93Nu4eZn8zE&X
	  
srv_confluent_ksqldb
6ZYn8dmyS$9U$r9Au7-rmF-3WM
	  
srv_confluent_rep
UD-N8g5-rP5swM-48sBa-6mBhB

password de los cert: 21aVx-8092
zip: M3rC4-C3rts
	  	  
```

##### SASL Credentials
creds-kafka-sasl-users.json
```json
{	
  "kafka_client": "",
  "srv_confluent_c3": "Do&sZ7&o86$T7Rc8n5ygADRS-y",
  "srv_confluent_broker": "FnCPY$27sVDr77$$smN4zuu&M6"
  "srv_confluent_sr": "sw29B$a4r9vYk$5TT-2DMr-RpT"
  "srv_confluent_con": "v-fc7t-C7YT$F93Nu4eZn8zE&X"
}
```

creds-zookeeper-sasl-digest-users.json
``` json
{
   "srv_confluent_broker": "FnCPY$27sVDr77$$smN4zuu&M6"
}
```

creds-broker-credentials.txt 
```text
username=srv_confluent_broker
			  password=FnCPY$27sVDr77$$smN4zuu&M6
			  ````
			- ```creds-control-center-users.txt
			  srv_confluent_c3:  PDo&sZ7&o86$T7Rc8n5ygADRS-y, CONFLUENT PLATFORM
```

ldap.txt
```text
username=CN=srv_ldap_confluent,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=integracion,DC=net
password=zx3h5K--P9ap394BZKdhDsYG&&
```

##### Apply
```bash
kubectl create secret generic credential \
   --from-file=plain-users.json=creds-kafka-sasl-users.json \
   --from-file=digest-users.json=creds-zookeeper-sasl-digest-users.json \
   --from-file=digest.txt=creds-kafka-zookeeper-credentials.txt \
   --from-file=plain.txt=creds-client-kafka-sasl-user.txt \
   --from-file=basic.txt=creds-control-center-users.txt \
   --from-file=ldap.txt=ldap.txt \
   --namespace confluent
```

TLS Secret
```bash
kubectl create secret generic kafka-tls \
  --from-file=fullchain.pem=creds/ssl/kafka.pem \
  --from-file=cacerts.pem=creds/ssl/CA.pem \
  --from-file=privkey.pem=creds/ssl/kafka.key \
  --namespace confluent
```

##### Auth config on crds:

Zookeeper (Digest)
```yaml
authentication:
   type: digest
   jaasConfig:
      secretRef: credential
		  
```

broker (Plain)
```yaml
authentication:
  type: plain
  jaasConfig:
    secretRef: credential
```

##### LDAP PRE
```text 
DNS: ofidonassl.cc.mercadona.com
maquina: TV1IW0001.preproduccion.net
user:username= CN=srv_ldap_confluent,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=preproduccion,DC=net
pwd:zx3h5K--P9ap394BZKdhDsYG&&
```

### LDAP Search Commands

```bash
LDAPTLS_REQCERT=never ldapsearch -x -b DC=preproduccion,DC=net  -H ldaps://TV1IW0001.preproduccion.net -D "CN=srv_ldap_confluent,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=preproduccion,DC=net" -w "zx3h5K--P9ap394BZKdhDsYG&&"
		  
ldapsearch -x -b DC=preproduccion,DC=net  -H ldaps://ofidonassl.cc.mercadona.com:3269 -D "CN=srv_ldap_confluent,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=preproduccion,DC=net" -w "zx3h5K--P9ap394BZKdhDsYG&&"
```

```bash
		  LDAPTLS_REQCERT=never ldapsearch -x -b DC=preproduccion,DC=net -H ldaps://TV1IW0001.preproduccion.net -D "CN=srv_confluent_broker,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=preproduccion,DC=net" -w "FnCPY$27sVDr77$$smN4zuu&M6"
		  FnCPY$27sVDr77$$smN4zuu&M6
```


## Run book PRE

### Credenciales:

##### Zookeeper:
Auth Digest:
```json
{
 "srv_confluent_broker": "FnCPY$27sVDr77$$smN4zuu&M6"
}
```
				  
```bash
 kubectl create secret generic zk-digest-json \
    --from-file=digest-users.json=zk-digest-json.json \
    --namespace confluent
```

##### Broker:
TLS:
```bash
				  kubectl create secret generic kafka-tls \
				    --from-file=fullchain.pem=certs/kafka.pre.cm.mercadona.com.pem \
				    --from-file=cacerts.pem=certs/Mercadona_CA_Emisora_SERVIDORES_ITG.pem \
				    --from-file=privkey.pem=certs/kafka.pre.cm.mercadona.com.key \
				    --namespace confluent
```

Internal Listener Credential:
``` internal-plain-jaas.conf
				  		  sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="srv_confluent_broker" password="FnCPY$27sVDr77$$smN4zuu&M6";
```

```bash
kubectl create secret generic kafka-internal-cred \
  --from-file=plain-jaas.conf=internal-plain-jaas.conf \
  --namespace confluent
```

External Listener Credential:
```external-plain-jaas.conf
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required;
```

```bash
kubectl create secret generic kafka-external-cred \
   --from-file=plain-jaas.conf=+ \
   --namespace confluent
```

Zookeeper Digest
```text		
username=srv_confluent_broker
password=FnCPY$27sVDr77$$smN4zuu&M6
```

```bash
kubectl create secret generic zk-digest \
    --from-file=digest.txt=zk-digest.txt \
    --namespace confluent
```

##### LDAP:
```txt
username=CN=srv_ldap_confluent,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=preproduccion,DC=net
password=zx3h5K--P9ap394BZKdhDsYG&&
```

```bash
kubectl create secret generic ldap-cred \
  --from-file=ldap.txt=ldap.txt \
  --namespace confluent
```

###### MDS:
Token Pair:
 ```bash
openssl genrsa -out mds-priv-key.pem 2048

rsa -in mds-priv-key.pem -out PEM -pubout -out mds-pub-key.pem
```

```bash
kubectl create secret generic mds-token \
  	--from-file=mdsPublicKey.pem=creds/mds-pub-key.pem \
 	--from-file=mdsTokenKeyPair.pem=creds/mds-priv-key.pem \
  	--namespace confluent
```

Client Bearer:
```txt
username=srv_confluent_broker
password=FnCPY$27sVDr77$$smN4zuu&M6
```

```bash
kubectl create secret generic mds-kafka-cred \
   --from-file=bearer.txt=mds-kafka-bearer.txt \
   --namespace confluent
```

Rest Class
```bash
kubectl create secret generic rest-class-cred \
   --from-file=bearer.txt=mds-kafka-bearer.txt \
    --from-file=basic.txt=mds-kafka-bearer.txt \
    --namespace confluent
```

Schema Registry:
```mds-sr-bearer.txt 
username=srv_confluent_sr
password=sw29B$a4r9vYk$5TT-2DMr-RpT
```

``` 
kubectl create secret generic mds-sr-cred \
   --from-file=bearer.txt=mds-sr-bearer.txt \
   --namespace confluent
```

##### Control Center:
```mds-c3-bearer.tx
username=srv_confluent_c3
password=Do&sZ7&o86$T7Rc8n5ygADRS-y
```

```bash
kubectl create secret generic mds-c3-cred \
   --from-file=bearer.txt=mds-c3-bearer.txt \
   --namespace confluent
```

##### MetricReporter Credential:
```text
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="srv_confluent_broker" password="FnCPY$27sVDr77$$smN4zuu&M6";
```

```bash
kubectl create secret generic metrics-cred \
   --from-file=plain-jaas.conf=metrics-plain-jaas.conf \
   --namespace confluent
```


##### Schema Registry:
Kafka Internal :
```sr-kafka-plain.txt
username=srv_confluent_broker
password=FnCPY$27sVDr77$$smN4zuu&M6
```

```bash 
kubectl create secret generic sr-kafka-cred \
  --from-file=plain.txt=sr-kafka-plain.txt \
  --namespace confluent
```

##### Control Center:
Kafka Internal:
``` c3-kafka-plain.txt
username=srv_confluent_broker
password=FnCPY$27sVDr77$$smN4zuu&M6
```

```bash
kubectl create secret generic c3-kafka-cred \
   --from-file=plain.txt=c3-kafka-plain.txt \
   --namespace confluent
```

##### Connect:
TLS:
```bash
kubectl create secret generic connect-tls \
   --from-file=fullchain.pem=PEM/connect.pre.cm.mercadona.com.pem \
   --from-file=cacerts.pem=PEM/Mercadona_CA_Emisora_SERVIDORES_ITG.pem \
   --from-file=privkey.pem=PEM/connect.pre.cm.mercadona.com.key \
   --namespace confluent
```

Kafka:
```connect-kafka-plain.txt
username=srv_confluent_broker
password=FnCPY$27sVDr77$$smN4zuu&M6
```

```bash 
kubectl create secret generic connect-kafka-cred \
   --from-file=plain.txt=connect-kafka-plain.txt \
   --namespace confluent
```

MDS:
```mds-connect-bearer.txt
username=srv_confluent_con
password=v-fc7t-C7YT$F93Nu4eZn8zE&X
```

```bash
kubectl create secret generic mds-connect-cred \
   --from-file=bearer.txt=mds-connect-bearer.txt \
   --namespace confluent
```

## PRO
#### Users
```text
srv_confluent_admin 
MDtFV6rM29-&7d&44-xkaCepGB
	  
srv_confluent_broker 
zsVeTAa5&9z--Gz33GWRU-pu74
		  
srv_confluent_c3 
$VgwcN42H9ohR&adVfM864--FU
		  
srv_confluent_con 
Z6-u-mE2TaEA2x-8atK3hS2g-N
		  
srv_confluent_ksqldb 
-cs4-FRGn$7T4K55ueEBbxwR4$
		  
srv_confluent_rep 
9TE-m456B$r&7-FHtKuZkza7Ef
		  
srv_confluent_rest 
cEG&PTt6xYD8w6$eBv&$4E5t4k
		  
srv_confluent_sr 
hr4$nDG2F48&uuvr9gX3M$GN$E
		  
srv_ldap_confluent 
DnpXDFnn6o3-$5B9M3S&h-4daC
```

#### Ldap:
```text
  PRO: PV1IW0001.ofidona.net 172.16.12.16
```

##### Ldap Search Commands:
```bash
LDAPTLS_REQCERT=never ldapsearch -x -b DC=ofidona,DC=net -H ldaps://ofidonassl.cc.mercadona.com -D "CN=srv_confluent_broker,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=ofidona,DC=net" -w "zsVeTAa5&9z--Gz33GWRU-pu74"
			  
ldapsearch -x -b DC=ofidona,DC=net -H ldap://PV1IW0001.ofidona.net -D "CN=srv_confluent_broker,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=ofidona,DC=net" -w "zsVeTAa5&9z--Gz33GWRU-pu74"
			  
			  
ldapsearch -x -b DC=ofidona,DC=net -H ldap://PV1IW0001.ofidona.net -D "CN=srv_ldap_confluent,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=ofidona,DC=net" -w "DnpXDFnn6o3-\$5B9M3S&h-4daC" "CN=srv_confluent_broker" 
```