![[zzzz_Attachments/confluent_logo.png]]


# GDO ksqlDB Incident Post Mortem
## Client: 
[[Santander]]
## Engagement Type: 
##  2022-03-28
---
Oscar Gómez Soriano - Solutions Architect  

```toc
 style: bullet 
 min_depth: 3 
 max_depth: 6
```

### Incident Timeline

![[Drawing 2022-03-24 16.26.34.excalidraw.png]]

#### ksqlDB Upgrade

```ad-info
title: Platform Upgrade

**CP Platform:** from v6.1.1 to v7.0.1
**ksqlDB Server:** from v0.14.0 to v0.21.0

```

Platform Upgrade via `via cp-ansible`.

```ad-danger

Rolling update of servers without stopping fisrt to get time for queries stop gracefully and do the rebalance.

```

After the upgrade we can see some topics getting errors of minISR not reached:

```ad-error
title: log

TBP

```

```ad-question
title: theory

Default value for minISR at broker level before upgrade: 2
Default value for minISR at broker level after upgrade: 2

With this in mind the only possibility is that the **topic was created manually with a minISR of 1**

We can see on the problematic queries that kind of statements:

CREATE TABLE SANES_GDOPEN_STATES_TABLE WITH (KAFKA_TOPIC='SANES.GDOPEN.STATES.STREAMING', PARTITIONS=3, REPLICAS=1, VALUE_AVRO_SCHEMA_FULL_NAME='es.santander.gdopen.StatesStreaming', VALUE_FORMAT='AVRO') 

so we can confirm that many topics was created with one replica, not using the default values.

So when we start again after the upgrade and ksql recreate the queries with the new version picks the default value a start failing.

```

![[image (2).png]]

After that we had 2 options:

```ad-info
title: Possible Actions

1. Recreate queries from scratch (recommended)
2. Increase replica number for topics.

```

Option 2 picked for matter of time and be less intrusive on app development:

```ad-summary

To increase the replicas we need to run a `kafka-reassign-partitions` scripts where a manual placement of the partitions replica is seted.

```

```ad-note
title: query

kafka-reassign-partitions --bootstrap-server localhost:9092 --reassignment-json-file increase.json --execute

```

~~~json
{
  "version": 1,
  "partitions": [
    {
      "topic": "my-topic",
      "partition": 0,
      "replicas": [
        1,
        3,
        2
      ]
    },
    {
      "topic": "my-topic",
      "partition": 1,
      "replicas": [
        2,
        3,
        1
      ]
    }
  ]
}
~~~


On top of that a script for doing it automatically for the list of topics with replica factor 1 was run:

```ad-note

for topico in lista;
docp−pfchangereplicatortopicTEMPLATE.json changereplicatortopicRUNNING.json;
sed−i′s/NOMBRETOPIC/′topico'/g' change_replicator_topic_RUNNING.json;
echo "TOPIC=$topico"; 
/opt/confluent/confluent-7.0.1/bin/kafka-reassign-partitions --bootstrap-server $HOSTNAME:9093 --command-config client.properties --reassignment-json-file change_replicator_topic_RUNNING.json --execute; done

```
~~~json
{

"version":1,

"partitions":[

      {"topic":"NOMBRE_TOPIC","partition":0,"replicas":[0,1,2]},

      {"topic":"NOMBRE_TOPIC","partition":1,"replicas":[0,1,2]},

      {"topic":"NOMBRE_TOPIC","partition":2,"replicas":[0,1,2]},

      {"topic":"NOMBRE_TOPIC","partition":3,"replicas":[0,1,2]},

      {"topic":"NOMBRE_TOPIC","partition":4,"replicas":[0,1,2]},

      {"topic":"NOMBRE_TOPIC","partition":5,"replicas":[0,1,2]},

      {"topic":"NOMBRE_TOPIC","partition":6,"replicas":[0,1,2]},

      {"topic":"NOMBRE_TOPIC","partition":7,"replicas":[0,1,2]},

      {"topic":"NOMBRE_TOPIC","partition":8,"replicas":[0,1,2]},

      {"topic":"NOMBRE_TOPIC","partition":9,"replicas":[0,1,2]}

]

}
~~~

```ad-warning

Doing this this way we are:

1. Repartition will cause moving data from a partition to another and this can have impact on the ksql queries. Task of Kstreams/ksql must be copartitioned so ** we should never change the partition number of this topic or we will broke the dependant queries including potential lose of data.**
2. Increasing the load of the brokers on a significant way. They will need to perform a rebalancing for all topics we are manually repartitioned (in this case more than a hundred)
```

#### GDO State Table with Stuck Partitions

After the upgrade and repartition GDO project complains about several tables not working.

We focus on `SANES_GDOPEN_STATES_TABLE`. After some investigation we found that this tables have some lag on consumer groups:
If we run:

~~~bash
describe SANES_GDOPEN_STATES_TABLE extended;
~~~

We can see as part of the output:

~~~bash
Consumer Groups summary:



Consumer Group : _confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1789



Kafka topic : SANES.GDOPEN.KSQL.CP_ORDEN_CONTR_TABLE
Max lag : 26



Partition | Start Offset | End Offset | Offset | Lag
------------------------------------------------------
0 | 0 | 169101 | 169101 | 0
1 | 0 | 169120 | 169103 | 17
2 | 0 | 169622 | 169596 | 26
------------------------------------------------------



Kafka topic : SANES.GDOPEN.KSQL.PL_RE_ORDEN_STATES_TABLE
Max lag : 46910



Partition | Start Offset | End Offset | Offset | Lag
--------------------------------------------------------
0 | 0 | 46910 | 0 | 46910
1 | 0 | 47227 | 47173 | 54
2 | 0 | 46909 | 46862 | 47
--------------------------------------------------------
~~~

We can't appreciate any work trying to catchup.

so we decide to **recreate the table**

#### SANES_GDOPEN_STATES_TABLE Recreation

Recreation script:

```ad-info

SET 'auto.offset.reset'='latest';
SET 'cache.max.bytes.buffering'='0';

CREATE TABLE SANES_GDOPEN_STATES_TABLE WITH (KAFKA_TOPIC='SANES.GDOPEN.STATES.STREAMING', PARTITIONS=3, REPLICAS=3, VALUE_AVRO_SCHEMA_FULL_NAME='es.santander.gdopen.StatesStreaming', VALUE_FORMAT='AVRO') AS SELECT
  CONTR.KEYOPERACION KEYOPERACION,
  PENDIENTE.OPERATIONID OPERATIONID,
  PENDIENTE.RESOLUTIONMARK RESOLUTIONMARK,
  PENDIENTE.OFFICE OFFICE,
  PENDIENTE.USERNUMBER USERNUMBER,
  PENDIENTE.RESOLUTIONDATE RESOLUTIONDATE,
  CONTR.CUENTAIMPUTACION[-1] IMPUTATIONACCOUNT,
  PENDIENTE.REVOKEDMARK REVOKEDMARK
FROM SANES_GDOPEN_PL_RE_ORDEN_STATES_TABLE PENDIENTE
LEFT OUTER JOIN SANES_GDOPEN_CP_ORDEN_CONTR_TABLE CONTR ON ((PENDIENTE.ROWKEY = CONTR.KEYOPERACION))
EMIT CHANGES;

```

After recreate it we can't see any consumer group created for `SANES_GDOPEN_PL_RE_ORDEN_STATES_TABLE`:

~~~bash
Consumer Groups summary:



Consumer Group : _confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_TEST_1801



Kafka topic : SANES.GDOPEN.KSQL.CP_ORDEN_CONTR_TABLE
Max lag : 167051



Partition | Start Offset | End Offset | Offset | Lag
---------------------------------------------------------
0 | 0 | 169057 | 17515 | 151542
1 | 0 | 169077 | 2026 | 167051
2 | 0 | 169572 | 9733 | 159839
---------------------------------------------------------
~~~

On this point we tried to check the functionality by creating a side table with the same functionality

#### SANES_GDOPEN_STATES_TABLE_TEST Creation

Creation script:

```ad-note

SET 'auto.offset.reset'='earliest';
SET 'cache.max.bytes.buffering'='0';

CREATE TABLE SANES_GDOPEN_STATES_TABLE_TEST WITH (KAFKA_TOPIC='SANES.GDOPEN.STATES.STREAMING', PARTITIONS=3, REPLICAS=3, VALUE_AVRO_SCHEMA_FULL_NAME='es.santander.gdopen.StatesStreaming', VALUE_FORMAT='AVRO') AS SELECT  
CONTR.KEYOPERACION KEYOPERACION,  
PENDIENTE.OPERATIONID OPERATIONID,  
PENDIENTE.RESOLUTIONMARK RESOLUTIONMARK,  
PENDIENTE.OFFICE OFFICE,  
PENDIENTE.USERNUMBER USERNUMBER,  
PENDIENTE.RESOLUTIONDATE RESOLUTIONDATE,  
CONTR.CUENTAIMPUTACION[-1] IMPUTATIONACCOUNT,  
PENDIENTE.REVOKEDMARK REVOKEDMARK  
FROM SANES_GDOPEN_PL_RE_ORDEN_STATES_TABLE PENDIENTE  
LEFT OUTER JOIN SANES_GDOPEN_CP_ORDEN_CONTR_TABLE CONTR ON ((PENDIENTE.ROWKEY = CONTR.KEYOPERACION))  
EMIT CHANGES;

```

```ad-error

By mistake we create the query pointing to the **same output topic** of the original one.

So what we are doing is overcharge the topic writing and reading, and on top of data a chance of putting wrong data in the topic causing errors on the join matching

```

We proceed to delete it, due the amount of load generated on this cluster by rebalancing and recreating queries we got some race conditions errors on `ksql.log`:

```ad-error

ksql.log:[2022-03-21 16:45:31,390] INFO Executed statement: drop table SANES_GDOPEN_STATES_TABLE_TEST; (io.confluent.ksql.rest.server.computation.CommandRunner:346)
ksql.log:[2022-03-21 16:45:31,438] WARN Deleted local state store for non-existing query _confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_TEST_1807. This is not expected and was likely due to a race condition when the query was dropped before. (io.confluent.ksql.engine.QueryCleanupService:121)
ksql.log:[2022-03-21 16:45:32,217] WARN Failed to cleanup internal consumer groups for _confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_TEST_1807 (io.confluent.ksql.engine.QueryCleanupService:149)
ksql.log:io.confluent.ksql.exception.KafkaResponseGetFailedException: Failed to delete consumer groups: [_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_TEST_1807]

```

that produces this kind of errors:

```ad-error

[2022-03-22 12:02:25,169] WARN [Producer clientId=_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_TEST_1801-960a99ae-06ae-42f0-b2d0-279332bf8dbb-StreamThread-1-producer] Error while fetching metadata with correlation id 759599 : {_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_TEST_1807-Project-Last-changelog=UNKNOWN_TOPIC_OR_PARTITION} (org.apache.kafka.clients.NetworkClient:1213)

```
```ad-info

That means we are running on a *orphaned resources* situations that implies that queries around this resources (we got the same dependencies on the querie chain) can experiment lags, or even be stucked.

```

#### Test data start to be consumed

After repeating the `SANES_GDOPEN_STATES_TABLE` to avoid the possible wrong data on the out topic, we generate some test data and after a while (AB Initio lags) we can see the data coming to the our querie chain and eventually the results on the `SEPA App`

We found that **data only can be consumed from some partitions**

So we are on the situation of `Stucked Partitions`

This situation is mainly has origin on the `orphaned resources` state. So we have two options:

1. Restart KSQL cluster and look if is able to catch up.
2. Recreate all the query chain

#### Recreate Query

Taking in consideration the query dependencies showed by the `DESCRIBE <table> extended` command we recreate all the isolated queries on the chain:

1. Delete all the queries 
2. Create it again.

This are the Stream/table recreated and the order, we followed:

```ad-note
title: Base Config


SET 'auto.offset.reset'='latest';
SET 'cache.max.bytes.buffering'='0';

```

```ad-note
title: STREAM SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM

CREATE OR REPLACE STREAM SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM WITH (KAFKA_TOPIC='SANES.GDOPEN.KSQL.PL_RE_ORDEN_STATES_STREAM', PARTITIONS=3, REPLICAS=3) AS SELECT
  SANES_GDOPEN_PL_RE_ORDEN_STREAM.ROWKEY ROWKEY,
  CONCAT(SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->IDEMPR, SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->IDCENT, SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->CODPROD, SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->NUMSOR) OPERATIONID,
  (CASE WHEN (SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->IDCENTD <> SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->IDCENTD) THEN 'RE' ELSE SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->CESTADO END) RESOLUTIONMARK,
  SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->IDCENTD OFFICE,
  SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->USUMOD USERNUMBER,
  SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->TIMESMOD RESOLUTIONDATE,
  SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->TIMESMOD REVOKEDMARK
FROM SANES_GDOPEN_PL_RE_ORDEN_STREAM SANES_GDOPEN_PL_RE_ORDEN_STREAM
WHERE (((SANES_GDOPEN_PL_RE_ORDEN_STREAM.OPERATION = 'REPL') AND (SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->CODPROD = '633')) AND (((((((SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->CESTADO = 'APL') OR (SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->CESTADO = 'DEV')) AND ((SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->CESTADO = 'PTE') OR (SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->CESTADO = 'PAP'))) OR (SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->IDCENTD <> SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->IDCENTD)) OR (((SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->CESTADO = 'PAP') OR (SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->CESTADO = 'PTE')) AND (SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->CESTADO = 'APL'))) OR (((SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->CESTADO = 'PAP') OR (SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->CESTADO = 'PTE')) AND ((SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->CESTADO = 'PED') OR (SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->CESTADO = 'DEV')))) OR (((SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->CESTADO = 'PTE') OR (SANES_GDOPEN_PL_RE_ORDEN_STREAM.BEFORE->CESTADO = 'PAP')) AND (SANES_GDOPEN_PL_RE_ORDEN_STREAM.AFTER->CESTADO = 'ANU'))))
EMIT CHANGES;

```
```ad-note
title: SANES_GDOPEN_PL_RE_ORDEN_STATES_TABLE

CREATE TABLE SANES_GDOPEN_PL_RE_ORDEN_STATES_TABLE WITH (KAFKA_TOPIC='SANES.GDOPEN.KSQL.PL_RE_ORDEN_STATES_TABLE', PARTITIONS=3, REPLICAS=3) AS SELECT
  SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM.ROWKEY ROWKEY,
  LATEST_BY_OFFSET(SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM.OPERATIONID) OPERATIONID,
  LATEST_BY_OFFSET(SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM.RESOLUTIONMARK) RESOLUTIONMARK,
  LATEST_BY_OFFSET(SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM.OFFICE) OFFICE,
  LATEST_BY_OFFSET(SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM.USERNUMBER) USERNUMBER,
  LATEST_BY_OFFSET(SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM.RESOLUTIONDATE) RESOLUTIONDATE,
  LATEST_BY_OFFSET(SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM.REVOKEDMARK) REVOKEDMARK
FROM SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM
GROUP BY SANES_GDOPEN_PL_RE_ORDEN_STATES_STREAM.ROWKEY
EMIT CHANGES;

```

```ad-note
title: SANES_GDOPEN_STATES_TABLE
CREATE TABLE SANES_GDOPEN_STATES_TABLE WITH (KAFKA_TOPIC='SANES.GDOPEN.STATES.STREAMING', PARTITIONS=3, REPLICAS=3, VALUE_AVRO_SCHEMA_FULL_NAME='es.santander.gdopen.StatesStreaming', VALUE_FORMAT='AVRO') AS SELECT
  CONTR.KEYOPERACION KEYOPERACION,
  PENDIENTE.OPERATIONID OPERATIONID,
  PENDIENTE.RESOLUTIONMARK RESOLUTIONMARK,
  PENDIENTE.OFFICE OFFICE,
  PENDIENTE.USERNUMBER USERNUMBER,
  PENDIENTE.RESOLUTIONDATE RESOLUTIONDATE,
  CONTR.CUENTAIMPUTACION[-1] IMPUTATIONACCOUNT,
  PENDIENTE.REVOKEDMARK REVOKEDMARK
FROM SANES_GDOPEN_PL_RE_ORDEN_STATES_TABLE PENDIENTE
LEFT OUTER JOIN SANES_GDOPEN_CP_ORDEN_CONTR_TABLE CONTR ON ((PENDIENTE.ROWKEY = CONTR.KEYOPERACION))
EMIT CHANGES;
```


We found the same errors that put us again on the `orphaned resources` state again.

```ad-error

[2022-03-22 16:24:28,737] WARN [Producer clientId=_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-9af2cccf-f899-4e3b-8d3c-0b9e4548a852-StreamThread-1-producer] Error while fetching metadata with correlation id 60189 : {_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-Project-Last-changelog=UNKNOWN_TOPIC_OR_PARTITION} (org.apache.kafka.clients.NetworkClient:1213)

[2022-03-22 16:24:28,838] WARN [Producer clientId=_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-9af2cccf-f899-4e3b-8d3c-0b9e4548a852-StreamThread-1-producer] Error while fetching metadata with correlation id 60190 : {_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-Project-Last-changelog=UNKNOWN_TOPIC_OR_PARTITION} (org.apache.kafka.clients.NetworkClient:1213)

[2022-03-22 16:24:28,939] WARN [Producer clientId=_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-9af2cccf-f899-4e3b-8d3c-0b9e4548a852-StreamThread-1-producer] Error while fetching metadata with correlation id 60191 : {_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-Project-Last-changelog=UNKNOWN_TOPIC_OR_PARTITION} (org.apache.kafka.clients.NetworkClient:1213)

[2022-03-22 16:24:29,041] WARN [Producer clientId=_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-9af2cccf-f899-4e3b-8d3c-0b9e4548a852-StreamThread-1-producer] Error while fetching metadata with correlation id 60192 : {_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-Project-Last-changelog=UNKNOWN_TOPIC_OR_PARTITION} (org.apache.kafka.clients.NetworkClient:1213)

[2022-03-22 16:24:29,175] WARN [Producer clientId=_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-9af2cccf-f899-4e3b-8d3c-0b9e4548a852-StreamThread-1-producer] Error while fetching metadata with correlation id 60193 : {_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-Project-Last-changelog=UNKNOWN_TOPIC_OR_PARTITION} (org.apache.kafka.clients.NetworkClient:1213)

[2022-03-22 16:24:29,276] WARN [Producer clientId=_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-9af2cccf-f899-4e3b-8d3c-0b9e4548a852-StreamThread-1-producer] Error while fetching metadata with correlation id 60194 : {_confluent-ksql-default_query_CTAS_SANES_GDOPEN_STATES_TABLE_1813-Project-Last-changelog=UNKNOWN_TOPIC_OR_PARTITION} (org.apache.kafka.clients.NetworkClient:1213)

```

#### KSQL Cluster Reboot

To mitigate the `orphaned resources` state we proceed to delete again the queries and reboot the ksql cluster.

After the reboot we found a `processing log error`:

```ad-error

[2022-03-22 12:02:25,186] WARN [Producer clientId=producer-1] Error while fetching metadata with correlation id 4113928 : {ksql-processing-log=UNKNOWN_TOPIC_OR_PARTITION} (org.apache.kafka.clients.NetworkClient:225)

```
 for more details go to [[2022-02-25 Santander Weekly Report#KSQL Post Upgrade Findings]]

#### Querie Chain Recreation

```ad-success

1. Recreate Query
2. New data on every single partition -> data consumed and we can see it on output topic & app
3. Other dependedant table (Altas) starts to catch up -> at this days no news of the state so we asume are ok now

```

### Conclusions

```ad-hint

KSQL Rolling update strategy caused some errors on the query recreation. The base of this errors was the incorrect replication factor/ minISR configuration of this given topics.

```
```ad-summary
title: KSQL Upgrade

1. Check replication factor and minISR for involved topics (internal ksql topics are out of this)
2. Stop all cluster instances before doing the upgrade
3. Be sure all queries are correctly stopped
4. Take a snapshot of the consumer lag (it would be preferable to wait all lags comes to zero)
5. Start instances with new version
6. Check correct replication on topics
7. Check processor log 
8. Check we are not falling on orphaned resources streams

```
```ad-hint

When we found a **minISR not reached state** for **KSQL output topics** is better to recreate the queries from scratch avoiding repartitions and rebalancing situations.

```
```ad-warning

Ressign partitions of a huge amount of topics (ksql ones or not) comes with a huge amount of extra work for the brokers so proceed very carefully

```
```ad-warning
title: Orphaned Resources States

As we saw during the troubleshooting in high load situations the deletion of ksql queries can origin some race conditions leaving the cluster on some inconsistent state for that particular consumer group.

We will found log traces as we describe on [[#Recreate Query]] section. The main idea is that the consumer group generated by the queries is not correctly deleted and is causing some *unknown topic or partition* errors that at the end are overloading the cluster and causing consumption lag for the topic.

```
```ad-hint

Check the state of the consumer group state not only on the broker (it will be deleted correctly) but on the ksql cluster too.

On this king of situations recreate the entire query chain is the best option, but reboot the server can works too.

```

### Next Steps

```ad-summary

- Complete document with some more evidences (to be provided by Big Data infra team)
- Check document with teams involved
- Complete migration cheklist with the result.

```
