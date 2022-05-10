--- 
### 12:04

### Status: #fleeting_note
### Tags:
[[julie_ops]] [[devops]] [[gitops]]
### References
[[Julie Ops]] [[Julie Ops Playground]] 
### Notes:

#todo 
- [ ] Merge the example on [[Julie Ops Playground]]

### 2022-05-09-13:38 Setup Environment, Topics & KSQL

---

tags:
[[julie_ops]]
team:
[[Santander]]
---

### Notes

Docker not working on [[Julie Ops Playground]] so I deleted all the environment now usin the [[Kafka Docker Playground]] forked from [[Vincent de Saboulin]]

Added apl_testJulie to ldap_users
> [!success] RBAC Cluster
> look at [[Kafka Docker Playground]] for docker config setup for RBAC with LDAP listener callback
> Julie Configuration for RBAC with LDAP and SR

> [!warning]
> Not posible have more than a file with topic config under the same folder, julie error merging existing project on subtopologies ASK PERE
> Consumer Group permission not deleted even with `allow.delete.bindings=true` set in topology builder properties

> [!error] KSQL Cluster Error
> java.io.IOException: java.io.IOException: Something happened with the connection, response status code: 400 body: {"status_code":400,"message":"Invalid Scope : cluster value cannot be blank","type":"INVALID REQUEST DATA"}
	at com.purbon.kafka.topology.clients.JulieHttpClient.doRequest(JulieHttpClient.java:249) ~[julie-ops.jar:?]
	at com.purbon.kafka.topology.clients.JulieHttpClient.doPost(JulieHttpClient.java:172) ~[julie-ops.jar:?]
	at com.purbon.kafka.topology.api.mds.MDSApiClient.bindRequest(MDSApiClient.java:163) ~[julie-ops.jar:?]
	at com.purbon.kafka.topology.roles.RBACProvider.createBindings(RBACProvider.java:30) ~[julie-ops.jar:?]
	at com.purbon.kafka.topology.actions.access.CreateBindings.execute(CreateBindings.java:28) ~[julie-ops.jar:?]
	at com.purbon.kafka.topology.actions.BaseAccessControlAction.run(BaseAccessControlAction.java:33) ~[julie-ops.jar:?]
	at com.purbon.kafka.topology.ExecutionPlan.execute(ExecutionPlan.java:124) ~[julie-ops.jar:?]
	at com.purbon.kafka.topology.ExecutionPlan.run(ExecutionPlan.java:101) [julie-ops.jar:?]
	at com.purbon.kafka.topology.JulieOps.run(JulieOps.java:212) [julie-ops.jar:?]
	at com.purbon.kafka.topology.JulieOps.run(JulieOps.java:227) [julie-ops.jar:?]
	at com.purbon.kafka.topology.CommandLineInterface.processTopology(CommandLineInterface.java:212) [julie-ops.jar:?]
	at com.purbon.kafka.topology.CommandLineInterface.run(CommandLineInterface.java:161) [julie-ops.jar:?]
	at com.purbon.kafka.topology.CommandLineInterface.main(CommandLineInterface.java:147) [julie-ops.jar:?]
Caused by: java.io.IOException: Something happened with the connection, response status code: 400 body: {"status_code":400,"message":"Invalid Scope : cluster value cannot be blank","type":"INVALID REQUEST DATA"}
	at com.purbon.kafka.topology.clients.JulieHttpClient.doRequest(JulieHttpClient.java:238) ~[julie-ops.jar:?]
	... 12 more

#todo 
- [ ] Pull request to [[Vincent de Saboulin]] for the LDAP callback
- [ ] Ask [[Pere Urbon]] how we can have more than a file for topic definiton on the same project




### 2022-05-10-12:14 Connector

---

tags:
team:

---

### Notes


---


---