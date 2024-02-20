### SQL and NoSQL

ScyllaDB is a NoSQL distributed wide column database. It supports tunable eventual consistency, meaning that  it is possible to configure the database to consider a write done as soon as one, the majority or all of the nodes in the cluster accept the write.  

Data is divided and stored in [partitions](https://opensource.docs.scylladb.com/stable/architecture/ringarchitecture/) that are replicated in different nodes.

Scylla does not provide strong consistency but it does provide lightweight transactions which are transactions that can span a single partition. The changes occur only if the provided condition evaluates to true. Lightweight transactions use Paxos.  

It's possible to use paxos to read from a single partition.

Only transactions that span a single partition are supported probably because they didn't want two-phase commit and since partitions can be replicated in several nodes, transactions that span multiple partitions could end up requiring consensus involving a number of nodes greater than the replication factor.

MySQL: Manual failover, no leader election, annoying replication setup

Distributed SQL: CockroachDB, Spanner

Rigid schema makes it hard to update the database tables sometimes.

Upgrading the machine to a bigger machine in a cloud provider can take the database down for a few minutes.

```
Different applications have different requirements, and the best choice of technology
for one use case may well be different from the best choice for another use case. It
therefore seems likely that in the foreseeable future, relational databases will continue
to be used alongside a broad variety of nonrelational datastores—an idea that is
sometimes called polyglot persistence [3].
```

### How a resume could be expressed in a relational schema

One of the options is to encode the resume as JSON instead of normalizing the data into several relational tables. Nowadays we can have a relational table with a JSON column where some of the [JSON fields are indexed](https://www.postgresql.org/docs/current/datatype-json.html#JSON-INDEXING).

### The relational model

Internals are abstracted away, query planner decides how to fetch data. We still need to know some database internals to use it effectively though.

### Data locality

Storing data that is fetched together as document databases usually do can be an advantage based on how the data is read. Spanner is not a document database and offers data locality while using the relational model by allowing a schema to declare that a table's row should be nested within the parent table

### Datalog

Academics liked it in the 1980's. Language used by [Datomic](https://www.datomic.com/)