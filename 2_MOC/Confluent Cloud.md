#ccloud 

# Confluent Cloud

## Constructs

### Organization
* Manage Billing ^9571a5
* Support Suscription Level ^5b9cbe
* SSO
* Manage User and Service Accounts
### Environments
- Logical Separation for cluster. Based on:
	- App lifecycle (Dev, UAT, PROD)
	- Region
	- Cloud Provider
	- Line of Business
### Cluster
- Topics
- Managed Connectors
- KsqlDB Instances
- Monitor Consumer groups
- Deploy ACLs & API Keys
#### Cluster Types:
##### Basic
- Multi-Tenant shared resources
- Per Partition cost
- Single-Zone Availability 
- **Uptime SLA no suitable or PROD** #sla-Numbers??
##### Standard
- Multi-Tenant shared resources
- Per Partition cost
- **Multi-Zone Availability**
- **Custom Networking Types**
- #ccloud-sla Numbers??
##### Dedicated
- Dedicated resources
- Multi-Zone Availability
- Custom Networking
- Robust SLA time #ccloud-sla
- Scale with CKUs(Kafka Units), scale realtime: #CKU
	- Throughput
	- Number of Partitions
	- ***NO POSIBLE TO SCALE DOWN AT THIS MOMENT***
- ##### CKUs numbers:
> 	50 MBps Ingress
> 	150 MBps Egress
> 	10 TB Storage
> 	4.5k Partitions
> 	3k Connections
> 	80 cps (Connections per Second)
> 	15k rpc (Request Per Second)
 ![[Pasted image 20220207124822.png]]
![[Pasted image 20220207124949.png]]

### SSO
- Integrate with client IDP
- **Local User**: User stored on CP
	- Recommended has at least 1 local user for troubleshooting and DR cases
- **SSO User**: User stored on client IDP
![[Pasted image 20220207115247.png]]
- ***RBAC is managed on CCloud UI whatever the login method is***
### RBAC
#### Roles
- **Organization Admin**
- **Environment Admin**
- **Cluster Admin**
*Role assignment gives user full access to the resource*
****Note: No granular support for Topics, Individual Connectors, KSQL Apps, Schema Subjects***

### Topics
> ***Naming Recommendation***
> Prefix topic with app name and data classification in order to facilitate ACLS

> ***CCloud default message size 2MB***

### Full Managed Connect
> Any External System need to be attached to the CCLoud Network (VPC peering, etc)
Self Managed Connector is an option for not fully managed connector or Networking issues.

### KSQL
> Directly related yo the CCloud network so **only will be accesible for peered Networks**

Only produces/consumes on the cluster within is deployed
ACLs Required:
- ???

### Networking
#### Demo
pkac-p9k7k.eu-west-1.aws.confluent.cloud

#### UI
- private endpoints no plubicly
- Reverse Proxy:
	- Configure Internal access to the componentes
	- [doc](https://docs.confluent.io/cloud/current/networking/peering/peering-ui-access.html)
	- ![[Pasted image 20220207134223.png]]
	- Proxy supports hostname based routing located on your peered VPC. Point to the Admin client address showed on the UI error.
	- To solve you can:
		- Edit host file (temporary)
		- Use AWS Route53 DNS or equivalent
### Monitoring
#### Out of the box:
- ##### Dataflow UI:
	- **Producers**: Production Metrics based on ClientId
	- **Topics**: Drill down to individual partitions
	- **Consumers**: Individual Consumers view
- Which Metrics??, Are Enough??
#### Metrics API
- Give access to Confluent Resource Metrics to integrate it with third-party tools.
- Public access using https (Cluster, Topic, Partition Levels aggregation)
- ![[Pasted image 20220207150543.png]]
- [Metrics APIdocs](https://docs.confluent.io/home/overview.html)
- [Api Specification](https://api.telemetry.confluent.cloud/docs)
- **Granularity**: ???
- **Abstraction Level**: ??
- Specific API Key Secret Pair needed for the cluster resource.
	- `ccloud api-key create --recource cloud`
### CCloud Schema Registry
- Shared Schema Registry per Environment
- Single endpoint for all cluster on the environment
> **1000 distinct schemas restriction. Only can be increased on the provisioning. Increase it is a destructive action**
- Separate API key/secret to access. ==***Same for all clusters in the environment NO RBAC or ACLS for Schemas***==
- Basic Auth over TLS (credential source + producer/consumer credentials)
#### Subject Naming Strategy
- **Topic Naming Strategy**: ==Use this to leverage UI to do Schema Management==
- **Record Naming Strategy**:
- **Topic Record Naming Strategy**:

### Security

### Bring your own key (BYOK)
> CCloud encrypts by default so if you don't use it your data still encrypted

**Keys**:
- Allows users to revoke access in any time
- Secures data on a called storage drive
- Compliance
**Limitations**:
- Still Allows access to Confluent and your Cloud Provider to access the data. **If you want to limit this access E2E Encryption will be needed**
- Only can be implemented on cluster creation time
- Only for Dedicated Cluster
- No manual rotation supported, automatic rotation must be configured on your KMS
- AWS y GCP only for the moment, Azure is coming
**Basic Steps**
1. Create Dedicated Cluster, **key must be placed on the same region**
2. Provide Key Resource Id
3. Update Security Policy to grant CCloud Access
#### BYOK AWS Confluent Console
1. Create Dedicated Cluster
2. Network
3. Self Managed, provide Key Id
	1. Go to AWS KMS
	2. Customer Managed Key
		1. Be sure that is a Symmetric Key
		2. Click on the id and copy the Amazon Resource Name
4. Copy the code from Step 2 on Key policies on AWS Key Manage Service Console
	1. To remove access, you can:
		1. Remove they key
		2. Remove the policy
		3. ==WARNING - Cluster will fail and data will be gone for ever==
#### BYOK AWS Confluent CLI
1. 
```bash
confluent kafka cluster create --cloud "aws --region "us-east-2" --type "dedicated" --encrypt <aws-key-resource-name> --cku <n>
```
2. Paste the statements on AWS Key Management Console
3. Confirm you allow the access
#### BYOK GCP Confluent Console
1. Create Dedicated Cluster
2. Network
3. Self Managed:
	1. Provide Google Cloud KMS
		1. Go to key ring of your region
		2. copy resource name from actions
4. Create Custom Role with the permissions on Step 2
	1. click on the key
	2. add member
	3. managed roles
	4. create role
	5. add the proper permissions
		1. select kms and mark Cloud KMS Admin and Cloud KMS Crypto Key Encrypter/Decrypter
		2. Select the permissions CCloud Console show us.
	6. Add confluent Cloud service account to GCP 
		1. Select the key
		2. add member, paste the Ccloud Service account copied from console
	7. Create Cluster
> 		Options marked with * cannot be changed after creation
#### BYOK GCP Confluent CLI
1
