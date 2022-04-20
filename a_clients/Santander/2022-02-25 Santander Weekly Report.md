![[zzzz_Attachments/confluent_logo.png]]
#  2022-03-25 Santander RSA Weekly Report


---
client:
[[Santander]]
status: #report 

---

```toc
 style: bullet 
 min_depth: 2 
 max_depth: 6
```

## Weekly Recap

### Oscar Gómez

### 2022-03-21 Day 1 of 4

#### 2022-03-15 10:01 Schema Registry Best Practices

#### Attendees:
* Oscar Gomez
* Ivan Yañez
* Abel Alvarez
* Fernado Rojas
* Idafe Reyes
* Daniel Acera

#### Notes:

```ad-question

When amd how is worthy to put a schema on our events?

```
```ad-question
title: Why you should have a schema?

1. Schemas are important for **Data Governance** and **Data Lineage** without one this two keys for a *Data Driven Organiztion* are nearly impossible
2. **Cleanup and Data Awareness** schema give us the capability of being aware of what kind of data is on a topic so give us a lot of information of the data we manage and help us to avoid meaningless one saving unnecessary costs and simplifying access to important information.
3. **Schema Evolution:** One of the killing features of *Schema Registry* is the *Schema Evolution* that allow us to, again, be aware of what kind of data is inside of a given topic and evolve it without having to create a new one or just have a breaking version of it.
4. **Subject Name Strategy** allow us a few strategies to set schemas on to a topic (one to one, one to many,...) simplifying a lot many of our use cases (state machines can be a really good example of that) [[#^e83f62|Subject Strategy Name]]
```
```ad-summary

**Raw Data:**

**Internal use only (intra domain service to service Comm):** We can afford not using Schema on this events, due the complexity to know the proper schema and then evolve to a new schema meaningful and possible public topic.


For **legacy systems** systems where we are not able or is difficult to transform data we will use raw one **without any schema**. 

*If we gonna expose it to the organization we should transform the data (ksqldb querie) and expose it in a public topic with proper schema*

**Raw Technical Data:**

Another uses cases for raw schemaless data can be **audit or technical logging topics, metrics**

**Domain Data:**

Understanding *Domain Data* as **any piece of information we expose outside a given domain**. This information is meaningful through the entire organization and **may have a proper schema**

```
```ad-success
title: Agreements

1. Applications that already have a topic without a Schema can continue working as is now but with the commiment of if the data will used on cross domain way they would migrate to one with proper schema on place.
2. We need to take care of the **Topic Naming Convention** as way to ensure the quick understanding of topic nature. We don't gonna over complicate it
3. Architecture will take care of documenting this agreements around schema and topic naming convention on confluence and encourage teams to follow this basic rules. 

```
```ad-hint
title: Recommendations

1. **Schema** is important even for inter domain data. Comes with a little more development work but give us a lot of improvament room technically speaking (message compression, typesafe development, on compilation error awareness, etc) and from business perspective too, just think that start using data previously domain internal will be almost inmediate.
2. **Avoid using Plain Json or other not managed schema**. The use of no managed schemas overcomplicate the client side (serialization/deserialization issues), make the message size higher and and prevents us from using the capabilities of schema registry. **We strongly recommend the use of Schema Registry with AVRO Schemas**
3. Use **AVRO specific objects** instead of **generic ones** will make us improve our *Data Awareness* and simplify a lot our schema design and use   

```
Like an useful asset I want to share with you the Data Mesh Nine Box:
![[Pasted image 20220225103006.png]]

#### KSQL GDO2 Incident

> Check the GDO2 KSQL Incident Postmortem Document
> 
### 2022-03-22 Day 2 of 4

#### 2022-03-22 11:00 Panoramix Connector Setup 

##### Attendees:
* Oscar Gómez
* Abel Fernandez
* Alejandro Serrano
* Panoramix Team
 
##### Notes:

```ad-summary

- Basic ideas of *Kafka Connect* authentication and authorization.
- Set up proposal for kerberos authentication for self developed **Dynatrace Source Connector** -> [[#^e8f06c|Connector GSSAPI Authentication]]

```


#### 2022-03-22 15:35 Parallel Consumer

##### Attendees:
* Oscar Gómez
* Miguel Angel García
* Abel Alvarez
* Fernando Rojas
* Daniel Villanueva
* Rafael 
 
##### Notes:

```ad-question

Parallel Consumer can be the solution for having external service calls inside a consumer?

```
```ad-quote

**In general couple message consumption and processing is not a recommended pattern**. But keeping this in mind *Parallel consumer* has a separate "thread" for consumption and process of the message taking care of the back pressure, correlation and order within internal queues, so it will reduce the risk and the complexity.

```
```ad-summary

 - Beside of Parallel consumer easiness of use we have to take in count the increase of complexity on *Error handling*
 - Other concern, explicitly on the side of the external calls can be the resilience. We strongly recommend put patterns such as *circuit breaker, bulkhead or backpressure* on external client side
```
```ad-success
title: Agreements

- Architecture will share their POC and we gonna take a look to give some advice and find *the when and why* of the use of Parallel Consumer
- Due the constraint of using docker, we are not able to use *test container* so we need to find a way to do integration testing for this uses cases. We will try provide a in memory cluster and SR on the *Kafka test util library*

```

#### KSQL GDO2 Incident

> Check the GDO2 KSQL Incident Postmortem Document
> 
### 2022-03-23 Day 3 of 4

#### 2022-03-23 11:00 Panoramix Connector Setup Follow Up 

##### Attendees:
* Oscar Gómez
* Alejandro Serrano

###### Notes:
- Check that all is up & running

#### 2022-03-23 16:00 Functional Test Archetype Alignment

##### Attendees:
* Oscar Gómez
* Jose Alberto Rollon
 
##### Notes:

- Access to the repos.
- Review and alignment on how to integrate the *kafka-test-utils library*  

#### KSQL GDO2 Incident

> Check the GDO2 KSQL Incident Postmortem Document
> 
#### Other

VDI set up

### 2022-03-23 Day 4 of 4

#### 2022-03-23 11:00 Julie Ops Test Pipeline Setup on LAB Env

```ad-error

We found an *admin client* timeout error on the pipeline execution listing the topics (first task of Julie Client).

The problem was on the kerberos authentication. The *jenkins agent container* has not a proper krb5.conf file on place.

```
```ad-success

After many tries we found a way to pass Java Opts configs to [[#^793d5e|julie-ops-client.sh]] so we can pass *-Djava.security.krb5.conf="./krb5.conf"* 

We need to provide the proper **krb5.conf** on the repo.

Set up *JULIE_OPS_OPTIONS* with *-Djava.security.krb5.conf* pointing to the path to the krb5 config file.

```

### Dennis Federico

### 2022-03-22 Day 1 of 3

#### KSQL GDO2 Incident

> Check the GDO2 KSQL Incident Postmortem Document

### 2022-03-23 Day 2 of 3

#### Connect Monitoring Interceptors:

```ad-summary

- Activated manually on a external Environment
- Documentation from Big Data infrastructure team with all the configuration changes neccesary to activate monitoring at cluster level.
- Overhead estimate on Kafka Storage: Monitoring topic ~3%
- We are able to change this by applying a flag on Ansible Inventory

```

#### Checklist Upgrade to PRO

```ad-summary

- We plan to do it by several days
- Draft plan to do:
	- **Day 1:** ZK, Brokers, Schema Registry, Control Center$^*$
	- **Day 2:** Connect, Replicator, Control Center$^*$
	- **Day 3:** KSQL2, KSQL3, KSQL-Panoramix, KSQL1$^1$
- Backup plan for KSQL1 (*see image below)

```

![[Pasted image 20220323123108.png]]

### 2022-03-24 Day 3 of 3

#### KSQL Post Upgrade Findings

^edd414

```ad-info
title: ksql_cluster_name
***ksql_cluster_name*** is used to define the MDS Identity when set
```
```ad-info
title: ksql_service_id
***ksql_service_id*** Should be used when defining multiple ksql clusters, defaults to `default_` is used as a prefix for most metadata and topics, also when creating RBAC bindings on SR, Topics and so on
```
```ad-info
title: ksql_processing_log
This is used only when ***ksql_log_streaming_enabled***, auto creates the logging topic with the value of "***`{{ksql_service_id}}{{ksql_processing_log}}`***"
Default value: `processing_log`
```

##### Tests

Using the following "default" settings in the ansible host inventory for cp-ansible 6.1.1-post. Taken from the deployment in PRE
```yaml
ksql:
  vars:  
    ksql_cluster_name: ksql
    ksql_log_streaming_enabled: true
    ## THIS WILL CREATE A TOPIC NAMED default_ksql-processing-log
    ksql_processing_log: "{{ksql_cluster_name}}-processing-log"
```

I can see the expected topic in C3
![[Pasted image 20220324133819.png]]

**NOTE** the internal *_command_topic* that has been prefixed with the ***ksql_service_id*** - each new cluster should have its own command topic and processing log (if enabled)

From MDS perspective, only a ksql-cluster has been registered since we only set the *cluster_name* for that role (we didn't set the kafka_broker_cluster_name nor schema_registry_cluster_name) in this test

![[Pasted image 20220324134517.png]]

Expected Rolebinding given the topics...

![[Pasted image 20220324135214.png]]

But most **important** the permission to the TransactionalId with `ResourceId={{ksql_service_id}}`

![[Pasted image 20220324135655.png]]

##### Upgrading and Adding additional KSQL clusters

This is the proposed Ansible host inventory fragment for upgrading the outstanding ksql cluster while adding two more and setup C3 properly.

```yaml
ksql_default:
  vars:
    ksql_cluster_name: ksql
    # Set explicitly, keep the previous value when upgrading
    ksql_service_id: default_
    ksql_log_streaming_enabled: true
    # Keep this as it was the previos naming convention
    ksql_processing_log: "{{ksql_cluster_name}}-processing-log"
  hosts:
    dfederico-demo-ksql-0:

ksql_wraggle:
  vars:
    ksql_cluster_name: ksql-wraggle
    # Set explicitly, keep the previous value when upgrading
    ksql_service_id: wraggle-ksql_
    ksql_log_streaming_enabled: true
  hosts:
    dfederico-demo-ksql-1:

ksql_analytics:
  vars:
    ksql_cluster_name: ksql-analytics
    # Set explicitly, keep the previous value when upgrading
    ksql_service_id: analytics-ksql_
    ksql_log_streaming_enabled: true
  hosts:
    dfederico-demo-ksql-2:

ksql:
  children:
    ksql_default:
    ksql_wraggle:
    ksql_analytics:

control_center:
  vars:
    ksql_cluster_ansible_group_names:
      - ksql_default
      - ksql_wraggle
      - ksql_analytics
  hosts:
    dfederico-demo-c3-0:
```

Note that we recommend the default naming convention for `ksql_processing_log` because it still leverages of `ksql_service_id` for composing the name and it is of the utmost importance to set explicitly when defining multiple ksql clusters.

It's also a good practice to set `ksql_cluster_name`, not only for "legibility", but also to trigger the cluster registration in MDS by Ansible.

From the Confluent Control Center point-of-view, the clusters appear using the name as expected

![[Pasted image 20220324172641.png]]
And the topics are created as expect by keeping the ones for the original cluster without changes

![[Pasted image 20220324172835.png]]

Rolebinding assignments in C3 work as expected, since it fetches all RB entries in spite of the registered clusters, and there's always the binding available for the service user.
![[Pasted image 20220325113859.png]]

```ad-bug
title: Feature Request

We just found that Ansible playbook not cover the functionality related to the registration of multiple "sub-clusters", so I just put a PR to cover it.

```
```ad-summary

It's a hard to rationalize issue, since ansible hosts group tree is stored and processed as a flat list and doesn't honor sub-groups. The result is that only one cluster will be registered in MDS comprising all 'ksql' host regardless of their sub-cluster distribution

```

![[Pasted image 20220325105702.png]]

```ad-tip
While we work and submit a bugfix, the work around would be to 'unregister' any wrongly registered cluster and manually register each cluster 
```
```bash
# To view the list of clusters in MDS
$ confluent cluster list

# To un-register clusters
$ confluent cluster unregister --cluster-name ksql

# Example of ksql cluster registration (note. the kafka cluster id can be retrieved with the list command, assuming the kafka cluster is explicitly named) 
$ confluent cluster register --cluster-name ksql --kafka-cluster-id <kafkaId> --ksql-cluster-id default_ --hosts <default_ hosts> --protocol HTTPS

$ confluent cluster register --cluster-name ksql-wraggle --kafka-cluster-id <kafkaId> --ksql-cluster-id wraggle-ksql_ --hosts <wraggle-ksql_ hosts> --protocol HTTPS

$ confluent cluster register --cluster-name ksql-analytics --kafka-cluster-id <kafkaId> --ksql-cluster-id analytics-ksql_ --hosts <analytics-ksql_ hosts> --protocol HTTPS

```

![[Pasted image 20220325113214.png]]

## Open Questions

```ad-question
title: ksqlDB Capacity Plan & Scalability

How I can know how and when to scale a **ksqldb Cluster**?

We recommend having a specific workshop for that but in the meantime you can still review the [[#^31d2b4|official docs]] about it.

```
```ad-question
title: Enabling Monitoring by connector

Are we able to enable/disable monitoring interceptors by connector?

```

## Next Steps

```ad-note
title: GDO2 KSQL Issue Post mortem review

- Complete information and review together the GDO KSQL incident report

```
```ad-note

Document Backup plan for KSQL upgrade

```
```ad-note
title: Julie Main Operations Runbook

Provide Document as recap of [[#^a3fd2f| Julie Ops Examples]] for the min operations requested:

- Topic Management
- Schema Management
- ACL Management
- Client Management (Producer, Consumer, Streams)
- Connector Management
- ksqlDB Queries Management
- Topology Validations

```

## References 

#### Schema Registry
[Subject Name Strategy](https://docs.confluent.io/platform/current/schema-registry/serdes-develop/index.html#sr-schemas-subject-name-strategy) ^e83f62

#### Kafka Connect
[Connector Kerberos GSSAPI Authentication](https://docs.confluent.io/platform/current/kafka/authentication_sasl/authentication_sasl_gssapi.html#kconnect-long) ^e8f06c

#### Julie Ops
[Julie Ops Topology Descriptor Examples](https://github.com/kafka-ops/julie/tree/master/example) ^a3fd2f

[julie-ops-client.sh](https://github.com/kafka-ops/julie/blob/master/src/main/scripts/julie-ops-cli.sh) ^793d5e

#### ksqlDB

[ksqlDB operate & deploy](https://docs.ksqldb.io/en/latest/operate-and-deploy/capacity-planning/) ^31d2b4
