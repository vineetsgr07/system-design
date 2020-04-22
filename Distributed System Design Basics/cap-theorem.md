# CAP Theorem

CAP theorem states that it is impossible to have a distributed system that offers complete consistency, availability, and partition tolerance.
Basically speaking, you have to choose between two of these attributes.

## Terms

### Consistency

Consistency is where all nodes in our distributed system see the same data at the same time.
This is achieved by updating multiple nodes before any reads are allowed.

### Availability

Availability means that every request gets a proper response, even if nodes have failed.
This is accomplished by replicating data across servers.

### Partition Tolerance

In CAP theorem, a "partition" is a break in communication between two nodes.
If a partition occurs between two a pair of nodes, say, in master-master replication, then there are two options:

* Mark these nodes as being down, meaning that they are no longer available
* Allow the nodes to become out of sync, which means that we have given up consistency

## Solutions

### Consistent and Available

This one is problematic.
Many claim that systems that are both consistent and available are not possible.
Their reasoning lies in the idea that you do not choose to have partition tolerance, it is something that naturally arises.
For example, you could have a database that is not sharded, but has an entire copy of that database to retain availability.
When a write comes in, you either choose to accept the write, knowing that the master and the replication will be out of sync, or you choose to refuse the write.
In the former case, you've chosen availability, and in the latter, you've chosen consistency.

Still, relational databases (RDBMS's) are considered to be the closest that we can get to distributed systems that are both consistent and available.

### Consistent and Partition Tolerant

This method ensures that the data is consistent between all nodes and becomes unavailable in the case of a partition.

Database solutions that practice this model are BigTable, MongoDB, and HBase.

### Available and Partition Tolerant

This method ensures that all of the nodes remain available (through replication), and, in the case of a partition, will resync data between the partitioned nodes once the partition has been resolved.
However, this means that the data between nodes might not be consistent.

Database solutions that practice this model are Cassandra and CouchDB.
