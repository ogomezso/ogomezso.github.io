# schema_migration
## 2022-02-15

### Status: #idea
### Tags: [[replicator]] [[schema_registry]]
### References:
- #confluent_docs [Schema Migration]( https://docs.confluent.io/platform/current/schema-registry/installation/migrate.html#schemaregistry-migrate)
- #confluent_docs [Replicator Schema Translation Example](https://docs.confluent.io/platform/current/tutorials/examples/replicator-schema-translation/docs/index.html#quickstart-demos-replicator-schema-translation)
- #confluent_docs [Schema Registry Limits](https://docs.confluent.io/cloud/current/sr/schemas-manage.html#supported-features-and-limits-for-sr-ccloud)
- #confluent_docs [Supported Versions and Interoperability](https://docs.confluent.io/platform/current/installation/versions-interoperability.html#interoperability-versions)
- #confluent_docs [Continuous Migration](https://docs.confluent.io/platform/current/schema-registry/installation/migrate.html#continuous-migration)
- #confluent_docs [One-off Migration ]([https://docs.confluent.io/platform/current/schema-registry/installation/migrate.html#one-time-migration](https://docs.confluent.io/platform/current/schema-registry/installation/migrate.html#one-time-migration))
- #confluent_docs [Quick Start Guides]([https://docs.confluent.io/platform/current/schema-registry/installation/migrate.html#quick-start](https://docs.confluent.io/platform/current/schema-registry/installation/migrate.html#quick-start))

---
### Limitations:

```ad-warning
Confluent Cloud just can be the destination for schema migration. To migrate from this kind of enviornment you need to use [[schema_registry_api]]
```

```ad-warning
title: Restrictions

Replicator versions superior of 5.4.0 cannot be used for Apache Kafka versions v0.10.2 or earlier and Confluent Platform v3.2.0 or earlier

```

```ad-tip

For early versions use v5.0.x . 

Check Supported Version of references

```

```ad-summary
title: Continuous Migration

- Replicator must be placed on Self Managed Cluster (Confluent Platform)
- Self managed Schema Registry is set as Primary as Origin of the schemas
- Confluent Cloud Schema Registry set on **Import Mode** and will replicator will copy the schemas there
- On case of _extend cluster_ we don't need to migrate topics, since only will live on the Self Managed cluster.
- On _active-active_ kind of deployments topics must be migrated too.

```
![[Pasted image 20220215192707.png]]

```ad-summary
title: One-off Migration

- Confluent Cloud Schema Registry set as **PRIMARY**
- New schemas will be registered on Confluent Cloud Schema registry
- _shift and lift_ situation, we will need to migrate the Topics to Confluent Cloud since our producer and consumers can connect to

```
![[Pasted image 20220215175226.png]]