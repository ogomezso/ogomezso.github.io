# replicator_config
## 2022-02-15 15:02

### Status: #idea
### Tags:[[replicator]] [[kafka_connect]] [[kafka_producer]] [[kafka_consumer]] [[MRC]]

### References:
- [[Replicator Overview]]
---
### Key Notes:

***Replicator*** is nothing more that a connector plugin optimize to use the native Producer and consumer, so you only need to install the plugin on your connection worker$^{[\textbf 1]}$.

```ad-example
~~~json
{
	"name": "replicator-dc1-to-dc2-topic1",
	"config": {
		"connector.class": "io.confluent.connect.replicator.ReplicatorSourceConnector",
		"topic.whitelist": "topic1,_schemas",
		"key.converter": "io.confluent.connect.replicator.util.ByteArrayConverter",
		"value.converter": "io.confluent.connect.replicator.util.ByteArrayConverter",
		"src.kafka.bootstrap.servers": "broker-dc1:29091",
		"src.consumer.group.id": "replicator-dc1-to-dc2-topic1",
		"src.consumer.interceptor.classes": "io.confluent.monitoring.clients.interceptor.MonitoringConsumerInterceptor",
		"src.consumer.confluent.monitoring.interceptor.bootstrap.servers": "broker-dc2:29092",
		"src.kafka.timestamps.topic.replication.factor": 1,
		"src.kafka.timestamps.producer.interceptor.classes": "io.confluent.monitoring.clients.interceptor.MonitoringProducerInterceptor",
		"src.kafka.timestamps.producer.confluent.monitoring.interceptor.bootstrap.servers": "broker-dc2:29092",
		"dest.kafka.bootstrap.servers": "broker-dc2:29092",
		"confluent.topic.replication.factor": 1,
		"provenance.header.enable": "true",
		"header.converter": "io.confluent.connect.replicator.util.ByteArrayConverter",
		"tasks.max": "1"
	}
}
~~~
``` 

Any standard Producer/Consumer can set up under:
```ad-info
title: config for clients
~~~yaml
producer.override.* : Producer Writing to the destination cluster 
src.consumer.* : Consumer reading from the source cluster 
des.kafka.* : Admin Client for topic manipulation on **Destination** 
src.kakfa.*: AdminClient for fetching on Source
~~~
```

```ad-hint
title: 1

As good practice Replicator should be on its own worker.

For instance there is a separate replicator image

```

### Tips & Warnings:

````ad-note

By default uses Connect offset, you can change to *consumer*


````
```ad-info
title: config
offset.start: connect/consumer
```
#### Topic Renaming
```ad-tip
title: Topic Renaming
To rename completely the destination topic we should use **RegexRouterSMT**
```
```ad-warning

All config and admin of the topic must be outside replicator. 

Topic config must be set up by another process **best practice**

```
```ad-example

 "topic.config.sync": false,

 "topic.auto.create": false,

 "topic.preserve.partitions": false,

 "transforms": "dropPrefix",

 "transforms.dropPrefix.type": "org.apache.kafka.connect.transforms.RegexRouter",

 "transforms.dropPrefix.regex": "example\\.(.*)\\.out",

 "transforms.dropPrefix.replacement": "br.$[1.in](http://1.in/)",

```


```ad-warning
title: Offset Flush Timeouts

[2019-04-04 12:42:19,886] ERROR WorkerSourceTask{id=SapMaterialsConnector-0} Failed to flush, timed out while waiting for producer to flush outstanding 712 message

s (org.apache.kafka.connect.runtime.WorkerSourceTask)


You can increase **offset.flush.timeout.ms** or reduce tha buffer size **producer.buffer.memory**
(recommended for large msgs) on [[kafka_connect]] worker configs.

```
```ad-warning
title: topic regex
`topic.regex` can be used but not pick automaticatlly new topics you need to restart
```
