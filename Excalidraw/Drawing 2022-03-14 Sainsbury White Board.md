---

excalidraw-plugin: parsed

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
Agenda ^PzeiHOFb

Replicated topics Deletion

Grouping Topics Strategy

Debugging Scenarios

Centralized Monitoring

Cleanup Policy Delete and Compact 
on the same topic
 ^3ZuGZCNP

Origin Cluster ^LJXXar06

Dest Cluster ^4CyluN8z

Replicator ^5k3AiSY0

Topic 1 ^anwEjRUf

Topic 2 ^ZXzrnWwo

Replicator ^G5jUdkKH

config.sync=enabled ^bGPIPrWZ

config.sync=disabled ^U3eUUgQD

retention.ms=0 ^sgoyH63W

Topic 1 ^oSZ0kAbB

retention.ms=0 ^wfddOSQd

retention.ms=0 ^SoZh7Cmn

retention.ms=0 ^YD3fFqRI

Topic 2 ^bvPRMILq

Segment 1 ^xqIhdcOt

segment 2 ^CY5gjQGt

Segment 3 ^7sc5ArIl

retention.ms=0
retention.bytes=0 ^GkooDbLZ

SO ^FMYnAWbg

Flush segments ^2bwZRW8f

delete
data ^LBhtXeRp

GROUPING ^3WmrKgEj

DELETION OF REPLICATED ^QIJ0e9vG

SM CONNECT Ticket ^ZOj8LqiB

TOPICS BY DOMAIN
KIND OF DATA ^TxjdIghU

CONNECT WORKERS / CLUSTER:

EKS -> SINGLE Deployment

Helm Chart (customize versión) -> replicator CE -> 6.1.1

Topic Regex

 ^wf7WqvWh

org.ticket.app1.ids ^7JzFxTVt

org.ticket.sell ^z3ltELSs

org.ticket.* ^p85DeO0j

users.id ^z2VIqUWp

users.tx ^jVL3Sojx

org.user.* ^BkpOvjyc

DOD of Topic Migration:

1. Define the regex the most specific -> TOPIC REGEX NEVER WHITELIST
2. Define the sizing of the dedicated cluster -> reach desired throughput (send some example)
3. Start Replicate -> Check the replicator lag < 10msg- few hundreds ms -> Enable prometheus exporter -> provide dashboard -> METRICS API AS WORKAROUND
4. Check lag message to be 0, -1 -> REPLICATION FINISHED
5. Check Consumer offsets translation -> is consumer offset reset needed? 
6. Check Consumer consumption. ^5b5ykhcK

1. Don't change consumer group Ids
2. Be aware of internal offsets
3. Template and version your config and env files
4. Be aware that the replication by default start from beginning -> configure from latest of specific offset
5. Connect Cluster Size Recommendation (send "official" data) -> NOT OUT OF MEMORY BUT SERVICE DEGRADATION
 ^VjxdSfEn

CONSUMER PART -> APPLICATION -> Decoupling consuming migration ^cPa6wspa

Replicator ^SR4BDmC6

Or Topic ^y1v4ZTIz

Re Topic ^16axGZ61

P1 ^YwPeLWdn

C1 ^6tPtJkhv

C2 ^IjWrjbW4

Cn ^grlywUe0

REPLICATION FINISHED (Point 4 above) ^TnhtUYyZ

REPLICATOR STILL WORKING

Start with Consumers

1. Identify our CG (we cannot asume any naming convention)
2. Check if offset translation needed
   2.1 If we got naming conventions it will be easier to automate (just regex)
   2.2 Script reading some file (f.e CSV) with the CG and execute translation
3. Change Consumer Config to new cluster.


 ^sBf5GdBh

C1 ^B48owMoo

Replicator ^GjXdcAwg

Or Topic ^thBql2xg

Re Topic ^cabVEMfb

P1 ^jQZ28Omn

C1 ^k5j5aWkb

C2 ^LuhJ1ukj

Cn ^EJhIKxqq

C1 ^hffzEhal

c2 ^ay3XoSHs

cn5 ^nx7CoWwF

Replicator ^PPgyi28W

Or Topic ^lYdcjJ3l

Re Topic ^Xc8IMsxf

P1 ^HbB6Tbvz

C1 ^dR4i3DKG

C2 ^ZVHm0trS

Cn ^zGevg2BS

c2 ^bJvcizyR

cn5 ^i5KepRzS

P1 ^JKegBlXU

NEW CONSUMER ^fVTbyU0T

Debugging Scenarios ^rw7b3GoE

CCloud Cluster ^osp7By7o

Consumers ^Na01xOKn

Producer ^ElkPWH80

1. Consumers can't see any new message
2. Producers can't produce
3. Consuming Lag is high ^i47MMr5P

1 Consumer can't see new messages

1. New consumer, no new data:
       - Check in C3 or with kafka topics if there is new messages
       - There is a dashboard with the offset info (Kafka-topics)
2. New Data coming but still can't consume:
       - Check the ACLS: You will find a 401/403 errors on consumer apps logs
       - Timeout on the fetch (Kafka Consumer Dashboard Metrics), timeouts on consumer app log
3. Check consumerId or ConsumerGroupId (C3 or metrics):
     - We don't have it: We should have connection errors on the client log
     - We saw it: check the offset of consumption and replica assignment
4. Replicated Topic with producer on origin cluster:
    - Check /connectors on Connect API
    - Grab the ID
    - Check Id on the Confluent Cloud Producers view (or Kafka Topics/Producer dashboard)
 ^j13S7KmS

2. Producers can't produce

1. Check Authencation/Authorization : Logs of Client side
2. Under minISR: You will se a exception (grab example) on the client Side Log
3. Acks=all Never produce.... Having a lag issue with your brokers. Kafka Topics IO metrics, logs on client side
     RP =3 minISR=2
   client -> producer thread -> broker
                           <- R1 ACk
                           <- R2 ACK
    ACK <- 
               close       <- R3 ACK *if not warning on logs and some metrics

    RP=3 minISR=3
   client -> producer thread -> broker
                            <- R1 ack
                            <- R2 ack
                            <- R3 fails
                     Retry  -> broker
                            <- R1 ack (older message delete from purgatory)
                            <- R2 ack (" " " ")
                            <- R3 fails
               Reach retry max
 UnderReplicated
             TopicException <-

x. Batch / linger.ms too high (Very rare) ^FAVF3eeC

3. Consumer Lag is going High

1. Check Producer metrics:
   - IO Metrics - Brokers struggling flushing to disk
   - Network Metric of network threads raising
   - Throughput

2. Broker Side (metrics dashboard)
   - Production Throughput
   - Network
   - Rebalancing / Repartition
   - IO Metrics
3. Consumer Side (consumer lag (lightbend))
   - Throughput
   - Network
   - Deserialization
    ^u5Y8bNbO

Centralized Monitoring ^qOOnIjgo

JMX Exporter 
Metrics API (CCLOUD) ^726Xx5tv

NEW RELIC PROMETHEUS AGENT ^4hJSBR8V

Lightbend Agent  ^nE7hCO4D

NEW RELIC ^dPzit88n

New Relic Flex Agent ^TDKnXTW3

HTTP/s Requests ^KFPo1EXj

SM CONNECT Users ^Lw0pc7kS

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://excalidraw.com",
	"elements": [
		{
			"type": "rectangle",
			"version": 363,
			"versionNonce": 324774908,
			"isDeleted": false,
			"id": "lafUjKPxiPIaCTM-Gpo-4",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -498.63160854985665,
			"y": 2287.6750730176027,
			"strokeColor": "#343a40",
			"backgroundColor": "#fab005",
			"width": 308.4302571614585,
			"height": 276.51924399724123,
			"seed": 134055292,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647430924257,
			"link": null
		},
		{
			"type": "text",
			"version": 227,
			"versionNonce": 277511023,
			"isDeleted": false,
			"id": "PzeiHOFb",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1138.5680236816406,
			"y": -814.1771850585938,
			"strokeColor": "#364fc7",
			"backgroundColor": "transparent",
			"width": 187,
			"height": 70,
			"seed": 1149906479,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 54.95617675781254,
			"fontFamily": 1,
			"text": "Agenda",
			"rawText": "Agenda",
			"baseline": 49,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Agenda"
		},
		{
			"type": "text",
			"version": 290,
			"versionNonce": 318350081,
			"isDeleted": false,
			"id": "3ZuGZCNP",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1129.5430712018697,
			"y": -731.3088751898872,
			"strokeColor": "#495057",
			"backgroundColor": "transparent",
			"width": 357,
			"height": 275,
			"seed": 1046437231,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647267906229,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Replicated topics Deletion\n\nGrouping Topics Strategy\n\nDebugging Scenarios\n\nCentralized Monitoring\n\nCleanup Policy Delete and Compact \non the same topic\n",
			"rawText": "Replicated topics Deletion\n\nGrouping Topics Strategy\n\nDebugging Scenarios\n\nCentralized Monitoring\n\nCleanup Policy Delete and Compact \non the same topic\n",
			"baseline": 268,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Replicated topics Deletion\n\nGrouping Topics Strategy\n\nDebugging Scenarios\n\nCentralized Monitoring\n\nCleanup Policy Delete and Compact \non the same topic\n"
		},
		{
			"type": "freedraw",
			"version": 13,
			"versionNonce": 834399969,
			"isDeleted": false,
			"id": "3jSkuKJCmrwXjipVoNwlB",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -869.7693099975586,
			"y": -712.2151031494141,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 1.626953125,
			"height": 1.626953125,
			"seed": 397381409,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.591796875,
					-0.591796875
				],
				[
					0.88671875,
					-1.03515625
				],
				[
					1.330078125,
					-1.478515625
				],
				[
					1.626953125,
					-1.626953125
				],
				[
					0,
					0
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.003662165254354477,
				0.01098649576306343,
				0.017090104520320892,
				0.016601815819740295,
				0.002685587853193283,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 99,
			"versionNonce": 1265085359,
			"isDeleted": false,
			"id": "VBmZci30W38ZRwNYIDdmd",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -864.1501693725586,
			"y": -717.6232452392578,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 279.19921875,
			"height": 16.12109375,
			"seed": 410154657,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.1484375,
					-0.146484375
				],
				[
					-0.443359375,
					-0.146484375
				],
				[
					-4.732421875,
					-0.443359375
				],
				[
					-6.0625,
					-0.443359375
				],
				[
					-9.01953125,
					-0.73828125
				],
				[
					-12.125,
					-1.18359375
				],
				[
					-14.935546875,
					-1.18359375
				],
				[
					-17.154296875,
					-1.18359375
				],
				[
					-22.921875,
					-1.18359375
				],
				[
					-24.84375,
					-1.18359375
				],
				[
					-27.208984375,
					-1.03515625
				],
				[
					-27.94921875,
					-1.03515625
				],
				[
					-29.279296875,
					-1.03515625
				],
				[
					-35.638671875,
					-1.03515625
				],
				[
					-40.814453125,
					-1.03515625
				],
				[
					-45.103515625,
					-0.73828125
				],
				[
					-50.57421875,
					0.296875
				],
				[
					-52.201171875,
					0.591796875
				],
				[
					-53.6796875,
					0.740234375
				],
				[
					-56.93359375,
					0.888671875
				],
				[
					-58.265625,
					0.888671875
				],
				[
					-58.85546875,
					0.888671875
				],
				[
					-61.07421875,
					0.888671875
				],
				[
					-68.171875,
					0.888671875
				],
				[
					-76.306640625,
					1.18359375
				],
				[
					-79.85546875,
					1.923828125
				],
				[
					-83.404296875,
					2.662109375
				],
				[
					-85.03125,
					2.958984375
				],
				[
					-87.841796875,
					3.40234375
				],
				[
					-89.763671875,
					3.40234375
				],
				[
					-92.572265625,
					3.40234375
				],
				[
					-95.234375,
					3.55078125
				],
				[
					-96.123046875,
					3.697265625
				],
				[
					-98.193359375,
					3.994140625
				],
				[
					-107.509765625,
					5.177734375
				],
				[
					-112.83203125,
					6.212890625
				],
				[
					-115.05078125,
					6.8046875
				],
				[
					-116.826171875,
					7.099609375
				],
				[
					-119.04296875,
					7.396484375
				],
				[
					-123.48046875,
					7.83984375
				],
				[
					-130.578125,
					8.134765625
				],
				[
					-133.091796875,
					8.134765625
				],
				[
					-136.640625,
					8.134765625
				],
				[
					-138.859375,
					8.134765625
				],
				[
					-141.96484375,
					8.134765625
				],
				[
					-147.140625,
					8.134765625
				],
				[
					-154.091796875,
					7.98828125
				],
				[
					-157.7890625,
					7.54296875
				],
				[
					-160.59765625,
					7.099609375
				],
				[
					-163.408203125,
					6.951171875
				],
				[
					-166.0703125,
					6.8046875
				],
				[
					-166.80859375,
					6.8046875
				],
				[
					-171.689453125,
					6.65625
				],
				[
					-173.611328125,
					6.65625
				],
				[
					-176.568359375,
					6.65625
				],
				[
					-177.603515625,
					6.65625
				],
				[
					-178.4921875,
					6.65625
				],
				[
					-182.189453125,
					6.65625
				],
				[
					-188.990234375,
					6.65625
				],
				[
					-192.244140625,
					6.8046875
				],
				[
					-196.828125,
					6.8046875
				],
				[
					-198.751953125,
					6.8046875
				],
				[
					-199.787109375,
					6.8046875
				],
				[
					-203.927734375,
					6.361328125
				],
				[
					-206.14453125,
					6.212890625
				],
				[
					-209.990234375,
					6.212890625
				],
				[
					-211.173828125,
					6.212890625
				],
				[
					-212.208984375,
					6.212890625
				],
				[
					-213.244140625,
					6.212890625
				],
				[
					-215.314453125,
					6.212890625
				],
				[
					-216.349609375,
					6.212890625
				],
				[
					-217.384765625,
					6.212890625
				],
				[
					-218.56640625,
					6.064453125
				],
				[
					-225.9609375,
					4.880859375
				],
				[
					-234.390625,
					3.40234375
				],
				[
					-240.75,
					2.0703125
				],
				[
					-244.59375,
					1.03515625
				],
				[
					-248.734375,
					-0.443359375
				],
				[
					-257.1640625,
					-3.25390625
				],
				[
					-260.56640625,
					-4.583984375
				],
				[
					-263.080078125,
					-5.47265625
				],
				[
					-267.220703125,
					-6.5078125
				],
				[
					-271.361328125,
					-7.099609375
				],
				[
					-274.76171875,
					-7.39453125
				],
				[
					-277.572265625,
					-7.837890625
				],
				[
					-278.75390625,
					-7.986328125
				],
				[
					-278.90234375,
					-7.986328125
				],
				[
					-279.05078125,
					-7.986328125
				],
				[
					-278.90234375,
					-7.099609375
				],
				[
					-275.05859375,
					-4.140625
				],
				[
					-275.05859375,
					-4.140625
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.005615320056676865,
				0.10107576102018356,
				0.1921721249818802,
				0.2082856446504593,
				0.21731899678707123,
				0.23489739000797272,
				0.24734874069690704,
				0.2549172341823578,
				0.2578469514846802,
				0.2700541615486145,
				0.2715190351009369,
				0.2732280492782593,
				0.2734721899032593,
				0.27469292283058167,
				0.2857099175453186,
				0.2891279458999634,
				0.2893720865249634,
				0.29205769300460815,
				0.29376667737960815,
				0.29376667737960815,
				0.29571983218193054,
				0.29694056510925293,
				0.29718470573425293,
				0.29742884635925293,
				0.2984054386615753,
				0.2991378605365753,
				0.2996261417865753,
				0.3010910153388977,
				0.3018234670162201,
				0.3032883107662201,
				0.3035324513912201,
				0.3040207624435425,
				0.3045090436935425,
				0.3047531843185425,
				0.30719462037086487,
				0.31134507060050964,
				0.31232166290283203,
				0.3142748177051544,
				0.3147630989551544,
				0.3169603943824768,
				0.32477301359176636,
				0.3357595205307007,
				0.3362478017807007,
				0.34332799911499023,
				0.3455252945423126,
				0.347478449344635,
				0.35480278730392456,
				0.3640802502632141,
				0.3648127019405365,
				0.3665217161178589,
				0.3667658567428589,
				0.3674982786178589,
				0.3677424192428589,
				0.3687190115451813,
				0.3689631521701813,
				0.3694514334201813,
				0.3696955740451813,
				0.3699397146701813,
				0.37067216634750366,
				0.37262532114982605,
				0.37262532114982605,
				0.37286946177482605,
				0.37311360239982605,
				0.37360188364982605,
				0.37409019470214844,
				0.37433433532714844,
				0.37482261657714844,
				0.37482261657714844,
				0.37482261657714844,
				0.37482261657714844,
				0.37506675720214844,
				0.37506675720214844,
				0.37482261657714844,
				0.37482261657714844,
				0.3765316307544708,
				0.3784847855567932,
				0.3784847855567932,
				0.3789730668067932,
				0.3797054886817932,
				0.3816586434841156,
				0.382879376411438,
				0.382391095161438,
				0.382879376411438,
				0.383367657661438,
				0.383611798286438,
				0.3841001093387604,
				0.3850766718387604,
				0.3855649530887604,
				0.38629740476608276,
				0.4114442765712738,
				0.15091173350811005,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 23,
			"versionNonce": 1090349761,
			"isDeleted": false,
			"id": "1X0VUycF6aY0YsxS4B2KK",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1109.3357162475586,
			"y": -730.5549468994141,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 18.041015625,
			"height": 27.658203125,
			"seed": 1350247855,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0,
					0.294921875
				],
				[
					-0.296875,
					0.294921875
				],
				[
					-1.626953125,
					1.03515625
				],
				[
					-3.10546875,
					2.958984375
				],
				[
					-5.619140625,
					6.212890625
				],
				[
					-6.359375,
					7.248046875
				],
				[
					-9.759765625,
					11.83203125
				],
				[
					-12.126953125,
					15.3828125
				],
				[
					-13.60546875,
					18.33984375
				],
				[
					-15.083984375,
					21.4453125
				],
				[
					-17.154296875,
					25.439453125
				],
				[
					-18.041015625,
					27.509765625
				],
				[
					-18.041015625,
					27.658203125
				],
				[
					-17.154296875,
					27.658203125
				],
				[
					-15.67578125,
					27.658203125
				],
				[
					-15.67578125,
					27.658203125
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.04150453954935074,
				0.08984512090682983,
				0.1765468865633011,
				0.29449912905693054,
				0.38776227831840515,
				0.4300297498703003,
				0.43320363759994507,
				0.43979552388191223,
				0.442481130361557,
				0.442481130361557,
				0.442969411611557,
				0.4451667070388794,
				0.4451667070388794,
				0.43759822845458984,
				0.19949644804000854,
				0.00317387655377388,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 18,
			"versionNonce": 1744068047,
			"isDeleted": false,
			"id": "ua7MzwQbco1YB0lVfdXMa",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1084.6404037475586,
			"y": -728.0412750244141,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 13.013671875,
			"height": 26.4765625,
			"seed": 782801103,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-0.146484375,
					2.810546875
				],
				[
					-1.181640625,
					4.5859375
				],
				[
					-1.7734375,
					5.62109375
				],
				[
					-2.365234375,
					6.65625
				],
				[
					-2.95703125,
					7.69140625
				],
				[
					-4.435546875,
					10.05859375
				],
				[
					-7.39453125,
					15.53125
				],
				[
					-10.943359375,
					22.185546875
				],
				[
					-13.013671875,
					26.4765625
				],
				[
					-12.421875,
					26.4765625
				],
				[
					-12.421875,
					26.4765625
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.048584725707769394,
				0.19485771656036377,
				0.22586403787136078,
				0.2385595440864563,
				0.24930189549922943,
				0.2583352327346802,
				0.2724956274032593,
				0.286930650472641,
				0.2886396646499634,
				0.2646830081939697,
				0.252231627702713,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 17,
			"versionNonce": 107244193,
			"isDeleted": false,
			"id": "E99Am_4tjjrM4x5l5IRf6",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1055.2126693725586,
			"y": -727.8928375244141,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 13.60546875,
			"height": 28.841796875,
			"seed": 1521185505,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.296875,
					0.1484375
				],
				[
					0.296875,
					0.88671875
				],
				[
					-0.73828125,
					3.55078125
				],
				[
					-5.9140625,
					14.19921875
				],
				[
					-10.646484375,
					23.369140625
				],
				[
					-11.38671875,
					24.701171875
				],
				[
					-11.9765625,
					25.8828125
				],
				[
					-13.30859375,
					28.693359375
				],
				[
					-13.30859375,
					28.841796875
				],
				[
					-13.30859375,
					28.841796875
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.052979324012994766,
				0.10424964129924774,
				0.15506218373775482,
				0.21194781363010406,
				0.28152894973754883,
				0.29279011487960815,
				0.29279011487960815,
				0.29303425550460815,
				0.252719908952713,
				0.19900816679000854,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 18,
			"versionNonce": 1348848623,
			"isDeleted": false,
			"id": "ZJOuX6U5u1hj-kgjaEu1Z",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1018.0935287475586,
			"y": -727.1526031494141,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 16.708984375,
			"height": 27.509765625,
			"seed": 1989809697,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.1484375,
					-0.296875
				],
				[
					0.294921875,
					-0.443359375
				],
				[
					0.1484375,
					2.21875
				],
				[
					-2.513671875,
					6.951171875
				],
				[
					-10.794921875,
					18.634765625
				],
				[
					-12.865234375,
					21.59375
				],
				[
					-14.34375,
					24.255859375
				],
				[
					-16.4140625,
					27.06640625
				],
				[
					-16.4140625,
					26.623046875
				],
				[
					-16.267578125,
					25.587890625
				],
				[
					-16.267578125,
					25.587890625
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.005127031356096268,
				0.020263981074094772,
				0.05517662316560745,
				0.221713587641716,
				0.2683451473712921,
				0.287907212972641,
				0.2881513833999634,
				0.2857099175453186,
				0.21194781363010406,
				0.14554055035114288,
				0.10278477519750595,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 17,
			"versionNonce": 715157121,
			"isDeleted": false,
			"id": "c35tR_XmOy4vebyzGXRUy",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -984.0818099975586,
			"y": -714.5803375244141,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 9.021484375,
			"height": 19.818359375,
			"seed": 207042863,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.1484375,
					-0.1484375
				],
				[
					0.296875,
					-0.296875
				],
				[
					-2.216796875,
					6.359375
				],
				[
					-3.9921875,
					9.908203125
				],
				[
					-5.9140625,
					13.75390625
				],
				[
					-8.427734375,
					18.634765625
				],
				[
					-8.576171875,
					19.2265625
				],
				[
					-8.724609375,
					19.521484375
				],
				[
					-6.654296875,
					14.9375
				],
				[
					-6.654296875,
					14.9375
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.061036087572574615,
				0.1443198323249817,
				0.1899748295545578,
				0.29352253675460815,
				0.29352253675460815,
				0.29571983218193054,
				0.29279011487960815,
				0.2886396646499634,
				0.27713435888290405,
				0.02197299152612686,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 18,
			"versionNonce": 549746191,
			"isDeleted": false,
			"id": "e06PNWXv280xnydiq9pY5",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -942.2302474975586,
			"y": -733.9572906494141,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 15.380859375,
			"height": 36.9765625,
			"seed": 203180527,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0,
					1.923828125
				],
				[
					-0.888671875,
					4.734375
				],
				[
					-1.33203125,
					5.916015625
				],
				[
					-4.140625,
					12.12890625
				],
				[
					-7.099609375,
					18.48828125
				],
				[
					-9.169921875,
					23.220703125
				],
				[
					-10.056640625,
					25.587890625
				],
				[
					-13.90234375,
					35.0546875
				],
				[
					-15.380859375,
					36.9765625
				],
				[
					-12.5703125,
					32.5390625
				],
				[
					-12.5703125,
					32.5390625
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.029785610735416412,
				0.13842985033988953,
				0.18240635097026825,
				0.19632257521152496,
				0.23025864362716675,
				0.24710460007190704,
				0.2549172341823578,
				0.2573586702346802,
				0.26150912046432495,
				0.22537575662136078,
				0.19876402616500854,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 22,
			"versionNonce": 342947425,
			"isDeleted": false,
			"id": "QURCMTMpQqLUdiJhb8GG0",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -906.2966537475586,
			"y": -729.0764312744141,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 16.4140625,
			"height": 34.90625,
			"seed": 1477229505,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-1.625,
					5.029296875
				],
				[
					-6.94921875,
					17.896484375
				],
				[
					-7.541015625,
					19.5234375
				],
				[
					-10.0546875,
					25.14453125
				],
				[
					-12.568359375,
					29.728515625
				],
				[
					-12.865234375,
					30.3203125
				],
				[
					-13.751953125,
					31.65234375
				],
				[
					-14.1953125,
					32.244140625
				],
				[
					-14.4921875,
					32.5390625
				],
				[
					-15.23046875,
					33.87109375
				],
				[
					-15.673828125,
					34.609375
				],
				[
					-15.970703125,
					34.90625
				],
				[
					-16.265625,
					34.90625
				],
				[
					-16.4140625,
					34.90625
				],
				[
					-16.4140625,
					34.90625
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.027832455933094025,
				0.26321813464164734,
				0.27615776658058167,
				0.27615776658058167,
				0.27737849950790405,
				0.2717631757259369,
				0.2702983021736145,
				0.251499205827713,
				0.2397802770137787,
				0.23245593905448914,
				0.2058442085981369,
				0.1753261685371399,
				0.1572594791650772,
				0.1286640763282776,
				0.0793469175696373,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 8,
			"versionNonce": 1611193391,
			"isDeleted": false,
			"id": "6MPL4GikkdMghR_bnHNtl",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1048.101219177246,
			"y": -568.1534576416016,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 1560911311,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 8,
			"versionNonce": 648380993,
			"isDeleted": false,
			"id": "b0wYdEjTT1BKguHxacy5i",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1048.101219177246,
			"y": -568.1534576416016,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 44510849,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 31,
			"versionNonce": 572227151,
			"isDeleted": false,
			"id": "mguQubz5-ivEuwHnKP3p1",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -979.5974349975586,
			"y": -734.7912750244141,
			"strokeColor": "#c92a2a",
			"backgroundColor": "transparent",
			"width": 24.3984375,
			"height": 39.935546875,
			"seed": 1126758273,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-0.58984375,
					0
				],
				[
					-1.330078125,
					0.443359375
				],
				[
					-2.80859375,
					2.3671875
				],
				[
					-4.435546875,
					4.4375
				],
				[
					-6.94921875,
					7.986328125
				],
				[
					-9.31640625,
					11.83203125
				],
				[
					-10.94140625,
					15.0859375
				],
				[
					-12.716796875,
					17.896484375
				],
				[
					-13.16015625,
					18.63671875
				],
				[
					-14.34375,
					20.263671875
				],
				[
					-15.23046875,
					21.7421875
				],
				[
					-16.1171875,
					23.220703125
				],
				[
					-17.15234375,
					25.14453125
				],
				[
					-18.1875,
					27.21484375
				],
				[
					-19.22265625,
					28.841796875
				],
				[
					-20.40625,
					30.912109375
				],
				[
					-21.146484375,
					32.390625
				],
				[
					-22.328125,
					34.314453125
				],
				[
					-23.068359375,
					35.498046875
				],
				[
					-23.955078125,
					36.681640625
				],
				[
					-24.3984375,
					37.419921875
				],
				[
					-24.3984375,
					38.751953125
				],
				[
					-24.3984375,
					39.935546875
				],
				[
					-24.3984375,
					39.935546875
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.004394598305225372,
				0.3845883905887604,
				0.38678568601608276,
				0.38800641894340515,
				0.39020371437072754,
				0.39142441749572754,
				0.3948424458503723,
				0.3948424458503723,
				0.3955748975276947,
				0.3955748975276947,
				0.3958190381526947,
				0.3958190381526947,
				0.3972838819026947,
				0.3982604742050171,
				0.3985046148300171,
				0.3987487554550171,
				0.3987487554550171,
				0.3985046148300171,
				0.3987487554550171,
				0.3989928960800171,
				0.3989928960800171,
				0.3997253477573395,
				0.28201723098754883,
				0.17947661876678467,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 29,
			"versionNonce": 1042125345,
			"isDeleted": false,
			"id": "n6kd-zgqAITmf9hy8L5IH",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -768.5700912475586,
			"y": -477.88111877441406,
			"strokeColor": "#495057",
			"backgroundColor": "transparent",
			"width": 24.84375,
			"height": 69.072265625,
			"seed": 1559134991,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-0.4453125,
					0.4453125
				],
				[
					-0.591796875,
					0.4453125
				],
				[
					-1.18359375,
					0.4453125
				],
				[
					-2.21875,
					0.4453125
				],
				[
					-3.845703125,
					-1.478515625
				],
				[
					-6.2109375,
					-7.689453125
				],
				[
					-8.873046875,
					-16.712890625
				],
				[
					-10.205078125,
					-21.888671875
				],
				[
					-10.6484375,
					-26.177734375
				],
				[
					-9.46484375,
					-32.390625
				],
				[
					-4.4375,
					-34.904296875
				],
				[
					-2.3671875,
					-35.34765625
				],
				[
					4.435546875,
					-36.974609375
				],
				[
					9.759765625,
					-40.525390625
				],
				[
					13.30859375,
					-47.7734375
				],
				[
					13.900390625,
					-50.73046875
				],
				[
					14.1953125,
					-59.45703125
				],
				[
					11.830078125,
					-66.556640625
				],
				[
					4.287109375,
					-68.626953125
				],
				[
					-1.48046875,
					-64.189453125
				],
				[
					-2.810546875,
					-57.978515625
				],
				[
					-2.810546875,
					-57.978515625
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.09814602881669998,
				0.3347829282283783,
				0.3684748709201813,
				0.4478522837162018,
				0.540871262550354,
				0.6021820306777954,
				0.6322118043899536,
				0.6370946764945984,
				0.6370946764945984,
				0.6305027604103088,
				0.6248874664306641,
				0.6302586197853088,
				0.6302586197853088,
				0.6305027604103088,
				0.6241550445556641,
				0.6209811568260193,
				0.6285496354103088,
				0.6551613807678223,
				0.6644388437271118,
				0.659067690372467,
				0.6068207621574402,
				0.191683828830719,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 21,
			"versionNonce": 1775557743,
			"isDeleted": false,
			"id": "M2XlGMHsk0pFBzEr1kXT7",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -769.1618881225586,
			"y": -467.08424377441406,
			"strokeColor": "#495057",
			"backgroundColor": "transparent",
			"width": 2.810546875,
			"height": 2.365234375,
			"seed": 425804943,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0,
					-0.146484375
				],
				[
					-0.740234375,
					-0.146484375
				],
				[
					-0.740234375,
					-0.88671875
				],
				[
					-0.740234375,
					-1.7734375
				],
				[
					-0.740234375,
					-2.365234375
				],
				[
					-1.626953125,
					-2.365234375
				],
				[
					-2.0703125,
					-2.365234375
				],
				[
					-2.662109375,
					-1.478515625
				],
				[
					-2.810546875,
					-1.03515625
				],
				[
					-1.923828125,
					-1.626953125
				],
				[
					-1.775390625,
					-2.0703125
				],
				[
					-1.775390625,
					-1.7734375
				],
				[
					0,
					0
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.0007324330508708954,
				0.14724956452846527,
				0.32672616839408875,
				0.4615243673324585,
				0.4617685079574585,
				0.4590829312801361,
				0.43979552388191223,
				0.43979552388191223,
				0.4446784257888794,
				0.47153428196907043,
				0.537697434425354,
				0.5440451502799988,
				0.2717631757259369,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 39,
			"versionNonce": 1875420673,
			"isDeleted": false,
			"id": "5YEXmwoUT9WsAK70SNBMG",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1144.7790756225586,
			"y": -532.9006500244141,
			"strokeColor": "#495057",
			"backgroundColor": "transparent",
			"width": 26.17578125,
			"height": 59.90234375,
			"seed": 645432719,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0,
					-1.626953125
				],
				[
					0,
					-2.21875
				],
				[
					0,
					-2.3671875
				],
				[
					0,
					-2.515625
				],
				[
					0.294921875,
					-2.515625
				],
				[
					0.740234375,
					-2.515625
				],
				[
					1.18359375,
					-2.515625
				],
				[
					1.921875,
					-1.626953125
				],
				[
					2.662109375,
					2.95703125
				],
				[
					3.25390625,
					14.05078125
				],
				[
					3.548828125,
					19.2265625
				],
				[
					3.845703125,
					21.298828125
				],
				[
					5.02734375,
					25.291015625
				],
				[
					5.32421875,
					25.439453125
				],
				[
					5.32421875,
					25.142578125
				],
				[
					3.400390625,
					22.92578125
				],
				[
					0,
					20.115234375
				],
				[
					-2.0703125,
					19.5234375
				],
				[
					-3.25390625,
					19.5234375
				],
				[
					-8.578125,
					21.7421875
				],
				[
					-12.126953125,
					26.474609375
				],
				[
					-14.197265625,
					32.2421875
				],
				[
					-14.4921875,
					34.4609375
				],
				[
					-14.640625,
					41.857421875
				],
				[
					-12.71875,
					49.103515625
				],
				[
					-9.46484375,
					55.908203125
				],
				[
					-7.986328125,
					57.23828125
				],
				[
					-3.40234375,
					57.38671875
				],
				[
					1.478515625,
					54.724609375
				],
				[
					9.611328125,
					48.51171875
				],
				[
					11.53515625,
					47.033203125
				],
				[
					11.53515625,
					47.033203125
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.0014648661017417908,
				0.17141985893249512,
				0.24197757244110107,
				0.287174791097641,
				0.3841001093387604,
				0.48178836703300476,
				0.6456397175788879,
				0.6810406446456909,
				0.6881208419799805,
				0.6805523633956909,
				0.6837262511253357,
				0.6842145323753357,
				0.6876325607299805,
				0.6917830109596252,
				0.69373619556427,
				0.658335268497467,
				0.658579409122467,
				0.658335268497467,
				0.6475929021835327,
				0.6410009860992432,
				0.6248874664306641,
				0.6241550445556641,
				0.6236667633056641,
				0.6241550445556641,
				0.6331883668899536,
				0.6414892673492432,
				0.6666361689567566,
				0.6876325607299805,
				0.69495689868927,
				0.6715190410614014,
				0.30988022685050964,
				0.0639658197760582,
				0
			]
		},
		{
			"type": "rectangle",
			"version": 362,
			"versionNonce": 1258124943,
			"isDeleted": false,
			"id": "K14j_ZoLtuN__hxcojQn4",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1129.743509565081,
			"y": 205.87590898786272,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 203,
			"height": 356.03540802001953,
			"seed": 1610820609,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null
		},
		{
			"type": "text",
			"version": 333,
			"versionNonce": 111558113,
			"isDeleted": false,
			"id": "LJXXar06",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1097.2413667951312,
			"y": 372.99228014264787,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 132,
			"height": 25,
			"seed": 325780769,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Origin Cluster",
			"rawText": "Origin Cluster",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Origin Cluster"
		},
		{
			"type": "rectangle",
			"version": 466,
			"versionNonce": 1280167087,
			"isDeleted": false,
			"id": "bj3BXprQjO5kp2OmQEkfU",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -10.876121520996094,
			"y": 215.6616461617607,
			"strokeColor": "#495057",
			"backgroundColor": "#15aabf",
			"width": 203,
			"height": 357,
			"seed": 1454121761,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "4CyluN8z"
				},
				{
					"id": "oMg7F3orcPeTSYDrdVamg",
					"type": "arrow"
				}
			],
			"updated": 1647266741840,
			"link": null
		},
		{
			"type": "text",
			"version": 347,
			"versionNonce": 1103485377,
			"isDeleted": false,
			"id": "4CyluN8z",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -5.876121520996094,
			"y": 381.6616461617607,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 193,
			"height": 25,
			"seed": 1665545665,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Dest Cluster",
			"rawText": "Dest Cluster",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "bj3BXprQjO5kp2OmQEkfU",
			"originalText": "Dest Cluster"
		},
		{
			"type": "diamond",
			"version": 516,
			"versionNonce": 158228175,
			"isDeleted": false,
			"id": "snYh7rXaiVYO-mPWJSDUq",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -540.0777119227819,
			"y": 179.70465959821428,
			"strokeColor": "#495057",
			"backgroundColor": "#228be6",
			"width": 169,
			"height": 139,
			"seed": 27614223,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "5k3AiSY0"
				},
				{
					"id": "oMg7F3orcPeTSYDrdVamg",
					"type": "arrow"
				},
				{
					"id": "xehYg5pj_bOHWsObXkLtr",
					"type": "arrow"
				}
			],
			"updated": 1647266741840,
			"link": null
		},
		{
			"type": "text",
			"version": 480,
			"versionNonce": 1380383137,
			"isDeleted": false,
			"id": "5k3AiSY0",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -535.0777119227819,
			"y": 236.70465959821428,
			"strokeColor": "#495057",
			"backgroundColor": "#228be6",
			"width": 159,
			"height": 25,
			"seed": 1701742913,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Replicator",
			"rawText": "Replicator",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "snYh7rXaiVYO-mPWJSDUq",
			"originalText": "Replicator"
		},
		{
			"type": "rectangle",
			"version": 134,
			"versionNonce": 351775983,
			"isDeleted": false,
			"id": "-6gzy7Mt96aBDtMSyKoOf",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1060.0597490583148,
			"y": 233.90309252057756,
			"strokeColor": "#495057",
			"backgroundColor": "#228be6",
			"width": 105,
			"height": 40,
			"seed": 567009903,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "anwEjRUf"
				},
				{
					"id": "AvVVfVpGf5Z7mfibAXNwC",
					"type": "arrow"
				}
			],
			"updated": 1647266741840,
			"link": null
		},
		{
			"type": "text",
			"version": 97,
			"versionNonce": 425270657,
			"isDeleted": false,
			"id": "anwEjRUf",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1055.0597490583148,
			"y": 241.40309252057756,
			"strokeColor": "#495057",
			"backgroundColor": "#228be6",
			"width": 95,
			"height": 25,
			"seed": 684918127,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Topic 1",
			"rawText": "Topic 1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "-6gzy7Mt96aBDtMSyKoOf",
			"originalText": "Topic 1"
		},
		{
			"type": "rectangle",
			"version": 224,
			"versionNonce": 443692815,
			"isDeleted": false,
			"id": "jGeuKOYlZqu6jt7-pghu7",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1063.9110358101982,
			"y": 299.86391013009217,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 109,
			"height": 36,
			"seed": 1469678479,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "ZXzrnWwo"
				},
				{
					"id": "g6CIOIZXbCdLuBRyDjIUA",
					"type": "arrow"
				}
			],
			"updated": 1647266741840,
			"link": null
		},
		{
			"type": "text",
			"version": 153,
			"versionNonce": 641451361,
			"isDeleted": false,
			"id": "ZXzrnWwo",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1058.9110358101982,
			"y": 305.36391013009217,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 99,
			"height": 25,
			"seed": 529690223,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Topic 2",
			"rawText": "Topic 2",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "jGeuKOYlZqu6jt7-pghu7",
			"originalText": "Topic 2"
		},
		{
			"type": "diamond",
			"version": 463,
			"versionNonce": 1079409967,
			"isDeleted": false,
			"id": "-sxSsCMgZf5FSWf9nDfAT",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -538.5009711129323,
			"y": 376.6828869410924,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 169,
			"height": 139,
			"seed": 1387733775,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "G5jUdkKH",
					"type": "text"
				},
				{
					"id": "f-1pHLlowHR7-KK05T962",
					"type": "arrow"
				},
				{
					"id": "Fn4FelIm-LQEGXYGp2zle",
					"type": "arrow"
				}
			],
			"updated": 1647266741840,
			"link": null
		},
		{
			"type": "text",
			"version": 425,
			"versionNonce": 1950249281,
			"isDeleted": false,
			"id": "G5jUdkKH",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -533.5009711129323,
			"y": 433.6828869410924,
			"strokeColor": "#495057",
			"backgroundColor": "#228be6",
			"width": 159,
			"height": 25,
			"seed": 839620961,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Replicator",
			"rawText": "Replicator",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "-sxSsCMgZf5FSWf9nDfAT",
			"originalText": "Replicator"
		},
		{
			"type": "arrow",
			"version": 238,
			"versionNonce": 460423932,
			"isDeleted": false,
			"id": "xehYg5pj_bOHWsObXkLtr",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -957.7579465593608,
			"y": 256.5519752502441,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 418.07756696428555,
			"height": 11.19977678571422,
			"seed": 2007964769,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920479,
			"link": null,
			"startBinding": null,
			"endBinding": {
				"elementId": "snYh7rXaiVYO-mPWJSDUq",
				"gap": 2.722959213688142,
				"focus": 0.0878484423974032
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					418.07756696428555,
					-11.19977678571422
				]
			]
		},
		{
			"type": "arrow",
			"version": 668,
			"versionNonce": 869554628,
			"isDeleted": false,
			"id": "f-1pHLlowHR7-KK05T962",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -928.7622394325319,
			"y": 330.9353861907273,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 388.73192942042965,
			"height": 119.28131020951156,
			"seed": 1102043521,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920482,
			"link": null,
			"startBinding": null,
			"endBinding": {
				"elementId": "-sxSsCMgZf5FSWf9nDfAT",
				"gap": 1.5415885484823235,
				"focus": -0.3903301566349092
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					0.18696439677967788,
					-0.4055998434344575
				],
				[
					7.737833168056079,
					-3.839856412514617
				],
				[
					388.73192942042965,
					115.44145379699694
				]
			]
		},
		{
			"type": "arrow",
			"version": 811,
			"versionNonce": 1445462084,
			"isDeleted": false,
			"id": "oMg7F3orcPeTSYDrdVamg",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -367.84414508679845,
			"y": 247.7554595590691,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 404.47714915894744,
			"height": 16.482656575874273,
			"seed": 2092670031,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920483,
			"link": null,
			"startBinding": {
				"elementId": "snYh7rXaiVYO-mPWJSDUq",
				"gap": 3.5434637342902207,
				"focus": -0.07229336739535253
			},
			"endBinding": {
				"elementId": "FdJ_kaYXRiv5zAI0M12kZ",
				"gap": 1.7677865511071786,
				"focus": 0.10389849670262119
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					404.47714915894744,
					16.482656575874273
				]
			]
		},
		{
			"type": "arrow",
			"version": 516,
			"versionNonce": 893867972,
			"isDeleted": false,
			"id": "Fn4FelIm-LQEGXYGp2zle",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -366.2650329701612,
			"y": 442.9571104502519,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 405.00028232750964,
			"height": 104.4478183319473,
			"seed": 908799809,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920484,
			"link": null,
			"startBinding": {
				"elementId": "-sxSsCMgZf5FSWf9nDfAT",
				"gap": 4.5691278853519535,
				"focus": 0.2791502428771167
			},
			"endBinding": {
				"elementId": "MZTrbL1HLhmJXnR9cDO-m",
				"gap": 4.08781081459648,
				"focus": 0.6179760859871976
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					405.00028232750964,
					-104.4478183319473
				]
			]
		},
		{
			"type": "text",
			"version": 166,
			"versionNonce": 860010383,
			"isDeleted": false,
			"id": "bGPIPrWZ",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -553.5305361066548,
			"y": 152.2802816118513,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 183,
			"height": 25,
			"seed": 1839407247,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "config.sync=enabled",
			"rawText": "config.sync=enabled",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "config.sync=enabled"
		},
		{
			"type": "text",
			"version": 292,
			"versionNonce": 554798305,
			"isDeleted": false,
			"id": "U3eUUgQD",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -525.0684999738421,
			"y": 526.9398536682129,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 190,
			"height": 25,
			"seed": 376548943,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "config.sync=disabled",
			"rawText": "config.sync=disabled",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "config.sync=disabled"
		},
		{
			"type": "text",
			"version": 378,
			"versionNonce": 1547890095,
			"isDeleted": false,
			"id": "sgoyH63W",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -903.5577577863434,
			"y": 12.395306723458816,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 142,
			"height": 25,
			"seed": 1836081121,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "AvVVfVpGf5Z7mfibAXNwC",
					"type": "arrow"
				}
			],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "retention.ms=0",
			"rawText": "retention.ms=0",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "retention.ms=0"
		},
		{
			"type": "arrow",
			"version": 360,
			"versionNonce": 750764924,
			"isDeleted": false,
			"id": "AvVVfVpGf5Z7mfibAXNwC",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -796.0675207898698,
			"y": 46.99264907836914,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 180.42582018203257,
			"height": 185.34689767020092,
			"seed": 879180527,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920480,
			"link": null,
			"startBinding": {
				"elementId": "sgoyH63W",
				"gap": 9.597342354910325,
				"focus": -0.6973932193393783
			},
			"endBinding": {
				"elementId": "-6gzy7Mt96aBDtMSyKoOf",
				"gap": 1.563545772007501,
				"focus": 0.1399962467274753
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-180.42582018203257,
					185.34689767020092
				]
			]
		},
		{
			"type": "rectangle",
			"version": 262,
			"versionNonce": 1531855,
			"isDeleted": false,
			"id": "FdJ_kaYXRiv5zAI0M12kZ",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 38.40079062325617,
			"y": 248.7498076302665,
			"strokeColor": "#495057",
			"backgroundColor": "#228be6",
			"width": 105,
			"height": 40,
			"seed": 441569871,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "oSZ0kAbB",
					"type": "text"
				},
				{
					"id": "AvVVfVpGf5Z7mfibAXNwC",
					"type": "arrow"
				},
				{
					"id": "3xw32rmnoWntXYP0cQpii",
					"type": "arrow"
				},
				{
					"id": "oMg7F3orcPeTSYDrdVamg",
					"type": "arrow"
				}
			],
			"updated": 1647266741840,
			"link": null
		},
		{
			"type": "text",
			"version": 222,
			"versionNonce": 929728673,
			"isDeleted": false,
			"id": "oSZ0kAbB",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 43.40079062325617,
			"y": 256.2498076302665,
			"strokeColor": "#495057",
			"backgroundColor": "#228be6",
			"width": 95,
			"height": 25,
			"seed": 1244036641,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Topic 1",
			"rawText": "Topic 1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "FdJ_kaYXRiv5zAI0M12kZ",
			"originalText": "Topic 1"
		},
		{
			"type": "text",
			"version": 457,
			"versionNonce": 819498479,
			"isDeleted": false,
			"id": "wfddOSQd",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 76.5413284301759,
			"y": 83.2150960649762,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 142,
			"height": 25,
			"seed": 108274031,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "AvVVfVpGf5Z7mfibAXNwC",
					"type": "arrow"
				},
				{
					"id": "3xw32rmnoWntXYP0cQpii",
					"type": "arrow"
				}
			],
			"updated": 1647266741840,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "retention.ms=0",
			"rawText": "retention.ms=0",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "retention.ms=0"
		},
		{
			"type": "arrow",
			"version": 372,
			"versionNonce": 1276360900,
			"isDeleted": false,
			"id": "3xw32rmnoWntXYP0cQpii",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 150.57867923766213,
			"y": 110.70044762747631,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 59.48782627619197,
			"height": 136.3232421875001,
			"seed": 1729484577,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920483,
			"link": null,
			"startBinding": {
				"elementId": "wfddOSQd",
				"gap": 2.4853515625001137,
				"focus": -0.1252579980632633
			},
			"endBinding": {
				"elementId": "FdJ_kaYXRiv5zAI0M12kZ",
				"gap": 1.7261178152900811,
				"focus": -0.15173967562201615
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-59.48782627619197,
					136.3232421875001
				]
			]
		},
		{
			"type": "text",
			"version": 464,
			"versionNonce": 697235471,
			"isDeleted": false,
			"id": "SoZh7Cmn",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -887.0823287963863,
			"y": 479.38823536464145,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 142,
			"height": 25,
			"seed": 1748565487,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "AvVVfVpGf5Z7mfibAXNwC",
					"type": "arrow"
				},
				{
					"id": "g6CIOIZXbCdLuBRyDjIUA",
					"type": "arrow"
				}
			],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "retention.ms=0",
			"rawText": "retention.ms=0",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "retention.ms=0"
		},
		{
			"type": "arrow",
			"version": 415,
			"versionNonce": 1802630140,
			"isDeleted": false,
			"id": "g6CIOIZXbCdLuBRyDjIUA",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -819.9856638048637,
			"y": 470.7277390616277,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 139.0557146159548,
			"height": 133.86382893153552,
			"seed": 424101039,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920481,
			"link": null,
			"startBinding": {
				"elementId": "SoZh7Cmn",
				"gap": 8.660496303013758,
				"focus": 0.21525163232540592
			},
			"endBinding": {
				"elementId": "jGeuKOYlZqu6jt7-pghu7",
				"gap": 1,
				"focus": -0.4184908403047437
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-139.0557146159548,
					-133.86382893153552
				]
			]
		},
		{
			"type": "text",
			"version": 554,
			"versionNonce": 808656431,
			"isDeleted": false,
			"id": "YD3fFqRI",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 272.3974097115654,
			"y": 388.93487494332413,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 142,
			"height": 25,
			"seed": 365157327,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "AvVVfVpGf5Z7mfibAXNwC",
					"type": "arrow"
				},
				{
					"id": "g6CIOIZXbCdLuBRyDjIUA",
					"type": "arrow"
				},
				{
					"id": "c0n1yTsjstDFfn42gNmLa",
					"type": "arrow"
				}
			],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "retention.ms=0",
			"rawText": "retention.ms=0",
			"baseline": 18,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "retention.ms=0"
		},
		{
			"type": "rectangle",
			"version": 257,
			"versionNonce": 1991794753,
			"isDeleted": false,
			"id": "MZTrbL1HLhmJXnR9cDO-m",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 42.82306017194492,
			"y": 325.20916693551186,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 109,
			"height": 36,
			"seed": 271629889,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "bvPRMILq",
					"type": "text"
				},
				{
					"id": "g6CIOIZXbCdLuBRyDjIUA",
					"type": "arrow"
				},
				{
					"id": "Fn4FelIm-LQEGXYGp2zle",
					"type": "arrow"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "text",
			"version": 184,
			"versionNonce": 934117455,
			"isDeleted": false,
			"id": "bvPRMILq",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 47.82306017194492,
			"y": 330.70916693551186,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 99,
			"height": 25,
			"seed": 1458700879,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Topic 2",
			"rawText": "Topic 2",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "MZTrbL1HLhmJXnR9cDO-m",
			"originalText": "Topic 2"
		},
		{
			"type": "arrow",
			"version": 152,
			"versionNonce": 69513249,
			"isDeleted": false,
			"id": "c0n1yTsjstDFfn42gNmLa",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 264.42865208217063,
			"y": 383.5385823931012,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 118.09151785714289,
			"height": 39.723772321428555,
			"seed": 607616111,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"startBinding": {
				"elementId": "YD3fFqRI",
				"focus": -0.238223907801764,
				"gap": 7.968757629394759
			},
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-118.09151785714289,
					-39.723772321428555
				]
			]
		},
		{
			"type": "freedraw",
			"version": 72,
			"versionNonce": 1104584303,
			"isDeleted": false,
			"id": "UsBrrZhtEAC9-InQ5qwZj",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 288.51347351074196,
			"y": 356.4933815002441,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 107.10658482142867,
			"height": 102.05357142857144,
			"seed": 535585121,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					10.984933035714448,
					15.42410714285711
				],
				[
					24.715401785714448,
					27.890625
				],
				[
					44.36383928571445,
					42.469308035714334
				],
				[
					65.91238839285711,
					59.58426339285711
				],
				[
					78.79743303571445,
					72.47209821428567
				],
				[
					90.20647321428578,
					84.51729910714289
				],
				[
					102.45814732142867,
					97.40513392857144
				],
				[
					107.10658482142867,
					102.05357142857144
				],
				[
					106.89453125,
					101.42020089285711
				],
				[
					103.515625,
					97.6171875
				],
				[
					103.515625,
					97.6171875
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.07763790339231491,
				0.29449912905693054,
				0.3687190115451813,
				0.42413976788520813,
				0.46616312861442566,
				0.4766613245010376,
				0.47885861992836,
				0.47910276055336,
				0.4634775221347809,
				0.3032883107662201,
				0.11230640113353729,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 75,
			"versionNonce": 1664877569,
			"isDeleted": false,
			"id": "MsO2biiKf44sKautNI-3X",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 373.22608511788485,
			"y": 331.5603457859584,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 72.24888392857156,
			"height": 181.4983258928571,
			"seed": 2053419503,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					3.8030133928571104,
					-3.8030133928572236
				],
				[
					4.227120535714221,
					-4.015066964285779
				],
				[
					4.436383928571558,
					-4.22433035714289
				],
				[
					2.9575892857142216,
					4.227120535714222
				],
				[
					1.0574776785715585,
					9.93024553571422
				],
				[
					-3.37890625,
					23.454241071428555
				],
				[
					-15.42131696428578,
					55.78125
				],
				[
					-30.84263392857133,
					88.95368303571422
				],
				[
					-51.33370535714289,
					131.6350446428571
				],
				[
					-58.30636160714289,
					147.48325892857133
				],
				[
					-62.95479910714289,
					159.10435267857133
				],
				[
					-67.8125,
					174.3164062499999
				],
				[
					-67.8125,
					177.27399553571422
				],
				[
					-67.8125,
					177.27399553571422
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0.009277485311031342,
				0.23001450300216675,
				0.2851911187171936,
				0.347234308719635,
				0.3816586434841156,
				0.38654154539108276,
				0.40167850255966187,
				0.41705960035324097,
				0.42462804913520813,
				0.4251163601875305,
				0.4248722195625305,
				0.3999694883823395,
				0.20950637757778168,
				0.005615320056676865,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 62,
			"versionNonce": 24574095,
			"isDeleted": false,
			"id": "6wUehY8MDUaZ3WvdCseQl",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -501.4432787214007,
			"y": 168.69212940761014,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 1160811247,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 62,
			"versionNonce": 805166049,
			"isDeleted": false,
			"id": "fc8vjn5pHV0x-a7PUIZmc",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -501.4432787214007,
			"y": 168.69212940761014,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 650968929,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 62,
			"versionNonce": 1011289775,
			"isDeleted": false,
			"id": "IVP8Md2-p3xlS0_SajhiP",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -501.4432787214007,
			"y": 168.69212940761014,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 1642571087,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 62,
			"versionNonce": 1517926337,
			"isDeleted": false,
			"id": "2tE5vZIH5kOf7S0Z60r52",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -518.9715292794365,
			"y": 174.99601473127092,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 953503489,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 62,
			"versionNonce": 1827627215,
			"isDeleted": false,
			"id": "9kEgFLMqe-LP_x9fpNQc-",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -518.9715292794365,
			"y": 174.99601473127092,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 35083183,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "rectangle",
			"version": 304,
			"versionNonce": 617551777,
			"isDeleted": false,
			"id": "fNut8LjDRdB8yK85mkBF_",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -599.7393515450614,
			"y": 115.89436503819081,
			"strokeColor": "#5c940d",
			"backgroundColor": "transparent",
			"width": 311.8284388950891,
			"height": 494.97811453683033,
			"seed": 1785119777,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 174,
			"versionNonce": 621309679,
			"isDeleted": false,
			"id": "TMBNqY1COE6KCtp3xBbcq",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -463.40407671246817,
			"y": 822.5608122689378,
			"strokeColor": "#5c940d",
			"backgroundColor": "#82c91e",
			"width": 630,
			"height": 79,
			"seed": 1037958433,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "xqIhdcOt"
				},
				{
					"id": "S0td7f3_MW3HmRldC40So",
					"type": "arrow"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 243,
			"versionNonce": 1630370689,
			"isDeleted": false,
			"id": "r3FJPieculS-Plir8Fgfo",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -464.3176858084503,
			"y": 936.1671081642044,
			"strokeColor": "#5c940d",
			"backgroundColor": "#82c91e",
			"width": 630,
			"height": 79,
			"seed": 1369579681,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "CY5gjQGt"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 287,
			"versionNonce": 790280463,
			"isDeleted": false,
			"id": "e9rqAUiJQDRa3nIGArHMB",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -456.34323828560764,
			"y": 1057.1151584030742,
			"strokeColor": "#5c940d",
			"backgroundColor": "#82c91e",
			"width": 630,
			"height": 79,
			"seed": 592336623,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "7sc5ArIl"
				},
				{
					"id": "RTymf2LRKAzX4J3-Lrsj9",
					"type": "arrow"
				},
				{
					"id": "uCuVkhjHPQBN8gEWDj3Al",
					"type": "arrow"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "text",
			"version": 118,
			"versionNonce": 1876190049,
			"isDeleted": false,
			"id": "xqIhdcOt",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -458.40407671246817,
			"y": 849.5608122689378,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 620,
			"height": 25,
			"seed": 1992786337,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Segment 1",
			"rawText": "Segment 1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "TMBNqY1COE6KCtp3xBbcq",
			"originalText": "Segment 1"
		},
		{
			"type": "text",
			"version": 120,
			"versionNonce": 1889451823,
			"isDeleted": false,
			"id": "CY5gjQGt",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -459.3176858084503,
			"y": 963.1671081642044,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 620,
			"height": 25,
			"seed": 310636559,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "segment 2",
			"rawText": "segment 2",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "r3FJPieculS-Plir8Fgfo",
			"originalText": "segment 2"
		},
		{
			"type": "text",
			"version": 119,
			"versionNonce": 2098417473,
			"isDeleted": false,
			"id": "7sc5ArIl",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -451.34323828560764,
			"y": 1084.1151584030742,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 620,
			"height": 25,
			"seed": 1167849807,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Segment 3",
			"rawText": "Segment 3",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "e9rqAUiJQDRa3nIGArHMB",
			"originalText": "Segment 3"
		},
		{
			"type": "text",
			"version": 181,
			"versionNonce": 201130319,
			"isDeleted": false,
			"id": "GkooDbLZ",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -983.6149835214994,
			"y": 814.6475577664064,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 171,
			"height": 50,
			"seed": 471094817,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "S0td7f3_MW3HmRldC40So",
					"type": "arrow"
				}
			],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "retention.ms=0\nretention.bytes=0",
			"rawText": "retention.ms=0\nretention.bytes=0",
			"baseline": 43,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "retention.ms=0\nretention.bytes=0"
		},
		{
			"type": "arrow",
			"version": 415,
			"versionNonce": 898613060,
			"isDeleted": false,
			"id": "S0td7f3_MW3HmRldC40So",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -802.6533611594865,
			"y": 822.6081516484921,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 327.4529719022026,
			"height": 83.20341971174207,
			"seed": 870148769,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920485,
			"link": null,
			"startBinding": {
				"elementId": "GkooDbLZ",
				"gap": 9.961622362013031,
				"focus": -0.8838003986972989
			},
			"endBinding": {
				"elementId": "TMBNqY1COE6KCtp3xBbcq",
				"gap": 12.538817428578227,
				"focus": -1.060633798314159
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					327.4529719022026,
					83.20341971174207
				]
			]
		},
		{
			"type": "arrow",
			"version": 643,
			"versionNonce": 1045124548,
			"isDeleted": false,
			"id": "uCuVkhjHPQBN8gEWDj3Al",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -199.77991443807286,
			"y": 1148.5753643042515,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 6.114416679998357,
			"height": 260.13291084765797,
			"seed": 1251763407,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920488,
			"link": null,
			"startBinding": {
				"elementId": "e9rqAUiJQDRa3nIGArHMB",
				"gap": 12.460205901177233,
				"focus": 0.18110225161656263
			},
			"endBinding": {
				"elementId": "pw8RYIA9T9MLhq8LjUOta",
				"gap": 16.240670041726432,
				"focus": 0.4177565348894707
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-6.114416679998357,
					260.13291084765797
				]
			]
		},
		{
			"type": "ellipse",
			"version": 250,
			"versionNonce": 1961308929,
			"isDeleted": false,
			"id": "pw8RYIA9T9MLhq8LjUOta",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -323.41006392937356,
			"y": 1418.6343123448362,
			"strokeColor": "#495057",
			"backgroundColor": "#fd7e14",
			"width": 163,
			"height": 147,
			"seed": 1560102017,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "FMYnAWbg"
				},
				{
					"id": "uCuVkhjHPQBN8gEWDj3Al",
					"type": "arrow"
				},
				{
					"id": "RTymf2LRKAzX4J3-Lrsj9",
					"type": "arrow"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "text",
			"version": 147,
			"versionNonce": 379206031,
			"isDeleted": false,
			"id": "FMYnAWbg",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -318.41006392937356,
			"y": 1479.6343123448362,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 153,
			"height": 25,
			"seed": 1239162177,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "SO",
			"rawText": "SO",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "pw8RYIA9T9MLhq8LjUOta",
			"originalText": "SO"
		},
		{
			"type": "rectangle",
			"version": 278,
			"versionNonce": 1651190497,
			"isDeleted": false,
			"id": "zbYS5ybtzhCo4vYAHheCE",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -101.99617898619181,
			"y": 1454.006571311455,
			"strokeColor": "#495057",
			"backgroundColor": "#fd7e14",
			"width": 241,
			"height": 46,
			"seed": 48405071,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "2bwZRW8f"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "text",
			"version": 278,
			"versionNonce": 760885167,
			"isDeleted": false,
			"id": "2bwZRW8f",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -96.99617898619181,
			"y": 1464.506571311455,
			"strokeColor": "#495057",
			"backgroundColor": "#fd7e14",
			"width": 231,
			"height": 25,
			"seed": 22161903,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Flush segments",
			"rawText": "Flush segments",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "zbYS5ybtzhCo4vYAHheCE",
			"originalText": "Flush segments"
		},
		{
			"type": "arrow",
			"version": 563,
			"versionNonce": 962854212,
			"isDeleted": false,
			"id": "RTymf2LRKAzX4J3-Lrsj9",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -283.7209752819108,
			"y": 1414.6675334466447,
			"strokeColor": "#495057",
			"backgroundColor": "#fd7e14",
			"width": 82.58932698306614,
			"height": 265.0408449476897,
			"seed": 1855212225,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920488,
			"link": null,
			"startBinding": {
				"elementId": "pw8RYIA9T9MLhq8LjUOta",
				"gap": 12.903248957532384,
				"focus": -0.2087423095817528
			},
			"endBinding": {
				"elementId": "e9rqAUiJQDRa3nIGArHMB",
				"gap": 13.511530095880744,
				"focus": 0.7377929757868814
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-82.58932698306614,
					-265.0408449476897
				]
			]
		},
		{
			"type": "text",
			"version": 117,
			"versionNonce": 1895195087,
			"isDeleted": false,
			"id": "LBhtXeRp",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -397.9033611594873,
			"y": 1273.008446755347,
			"strokeColor": "#495057",
			"backgroundColor": "#fd7e14",
			"width": 61,
			"height": 50,
			"seed": 1249642177,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "delete\ndata",
			"rawText": "delete\ndata",
			"baseline": 43,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "delete\ndata"
		},
		{
			"type": "text",
			"version": 289,
			"versionNonce": 158639777,
			"isDeleted": false,
			"id": "3WmrKgEj",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -321.808790392706,
			"y": 1652.9399109506012,
			"strokeColor": "#495057",
			"backgroundColor": "#15aabf",
			"width": 200,
			"height": 45,
			"seed": 1110796655,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 36,
			"fontFamily": 1,
			"text": "GROUPING",
			"rawText": "GROUPING",
			"baseline": 32,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "GROUPING"
		},
		{
			"type": "text",
			"version": 51,
			"versionNonce": 2068823137,
			"isDeleted": false,
			"id": "QIJ0e9vG",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -776.9265533670364,
			"y": -289.79168476996256,
			"strokeColor": "#495057",
			"backgroundColor": "#15aabf",
			"width": 521,
			"height": 45,
			"seed": 1723305007,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647277915427,
			"link": null,
			"fontSize": 36,
			"fontFamily": 1,
			"text": "DELETION OF REPLICATED",
			"rawText": "DELETION OF REPLICATED",
			"baseline": 32,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "DELETION OF REPLICATED"
		},
		{
			"type": "rectangle",
			"version": 238,
			"versionNonce": 1481190017,
			"isDeleted": false,
			"id": "DX5tZkXo-9kmLKw9Px80o",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1049.0033329554967,
			"y": 1864.0416810791212,
			"strokeColor": "#343a40",
			"backgroundColor": "#868e96",
			"width": 295,
			"height": 512,
			"seed": 1987652097,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 258,
			"versionNonce": 1436833988,
			"isDeleted": false,
			"id": "kZub_jn2-BamTDnEntidi",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -492.4604388040209,
			"y": 1853.4778627977746,
			"strokeColor": "#343a40",
			"backgroundColor": "#fab005",
			"width": 308.4302571614585,
			"height": 276.51924399724123,
			"seed": 1441457167,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647430902250,
			"link": null
		},
		{
			"type": "text",
			"version": 311,
			"versionNonce": 911286596,
			"isDeleted": false,
			"id": "ZOj8LqiB",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -468.3846279539648,
			"y": 1799.0167162267192,
			"strokeColor": "#495057",
			"backgroundColor": "#15aabf",
			"width": 370,
			"height": 45,
			"seed": 1131110127,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647430899138,
			"link": null,
			"fontSize": 36,
			"fontFamily": 1,
			"text": "SM CONNECT Ticket",
			"rawText": "SM CONNECT Ticket",
			"baseline": 32,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "SM CONNECT Ticket"
		},
		{
			"type": "rectangle",
			"version": 86,
			"versionNonce": 1117347887,
			"isDeleted": false,
			"id": "1g0hgOB0Qadi767o3M1fv",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1025.073559288217,
			"y": 1906.9881992334613,
			"strokeColor": "#343a40",
			"backgroundColor": "#fa5252",
			"width": 192,
			"height": 60,
			"seed": 939223713,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "4PLpqwFkgoWn5YJmgA_7G",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "7JzFxTVt"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 270,
			"versionNonce": 1448033857,
			"isDeleted": false,
			"id": "lTTC86-42GNZ-3mAY0Oxe",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1006.0657249899471,
			"y": 2269.769354955943,
			"strokeColor": "#343a40",
			"backgroundColor": "#82c91e",
			"width": 159,
			"height": 55,
			"seed": 1793068719,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "jVL3Sojx"
				},
				{
					"id": "84BPolyXt4w6TY3XPjLY3",
					"type": "arrow"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 226,
			"versionNonce": 1477305089,
			"isDeleted": false,
			"id": "oi0v4yw76aKW2RzYotZsy",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1013.8429596916078,
			"y": 1992.24424375841,
			"strokeColor": "#343a40",
			"backgroundColor": "#fa5252",
			"width": 196,
			"height": 60,
			"seed": 1531366945,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "lyZVSE2XxkryUwTw_SGqG",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "z3ltELSs"
				}
			],
			"updated": 1647267091169,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 375,
			"versionNonce": 1227869729,
			"isDeleted": false,
			"id": "uEqW9ayjDiFhKaSn4VpvE",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1005.7134464808873,
			"y": 2179.0380837731554,
			"strokeColor": "#343a40",
			"backgroundColor": "#82c91e",
			"width": 163,
			"height": 55,
			"seed": 2079154287,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "z2VIqUWp"
				},
				{
					"id": "ynR3Rt-7mspzKvkAZWdld",
					"type": "arrow"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "diamond",
			"version": 385,
			"versionNonce": 2098487407,
			"isDeleted": false,
			"id": "0XMEJgG2SJXBnmgHS1GD2",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -461.91627193632644,
			"y": 1863.5972713443632,
			"strokeColor": "#343a40",
			"backgroundColor": "#fa5252",
			"width": 155,
			"height": 156,
			"seed": 1748683265,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "4PLpqwFkgoWn5YJmgA_7G",
					"type": "arrow"
				},
				{
					"id": "lyZVSE2XxkryUwTw_SGqG",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "p85DeO0j"
				}
			],
			"updated": 1647266741841,
			"link": null
		},
		{
			"type": "diamond",
			"version": 454,
			"versionNonce": 331406788,
			"isDeleted": false,
			"id": "m7YVrnMlRpeJLwJweyBCP",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -428.80218124389717,
			"y": 2351.5564175715185,
			"strokeColor": "#343a40",
			"backgroundColor": "#40c057",
			"width": 159,
			"height": 144,
			"seed": 1832000847,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "ynR3Rt-7mspzKvkAZWdld",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "BkpOvjyc"
				},
				{
					"id": "84BPolyXt4w6TY3XPjLY3",
					"type": "arrow"
				}
			],
			"updated": 1647430927307,
			"link": null
		},
		{
			"type": "text",
			"version": 98,
			"versionNonce": 1403812495,
			"isDeleted": false,
			"id": "TxjdIghU",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1293.7853597665753,
			"y": 1917.0262911072014,
			"strokeColor": "#343a40",
			"backgroundColor": "#fa5252",
			"width": 207,
			"height": 50,
			"seed": 1272869647,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "TOPICS BY DOMAIN\nKIND OF DATA",
			"rawText": "TOPICS BY DOMAIN\nKIND OF DATA",
			"baseline": 43,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "TOPICS BY DOMAIN\nKIND OF DATA"
		},
		{
			"type": "text",
			"version": 471,
			"versionNonce": 180916932,
			"isDeleted": false,
			"id": "wf7WqvWh",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1247.1022569061652,
			"y": 2409.6630694577707,
			"strokeColor": "#343a40",
			"backgroundColor": "#fa5252",
			"width": 541,
			"height": 225,
			"seed": 951804193,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647430960528,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "CONNECT WORKERS / CLUSTER:\n\nEKS -> SINGLE Deployment\n\nHelm Chart (customize versión) -> replicator CE -> 6.1.1\n\nTopic Regex\n\n",
			"rawText": "CONNECT WORKERS / CLUSTER:\n\nEKS -> SINGLE Deployment\n\nHelm Chart (customize versión) -> replicator CE -> 6.1.1\n\nTopic Regex\n\n",
			"baseline": 218,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "CONNECT WORKERS / CLUSTER:\n\nEKS -> SINGLE Deployment\n\nHelm Chart (customize versión) -> replicator CE -> 6.1.1\n\nTopic Regex\n\n"
		},
		{
			"type": "arrow",
			"version": 882,
			"versionNonce": 1265185476,
			"isDeleted": false,
			"id": "4PLpqwFkgoWn5YJmgA_7G",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -826.0879284548661,
			"y": 1929.179367549481,
			"strokeColor": "#343a40",
			"backgroundColor": "#fa5252",
			"width": 366.59436962253073,
			"height": 7.928640314486074,
			"seed": 1376727791,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920493,
			"link": null,
			"startBinding": {
				"elementId": "1g0hgOB0Qadi767o3M1fv",
				"gap": 6.9856308333510535,
				"focus": -0.312885095327515
			},
			"endBinding": {
				"elementId": "0XMEJgG2SJXBnmgHS1GD2",
				"gap": 1.4455488602482036,
				"focus": 0.036737240545586136
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					366.59436962253073,
					7.928640314486074
				]
			]
		},
		{
			"type": "arrow",
			"version": 1038,
			"versionNonce": 12466756,
			"isDeleted": false,
			"id": "lyZVSE2XxkryUwTw_SGqG",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -811.5408049199226,
			"y": 2018.467238205907,
			"strokeColor": "#343a40",
			"backgroundColor": "#fa5252",
			"width": 369.1409607560203,
			"height": 52.86376482979722,
			"seed": 1398423567,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920494,
			"link": null,
			"startBinding": {
				"elementId": "oi0v4yw76aKW2RzYotZsy",
				"gap": 6.302154771685163,
				"focus": 0.2534351337136428
			},
			"endBinding": {
				"elementId": "0XMEJgG2SJXBnmgHS1GD2",
				"gap": 3.075778469013384,
				"focus": -0.2013143324672609
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					369.1409607560203,
					-52.86376482979722
				]
			]
		},
		{
			"type": "text",
			"version": 88,
			"versionNonce": 757883599,
			"isDeleted": false,
			"id": "7JzFxTVt",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1020.073559288217,
			"y": 1924.4881992334613,
			"strokeColor": "#343a40",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 25,
			"seed": 930915617,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "org.ticket.app1.ids",
			"rawText": "org.ticket.app1.ids",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "1g0hgOB0Qadi767o3M1fv",
			"originalText": "org.ticket.app1.ids"
		},
		{
			"type": "text",
			"version": 113,
			"versionNonce": 1651935631,
			"isDeleted": false,
			"id": "z3ltELSs",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1008.8429596916078,
			"y": 2009.74424375841,
			"strokeColor": "#343a40",
			"backgroundColor": "#868e96",
			"width": 186,
			"height": 25,
			"seed": 1371184175,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647267091169,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "org.ticket.sell",
			"rawText": "org.ticket.sell",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "oi0v4yw76aKW2RzYotZsy",
			"originalText": "org.ticket.sell"
		},
		{
			"type": "text",
			"version": 338,
			"versionNonce": 441790703,
			"isDeleted": false,
			"id": "p85DeO0j",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -456.91627193632644,
			"y": 1929.0972713443632,
			"strokeColor": "#343a40",
			"backgroundColor": "#868e96",
			"width": 145,
			"height": 25,
			"seed": 1814260353,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "org.ticket.*",
			"rawText": "org.ticket.*",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "0XMEJgG2SJXBnmgHS1GD2",
			"originalText": "org.ticket.*"
		},
		{
			"type": "text",
			"version": 93,
			"versionNonce": 1166399873,
			"isDeleted": false,
			"id": "z2VIqUWp",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1000.7134464808873,
			"y": 2194.0380837731554,
			"strokeColor": "#343a40",
			"backgroundColor": "#82c91e",
			"width": 153,
			"height": 25,
			"seed": 1721007727,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "users.id",
			"rawText": "users.id",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "uEqW9ayjDiFhKaSn4VpvE",
			"originalText": "users.id"
		},
		{
			"type": "text",
			"version": 85,
			"versionNonce": 561986319,
			"isDeleted": false,
			"id": "jVL3Sojx",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1001.0657249899471,
			"y": 2284.769354955943,
			"strokeColor": "#343a40",
			"backgroundColor": "#82c91e",
			"width": 149,
			"height": 25,
			"seed": 549198223,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "users.tx",
			"rawText": "users.tx",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "lTTC86-42GNZ-3mAY0Oxe",
			"originalText": "users.tx"
		},
		{
			"type": "arrow",
			"version": 1076,
			"versionNonce": 1304453372,
			"isDeleted": false,
			"id": "ynR3Rt-7mspzKvkAZWdld",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -837.9644642178758,
			"y": 2206.6387503627875,
			"strokeColor": "#343a40",
			"backgroundColor": "#82c91e",
			"width": 435.30760832267856,
			"height": 184.3365641975497,
			"seed": 904813199,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430927307,
			"link": null,
			"startBinding": {
				"elementId": "uEqW9ayjDiFhKaSn4VpvE",
				"gap": 4.748982263011499,
				"focus": -0.5876194862817274
			},
			"endBinding": {
				"elementId": "m7YVrnMlRpeJLwJweyBCP",
				"gap": 6.598436880248194,
				"focus": 0.13871367868795512
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					435.30760832267856,
					184.3365641975497
				]
			]
		},
		{
			"type": "arrow",
			"version": 953,
			"versionNonce": 763552124,
			"isDeleted": false,
			"id": "84BPolyXt4w6TY3XPjLY3",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -887.9904405584231,
			"y": 2328.6978549437536,
			"strokeColor": "#343a40",
			"backgroundColor": "#82c91e",
			"width": 461.01050892904055,
			"height": 84.53436451442576,
			"seed": 1004881871,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430927307,
			"link": null,
			"startBinding": {
				"elementId": "lTTC86-42GNZ-3mAY0Oxe",
				"gap": 3.9284999878108238,
				"focus": 0.5779926045587014
			},
			"endBinding": {
				"elementId": "m7YVrnMlRpeJLwJweyBCP",
				"gap": 6.429102107411012,
				"focus": -0.054435835464707594
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					461.01050892904055,
					84.53436451442576
				]
			]
		},
		{
			"type": "text",
			"version": 306,
			"versionNonce": 731369596,
			"isDeleted": false,
			"id": "BkpOvjyc",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -423.80218124389717,
			"y": 2411.0564175715185,
			"strokeColor": "#343a40",
			"backgroundColor": "#82c91e",
			"width": 149,
			"height": 25,
			"seed": 434851023,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647430927307,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "org.user.*",
			"rawText": "org.user.*",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "m7YVrnMlRpeJLwJweyBCP",
			"originalText": "org.user.*"
		},
		{
			"type": "text",
			"version": 1104,
			"versionNonce": 1823015759,
			"isDeleted": false,
			"id": "5b5ykhcK",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -143.84110811572054,
			"y": 1867.548812082468,
			"strokeColor": "#343a40",
			"backgroundColor": "#fa5252",
			"width": 1613,
			"height": 200,
			"seed": 758730159,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "DOD of Topic Migration:\n\n1. Define the regex the most specific -> TOPIC REGEX NEVER WHITELIST\n2. Define the sizing of the dedicated cluster -> reach desired throughput (send some example)\n3. Start Replicate -> Check the replicator lag < 10msg- few hundreds ms -> Enable prometheus exporter -> provide dashboard -> METRICS API AS WORKAROUND\n4. Check lag message to be 0, -1 -> REPLICATION FINISHED\n5. Check Consumer offsets translation -> is consumer offset reset needed? \n6. Check Consumer consumption.",
			"rawText": "DOD of Topic Migration:\n\n1. Define the regex the most specific -> TOPIC REGEX NEVER WHITELIST\n2. Define the sizing of the dedicated cluster -> reach desired throughput (send some example)\n3. Start Replicate -> Check the replicator lag < 10msg- few hundreds ms -> Enable prometheus exporter -> provide dashboard -> METRICS API AS WORKAROUND\n4. Check lag message to be 0, -1 -> REPLICATION FINISHED\n5. Check Consumer offsets translation -> is consumer offset reset needed? \n6. Check Consumer consumption.",
			"baseline": 193,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "DOD of Topic Migration:\n\n1. Define the regex the most specific -> TOPIC REGEX NEVER WHITELIST\n2. Define the sizing of the dedicated cluster -> reach desired throughput (send some example)\n3. Start Replicate -> Check the replicator lag < 10msg- few hundreds ms -> Enable prometheus exporter -> provide dashboard -> METRICS API AS WORKAROUND\n4. Check lag message to be 0, -1 -> REPLICATION FINISHED\n5. Check Consumer offsets translation -> is consumer offset reset needed? \n6. Check Consumer consumption."
		},
		{
			"type": "text",
			"version": 585,
			"versionNonce": 1356258593,
			"isDeleted": false,
			"id": "VjxdSfEn",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -145.1773217271416,
			"y": 2091.433327753971,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#82c91e",
			"width": 1194,
			"height": 150,
			"seed": 1609633487,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741841,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "1. Don't change consumer group Ids\n2. Be aware of internal offsets\n3. Template and version your config and env files\n4. Be aware that the replication by default start from beginning -> configure from latest of specific offset\n5. Connect Cluster Size Recommendation (send \"official\" data) -> NOT OUT OF MEMORY BUT SERVICE DEGRADATION\n",
			"rawText": "1. Don't change consumer group Ids\n2. Be aware of internal offsets\n3. Template and version your config and env files\n4. Be aware that the replication by default start from beginning -> configure from latest of specific offset\n5. Connect Cluster Size Recommendation (send \"official\" data) -> NOT OUT OF MEMORY BUT SERVICE DEGRADATION\n",
			"baseline": 143,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "1. Don't change consumer group Ids\n2. Be aware of internal offsets\n3. Template and version your config and env files\n4. Be aware that the replication by default start from beginning -> configure from latest of specific offset\n5. Connect Cluster Size Recommendation (send \"official\" data) -> NOT OUT OF MEMORY BUT SERVICE DEGRADATION\n"
		},
		{
			"type": "text",
			"version": 514,
			"versionNonce": 889180769,
			"isDeleted": false,
			"id": "cPa6wspa",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -823.5347508682321,
			"y": 2670.3697523726473,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 960,
			"height": 35,
			"seed": 378876015,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647267419114,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "CONSUMER PART -> APPLICATION -> Decoupling consuming migration",
			"rawText": "CONSUMER PART -> APPLICATION -> Decoupling consuming migration",
			"baseline": 25,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "CONSUMER PART -> APPLICATION -> Decoupling consuming migration"
		},
		{
			"type": "rectangle",
			"version": 317,
			"versionNonce": 764674799,
			"isDeleted": false,
			"id": "A98ZKyTLAkiozi8EnVWjr",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -344.96527225114255,
			"y": 2915.807508741647,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 217.96530983664775,
			"height": 374.47548606179,
			"seed": 981179087,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266759306,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 219,
			"versionNonce": 430816143,
			"isDeleted": false,
			"id": "yw2FJ5PAIeu7Uvr-Z_Ifj",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1199.4561835615123,
			"y": 2878.6415290287446,
			"strokeColor": "#495057",
			"backgroundColor": "#ced4da",
			"width": 217.96530983664775,
			"height": 374.47548606179,
			"seed": 1592170881,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "diamond",
			"version": 138,
			"versionNonce": 1841875169,
			"isDeleted": false,
			"id": "zLrJLxzVbhmoQQXu4pDvH",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -736.0383020045979,
			"y": 2985.7836736348722,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 231,
			"height": 160,
			"seed": 1993202351,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "AqO2sahMhJv0i-dBMyiBh",
					"type": "arrow"
				},
				{
					"id": "OuZgC-thmVT35BQZ8Axcj",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "OhDE85D7vpgP8ZwTO1pBv"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 117,
			"versionNonce": 132506831,
			"isDeleted": false,
			"id": "zgYl3devlzK9hMP2CM0tv",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1132.8585701153936,
			"y": 2934.169745658822,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 108,
			"height": 49,
			"seed": 1727119087,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "AqO2sahMhJv0i-dBMyiBh",
					"type": "arrow"
				},
				{
					"id": "L1sadhJUkunYN_8LTPwCQ",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "y1v4ZTIz"
				},
				{
					"id": "iSByU35WwFcXcCE82qCaC",
					"type": "arrow"
				},
				{
					"id": "F2hJgOCRxH6N-0zxE359w",
					"type": "arrow"
				},
				{
					"id": "GJBMRyiQRdDF4QUWB4G8q",
					"type": "arrow"
				}
			],
			"updated": 1647267494736,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 42,
			"versionNonce": 1516176577,
			"isDeleted": false,
			"id": "2diJURSgKrkZ3krQSqplj",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1457.280610187748,
			"y": 2891.1402854068892,
			"strokeColor": "#495057",
			"backgroundColor": "#ced4da",
			"width": 107,
			"height": 80,
			"seed": 1355089185,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "GJBMRyiQRdDF4QUWB4G8q",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "YwPeLWdn"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "arrow",
			"version": 372,
			"versionNonce": 566054852,
			"isDeleted": false,
			"id": "AqO2sahMhJv0i-dBMyiBh",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1023.8547041375039,
			"y": 2952.925739860533,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 344.6533271983395,
			"height": 72.26921364335885,
			"seed": 1313420673,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920496,
			"link": null,
			"startBinding": {
				"elementId": "zgYl3devlzK9hMP2CM0tv",
				"gap": 1.0038659778896986,
				"focus": -0.4823031308018715
			},
			"endBinding": {
				"elementId": "zLrJLxzVbhmoQQXu4pDvH",
				"gap": 1.0038659778896986,
				"focus": 0.3535981802241294
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					344.6533271983395,
					72.26921364335885
				]
			]
		},
		{
			"type": "rectangle",
			"version": 210,
			"versionNonce": 1587163457,
			"isDeleted": false,
			"id": "mzZ4KBPDDxxlWlQEDvlgw",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -285.1976247239795,
			"y": 2970.9062279130053,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 121,
			"height": 55,
			"seed": 76213903,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "fprRuSVIcqE3z-fZWOAny",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "16axGZ61"
				},
				{
					"id": "IISHeBKLET2ikq5ZjNDK6",
					"type": "arrow"
				},
				{
					"id": "OuZgC-thmVT35BQZ8Axcj",
					"type": "arrow"
				}
			],
			"updated": 1647266768844,
			"link": null
		},
		{
			"type": "arrow",
			"version": 202,
			"versionNonce": 1359836228,
			"isDeleted": false,
			"id": "OuZgC-thmVT35BQZ8Axcj",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -561.9676370322045,
			"y": 3025.1335512577634,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 267.9513127139127,
			"height": 27.84640421430049,
			"seed": 1546924673,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920499,
			"link": null,
			"startBinding": {
				"elementId": "zLrJLxzVbhmoQQXu4pDvH",
				"gap": 1.0017247853920281,
				"focus": -0.4320407664364629
			},
			"endBinding": {
				"elementId": "mzZ4KBPDDxxlWlQEDvlgw",
				"gap": 8.81869959431225,
				"focus": 0.2463321473206988
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					267.9513127139127,
					-27.84640421430049
				]
			]
		},
		{
			"type": "arrow",
			"version": 108,
			"versionNonce": 1159656772,
			"isDeleted": false,
			"id": "GJBMRyiQRdDF4QUWB4G8q",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1346.1651319524844,
			"y": 2931.6459511025905,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 207.69385764675008,
			"height": 29.335504181784472,
			"seed": 219610703,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920498,
			"link": null,
			"startBinding": {
				"elementId": "2diJURSgKrkZ3krQSqplj",
				"gap": 4.1192628567825595,
				"focus": -0.18748807535970263
			},
			"endBinding": {
				"elementId": "zgYl3devlzK9hMP2CM0tv",
				"gap": 5.612704190340764,
				"focus": -0.33403636240726936
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					207.69385764675008,
					29.335504181784472
				]
			]
		},
		{
			"type": "diamond",
			"version": 125,
			"versionNonce": 1347943439,
			"isDeleted": false,
			"id": "blArqm5WGYYmgdJgrB-Pn",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -734.4895071715016,
			"y": 3380.2728801223543,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 113,
			"seed": 1894788513,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "L1sadhJUkunYN_8LTPwCQ",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "0xAwo6ckrDxPQZJFxuGVp"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "arrow",
			"version": 364,
			"versionNonce": 459164612,
			"isDeleted": false,
			"id": "L1sadhJUkunYN_8LTPwCQ",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1024.3964414979148,
			"y": 2984.8536164964025,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 350.8591899241169,
			"height": 403.71360753664703,
			"seed": 1061356065,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920499,
			"link": null,
			"startBinding": {
				"elementId": "zgYl3devlzK9hMP2CM0tv",
				"gap": 1.7461339744551172,
				"focus": -0.4211081844861841
			},
			"endBinding": {
				"elementId": "blArqm5WGYYmgdJgrB-Pn",
				"gap": 8.802946358652527,
				"focus": 0.13018460962759806
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					350.8591899241169,
					403.71360753664703
				]
			]
		},
		{
			"type": "text",
			"version": 26,
			"versionNonce": 524001839,
			"isDeleted": false,
			"id": "SR4BDmC6",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -731.0383020045979,
			"y": 3053.2836736348722,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 221,
			"height": 25,
			"seed": 1979871585,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Replicator",
			"rawText": "Replicator",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "zLrJLxzVbhmoQQXu4pDvH",
			"originalText": "Replicator"
		},
		{
			"type": "text",
			"version": 86,
			"versionNonce": 1867260463,
			"isDeleted": false,
			"id": "y1v4ZTIz",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1127.8585701153936,
			"y": 2946.169745658822,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 98,
			"height": 25,
			"seed": 2072094593,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647267494534,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Or Topic",
			"rawText": "Or Topic",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "zgYl3devlzK9hMP2CM0tv",
			"originalText": "Or Topic"
		},
		{
			"type": "text",
			"version": 162,
			"versionNonce": 450734945,
			"isDeleted": false,
			"id": "16axGZ61",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -280.1976247239795,
			"y": 2985.9062279130053,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 111,
			"height": 25,
			"seed": 1070422337,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266764074,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Re Topic",
			"rawText": "Re Topic",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "mzZ4KBPDDxxlWlQEDvlgw",
			"originalText": "Re Topic"
		},
		{
			"type": "text",
			"version": 12,
			"versionNonce": 88009761,
			"isDeleted": false,
			"id": "YwPeLWdn",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1452.280610187748,
			"y": 2918.6402854068892,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 97,
			"height": 25,
			"seed": 1328453665,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "P1",
			"rawText": "P1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "2diJURSgKrkZ3krQSqplj",
			"originalText": "P1"
		},
		{
			"type": "diamond",
			"version": 163,
			"versionNonce": 596517487,
			"isDeleted": false,
			"id": "ZdCsAQrK7-3O5AgpzJqeG",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -999.4553737227561,
			"y": 3443.327361397506,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 113,
			"seed": 1506948929,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "L1sadhJUkunYN_8LTPwCQ",
					"type": "arrow"
				},
				{
					"id": "iSByU35WwFcXcCE82qCaC",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "8g1zt0fTOyiaYiSNY5ad3"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "diamond",
			"version": 400,
			"versionNonce": 1034862593,
			"isDeleted": false,
			"id": "S8QB6-Fa2BZm8wJi2pOKe",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -539.1136820739491,
			"y": 3314.7046188738404,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 113,
			"seed": 862448865,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "L1sadhJUkunYN_8LTPwCQ",
					"type": "arrow"
				},
				{
					"id": "F2hJgOCRxH6N-0zxE359w",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "6tPtJkhv"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "arrow",
			"version": 330,
			"versionNonce": 615002948,
			"isDeleted": false,
			"id": "iSByU35WwFcXcCE82qCaC",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1048.1016499620127,
			"y": 2986.276180875157,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 119.1816085682292,
			"height": 468.57588117539353,
			"seed": 1720260719,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920499,
			"link": null,
			"startBinding": {
				"elementId": "zgYl3devlzK9hMP2CM0tv",
				"gap": 3.106435216334787,
				"focus": -0.3940670052314997
			},
			"endBinding": {
				"elementId": "ZdCsAQrK7-3O5AgpzJqeG",
				"gap": 1.003666270430319,
				"focus": -0.09917872840004392
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					119.1816085682292,
					468.57588117539353
				]
			]
		},
		{
			"type": "arrow",
			"version": 576,
			"versionNonce": 1263086276,
			"isDeleted": false,
			"id": "F2hJgOCRxH6N-0zxE359w",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1023.3518096961275,
			"y": 2938.808553871725,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 540.7775710007672,
			"height": 392.678989158062,
			"seed": 1196564943,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920500,
			"link": null,
			"startBinding": {
				"elementId": "zgYl3devlzK9hMP2CM0tv",
				"gap": 1.506760419266129,
				"focus": -0.9443633225307446
			},
			"endBinding": {
				"elementId": "S8QB6-Fa2BZm8wJi2pOKe",
				"gap": 3.918985935792189,
				"focus": 0.22237125432390212
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					540.7775710007672,
					392.678989158062
				]
			]
		},
		{
			"type": "text",
			"version": 77,
			"versionNonce": 1894617775,
			"isDeleted": false,
			"id": "6tPtJkhv",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -534.1136820739491,
			"y": 3358.7046188738404,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 1946634881,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "C1",
			"rawText": "C1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "S8QB6-Fa2BZm8wJi2pOKe",
			"originalText": "C1"
		},
		{
			"type": "text",
			"version": 11,
			"versionNonce": 208499649,
			"isDeleted": false,
			"id": "IjWrjbW4",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -729.4895071715016,
			"y": 3424.2728801223543,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 1803043535,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "C2",
			"rawText": "C2",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "blArqm5WGYYmgdJgrB-Pn",
			"originalText": "C2"
		},
		{
			"type": "text",
			"version": 14,
			"versionNonce": 1378182351,
			"isDeleted": false,
			"id": "grlywUe0",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -994.4553737227561,
			"y": 3487.327361397506,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 1566783457,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Cn",
			"rawText": "Cn",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "ZdCsAQrK7-3O5AgpzJqeG",
			"originalText": "Cn"
		},
		{
			"type": "text",
			"version": 223,
			"versionNonce": 1491817377,
			"isDeleted": false,
			"id": "TnhtUYyZ",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -731.5666378401068,
			"y": 3684.1756576922035,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 452,
			"height": 27,
			"seed": 93603279,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "gjcN6rkqwUTrA77AlkfXj",
					"type": "arrow"
				}
			],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 21.76515706380207,
			"fontFamily": 1,
			"text": "REPLICATION FINISHED (Point 4 above)",
			"rawText": "REPLICATION FINISHED (Point 4 above)",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "REPLICATION FINISHED (Point 4 above)"
		},
		{
			"type": "text",
			"version": 680,
			"versionNonce": 131146529,
			"isDeleted": false,
			"id": "sBf5GdBh",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1328.0028362411797,
			"y": 3904.907418353011,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 778,
			"height": 300,
			"seed": 294342607,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "gjcN6rkqwUTrA77AlkfXj",
					"type": "arrow"
				}
			],
			"updated": 1647267337945,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "REPLICATOR STILL WORKING\n\nStart with Consumers\n\n1. Identify our CG (we cannot asume any naming convention)\n2. Check if offset translation needed\n   2.1 If we got naming conventions it will be easier to automate (just regex)\n   2.2 Script reading some file (f.e CSV) with the CG and execute translation\n3. Change Consumer Config to new cluster.\n\n\n",
			"rawText": "REPLICATOR STILL WORKING\n\nStart with Consumers\n\n1. Identify our CG (we cannot asume any naming convention)\n2. Check if offset translation needed\n   2.1 If we got naming conventions it will be easier to automate (just regex)\n   2.2 Script reading some file (f.e CSV) with the CG and execute translation\n3. Change Consumer Config to new cluster.\n\n\n",
			"baseline": 293,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "REPLICATOR STILL WORKING\n\nStart with Consumers\n\n1. Identify our CG (we cannot asume any naming convention)\n2. Check if offset translation needed\n   2.1 If we got naming conventions it will be easier to automate (just regex)\n   2.2 Script reading some file (f.e CSV) with the CG and execute translation\n3. Change Consumer Config to new cluster.\n\n\n"
		},
		{
			"type": "arrow",
			"version": 1010,
			"versionNonce": 1046624129,
			"isDeleted": false,
			"id": "gjcN6rkqwUTrA77AlkfXj",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -544.9878564073811,
			"y": 3717.4225855893387,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 345.6385945162042,
			"height": 183.29533894856786,
			"seed": 1044927457,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"startBinding": {
				"elementId": "YsnCDGalicMXGvml7ELjJ",
				"focus": 0.01065496067021532,
				"gap": 6.246927897135265
			},
			"endBinding": {
				"elementId": "MWuJVWaVxoA-tnxPuu_Tv",
				"focus": -0.36069830467405467,
				"gap": 4.18949381510447
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-345.6385945162042,
					183.29533894856786
				]
			]
		},
		{
			"type": "diamond",
			"version": 404,
			"versionNonce": 2040511759,
			"isDeleted": false,
			"id": "zOdDSFBX0STyoi4iY7to2",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 123.6386233903595,
			"y": 2786.53746392593,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 182,
			"height": 113,
			"seed": 480960015,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "L1sadhJUkunYN_8LTPwCQ",
					"type": "arrow"
				},
				{
					"id": "F2hJgOCRxH6N-0zxE359w",
					"type": "arrow"
				},
				{
					"id": "B48owMoo",
					"type": "text"
				},
				{
					"id": "IISHeBKLET2ikq5ZjNDK6",
					"type": "arrow"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "text",
			"version": 78,
			"versionNonce": 1058119521,
			"isDeleted": false,
			"id": "B48owMoo",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 128.6386233903595,
			"y": 2830.53746392593,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 2122649185,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "C1",
			"rawText": "C1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "zOdDSFBX0STyoi4iY7to2",
			"originalText": "C1"
		},
		{
			"type": "arrow",
			"version": 416,
			"versionNonce": 1910452804,
			"isDeleted": false,
			"id": "IISHeBKLET2ikq5ZjNDK6",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -156.58667395715975,
			"y": 2974.7166496118375,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 312.3093720536277,
			"height": 107.55789915970627,
			"seed": 416087041,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920501,
			"link": null,
			"startBinding": {
				"elementId": "mzZ4KBPDDxxlWlQEDvlgw",
				"gap": 7.610950766819769,
				"focus": -0.004809731088096475
			},
			"endBinding": {
				"elementId": "zOdDSFBX0STyoi4iY7to2",
				"gap": 3.5689966002637377,
				"focus": -0.06780380748715803
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					312.3093720536277,
					-107.55789915970627
				]
			]
		},
		{
			"type": "rectangle",
			"version": 163,
			"versionNonce": 1766394689,
			"isDeleted": false,
			"id": "uYZoZsAohxsRleZcXWQz-",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -218.21460147583593,
			"y": 4572.706833988851,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 217.96530983664775,
			"height": 374.47548606179,
			"seed": 1011495471,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 248,
			"versionNonce": 1947272527,
			"isDeleted": false,
			"id": "X7lJjl0bEzhcZuHzDbf0i",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1215.8402784112054,
			"y": 4576.788726522585,
			"strokeColor": "#495057",
			"backgroundColor": "#ced4da",
			"width": 217.96530983664775,
			"height": 374.47548606179,
			"seed": 709659713,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "diamond",
			"version": 169,
			"versionNonce": 1432877857,
			"isDeleted": false,
			"id": "OJNy0dF70qmcFHdZV9j-5",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -752.4223968542915,
			"y": 4683.930871128713,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 231,
			"height": 160,
			"seed": 1472336975,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "nVLNRWYLHOcWANIanWZXy",
					"type": "arrow"
				},
				{
					"id": "3bqFRjo2iOAlpO_fvKpCh",
					"type": "arrow"
				},
				{
					"id": "GjXdcAwg",
					"type": "text"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 92,
			"versionNonce": 293700463,
			"isDeleted": false,
			"id": "boGi1nYtZsO2ikb0OlcvL",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1155.719715746337,
			"y": 4632.31131435492,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 108,
			"height": 49,
			"seed": 206789665,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "nVLNRWYLHOcWANIanWZXy",
					"type": "arrow"
				},
				{
					"id": "HeenACvOZTWzRY9Rncy9x",
					"type": "arrow"
				},
				{
					"id": "thBql2xg",
					"type": "text"
				},
				{
					"id": "BJvO0lyUYxamTKDjWZ7rh",
					"type": "arrow"
				},
				{
					"id": "SNhrW36U736I658mHDo6u",
					"type": "arrow"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 73,
			"versionNonce": 1675489025,
			"isDeleted": false,
			"id": "bE0_Dort6cwje_Wi_KNRC",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1473.6647050374413,
			"y": 4589.2874829007305,
			"strokeColor": "#495057",
			"backgroundColor": "#ced4da",
			"width": 107,
			"height": 80,
			"seed": 745966735,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "ciUQFF5avMWTuiIU9ZuZA",
					"type": "arrow"
				},
				{
					"id": "jQZ28Omn",
					"type": "text"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "arrow",
			"version": 352,
			"versionNonce": 1464576196,
			"isDeleted": false,
			"id": "nVLNRWYLHOcWANIanWZXy",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1046.7158497684472,
			"y": 4650.978036515406,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 350.92927830106373,
			"height": 72.50340430295182,
			"seed": 2090329025,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920503,
			"link": null,
			"startBinding": {
				"elementId": "boGi1nYtZsO2ikb0OlcvL",
				"gap": 1.0038659778897454,
				"focus": -0.48230313080187603
			},
			"endBinding": {
				"elementId": "OJNy0dF70qmcFHdZV9j-5",
				"gap": 1.0038659778894896,
				"focus": 0.3535981802241229
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					350.92927830106373,
					72.50340430295182
				]
			]
		},
		{
			"type": "rectangle",
			"version": 173,
			"versionNonce": 992634593,
			"isDeleted": false,
			"id": "-UVrHTij4X-cmVlR-K1VZ",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -151.58483914832573,
			"y": 4606.380017746256,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 121,
			"height": 55,
			"seed": 605680847,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "fprRuSVIcqE3z-fZWOAny",
					"type": "arrow"
				},
				{
					"id": "cabVEMfb",
					"type": "text"
				},
				{
					"id": "EQCBFreRXs-Y4uhI2HO2O",
					"type": "arrow"
				},
				{
					"id": "3bqFRjo2iOAlpO_fvKpCh",
					"type": "arrow"
				},
				{
					"id": "NDb4M5Z4AtnTeOzEZWQhN",
					"type": "arrow"
				},
				{
					"id": "jwgyaps_41hfSGG8hE-XG",
					"type": "arrow"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "arrow",
			"version": 176,
			"versionNonce": 915818492,
			"isDeleted": false,
			"id": "3bqFRjo2iOAlpO_fvKpCh",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -578.351731881899,
			"y": 4723.280748751604,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 420.32941492817565,
			"height": 88.15600222338799,
			"seed": 977781665,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920505,
			"link": null,
			"startBinding": {
				"elementId": "OJNy0dF70qmcFHdZV9j-5",
				"gap": 1.0017247853917155,
				"focus": -0.35457566317464606
			},
			"endBinding": {
				"elementId": "-UVrHTij4X-cmVlR-K1VZ",
				"gap": 6.437477805397634,
				"focus": 0.318351058889448
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					420.32941492817565,
					-88.15600222338799
				]
			]
		},
		{
			"type": "arrow",
			"version": 149,
			"versionNonce": 1507514108,
			"isDeleted": false,
			"id": "ciUQFF5avMWTuiIU9ZuZA",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1362.5492538294245,
			"y": 4629.793144778996,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 207.693884673997,
			"height": 29.335507999219317,
			"seed": 1103135471,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920504,
			"link": null,
			"startBinding": {
				"elementId": "bE0_Dort6cwje_Wi_KNRC",
				"gap": 4.119235775259057,
				"focus": -0.18748807535970233
			},
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					207.693884673997,
					29.335507999219317
				]
			]
		},
		{
			"type": "diamond",
			"version": 156,
			"versionNonce": 2095605199,
			"isDeleted": false,
			"id": "AvwwaFF1d7xwH-YfBXFvy",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -750.873602021195,
			"y": 5078.420077616195,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 113,
			"seed": 447784833,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "HeenACvOZTWzRY9Rncy9x",
					"type": "arrow"
				},
				{
					"id": "LuhJ1ukj",
					"type": "text"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "arrow",
			"version": 344,
			"versionNonce": 1758001532,
			"isDeleted": false,
			"id": "HeenACvOZTWzRY9Rncy9x",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1047.039219664186,
			"y": 4682.694493328953,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 356.705179523409,
			"height": 404.27616111031693,
			"seed": 598756623,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920506,
			"link": null,
			"startBinding": {
				"elementId": "boGi1nYtZsO2ikb0OlcvL",
				"gap": 1.5415119175768268,
				"focus": -0.4211081844861867
			},
			"endBinding": {
				"elementId": "AvwwaFF1d7xwH-YfBXFvy",
				"gap": 8.802946358652669,
				"focus": 0.1301846096275957
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					356.705179523409,
					404.27616111031693
				]
			]
		},
		{
			"type": "text",
			"version": 56,
			"versionNonce": 966317039,
			"isDeleted": false,
			"id": "GjXdcAwg",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -747.4223968542915,
			"y": 4751.430871128713,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 221,
			"height": 25,
			"seed": 1746014049,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Replicator",
			"rawText": "Replicator",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "OJNy0dF70qmcFHdZV9j-5",
			"originalText": "Replicator"
		},
		{
			"type": "text",
			"version": 61,
			"versionNonce": 1744955009,
			"isDeleted": false,
			"id": "thBql2xg",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1150.719715746337,
			"y": 4644.31131435492,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 98,
			"height": 25,
			"seed": 1700909871,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Or Topic",
			"rawText": "Or Topic",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "boGi1nYtZsO2ikb0OlcvL",
			"originalText": "Or Topic"
		},
		{
			"type": "text",
			"version": 122,
			"versionNonce": 2119597583,
			"isDeleted": false,
			"id": "cabVEMfb",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -146.58483914832573,
			"y": 4621.380017746256,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 111,
			"height": 25,
			"seed": 2025552705,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Re Topic",
			"rawText": "Re Topic",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "-UVrHTij4X-cmVlR-K1VZ",
			"originalText": "Re Topic"
		},
		{
			"type": "text",
			"version": 42,
			"versionNonce": 384003681,
			"isDeleted": false,
			"id": "jQZ28Omn",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1468.6647050374413,
			"y": 4616.7874829007305,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 97,
			"height": 25,
			"seed": 524129615,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "P1",
			"rawText": "P1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "bE0_Dort6cwje_Wi_KNRC",
			"originalText": "P1"
		},
		{
			"type": "diamond",
			"version": 194,
			"versionNonce": 1310319663,
			"isDeleted": false,
			"id": "Bh_qcKB3fY6jCEeVtKn1K",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1015.8394685724493,
			"y": 5141.4745588913465,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 113,
			"seed": 755788577,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "HeenACvOZTWzRY9Rncy9x",
					"type": "arrow"
				},
				{
					"id": "BJvO0lyUYxamTKDjWZ7rh",
					"type": "arrow"
				},
				{
					"id": "EJhIKxqq",
					"type": "text"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "diamond",
			"version": 431,
			"versionNonce": 604568129,
			"isDeleted": false,
			"id": "AFqSNOQf1qXaYMwouwyo6",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -555.4977769236425,
			"y": 5012.851816367682,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 113,
			"seed": 1218188143,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "HeenACvOZTWzRY9Rncy9x",
					"type": "arrow"
				},
				{
					"id": "SNhrW36U736I658mHDo6u",
					"type": "arrow"
				},
				{
					"id": "k5j5aWkb",
					"type": "text"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "arrow",
			"version": 310,
			"versionNonce": 2095344124,
			"isDeleted": false,
			"id": "BJvO0lyUYxamTKDjWZ7rh",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1070.5247405712673,
			"y": 4684.417749571255,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 124.76683016489301,
			"height": 468.86324887647334,
			"seed": 762601217,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920507,
			"link": null,
			"startBinding": {
				"elementId": "boGi1nYtZsO2ikb0OlcvL",
				"gap": 3.1064352163348303,
				"focus": -0.39406700523149635
			},
			"endBinding": {
				"elementId": "Bh_qcKB3fY6jCEeVtKn1K",
				"gap": 1.003666270430493,
				"focus": -0.09917872840004027
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					124.76683016489301,
					468.86324887647334
				]
			]
		},
		{
			"type": "arrow",
			"version": 610,
			"versionNonce": 1295895164,
			"isDeleted": false,
			"id": "SNhrW36U736I658mHDo6u",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1044.6523995873702,
			"y": 4667.031014111168,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 547.7761382707564,
			"height": 361.31101123766075,
			"seed": 1506135439,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920508,
			"link": null,
			"startBinding": {
				"elementId": "boGi1nYtZsO2ikb0OlcvL",
				"gap": 3.0673161589668325,
				"focus": -0.45612950246362693
			},
			"endBinding": {
				"elementId": "AFqSNOQf1qXaYMwouwyo6",
				"gap": 3.9189859357918166,
				"focus": 0.3274242868605408
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					547.7761382707564,
					361.31101123766075
				]
			]
		},
		{
			"type": "text",
			"version": 107,
			"versionNonce": 1027859567,
			"isDeleted": false,
			"id": "k5j5aWkb",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -550.4977769236425,
			"y": 5056.851816367682,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 1752356577,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "C1",
			"rawText": "C1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "AFqSNOQf1qXaYMwouwyo6",
			"originalText": "C1"
		},
		{
			"type": "text",
			"version": 41,
			"versionNonce": 346955265,
			"isDeleted": false,
			"id": "LuhJ1ukj",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -745.873602021195,
			"y": 5122.420077616195,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 770917295,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "C2",
			"rawText": "C2",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "AvwwaFF1d7xwH-YfBXFvy",
			"originalText": "C2"
		},
		{
			"type": "text",
			"version": 44,
			"versionNonce": 1800109711,
			"isDeleted": false,
			"id": "EJhIKxqq",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1010.8394685724493,
			"y": 5185.4745588913465,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 1352094401,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Cn",
			"rawText": "Cn",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Bh_qcKB3fY6jCEeVtKn1K",
			"originalText": "Cn"
		},
		{
			"type": "diamond",
			"version": 436,
			"versionNonce": 1612494305,
			"isDeleted": false,
			"id": "OCRMkPI-ja-rlTMji_SBg",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 107.25452854066612,
			"y": 4484.684661419771,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 182,
			"height": 113,
			"seed": 625800655,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "HeenACvOZTWzRY9Rncy9x",
					"type": "arrow"
				},
				{
					"id": "SNhrW36U736I658mHDo6u",
					"type": "arrow"
				},
				{
					"id": "hffzEhal",
					"type": "text"
				},
				{
					"id": "EQCBFreRXs-Y4uhI2HO2O",
					"type": "arrow"
				}
			],
			"updated": 1647266741842,
			"link": null
		},
		{
			"type": "text",
			"version": 108,
			"versionNonce": 1167774895,
			"isDeleted": false,
			"id": "hffzEhal",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 112.25452854066612,
			"y": 4528.684661419771,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 1518424737,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "C1",
			"rawText": "C1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "OCRMkPI-ja-rlTMji_SBg",
			"originalText": "C1"
		},
		{
			"type": "arrow",
			"version": 366,
			"versionNonce": 2090277628,
			"isDeleted": false,
			"id": "EQCBFreRXs-Y4uhI2HO2O",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -22.973888381505972,
			"y": 4612.791891629098,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 159.89599194612356,
			"height": 48.986297881388055,
			"seed": 116089839,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920509,
			"link": null,
			"startBinding": {
				"elementId": "-UVrHTij4X-cmVlR-K1VZ",
				"gap": 7.610950766819769,
				"focus": -0.004809731088068328
			},
			"endBinding": {
				"elementId": "OCRMkPI-ja-rlTMji_SBg",
				"gap": 3.5689966002637377,
				"focus": -0.06780380748715213
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					159.89599194612356,
					-48.986297881388055
				]
			]
		},
		{
			"type": "freedraw",
			"version": 34,
			"versionNonce": 1675257551,
			"isDeleted": false,
			"id": "xwQz2JSj5ICM2iPAl4pCG",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -632.1560105687711,
			"y": 5064.182229125577,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 90.99826388888891,
			"height": 104.02696397569434,
			"seed": 1170388239,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-0.3766547309026009,
					0.7534450954854037
				],
				[
					-3.0094401041665155,
					4.180365668402374
				],
				[
					-8.602294921875,
					10.49072265625
				],
				[
					-15.648057725694343,
					18.485785590277374
				],
				[
					-23.629964192708258,
					27.65706380208303
				],
				[
					-31.30425347222217,
					36.90633138020803
				],
				[
					-37.757568359375,
					45.08273654513869
				],
				[
					-43.48402235243043,
					52.45374891493066
				],
				[
					-48.43709309895826,
					59.22987196180566
				],
				[
					-53.64637586805543,
					66.04749891493066
				],
				[
					-59.879150390625,
					73.1557888454854
				],
				[
					-66.63099500868043,
					79.94954427083303
				],
				[
					-73.157958984375,
					86.1181640625
				],
				[
					-78.38541666666652,
					91.07557508680566
				],
				[
					-82.86485460069434,
					95.0830078125
				],
				[
					-86.58962673611109,
					98.133544921875
				],
				[
					-88.80791558159717,
					99.97260199652737
				],
				[
					-89.90180121527783,
					101.051025390625
				],
				[
					-90.29852973090283,
					101.73516167534672
				],
				[
					-90.56803385416652,
					102.2451443142354
				],
				[
					-90.71139865451391,
					102.65123155381934
				],
				[
					-90.71139865451391,
					102.92928059895803
				],
				[
					-90.71139865451391,
					103.18400065104197
				],
				[
					-90.71139865451391,
					103.48375108506934
				],
				[
					-90.71139865451391,
					104.02696397569434
				],
				[
					-90.85489908854152,
					104.02696397569434
				],
				[
					-90.99826388888891,
					103.72816297743066
				],
				[
					-90.99826388888891,
					102.38511827256934
				],
				[
					-90.99826388888891,
					102.38511827256934
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 25,
			"versionNonce": 712995233,
			"isDeleted": false,
			"id": "9Nes1PVmjPuRXqR06UbUy",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -734.9586093100904,
			"y": 5086.357522094327,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 142.47273763020826,
			"height": 78.63213433159672,
			"seed": 1036787073,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.4755316840278283,
					0
				],
				[
					2.8070746527778283,
					0.45220269097171717
				],
				[
					10.07948133680543,
					3.76302083333303
				],
				[
					24.114040798611086,
					11.45304361979197
				],
				[
					42.931179470486086,
					21.43798828125
				],
				[
					62.945827907986086,
					32.32747395833303
				],
				[
					79.14496527777783,
					41.35308159722172
				],
				[
					92.81060112847217,
					49.03605143229197
				],
				[
					106.18014865451391,
					56.81572808159672
				],
				[
					117.92290581597217,
					63.13340928819434
				],
				[
					126.39811197916674,
					67.62424045138869
				],
				[
					131.81111653645826,
					70.9033203125
				],
				[
					136.25800238715283,
					73.61626519097172
				],
				[
					139.15608723958326,
					75.367431640625
				],
				[
					140.7638888888889,
					76.68877495659672
				],
				[
					141.58257378472217,
					77.50745985243066
				],
				[
					142.09269205729174,
					78.017578125
				],
				[
					142.41021050347217,
					78.42624240451369
				],
				[
					142.47273763020826,
					78.63213433159672
				],
				[
					142.47273763020826,
					78.63213433159672
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 22,
			"versionNonce": 1419949295,
			"isDeleted": false,
			"id": "Vz4UFSb8QmyMycQ8X9qYu",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -867.8923658205072,
			"y": 5130.21345199884,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 147.9050021701389,
			"height": 114.9609375,
			"seed": 913813537,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741842,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0,
					0.14336480034671695
				],
				[
					-0.4966905381943433,
					0.4523383246532831
				],
				[
					-2.8987630208332575,
					1.8602159288193434
				],
				[
					-8.729926215277828,
					6.119520399305657
				],
				[
					-21.42469618055543,
					15.713840060763687
				],
				[
					-39.035915798611086,
					29.001736111111313
				],
				[
					-57.471652560763914,
					43.76451280381934
				],
				[
					-76.38807508680543,
					59.00105794270803
				],
				[
					-95.26597764756934,
					72.67822265625
				],
				[
					-110.6982421875,
					84.16219075520803
				],
				[
					-122.81684027777783,
					94.20857747395803
				],
				[
					-132.8347439236111,
					102.53404405381934
				],
				[
					-139.78040907118043,
					108.29861111111131
				],
				[
					-144.49354383680543,
					112.117919921875
				],
				[
					-146.76513671875,
					113.995361328125
				],
				[
					-147.9050021701389,
					114.9609375
				],
				[
					-147.9050021701389,
					114.9609375
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 25,
			"versionNonce": 1136825729,
			"isDeleted": false,
			"id": "l8oSqIplNpb7h8qgGeYjp",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1002.9520446399515,
			"y": 5145.198396660298,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 154.30555555555543,
			"height": 87.635498046875,
			"seed": 1180121487,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					2.7755575615628914e-17,
					0.14336480034762644
				],
				[
					0.6815592447915151,
					0.2867296006952529
				],
				[
					4.409586588541515,
					0.2867296006952529
				],
				[
					14.443630642361086,
					2.58097330729197
				],
				[
					30.569118923611086,
					9.178602430555657
				],
				[
					49.866129557291515,
					18.269585503472626
				],
				[
					71.396484375,
					29.14998372395894
				],
				[
					93.12825520833326,
					40.71560329861131
				],
				[
					110.89653862847217,
					50.79739040798631
				],
				[
					123.14670138888891,
					58.70578342013869
				],
				[
					131.34385850694434,
					64.63826497395894
				],
				[
					137.92399088541652,
					69.82272677951369
				],
				[
					142.7685546875,
					73.99590386284763
				],
				[
					145.3152126736111,
					76.949462890625
				],
				[
					147.54150390625,
					79.95198567708394
				],
				[
					149.7247992621526,
					83.05270724826369
				],
				[
					151.45182291666652,
					85.46142578125
				],
				[
					153.18467881944434,
					86.99964735243066
				],
				[
					154.30555555555543,
					87.635498046875
				],
				[
					154.30555555555543,
					87.635498046875
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "diamond",
			"version": 60,
			"versionNonce": 1065107215,
			"isDeleted": false,
			"id": "_WL577ooR-pmXvHm6mhtO",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 185.0514004555348,
			"y": 4627.681591647277,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 176,
			"height": 144,
			"seed": 1760454401,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "2mhmdweUxZMSEuw7hAwXQ"
				},
				{
					"id": "NDb4M5Z4AtnTeOzEZWQhN",
					"type": "arrow"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "diamond",
			"version": 47,
			"versionNonce": 884358497,
			"isDeleted": false,
			"id": "faX_TctFEWxiZUPMrCnZz",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 125.80766673157609,
			"y": 4776.1819646399,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 176,
			"height": 144,
			"seed": 4966287,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "jwgyaps_41hfSGG8hE-XG",
					"type": "arrow"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "text",
			"version": 8,
			"versionNonce": 1189053743,
			"isDeleted": false,
			"id": "ay3XoSHs",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 190.0514004555348,
			"y": 4687.181591647277,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 166,
			"height": 25,
			"seed": 448927567,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "c2",
			"rawText": "c2",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "_WL577ooR-pmXvHm6mhtO",
			"originalText": "c2"
		},
		{
			"type": "text",
			"version": 9,
			"versionNonce": 1017768257,
			"isDeleted": false,
			"id": "nx7CoWwF",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 130.8076667315761,
			"y": 4835.552936319587,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 166,
			"height": 25,
			"seed": 297415809,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "cn5",
			"rawText": "cn5",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "faX_TctFEWxiZUPMrCnZz",
			"originalText": "cn5"
		},
		{
			"type": "arrow",
			"version": 89,
			"versionNonce": 91380604,
			"isDeleted": false,
			"id": "NDb4M5Z4AtnTeOzEZWQhN",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -22.398231977193078,
			"y": 4634.966558010126,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 231.8162982939417,
			"height": 43.486609714016595,
			"seed": 1626611937,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920509,
			"link": null,
			"startBinding": {
				"elementId": "-UVrHTij4X-cmVlR-K1VZ",
				"gap": 8.186607171132664,
				"focus": -0.30369797061024895
			},
			"endBinding": {
				"elementId": "_WL577ooR-pmXvHm6mhtO",
				"gap": 1,
				"focus": 0.12904714625283498
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					231.8162982939417,
					43.486609714016595
				]
			]
		},
		{
			"type": "arrow",
			"version": 90,
			"versionNonce": 1038711036,
			"isDeleted": false,
			"id": "jwgyaps_41hfSGG8hE-XG",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -16.446327680318063,
			"y": 4645.198366176169,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 173.96892952883405,
			"height": 170.81048102627483,
			"seed": 275361071,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920505,
			"link": null,
			"startBinding": {
				"elementId": "-UVrHTij4X-cmVlR-K1VZ",
				"gap": 14.138511468007664,
				"focus": -0.7130487483307146
			},
			"endBinding": {
				"elementId": "faX_TctFEWxiZUPMrCnZz",
				"gap": 4.817523647241167,
				"focus": -0.267239043831183
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					173.96892952883405,
					170.81048102627483
				]
			]
		},
		{
			"type": "freedraw",
			"version": 35,
			"versionNonce": 668288367,
			"isDeleted": false,
			"id": "jCFG11-c4UW6Pp9MijEfW",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -370.37796830531806,
			"y": 5000.577641994501,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 176.167724609375,
			"height": 113.827392578125,
			"seed": 1579232271,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-0.1290283203125,
					1.3877787807814457e-17
				],
				[
					-0.9017333984375,
					5.551115123125783e-17
				],
				[
					-2.587158203125,
					0.9366455078124998
				],
				[
					-7.9404296875,
					4.979736328125
				],
				[
					-21.3651123046875,
					13.98284912109375
				],
				[
					-40.2943115234375,
					25.7384033203125
				],
				[
					-61.5838623046875,
					37.78753662109375
				],
				[
					-79.681396484375,
					47.1585693359375
				],
				[
					-93.7603759765625,
					54.633056640625
				],
				[
					-104.4796142578125,
					61.03399658203125
				],
				[
					-111.1239013671875,
					65.39532470703125
				],
				[
					-116.908203125,
					69.67987060546875
				],
				[
					-121.864501953125,
					73.84625244140625
				],
				[
					-126.6568603515625,
					77.8460693359375
				],
				[
					-131.3092041015625,
					81.52679443359375
				],
				[
					-136.092041015625,
					85.2684326171875
				],
				[
					-139.991455078125,
					88.25738525390625
				],
				[
					-142.8316650390625,
					90.271728515625
				],
				[
					-145.7196044921875,
					92.36895751953125
				],
				[
					-149.0196533203125,
					94.91119384765625
				],
				[
					-153.044189453125,
					97.8778076171875
				],
				[
					-158.5518798828125,
					101.7982177734375
				],
				[
					-164.3863525390625,
					105.97882080078125
				],
				[
					-169.2879638671875,
					109.3140869140625
				],
				[
					-172.9046630859375,
					111.7119140625
				],
				[
					-174.927490234375,
					113.1402587890625
				],
				[
					-175.8876953125,
					113.8145751953125
				],
				[
					-176.167724609375,
					113.827392578125
				],
				[
					-176.0789794921875,
					113.56927490234375
				],
				[
					-175.9498291015625,
					113.4384765625
				],
				[
					-175.9498291015625,
					113.4384765625
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 28,
			"versionNonce": 1595886849,
			"isDeleted": false,
			"id": "04W41XK-Rzj2bC_njMGtA",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -549.8064351021931,
			"y": 5008.648503810908,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 150.993408203125,
			"height": 111.79620361328125,
			"seed": 602226273,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.9342041015625001,
					0.74737548828125
				],
				[
					3.2130126953124996,
					2.61126708984375
				],
				[
					9.0369873046875,
					6.78753662109375
				],
				[
					21.0672607421875,
					15.22601318359375
				],
				[
					38.0220947265625,
					27.50323486328125
				],
				[
					56.6505126953125,
					41.27008056640625
				],
				[
					71.127197265625,
					52.59063720703125
				],
				[
					82.68017578125,
					62.10015869140625
				],
				[
					93.3702392578125,
					70.88116455078125
				],
				[
					101.9154052734375,
					77.35760498046875
				],
				[
					109.3804931640625,
					81.95123291015625
				],
				[
					116.2578125,
					85.45635986328125
				],
				[
					122.9000244140625,
					88.24603271484375
				],
				[
					128.8564453125,
					90.8846435546875
				],
				[
					134.8004150390625,
					94.13970947265625
				],
				[
					140.2774658203125,
					97.89300537109375
				],
				[
					144.1243896484375,
					101.7523193359375
				],
				[
					146.6275634765625,
					105.22979736328125
				],
				[
					148.2879638671875,
					107.94793701171875
				],
				[
					149.4342041015625,
					109.87255859375
				],
				[
					150.2242431640625,
					111.0841064453125
				],
				[
					150.6961669921875,
					111.62799072265625
				],
				[
					150.993408203125,
					111.79620361328125
				],
				[
					150.993408203125,
					111.79620361328125
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "rectangle",
			"version": 289,
			"versionNonce": 520667023,
			"isDeleted": false,
			"id": "XK3gxOkDgQqhpIRFot0_R",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -316.64901494115065,
			"y": 5663.231001095484,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 217.96530983664775,
			"height": 374.47548606179,
			"seed": 1859747233,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 374,
			"versionNonce": 1116734689,
			"isDeleted": false,
			"id": "CAxQjBfire09n4ksIhbjX",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1314.2746918765201,
			"y": 5667.312893629218,
			"strokeColor": "#495057",
			"backgroundColor": "#ced4da",
			"width": 217.96530983664775,
			"height": 374.47548606179,
			"seed": 599746799,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "diamond",
			"version": 297,
			"versionNonce": 1913124271,
			"isDeleted": false,
			"id": "0Enz5WWcEZBQh92_470JI",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -850.8568103196062,
			"y": 5774.455038235346,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 231,
			"height": 160,
			"seed": 150675841,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "x0IMEIDamENTnlotd5B2w",
					"type": "arrow"
				},
				{
					"id": "hxNvYqqXBhzQ3GTfE6Ujb",
					"type": "arrow"
				},
				{
					"id": "PPgyi28W",
					"type": "text"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 221,
			"versionNonce": 182370497,
			"isDeleted": false,
			"id": "3kEsgBAUibvDdgYsXxinY",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1254.1541292116517,
			"y": 5722.835481461554,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 108,
			"height": 49,
			"seed": 1893888783,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "x0IMEIDamENTnlotd5B2w",
					"type": "arrow"
				},
				{
					"id": "SzGirK7CWGw83BeKhGeC6",
					"type": "arrow"
				},
				{
					"id": "lYdcjJ3l",
					"type": "text"
				},
				{
					"id": "n9kb9SCKA8Ig0fn0KBBGc",
					"type": "arrow"
				},
				{
					"id": "LS3j7x8v4ispg4m4HPzEY",
					"type": "arrow"
				},
				{
					"id": "ZXEykohbRIWnk7mCnFFTY",
					"type": "arrow"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 297,
			"versionNonce": 278171105,
			"isDeleted": false,
			"id": "Q9ZGHUWe54HkpHh7t2bU3",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1572.393511406402,
			"y": 5660.55749961674,
			"strokeColor": "#495057",
			"backgroundColor": "#ced4da",
			"width": 107,
			"height": 80,
			"seed": 506426689,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "28aq9wGPvgWI4Ou5kXCIX",
					"type": "arrow"
				},
				{
					"id": "HbB6Tbvz",
					"type": "text"
				},
				{
					"id": "fWcVWa40a4bZTRjN2IjpA",
					"type": "arrow"
				}
			],
			"updated": 1647269163113,
			"link": null
		},
		{
			"type": "arrow",
			"version": 735,
			"versionNonce": 1315159292,
			"isDeleted": false,
			"id": "x0IMEIDamENTnlotd5B2w",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1145.150263233762,
			"y": 5741.50220362204,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 350.92927830106305,
			"height": 72.50340430295091,
			"seed": 955775343,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920511,
			"link": null,
			"startBinding": {
				"elementId": "3kEsgBAUibvDdgYsXxinY",
				"gap": 1.0038659778897454,
				"focus": -0.4823031308018764
			},
			"endBinding": {
				"elementId": "0Enz5WWcEZBQh92_470JI",
				"gap": 1.0038659778891343,
				"focus": 0.35359818022410944
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					350.92927830106305,
					72.50340430295091
				]
			]
		},
		{
			"type": "rectangle",
			"version": 303,
			"versionNonce": 1325110767,
			"isDeleted": false,
			"id": "Rkic3B9tzy4bq8FKfI4uY",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -250.01925261364045,
			"y": 5696.904184852889,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 121,
			"height": 55,
			"seed": 1840371969,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "fprRuSVIcqE3z-fZWOAny",
					"type": "arrow"
				},
				{
					"id": "Xc8IMsxf",
					"type": "text"
				},
				{
					"id": "1ourfCPRMIqln6bqTzAIl",
					"type": "arrow"
				},
				{
					"id": "hxNvYqqXBhzQ3GTfE6Ujb",
					"type": "arrow"
				},
				{
					"id": "iKFqyMkThpFisML3fjt4K",
					"type": "arrow"
				},
				{
					"id": "9jeWzEXPqT-J_4SNhbiFr",
					"type": "arrow"
				},
				{
					"id": "SZKRS5QQnMWmDIKZ92r38",
					"type": "arrow"
				},
				{
					"id": "ZXEykohbRIWnk7mCnFFTY",
					"type": "arrow"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "arrow",
			"version": 559,
			"versionNonce": 1117200068,
			"isDeleted": false,
			"id": "hxNvYqqXBhzQ3GTfE6Ujb",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -676.786145347213,
			"y": 5813.804915858238,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 420.32941492817497,
			"height": 88.1560022233898,
			"seed": 1639248783,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920513,
			"link": null,
			"startBinding": {
				"elementId": "0Enz5WWcEZBQh92_470JI",
				"gap": 1.0017247853915165,
				"focus": -0.35457566317464545
			},
			"endBinding": {
				"elementId": "Rkic3B9tzy4bq8FKfI4uY",
				"gap": 6.437477805397634,
				"focus": 0.3183510588894505
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					420.32941492817497,
					-88.1560022233898
				]
			]
		},
		{
			"type": "diamond",
			"version": 351,
			"versionNonce": 504430607,
			"isDeleted": false,
			"id": "I1IuF4XGHAejpsgKNPmQw",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -861.9587723224472,
			"y": 6157.37179599236,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 113,
			"seed": 2030938543,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "SzGirK7CWGw83BeKhGeC6",
					"type": "arrow"
				},
				{
					"id": "ZVHm0trS",
					"type": "text"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "arrow",
			"version": 861,
			"versionNonce": 2029478980,
			"isDeleted": false,
			"id": "SzGirK7CWGw83BeKhGeC6",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1145.7201400603503,
			"y": 5773.148812147543,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 344.4597717804097,
			"height": 392.67493888593526,
			"seed": 658394305,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920514,
			"link": null,
			"startBinding": {
				"elementId": "3kEsgBAUibvDdgYsXxinY",
				"gap": 1.3831789740333988,
				"focus": -0.4211081844861904
			},
			"endBinding": {
				"elementId": "I1IuF4XGHAejpsgKNPmQw",
				"gap": 8.802946358652669,
				"focus": 0.13018460962759631
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					344.4597717804097,
					392.67493888593526
				]
			]
		},
		{
			"type": "text",
			"version": 183,
			"versionNonce": 1463035439,
			"isDeleted": false,
			"id": "PPgyi28W",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -845.8568103196062,
			"y": 5841.955038235346,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 221,
			"height": 25,
			"seed": 740989903,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Replicator",
			"rawText": "Replicator",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "0Enz5WWcEZBQh92_470JI",
			"originalText": "Replicator"
		},
		{
			"type": "text",
			"version": 188,
			"versionNonce": 36427841,
			"isDeleted": false,
			"id": "lYdcjJ3l",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1249.1541292116517,
			"y": 5734.835481461554,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 98,
			"height": 25,
			"seed": 1983450273,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Or Topic",
			"rawText": "Or Topic",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "3kEsgBAUibvDdgYsXxinY",
			"originalText": "Or Topic"
		},
		{
			"type": "text",
			"version": 249,
			"versionNonce": 1766688847,
			"isDeleted": false,
			"id": "Xc8IMsxf",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -245.01925261364045,
			"y": 5711.904184852889,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 111,
			"height": 25,
			"seed": 1864649199,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Re Topic",
			"rawText": "Re Topic",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Rkic3B9tzy4bq8FKfI4uY",
			"originalText": "Re Topic"
		},
		{
			"type": "text",
			"version": 264,
			"versionNonce": 1529642159,
			"isDeleted": false,
			"id": "HbB6Tbvz",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1567.393511406402,
			"y": 5688.05749961674,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 97,
			"height": 25,
			"seed": 999346305,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647269163113,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "P1",
			"rawText": "P1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Q9ZGHUWe54HkpHh7t2bU3",
			"originalText": "P1"
		},
		{
			"type": "diamond",
			"version": 322,
			"versionNonce": 1379247727,
			"isDeleted": false,
			"id": "VkW9Mgw7NePAHg8bkKHEV",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1114.273882037764,
			"y": 6231.998725997981,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 113,
			"seed": 520303631,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "SzGirK7CWGw83BeKhGeC6",
					"type": "arrow"
				},
				{
					"id": "n9kb9SCKA8Ig0fn0KBBGc",
					"type": "arrow"
				},
				{
					"id": "zGevg2BS",
					"type": "text"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "diamond",
			"version": 559,
			"versionNonce": 1378974721,
			"isDeleted": false,
			"id": "ajOQp-xT2sjTKrXS9HCZK",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -653.9321903889572,
			"y": 6103.375983474316,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 182,
			"height": 113,
			"seed": 755308641,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "SzGirK7CWGw83BeKhGeC6",
					"type": "arrow"
				},
				{
					"id": "LS3j7x8v4ispg4m4HPzEY",
					"type": "arrow"
				},
				{
					"id": "dR4i3DKG",
					"type": "text"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "arrow",
			"version": 693,
			"versionNonce": 277494724,
			"isDeleted": false,
			"id": "n9kb9SCKA8Ig0fn0KBBGc",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1168.959154036582,
			"y": 5774.941916677889,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 124.76683016489301,
			"height": 468.86324887647334,
			"seed": 1673517615,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920515,
			"link": null,
			"startBinding": {
				"elementId": "3kEsgBAUibvDdgYsXxinY",
				"gap": 3.1064352163348303,
				"focus": -0.39406700523149635
			},
			"endBinding": {
				"elementId": "VkW9Mgw7NePAHg8bkKHEV",
				"gap": 1.003666270430493,
				"focus": -0.09917872840004027
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					124.76683016489301,
					468.86324887647334
				]
			]
		},
		{
			"type": "arrow",
			"version": 939,
			"versionNonce": 1859527492,
			"isDeleted": false,
			"id": "LS3j7x8v4ispg4m4HPzEY",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1144.6473687923856,
			"y": 5727.403860744193,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 550.1963924020968,
			"height": 390.92856292964734,
			"seed": 476406849,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920516,
			"link": null,
			"startBinding": {
				"elementId": "3kEsgBAUibvDdgYsXxinY",
				"gap": 1.5067604192661292,
				"focus": -0.9443633225306354
			},
			"endBinding": {
				"elementId": "ajOQp-xT2sjTKrXS9HCZK",
				"gap": 3.9189859357918166,
				"focus": 0.2961537967370145
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					550.1963924020968,
					390.92856292964734
				]
			]
		},
		{
			"type": "text",
			"version": 234,
			"versionNonce": 1988368047,
			"isDeleted": false,
			"id": "dR4i3DKG",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -648.9321903889572,
			"y": 6147.375983474316,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 1380105295,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "C1",
			"rawText": "C1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "ajOQp-xT2sjTKrXS9HCZK",
			"originalText": "C1"
		},
		{
			"type": "text",
			"version": 235,
			"versionNonce": 879833025,
			"isDeleted": false,
			"id": "ZVHm0trS",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -856.9587723224472,
			"y": 6201.37179599236,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 1972450337,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "C2",
			"rawText": "C2",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "I1IuF4XGHAejpsgKNPmQw",
			"originalText": "C2"
		},
		{
			"type": "text",
			"version": 171,
			"versionNonce": 1783326927,
			"isDeleted": false,
			"id": "zGevg2BS",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1109.273882037764,
			"y": 6275.998725997981,
			"strokeColor": "#495057",
			"backgroundColor": "#868e96",
			"width": 172,
			"height": 25,
			"seed": 1315199599,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Cn",
			"rawText": "Cn",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "VkW9Mgw7NePAHg8bkKHEV",
			"originalText": "Cn"
		},
		{
			"type": "freedraw",
			"version": 160,
			"versionNonce": 1092063137,
			"isDeleted": false,
			"id": "yNY6oQRZHR-O-x7y4QrTx",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -730.5904240340858,
			"y": 6154.70639623221,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 90.99826388888891,
			"height": 104.02696397569434,
			"seed": 753742991,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-0.3766547309026009,
					0.7534450954854037
				],
				[
					-3.0094401041665155,
					4.180365668402374
				],
				[
					-8.602294921875,
					10.49072265625
				],
				[
					-15.648057725694343,
					18.485785590277374
				],
				[
					-23.629964192708258,
					27.65706380208303
				],
				[
					-31.30425347222217,
					36.90633138020803
				],
				[
					-37.757568359375,
					45.08273654513869
				],
				[
					-43.48402235243043,
					52.45374891493066
				],
				[
					-48.43709309895826,
					59.22987196180566
				],
				[
					-53.64637586805543,
					66.04749891493066
				],
				[
					-59.879150390625,
					73.1557888454854
				],
				[
					-66.63099500868043,
					79.94954427083303
				],
				[
					-73.157958984375,
					86.1181640625
				],
				[
					-78.38541666666652,
					91.07557508680566
				],
				[
					-82.86485460069434,
					95.0830078125
				],
				[
					-86.58962673611109,
					98.133544921875
				],
				[
					-88.80791558159717,
					99.97260199652737
				],
				[
					-89.90180121527783,
					101.051025390625
				],
				[
					-90.29852973090283,
					101.73516167534672
				],
				[
					-90.56803385416652,
					102.2451443142354
				],
				[
					-90.71139865451391,
					102.65123155381934
				],
				[
					-90.71139865451391,
					102.92928059895803
				],
				[
					-90.71139865451391,
					103.18400065104197
				],
				[
					-90.71139865451391,
					103.48375108506934
				],
				[
					-90.71139865451391,
					104.02696397569434
				],
				[
					-90.85489908854152,
					104.02696397569434
				],
				[
					-90.99826388888891,
					103.72816297743066
				],
				[
					-90.99826388888891,
					102.38511827256934
				],
				[
					-90.99826388888891,
					102.38511827256934
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 151,
			"versionNonce": 698578671,
			"isDeleted": false,
			"id": "of6aatjrq0k5dJ0ZzBKnt",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -833.3930227754051,
			"y": 6176.88168920096,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 142.47273763020826,
			"height": 78.63213433159672,
			"seed": 1259954145,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.4755316840278283,
					0
				],
				[
					2.8070746527778283,
					0.45220269097171717
				],
				[
					10.07948133680543,
					3.76302083333303
				],
				[
					24.114040798611086,
					11.45304361979197
				],
				[
					42.931179470486086,
					21.43798828125
				],
				[
					62.945827907986086,
					32.32747395833303
				],
				[
					79.14496527777783,
					41.35308159722172
				],
				[
					92.81060112847217,
					49.03605143229197
				],
				[
					106.18014865451391,
					56.81572808159672
				],
				[
					117.92290581597217,
					63.13340928819434
				],
				[
					126.39811197916674,
					67.62424045138869
				],
				[
					131.81111653645826,
					70.9033203125
				],
				[
					136.25800238715283,
					73.61626519097172
				],
				[
					139.15608723958326,
					75.367431640625
				],
				[
					140.7638888888889,
					76.68877495659672
				],
				[
					141.58257378472217,
					77.50745985243066
				],
				[
					142.09269205729174,
					78.017578125
				],
				[
					142.41021050347217,
					78.42624240451369
				],
				[
					142.47273763020826,
					78.63213433159672
				],
				[
					142.47273763020826,
					78.63213433159672
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 148,
			"versionNonce": 1302786945,
			"isDeleted": false,
			"id": "RySKqWIJsGyd0Onlzr1it",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -966.3267792858219,
			"y": 6220.737619105474,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 147.9050021701389,
			"height": 114.9609375,
			"seed": 362138287,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0,
					0.14336480034671695
				],
				[
					-0.4966905381943433,
					0.4523383246532831
				],
				[
					-2.8987630208332575,
					1.8602159288193434
				],
				[
					-8.729926215277828,
					6.119520399305657
				],
				[
					-21.42469618055543,
					15.713840060763687
				],
				[
					-39.035915798611086,
					29.001736111111313
				],
				[
					-57.471652560763914,
					43.76451280381934
				],
				[
					-76.38807508680543,
					59.00105794270803
				],
				[
					-95.26597764756934,
					72.67822265625
				],
				[
					-110.6982421875,
					84.16219075520803
				],
				[
					-122.81684027777783,
					94.20857747395803
				],
				[
					-132.8347439236111,
					102.53404405381934
				],
				[
					-139.78040907118043,
					108.29861111111131
				],
				[
					-144.49354383680543,
					112.117919921875
				],
				[
					-146.76513671875,
					113.995361328125
				],
				[
					-147.9050021701389,
					114.9609375
				],
				[
					-147.9050021701389,
					114.9609375
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 151,
			"versionNonce": 2064957711,
			"isDeleted": false,
			"id": "aYZbJfT0A-KTreLUDcGbV",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1101.3864581052662,
			"y": 6235.722563766931,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 154.30555555555543,
			"height": 87.635498046875,
			"seed": 260357057,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					2.7755575615628914e-17,
					0.14336480034762644
				],
				[
					0.6815592447915151,
					0.2867296006952529
				],
				[
					4.409586588541515,
					0.2867296006952529
				],
				[
					14.443630642361086,
					2.58097330729197
				],
				[
					30.569118923611086,
					9.178602430555657
				],
				[
					49.866129557291515,
					18.269585503472626
				],
				[
					71.396484375,
					29.14998372395894
				],
				[
					93.12825520833326,
					40.71560329861131
				],
				[
					110.89653862847217,
					50.79739040798631
				],
				[
					123.14670138888891,
					58.70578342013869
				],
				[
					131.34385850694434,
					64.63826497395894
				],
				[
					137.92399088541652,
					69.82272677951369
				],
				[
					142.7685546875,
					73.99590386284763
				],
				[
					145.3152126736111,
					76.949462890625
				],
				[
					147.54150390625,
					79.95198567708394
				],
				[
					149.7247992621526,
					83.05270724826369
				],
				[
					151.45182291666652,
					85.46142578125
				],
				[
					153.18467881944434,
					86.99964735243066
				],
				[
					154.30555555555543,
					87.635498046875
				],
				[
					154.30555555555543,
					87.635498046875
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "diamond",
			"version": 188,
			"versionNonce": 2120145761,
			"isDeleted": false,
			"id": "pcovgvzEfbQ10P4X4dXmi",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 86.61698699022008,
			"y": 5718.205758753911,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 176,
			"height": 144,
			"seed": 621190351,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "bJvcizyR",
					"type": "text"
				},
				{
					"id": "iKFqyMkThpFisML3fjt4K",
					"type": "arrow"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "diamond",
			"version": 173,
			"versionNonce": 1075300143,
			"isDeleted": false,
			"id": "19sm9e6_4nwTmd7ReB_eX",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 27.373253266261372,
			"y": 5866.57710342622,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 176,
			"height": 144,
			"seed": 165256097,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "i5KepRzS",
					"type": "text"
				},
				{
					"id": "9jeWzEXPqT-J_4SNhbiFr",
					"type": "arrow"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "text",
			"version": 135,
			"versionNonce": 1133430593,
			"isDeleted": false,
			"id": "bJvcizyR",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 91.61698699022008,
			"y": 5777.705758753911,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 166,
			"height": 25,
			"seed": 1322789615,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "c2",
			"rawText": "c2",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "pcovgvzEfbQ10P4X4dXmi",
			"originalText": "c2"
		},
		{
			"type": "text",
			"version": 136,
			"versionNonce": 387254607,
			"isDeleted": false,
			"id": "i5KepRzS",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 32.37325326626137,
			"y": 5926.07710342622,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 166,
			"height": 25,
			"seed": 1246199681,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "cn5",
			"rawText": "cn5",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "19sm9e6_4nwTmd7ReB_eX",
			"originalText": "cn5"
		},
		{
			"type": "arrow",
			"version": 472,
			"versionNonce": 2050562756,
			"isDeleted": false,
			"id": "iKFqyMkThpFisML3fjt4K",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -120.8326454425078,
			"y": 5725.490725116759,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 231.81629829394143,
			"height": 43.486609714018414,
			"seed": 2140337423,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920517,
			"link": null,
			"startBinding": {
				"elementId": "Rkic3B9tzy4bq8FKfI4uY",
				"gap": 8.186607171132664,
				"focus": -0.3036979706102593
			},
			"endBinding": {
				"elementId": "pcovgvzEfbQ10P4X4dXmi",
				"gap": 1,
				"focus": 0.1290471462528275
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					231.81629829394143,
					43.486609714018414
				]
			]
		},
		{
			"type": "arrow",
			"version": 487,
			"versionNonce": 491856452,
			"isDeleted": false,
			"id": "9jeWzEXPqT-J_4SNhbiFr",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -114.8807411456328,
			"y": 5736.314899001467,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 171.73431632715085,
			"height": 171.9174068795819,
			"seed": 789440353,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920518,
			"link": null,
			"startBinding": {
				"elementId": "Rkic3B9tzy4bq8FKfI4uY",
				"gap": 14.138511468007664,
				"focus": -0.7131973832488374
			},
			"endBinding": {
				"elementId": "19sm9e6_4nwTmd7ReB_eX",
				"gap": 4.8175236472416145,
				"focus": -0.32053633507598145
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					171.73431632715085,
					171.9174068795819
				]
			]
		},
		{
			"type": "freedraw",
			"version": 161,
			"versionNonce": 2095360769,
			"isDeleted": false,
			"id": "ZpQ0fnECOVWMaN990Mhlj",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -468.8123817706328,
			"y": 6091.101809101134,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 176.167724609375,
			"height": 113.827392578125,
			"seed": 2125429551,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-0.1290283203125,
					1.3877787807814457e-17
				],
				[
					-0.9017333984375,
					5.551115123125783e-17
				],
				[
					-2.587158203125,
					0.9366455078124998
				],
				[
					-7.9404296875,
					4.979736328125
				],
				[
					-21.3651123046875,
					13.98284912109375
				],
				[
					-40.2943115234375,
					25.7384033203125
				],
				[
					-61.5838623046875,
					37.78753662109375
				],
				[
					-79.681396484375,
					47.1585693359375
				],
				[
					-93.7603759765625,
					54.633056640625
				],
				[
					-104.4796142578125,
					61.03399658203125
				],
				[
					-111.1239013671875,
					65.39532470703125
				],
				[
					-116.908203125,
					69.67987060546875
				],
				[
					-121.864501953125,
					73.84625244140625
				],
				[
					-126.6568603515625,
					77.8460693359375
				],
				[
					-131.3092041015625,
					81.52679443359375
				],
				[
					-136.092041015625,
					85.2684326171875
				],
				[
					-139.991455078125,
					88.25738525390625
				],
				[
					-142.8316650390625,
					90.271728515625
				],
				[
					-145.7196044921875,
					92.36895751953125
				],
				[
					-149.0196533203125,
					94.91119384765625
				],
				[
					-153.044189453125,
					97.8778076171875
				],
				[
					-158.5518798828125,
					101.7982177734375
				],
				[
					-164.3863525390625,
					105.97882080078125
				],
				[
					-169.2879638671875,
					109.3140869140625
				],
				[
					-172.9046630859375,
					111.7119140625
				],
				[
					-174.927490234375,
					113.1402587890625
				],
				[
					-175.8876953125,
					113.8145751953125
				],
				[
					-176.167724609375,
					113.827392578125
				],
				[
					-176.0789794921875,
					113.56927490234375
				],
				[
					-175.9498291015625,
					113.4384765625
				],
				[
					-175.9498291015625,
					113.4384765625
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 154,
			"versionNonce": 827343247,
			"isDeleted": false,
			"id": "2N-Tt1GDbkPk-u7hmd4oc",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -648.2408485675078,
			"y": 6099.17267091754,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 150.993408203125,
			"height": 111.79620361328125,
			"seed": 578573121,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.9342041015625001,
					0.74737548828125
				],
				[
					3.2130126953124996,
					2.61126708984375
				],
				[
					9.0369873046875,
					6.78753662109375
				],
				[
					21.0672607421875,
					15.22601318359375
				],
				[
					38.0220947265625,
					27.50323486328125
				],
				[
					56.6505126953125,
					41.27008056640625
				],
				[
					71.127197265625,
					52.59063720703125
				],
				[
					82.68017578125,
					62.10015869140625
				],
				[
					93.3702392578125,
					70.88116455078125
				],
				[
					101.9154052734375,
					77.35760498046875
				],
				[
					109.3804931640625,
					81.95123291015625
				],
				[
					116.2578125,
					85.45635986328125
				],
				[
					122.9000244140625,
					88.24603271484375
				],
				[
					128.8564453125,
					90.8846435546875
				],
				[
					134.8004150390625,
					94.13970947265625
				],
				[
					140.2774658203125,
					97.89300537109375
				],
				[
					144.1243896484375,
					101.7523193359375
				],
				[
					146.6275634765625,
					105.22979736328125
				],
				[
					148.2879638671875,
					107.94793701171875
				],
				[
					149.4342041015625,
					109.87255859375
				],
				[
					150.2242431640625,
					111.0841064453125
				],
				[
					150.6961669921875,
					111.62799072265625
				],
				[
					150.993408203125,
					111.79620361328125
				],
				[
					150.993408203125,
					111.79620361328125
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 105,
			"versionNonce": 2079957729,
			"isDeleted": false,
			"id": "glyEcpdsE7PcAbsAh3_7u",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1420.557899945944,
			"y": 5650.895507320978,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 190.44744873046875,
			"height": 124.42327880859375,
			"seed": 578610159,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-0.267822265625,
					0
				],
				[
					-1.309326171875,
					0.453369140625
				],
				[
					-4.886962890625,
					2.363006591796875
				],
				[
					-12.4537353515625,
					6.67437744140625
				],
				[
					-23.6719970703125,
					13.366607666015625
				],
				[
					-35.895751953125,
					21.087890625
				],
				[
					-52.88592529296875,
					31.7772216796875
				],
				[
					-74.24819946289062,
					44.97265625
				],
				[
					-95.0001220703125,
					59.228515625
				],
				[
					-116.74853515625,
					74.55276489257812
				],
				[
					-135.1068115234375,
					87.2169189453125
				],
				[
					-149.4698486328125,
					97.1507568359375
				],
				[
					-161.09042358398438,
					105.01510620117188
				],
				[
					-170.1129150390625,
					111.1536865234375
				],
				[
					-176.63031005859375,
					115.42974853515625
				],
				[
					-180.04376220703125,
					117.67312622070312
				],
				[
					-182.35968017578125,
					119.18637084960938
				],
				[
					-183.5469970703125,
					119.96673583984375
				],
				[
					-184.08209228515625,
					120.433349609375
				],
				[
					-184.30621337890625,
					120.7864990234375
				],
				[
					-184.50601196289062,
					120.9808349609375
				],
				[
					-185.34259033203125,
					121.37481689453125
				],
				[
					-186.70135498046875,
					122.238525390625
				],
				[
					-188.08709716796875,
					123.17703247070312
				],
				[
					-189.2586669921875,
					123.82891845703125
				],
				[
					-190.18939208984375,
					124.42327880859375
				],
				[
					-190.31842041015625,
					124.42327880859375
				],
				[
					-190.44744873046875,
					124.30059814453125
				],
				[
					-190.44744873046875,
					124.069091796875
				],
				[
					-190.35916137695312,
					123.93368530273438
				],
				[
					-190.2301025390625,
					123.9071044921875
				],
				[
					-190.2301025390625,
					123.9071044921875
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 100,
			"versionNonce": 336541615,
			"isDeleted": false,
			"id": "sRjyBmgbo9zSBXPZ0QoZe",
			"fillStyle": "hachure",
			"strokeWidth": 4,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1611.4364399850065,
			"y": 5644.142882809259,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 177.05291748046875,
			"height": 162.14190673828125,
			"seed": 1641222159,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.029968261718750007,
					0.15155029296875
				],
				[
					1.580413818359375,
					1.4840087890624998
				],
				[
					6.500244140625,
					5.0731201171875
				],
				[
					14.550811767578125,
					10.96063232421875
				],
				[
					24.866729736328125,
					18.508087158203125
				],
				[
					37.8690185546875,
					27.85296630859375
				],
				[
					57.28704833984375,
					43.057708740234375
				],
				[
					79.11224365234375,
					61.148223876953125
				],
				[
					101.35617065429688,
					80.84515380859375
				],
				[
					121.3907470703125,
					99.63885498046875
				],
				[
					136.0328369140625,
					114.711181640625
				],
				[
					147.75714111328125,
					127.76229858398438
				],
				[
					161.9725341796875,
					144.76522827148438
				],
				[
					166.3065185546875,
					149.88241577148438
				],
				[
					168.97088623046875,
					152.83477783203125
				],
				[
					170.97735595703125,
					155.02581787109375
				],
				[
					172.11419677734375,
					156.3594970703125
				],
				[
					172.67236328125,
					157.06057739257812
				],
				[
					173.14251708984375,
					157.6480712890625
				],
				[
					173.79388427734375,
					158.385009765625
				],
				[
					174.64044189453125,
					159.20404052734375
				],
				[
					175.4317626953125,
					160.0526123046875
				],
				[
					176.01641845703125,
					160.82376098632812
				],
				[
					176.4892578125,
					161.43951416015625
				],
				[
					177.05291748046875,
					162.01284790039062
				],
				[
					177.05291748046875,
					162.14190673828125
				],
				[
					177.05291748046875,
					162.14190673828125
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "ellipse",
			"version": 117,
			"versionNonce": 1633984193,
			"isDeleted": false,
			"id": "qJtKYOYCYkwSLf6qAI9nK",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -515.4943013131315,
			"y": 5537.760146603205,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 130,
			"height": 124,
			"seed": 1426591393,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "JKegBlXU"
				},
				{
					"id": "SZKRS5QQnMWmDIKZ92r38",
					"type": "arrow"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "text",
			"version": 77,
			"versionNonce": 1271556559,
			"isDeleted": false,
			"id": "JKegBlXU",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -510.4943013131315,
			"y": 5587.260146603205,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 120,
			"height": 25,
			"seed": 1958089871,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "P1",
			"rawText": "P1",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "qJtKYOYCYkwSLf6qAI9nK",
			"originalText": "P1"
		},
		{
			"type": "arrow",
			"version": 229,
			"versionNonce": 1056717252,
			"isDeleted": false,
			"id": "SZKRS5QQnMWmDIKZ92r38",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -379.81080163837765,
			"y": 5612.1616126672625,
			"strokeColor": "#000000",
			"backgroundColor": "#40c057",
			"width": 130.29947222283866,
			"height": 81.56029117463822,
			"seed": 974928577,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920519,
			"link": null,
			"startBinding": {
				"elementId": "qJtKYOYCYkwSLf6qAI9nK",
				"gap": 6.858517158847553,
				"focus": -0.4293883453433033
			},
			"endBinding": {
				"elementId": "Rkic3B9tzy4bq8FKfI4uY",
				"gap": 3.182281010987445,
				"focus": -0.10508626270591347
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					130.29947222283866,
					81.56029117463822
				]
			]
		},
		{
			"type": "diamond",
			"version": 561,
			"versionNonce": 1704613871,
			"isDeleted": false,
			"id": "xeTBszO6k_3Kn-yxXq5vo",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 6.282567865994325,
			"x": 134.00669389560028,
			"y": 6343.93039424539,
			"strokeColor": "#495057",
			"backgroundColor": "#4c6ef5",
			"width": 209,
			"height": 155,
			"seed": 1214125185,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "fVTbyU0T"
				},
				{
					"id": "28aq9wGPvgWI4Ou5kXCIX",
					"type": "arrow"
				},
				{
					"id": "ZXEykohbRIWnk7mCnFFTY",
					"type": "arrow"
				}
			],
			"updated": 1647266741843,
			"link": null
		},
		{
			"type": "text",
			"version": 498,
			"versionNonce": 432710273,
			"isDeleted": false,
			"id": "fVTbyU0T",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 6.282567865994325,
			"x": 139.00669389560028,
			"y": 6408.93039424539,
			"strokeColor": "#495057",
			"backgroundColor": "#4c6ef5",
			"width": 199,
			"height": 25,
			"seed": 200776609,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647266741843,
			"link": null,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "NEW CONSUMER",
			"rawText": "NEW CONSUMER",
			"baseline": 18,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "xeTBszO6k_3Kn-yxXq5vo",
			"originalText": "NEW CONSUMER"
		},
		{
			"type": "arrow",
			"version": 190,
			"versionNonce": 1269081924,
			"isDeleted": false,
			"id": "fWcVWa40a4bZTRjN2IjpA",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1460.17778504469,
			"y": 5707.734882213548,
			"strokeColor": "#000000",
			"backgroundColor": "#4c6ef5",
			"width": 210.08166584923651,
			"height": 39.5791388141879,
			"seed": 1124232879,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920512,
			"link": null,
			"startBinding": {
				"elementId": "Q9ZGHUWe54HkpHh7t2bU3",
				"gap": 5.945534885316739,
				"focus": -0.09417106305262803
			},
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					210.08166584923651,
					39.5791388141879
				]
			]
		},
		{
			"type": "arrow",
			"version": 326,
			"versionNonce": 978870596,
			"isDeleted": false,
			"id": "ZXEykohbRIWnk7mCnFFTY",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -141.0160934855722,
			"y": 5758.012684139567,
			"strokeColor": "#495057",
			"backgroundColor": "#4c6ef5",
			"width": 282.9230597577673,
			"height": 656.006216362679,
			"seed": 1028471695,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920519,
			"link": null,
			"startBinding": {
				"elementId": "Rkic3B9tzy4bq8FKfI4uY",
				"gap": 6.108499286678125,
				"focus": -0.46998814250779336
			},
			"endBinding": {
				"elementId": "xeTBszO6k_3Kn-yxXq5vo",
				"gap": 1.29211523528285,
				"focus": -0.8935736143371649
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					282.9230597577673,
					656.006216362679
				]
			]
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 239428783,
			"isDeleted": false,
			"id": "eYB9I0mjjEbdhqDT6uRuT",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -880.7343144149518,
			"y": -564.3171826324253,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 436735631,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647267985289,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": false,
			"pressures": [
				0,
				0
			]
		},
		{
			"type": "freedraw",
			"version": 5,
			"versionNonce": 32310657,
			"isDeleted": false,
			"id": "u7mdsEc6o1RODYdnWrJqg",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1048.0967818628683,
			"y": -675.3579676801684,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0,
			"height": 0.14339870876733585,
			"seed": 1857274305,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647268876170,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0,
					0.14339870876733585
				],
				[
					0,
					0
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 1573000495,
			"isDeleted": false,
			"id": "uZ9dujMafYOA_ZCzVP6RE",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1009.7184181475907,
			"y": -681.1706168772171,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 594595599,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647268877390,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 1006471457,
			"isDeleted": false,
			"id": "etctjuOyev8ESpFUUVAIZ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1009.7184181475907,
			"y": -681.1706168772171,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 1727551809,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647268878110,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 1546997647,
			"isDeleted": false,
			"id": "_sFHNrHnCLSWCifeUcOa9",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1009.7184181475907,
			"y": -681.1706168772171,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 861132143,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647268878330,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 578105537,
			"isDeleted": false,
			"id": "Y1TvXoZVIZjTKjl9aHYza",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1188.9317428003683,
			"y": -655.0951096072517,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 674526433,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647268879510,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 43,
			"versionNonce": 441913999,
			"isDeleted": false,
			"id": "I55tQtkOO0SdftY4j62Uy",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1132.3021041284933,
			"y": -668.0529410959671,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 265.0299750434026,
			"height": 5.967848036024293,
			"seed": 514991599,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647268892430,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0,
					-0.1433648003471717
				],
				[
					0.9189860026040151,
					-0.2867635091145076
				],
				[
					6.831122504340327,
					-0.2867635091145071
				],
				[
					16.6480170355901,
					-0.28676350911450754
				],
				[
					28.92164442274293,
					-0.28676350911450754
				],
				[
					48.18617078993043,
					-0.28676350911450754
				],
				[
					59.88362630208326,
					-0.28676350911450754
				],
				[
					80.68806966145826,
					-0.28676350911450754
				],
				[
					98.23255750868043,
					-0.28676350911450754
				],
				[
					114.84815809461793,
					-0.28676350911450754
				],
				[
					130.91606987847217,
					-0.28676350911450754
				],
				[
					153.06627061631934,
					-1.6031561957464646
				],
				[
					156.851806640625,
					-1.6031561957464646
				],
				[
					167.3879665798611,
					-1.6031561957464646
				],
				[
					180.85923936631934,
					-1.6031561957464646
				],
				[
					195.54945203993043,
					-1.6031561957464646
				],
				[
					208.433837890625,
					-1.6031561957464646
				],
				[
					215.90074327256934,
					-1.20758056640625
				],
				[
					221.44341362847217,
					-0.38479275173608585
				],
				[
					227.073974609375,
					0.43368869357641415
				],
				[
					231.89697265625,
					1.2011040581597854
				],
				[
					235.3603786892361,
					1.728108723958485
				],
				[
					238.3200412326387,
					2.122836642795164
				],
				[
					241.08384874131934,
					2.442220052083485
				],
				[
					243.262939453125,
					2.6318359375
				],
				[
					245.52232530381934,
					2.7459716796875
				],
				[
					247.7765570746526,
					2.7459716796875
				],
				[
					250.1059299045137,
					2.7459716796875
				],
				[
					252.2873263888887,
					2.7459716796875
				],
				[
					254.38151041666652,
					2.7459716796875
				],
				[
					256.4154730902776,
					2.7459716796875
				],
				[
					258.2736545138887,
					2.8614976671008208
				],
				[
					259.952392578125,
					3.1050279405383208
				],
				[
					261.56141493055543,
					3.373006184895985
				],
				[
					263.0337185329861,
					3.795369466145985
				],
				[
					264.0231662326387,
					4.184773763020985
				],
				[
					264.7067599826387,
					4.328206380208485
				],
				[
					265.0299750434026,
					4.364691840277828
				],
				[
					264.9643283420137,
					4.364691840277828
				],
				[
					264.8208279079861,
					4.364691840277828
				],
				[
					264.8208279079861,
					4.364691840277828
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 1409252801,
			"isDeleted": false,
			"id": "VcJKwdfXSJIWUxS0kuc1w",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 270.7150670954602,
			"y": 6417.528317042065,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 675570145,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647269129954,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "text",
			"version": 104,
			"versionNonce": 1973628431,
			"isDeleted": false,
			"id": "rw7b3GoE",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -750.2307065590564,
			"y": 6688.34571341793,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 551,
			"height": 72,
			"seed": 328132367,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647269233896,
			"link": null,
			"fontSize": 57.245768229167254,
			"fontFamily": 1,
			"text": "Debugging Scenarios",
			"rawText": "Debugging Scenarios",
			"baseline": 51,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Debugging Scenarios"
		},
		{
			"type": "diamond",
			"version": 86,
			"versionNonce": 970557807,
			"isDeleted": false,
			"id": "Yoqzx8UOZk59923KthzYU",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1500.8279016545425,
			"y": 7121.339325071574,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 388,
			"height": 258,
			"seed": 200916577,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "ElkPWH80"
				},
				{
					"id": "XYkNpzOZ9oAbPMLpLVD5v",
					"type": "arrow"
				}
			],
			"updated": 1647269532556,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 99,
			"versionNonce": 1762241327,
			"isDeleted": false,
			"id": "DEltRv-byrkFVrnoGVGsf",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -614.027771446209,
			"y": 6980.656165349354,
			"strokeColor": "#495057",
			"backgroundColor": "#7950f2",
			"width": 452.9965549045137,
			"height": 539.8385959201387,
			"seed": 1404446447,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "XYkNpzOZ9oAbPMLpLVD5v",
					"type": "arrow"
				},
				{
					"id": "ggr_j7w1jxHEpK0eZ4JCp",
					"type": "arrow"
				}
			],
			"updated": 1647270768066,
			"link": null
		},
		{
			"type": "text",
			"version": 159,
			"versionNonce": 1875332897,
			"isDeleted": false,
			"id": "osp7By7o",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -472.7875370712093,
			"y": 7209.237233600221,
			"strokeColor": "#495057",
			"backgroundColor": "#7950f2",
			"width": 204,
			"height": 35,
			"seed": 2079202255,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647270771130,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "CCloud Cluster",
			"rawText": "CCloud Cluster",
			"baseline": 25,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "CCloud Cluster"
		},
		{
			"type": "diamond",
			"version": 40,
			"versionNonce": 1405301249,
			"isDeleted": false,
			"id": "_8G9SlxEMtZ4uSjjTp5tS",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 521.4675627551796,
			"y": 7100.81892576602,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 455,
			"height": 275,
			"seed": 1747926319,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "Na01xOKn"
				},
				{
					"id": "ggr_j7w1jxHEpK0eZ4JCp",
					"type": "arrow"
				}
			],
			"updated": 1647270251383,
			"link": null
		},
		{
			"type": "text",
			"version": 14,
			"versionNonce": 583117455,
			"isDeleted": false,
			"id": "Na01xOKn",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 526.4675627551796,
			"y": 7220.81892576602,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 445,
			"height": 35,
			"seed": 430600961,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647270251383,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "Consumers",
			"rawText": "Consumers",
			"baseline": 25,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "_8G9SlxEMtZ4uSjjTp5tS",
			"originalText": "Consumers"
		},
		{
			"type": "text",
			"version": 12,
			"versionNonce": 1908222273,
			"isDeleted": false,
			"id": "ElkPWH80",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1495.8279016545425,
			"y": 7232.839325071574,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 378,
			"height": 35,
			"seed": 892427777,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647269526721,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "Producer",
			"rawText": "Producer",
			"baseline": 25,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Yoqzx8UOZk59923KthzYU",
			"originalText": "Producer"
		},
		{
			"type": "arrow",
			"version": 181,
			"versionNonce": 1455659204,
			"isDeleted": false,
			"id": "XYkNpzOZ9oAbPMLpLVD5v",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1114.5371991146424,
			"y": 7236.75433870452,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 498.5236149514194,
			"height": 76.32250346489309,
			"seed": 1050247055,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920520,
			"link": null,
			"startBinding": {
				"elementId": "Yoqzx8UOZk59923KthzYU",
				"gap": 10.365900990046072,
				"focus": 0.12290043620903075
			},
			"endBinding": {
				"elementId": "DEltRv-byrkFVrnoGVGsf",
				"gap": 1.9858127170139142,
				"focus": 0.41078697485357607
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					498.5236149514194,
					-76.32250346489309
				]
			]
		},
		{
			"type": "arrow",
			"version": 212,
			"versionNonce": 1071374404,
			"isDeleted": false,
			"id": "ggr_j7w1jxHEpK0eZ4JCp",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -153.61666576044536,
			"y": 7013.225243052644,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 779.7205432831308,
			"height": 137.13334508167281,
			"seed": 1363312865,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920521,
			"link": null,
			"startBinding": {
				"elementId": "DEltRv-byrkFVrnoGVGsf",
				"gap": 7.41455078125,
				"focus": -0.8990651455405777
			},
			"endBinding": {
				"elementId": "_8G9SlxEMtZ4uSjjTp5tS",
				"gap": 21.154897642117916,
				"focus": 0.4825577298406666
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					779.7205432831308,
					137.13334508167281
				]
			]
		},
		{
			"type": "text",
			"version": 421,
			"versionNonce": 1936558177,
			"isDeleted": false,
			"id": "i47MMr5P",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1757.299798355931,
			"y": 7609.665496946576,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 554,
			"height": 105,
			"seed": 426429583,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647272546569,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "1. Consumers can't see any new message\n2. Producers can't produce\n3. Consuming Lag is high",
			"rawText": "1. Consumers can't see any new message\n2. Producers can't produce\n3. Consuming Lag is high",
			"baseline": 95,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "1. Consumers can't see any new message\n2. Producers can't produce\n3. Consuming Lag is high"
		},
		{
			"type": "text",
			"version": 1420,
			"versionNonce": 1121880577,
			"isDeleted": false,
			"id": "j13S7KmS",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1045.1424090330136,
			"y": 7605.130652654044,
			"strokeColor": "#087f5b",
			"backgroundColor": "#40c057",
			"width": 1373,
			"height": 560,
			"seed": 1948288033,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647270691671,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "1 Consumer can't see new messages\n\n1. New consumer, no new data:\n       - Check in C3 or with kafka topics if there is new messages\n       - There is a dashboard with the offset info (Kafka-topics)\n2. New Data coming but still can't consume:\n       - Check the ACLS: You will find a 401/403 errors on consumer apps logs\n       - Timeout on the fetch (Kafka Consumer Dashboard Metrics), timeouts on consumer app log\n3. Check consumerId or ConsumerGroupId (C3 or metrics):\n     - We don't have it: We should have connection errors on the client log\n     - We saw it: check the offset of consumption and replica assignment\n4. Replicated Topic with producer on origin cluster:\n    - Check /connectors on Connect API\n    - Grab the ID\n    - Check Id on the Confluent Cloud Producers view (or Kafka Topics/Producer dashboard)\n",
			"rawText": "1 Consumer can't see new messages\n\n1. New consumer, no new data:\n       - Check in C3 or with kafka topics if there is new messages\n       - There is a dashboard with the offset info (Kafka-topics)\n2. New Data coming but still can't consume:\n       - Check the ACLS: You will find a 401/403 errors on consumer apps logs\n       - Timeout on the fetch (Kafka Consumer Dashboard Metrics), timeouts on consumer app log\n3. Check consumerId or ConsumerGroupId (C3 or metrics):\n     - We don't have it: We should have connection errors on the client log\n     - We saw it: check the offset of consumption and replica assignment\n4. Replicated Topic with producer on origin cluster:\n    - Check /connectors on Connect API\n    - Grab the ID\n    - Check Id on the Confluent Cloud Producers view (or Kafka Topics/Producer dashboard)\n",
			"baseline": 550,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "1 Consumer can't see new messages\n\n1. New consumer, no new data:\n       - Check in C3 or with kafka topics if there is new messages\n       - There is a dashboard with the offset info (Kafka-topics)\n2. New Data coming but still can't consume:\n       - Check the ACLS: You will find a 401/403 errors on consumer apps logs\n       - Timeout on the fetch (Kafka Consumer Dashboard Metrics), timeouts on consumer app log\n3. Check consumerId or ConsumerGroupId (C3 or metrics):\n     - We don't have it: We should have connection errors on the client log\n     - We saw it: check the offset of consumption and replica assignment\n4. Replicated Topic with producer on origin cluster:\n    - Check /connectors on Connect API\n    - Grab the ID\n    - Check Id on the Confluent Cloud Producers view (or Kafka Topics/Producer dashboard)\n"
		},
		{
			"type": "text",
			"version": 1712,
			"versionNonce": 1663962017,
			"isDeleted": false,
			"id": "FAVF3eeC",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1037.0253775117465,
			"y": 8195.978227523838,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 1536,
			"height": 910,
			"seed": 1523101697,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647273045197,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "2. Producers can't produce\n\n1. Check Authencation/Authorization : Logs of Client side\n2. Under minISR: You will se a exception (grab example) on the client Side Log\n3. Acks=all Never produce.... Having a lag issue with your brokers. Kafka Topics IO metrics, logs on client side\n     RP =3 minISR=2\n   client -> producer thread -> broker\n                           <- R1 ACk\n                           <- R2 ACK\n    ACK <- \n               close       <- R3 ACK *if not warning on logs and some metrics\n\n    RP=3 minISR=3\n   client -> producer thread -> broker\n                            <- R1 ack\n                            <- R2 ack\n                            <- R3 fails\n                     Retry  -> broker\n                            <- R1 ack (older message delete from purgatory)\n                            <- R2 ack (\" \" \" \")\n                            <- R3 fails\n               Reach retry max\n UnderReplicated\n             TopicException <-\n\nx. Batch / linger.ms too high (Very rare)",
			"rawText": "2. Producers can't produce\n\n1. Check Authencation/Authorization : Logs of Client side\n2. Under minISR: You will se a exception (grab example) on the client Side Log\n3. Acks=all Never produce.... Having a lag issue with your brokers. Kafka Topics IO metrics, logs on client side\n     RP =3 minISR=2\n   client -> producer thread -> broker\n                           <- R1 ACk\n                           <- R2 ACK\n    ACK <- \n               close       <- R3 ACK *if not warning on logs and some metrics\n\n    RP=3 minISR=3\n   client -> producer thread -> broker\n                            <- R1 ack\n                            <- R2 ack\n                            <- R3 fails\n                     Retry  -> broker\n                            <- R1 ack (older message delete from purgatory)\n                            <- R2 ack (\" \" \" \")\n                            <- R3 fails\n               Reach retry max\n UnderReplicated\n             TopicException <-\n\nx. Batch / linger.ms too high (Very rare)",
			"baseline": 900,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "2. Producers can't produce\n\n1. Check Authencation/Authorization : Logs of Client side\n2. Under minISR: You will se a exception (grab example) on the client Side Log\n3. Acks=all Never produce.... Having a lag issue with your brokers. Kafka Topics IO metrics, logs on client side\n     RP =3 minISR=2\n   client -> producer thread -> broker\n                           <- R1 ACk\n                           <- R2 ACK\n    ACK <- \n               close       <- R3 ACK *if not warning on logs and some metrics\n\n    RP=3 minISR=3\n   client -> producer thread -> broker\n                            <- R1 ack\n                            <- R2 ack\n                            <- R3 fails\n                     Retry  -> broker\n                            <- R1 ack (older message delete from purgatory)\n                            <- R2 ack (\" \" \" \")\n                            <- R3 fails\n               Reach retry max\n UnderReplicated\n             TopicException <-\n\nx. Batch / linger.ms too high (Very rare)"
		},
		{
			"type": "text",
			"version": 614,
			"versionNonce": 1417752065,
			"isDeleted": false,
			"id": "u5Y8bNbO",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1045.1240984861388,
			"y": 9183.937279715683,
			"strokeColor": "#e67700",
			"backgroundColor": "#40c057",
			"width": 726,
			"height": 595,
			"seed": 216449857,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275475343,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "3. Consumer Lag is going High\n\n1. Check Producer metrics:\n   - IO Metrics - Brokers struggling flushing to disk\n   - Network Metric of network threads raising\n   - Throughput\n\n2. Broker Side (metrics dashboard)\n   - Production Throughput\n   - Network\n   - Rebalancing / Repartition\n   - IO Metrics\n3. Consumer Side (consumer lag (lightbend))\n   - Throughput\n   - Network\n   - Deserialization\n   ",
			"rawText": "3. Consumer Lag is going High\n\n1. Check Producer metrics:\n   - IO Metrics - Brokers struggling flushing to disk\n   - Network Metric of network threads raising\n   - Throughput\n\n2. Broker Side (metrics dashboard)\n   - Production Throughput\n   - Network\n   - Rebalancing / Repartition\n   - IO Metrics\n3. Consumer Side (consumer lag (lightbend))\n   - Throughput\n   - Network\n   - Deserialization\n   ",
			"baseline": 585,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "3. Consumer Lag is going High\n\n1. Check Producer metrics:\n   - IO Metrics - Brokers struggling flushing to disk\n   - Network Metric of network threads raising\n   - Throughput\n\n2. Broker Side (metrics dashboard)\n   - Production Throughput\n   - Network\n   - Rebalancing / Repartition\n   - IO Metrics\n3. Consumer Side (consumer lag (lightbend))\n   - Throughput\n   - Network\n   - Deserialization\n   "
		},
		{
			"type": "freedraw",
			"version": 56,
			"versionNonce": 986895983,
			"isDeleted": false,
			"id": "zrc_m_gx4McJlcDlLKr9p",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1162.8542688420482,
			"y": -608.193389772197,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#40c057",
			"width": 248.01798502604174,
			"height": 14.2962646484375,
			"seed": 1080852897,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647277568083,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0,
					-0.14336480034728538
				],
				[
					0.1421440972221717,
					-0.2867296006944571
				],
				[
					1.475830078125,
					-0.5360921223958712
				],
				[
					6.365017361111086,
					-1.6893174913194575
				],
				[
					16.15966796875,
					-3.6458333333333712
				],
				[
					25.19721137152783,
					-5.335354275173586
				],
				[
					33.49283854166674,
					-7.133449978298586
				],
				[
					42.18193901909717,
					-8.731282552083371
				],
				[
					49.83194986979174,
					-9.924994574652828
				],
				[
					57.096896701388914,
					-10.871649848090328
				],
				[
					63.46055772569457,
					-11.312662760416629
				],
				[
					69.35519748263891,
					-11.699490017361086
				],
				[
					79.48594835069457,
					-12.067125108506957
				],
				[
					83.62074110243066,
					-12.067125108506957
				],
				[
					87.67768012152783,
					-12.067125108506957
				],
				[
					92.35012478298609,
					-12.067125108506957
				],
				[
					97.95518663194457,
					-12.067125108506957
				],
				[
					105.08287217881957,
					-11.807318793402828
				],
				[
					114.07918294270848,
					-11.540323893229129
				],
				[
					123.87668185763891,
					-11.533135308159785
				],
				[
					132.8713650173611,
					-11.533135308159785
				],
				[
					141.06499565972217,
					-12.061767578125
				],
				[
					149.6506076388889,
					-13.076714409722285
				],
				[
					161.68416341145848,
					-13.929646809895871
				],
				[
					174.57316080729174,
					-14.2962646484375
				],
				[
					186.69731987847217,
					-14.2962646484375
				],
				[
					198.72599283854174,
					-14.2962646484375
				],
				[
					208.1787109375,
					-13.997599283854129
				],
				[
					215.992431640625,
					-13.055148654513914
				],
				[
					222.29722764756957,
					-11.857164171006957
				],
				[
					226.9875759548611,
					-10.735880533854129
				],
				[
					230.26014539930566,
					-9.844970703125
				],
				[
					232.20147026909717,
					-9.221259223090328
				],
				[
					233.55902777777783,
					-8.641153971354129
				],
				[
					234.8940700954861,
					-8.060777452256957
				],
				[
					236.4370388454861,
					-7.462361653645871
				],
				[
					237.92670355902783,
					-6.8707275390625
				],
				[
					239.08732096354174,
					-6.307033962673586
				],
				[
					240.09195963541674,
					-5.912814670138914
				],
				[
					241.18570963541674,
					-5.524766710069457
				],
				[
					242.518310546875,
					-5.119832356770871
				],
				[
					243.88726128472217,
					-4.6966552734375
				],
				[
					244.84266493055566,
					-4.214206271701414
				],
				[
					245.7973904079861,
					-3.823988172743043
				],
				[
					246.756591796875,
					-3.5064019097222854
				],
				[
					247.40641276041674,
					-3.235744900173586
				],
				[
					247.73111979166674,
					-2.8055826822916288
				],
				[
					247.87462022569457,
					-2.8055826822916288
				],
				[
					248.01798502604174,
					-2.6673041449653283
				],
				[
					248.01798502604174,
					-2.523939344618043
				],
				[
					248.01798502604174,
					-2.3142496744791288
				],
				[
					247.73111979166674,
					-1.658460828993043
				],
				[
					247.73111979166674,
					-1.658460828993043
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "text",
			"version": 133,
			"versionNonce": 2111166881,
			"isDeleted": false,
			"id": "qOOnIjgo",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -356.2486373316358,
			"y": 9822.576873899672,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 890,
			"height": 104,
			"seed": 1305209345,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647277096256,
			"link": null,
			"fontSize": 82.93432617187429,
			"fontFamily": 1,
			"text": "Centralized Monitoring",
			"rawText": "Centralized Monitoring",
			"baseline": 73,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Centralized Monitoring"
		},
		{
			"type": "rectangle",
			"version": 43,
			"versionNonce": 1065912481,
			"isDeleted": false,
			"id": "NYLzQ_33knzJo14yDmEcL",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1190.3544044757327,
			"y": 10097.795108491686,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 280.2408854166665,
			"height": 114.40483940972263,
			"seed": 1305187137,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275622796,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 79,
			"versionNonce": 1841673473,
			"isDeleted": false,
			"id": "JqGUFgRcRIkgRmabpVVUl",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1194.378842274127,
			"y": 10256.806135509916,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 294.9422200520835,
			"height": 119.53572591145712,
			"seed": 1100647919,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275710938,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 127,
			"versionNonce": 2021288655,
			"isDeleted": false,
			"id": "a4sSSD5SkQRQ1CD6zmMPs",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1194.376485638927,
			"y": 10422.800262571549,
			"strokeColor": "#495057",
			"backgroundColor": "#40c057",
			"width": 303.4757486979167,
			"height": 120.35129123263862,
			"seed": 1210999503,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275645519,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 35,
			"versionNonce": 2103029633,
			"isDeleted": false,
			"id": "zWXS9PCfR9h3ejldomELR",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1167.7464400660078,
			"y": 10631.387366521192,
			"strokeColor": "#495057",
			"backgroundColor": "#4c6ef5",
			"width": 267.2243923611111,
			"height": 120.12627495659763,
			"seed": 852602273,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275654519,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 153,
			"versionNonce": 172906735,
			"isDeleted": false,
			"id": "cFxhl6xiLYNh-IYiymzJZ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1195.0591299531604,
			"y": 10791.682649520866,
			"strokeColor": "#495057",
			"backgroundColor": "#4c6ef5",
			"width": 308.92591688368043,
			"height": 126.92423502604288,
			"seed": 1643889935,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275729305,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 18,
			"versionNonce": 1202004577,
			"isDeleted": false,
			"id": "t4F6QXOpOsAJltErZ7vKx",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1211.158440934064,
			"y": 10973.017683361471,
			"strokeColor": "#495057",
			"backgroundColor": "#4c6ef5",
			"width": 379.5370822482639,
			"height": 151.71305338541788,
			"seed": 156289295,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275664497,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 25,
			"versionNonce": 1257865647,
			"isDeleted": false,
			"id": "QGLE_zAdNbE-0nWBqGvaz",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1145.6979645885779,
			"y": 11168.81561630418,
			"strokeColor": "#495057",
			"backgroundColor": "#4c6ef5",
			"width": 204.79600694444457,
			"height": 102.63685438367975,
			"seed": 1249734703,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275667598,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 84,
			"versionNonce": 1302167108,
			"isDeleted": false,
			"id": "BD8rBHBAsaZ4zR_aPwy-7",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1168.1444571015986,
			"y": 11316.02915797085,
			"strokeColor": "#495057",
			"backgroundColor": "#4c6ef5",
			"width": 252.91463216145826,
			"height": 127.74495442708394,
			"seed": 1811997679,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647430736892,
			"link": null
		},
		{
			"type": "rectangle",
			"version": 35,
			"versionNonce": 1521982049,
			"isDeleted": false,
			"id": "mR_LF8dRjGJn30SpzQkIi",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1174.1734420191333,
			"y": 11490.461666651405,
			"strokeColor": "#495057",
			"backgroundColor": "#fd7e14",
			"width": 313.71839735243043,
			"height": 116.22748480902737,
			"seed": 480588929,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275685299,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 54,
			"versionNonce": 698594639,
			"isDeleted": false,
			"id": "O6hME8pDGJDJAw1Z0Si-q",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -970.9173351126656,
			"y": 10156.214721800063,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 41.505889892578125,
			"height": 43.39653015136719,
			"seed": 880256577,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "HZNPfJVpkzdBu8VCM7UT6",
					"type": "arrow"
				}
			],
			"updated": 1647276758512,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 39,
			"versionNonce": 720562831,
			"isDeleted": false,
			"id": "ngPgjEj52_uGigB3ZzMzR",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -960.8535533743843,
			"y": 10337.288787962172,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 34.834747314453125,
			"height": 33.665771484375,
			"seed": 39638927,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "AC5V6K1P6pCRwtyvs3hJ3",
					"type": "arrow"
				}
			],
			"updated": 1647275960573,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 30,
			"versionNonce": 1271913423,
			"isDeleted": false,
			"id": "8KG4Un3c7hpq5HHrU7FRJ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -960.4263072806343,
			"y": 10498.149231077407,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 43.032684326171875,
			"height": 32.89176940917969,
			"seed": 1108701167,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "hi1TAyHcqbsp0XYfssBEH",
					"type": "arrow"
				}
			],
			"updated": 1647275971392,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 22,
			"versionNonce": 1458829423,
			"isDeleted": false,
			"id": "Q5zzgZTDjZ1AdgJpr8gCt",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -970.1623520281232,
			"y": 10709.443699548388,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 44.04510498046875,
			"height": 27.815170288085938,
			"seed": 2032370639,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "EU8A3_HondPmuhVyJziaJ",
					"type": "arrow"
				}
			],
			"updated": 1647276685862,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 29,
			"versionNonce": 865315745,
			"isDeleted": false,
			"id": "fZsA0K9IqVsdKJwxT0iyO",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -953.9378124075874,
			"y": 10871.81225597975,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 25.852508544921882,
			"height": 24.48387145996095,
			"seed": 1259549057,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275733806,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 27,
			"versionNonce": 1217635983,
			"isDeleted": false,
			"id": "G9dcRXtiKnoIDLpY6tIBS",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -918.3697276907906,
			"y": 11062.514618040297,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 41.839752197265625,
			"height": 43.83209228515625,
			"seed": 1099373295,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275736607,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 67,
			"versionNonce": 285766401,
			"isDeleted": false,
			"id": "QAXtOrhyAPw5_6VyfycSN",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1000.0620799857124,
			"y": 11221.332519651625,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 40.650787353515625,
			"height": 42.62603759765625,
			"seed": 2026573281,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "lpeJI8SADNzHMQGN9jh0N",
					"type": "arrow"
				}
			],
			"updated": 1647276771819,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 51,
			"versionNonce": 2059183489,
			"isDeleted": false,
			"id": "TVoFihFGkoxskhfOWWt-l",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -976.4118724661812,
			"y": 11345.411499143813,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 40.924072265625,
			"height": 45.355987548828125,
			"seed": 214905167,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647276908680,
			"link": null
		},
		{
			"type": "ellipse",
			"version": 41,
			"versionNonce": 2049466273,
			"isDeleted": false,
			"id": "z0a0bt7IX6ZhzOXxJl46n",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1796.6880565482124,
			"y": 10587.004669309828,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 74.77554321289062,
			"height": 72.4505615234375,
			"seed": 132831777,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275747948,
			"link": null
		},
		{
			"type": "text",
			"version": 58,
			"versionNonce": 1843595265,
			"isDeleted": false,
			"id": "726Xx5tv",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1684.2266612845406,
			"y": 10587.034729124282,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 316,
			"height": 70,
			"seed": 1657251567,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275770968,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "JMX Exporter \nMetrics API (CCLOUD)",
			"rawText": "JMX Exporter \nMetrics API (CCLOUD)",
			"baseline": 60,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "JMX Exporter \nMetrics API (CCLOUD)"
		},
		{
			"type": "diamond",
			"version": 243,
			"versionNonce": 2039675900,
			"isDeleted": false,
			"id": "BHh6G_RAQbGKPn3utdabt",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -134.30081899938432,
			"y": 10492.515808225844,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 763,
			"height": 414,
			"seed": 1462001633,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "4hJSBR8V"
				},
				{
					"id": "AC5V6K1P6pCRwtyvs3hJ3",
					"type": "arrow"
				},
				{
					"id": "hi1TAyHcqbsp0XYfssBEH",
					"type": "arrow"
				},
				{
					"id": "EU8A3_HondPmuhVyJziaJ",
					"type": "arrow"
				},
				{
					"id": "N2SVAKtEt2ar58rpyousT",
					"type": "arrow"
				},
				{
					"id": "lpeJI8SADNzHMQGN9jh0N",
					"type": "arrow"
				},
				{
					"id": "YEPrOM8HQAeZtUSzsYiHF",
					"type": "arrow"
				},
				{
					"id": "HZNPfJVpkzdBu8VCM7UT6",
					"type": "arrow"
				},
				{
					"id": "Xw8befYbxADXhLZkKz7iG",
					"type": "arrow"
				}
			],
			"updated": 1647430762093,
			"link": null
		},
		{
			"type": "text",
			"version": 198,
			"versionNonce": 1470128481,
			"isDeleted": false,
			"id": "4hJSBR8V",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -129.30081899938432,
			"y": 10682.015808225844,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 753,
			"height": 35,
			"seed": 1243075247,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647276709974,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "NEW RELIC PROMETHEUS AGENT",
			"rawText": "NEW RELIC PROMETHEUS AGENT",
			"baseline": 25,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "BHh6G_RAQbGKPn3utdabt",
			"originalText": "NEW RELIC PROMETHEUS AGENT"
		},
		{
			"type": "arrow",
			"version": 469,
			"versionNonce": 45079876,
			"isDeleted": false,
			"id": "HZNPfJVpkzdBu8VCM7UT6",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -923.3330476053279,
			"y": 10186.982148956831,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 879.3693657963249,
			"height": 448.71625723210127,
			"seed": 108613537,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920523,
			"link": null,
			"startBinding": {
				"elementId": "O6hME8pDGJDJAw1Z0Si-q",
				"gap": 7.476950038428708,
				"focus": -0.19143447177153355
			},
			"endBinding": {
				"elementId": "BHh6G_RAQbGKPn3utdabt",
				"gap": 13.00927002105783,
				"focus": -0.40944164869301264
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					879.3693657963249,
					448.71625723210127
				]
			]
		},
		{
			"type": "ellipse",
			"version": 26,
			"versionNonce": 344151375,
			"isDeleted": false,
			"id": "rAk7j4pISEHfL2dPGPNW9",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1857.4307018118843,
			"y": 10342.005966306899,
			"strokeColor": "#495057",
			"backgroundColor": "#be4bdb",
			"width": 157.58529663085938,
			"height": 94.39468383789062,
			"seed": 1553601,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275871871,
			"link": null
		},
		{
			"type": "text",
			"version": 43,
			"versionNonce": 133421089,
			"isDeleted": false,
			"id": "nE7hCO4D",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1660.8645397025093,
			"y": 10365.162399412367,
			"strokeColor": "#495057",
			"backgroundColor": "#be4bdb",
			"width": 230,
			"height": 35,
			"seed": 799186689,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647275890032,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "Lightbend Agent ",
			"rawText": "Lightbend Agent ",
			"baseline": 25,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Lightbend Agent "
		},
		{
			"type": "ellipse",
			"version": 70,
			"versionNonce": 2047388751,
			"isDeleted": false,
			"id": "sfYSAex3ddoc3MSQNVPTD",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -929.1778331595406,
			"y": 11552.250844440037,
			"strokeColor": "#495057",
			"backgroundColor": "#be4bdb",
			"width": 34.087982177734375,
			"height": 34.73785400390625,
			"seed": 26076751,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "2ttSzc3Wa0OzUR8UP4UlX",
					"type": "arrow"
				}
			],
			"updated": 1647277124357,
			"link": null
		},
		{
			"type": "arrow",
			"version": 211,
			"versionNonce": 2105489348,
			"isDeleted": false,
			"id": "AC5V6K1P6pCRwtyvs3hJ3",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -923.327421623524,
			"y": 10363.808242193381,
			"strokeColor": "#495057",
			"backgroundColor": "#be4bdb",
			"width": 834.135527686847,
			"height": 299.9135654306956,
			"seed": 902766863,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920522,
			"link": null,
			"startBinding": {
				"elementId": "ngPgjEj52_uGigB3ZzMzR",
				"gap": 5.01650548711789,
				"focus": 0.1367735140936652
			},
			"endBinding": {
				"elementId": "BHh6G_RAQbGKPn3utdabt",
				"gap": 9.948063971557133,
				"focus": -0.4113790599999891
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					834.135527686847,
					299.9135654306956
				]
			]
		},
		{
			"type": "arrow",
			"version": 201,
			"versionNonce": 1808506692,
			"isDeleted": false,
			"id": "hi1TAyHcqbsp0XYfssBEH",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -909.3432456167841,
			"y": 10517.118470842717,
			"strokeColor": "#495057",
			"backgroundColor": "#be4bdb",
			"width": 800.6447254774172,
			"height": 164.93835649862012,
			"seed": 1503056591,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920522,
			"link": null,
			"startBinding": {
				"elementId": "8KG4Un3c7hpq5HHrU7FRJ",
				"gap": 8.206491177005727,
				"focus": -0.20945504546282764
			},
			"endBinding": {
				"elementId": "BHh6G_RAQbGKPn3utdabt",
				"gap": 3.135479298800675,
				"focus": -0.26984738895249716
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					800.6447254774172,
					164.93835649862012
				]
			]
		},
		{
			"type": "arrow",
			"version": 177,
			"versionNonce": 857675460,
			"isDeleted": false,
			"id": "EU8A3_HondPmuhVyJziaJ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -926.1415692095845,
			"y": 10716.00088080279,
			"strokeColor": "#495057",
			"backgroundColor": "#be4bdb",
			"width": 788.3058719871684,
			"height": 19.373899906955558,
			"seed": 106460175,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920522,
			"link": null,
			"startBinding": {
				"elementId": "Q5zzgZTDjZ1AdgJpr8gCt",
				"gap": 2.5603238985953247,
				"focus": -0.489273340857802
			},
			"endBinding": {
				"elementId": "BHh6G_RAQbGKPn3utdabt",
				"gap": 4.565160171809282,
				"focus": 0.059669981510025644
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					788.3058719871684,
					-19.373899906955558
				]
			]
		},
		{
			"type": "arrow",
			"version": 170,
			"versionNonce": 1662363516,
			"isDeleted": false,
			"id": "N2SVAKtEt2ar58rpyousT",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -935.8884960013374,
			"y": 10882.768829466078,
			"strokeColor": "#495057",
			"backgroundColor": "#be4bdb",
			"width": 815.5255528234686,
			"height": 171.47081222647103,
			"seed": 758071073,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920522,
			"link": null,
			"startBinding": null,
			"endBinding": {
				"elementId": "BHh6G_RAQbGKPn3utdabt",
				"gap": 3.708806068909894,
				"focus": 0.3164284365400146
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					815.5255528234686,
					-171.47081222647103
				]
			]
		},
		{
			"type": "arrow",
			"version": 162,
			"versionNonce": 1544534596,
			"isDeleted": false,
			"id": "it3fT61kgU5zSGrVnx7zi",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -879.0646129446968,
			"y": 11079.748352171157,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 789.7961488725682,
			"height": 358.1921646498886,
			"seed": 670649249,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430762093,
			"link": null,
			"startBinding": null,
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					789.7961488725682,
					-358.1921646498886
				]
			]
		},
		{
			"type": "rectangle",
			"version": 148,
			"versionNonce": 1857193775,
			"isDeleted": false,
			"id": "JK5SOmOFZgSKULOx-SLs0",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 860.7981334709079,
			"y": 11077.6374703661,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 548,
			"height": 320,
			"seed": 424939951,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"type": "text",
					"id": "dPzit88n"
				}
			],
			"updated": 1647276782973,
			"link": null
		},
		{
			"type": "text",
			"version": 35,
			"versionNonce": 1582093871,
			"isDeleted": false,
			"id": "dPzit88n",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 865.7981334709079,
			"y": 11220.1374703661,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 538,
			"height": 35,
			"seed": 1344801743,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647276760728,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "NEW RELIC",
			"rawText": "NEW RELIC",
			"baseline": 25,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "JK5SOmOFZgSKULOx-SLs0",
			"originalText": "NEW RELIC"
		},
		{
			"type": "arrow",
			"version": 542,
			"versionNonce": 627791868,
			"isDeleted": false,
			"id": "lpeJI8SADNzHMQGN9jh0N",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -959.1386714999709,
			"y": 11236.346512550048,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 907.761760607231,
			"height": 489.8380552666076,
			"seed": 439756865,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920523,
			"link": null,
			"startBinding": {
				"elementId": "QAXtOrhyAPw5_6VyfycSN",
				"gap": 1.1348344027321744,
				"focus": 0.20091713567219907
			},
			"endBinding": {
				"elementId": "BHh6G_RAQbGKPn3utdabt",
				"gap": 1.7566251224338885,
				"focus": 0.5513151523247799
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					907.761760607231,
					-489.8380552666076
				]
			]
		},
		{
			"type": "diamond",
			"version": 86,
			"versionNonce": 1743538799,
			"isDeleted": false,
			"id": "Wghx3FFc1jJIVSVaJq6eE",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 74.94452411334714,
			"y": 11058.88488945092,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 435,
			"height": 289,
			"seed": 1895827265,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "lpeJI8SADNzHMQGN9jh0N",
					"type": "arrow"
				},
				{
					"type": "text",
					"id": "TDKnXTW3"
				},
				{
					"id": "t9RY8LEHZHhdZlR8mvB-I",
					"type": "arrow"
				},
				{
					"id": "2ttSzc3Wa0OzUR8UP4UlX",
					"type": "arrow"
				}
			],
			"updated": 1647277124357,
			"link": null
		},
		{
			"type": "text",
			"version": 29,
			"versionNonce": 1115950049,
			"isDeleted": false,
			"id": "TDKnXTW3",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 79.94452411334714,
			"y": 11185.88488945092,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 425,
			"height": 35,
			"seed": 1764509327,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647276803609,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "New Relic Flex Agent",
			"rawText": "New Relic Flex Agent",
			"baseline": 25,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Wghx3FFc1jJIVSVaJq6eE",
			"originalText": "New Relic Flex Agent"
		},
		{
			"type": "text",
			"version": 98,
			"versionNonce": 2028927713,
			"isDeleted": false,
			"id": "KFPo1EXj",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -446.1548462072906,
			"y": 11414.21267875756,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 249,
			"height": 35,
			"seed": 733240641,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647277128121,
			"link": null,
			"fontSize": 28,
			"fontFamily": 1,
			"text": "HTTP/s Requests",
			"rawText": "HTTP/s Requests",
			"baseline": 25,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "HTTP/s Requests"
		},
		{
			"type": "arrow",
			"version": 72,
			"versionNonce": 516658372,
			"isDeleted": false,
			"id": "t9RY8LEHZHhdZlR8mvB-I",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 509.93516459063903,
			"y": 11202.178094509263,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 353.3150998791534,
			"height": 8.885704243413784,
			"seed": 578766817,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920524,
			"link": null,
			"startBinding": {
				"elementId": "Wghx3FFc1jJIVSVaJq6eE",
				"gap": 1,
				"focus": -0.0462047029988511
			},
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					353.3150998791534,
					8.885704243413784
				]
			]
		},
		{
			"type": "arrow",
			"version": 677,
			"versionNonce": 649758972,
			"isDeleted": false,
			"id": "Xw8befYbxADXhLZkKz7iG",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -875.5410185677454,
			"y": 11325.238810518631,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 890.6981392936784,
			"height": 535.9470884746224,
			"seed": 766990991,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920523,
			"link": null,
			"startBinding": null,
			"endBinding": {
				"elementId": "BHh6G_RAQbGKPn3utdabt",
				"gap": 7.629971786312865,
				"focus": 0.21867249662997973
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					6.083716155040232,
					-1.5784418240398708
				],
				[
					890.6981392936784,
					-535.9470884746224
				]
			]
		},
		{
			"type": "arrow",
			"version": 76,
			"versionNonce": 999503300,
			"isDeleted": false,
			"id": "YEPrOM8HQAeZtUSzsYiHF",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 481.00691730182194,
			"y": 10802.652671026439,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 382.17346191406244,
			"height": 411.89351399739644,
			"seed": 2138561647,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920523,
			"link": null,
			"startBinding": {
				"elementId": "BHh6G_RAQbGKPn3utdabt",
				"gap": 20.215653551570853,
				"focus": -0.36202538055522454
			},
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					382.17346191406244,
					411.89351399739644
				]
			]
		},
		{
			"type": "arrow",
			"version": 95,
			"versionNonce": 1032084548,
			"isDeleted": false,
			"id": "2ttSzc3Wa0OzUR8UP4UlX",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -886.5538949299666,
			"y": 11576.75874099954,
			"strokeColor": "#495057",
			"backgroundColor": "#82c91e",
			"width": 962.7182829349149,
			"height": 369.41612856684696,
			"seed": 552034063,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647430920524,
			"link": null,
			"startBinding": {
				"elementId": "sfYSAex3ddoc3MSQNVPTD",
				"gap": 9.490381430019244,
				"focus": 0.9135268224786385
			},
			"endBinding": {
				"elementId": "Wghx3FFc1jJIVSVaJq6eE",
				"gap": 2.6214772225392835,
				"focus": 0.546946095058778
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					962.7182829349149,
					-369.41612856684696
				]
			]
		},
		{
			"type": "freedraw",
			"version": 3,
			"versionNonce": 1973334607,
			"isDeleted": false,
			"id": "hggsWwh2jRjZhZ8M1X9Bk",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1879.065256770442,
			"y": -827.4837425477813,
			"strokeColor": "#495057",
			"backgroundColor": "#fa5252",
			"width": 0.0001,
			"height": 0.0001,
			"seed": 283283503,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647277521713,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.0001,
					0.0001
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 63,
			"versionNonce": 1775286351,
			"isDeleted": false,
			"id": "C1gm5r2yGlIKp2v4qyKMj",
			"fillStyle": "hachure",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1144.4335123856763,
			"y": -568.0138939149688,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 256.6051025390625,
			"height": 17.93310546875,
			"seed": 1050123809,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647277559704,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					0.126708984375,
					1.3877787807814457e-17
				],
				[
					1.287353515625,
					0
				],
				[
					4.359130859375,
					0
				],
				[
					9.051513671875,
					0
				],
				[
					15.8079833984375,
					0
				],
				[
					24.43212890625,
					-0.2659912109375
				],
				[
					33.888671875,
					-0.77447509765625
				],
				[
					43.33984375,
					-1.0169677734375
				],
				[
					52.379150390625,
					-1.0169677734375
				],
				[
					61.4171142578125,
					-1.27117919921875
				],
				[
					71.0135498046875,
					-1.63470458984375
				],
				[
					81.7955322265625,
					-1.954345703125
				],
				[
					92.339111328125,
					-2.38153076171875
				],
				[
					101.7115478515625,
					-3.0428466796875
				],
				[
					110.5584716796875,
					-3.80560302734375
				],
				[
					118.715576171875,
					-4.19598388671875
				],
				[
					126.3719482421875,
					-4.436279296875
				],
				[
					133.4019775390625,
					-4.6043701171875
				],
				[
					140.0616455078125,
					-4.6043701171875
				],
				[
					146.8504638671875,
					-4.51220703125
				],
				[
					153.77490234375,
					-4.00238037109375
				],
				[
					160.9219970703125,
					-3.168212890625
				],
				[
					167.716796875,
					-2.34893798828125
				],
				[
					174.1622314453125,
					-1.54412841796875
				],
				[
					180.7025146484375,
					-0.9620361328125
				],
				[
					187.4915771484375,
					-0.59613037109375
				],
				[
					194.17626953125,
					-0.00787353515625
				],
				[
					200.6102294921875,
					0.57000732421875
				],
				[
					206.025634765625,
					1.1007080078125
				],
				[
					210.792236328125,
					1.82745361328125
				],
				[
					216.0894775390625,
					2.581298828125
				],
				[
					220.4437255859375,
					3.28839111328125
				],
				[
					224.0545654296875,
					3.96051025390625
				],
				[
					227.68603515625,
					4.6365966796875
				],
				[
					230.9505615234375,
					5.30596923828125
				],
				[
					233.60302734375,
					6.09503173828125
				],
				[
					235.335205078125,
					6.8043212890625
				],
				[
					236.4971923828125,
					7.322021484375
				],
				[
					237.187744140625,
					7.7840576171875
				],
				[
					237.5223388671875,
					8.1131591796875
				],
				[
					237.626708984375,
					8.35491943359375
				],
				[
					237.626708984375,
					8.4893798828125
				],
				[
					237.755615234375,
					8.50567626953125
				],
				[
					238.0615234375,
					8.553466796875
				],
				[
					238.31982421875,
					8.68255615234375
				],
				[
					238.5684814453125,
					8.8021240234375
				],
				[
					239.1331787109375,
					9.07403564453125
				],
				[
					240.197509765625,
					9.441650390625
				],
				[
					241.399658203125,
					9.83184814453125
				],
				[
					242.62841796875,
					10.1995849609375
				],
				[
					243.8656005859375,
					10.537841796875
				],
				[
					245.240966796875,
					10.91510009765625
				],
				[
					249.7008056640625,
					11.8050537109375
				],
				[
					252.060302734375,
					12.2357177734375
				],
				[
					253.8662109375,
					12.65777587890625
				],
				[
					255.064697265625,
					12.9254150390625
				],
				[
					255.8392333984375,
					13.1514892578125
				],
				[
					256.298828125,
					13.280517578125
				],
				[
					256.6051025390625,
					13.3287353515625
				],
				[
					256.6051025390625,
					13.3287353515625
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "freedraw",
			"version": 5,
			"versionNonce": 1901850113,
			"isDeleted": false,
			"id": "3_cb4cvlcqQNr9AVRJfQE",
			"fillStyle": "hachure",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -1112.7132975419263,
			"y": -571.1430443055938,
			"strokeColor": "#c92a2a",
			"backgroundColor": "#fa5252",
			"width": 0,
			"height": 0.12908935546875,
			"seed": 317377089,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1647277533407,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					2.7755575615628914e-17,
					0.12908935546875
				],
				[
					0,
					0
				]
			],
			"lastCommittedPoint": null,
			"simulatePressure": true,
			"pressures": []
		},
		{
			"type": "text",
			"version": 422,
			"versionNonce": 5136892,
			"isDeleted": false,
			"id": "Lw0pc7kS",
			"fillStyle": "hachure",
			"strokeWidth": 0.5,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -545.2164474170443,
			"y": 2220.003196806588,
			"strokeColor": "#495057",
			"backgroundColor": "#15aabf",
			"width": 359,
			"height": 45,
			"seed": 1065315140,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1647430945939,
			"link": null,
			"fontSize": 36,
			"fontFamily": 1,
			"text": "SM CONNECT Users",
			"rawText": "SM CONNECT Users",
			"baseline": 32,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "SM CONNECT Users"
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#c92a2a",
		"currentItemBackgroundColor": "#fa5252",
		"currentItemFillStyle": "hachure",
		"currentItemStrokeWidth": 2,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 1,
		"currentItemFontSize": 36,
		"currentItemTextAlign": "left",
		"currentItemStrokeSharpness": "sharp",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"currentItemLinearStrokeSharpness": "round",
		"gridSize": null,
		"colorPalette": {}
	},
	"files": {}
}
```
%%