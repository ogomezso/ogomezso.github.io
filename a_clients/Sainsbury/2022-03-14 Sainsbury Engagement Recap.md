![[zzzz_Attachments/confluent_logo.png]]


# 2022-03-14 Sainsbury Engagement Recap
## Client: 
[[Sainsbury]]
## Engagement Type: 
[[Tech Enablement]]

## 2022-03-14
---
**Oscar Gómez Soriano - Solutions Architect**  

```toc
 style: bullet 
 min_depth: 3 
 max_depth: 6
```
---

### Whiteboard

![[Drawing 2022-03-14 Sainsbury White Board]]

---

### Work Notes

#### Deletion of Replicated Topics

```ad-hint

When *"topic.config.sync"* have true value all the actions, will be propagated to the replicated one.

So if we delete data by setting *retention.ms* or *retention.bytes* to 0 replicated topic will be purged too.

```

```ad-attention
title: Fresh Start for Topic Data

In case that owners of public topic want to purge topic data we need to be aware of:

- Consumers will eventually lost data
- Extra work:
	- Stoppage plan for production
	- Producer - clients have to agreed the change to avoid data lost and error on consumer side

```

```ad-tip

Encourage Teams to follos this recommendantions/rules:

- Public Topics should have a Schema
	- All meaningful data should have one to be able to habe proper data lineage and governance in place 
	- Deprecate schemaless public topic, any change must come with the proper schema
- AVRO over Plain Json:
	- More Compression
	- nested object simplifies schema design
- Schema Evolution over creation of new Topics (adding version to topic name)
- Purging Topics should not be allowed

```

```ad-caution

Migration to topic with schemas will come with a cost on Development Team side. So as trade off we can asume:

- For this ones that are versioning that through topic name:
	- Migrating last n version of a topic. *Just this one that are being used by consumers*
	- Create a new topic with new version during the migration. An mark it as deprecated with a clear date to fix it
	- Migrate to a new meaningful topic with proper schema in place
 
```

#### Topic Migration Grouping Strategy

##### Goals

- [x] Check Migration Strategy regarding Topic which each Replicator Instance will migrate
- [x] How we can escalate/plan through new request: Priority vs Maintainability

##### Migration Strategy

```ad-done
title: Do

- Use topic.regex not topic whitelist
- Define workload for your migration group and your cluster properly. [[#References#Cluster Sizing]] 
- Monitor Replicator Message lag. [[#References#Replicator Monitoring]]
- Template, version and create a config repository (git)

```
```ad-error
title: Don't


- Change Consumer Group Ids
- Be aware that replicator will star *from-beginning*

```


###### Domain Based Strategy

```ad-summary

- Sizing exercise of Domain Data
- 1 Kafka Connect K8s Deployment per Domain 
	- Resources by default according [system recommendations](https://docs.confluent.io/platform/current/installation/system-requirements.html)
	- Number of replicas and replicator instances and tasks depending on the sizing exercise
	- Topic Regex on domain level (all domain topics will be migrated at the same time)
- When new Teams requires a topic to be replicated we can face two situations
	1. **Topic is under an already migrated domain**, topic will be already migrated
	2. **Topic is under a not migrated domain**, provide date for domain migration.
```

```ad-success
title: Pros

- Maintanaibility
- Predictibility and Planning Ease

```

```ad-warning
title: Drawbacks

- **Retention of data**: If development consumer comes much later than migration happens data can be evicted by retention time / bytes. **The only thing we can do is to be sure that we have the same retention on destination and source topic or considering infinite retention on destination**.
- Possible increase on costs

```

######  App based Strategy

```ad-summary

- Sizing exercise by Domain
- 1 Kafka Connect k8s deployment per domain
	- Number of replicas depending on domain sizing exercise
- App Data Sizing Exercise
- Replicator instance per APP (all app topics will be migrated at same time)
	- Number of task dependending on app sizing exercise
- New Topic Requirement
	1. Create a new App Replicator instance if needed
	2. App replicator in place

```

```ad-success
title: Pros

- Granularity and control on the migration plan
- best control of the resources and cost

```

```ad-warning
title: Drawbacks

- Maintainability (much more work)

```

#### Debugging Scenarios

On whiteboard

#### Centralised Monitoring

```ad-info
 - New Relic Prometheus Agent or New Relic Flex Agent (preferred) scrapping directly to all componetns on different clusters$^1$
 - One centralised New Relic Dashboard to monitor them all
 - Explore possibility to one Control Center to manage all clusters 
```
```ad-warning

Be aware of the load of the prometheus agents and new relic metrics backend, the gap between the production of the metric to be available on the final dashboard is directly related to this workload.

[[#References#One big clusters or Many Small Ones]]

```

#### Compact Topics Configuration

```ad-question

Can we have delete / compact configuration at the same time?

What can be a good use case for this config?

```

```ad-summary

As we can see in [[#References#Compacted Topic]] we have three different possibilties of cleanup policies for the topics:

- **Delete:** Topic data will be stored on current segment until reach the `segment.bytes` and/or `segment.ms` configuration. Old segments will be deleted according `retention.ms` and/or `retention.bytes`  configurations.
- **Compacted:** In short we will have just the last value of a given available on the current segment. **With this config the las key will never be deleted**
- **Both:** We will have the same behaivour that the compacted topics but **the retention.ms will apply to the las valid key too**. That means that our last valid values will be deleted from the segment after the retention.ms.
```

```ad-tip
title: Possible Use Cases for Both

1. **High load on a compacted topic:** The compacted topic will manage huge amount of data and even mantain all the values for a given key is not worthy (we will keep it on a *cold storage*, some kind of **Archive**, for example.)
2. **Windowed Key Changes:** On this case we just want to have the value of a given key on a given period of time.
3. **Windowed Changelog:** In fact, many of our internal topics (Control Center, Kstreams changelog, etc) work in that way.

```


### Open Questions

Possibility to have one Confluent Cloud Control Center for manage all the clusters (many environments). [[#References#Configuring Control Center]]

There is some differences between the monitoring API [docs](https://api.telemetry.confluent.cloud/docs?_ga=2.165159935.1146243151.1647248586-1082649824.1639387855&_gac=1.60102751.1647263299.Cj0KCQjwz7uRBhDRARIsAFqjulmrY8U7SUTroFxOyoKcKrp856OEmPIIRPtw3AIUWJ2VLmZDDuoM9lEaAlX3EALw_wcB#tag/Version-2/paths/~1v2~1metrics~1{dataset}~1descriptors~1resources/get) and what we saw on the clusters:

![[Pasted image 20220314174956.png]]

```ad-question

What of those two we should use?

Is there any deprecation plan for "the old one"?

```



### References

#### Cluster Sizing
[Event Sizer]( https://eventsizer.io/)

#### Configuring Control Center
[Control Center Config](https://docs.confluent.io/platform/current/control-center/installation/configure-control-center.html#multi-cluster-configuration)

#### Replicator Monitoring
[Replicator Monitoring](https://docs.confluent.io/platform/current/multi-dc-deployments/replicator/replicator-monitoring.html#)

#### One big clusters or Many Small Ones
[essay](https://cnr.sh/essays/one-big-cluster-many-small-ones)

#### Compacted Topics
[Cleanup policy Configuration](https://docs.confluent.io/platform/current/installation/configuration/topic-configs.html#topicconfigs_cleanup.policy)
[Log Comapaction](https://kafka.apache.org/documentation/#compaction)