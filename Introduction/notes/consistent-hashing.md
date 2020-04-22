# Consistent Hashing

## Introduction

Distributed Hash Tables (DHT) are a fundamental part of distributed, scalable systems.
DHT's are composed of keys, values, and a hashing function.

```
index = hashing_function(key)
```

The `index` in this function mapping can be seen as the server index.

Imagine we're building some kind of distributed system.
A naïve hashing function would just return `key % n`, where `n` is the number of servers available in the distributed system.

There are at least two problems with this approach.

### Lack of Horizontal Scalability

If a server is added to the distributed system, then all of the previous mappings will have to be updated.
This would include downtime and some kind of job that would redistribute the previous mappings to their appropriate places.
That would mean that there would be many reads and writes.
Obviously, the downtime is problematic, and the amount of downtime could be significant, depending on the amount of data stored.

### Lack of Load Balancer Application

We can typically assume that data is non-uniformly distributed.
In the case of the naïve hashing function, the data is only written to one server.
Unless the data is perfectly distributed, and unless that distribution is not affected by our hashing function, we can expect that the data written to our servers is not done so in a uniform manner.

## Benefits of Consistent Hashing

Consistent hashing is a very helpful strategy for distributed systems and DHT's.
While it doesn't necessarily eliminate the need for redistribution, it can greatly diminish the amount of redistribution that needs to occur.
This method of caching allows us to reduce the total number of redistributions from `n` to `k / n`, where `k` is the total number of keys, and `n` is the number of servers in the distributed system.


## How it Works

Imagine that there is a range of server indexes from 0 up to, but no including, 300.
We only have 3 servers, though.
So, we begin by making sure that each server is equidistant from the others – server A is placed at index 50, server B is placed at index 150, and server C is placed at index 250.
Now, imagine that this range is a ring, where a call to server at index 350 is actually a call to the server at 50 (`350 % 300 = 50`).
When a hashing function returns an index, if that index is not one that is directly associated with a server, we move along the ring in a clockwise fashion until we hit a server.
For instance, if our hashing function returns 57, then we move clockwise along the ring until we reach the next server, which is at index 150.
That means that accessing a server with the hashed index of 57 actually accesses server B.

This approach to distribution provides a solution to our to original problems.

### Horizontal Scaling

#### Adding Servers to the Distributed System

When we add servers to our distributed system, this means that we just move over the relevant entries at the server it precedes on the ring.
For example, if we were to add a server at index 200, then it would be placed after server B and before server C.
This means that all of the keys on server C that were meant to be written to a server with an index of 151 to 200 must be rewritten to server D.

#### Removing Servers from the Distributed System

Whenever we want to remove a server from the distributed system, we just have to take all of the records at the server being deleted and copy them over to the server that it precedes on the ring.
For instance, if we go to remove server A, then all of its records are copied over to server B.

### Load Balancing

To handle the issue of load balancing, we add "virtual replicas."
What this basically means is that when we go to write a record to a server, we actually write it to multiple.
We can come up with a pattern where each server has two redundant copy-servers on the ring spaced 60 and 80 indices apart, respectively.
That would mean that there are A servers at 50, 110, and 130, there are B servers at 150, 210, and 230, there are D servers at 200, 260, and 280, and there are C servers at 250, 10, and 30.
Anytime that a write is to be made on any of the A servers, it writes them to all.
Then, when a read is made, one of these servers can be picked at random (or strategically queued) to guarantee a faster lookup.
If the hash function mixes well, then, as the number of replicas increases, the more likely it is that our keys are balanced.
