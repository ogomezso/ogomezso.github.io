![[zzzz_Attachments/confluent_logo.png]]


# Migration Runbook

# [[Allianz]]

##  2022-03-02
---
Oscar Gómez Soriano - Solutions Architect  

```toc
 style: bullet 
 min_depth: 3 
 max_depth: 6
```

### Goal

>Provide Code snippets and references to guide the Migration from current *MSK Cluster* to *Confluent Cloud*

```ad-warning
title: Disclaimer

This is a *Work in Progress* living document that will be updated/completed with the outcome of the PREPROD Migration.

```


```ad-info

The approach of the document will go through the original *Migration Plan* just qualifying the important points 

```
 
### Action Items

#### Verify CCloud Networking

```ad-note

Covered on previous engagements

```
```ad-tip
title: Automation Script

[[#References#Private Link Automation Script]]

```

#### Verify Connection

```ad-info
title: Note

Covered on previous engagements

```
```ad-tip

The easiest way to check connection with CCloud from our linked *AWS VPC* is through *Confluent CLI* on a VM Instance.

[[#Clonfluent CLI]]

```


#### Topic Replication Verification & Test App

```ad-tip

Use [Confluent Cli Kafka Topic Commands](https://docs.confluent.io/cloud/current/client-apps/config-client.html) or *Kafkacat Docker Image* already provided

For Java Clients $^1$:  [[#Java Clients Examples]]

```

```ad-warning
title: 1

This repos are not adapted yet to use on K8s clusters

```

### References

#### Private Link Automation Script
[Repo]([https://github.com/confluentinc/ccloud-connectivity/tree/master/privatelink/aws](https://github.com/confluentinc/ccloud-connectivity/tree/master/privatelink/aws))

#### Clonfluent CLI
[Command Reference](https://docs.confluent.io/confluent-cli/current/command-reference/index.html)
[Topic Management](https://docs.confluent.io/cloud/current/client-apps/config-client.html)

#### Java Clients Examples

[Kstreams App Migration Example](https://github.com/Dabz/kafka-streams-migration)
[Java and Spring Client Examples](git@github.com:ogomezso/kafka-clients-benchmark.git)
[CCloud Client Config](https://docs.confluent.io/cloud/current/client-apps/config-client.html)

### Next Steps
