# Proxies

A proxy server is one that lies between the client and the target server.
Proxies are used to perform some additional mutations and/or measurement of requests.
They will commonly add or remove headers, encrypt or decrypt requests and/or responses, or perform compression.
Also, proxies can have a caching system implemented on them.
If a resource is cached, then it can be served without having to ping the remote servers.

Collapsed forwarding is when same requests are merged into a single request to the remote servers.
In a situation where multiple clients simultaneously request the same uncached resource, then all of their requests can be condensed into a single request from the proxy server to the target server, reading the disk only once.

Another way a proxy can increase efficiency is by using spatial locality.
If there are files A, B, and C located consecutively on a disk, and if there are many clients that are simultaneously requesting files in the set A, B, C, then the proxy can collapse these requests into a single request to read all of A, B, and C at once and return it to the proxy before splitting the files up to be sent to the appropriate clients.

These sorts of schemes can greatly increase efficiency when randomly accessing data in datasets terabytes in size.
Proxies are particularly useful under high load situations or when we have limited caching, since proxies can mostly batch several requests into one.
