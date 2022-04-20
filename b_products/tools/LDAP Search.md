#k8s 
#CFK 
#LDAP

### LDAP SEARCH CR
``` YAML
apiVersion: v1
  kind: Pod
  metadata:
    name: ldapsearch
    labels:
	app: ldapsearch
  spec:
    imagePullSecrets:
    - name: regcred
    containers:
    - name: ldapsearch
      image: docker.artifactory.gcp.mercadona.com/emeraldsquad/ldapsearch
      command: ["/bin/sleep", "3650d"]
      imagePullPolicy: IfNotPresent
      resources:
        requests:
          cpu: "1"
          memory: "1Gi"
        limits:
          cpu: "2"
          memory: "2Gi"
    restartPolicy: Always
```

#### Ldap Search Commands Examples:
```bash
LDAPTLS_REQCERT=never ldapsearch -x -b DC=ofidona,DC=net -H ldaps://ofidonassl.cc.mercadona.com -D "CN=srv_confluent_broker,OU=CONFLUENT PLATFORM,OU=ROLES 2019,DC=ofidona,DC=net" -w "zsVeTAa5&9z--Gz33GWRU-pu74"
			  
ldapsearch -x -b DC=ofidona,DC=net -H <ldap dns> -D <connection user> -w <password>	<search"CN=srv_confluent_broker">  		  
```
