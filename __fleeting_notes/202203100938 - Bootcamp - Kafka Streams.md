--- 
### 09:38

### Status: #fleeting_note
### Tags:
[[kstreams]]
### References
#repos [Twitter Apps](https://github.com/Dabz/kafka-streams-bootcamp/tree/master/twitter-streams-sample)
#docs [labs](https://docs.google.com/document/d/15ptEfpaq7X8AKqaYxdvAEp__aQLcqhErrhAtuHIr0A0/edit?usp=sharing)
#docs [slides](https://docs.google.com/presentation/d/1Hq06K9Y5OEITC8bmBsq_3hRrsc7owYFNqRa1qjm-51g/edit#slide=id.g904488692e_3_0)

### Notes:

```ad-warning

Don't use avro or protobuf for keys, due serialization will be problems with the byte to byte comparation... so can be problematic.

Use primitives types.

```

--- 
---

### 11:40

### Status: #fleeting_note
### Tags:
[[kstreams]]
### References

### Notes:
#### Filtering app

![[202203100938 - Bootcamp - Kafka Streams_2022-03-10 10.22.07.excalidraw]]

[Kafka Stream Viz](https://zz85.github.io/kafka-streams-viz/) -> You provide the string of the topology.describe()

---