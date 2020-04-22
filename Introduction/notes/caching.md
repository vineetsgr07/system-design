# Caching

While load balancers allow for horizontal scaling, caching allows us to make better use of the resources that we already have.
A cache is essentially a kind of short-term memory bank.
Caches operate on the premise that things that are requested frequently or recently are likely to be requested again soon.
Typically, a cache offers a faster lookup time than looking it up at the data's original location, but the cache is limited in its size.

Caches can exist at just about any layer of computing.
They can exist at the hardware level, the operating system level, the web application level, the browser level, etc.
Caches can exist at any layer of our architecture, but they're typically found closer to our frontend, where it can significantly reduce the strain.

## Types of Caching

### Application Server Cache

A simple approach to caching occurs at the request layer.
When a request is made of a server for some resource or response, the cache is used in its place since it is significantly faster than reading from the database.
A cache could exist in memory (which is very fast) or on a local disk on the server (which is faster than looking it up on network storage).

This approach, however, presents a problem.
If we're load balancing our requests, we could have each node in the request layer host its own cache.
But, because these the load balancer will delegate various requests to various nodes, and because our cache relies on the history of requests by temporal proximity or frequency, we end up being in a position where cache misses are likely.
For instance, if a client requests a user's profile information from a distributed system and the system's load balancer sends the request to server A, then if another client makes a request for that same user's profile information and happens to be sent to server B by the load balancer, it might not be in the server B's cache.

The following two approaches handle this problem in different manners.

### Distributed Cache

Instead of having each node host its own cache, we can have a distributed cache using [consistent hashing].
When a request is made, we check the distributed cache to see if there is an entry for the given request.

The downside of this approach is that it is not protected from individual caching nodes from going down.
Some implementation of this approach will have duplicate caching nodes to maintain availability, but this can get very complex.
However, if a cache node goes down and that portion of the cache is temporarily unavailable, it's not the end of the world.
The request should continue on to be provided a response by the correct server.

[consistent hashing]: ./consistent-hashing.md

### Global Cache

This method involves introducing a single cache store server between the requestor and the target server.
There are two approaches that can occur with this method when a cache fails to have the desired record/resource.

#### Cache Server Handles Requests

In the case that the cache does not have an entry, this approach has the cache server pass the request along to the appropriate servers.
Then, when the response is received, the cache can be updated to reflect the most recently and/or frequently given responses.

#### Requestor Handles Requests

In this approach, the requestor attempts to get the response from the cache server.
If the cache server does not have the resource, then a redirect is given to the requestor to fetch the resource from the appropriate server.

#### Comparing These Approaches

Overall, the second method is less common than the first one.
Having the cache server handle all of the requests and the eviction and fetching of cached material prevents a flood of requestors requesting the same resource.

It does, however, occasionally make sense to prefer the second method.
If a cache is being used to store large files, then there will be many cache misses.
In this case, it makes more sense to have the requestors fetch the resources from the targeted servers, since the cache server would be flooded with many fetches.
Another case where it would make sense to delegate the fetching to the requestors is when static files (ones that will not be evicted from the cache) are being stored in the cache.
In this case it might make sense because the application itself might understand the eviction strategy and hot spots better than the cache server.

### Content Distribution Network (CDN)

These kinds of caches are typically for storing very large, static files.
Typically, a client will attempt requesting a resource from a CDN, and if the CDN does not have the requested resource available locally, then it forwards the request to the backend servers.

If your application is too small to warrant a CDN, you can ease a future transition to using one by serving assets from a subdomain (i.e. static.website.com) and use NGINX or some other lightweight server to redirect requests from this address to the appropriate servers.
In the future, you can just adjust your DNS to map the requests to that subdomain to a CDN.

## Cache Invalidation

When the data in our database is changed, the cache will need to be updated to reflect those changes, or else we lose consistency.
There are three cache invalidation strategies that allow us to handle the invalidation of cached elements.

### Write-Through Cache

In this approach, when any kind of write is made to create or update a record, the write is simultaneously made to the cache and to the database.

The benefit of this approach is that it's highly-consistent and is guarded in the cases of a failure through power outage or what have you.

The downside of this approach is that it comes at the cost of higher latency, since each write must occur twice before returning a response.

### Write-Around Cache

In this approach, when any kind of write is made, it is made directly to the database, bypassing the cache.
This means that it will not be added to the cache until a request has been made to fetch that data.

One point of confusion for me is how updates to records go stale in the cache.
Either you would have to allow the cached version to be used (leading to inconsistency) or have the application occasionally update the cache.

### Write-Back Cache

In this approach, when any kind of write is made, it's made to the cache, and not immediately made to the database.
A job will, every so often, take the elements in the cache and write them to the database.
However, if there happens to be a power outage before those elements are persisted, then you will lose data.

The benefit of this approach is that it's very fast.

## Cache Eviction Policies

### First-in-First-out (FIFO)

Just as it sounds, there is no consideration of how often the resource has been accessed.

### Last-in-First-out (LIFO)

Just as it sounds, there is no consideration of how often the resource has been accessed.

### Least-Recently-Used (LRU)

In this policy, entries that are older than the others are evicted.

### Most-Recently-Used (MRU)

In this policy, the most recently-used entries are evicted.

### Least-Frequently-Used (LFU)

In this policy, the least frequently-used entries are evicted.

### Random Replacement (RR)

In this policy, a random element is removed to free up space when needed.
