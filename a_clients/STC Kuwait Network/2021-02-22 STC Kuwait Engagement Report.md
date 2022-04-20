![[zzzz_Attachments/confluent_logo.png]]


# 2021-02-22 STC Kuwait Engagement Report

---
  
#### Oscar Gómez Soriano - Solutions Architect  


## Q&A Session for better Topic Configuration and capacity understanding

### Session goal:

ESP Teams will integrate with Kafka, so they need to understand the configuration that can suits better with their use case and the implications of this.

### Work Notes:

#### **Topic Topology: Logical Perspective**:

1. ***Topic*** is a Logic Construct made by ***Partitions*** that actually lives inside a ***Broker***:
2.  ***Partition*** can be replicated through ***Brokers***, one of this will be the ***Leader*** and the other will be ***Replicas***. 
3. Clients (Producers/Consumer) will ***only communicate with Leader Partition***
4. ***Replica*** will sync with the ***Leader*** on a **Zero copy strategy**. This means that will be the replica who is in charge to ask the leader for new message that comes avoiding the overuse of resources for this task.
5.  ***Replication Factor and Partition Number*** are configurable both on creation time and runtime$^1$

![[21-02-22  [STC] Topic Basics.png]] 
```ad-warning
title: 1-Repartition

Change the number of partitions on a existing topic is a sensible operation and comes with some operational and performance costs so be very conscious of this kind of changes.

```
```ad-tip

Replicas is the best way to ensure your data will never be lost

```


#### **Topic Topology: Infrastructure Perspective**

1. ***Partitions*** are made of ***Segments***$^1$ 
2. ***Segments*** are log files located on the ***broker*** file system (or any that is configured). This logs files contains the messages on strict order of production, the ***ordinal of the message within a partition is called offset***. 
3. ***Segments are inmutable***
4. ***Segment Capacity*** is determined by `segment bytes` configuration. Once one ***Segment*** reach the size limit another file is created.
5. `retention ms` determines the ***time limit for a segment to be available to the system**. So is the ***minimun time that a single message will be available for a consumer***$^2$
6. `retention bytes` configure the max capacity for all partition segment$^2$.
 ![[21-02-22  [STC] Topic Basics-segments.png]]
 ```ad-hint
title: 2-Retention Considerations

[retention.bytes](https://docs.confluent.io/platform/current/installation/configuration/topic-configs.html#topicconfigs_retention.bytes)
 [retention.ms](https://docs.confluent.io/platform/current/installation/configuration/topic-configs.html#topicconfigs_retention.ms)

```

#### Producer vs Consumer

Messages are distributed within partitions taking in count the ***Partition Key***, this PK will be the *key* of the message$^1$. So messages with the same key will be placed on the same partition. 

**Producers** will communicate with the bootstrap server/s configured to get the metadata info about the topic in what it wants to put the message (partitions and leaders of each one). 

Once he got it going to calculate the partition that correspond with a given key a establish a peer to peer communication with the broker containing the leaded of this specific partition.

**Consumer instance** are grouped on a ***Consumer group***, each consumer instance is able to read from one or more partition.

More than one ***Consumer group*** can consumer message from a topic (all of them will be reading all the available messages)


```ad-tip

The maximun parallelism we can have on consumer side is the number of partitions we have for a topic

```


```ad-tip
If no key is specified messages will be distributed within partitions in round robin fashion
```


```ad-important
title: Partition Considerations

**Load Balance**: Messages with the same key always will be placed on the same partition, so a good partition key will the the one that ***ensure the even distribution of the message within all the partition***. This way the workload of each broker will be even.

***Ordering***: The other important factor to the partition number election is the Ordering. ***Order is only preserved within a Partition, never through a topic***

```
![[21-02-22  [STC] Topic Basics- producer-consumer.png]]
```ad-tip

Partition will increase the throughput on the producer side, and the max parallelism you can have on consumer (so throughput will be raise too)

```

```ad-tip

If ordering is important you have to choose carefully the partition number, taking in count the number of consumer instance you will have on the other side

```

### Conclusions:

1. For ESP integration ordering ***is not important*** so key will be left to null
2. No specific requirement on throughput or parallelism so **partitions** will be set to 3 
3. As a good practice **replica factor** will be set to 3
4. No specific requirement for **retention** so will be left as **default (7 days)**


## Zookeeper Security

### Session Goals

Prepare certificates to secure zookeeper internal communication trough mTLS

Explain  `cp-ansible` security config and how to use it

### Work Notes

Convert certificates from p7b format to cer:

```ad-note
title: Certificate Conversion

~~~bash
openssl pkcs7 -print_certs -in zk-<n>.p7b -out <hostname>.cer
~~~~

```

```ad-info

Cert name has to match with the hostname on the ansible inventory so we can configure it on a easy way  

```

#### Pending tasks

1. Generate Zookeeper JKS files with the signed certificates provided for infosec team and the key provided with the csr files
2. Generate (or just grab the existing ones from the servers) for the rest of the components
3. Configure ansible inventory to use this kind of config:

```ad-example

~~~yaml
ssl_enabled: true

ssl_mutual_auth_enabled: true

ssl_provided_keystore_and_truststore: true

ssl_keystore_filepath: "<path_to_jks_files_on_ansible_machine>/{{inventory_hostname}}-keystore.jks"

ssl_keystore_key_password: ***              

ssl_keystore_store_password: ***

ssl_truststore_filepath: "<path_to_jks_files_on_ansible_machine>/kafka-truststore.jks"

ssl_truststore_password: ***

ssl_truststore_ca_cert_alias: <ca_alias>
~~~

```

### Conclusions

I recommend to reconsider secure Zookeeper on this very moment, taking in count the nature of the use cases in place and the infra security in place (isolation of the Zookeeper machines at network level) I would say that securing ZK at this moment has to be a **nicet to have** more than a **mandatory** task.


## Next Steps

1. I strongly recommend a ***Path finder session*** as a good way to get the most from the cluster. I would help a lot to identify the next uses cases and go through Architectural Designs for it and the cluster improvements you will need
2. Migrate from CP 6.1.1 to 7.x and the consequent migration of cp-ansible.