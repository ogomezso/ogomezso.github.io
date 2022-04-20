![[zzzz_Attachments/confluent_logo.png]]
# 2022-02-18 Oscar Gómez RSA Weekly Report

---
Client: [[Sainsbury]]
status: #report 
tags: [[replicator]] [[ccloud_migration]]

---

```toc
 style: bullet 
 min_depth: 2 
 max_depth: 6
```
## Weekly Recap

### 2022-02-16 Q&A Session
#### Agenda & Status
- [x] Schema Validation
- [x] Topic Replication Best Practices
- [x] Replication from Commercial Confluent Cloud Cluster
- [x] Producer/Consumer Migration Process
- [x] Kafka Streams Application Migration Process
- [x] Kafka Connect Migration Process

### 2022-02-17 Q&A Session
- [x] RBAC in CCLoud
- [x] Schema Registry Best Practices
- [x] Topic Configuration Best Practices
- [x] Private vs Public Topics
- [ ] Implication of purge a replicated topic
- [ ] Compact Topics

## Work Notes

### 2022-02-16 Day 1 of 2

#### Topic Replication Best Practices

![[Pasted image 20220224113028.png]]

##### Commercial Apache Kafka -> Confluent Supply Chain

```ad-summary

1. We will have several Replicator Instances, one for every migration milstone
2. 1 <-> 1 Replication, Consummer Offset Translation not needed.
3. Topics will be replicated from the proper offset so Consummers that previouly read from Commercial can start consuming from earliest. **In case of this not happen consumer have to be configured to read from proper offset on the first start**
4. Topics remains the same (config & naming) on both sides.

```

##### Commercial Apache Kafka -> Commercial Confluent Cloud Cluster

```ad-summary

1. Once we start to migrate to new Commercial Confluent Cloud Cluster this one will the **Source of Thruth** of our system so:
	1. This will be the origin of the replication to Supply Chain Cluster
	2. We have to asume **eventual consistency** between the sources. That will be dictated by the **replication lag**. _But we don't expect so much lag_
2. Consumers can be on both of the clusters
3. Once migration are completed producers can be moved from Apache Kafka to Confluent Cloud Cluster

```

#### Schema Replication

```ad-important
title: Initial Considerations

Sainsbury needs Schema level ACLS (pending of GA date, see open questions) but putting in place  some devops practices that can mitigate not having it:

1. Set **auto.schema.creation** to **false**
2. Restrict access to Schema Registry Api for nominal users. Only service users associated to CI/CD Orchestrator (Github, Jenkins,...) and admins will have this permission granted
3. Let a automated process (Deployment automated pipeline) create the schema for you.

As **drawback** of this practice we have the **complexity increase** on development teams deployment workflow

```

##### Commercial Apache Kafka -> Confluent Cloud Supply Chain 

```ad-summary

1. Self Managed Schema Registry on Apache Kafka cluster replcated to another Self Managed Schema Registry (on Supply Account) via Replicator (1<->1 replication, we maintain same subject naming strategy and schema id)
2. Consumers of Supply Chain topics moved from Apache Kafka Topic use this self managed Schema Registry
3. New Producers/Consumers for **new supply chain topics** use Confluent Cloud Schema Registry of Supply Environment

```

##### Confluent Cloud Commercial -> Confluent Cloud Supply Chain

##### Day 0 - Migration

![[Pasted image 20220224121550.png]]

```ad-summary

1. A self managed Schema Registry acts as **man in the middle** to replicate schemas from/to Confluent Cloud Commercial to Confluent Cloud Supply Chain
2. Replicator will be placed on both sides replicating Schemas on a one-off mode
```

##### Day1 - After migration

After Schema migration completes we have 2 possible scenarios:

###### Schema Registry ACL Not Present:

![[Pasted image 20220224125403.png]]

```ad-summary

Two options

1. Self managed Schema registry acts a multi cluster Schema Registry (recommended): 
	1. Both Commercial and Supply Chain use this as Schema Repository, no cloud SR Needed
	2. Once ACLS are in GA migrate specific schemas to the proper Confluent Cloud Schema Registry
2. Go with [Schema Replication Continuos Migration Pattern](https://docs.confluent.io/platform/current/schema-registry/installation/migrate.html#continuous-migration) from the begginning and each cluster uses the managed Schema Registry of its own environment

```

###### Schema Registry ACL Present:

![[Pasted image 20220224130418.png]]

```ad-summary

After migration topics have schemas on it's own environment Schema Registry for those consumers
on supply chain that consumer replicated topics from Commercial, schema will be not replicated so will use Commercial One$^1$

```
```ad-warning
title: 1

We have to be sure of allow connectitivy and mesure cautiously possible network lag 

```
```ad-hint
We always can go with this approach just asumming the complexity described on the *Initial Considerations*

```


#### Producer/ Consumer Migration

##### Commercial Apache Kafka -> Confluent Cloud Supply Chain

![[Pasted image 20220224133504.png]]

```ad-summary

1. Start replicating Topics and Schemas (Schemas will live on a Self Managed Schemas & Supply Chain cloud environment one too)
2. Once replicated **new consumer groups** can start to consume from replicated topic using the Managed Schema (no offset translation needed)
3. For migrated consumers from Commercial Apache Kafka uses Self Managed Schema Registry
	1. We need to be sure that **GroupId** remains the same after the migration
	2. \_consumer_offset will be migrated too, in case not present, several options (on recommended priority order):
		1. Put offset translation to work
		2. review last offset consumed on origin topic, config consumer to start from there
		3. replicate \_consumer_offset topic  
```

##### Commercial Apache Kafka -> Confluent Cloud Commercial

![[Pasted image 20220224135028.png]]

![[Pasted image 20220224135115.png]]

```ad-summary
title: Producers

Asuming 1 <-> 1 replication on all topic resources:

After topic is replicated, **producers** just need to point to new boostrap/SR and start to produce again

```
```ad-summary
title: Java Consumers

**Offset translation + Rollout Mechanism + Service Discovery + Config Server**

1. Change config of boostrap servers and Schema Registry url through Config Server
2. Config Server propagates config change to Applications
3. Rollout restart happens after config change

**On prem deployed apps - no stoppage but unsafe mechanism**

1. Stop 1 instance
2. Change config
3. Consumer offset reset happens
4. Restart Consumer with new cluster config

**On prem deployed apps - Safe mode - some downtime**

1. Scale origin consumer group to 1 instance (stop the others)
3. Change config
4. Consummer offset reset
5. Start 1 instance with new config
6. Stop origin Consumer
7. Scale new consumer to desired number

```

#### Kafka Connect Migration (Connectors)

![[Pasted image 20220224153255.png]]

```ad-summary

We will be using a self managed worker still after the migration so:

1. We need to replicate source/sink topics alongside Connect Internal ones
2. Once these are fully replicated, update the bootstrap config on the connector 

```

#### Kstream Apps

##### Kstream Architecture Reminder

![[Pasted image 20220224153936.png]]

```ad-important
title: Note

Kafka Streams only configures one boostrap server for produce and consume so we need to have in and out topic migrated before migrate the app

```


##### With connector as producer

![[Pasted image 20220224154428.png]]

```ad-summary

1. Stop consumer and monitor the topic replication
2. Once in topic is fully replicated, shutdown Kstream app on origin and monitor out topic replication
3. Restart connector and Kstream app pointing to the new cluster  

```

##### Plain Producer as Input

![[Pasted image 20220224155314.png]]

```ad-summary
title: Producer remains on origin cluster:

1. Stop Kstream App
2. Replicate in and out topic (with offset translation) and internal kstreams topics (specially on stateful apps)
3. Start Kstream app on new cluster 

```
```ad-summary
title: Producer moved to new cluster

1. Stop producer and Kstream
2. Migrate in and out topic
3. 2 options, depending on what amount of lag we can asume:
	1. Start Kstream and Producer at same time
	2. Start Kstreams and when is up to date start producer  

```

### 2022-02-17 Day 2 of 2

#### RBAC om Confluent cloud

```ad-note

Discussion around RBAC features and limitation through the offical docs (see references)

```

#### Schema Registry Best Practices

```ad-note

You will find the presentations about Schema Registry we used during the session attached alongside this document.

```

#### Topic Configuration Best Practices

![[Pasted image 20220224203049.png]]

![[Pasted image 20220224203310.png]]

```ad-summary

During the session we go through the kafka basics, as summary find the whiteboard we create on it.

You can find more detailed information on [[#^682a03|Kafka Basics Tutorial]]

```


![[Pasted image 20220224204306.png]]
#### Public vs Private Topics

![[Pasted image 20220224205156.png]]


##### Private Topics

> From developer perspective Private topic are a great pattern for service communication within the same Team/Domain, but the overuse of this pattern can take us away from the main objective of our platform.


```ad-hint
title: Private Topics: The good

- Used to notice internaly some change within the domain: Many consumers will be insterested
- No Schema needed (technical raw data): Use [Contract Testing](https://docs.pact.io/#:~:text=Contract%20testing%20is%20a%20technique,documented%20in%20a%20%22contract%22.) to ensure data realibilty
- You have totally different paces of produce/consume and need totally decoupled Apps.

```

```ad-error
title: Private Topics: The bad

- **Used to communicate within the same Application**
- Use to 1<->1 service communication: Think twice if is the right choice
- If you need to reply your topic data, this need to be reliable, so you will need to be very careful about the schema and its evolution. So perhaps you need a **public topic**
- External users ask for access our private data: **make it public**, if you can't put a clear **deprecation date**


```
```ad-info
title: Private Topics Tips

- Private data normally no need so much retention avoid to use default, perhaps **2 days** will be more than enough
- Try your best to maintain private topics schemaless (just to simplify production and consume) and use compression to minimize the disk usage
- Put a standarized and automated creation process this will avoid developers overuse or just accumulate topics (for example for different version of messages)

```

##### Public Topics

> Public topics expose data across all company within a company

```ad-success
title: Public Topics: Continue Doing

- Governated creation topic process driven by Data Admin Team
- Use this as way to expose company meaningful Domain Data for all internal Clients
- Naming Conventions (take a look to the suggestions on the whiteboard)
- Team request form (don't hesitate to revisit it from time to time)
- ACLS Managed by Data Team
- All users with access to the platform has read access to any topic

```
```ad-error
title: Stop Doing

- Schemaless public topics: You will need to rely on public topic information so you will need a well known and managed schema
- Avoid to attach event version o any kind of similar info to the topic name

```
```ad-tip
title: start to do

- Get deeper understanding on the retention need of the data
- Shift left on the responsability of the data quality and schema check to the domains: **Domain Data Owners**
- Push Domains to expose at least one public topic (perhaps more than one will be needed) as point of querie for other domains 

```

##### Data Governance: The Domain Data Owner

On the way to being a data driven company, governance and data lineage play a key role. A good way to start can be incorporate the rol of the **Domain Data Owner**.

```ad-tip
title: Data Owner Accountabilities

1. Be the first point of contact within the teams on Data Model subjects
2. Do the data assestments within a her/his domain
3. Agree with other Domain Data Owners a general guide and best practices for data modeling
4. Ensure that all key data of the domain are publicly exposed on a company meaningful way.

```


##### Data Assessment: The Data Mesh Nine-Box

A really good way of know what kind of data will be published on a topic and which kind it should be can be te ***Data Mesh Nine-Box***

![[Pasted image 20220225103006.png]]

> The higher and to the right is our data, the more important it will be for it to be publicly exposed.
> 

## Open Questions

```ad-question
title: Questions

- Schema Registry & Kafka (Data plane) ACLs GA Dates for CCloud
- Implications from replicated topic perspective of purge a origin one

```


## References 

- [Confluent Cloud Schema Registry Supported Features and Limits](https://docs.confluent.io/cloud/current/sr/schemas-manage.html#supported-features-and-limits-for-sr-ccloud)
- [Migrate Schemas to Cloud](https://docs.confluent.io/platform/current/schema-registry/installation/migrate.html#migrate-schemas-to-ccloud)
- [Role-Based Access Control for Confluent Cloud](https://docs.confluent.io/cloud/current/access-management/access-control/cloud-rbac.html)
- [Kafka Basics Tutorial](https://www.confluent.io/blog/apache-kafka-intro-how-kafka-works/) ^682a03
