HIGH AVAILABILITY & SCALABILITY for EC2

Vertical Scaling: Increase instance size (= scale up / down )
From t2.nano - 0.5G RAM, 1 vCPU
to u-12tb l.metal - 12.3 TB ram, 448 vCPUS



Horizontal Scaling: Increase the number of instances (= scale out / in )
Auto Scaling Group
Load Balancer





High Availability: Run instances for the same application across multi AZ
Auto Scaling Group multi AZ
Load Balancer Multi AZ



What is load balancing

load balancer are servers that forward traffic to multiple servers downstream



why

Spread load across multilple downstreams

expose a single point of access(DNS) to you application

seamlessly handle failures to downstream instances

do regular health checks to your instances

provide SSL termination (HTTPS) for your website

enforce stickiness with cookies

high availability across zones

seperate public traffic from private traffic



Why use an Elastic Load balancer?
An Elastic load balancer is a managed load balancer

AWS guarantees that it will be working

aws takes care of upgrades, maintenance, high availability

aws provides only a few configuration knobs

It costs less to setup your own load balancer but it will be more effort on your end

it is integrated with many AWS offerings / services



Health checks
Health checks are crucial for load balancers

they enable the load balancer to know if instances it forward traffic to are available to reply requests

the health check is done on a port and a route (/health is common)

Types of Load balancers
AWS has 4 kinds

1 classic load balancer
HTTP, HTTPS, TCP , SSL

2 application load balancer

HTTP, HTTPS, WebSocket

3 network load balancer

TCP, TLS (Secure TCP), UDP

4 gateway load balancer
Operates at layer 3



APPLICATION LOAD BALANCER
application load balancer is layer 7 (HTTP)

load balancing to multiple HTTP applications across multiple machines (target groups)

Load balancing to multiple applications on the same machine

Support for HTTP/2 and websocket

Support redirects ( from HTTP to HTTPS for example)


Routing Tables to different target groups:
Routing based on path in URL (example.com/users & example.com/post)
Routing based on hostname in URL ( one.example.com & other.example.com)

Routing based on Query String, Headers



ALB are a great fit for micro services & container based application

Has a port mapping feature to redirect to a dynamic port in ECS

In comparison, we'd need multiple classic load balancer per application



