---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 07: Load Balancing and DNS'
---

# Module 07: Load Balancing and DNS

**Phase 2: Core Services**
Estimated lecture time: 90 minutes

<!-- Speaker notes: Welcome to Module 07. This module covers Elastic Load Balancing and Route 53. Breakdown: 10 min ELB overview, 10 min ALB vs NLB vs GLB, 15 min ALB deep dive (listeners, rules, target groups), 10 min health checks, 10 min SSL/TLS, 15 min Route 53 (records, routing policies), 10 min Route 53 health checks, 10 min Q&A. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Demonstrate how ELB distributes traffic for high availability and fault tolerance
- Configure an ALB with listeners, rules, and target groups
- Implement health checks for load balancer target groups
- Use SSL/TLS termination on an ALB with ACM certificates
- Deploy an NLB for TCP/UDP workloads requiring low latency
- Set up Route 53 hosted zones and DNS records
- Configure Route 53 routing policies for traffic management
- Implement Route 53 health checks for DNS failover

---

## Prerequisites and agenda

**Prerequisites:** Module 03 (VPC, subnets, security groups, AZs), Module 04 (EC2 instances, Auto Scaling)

**Agenda:**
1. Elastic Load Balancing overview
2. ALB vs. NLB vs. GLB
3. Application Load Balancer deep dive
4. Target groups and health checks
5. SSL/TLS termination
6. Route 53 overview and record types
7. Routing policies
8. Route 53 health checks

---

# Elastic Load Balancing overview

<!-- Speaker notes: This section takes approximately 10 minutes. Connect to Module 04's Auto Scaling concepts. Draw a VPC with two AZs, EC2 instances, and an ALB in front. -->

---

## Why use a load balancer?

- Distributes traffic across multiple targets and AZs
- Routes traffic only to healthy targets (automatic failover)
- Scales its own capacity automatically
- Integrates with Auto Scaling (Module 04)
- Supports SSL/TLS termination and security groups (Module 03)

---

# ALB vs. NLB vs. GLB

<!-- Speaker notes: This section takes approximately 10 minutes. Walk through the comparison table and ask students to match scenarios to load balancer types. -->

---

## Load balancer types

| Feature | ALB | NLB | GLB |
|---------|-----|-----|-----|
| OSI Layer | Layer 7 (Application) | Layer 4 (Transport) | Layer 3 (Network) |
| Protocols | HTTP, HTTPS, gRPC | TCP, UDP, TLS | IP (all traffic) |
| Routing | Path, host, header-based | Port-based | Transparent |
| Static IP | No | Yes (one per AZ) | No |
| Use cases | Web apps, APIs | Gaming, IoT, trading | Firewalls, IDS |

> For most web applications, start with an ALB. Use NLB for TCP/UDP or when you need static IPs.

---

## Discussion: choosing a load balancer

Your application needs to handle WebSocket connections and route requests to different backend services based on the URL path (`/api/*` vs. `/images/*`).

**Which load balancer type would you choose?**

<!-- Speaker notes: Answer: ALB. It operates at Layer 7 and supports path-based routing, which is needed to route /api/* and /images/* to different target groups. ALB also supports WebSocket connections natively. NLB operates at Layer 4 and cannot inspect HTTP paths. -->

---

# Application Load Balancer deep dive

<!-- Speaker notes: This section takes approximately 15 minutes. Cover listeners, rules, path-based and host-based routing. Use the receptionist analogy. -->

---

## ALB components: listeners and rules

- **Listener:** checks for connections on a protocol and port (HTTP:80, HTTPS:443)
- **Rules:** determine how requests are routed to target groups
- Rules have a priority, conditions, and actions
- Every listener has a default rule for unmatched requests

---

## Path-based and host-based routing

```
ALB Listener (port 443)
├── Rule 1: Path = /api/*    --> Target Group A
├── Rule 2: Path = /images/* --> Target Group B
└── Default Rule             --> Target Group C
```

```
ALB Listener (port 443)
├── Rule 1: Host = api.example.com  --> Target Group A
├── Rule 2: Host = app.example.com  --> Target Group B
└── Default Rule                    --> Target Group C
```

> Combining path and host routing on one ALB reduces costs compared to separate load balancers.

---

# Target groups and health checks

<!-- Speaker notes: This section takes approximately 10 minutes. Cover target types and health check parameters. -->

---

## Target types

| Target Type | Description | Use Case |
|-------------|-------------|----------|
| Instance | EC2 instances by ID | Standard web/app servers |
| IP | Specific IP addresses | Containers, on-premises servers |
| Lambda | Lambda function | Serverless backends |

---

## Health check parameters

| Parameter | Default (ALB) | Description |
|-----------|---------------|-------------|
| Path | `/` | URL path for health checks |
| Interval | 30 seconds | Time between checks |
| Healthy threshold | 5 | Consecutive successes to mark healthy |
| Unhealthy threshold | 2 | Consecutive failures to mark unhealthy |
| Timeout | 5 seconds | Wait time for response |

> Create a dedicated `/health` endpoint that verifies your app can reach its dependencies.

---

## Quick check: health check failure

All targets in a target group fail their health checks.

**What does the load balancer return to clients?**

<!-- Speaker notes: Answer: HTTP 503 (Service Unavailable). The load balancer has no healthy targets to route to. This is why health checks are critical: they prevent the load balancer from sending traffic to broken instances, but if everything is broken, clients get a 503. Monitoring the HealthyHostCount metric in CloudWatch helps detect this situation early. -->

---

# SSL/TLS termination

<!-- Speaker notes: This section takes approximately 10 minutes. Cover ACM certificates and HTTPS listener configuration. -->

---

## SSL/TLS termination with ACM

- ALB handles encryption/decryption on behalf of backend targets
- AWS Certificate Manager (ACM) provides free public certificates
- ACM handles provisioning, renewal, and deployment automatically

Setup steps:
1. Request a certificate in ACM for your domain
2. Validate domain ownership (DNS or email)
3. Create an HTTPS listener on the ALB with the ACM certificate
4. Optionally redirect HTTP to HTTPS

---

## Connection draining (deregistration delay)

- Allows in-flight requests to complete before removing a target
- Default: 300 seconds (configurable 0 to 3600 seconds)
- During the delay, no new requests are sent to the target
- Set shorter delays for stateless apps, longer for long-lived connections

---

# Route 53 overview

<!-- Speaker notes: This section takes approximately 15 minutes. Draw the DNS resolution flow on the whiteboard. -->

---

## What is Route 53?

Three main functions:
1. **Domain registration:** register new domain names
2. **DNS routing:** translate domain names to IP addresses
3. **Health checking:** monitor resource health for failover

**Hosted zones** contain DNS records for a domain:
- Public hosted zone: routes traffic on the internet
- Private hosted zone: routes traffic within VPCs

---

## Record types and Alias records

| Record Type | Purpose | Example |
|-------------|---------|---------|
| A | Domain to IPv4 address | `192.0.2.1` |
| AAAA | Domain to IPv6 address | `2001:0db8::7334` |
| CNAME | Domain to another domain | `app.example.com` |
| Alias | Domain to AWS resource | ALB DNS name |

| Feature | Alias Record | CNAME Record |
|---------|-------------|--------------|
| Zone apex support | Yes | No |
| Query charges | Free for AWS resources | Standard charges |

> Always use Alias records when pointing to AWS resources (ALB, CloudFront, S3).

---

# Routing policies

<!-- Speaker notes: This section takes approximately 10 minutes. Walk through each policy with use cases. -->

---

## Route 53 routing policies

| Policy | How It Works | Use Case |
|--------|-------------|----------|
| Simple | Returns a single resource | No special routing needed |
| Weighted | Distributes by assigned weights | A/B testing, gradual deployments |
| Latency-based | Routes to lowest-latency Region | Multi-Region performance |
| Failover | Primary with health check; secondary backup | Disaster recovery |
| Geolocation | Routes by user's geographic location | Content localization |
| Multivalue answer | Returns up to 8 healthy records | Simple load balancing |

---

## Think about it: routing policy selection

A company has users in North America and Europe. They deploy their application in us-east-1 and eu-west-1 and want each user group routed to the nearest Region.

**Which routing policy should they use?**

<!-- Speaker notes: Answer: Latency-based routing. It routes users to the Region with the lowest network latency, which typically corresponds to the nearest Region. Geolocation routing is also a valid answer if they want strict geographic boundaries (e.g., all European users go to eu-west-1 regardless of latency). The key difference: latency-based optimizes for performance, geolocation optimizes for geographic control. -->

---

# Route 53 health checks

<!-- Speaker notes: This section takes approximately 10 minutes. Cover the three types and how they enable DNS failover. -->

---

## Health check types

1. **Endpoint:** Route 53 sends requests to an IP or domain
2. **Calculated:** monitors status of other health checks
3. **CloudWatch alarm:** healthy when alarm is in OK state

Failover pattern with health checks:

```
Primary (us-east-1) --> Health check monitors ALB
Secondary (us-west-2) --> Activated when primary fails
```

> Combine routing policies with health checks for robust traffic management.

---

## Key takeaways

- ELB distributes traffic across targets and AZs for high availability. Use ALB for HTTP/HTTPS, NLB for TCP/UDP with low latency, GLB for network appliances.
- Health checks are essential for both load balancers and DNS. ELB health checks remove unhealthy targets; Route 53 health checks enable DNS failover across Regions.
- Route 53 Alias records are preferred for AWS resources: free queries, zone apex support, automatic IP tracking.
- Route 53 routing policies control traffic distribution: weighted for gradual deployments, latency-based for multi-Region, failover for disaster recovery.
- SSL/TLS termination at the ALB with ACM certificates simplifies certificate management and offloads encryption from backend targets.

---

## Lab preview: ALB and Route 53

**Objective:** Create an ALB with target groups, configure health checks, test load balancing across EC2 instances, and optionally set up Route 53 records

**Key services:** Elastic Load Balancing (ALB), EC2, VPC, Route 53

**Duration:** 60 minutes

<!-- Speaker notes: Students will create a VPC with two public subnets, launch two EC2 instances with different web pages, create a target group and ALB, and observe load balancing and health check behavior. The Route 53 section is optional for students who have a registered domain. Remind students to delete the ALB and instances after the lab. -->

---

# Questions?

Review `modules/07-load-balancing-and-dns/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions involve ALB vs NLB selection and the difference between ELB and Route 53 health checks. Transition to the lab when ready. -->
