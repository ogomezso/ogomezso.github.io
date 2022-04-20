![[zzzz_Attachments/confluent_logo.png]]
# ADIB  2022-02-04 Oscar Gómez RSA Weekly Report

##### Status: #report
##### Tags: [[ADIB]] [[CP 7.0]] [[secret_protection]] [[cp_ansible]]

# Weekly Recap

### 2022-02-02
#### Pro Environment Deployment
- ADIB need to have the secrets encrypted both on the YAML script and on the component file system itself. 
- We are using [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) to encrypt passwords.
- **mTLS deployment succeed**.
	- Inter-broker communication secured over mTLS
	- Component-Broker Communication secured over mTLS.
- Waiting for LDAP Passwords
- We got errors trying to put secret protection in place [secret protection](https://docs.confluent.io/ansible/current/ansible-configure.html#configure-secrets-protection) . **Sunil will raise a ticket**.
	- Problems with Confluent Client login on the brokers that avoid ansible to encrypt the properties properly
	- Default integration failing, Ansible is not able to create master key and security properties.

### 2022-02-03
#### Pro Environment Deployment
- Upgrade to CP 7.0.1 succeed
- We still not able to put secret protection in place<
- Continue working on the secret protection strategy:
	- Hashicorp Vault integration will be put in place in the future, [hashi-vault plugin](https://docs.ansible.com/ansible/2.9/plugins/lookup/hashi_vault.html) suggested
	- For the moment local encryption with Ansible Vault will be enough. We need to put Secret Protection in place. 
- LDAP Passwords provided by the end of the day.
	- LDAP Configuration put in place
- RBAC Configuration, failing with the authorization for SR
	- SSL principal and super users config with intermittent errors
#### Dev Environment Deployment
* Upgrade to CP 7.0.1 succeed.
### 2022-02-04
#### Pro Deployment
Continue with the RBAC Deployment:
- SSL principal extract REGEX provided
Discover that the error was **connectivity and DNS resolution error on LDAP Servers

After checking again all the config we agreed that the configuration provided on the host.yaml is correct for this deployment. Have to try the deployment again when the LDAP connectivity issue is fixed.

# Work Notes

### Secret Encryption
We need to use the same vault password for all secrets in order to be able to decrypt it in unattended mode.

*Command to encrypt a secret:*
```bash
 ansible-vault encrypt_script <secret>
``` 
*Output:*
```
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          37303732663733633061656535633561666235363138336664333331626562313133356437663733
          6332663134646535326265623662613232396666636636330a656639396436343938383363616537
          62376566396166646463623336656136633930306363653365323730366134353332353265633663
          3162623862643762350a303832346331336637356566363334656461303835653035646336643733
          3265
```
**Note: It's important to maintain the indentation, if not it will fail**

*Run playbook asking for vault password*
```bash
ansible-playbook -i host.yml --ask-vault-password
```

### KSQL
Checking state we face this problem connecting to the server from one of KSQL nodes:
```log
[2022-02-02 15:02:35,039] ERROR Unhandled exception (io.confluent.ksql.api.server.ServerVerticle:376)
io.netty.handler.ssl.NotSslRecordException: not an SSL/TLS record: 505249202a20485454502f322e300d0a0d0a534d0d0a0d0a0000240400000000000001000010000002000000000003000000640004001000000005000040000006000020000000040800000000007fff0000
        at io.netty.handler.ssl.SslHandler.decodeJdkCompatible(SslHandler.java:1213)
        at io.netty.handler.ssl.SslHandler.decode(SslHandler.java:1280)
        at io.netty.handler.codec.ByteToMessageDecoder.decodeRemovalReentryProtection(ByteToMessageDecoder.java:507)
        at io.netty.handler.codec.ByteToMessageDecoder.callDecode(ByteToMessageDecoder.java:446)
        at io.netty.handler.codec.ByteToMessageDecoder.channelRead(ByteToMessageDecoder.java:276)
        at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:379)
        at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:365)
        at io.netty.channel.AbstractChannelHandlerContext.fireChannelRead(AbstractChannelHandlerContext.java:357)
        at io.netty.channel.DefaultChannelPipeline$HeadContext.channelRead(DefaultChannelPipeline.java:1410)
        at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:379)
        at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:365)
        at io.netty.channel.DefaultChannelPipeline.fireChannelRead(DefaultChannelPipeline.java:919)
        at io.netty.channel.nio.AbstractNioByteChannel$NioByteUnsafe.read(AbstractNioByteChannel.java:166)
        at io.netty.channel.nio.NioEventLoop.processSelectedKey(NioEventLoop.java:719)
        at io.netty.channel.nio.NioEventLoop.processSelectedKeysOptimized(NioEventLoop.java:655)
        at io.netty.channel.nio.NioEventLoop.processSelectedKeys(NioEventLoop.java:581)
        at io.netty.channel.nio.NioEventLoop.run(NioEventLoop.java:493)
        at io.netty.util.concurrent.SingleThreadEventExecutor$4.run(SingleThreadEventExecutor.java:989)
        at io.netty.util.internal.ThreadExecutorMap$2.run(ThreadExecutorMap.java:74)
        at io.netty.util.concurrent.FastThreadLocalRunnable.run(FastThreadLocalRunnable.java:30)
        at java.base/java.lang.Thread.run(Thread.java:829)
[2022-02-02 15:03:06,760] ERROR Unhandled exception (io.confluent.ksql.api.server.ServerVerticle:376)
io.netty.handler.ssl.NotSslRecordException: not an SSL/TLS record: 505249202a20485454502f322e300d0a0d0a534d0d0a0d0a0000240400000000000001000010000002000000000003000000640004001000000005000040000006000020000000040800000000007fff0000
        at io.netty.handler.ssl.SslHandler.decodeJdkCompatible(SslHandler.java:1213)
        at io.netty.handler.ssl.SslHandler.decode(SslHandler.java:1280)
        at io.netty.handler.codec.ByteToMessageDecoder.decodeRemovalReentryProtection(ByteToMessageDecoder.java:507)
```
We can decrypt the record: 
````bash
printf '505249202a20485454502f322e300d0a0d0a534d0d0a0d0a0000240400000000000001000010000002000000000003000000640004001000000005000040000006000020000000040800000000007fff0000' | xxd -r -p | xxd
````

```` text
00000000: 5052 4920 2a20 4854 5450 2f32 2e30 0d0a  PRI * HTTP/2.0..
00000010: 0d0a 534d 0d0a 0d0a 0000 2404 0000 0000  ..SM......$.....
00000020: 0000 0100 0010 0000 0200 0000 0000 0300  ................
00000030: 0000 6400 0400 1000 0000 0500 0040 0000  ..d..........@..
00000040: 0600 0020 0000 0004 0800 0000 0000 7fff  ... ............
00000050: 0000
````

Seems to be a dns solve issue: we are trying to connect to the other node, connecting to the same node the problem is solved.
*Command to connect to the server solving the log issue*
````bash
KSQL_OPTS="-Djava.io.tmpdir=/some/writable/directory -Dlog4j.configuration=file:/path/to/log4j-file.properties" ksql --config-file ssl-config.properties  https://localhost:8088 -Dlog4j.configuration=file:/Users/mikebin/cp/confluent-7.0.1/etc/ksqldb/log4j.properties
````

### Connect

After changing connect user can't reboot due folder permission are granted to the old user so we reset machine manually deleting the folders

```bash
Wednesday 02 February 2022 19:51:07 +0400 (0:00:00.169) 0:01:28.640 ****
fatal: [lpp2azapkfb1.adib.co.ae]: FAILED! => {"changed": true, "cmd": "/usr/local/bin/confluent secret file encrypt --config-file /opt/confluent/etc/kafka/server.properties --local-secrets-file /var/ssl/private/kafka-broker-security.properties --remote-secrets-file /var/ssl/private/kafka-broker-security.properties\n", "delta": "0:00:00.041102", "end": "2022-02-02 19:51:08.127196", "msg": "non-zero return code", "rc": 1, "start": "2022-02-02 19:51:08.086094", "stderr": "Error: you must log in to Confluent Platform to use this command\n\nSuggestions:\n Log in with \"confluent login --url <mds-url>\".", "stderr_lines": ["Error: you must log in to Confluent Platform to use this command", "", "Suggestions:", " Log in with \"confluent login --url <mds-url>\"."], "stdout": "", "stdout_lines": []}
fatal: [lpp1apkfb2.adib.co.ae]: FAILED! => {"changed": true, "cmd": "/usr/local/bin/confluent secret file encrypt --config-file /opt/confluent/etc/kafka/server.properties --local-secrets-file /var/ssl/private/kafka-broker-security.properties --remote-secrets-file /var/ssl/private/kafka-broker-security.properties\n", "delta": "0:00:00.049201", "end": "2022-02-02 19:51:08.151917", "msg": "non-zero return code", "rc": 1, "start": "2022-02-02 19:51:08.102716", "stderr": "Error: you must log in to Confluent Platform to use this command\n\nSuggestions:\n Log in with \"confluent login --url <mds-url>\".", "stderr_lines": ["Error: you must log in to Confluent Platform to use this command", "", "Suggestions:", " Log in with \"confluent login --url <mds-url>\"."], "stdout": "", "stdout_lines": []}
fatal: [lpp2azapkfb2.adib.co.ae]: FAILED! => {"changed": true, "cmd": "/usr/local/bin/confluent secret file encrypt --config-file /opt/confluent/etc/kafka/server.properties --local-secrets-file /var/ssl/private/kafka-broker-security.properties --remote-secrets-file /var/ssl/private/kafka-broker-security.properties\n", "delta": "0:00:00.040704", "end": "2022-02-02 19:51:08.170609", "msg": "non-zero return code", "rc": 1, "start": "2022-02-02 19:51:08.129905", "stderr": "Error: you must log in to Confluent Platform to use this command\n\nSuggestions:\n Log in with \"confluent login --url <mds-url>\".", "stderr_lines": ["Error: you must log in to Confluent Platform to use this command", "", "Suggestions:", " Log in with \"confluent login --url <mds-url>\"."], "stdout": "", "stdout_lines": []}
```

### Control Center

- Control center can't configure the Connect cluster,  Control center has not connectivity with one of the workers that unfortunately was the leader. **ADIB need to check this errors**
- **As workaround** we stop the no connected worker, the other become leader and we C3 can register the connect cluster

### RBAC

RBAC Enabled, and extra LDAP Users configured. 
````yml
mds_super_user: srv-kafkaprd

mds_super_user_password: password

#

kafka_broker_ldap_user: srv-kafkaprd

kafka_broker_ldap_password:

#

schema_registry_ldap_user: srv-kafkaprd

schema_registry_ldap_password:

#

kafka_connect_ldap_user: srv-kafkaprd

kafka_connect_ldap_password:

#

ksql_ldap_user: srv-kafkaprd

ksql_ldap_password:

#

kafka_rest_ldap_user: srv-kafkaprd

kafka_rest_ldap_password:

#

control_center_ldap_user: srv-kafkaprd

control_center_ldap_password:
````
*I think `mds-super_user`  is the only one really needed since we will communicate through mTLS, so the principal will be extracted from the CN of the certificate*

With this config we got this error:

````text
ASK [confluent.kafka_broker : Grant role System Admin to Additional Kafka Broker System Admins] ************************************************************
Thursday 03 February 2022 21:47:08 +0400 (0:00:01.273) 0:04:23.839 *****
failed: [lpp2azapkfb1.adib.co.ae] (item=srv-kafkaprd) => {"ansible_loop_var": "item", "changed": false, "content": "", "elapsed": 30, "item": "srv-kafkaprd", "msg": "Status code was -1 and not [204]: Connection failure: ('The read operation timed out',)", "redirected": false, "status": -1, "url": "https://lpp2azapkfb1.adib.co.ae:8090/security/1.0/principals/User:srv-kafkaprd/roles/SystemAdmin"}
failed: [lpp1apkfb2.adib.co.ae] (item=srv-kafkaprd) => {"ansible_loop_var": "item", "changed": false, "content": "", "elapsed": 30, "item": "srv-kafkaprd", "msg": "Status code was -1 and not [204]: Connection failure: ('The read operation timed out',)", "redirected": false, "status": -1, "url": "https://lpp2azapkfb1.adib.co.ae:8090/security/1.0/principals/User:srv-kafkaprd/roles/SystemAdmin"}
failed: [lpp2azapkfb2.adib.co.ae] (item=srv-kafkaprd) => {"ansible_loop_var": "item", "changed": false, "content": "", "elapsed": 30, "item": "srv-kafkaprd", "msg": "Status code was -1 and not [204]: Connection failure: ('The read operation timed out',)", "redirected": false, "status": -1, "url": "https://lpp2azapkfb1.adib.co.ae:8090/security/1.0/principals/User:srv-kafkaprd/roles/SystemAdmin"}
failed: [lpp1azapkfb2.adib.co.ae] (item=srv-kafkaprd) => {"ansible_loop_var": "item", "changed": false, "content": "", "elapsed": 30, "item": "srv-kafkaprd", "msg": "Status code was -1 and not [204]: Connection failure: ('The read operation timed out',)", "redirected": false, "status": -1, "url": "https://lpp2azapkfb1.adib.co.ae:8090/security/1.0/principals/User:srv-kafkaprd/roles/SystemAdmin"}
failed: [lpp1azapkfb1.adib.co.ae] (item=srv-kafkaprd) => {"ansible_loop_var": "item", "changed": false, "content": "", "elapsed": 30, "item": "srv-kafkaprd", "msg": "Status code was -1 and not [204]: Connection failure: ('The read operation timed out',)", "redirected": false, "status": -1, "url": "https://lpp2azapkfb1.adib.co.ae:8090/security/1.0/principals/User:srv-kafkaprd/roles/SystemAdmin"}
failed: [lpp1apkfb1.adib.co.ae] (item=srv-kafkaprd) => {"ansible_loop_var": "item", "changed": false, "content": "", "elapsed": 30, "item": "srv-kafkaprd", "msg": "Status code was -1 and not [204]: Connection failure: ('The read operation timed out',)", "redirected": false, "status": -1, "url": "https://lpp2azapkfb1.adib.co.ae:8090/security/1.0/principals/User:srv-kafkaprd/roles/SystemAdmin"}
````

We solved with this config:

````yml
super.users: User:kafkaprd;User:dev-kafka.devdc.adib.local
````

But the problem comes again when trying to deploy Schema Registry:
````
2022-02-03 18:57:16,014] INFO [Schema registry clientId=sr-1, groupId=schema-registry] Discovered group coordinator lpp1azapkfb1.adib.co.ae:9092 (id: 2147483647 rack: null) (io.confluent.kafka.schemaregistry.leaderelector.kafka.SchemaRegistryCoordinator)
[2022-02-03 18:57:16,014] INFO [Schema registry clientId=sr-1, groupId=schema-registry] Group coordinator lpp1azapkfb1.adib.co.ae:9092 (id: 2147483647 rack: null) is unavailable or invalid due to cause: coordinator unavailable.isDisconnected: false. Rediscovery will be attempted. (io.confluent.kafka.schemaregistry.leaderelector.kafka.SchemaRegistryCoordinator)
[2022-02-03 18:57:16,118] INFO [Schema registry clientId=sr-1, groupId=schema-registry] Discovered group coordinator lpp1azapkfb1.adib.co.ae:9092 (id: 2147483647 rack: null) (io.confluent.kafka.schemaregistry.leaderelector.kafka.SchemaRegistryCoordinator)
[2022-02-03 18:57:21,870] INFO [Schema registry clientId=sr-1, groupId=schema-registry] Attempt to heartbeat failed since coordinator lpp1azapkfb1.adib.co.ae:9092 (id: 2147483647 rack: null) is either not started or not valid (io.confluent.kafka.schemaregistry.leaderelector.kafka.SchemaRegistryCoordinator)
[2022-02-03 18:57:21,870] INFO [Schema registry clientId=sr-1, groupId=schema-registry] Group coordinator lpp1azapkfb1.adib.co.ae:9092 (id: 2147483647 rack: null) is unavailable or invalid due to cause: error response NOT_COORDINATOR.isDisconnected: false. Rediscovery will be attempted. (io.confluent.kafka.schemaregistry.leaderelector.kafka.SchemaRegistryCoordinator)
[2022-02-03 18:57:23,815] INFO [Schema registry clientId=sr-1, groupId=schema-registry] Discovered group coordinator lpp2azapkfb2.adib.co.ae:9092 (id: 2147483644 rack: null) (io.confluent.kafka.schemaregistry.leaderelector.kafka.SchemaRegistryCoordinator)
[2022-02-03 18:57:23,913] INFO [Schema registry clientId=sr-1, groupId=schema-registry] Attempt to heartbeat failed since coordinator lpp2azapkfb2.adib.co.ae:9092 (id: 2147483644 rack: null) is either not started or not valid (io.confluent.kafka.schemaregistry.leaderelector.kafka.SchemaRegistryCoordinator)
[2022-02-03 18:57:23,914] INFO [Schema registry clientId=sr-1, groupId=schema-registry] Group coordinator lpp2azapkfb2.adib.co.ae:9092 (id: 2147483644 rack: null) is unavailable or invalid due to cause: error response NOT_COORDINATOR.isDisconnected: false. Rediscovery will be attempted. (io.confluent.kafka.schemaregistry.leaderelector.kafka.SchemaRegistryCoordinator)
````

The seems to be with the authorization in MDS, we try to solve with the `ssl.principal.mapping.rules` with weird behaviour, sometimes seems to works and not another.

Checking *MDS logs* found this error:

````LOG
015-[2022-02-04 17:30:27,346] ERROR Failed to obtain user DN for srv-kafkaprd. javax.naming.ldap.InitialLdapContext@56bb9d93 (io.confluent.security.auth.provider.ldap.LdapAuthenticateCallbackHandler)
4016:javax.naming.PartialResultException [Root exception is javax.naming.CommunicationException: adib.co.ae:389 [Root exception is java.net.SocketTimeoutException: connect timed out]]
4017- at java.naming/com.sun.jndi.ldap.AbstractLdapNamingEnumeration.hasMoreImpl(AbstractLdapNamingEnumeration.java:237)
4018- at java.naming/com.sun.jndi.ldap.AbstractLdapNamingEnumeration.hasMoreReferrals(AbstractLdapNamingEnumeration.java:347)
4019- at java.naming/com.sun.jndi.ldap.AbstractLdapNamingEnumeration.hasMoreImpl(AbstractLdapNamingEnumeration.java:227)
4020- at java.naming/com.sun.jndi.ldap.AbstractLdapNamingEnumeration.hasMoreReferrals(AbstractLdapNamingEnumeration.java:347)
4021- at java.naming/com.sun.jndi.ldap.AbstractLdapNamingEnumeration.hasMoreImpl(AbstractLdapNamingEnumeration.java:227)
--
4060- at org.eclipse.jetty.io.FillInterest.fillable(FillInterest.java:105)
4061- at org.eclipse.jetty.io.ssl.SslConnection$1.run(SslConnection.java:149)
4062- at org.eclipse.jetty.util.thread.QueuedThreadPool.runJob(QueuedThreadPool.java:883)
4063- at org.eclipse.jetty.util.thread.QueuedThreadPool$Runner.run(QueuedThreadPool.java:1034)
4064- at java.base/java.lang.Thread.run(Thread.java:829)
4065:Caused by: javax.naming.CommunicationException: adib.co.ae:389 [Root exception is java.net.SocketTimeoutException: connect timed out]
4066- at java.naming/com.sun.jndi.ldap.LdapReferralContext.<init>(LdapReferralContext.java:96)
4067- at java.naming/com.sun.jndi.ldap.LdapReferralException.getReferralContext(LdapReferralException.java:151)
4068- at java.naming/com.sun.jndi.ldap.AbstractLdapNamingEnumeration.hasMoreReferrals(AbstractLdapNamingEnumeration.java:325)
4069- at java.naming/com.sun.jndi.ldap.AbstractLdapNamingEnumeration.hasMoreImpl(AbstractLdapNamingEnumeration.java:227

````

That means that we are not being able to connect to the LDAP server.

Important points:
- In the log we can see that is trying to connect to `adib.co.ae:389` in fact we are configuring the ip
- Some DNS solving stuff is in place
- If we try a `nslookup` to this dns multiple ips are configured but not all are working so this can be the problem behind the intermittent timeouts on the LDAP Connection.
- We try to workaround this problem adding some retries to the RBAC task on the Schema Registry role of cp-ansible with the same result 

Once this problem is solved we can try the deployment again with this config:
* First to all we can try just adding all the LDAP users and try with any additional config.
* Add just the `ssl.principal.mapping.rules`:
````yml
ssl.principal.mapping-rules: "CN=(.*), O=Abu Dhabi Islamic Bank PJSC, L=Abu Dhabi, ST=UAE, C=AE.*$/$1/,DEFAULT"
````
* Add super users explicitly:
````yaml
super.users=User:srv-kafkaprd;User:CN=prd-broker.adib.co.ae
````

* Try once on a time, the first should work, but we have to try. 
* Once this is working try to remove all LDAP users except the mds-super-user. I think is the only really needed right now. 
