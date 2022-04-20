# why having a schema?
## 2022-03-25

### Status: #idea
### Tags:
[[schema_registry]] [[data_governance]] [[data_lineage]]

### References:

- [Subject Name Strategy](https://docs.confluent.io/platform/current/schema-registry/serdes-develop/index.html#sr-schemas-subject-name-strategy) ^a149b2

---

```ad-question
title: Why you should have a schema?

1. Schemas are important for **Data Governance** and **Data Lineage** without one this two keys for a *Data Driven Organiztion* are nearly impossible
2. **Cleanup and Data Awareness** schema give us the capability of being aware of what kind of data is on a topic so give us a lot of information of the data we manage and help us to avoid meaningless one saving unnecessary costs and simplifying access to important information.
3. **Schema Evolution:** One of the killing features of *Schema Registry* is the *Schema Evolution* that allow us to, again, be aware of what kind of data is inside of a given topic and evolve it without having to create a new one or just have a breaking version of it.
4. **Subject Name Strategy** allow us a few strategies to set schemas on to a topic (one to one, one to many,...) simplifying a lot many of our use cases (state machines can be a really good example of that.). [[#^a149b2|Subject Strategy Name]]
```