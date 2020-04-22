# Queues

When dealing with small systems, writes can be predictably fast.
However, when systems get large, the amount of time it takes to actually write can become non-deterministic.
With all of the other techniques mentioned thus far, it isn't difficult to imagine scenarios where a write can require a lot more work than a single transaction.

When there is a high load, adding additional servers does not necessarily guarantee less stalling for clients.
Even with load balancing, we can't guarantee that the work will be distributed among the system of servers.
Also, if a server goes down, then the client's request will fail.

Queues allow us to create an ordering of work to be done.
Clients' requests to perform work on a remote server can be added to this queue.

In order to increase apparent response times, it's possible to send a success response to the client once their work request has been added to the queue.

Queues are also used for fault tolerance.
If the allotted server for a given work request happens to be down, we can make the queue retry until it is successful.

RabbitMQ, ZeroMQ, ActiveMQ, and BeanstalkD are all examples of implementations of queues.
