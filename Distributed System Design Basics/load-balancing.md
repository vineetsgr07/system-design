# Load Balancing

Load balancing does exactly what you'd expect.
Given a request, it dispatches that request to a variety of duplicate servers to ensure that response times are optimal.
There are a few methods used when load balancing:

* Random delegation
* Round-Robin delegation
* Random delegation with weighting for CPU and/or memory utilization

There are several places where load balancing can occur.
You can have it between the client and the web server.
You can also have it between the web servers and the application servers.

## Types of Servers

### Load Balancers

Load balancers distribute requests within groups of replicated servers.

### Web Servers

Web servers are servers that handle HTTP responses.
These can handle some levels of caching, faster asset serving, and more.
Examples of these are NGINX and Apache.

### Application Servers

Application servers are the things that keep our web applications alive.
In the case of Rails, Puma is the standard application server in development.
In production, Unicorn is common.

### Bonus: Interfaces

In the Rails world, what would we call Rack?
Well, it ends up being that Rack is a "Ruby Webserver Interface."
The Rack documentation makes it seem that Rack is intended to lie between the web server and the Rails framework.
Technically, it does, but the layout looks more like this: load balancer -> web server -> application server -> interface -> application.
In a typical deployment of a Rails application, that would look like this: load balancer (NGINX) -> NGINX -> Unicorn -> Rack -> Rails Application.

## Approaches

### Smart Clients

The smart client balances a load across a pool of servers.

When starting out a small deployment, it can be tempting to have a load balancer on one of the pool servers to reduce costs.
Later on, it's probably better to have the load balancer be its own standalone server.

### Hardware Load Balancers

One example of a hardware load balance is Citrix NetScalar.
These pieces of hardware are very expensive and difficult to configure.
Even large companies will avoid this as a solution.

### Software Load Balancers

HAProxy is an example of a software load balancer.
HAProxy is used on the requestor's machine.
The requestor can be an actual client or a standalone load balancing server.
All requests go through, say, port 9000, which is where HAProxy happens to be running.
The proxy then efficiently delegates the request to the appropriate server.
The difference between a smart client and a software load balancer is that a software load balancer is used on all of the clients instead of there being a single load balancer at any given layer.

## The Standard Approach

In most cases, it's probably best to start with a software load balancer, then move on to a smart client or hardware.
