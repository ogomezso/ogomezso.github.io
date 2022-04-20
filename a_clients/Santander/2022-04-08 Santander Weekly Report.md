![[zzzz_Attachments/confluent_logo.png]]
#  2022-04-08 Santander RSA Weekly Report


---
client: 
[[Santander]]
status: #report 
tags: 

---

```toc
 style: bullet 
 min_depth: 2 
 max_depth: 6
```

## Weekly Recap
### Monitoring Interceptors

Research in progress about lags that GDO app are experimenting

```ad-success
title: Done

Monitoring Interceptors Enabled by default on CP-Ansible.

Working on PROD Environment.

```

```ad-question
title: In progress

Keep checking some topics in where metrics are not plotted properly due the low throughput.

In case we found some we will retake the topic.

```

### KSQL Incident

```ad-warning

**We are not upgrading KSQL working clusters until issue is indentified**

```

```ad-summary
title: timeline

- We saw lags again on GDO applications
- Improve monitoring to have more visibility on the issue
- Infra process that reduced the amount of memory used for KSQL clusters on DEV and PRE. **There is not enough resources for queries running there**
- Resources back to normal, but still not enough resources for workload. There is a huge difference beetween PRE and PROD env.
- After the upgrade we saw a constant usage of 100% of CPU on KSQL servers. Incident case opened: [#100610](https://confluent.zendesk.com/agent/tickets/100610) 

```

```ad-important
title: Next Steps

- [ ] We think that is a issue of resources vs workload over a matter of ksql version. To be sure is not before upgrade want to cleanup the cluster of unnecesary workload and create the same queries with we are working on PROD env, expecting to have the same performance in both environments.
- [ ] **Use KSQL Bound Memory setter for RocksDB** to have a more deterministic use of resources
- [ ] In order to have better troubleshooting tools we want to install [[#^ff7591|Kafka Lag Exporter]] as part of Control Center Instance.

```

### Hive JDBC Connector

```ad-summary
title: Origin error with Custom Connector

1. Error with custom connector on top JDBC one trying to connect to *Hive* with Kerberos Auth. Is not enough to provide the configuration for kerberos, the Hive Driver needs that some kind of client do the auth to kerberos before authenticate to the database 
```

```ad-error
Caused by: java.sql.SQLException: [Cloudera][HiveJDBCDriver](500166) Error creating login context using JAAS configuration: No LoginModules configured for Client.

at com.cloudera.hiveserver2.hivecommon.api.HiveServer2ClientFactory.createTransport(Unknown Source)

at com.cloudera.hiveserver2.hivecommon.api.ServiceDiscoveryFactory.createClient(Unknown Source)

at com.cloudera.hiveserver2.hivecommon.core.HiveJDBCCommonConnection.establishConnection(Unknown Source)

 at com.cloudera.hiveserver2.jdbc.core.LoginTimeoutConnection$1.call(Unknown Source)

 at com.cloudera.hiveserver2.jdbc.core.LoginTimeoutConnection$1.call(Unknown Source)

 at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)

 at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)

 at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)

Caused by: com.cloudera.hiveserver2.support.exceptions.GeneralException: [Cloudera][HiveJDBCDriver](500166) Error creating login context using JAAS configuration: No LoginModules configured for Cli

```

```ad-note
title: Confluent Licensed JDBC Connector

1. Error getting the kerberos config, no kinit so we can't authenticate on dabase
2. All point to a hive driver limitation. Support ticket open: [#99414](https://confluent.zendesk.com/agent/tickets/99414)
3. In the mean time two options:
	1. Develop custom connector (some genral guidance on the how provided by mail)
	2. Develop microservice that read from hive and produce to a topic

```
```ad-error

{

 "name": "SANES.PANORA.JDBCSOURCE.DLGE_ARQTEC_ARQTEC_ATLAS_ORBIS-ASSEMBLY",

 "connector": {

 "state": "FAILED",

 "worker_id": "sanlcckfcod0001.santander.dev.corp:8083",

 "trace": "org.apache.kafka.connect.errors.ConnectException: java.sql.SQLException: [Cloudera][HiveJDBCDriver](500205) Error analysis the kerberos service string.\n\tat io.confluent.connect.jdbc.util.CachedConnectionProvider.getConnection(CachedConnectionProvider.java:59)\n\tat io.confluent.connect.jdbc.JdbcSourceConnector.start(JdbcSourceConnector.java:92)\n\tat org.apache.kafka.connect.runtime.WorkerConnector.doStart(WorkerConnector.java:185)\n\tat org.apache.kafka.connect.runtime.WorkerConnector.start(WorkerConnector.java:210)\n\tat org.apache.kafka.connect.runtime.WorkerConnector.doTransitionTo(WorkerConnector.java:349)\n\tat org.apache.kafka.connect.runtime.WorkerConnector.doTransitionTo(WorkerConnector.java:332)\n\tat org.apache.kafka.connect.runtime.WorkerConnector.doRun(WorkerConnector.java:141)\n\tat org.apache.kafka.connect.runtime.WorkerConnector.run(WorkerConnector.java:118)\n\tat java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:515)\n\tat java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)\n\tat java.base/java.lang.Thread.run(Thread.java:829)\nCaused by: java.sql.SQLException: [Cloudera][HiveJDBCDriver](500205) Error analysis the kerberos service string.\n\tat com.cloudera.hiveserver2.hivecommon.api.HiveServer2ClientFactory.createTransport(Unknown Source)\n\tat com.cloudera.hiveserver2.hivecommon.api.ServiceDiscoveryFactory.createClient(Unknown Source)\n\tat com.cloudera.hiveserver2.hivecommon.core.HiveJDBCCommonConnection.establishConnection(Unknown Source)\n\tat com.cloudera.hiveserver2.jdbc.core.LoginTimeoutConnection$1.call(Unknown Source)\n\tat com.cloudera.hiveserver2.jdbc.core.LoginTimeoutConnection$1.call(Unknown Source)\n\tat java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)\nCaused by: com.cloudera.hiveserver2.support.exceptions.GeneralException: [Cloudera][HiveJDBCDriver](500205) Error analysis the kerberos service string.\n\t... 8 more\n"

    },

    "tasks": [],

    "type": "source"

}

```

```ad-question
title: Next Steps

Panoramix Team will go to develop **Microservice option**. 

Will ask for support if needed

```

### Functional Testing

```ad-success

First version of [[#^6f2ee3|Kafka Test Utils library]] delivered on github. That provides:

- Test Producer
- Text File Test Producer
- Test Consumer
- Test Admin Client (create, describe, delete topics)
- Configuration Creator from properties file

Check integration wit Cylantrum -> Tested locally by Jose Alberto Rollon

Check with ADN 360 Team (José Manuel Pellejero) the use of it inside a real project.

```

```ad-question
title: Next Steps

- [ ] Deploy Library to Nexus -> Jose Alberto Rollon
- [ ] Test Example on Laika -> Jose Alberto Rollon
- [ ] Deploy use case with ADN 360 -> Jose Manuel Pellejero, Oscar Gómez, Jose Alberto Rollon -> booked to next monday's afternoon
- [ ] Create a in memory server for testing purpose -> Oscar Gómez
- [ ] Improve library with Perf Test producers/consumers
- [ ] Provide Use example Project -> Oscar Gómez

```

## Open Questions

- [x] Complete information and review together the GDO KSQL incident report
- [x] Document Backup plan for KSQL upgrade
- [ ] Provide example repo as recap of [Julie Ops Examples](app://obsidian.md/index.html#^a3fd2f) for the min operations requested
- [ ] how to intercept Connectors classes to trace it with Dynatrace


## References 
- [what contributes to CPU usage of ksqlDB process](https://support.confluent.io/hc/en-us/articles/360056329831-What-contributes-to-the-CPU-usage-of-a-ksqlDB-process)
- [Kafka Lag Exporter]([https://github.com/seglo/kafka-lag-exporter](https://github.com/seglo/kafka-lag-exporter)) ^ff7591
- [Kafka Test Utils Repo](https://github.alm.europe.cloudcenter.corp/sanes-pruebas-pre/kafka-test-utils) ^6f2ee3