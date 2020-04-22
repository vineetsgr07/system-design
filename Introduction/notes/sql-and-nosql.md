# SQL and NoSQL

SQL databases have pre-defined schemas, and NoSQL databases have dynamic schemas.

## SQL

SQL databases store things in rows and columns.
Each row contains all of the information about a single entity.

Examples of SQL databases are MySQL, SQLite, Postgresql, MariaDB, Oracle, and MS SQL Server.

## NoSQL

### Key-Value Stores

These NoSQL databases do exactly what it seems like they'd do: they map a given key, which is an attribute, to a value.

Examples of key-value stores are Redis, Voldemort, and Dynamo.

### Document Databases

Document databases store things in documents, and those documents are grouped into collections.
Each document can have an entirely different structure.
Document databases include CouchDB and MongoDB.

### Wide-Column Databases

Instead of having tables, these databases have column families, each of which acts as a container for a group of rows.
Columns don't need to be known up front, and each row doesn't have to have the same number of columns.
These are best for analyzing very large datasets.
Examples include Cassandra and HBase.

### Graph Databases

These are best used for datasets that are best-suited for graph representation.
Entities are represented with nodes, properties, and lines.
Examples include Neo4J and InfiniteGraph.

## SQL vs. NoSQL

### Storage

SQL uses tables, rows, and columns, whereas NoSQL have different models.

### Schema

SQL requires a schema.
Changes to a database schema are possible, but often require that production databases go offline.

NoSQL allows for dynamic schemas.

### Querying

SQL databases use SQL, which is very powerful.
NoSQL uses NoQL.
Syntax varies between databases.

### Scalability

SQL databases are easily scalable in a vertical fashion.
Just use better hardware.
However, it is difficult to scale these databases horizontally, since joins can not (at least easily) be done across database servers.

NoSQL databases are horizontally-scalable.
Scaling out is cheaper than scaling up, so this might be preferable if cost for scale is a major issue.
Many NoSQL databases are able to automatically scale across servers.

### ACID Compliance

Most RDBMS's are ACID compliant, where most of the NoSQL sacrifice ACID compliance for performance and scalability.

#### A is for Atomicity

This guarantees that a transaction is completed in an all-or-nothing manner.
Even during power failures and crashes!

#### C is for Consistency

This ensures that any given transaction is guaranteed to bring the database from one valid state to another.

#### I is for Isolation

This ensures that the database will arrive at the same state whether the actions occur concurrently or sequentially.

#### D is for Durability

This ensures that once a transaction has been committed, it will remain so.
That is not, for example, the case in leader-follower replication if the leader goes down before updating the followers.

## How to Choose

### When to Choose SQL

* You need ACID compliance
* Your data structure will never change and you don't anticipate massive growth.

### When to Choose NoSQL

* You need to store large amounts of data that has little-to-no structure.
* You need the cloud-based storage, which, in turn, requires simple horizontal scaling.
* You need to make something quickly.
