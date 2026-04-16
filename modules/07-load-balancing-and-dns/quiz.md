# Module 07: Quiz

1. Which type of Elastic Load Balancer operates at Layer 7 of the OSI model and can route traffic based on the URL path or Host header in an HTTP request?

   A) Network Load Balancer (NLB)
   B) Gateway Load Balancer (GLB)
   C) Application Load Balancer (ALB)
   D) Classic Load Balancer (CLB)

2. True or False: A Network Load Balancer provides one static IP address per Availability Zone, while an Application Load Balancer does not provide static IP addresses.

3. An Application Load Balancer has the following listener rule configuration:

   - Rule 1 (priority 1): Path = `/api/*` forwards to Target Group A
   - Rule 2 (priority 2): Host = `admin.example.com` forwards to Target Group B
   - Default rule: forwards to Target Group C

   A client sends a request to `https://admin.example.com/api/users`. Which target group receives the request, and why?

4. Which of the following are configurable parameters for ALB target group health checks? (Select THREE.)

   A) Healthy threshold (number of consecutive successful checks)
   B) Maximum number of targets per health check cycle
   C) Unhealthy threshold (number of consecutive failed checks)
   D) Health check interval (time between checks)
   E) Maximum response body size

5. When an ALB target fails its health checks, what happens to existing connections to that target?

   A) All connections are terminated immediately
   B) The load balancer continues sending new requests to the target for 60 seconds
   C) Existing connections are allowed to complete during the deregistration delay period while new requests are routed to other targets
   D) The target is removed from the target group permanently and must be re-registered manually

6. In your own words, explain why you would use an AWS Certificate Manager (ACM) certificate with an ALB HTTPS listener instead of handling SSL/TLS on each backend EC2 instance.

7. True or False: A CNAME record can be used at the zone apex (for example, `example.com` without a subdomain prefix) to point to an ALB DNS name.

8. Which Route 53 routing policy should you use if you want to send 90% of DNS traffic to one ALB and 10% to a second ALB during a gradual deployment?

   A) Simple routing
   B) Failover routing
   C) Weighted routing
   D) Geolocation routing

9. Which of the following statements about Route 53 Alias records are correct? (Select TWO.)

   A) Alias records can point to any domain name on the internet, including non-AWS resources
   B) Alias records can be used at the zone apex (for example, `example.com`)
   C) Alias records incur standard Route 53 query charges when pointing to AWS resources
   D) Alias records automatically reflect changes to the target AWS resource's IP addresses
   E) Alias records require a separate Route 53 health check to monitor the target resource

10. A company runs its application in two AWS Regions: `us-east-1` (primary) and `us-west-2` (standby). They want Route 53 to automatically direct all traffic to `us-west-2` if the primary Region becomes unavailable. Which combination of Route 53 features should they configure?

    A) Simple routing with two A records
    B) Failover routing with a health check on the primary record
    C) Latency-based routing without health checks
    D) Multivalue answer routing with health checks on both records

---

<details>
<summary>Answer Key</summary>

1. **C) Application Load Balancer (ALB)**
   The ALB operates at Layer 7 (the application layer) and inspects HTTP/HTTPS request content to make routing decisions. It supports path-based routing (routing by URL path) and host-based routing (routing by the Host header). The NLB (A) operates at Layer 4 and routes based on TCP/UDP port, not HTTP content. The GLB (B) operates at Layer 3 for network appliance traffic. The CLB (D) is a legacy load balancer that has been superseded by ALB and NLB.
   Further reading: [What is an Application Load Balancer?](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

2. **True.**
   An NLB allocates one static IP address per Availability Zone, which is useful for applications that require whitelisting by IP address or need a fixed entry point. An ALB uses dynamic IP addresses that can change over time. If you need static IPs for an HTTP/HTTPS workload, you can place an ALB behind a Global Accelerator.
   Further reading: [What is a Network Load Balancer?](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html)

3. **Target Group A receives the request.** The ALB evaluates listener rules in priority order. Rule 1 (priority 1) checks whether the path matches `/api/*`. The request path `/api/users` matches this condition, so the ALB executes Rule 1's action and forwards the request to Target Group A. Rule 2 is never evaluated because a higher-priority rule already matched. The default rule is only used when no other rule matches.
   Further reading: [Listener rules for your Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-rules.html)

4. **A, C, D**
   The configurable health check parameters for ALB target groups include: healthy threshold (A), which is the number of consecutive successful checks before marking a target healthy; unhealthy threshold (C), which is the number of consecutive failed checks before marking a target unhealthy; and health check interval (D), which is the time in seconds between health check requests. Other configurable parameters include protocol, path, port, timeout, and success codes. There is no "maximum number of targets per health check cycle" (B) or "maximum response body size" (E) parameter.
   Further reading: [Health checks for your target groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html)

5. **C) Existing connections are allowed to complete during the deregistration delay period while new requests are routed to other targets**
   When a target fails health checks, the ALB stops sending new requests to it but does not immediately terminate existing connections. The target enters a "draining" state during the deregistration delay period (default 300 seconds), allowing in-flight requests to complete. After the delay expires, any remaining connections are closed. The target is not permanently removed (D); the ALB continues health checks and resumes sending traffic when the target becomes healthy again.
   Further reading: [Target groups for your Application Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)

6. **Sample answer:** Using an ACM certificate with an ALB HTTPS listener centralizes SSL/TLS termination at the load balancer. The ALB handles encryption and decryption of HTTPS traffic, then forwards decrypted HTTP requests to backend instances. This approach has several benefits: ACM provides free public certificates with automatic renewal, so you do not need to purchase or manually renew certificates. Managing one certificate on the ALB is simpler than installing and maintaining certificates on every backend instance. Offloading encryption to the ALB reduces CPU load on your EC2 instances, freeing them to focus on application processing.
   Further reading: [Create an HTTPS listener for your Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html), [What is AWS Certificate Manager?](https://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html)

7. **False.**
   A CNAME record cannot be used at the zone apex. This is a DNS specification limitation, not an AWS-specific restriction. The DNS standard (RFC 1034) prohibits CNAME records at the zone apex because the apex must also contain SOA and NS records, and CNAME records cannot coexist with other record types for the same name. To point a zone apex to an ALB, use a Route 53 Alias record instead, which supports the zone apex and is free for queries to AWS resources.
   Further reading: [Choosing between alias and non-alias records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html)

8. **C) Weighted routing**
   Weighted routing distributes traffic across multiple resources based on assigned weights. You create two weighted records for the same domain name, one with weight 90 pointing to the first ALB and one with weight 10 pointing to the second ALB. Route 53 responds to DNS queries proportionally based on these weights. This is commonly used for A/B testing and gradual deployments (canary releases). Simple routing (A) does not support proportional distribution. Failover routing (B) is for active-passive configurations, not proportional splits. Geolocation routing (D) routes based on the user's geographic location, not traffic percentages.
   Further reading: [Choosing a routing policy](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html)

9. **B and D**
   Alias records can be used at the zone apex, such as `example.com` (B), which is a key advantage over CNAME records. Alias records also automatically reflect changes to the target AWS resource's IP addresses (D), so you do not need to update the record when the underlying IPs change. Option A is incorrect because Alias records can only point to specific AWS resources (ALB, NLB, CloudFront, S3, Elastic Beanstalk, or another Route 53 record), not to arbitrary domain names. Option C is incorrect because queries to AWS resources via Alias records are free. Option E is incorrect because Alias records inherit the health of the target AWS resource automatically.
   Further reading: [Choosing between alias and non-alias records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html)

10. **B) Failover routing with a health check on the primary record**
    Failover routing is designed for active-passive disaster recovery. You create a primary record pointing to the `us-east-1` ALB with a Route 53 health check attached, and a secondary record pointing to the `us-west-2` ALB. When the primary resource fails its health check, Route 53 automatically responds with the secondary record, directing traffic to the standby Region. When the primary recovers, Route 53 routes traffic back to it. Simple routing (A) does not support health-check-based failover. Latency-based routing without health checks (C) would route to the lowest-latency Region but would not fail over if a Region becomes unavailable. Multivalue answer routing (D) returns multiple records for client-side selection, which is not the same as deterministic active-passive failover.
    Further reading: [Creating Amazon Route 53 health checks](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover.html), [Configuring DNS failover](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-configuring.html)

</details>
