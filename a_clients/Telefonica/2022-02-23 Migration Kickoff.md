![[zzzz_Attachments/confluent_logo.png]]

# Migración de Bus - Kickoff

## Client: Telefónica 
## Date: 2022-02-23
---
Oscar Gómez Soriano - Solutions Architect  

```toc
 style: bullet 
 min_depth: 3 
 max_depth: 6
```

### Objetivo
- Poner en contexto la situación actual, así como las necesidades y requisitos para el proceso de migración
- Trazar un *draft* de plan de trabajo para dicho proceso.

### Contexto

#### Infraestructura
- La migración se realizará simultáneamente en los cluster de PROD y PREPOD (Muleto)
- No se contempla la actualización de versiones de componente durante el proceso de migración.
- **Situación Actual Cluster de PROD:**
	- Confluent Platform v6.2.1 desplegado en OCP v3 (sin Operator)
	- 5 Brokers en máquinas dedicadas (+1 ya decomisionado de OCP)
	- 3 Máquinas Multi-próposito que contienen Zookeeper y el resto de componentes de la plataforma
	- Cluster en **Confluent Cloud** que sirve de *man in the middle* para simplificar la disponibilidad de los datos para los distintos consumidores.
- **Situación Actual Cluster "Muleto"**
	- Confluent Platform v6.2.1 desplegado en OCP v3 (sin Operator)
	- 3 Brokers en máquinas dedicadas
	- 3 Máquinas Multi-próposito que contienen Zookeeper y el resto de componentes de la plataforma.
	 
```ad-info

En la actualidad tanto PRO como Muleto despliegan en OCPv3 en un modo totalmente estático, sin hacer uso de las capacidades de la plataforma de escalabilidad o auditoria, esto quiere decir:

- En el caso de los **broker**, estos despliegan en modo dedicado (1 broker <-> 1 worker de OCP), utilizan la persistencia local e interfaces de red del nodo, exponiendo su ip al exterior
- En caso de **Zookeeper**, al igual que los broker despliegan en nodo decicado utlizando los recursos del propio nodo
- El resto de componentes, comparten recursos en las **maquinas multiprosito**, pudiendo levantar en cualquier de ellas, aunque sigue utilizando recursos locales.

```

- **Stack de Monitorización:** Elastic/Kibana
- **Alertado:** Adhoc Tool: ***Maruja*** + C3
- Herramienta Gitops adhoc para automatización de despliegues (no incluye instalación de nuevos componentes, ni creación de topics/schemas) 

#### Capacidad de Infraestructura Existente

![[Pasted image 20220223155239.png]]

```ad-hint
title: Consideraciones sobre capacidad de Máquinas

Teniendo en consideración las recomendaciones para el [Cluster Sizing](https://docs.confluent.io/operator/current/co-plan.html#co-env-sizing) aún siendo generosos estamos muy por encima de los valores recomendados.

Si la previsión es tener un broker por máquina podríamos pensar en adecuar la capacidad de computo a la necesidad real de los broker, lo que nos dejaría capacidad de computo para nodos extra (conectores, los dos zk extra).

Importante reseñar que seguimos manteniendo la recomendación de no tener mas de un componente por máquina

```


#### Volumetría
- Evidencia de picos en PROD de 65k eventos/sg
 
#### Arquitectura

##### Seguridad
- SASL-SCRAM-252 para Listeners Externos sin securización de protocolo
- Protocolo no securizado para comunicación interna
- Autorización con **RBAC** 
- ***Secret Protection***

##### Clientes
- Varios Apache Kafka (pertenecientes a otras areas de la compañía) que usan replicator para mover datos a nuestro cluster
- Golden Gate -> Los administradores de Oracle tienen su propia instancia de Connect con [Golden Gate Source Connector](https://www.confluent.io/hub/oracle/goldengate) corriendo
- ***NGBI*** -> Equipos de desarrollo que trabajan en la transformación y disponibilización del dato. **Su salida son otros topics dentro del mismo cluster**. Principalmente se trata de aplicaciones **Spark**
- Sink Connectors a:
	- Hortonworks Cluster ([HDFS Sink Connector](https://www.confluent.io/hub/confluentinc/kafka-connect-hdfs3))
	- Elastic - Kibana ([Elastic Search Sink Connector](https://www.confluent.io/hub/confluentinc/kafka-connect-elasticsearch))
	- Teradata ([Teradata Sink Connector](https://www.confluent.io/hub/confluentinc/kafka-connect-teradata))
	
#### Conclusiones

```ad-warning

Nuestra recomendación es tener **máquinas dedicadas para cada servicio**, especialmente en el caso de **Zookeeper**. No es un **must** pero si algo muy recomendable.

```
```ad-warning

A priori el número de conectores en cada worker de connect parece excesivo, planteamos el hacer un estudio de como optimizar los recursos disponibles para levantar mas workers (por caso de uso, dominio, ...). Para ello veremos más adelante que la recomendación es aprovechar el cluster de OCPv4 a este propsósito

```

```ad-hint

Del mismo modo la recomendación para mayor resilencia de un cluster de este tipo sería tener **5 Zookeeper**

```

```ad-hint

No tenemos ninguna securización a nivel de protocolo (SSL). No es una prioridad ahora misma debido al aislamiento de los entornos, pero lo apuntamos como **nice to have**

```
```ad-note

Se acuerda que la anonimización de secretos en los inventarios de Ansible queda fuera de scope, no es algo importante y puede tratarse en el futuro si se considera necesario.

```

### Plan de Trabajo

#### Prerrequisitos y asunciones

##### Para los componentes instalados *on prem*

- La instalación se realizara haciendo uso de [cp-ansible](https://docs.confluent.io/ansible/current/overview.html) en su última versión. 
- La versión de *Confluent Platform* 6.2.1
- Asumimos que tanto las ips como cualquier nombre de maquina/dns no se mantendrán intactos con respecto al cluster OCP existente$^1$
- Podéis encontrar la lista de prerrequisitos para cp-ansible [aquí](https://docs.confluent.io/ansible/current/ansible-requirements.html) pero cabe destacar:
	- La maquina donde instalemos ansible deberá tener acceso al resto 
	- En caso de no tener acceso a internet necesitaremos tener los paquetes disponibles en la máquina host de ansible. Podeís descargar los paquetes [aquí](https://docs.confluent.io/platform/6.2.1/installation/available_packages.html)

```ad-warning
title: 1

El cambio en ips, dns o puertos de algún componente supondrá cambios de configuración en clientes y dependiendo del caso en otros componentes de la plataforma, haciendo el objetivo de 0 Downtime casi imposible.

```

##### Para los componentes a instalar en OCPv4

- Disponer de una máquina (idealmente la misma donde instalamos ansible) que disponga de:
	- kubectl, krew, kubens, kubectx (para la interacción con el cluster Openshift). Yo estoy habituado a estas herramientas y me parece que son las que mejor funcionan pero adicionalmente se podría instalar el cliente oc.
	- Helm, lo necesitaremos para instalar CFK.
	- [Confluent kubectl plugin](https://docs.confluent.io/operator/current/co-plugin-cli-index.html)  
- Namespace creado o posibilidad de crearlo, con quota de recursos suficiente para los componentes a instalar
- Permisos para crear nuevos recursos en dicho namespace, incluido el claim de nuevos PVC/PV
- La instalación se hará a traves de [Confluent for Kubernetes](https://docs.confluent.io/operator/current/overview.html) en su última versión (incluidos los crds)
- Los prerrequisitos a nivel cluster los podeís encontrar [aquí](https://docs.confluent.io/operator/current/co-prepare.html) 

#### Desglose de Propuesta de Plan de Trabajo


![[21-02-23- [Telefonica] Migracion Bus Kickoff-Estado Inicial.png]]

###### 0. Consideraciones Iniciales

```ad-important

La propuesta de plan se realiza sobre la asumiendo:

- **Maquinas dedicadas para Zookeeper**

- Paralelización del trabajo entre Muleto y PROD

- El decomisionado y reformateo de las máquinas se hace de 1 en 1, con una cadencia semanal (se plantea la posibilidad de hacerlo de 2 en 2 bajando el tiempo a la mitad)

- La instalación de los componentes de *Ecosistema* (KSQL, Connect, Control Center, Schema Registry) se realizara en el nuevo cluster de OCPv4 mediante CFK, por lo que en el momento de mover el resto de componentes tendremos los recursos disponibles en el nuevo cluster OCPv4.

```

```ad-info
title: Plan General Migración Zookeeper

Para evitar/reducir al máximo el *downtime* de servicio durante el proceso, el plan de migración se realizara añadiendo los nuevos *nodos on prem* al cluster existente en modo *stretch cluster*. Para lo cual seguiremos la siguiente aproximación

1. Aprovisionamiento de una nueva máquina.   

2. Instalación y actualización de la máquina$^1$.   
3. Instalación de Zookeeper en la máquina en cuestión.  

4. Comprobación de nuevo Quorum y réplicado de datos.   

5. Borrado de statefulset de zookeeper en el cluster OCP correspondiente.   

```
```ad-info
title: Plan General Migración Brokers

Para evitar/reducir al máximo el *downtime* de servicio durante el proceso, el plan de migración se realizara añadiendo los nuevos *nodos on prem* al cluster existente en modo *stretch cluster*. Para lo cual seguiremos la siguiente aproximación

1. Retirada de la máquina del cluster de Openshift (bajo demanda, dependencia con los administradores de OCP).   

2. Instalación y actualización de la máquina$^1$.   
3. Instalación del Broker en la máquina en cuestión.   

4. Healthcheck del estado del cluster y *self balancing*.   

```
```ad-info
title: Plan General Migración Resto de Componetes

Schema Registry, Kakfa Connect, KSQL y Control Center, se desplegaran en el nuevo cluster de OCPv4, con **CFK (Operator)** aprovechando así las ventajas de un despliegue de este tipo (elasticidad en escalado, aprovechamiento de recursos).

  

Además esto abre la puerta a una extensión del cluster añadiendo nodos en OCPv4

```
```ad-warning

La configuración estandar de los clientes es de **3 particiones y 3 replicas con minISR de 2**.

  

Por lo que debemos asegurarnos de tener siempre un minimo de **4 brokers en activo**

```
```ad-warning
title: 1
  
El tiempo estimado para este proceso es de **un dia**

```


##### 1. Puesta en Marcha y check de prerrequisitos (2 dias)

- Check de prerrequisitos y conectividad de *bastion/es* 
- Usaremos el *nodo 8* ya decomisionado para probar la instalación a través de `cp-ansible`. Idealmente tendríamos un nodo más para probar el añadido de un nodo mas a la *region on prem* de nuestro stretch cluster.
- Enablement del equipo Telefónica para dotarles de independencia en la ejecución de estas tareas con independencia de PS Confluent.
- Health Check del cluster tras el añadido de los broker on prem.

![[21-02-23- [Telefonica] Migracion Bus Kickoff-fase 1.png]]

##### 2. Health check 1 (1 Día)

![[21-02-23- [Telefonica] Migracion Bus Kickoff- fase 2.png]]

- Telefónica y PS revisan la ejecución de la **Fase 2** (realizado por equipo telefónica)
- Planificación de Fase 3

##### 3. Health Check 2 (1 Día)

![[21-02-23- [Telefonica] Migracion Bus Kickoff-fase 3.png]]

- Telefónica y PS revisan la ejecución de la **Fase 3**
- Planificación Fase 4

##### 4. Health Check 3 (1 Día)

![[21-02-23- [Telefonica] Migracion Bus Kickoff-fase 4 2.png]]

- Telefónica y PS revisan la ejecución de la **Fase 4**
- Planificación Fase 5

##### 5. Health Check 4 (1 Día)

![[21-02-23- [Telefonica] Migracion Bus Kickoff-fase 5.png]]
- Telefónica y PS revisan la ejecución de la **fase 5**
- Planificación Fase 6

##### 6. Health Check 5 (1 Día)

![[21-02-23- [Telefonica] Migracion Bus Kickoff-fase 6.png]]

-   Telefónica y PS revisan la ejecución de la **Fase 7**
-   Planificación Fase 7

##### 7. Revision Prerrequisitos e Instalación Muleto OCPv4 / Healthcheck 6 (1-2 Días)

![[21-02-23- [Telefonica] Migracion Bus Kickoff-fase 7.png]]

- Revisión prerrequisitos y conectividad de bastion muleto con OCPv4
- Instalación de CFK en namespace Muleto OCPv4
- Despliegue de Componentes en Muleto OCPv4
- Healthcheck general Muleto
- Healtcheck estado de Fase 7 en PROD
- Planificación Fase 8

##### 8. Healcheck 7 (1 Día)

![[21-02-23- [Telefonica] Migracion Bus Kickoff-fase 8.png]]
- Monitorización de estado de Muleto on prem y cierre de entorno
- Health Check Fase 8$^1$
- Planificación Fase 9



##### 9. Revision de Prerrequisitos e Instalación PROD OCPv4 (1 Día)

![[21-02-23- [Telefonica] Migracion Bus Kickoff- Fase 9.png]]

-   Revisión prerrequisitos y conectividad de bastion PROD con OCPv4
-   Instalación de CFK en namespace PROD OCPv4
-   Despliegue de Componentes en PROD OCPv4
-   Healthcheck general PROD

##### 10. Healthcheck Worshop (2 Días)

![[21-02-23- [Telefonica] Migracion Bus Kickoff- final.png]]
- Confluent Platform Healthcheck Workshop

##### 11. KSQL & Kafka Connect Capacity Planning para los casos de uso existentes (2 Días)

### Preguntas pendientes

#### A responder por Confluent Team

- Implicaciones a nivel Suscripción/Licencias de levantar nuevas instancias de componentes, sea como pod o en máquinas físicas (Customer Success/Sales)

#### A Responder por Telefónica Team

- Dimensionamiento de Máquinas para el nuevo cluster on Prem 
- Aclaración de disponibilidad de máquinas para nodos independientes de Zookeeper
- Volumetría de eventos media, en pico y valle expresada en Bytes/sg
- {a responder durante el engagement} Volumetría y clasificación (por caso de uso) de los conectores actualemente en uso

### Siguientes pasos

- Validar plan de trabajo (PS - Telefónica)
- Validar plan de engagement con Management Profesional Services$^1$
- Poner fecha (ASAP) para la ejecución de la primer hito

```ad-warning

Aquí dependemos de Axel que desafortunadamente esta de vacaciones hasta el día 4. Haremos todo lo posible para tener un workaround y poder empezar a trabajar.

```
