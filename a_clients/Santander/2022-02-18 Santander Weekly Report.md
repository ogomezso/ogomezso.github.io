![[zzzz_Attachments/confluent_logo.png]]
#  2022-03-18 Santander RSA Weekly Report


---
client:
[[Santander]]
status: #report 
tags: 
[[cp_upgrade]] [[integration_test]] [[monitoring]]

---

```toc
 style: bullet 
 min_depth: 2 
 max_depth: 6
```

## Weekly Recap

ESTO ES TEXTO NORMAL

### Jorge Esteban Quilcate - 22022-03-14/15

```ad-summary

- Improving the tooling for troubleshooting issues on clients.

- Apply changes on Dev and Pre.

- Discussed briefly memory issues on ksqlDB.


```

```ad-success

- **Latency troubleshooting**

	- Review main metrics/dashboards for troubleshooting
	- Cluster input/output throughput
	- Request lifecycle
	- Topic read/write throughput

- **Enabling Quota metrics** :  [[#References#Client Quotas]]
	- Update JMX exporter template
	- Downgrade to Grafana 7
	- Add _topk(10, ...)_ functions on Grafana to reduce cardinality and pressure on the UI.
	- Run cp-ansible on Dev and Pre
	- Create quotas to enable its metrics
	- Deployed new dashboard for quotas to identify Clients throughput.
	- Run changes on Dev and Pre.
	
- **ksqlDB cluster memory issues**
	- Fixed ksqlDB dashboard metrics configuration.
	- Discussed potential issues with memory configuration.
```

---

### Dennis Federico - 2022-03-16

```ad-summary
title: Upgrade of PRE Environment to 7.0.1

- **Zookeeper** upgraded without any issue
- **Schema Regristry** upgraded without any issue
- **Kafka Connect** upgraded without any issue
- **Control Center** upgraded without any issue
- **Confluent Server**:
	- CP-Ansible's *Tune Virtual Memory settings* task tweak to match the correct order and format.
- **Replicator:**
	- PRE environment internal topics comes with *_bis suffix* that origin a load error on **replicator's secrets registry**
- **ksqlDB:**
	- Issue: The upgrade process starts without proper rolling stoppage by error, so there was one table that was unable to start on the first upgraded node due a error on other depending ones still on the not upgraded ones.
	-  Solution: 
		1. Shut off all nodes but one not upgraded one to unlock the update
		2. Drop of the issued table
			1. Get the DDL with a *describe extended* command
			2. Drop the table
			3. recreate it
		3. Shut off the not upgraded one
		4. Run CP-Ansible node by node starting with the one previously interrupted
```

---

### Oscar Gómez

### 2022-03-15 Day 1 of 3

#### 2022-03-15 10:01 JulieOps Implementation and First Tests


#### Attendees:
* Oscar Gomez
* Manuel Lara
* Alfredo Alcala
* Juan Pablo de Frutos
* Jose María Salazar Quirce
* Alejandro Sierra

#### Notes:

```ad-success

- Kerberos Authentication tested with Admin Team personal Keytab.
- Manuel Lara can provide us with the property file used.
- Config Repo for Julie Ops for Admin Team Created

```

```ad-error

Connectivity problem between Jenkins Agent and Brokers:

~~~Log
org.apache.kafka.common.KafkaException: Failed to create new KafkaAdminClient

Caused by: org.apache.kafka.common.KafkaException: javax.security.auth.login.LoginException: Receive timed out

Caused by: java.net.SocketTimeoutException: Receive timed out
~~~

```


#### Topic: Functional Test Automation


```ad-summary
title: Concepts & Tools

- **Unit Test:** Test running developed and run inside the app binary by app team with the goal to test design and functionality at class implementation level.
	- **Tools & Environment:** Junit, Topology Test Driver, Application Local
- **Functional Component Test:** Test developed by app team on a separate repository and run on every App deployment. Test the App functionality isolated, all interactions with real environments or application are mocked up
	- **Tools & Environment:** 
		- "Local Environment": Test Container, Topology Test Driver
		- Dedicated Resources on real environment: Cucumber, Cylantrum 
- **Functional Integration Test:** Test developed on a separate repository, ca be run on every app deployment or as part of a much bigger test run. Test App functionality against real applications deployed on a real environment, **nothing is mocked up**.
- **Regression Test:** Test developed by QA Team alongside App Team on a separate repo. The main goal is perform periodic healthchecks over a real enviroment.
	- **Tools & Environment:** 
		- Cucumber, Cylantrum, Adhoc pipelines
		- Real environment over real app deployments
		- Real data provided by app teams.
- **Performance Test**: Developed on separate repo by any of QA or App Team (normally QA team). Test the limits of workload of a given environment.
	- **Tools & Environment:**
		- Jmeter, Gatling, Adhoc Tooling (Kafka-perf-producer/consumer)
		- Real environment, dedicated resources 
```
```ad-quote
title: Goals

- Unlock the expections on current pipelines for the Apps Teams
- Provide guidance and tools for proper Functional Testing for App Teams
- Unify tools and practice over the funtional testing
- End with a black box type regression (and system) test running on a TBD cadency 

```
```ad-success
title: Agreements

- Out test applications will be **Tirecx** and **ADN360**
- Applications will start running their test as is right now to avoid the exception on release proccess.
- Application will continue using their own framework (for the moment). Cucumber for Tirecx and Cylantrum for ADN360.
- Confluent PS will help with the implementation of a base library for Cucumber and Cylantrum based on Java Kafka Clients.
- We will be using Julie Ops as way to create test environments

```

---

##### 2022-03-15 12:01 Tirecx Test Automation
###### Attendees:
* Jose Alberto Rollon
* Idafe Reyes
* Daniel Acera
* Abel Alvarez
###### Notes:
 - Cucumber Implementation based on *Datasource Abstract Classes*
 - As first approach we will try to provide this kind of Abstract Classes on our Test Base library

---

##### 2022-03-15 16:03 ADN360 Archetype Review 

###### Attendees:
* Jose Alberto Rollon
* Jose Manuel Pellejero
* Daniel Acera Bolanos

###### Notes:

- Cylantrum based test
- Isolated app test approach, using the binaries of dependant apps and topology test driver to simulate Kafka
- Adhoc implementation of Kafka Clients on separate classes
- **We will try to adapt our future base test library on a way ADN360 can use it on their current tests**

#### Other

```ad-note
title: Topic - Schema Relation Document

Topic - Schema relation per environment csvs provided based on the result of kafka-topics command list and schema registry subjects query.

```

### 2022-03-16 Day 2 of 3

#### 2022-03-16 09:36 Daily

##### Attendees:
* Ivan Yañex
* Manuel Lara
* Abel Alvarez
* Juan Pablo de Frutos
 
##### Notes:
- No new about Jenkins -> Lab Cluster connectity 
- PRO / PRE HDFS Sink connector updated to fix log4j vulnerability 

#### Other

```ad-info

Scaffolding and dependencies development for Base Test Library

```

### 2022-03-17 Day 3 of 3

#### 2022-03-17 09:35 Daily
##### Attendees:
* Abel Fernandez
* Miguel Angel Garcia

#### Notes:

- Blocked consumers on KSQL PRE 
- No KSQL on C3 UIs
- Proposal of use of Julie Ops
- consumerId/userId metrcis and app -> Spark Streaming creates a random clientId for every worker.
- Checklist for Julie Ops
- Dynatrace connector install -> how we can use the jar provided though cp-ansible


#### ksqlDB Errors

```ad-summary

After PRE environemnt upgrade we are finding errors with some ksqlDB queries. The main problem behind is that there is some ksql queries that create topics with just 1 partition and we are running with under replicated topic errors.

```

As example we have the describe of one of them:

```ad-example

SET 'cache.max.bytes.buffering'='0'; SET 'processing.guarantee'='exactly_once'; Statement : CREATE TABLE SANES_GDOPEN_PLREIDENTROL_JOIN_KVROLPRIV_TABLE_TEST WITH (KAFKA_TOPIC='SANES.GDOPEN.KSQL.PLREIDENTROL_JOIN_KVROLPRIV_TABLE.TEST', PARTITIONS=3, REPLICAS=1) AS SELECT SANES_GDOPEN_PLREIDENTROL_JOIN_KVROLPRIV_STREAM_TEST.IDOPERACION KEYOPERACION, AS_VALUE(SANES_GDOPEN_PLREIDENTROL_JOIN_KVROLPRIV_STREAM_TEST.IDOPERACION) IDOPERACION, LATEST_BY_OFFSET(SANES_GDOPEN_PLREIDENTROL_JOIN_KVROLPRIV_STREAM_TEST.IDEPRIVA) IDEPRIVA, LATEST_BY_OFFSET(SANES_GDOPEN_PLREIDENTROL_JOIN_KVROLPRIV_STREAM_TEST.OPERATION_IDENTROL) OPERATION_IDENTROL, LATEST_BY_OFFSET(SANES_GDOPEN_PLREIDENTROL_JOIN_KVROLPRIV_STREAM_TEST.OPERATION_ROLPRIV) OPERATION_ROLPRIV FROM SANES_GDOPEN_PLREIDENTROL_JOIN_KVROLPRIV_STREAM_TEST SANES_GDOPEN_PLREIDENTROL_JOIN_KVROLPRIV_STREAM_TEST GROUP BY SANES_GDOPEN_PLREIDENTROL_JOIN_KVROLPRIV_STREAM_TEST.IDOPERACION EMIT CHANGES;

```

```ad-tip
title: Possible Solutions

1. Recreate the querie (recommended)
2. Increment replica factor using reassign partitions scripts

```

Infra admin team finally choose to go with replica factor increment:

~~~bash

kafka-reassign-partitions --bootstrap-server localhost:9092 --reassignment-json-file increase.json --execute
~~~

Automate it through bash script:

~~~bash

for topico in lista;docp−pfchangereplicatortopicTEMPLATE.jsonchangereplicatortopicRUNNING.json;sed−i′s/NOMBRETOPIC/′topico'/g' change_replicator_topic_RUNNING.json;echo "TOPIC=$topico"; /opt/confluent/confluent-7.0.1/bin/kafka-reassign-partitions --bootstrap-server $HOSTNAME:9093 --command-config client.properties --reassignment-json-file change_replicator_topic_RUNNING.json --execute; done
~~~

#### SEPA Transfers Streams not Working

```ad-summary

As health check for the actions on ksqlDB we try to monitor SEPA Transfer state change on all the chain.

We indentify some consumer groups that are not getting any messages so we just follow the chain to the origin **ABINITIO**

This 3rd party is running with delay (currently proccesing data from 14-03), so origin topics doesn't get any data.

We decide to postpone to next Monday March 21st to track the errors. 

```

#### Under replicated Internal Topics

```ad-summary

We found some internal topics with just one replica on PROD environemt, something that feels quite strange for me. So decide to investigate the configuration aroung this.

```

```ad-hint
title: KStreams Apps

[[#References#KStreams Config]]

We found that default values for **replication factor** and **min in sync replicas** is **1**. But as a general recommendation for resilency the **official recommendation is to put it to 3**

```
```ad-hint
title: ksqlDB

At same level we found that the **replica factor by default for sink topics created for KSQL is 1**. So the behaivour is completely normal.

```

## Open Questions

```ad-question
title: Metric Stack Sizing

- Regarding Prometheus/Grafana latency we recommend having more small instances rather than a big one -> [[#References#One Big Cluster or Many Small Ones]]

```

```ad-question
title: Dynatrace Connector Install

How we can use the provided jar on CP-Ansible

```


## Next Steps

```ad-note
title: ksqlDB Memory Issues

- Tune the RocksDB configuration -> [[#References#RocksDB Configuration]]

```
```ad-note
title: Metrics Monitoring
[[#References#Metrics]]
- Upgrade to Grafana 8
- Add _topk(10, ...)_ functions on Grafana to reduce cardinality and pressure on the UI on the rest of metrics
- Tune JMX Exporter by enabling cache (avoid high cost scrapes ~30sec)
- Discuss with project teams to align metrics both client and cluster side. Client Metrics can already answer latency and throughput to specifuc topics/partitions/retries, etc.
```
```ad-note
title: Julie Ops

- Create dedicated User for Infra Admin Team -> @Ivan Yañez
- Check and Open Firewall rules between Jenkins Agent and Broker, Schema Registry and Kerberos 
- Test basic functionality with Infra Admin team

```
```ad-note
title: Test Automation

- Get access to App test repos
- Base test library first draft
- 

```

```ad-note
title: ksqlErrors

- Track errors with SEPA Transfers Application 

```

## References 

#### Client Quotas
[Quotas](https://docs.confluent.io/platform/current/kafka/design.html#quotas)
[Enforcing Client Quotas](https://docs.confluent.io/platform/current/kafka/post-deployment.html#enforcing-client-quotas)
[Quota Dashboard]([https://github.com/confluentinc/jmx-monitoring-stacks/blob/6.1.0-post/jmxexporter-prometheus-grafana/assets/grafana/provisioning/dashboards/kafka-quotas.json](https://github.com/confluentinc/jmx-monitoring-stacks/blob/6.1.0-post/jmxexporter-prometheus-grafana/assets/grafana/provisioning/dashboards/kafka-quotas.json))

#### Metrics
[JMX Exporter Broker Configuration]([https://github.com/confluentinc/jmx-monitoring-stacks/blob/68085e42f1ab656ba70a4a8d4fed9cecf09a266a/shared-assets/jmx-exporter/kafka_broker.yml#L174-L192](https://github.com/confluentinc/jmx-monitoring-stacks/blob/68085e42f1ab656ba70a4a8d4fed9cecf09a266a/shared-assets/jmx-exporter/kafka_broker.yml#L174-L192))
[JMX Exporter Configuration]([https://github.com/prometheus/jmx_exporter#configuration](https://github.com/prometheus/jmx_exporter#configuration))
[Consumer Metrics](https://docs.confluent.io/platform/current/kafka/monitoring.html#consumer-metrics)
[Streams Monitoring](https://docs.confluent.io/platform/current/streams/monitoring.html)

#### RocksDB Configuration
[Kafka Streams Store Performance](https://www.confluent.io/blog/how-to-tune-rocksdb-kafka-streams-state-stores-performance/)
[ksqlDB Memory Usage Recommendations](https://www.confluent.io/blog/bounding-ksqldb-memory-usage/#recommendations)
[Streams Sizing Troubleshooting](https://docs.confluent.io/platform/current/streams/sizing.html#troubleshooting)

#### One Big Cluster or Many Small Ones
[Essay](https://cnr.sh/essays/one-big-cluster-many-small-ones)

#### Julie Ops
[JulieOps Kerberos Authentication](https://github.com/kafka-ops/julie/blob/master/example/topology-builder-kerberos.properties)

#### KStreams Config

[Kstreams config](https://docs.confluent.io/platform/current/streams/developer-guide/config-streams.html#recommended-configuration-parameters-for-resiliency)
