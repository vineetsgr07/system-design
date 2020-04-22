# Data Partitioning

Data partitioning (or sharding) is the breaking up of a large database into multiple small parts.
The premise of sharding is that, at a certain point, it becomes cheaper to scale horizontally than to invest in a single, more-capable server.

## Partitioning Methods

These are the tree most popular approaches to data partitioning.

### Horizontal Partitioning

In this scheme, different rows are placed into different tables.
An example of this is range-based sharding, where entries in a certain range are placed in one database, while those in a different range are placed elsewhere.

The issue with this is that delegating data based on a range is likely to lead to non-uniform distributions.

### Vertical Partitioning

In this scheme, different features are delegated to different tables/databases.
For instance, if an application has users and photos, users would be stored on one database, and photos would be stored on another.

This approach is pretty straightforward, but it becomes problematic if your storage needs eventually need to be drastically scaled.

### Directory-Based Partitioning

This scheme abstracts away the need to develop a robust DB indexing strategy.
Instead of necessarily having a consistent strategy, the proper database index is store in a directory server.
The draw of this approach is that a combination of schemes can be used, and changes in the future will require little impact.

## Partitioning Criteria

### Key or Hash-Based Partitioning

This method uses a hashing function modded by the number of database servers to determine the database index.
This approach is simple, but it essentially fixes the total number of database servers from the get-go.

### List Partitioning

This scheme determines that each partition is a list (or subset) of values.
For instance, if we have users, we can have a Nordic database server that only stores users from Iceland, Norway, Finland, and Denmark.
It's unlikely that this method leads to a uniform distribution.

### Round-Robin Partitioning

This scheme uniformly distributes data by taking every `i`th row and placing it in the `i % n`th database, where `n` is the total number of partitions.

### Composite Partitioning

This method is a combination of any of the above schemes.

## Common Problems

Because several kinds of data might not be stored on the same server, we encounter some extra constraints when partitioning data.

### Joins and Denormalization

If two tables are not in the same database, we can't really perform joins.
A common way to work around this is to denormalize the data.
Denormalization is the process of storing associated data in a record's row.
For instance, if we are storing photos on one database server and users on another, we might want to store the `photographer_username` in the photos table.

The problem with this approach is that we have to handle record updates on all of the appropriate database servers.

### Referential Integrity

Ensuring that foreign keys actually point to a record is something that becomes very difficult with sharding.
If there is a user record with an `id` of 1 and photo with a `user_id` of 1, if the first user is deleted, then the photo remains, pointing to a non-existent entity.
RDBMS databases generally don't support foreign key constraints across database servers.
In order to compensate for this loss of automatic support, it is common to enforce this sort of integrity in the application's logic.

### Rebalancing

There are two cases when we should consider rebalancing our data distribution:

1. The data is not distributed in a uniform manner.
2. There is a significant load on a single shard or group of shards.

It's very difficult to rebalance without having down-time.
Directory-based partitioning makes this experience much easier.
However, it also introduces much more complexity and a single point of failure.
