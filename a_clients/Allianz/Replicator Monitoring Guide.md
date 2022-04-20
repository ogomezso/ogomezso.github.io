![[zzzz_Attachments/confluent_logo.png]]


# Replicator Monitoring Guide

## [[Allianz]]

## 2022-03-02
---
Oscar Gómez Soriano - Nakul Mishra 

```toc
 style: bullet 
 min_depth: 3 
 max_depth: 6
```
### Goals

Provide guidelines and asset to monitor the migration process

### Monitoring

We will be monitoring a *self managed replicator instance* deployed on a Kubernetes cluster (EKS).
The monitoring stack will be based on *Prometheus / Grafana Stack*.

You will find grafana dashboards for Kubernetes on [[#References]]$^1$. 

There are several metrics that are important to take care during the migration process.

#### Replicator Metrics

[Official Doc]((https://docs.confluent.io/platform/current/multi-dc-deployments/replicator/replicator-monitoring.html#important-crep-metrics)

You can find it alongside another useful *Kafka Connect* metrics on the [Kafka Connect Dashboard](https://github.com/ogomezso/cfk-dashboards/blob/main/cfk-kafka-connect-cluster.json)

![[Pasted image 20220301221315.png]]

#### Producer Metrics

[Official Docs](https://docs.confluent.io/platform/current/multi-dc-deployments/replicator/replicator-monitoring.html#important-producer-metrics)
[Kafka Producer Dashboard](https://github.com/ogomezso/cfk-dashboards/blob/main/cfk-kafka-producer.json)

![[Pasted image 20220301221647.png]]

![[Pasted image 20220301221808.png]]

#### Consumer Metrics

[Official Docs](https://docs.confluent.io/platform/current/multi-dc-deployments/replicator/replicator-monitoring.html#important-consumer-metrics)
[Kafka Consumer Dashboard](https://github.com/ogomezso/cfk-dashboards/blob/main/cfk-kafka-consumer.json)

![[Pasted image 20220301222413.png]]

![[Pasted image 20220301222520.png]]


```ad-note
title: 1

This is a private repository, not officialy supported by Confluent

```


### References

[CFK Grafana Dashboards](https://github.com/ogomezso/cfk-dashboards)
[Monitoring Replicator](https://docs.confluent.io/platform/current/multi-dc-deployments/replicator/replicator-monitoring.html#)
