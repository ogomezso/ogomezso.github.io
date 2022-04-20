# CP 7.0

-
- https://confluent.saleshood.com/huddle_events/310449/event_links/40687
## [[Kafka]]

### Apache Kafka 3.0
- **KIP-630** Kraft Snapshot
			New clean up policy `snapshot` that create a file `<topic-name>-<topic-partition>-<partition_index>/checkpoints/x-a.checkpoint`
	DONE Q&A  what is `partition_index`??
- **KIP-720** Drop Support for  msg format V0 and V1 deprecated (removed on Apache Kafka 4.0
- **KIP-709** Extend OffsetFetch requests to accept multiple groups ids
	- Useful for monitoring
	- Language-Deprecations:
		- **KIP-750** Java 8 (drop in Apache Kafka 4.0 )
		- **KIP-751** Scala 2.12 (drop in Apache Kafka 4.0 )
		- **KIP-707** Future of KafkaFuture
			- No needed on version superior of Java 8 which didn't support
			- CompletionStage and/or CompletableFuture
			`KafkaFuture.toCompletionStage` added.
			`toCompletableFuture()` added for 3rd parties that needed
			- **KIP-734** Admin Client.listOffsets returns timestamp and offset with the largest timestamp.
				- now you can easily know which topic is being a slow producer or is not live at all for a while.
			- **KIP-720** Deprecate MirrorMake v1
				- Deprecate on Apache Kafka 3.0
				- Removed on Apache Kafka 4.0
			- **KIP-716** Allow Configuring Location fo offset-syncs topic with MirrorMakerV2
			- **MirrorMakerV2**  uses mm2-offset-syncs topic for offset translation
				- For security sake only reader has granted access to the source cluster to **MirrorMakerV2**
				- Introduction of a new setting to control the location of offset syncs topic:
						```
					  cluster = us-east, us-west
					  us-east -> us-west.enabled true
					  us-east -> us-west.offset-syncs.topic.location = target
						  	```
## [[Kafka Connect]] 
**KIP-745**  Connect API to restart Connector and tasks
  Previously restart just restart the connector and all its task now 2 paramters:
  	`includeTask` : true/false
  	`onlyFailed`: true/false
  	KIP-721 Enable connector log context in Connect Log4j Config
- ## [[Kafka Streams]]
	- [[KIP -695]] Timestamp Synchronization
		- Previously `max.task.idle.ms` said how much time you wait incase of empty buffer on a partition before search in other of the  record with least ts.
			- now `max.task.idle.ms`:
				- `0`: `NEW DEFAULT` always fetch data that is present on the broker
				- `-1`: `PREVIOUS DEFAULT` never wait extra time for broker new data.
				- `int >0`: set extra time to wait for new data on the broker.
	- [[KIP-633]] Remove grace period (24h) for windows:
		- Previously we have 24h grace for events with TS that can be on that window but we need to recompute the entire thing
		- New methods on the `TimeWindows`class:
			- `ofSizeAndGrace`: Parameterized by the window and grace period duration
			- `ofSizeWithNoGrace`: Just accept window duration, grace period set to 0.
	- ### [[Configuration]]
		- [[KIP-732]] `eos-v2` subsitutes `eos-alpha` and `eos-beta` deprecation
		- [[KIP-733]] Default `Replication Factor` for [[Kafka Streams]] App is now ) `-1` that means that's is inherited from cluster config.
		- [[KIP-741]] Default `serde` is null no more byteArray, if not change `configuration exception`
		- [[KIP-466]] `ListSerializer<Serde>`,  `ListDes erializer<Serde>`
		- [[KIP-623]] `internal-topics` which internal topics should be deleted added to application reset tool
	- ## [[KSQL]]
		- `v0.21.0`
		- `Foreign-key JOINs`: just in tables, one side is a `key` the other is just regular `non key column` so n `value columns` can match
		- `show connector plugins` list all connector plugins installed.
		- `createConnector`, `dropConnector`, `describeConnector`, `listConnector` on [[KSQL Java Client]]
		- Idle Timeout for push queries  configurable instead of hardcoded `10 min`
			- `ksql.idle.connection.timeout.seconds`
		- #### Types:
			- `DATE`  & `TIME` comparable types and `Helper functions`
			- `BYTES`
				- perhaps can be used for [[JSON_TO_STRUCT]]  function??
				- `TO_BYTES` & `FROM_BYTES` functions
		- #### Built-in Functions
			- `ARRAY_CONCAT`
			- `LEAST` & `GREATEST`: minimun, maximun
			-
	- ## [[Health+]]
		- new menu option
		- Specific API Key & Secret needed to configure
	- ## [[Confluent Cli]]
		- ACL commands available
		- ## [[KRaft]]
			- https://developer.confluent.io/learn/kraft/
			- TODO TRY IT https://developer.confluent.io/quickstart/kafka-local/ and try it with docker
			- https://www.confluent.io/blog/kafka-without-zookeeper-a-sneak-peek/?_ga=2.149525238.1933551869.1634551446-994832095.1630304430&_gac=1.27996110.1634311297.CjwKCAjwzaSLBhBJEiwAJSRoktk5I-kcm4SAi5vYgKxLpPDEEitOEzaTl53Cqnjlo171uuPEAtMtIRoCJI4QAvD_BwE#zookeeper-less-kafka
	- ## [[Cluster Linking]]
		- https://confluent.saleshood.com/huddle_events/310449/event_videos/471849
		- DONE [[Q&A]]  In back to normal DR case how I sync the destination with the origin one
			- Two way link?
			- create a new link when you want to comeback
		- ### Best Practices:
			- Fanning out (mesh topology, consider DR over complicate with this) & Chaining totally supported
			- Aggregate:
				- Consumer with regex
				- KSQL Process to agregate several topics on unique one. For example useful for [[Active-Active Data Sharing]]  use case
			- #### [[Share Data]]:
				- Data goes from origin to destination,
				- DONE  [[Q&A]]  Two way linking is possible?
				- off Consumer Offsets
				- off ACLs
				- Be aware of consumer quotas since the linked cluster acts like it
			- #### [[Active-Active Data Sharing]]
				- Create a new topic on destinations and relink to origin one, consumer will consume from regex.
				- **BE AWARE OF THE ORDER BETWEEN THOSE TWO**
			- #### [[Migration Process]]
				- Link a topic (no ACLS if don't needed explicitly) -> sync -> migrate consumer groups: **Don't migrate consumer groups (explicitly excluded from the cluster link), weird behaviours expected**
				- Producers migrates on batchs then you call `promote` on your mirror topic and becomes a normal topics.  **You can expect some waiting time to the sync and checked properly**
			- #### [[Disaster Recovery]]
				- **Always ACTIVE - PASSIVE on topic level**
				- ACLs, Customer Offset (reduce RTO) need to be sync too
				- `failover` command over the mirror topics looks  the same of `promote` but acts immediately **(without checks and syncs and no guarantees)**
				- To get back to the original [[Migration Process]] need to be exectuted.
			- #### Limitation
				- NO UI (limitation at all???)
				- Be Aware of network types supported
				- **NO RPO = 0 (no data loss)** guaranteed. MRC (a single "Multi-Region Cluster" needed for that)