# AWS Elastic Load Balancers

## AWS Load Balancer Types
1. Classic Load Balancer
2. Network Load Balancer
3. Application Load Balancer  (k8s Ingress)

- AWS offers three main types of load balancers: Classic Load Balancer (CLB), Network Load Balancer (NLB), and Application Load Balancer (ALB). Each has distinct features and use cases for distributing traffic across multiple targets. Let's explore each in detail:
  
Classic Load Balancer (CLB)
CLB is the original AWS load balancer, designed for applications built within the EC2-Classic network.

Traffic Matching:
Operates at both Layer 4 (TCP) and Layer 7 (HTTP/HTTPS)
Routes traffic based on basic protocol and port number

Load Balancing Criteria:
For TCP listeners: Uses round-robin algorithm
For HTTP/HTTPS listeners: Uses least outstanding requests algorithm

Key Features:
Supports both EC2-Classic and VPC
SSL offloading and termination
Basic health checks
Sticky sessions

Use Cases:
Legacy applications in EC2-Classic
Simple load balancing needs

Network Load Balancer (NLB)
NLB is designed for high-performance, low-latency applications operating at the transport layer (Layer 4).

Traffic Matching:
Operates at Layer 4 (TCP, UDP, TLS)
Routes connections based on IP protocol data

Load Balancing Criteria:
Uses a flow hash algorithm based on:
Protocol
Source IP address and port
Destination IP address and port
TCP sequence number

Key Features:
Extremely low latency
Handles millions of requests per second
Static IP per Availability Zone
Preserves source IP address
TLS termination

Use Cases:
TCP/UDP applications
Gaming
IoT
High-performance computing

Application Load Balancer (ALB)
ALB operates at the application layer (Layer 7) and provides advanced routing capabilities.

Traffic Matching:
Operates at Layer 7 (HTTP/HTTPS)

Routes based on content of the request, including:
URL path
Host headers
HTTP headers
Query parameters
Load Balancing Criteria:
Default: Round-robin algorithm
Optional: Least outstanding requests algorithm

Key Features:
Content-based routing
Support for containerized applications
WebSocket and HTTP/2 support
Integration with AWS services (e.g., Lambda, ECS)
Advanced health checks

Use Cases:
Microservices architectures
Container-based applications
Web applications with complex routing needs
Comparison of Load Balancing Criteria
Feature	CLB	NLB	ALB
Layer	4 & 7	4	7
Protocol Support	TCP, SSL/TLS, HTTP, HTTPS	TCP, UDP, TLS	HTTP, HTTPS
Routing Decision	Basic (port, protocol)	IP protocol data	Content-based
Performance	Good	Excellent	Very Good
Latency	Low	Ultra-low	Low
Static IP	No	Yes	No
Preserve Source IP	No	Yes	No
