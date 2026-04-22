---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'IT Fundamentals: Module 03, Networking and the Internet'
---

# IT Fundamentals Module 03: Networking and the Internet

**Module 03 of 06**
Estimated lecture time: 20 to 25 minutes

<!-- Speaker notes: Welcome students to Module 03 of the IT Fundamentals primer. This module covers networking fundamentals and how the internet works. This is the most conceptually dense module, so take time with the diagrams and examples. Total lecture time is approximately 20 to 25 minutes. -->

---

## Learning Objectives

By the end of this module, you will be able to:

- Explain how the TCP/IP model organizes network communication into four layers
- Identify common networking concepts including IP addresses, DNS, ports, and protocols
- Describe how HTTP requests and responses work between a browser and a web server

<!-- Speaker notes: Three objectives for this module, all at the Remember and Understand levels of Bloom's Taxonomy. Networking is the foundation for VPCs, security groups, and load balancers in the bootcamp. Approximately 1 minute on this slide. -->

---

## Networking: The TCP/IP Model

| Layer | Name | Purpose | Example |
|-------|------|---------|---------|
| 4 | Application | Services for apps | HTTP, DNS, SSH |
| 3 | Transport | Reliable or fast delivery | TCP, UDP |
| 2 | Internet | Routes data between networks | IP |
| 1 | Network Access | Physical transmission | Ethernet, Wi-Fi |

- Data passes through all four layers when traveling between computers
- Each layer adds its own header information to the data

<!-- Speaker notes: Trace a web request through all four layers: your browser (Application) sends an HTTP request, TCP (Transport) ensures reliable delivery, IP (Internet) routes it to the server, and Ethernet/Wi-Fi (Network Access) handles the physical transmission. Approximately 4 minutes. -->

---

## IP Addresses and DNS

**IP addresses** identify devices on a network:
- IPv4 example: `192.168.1.100`
- Private ranges: `10.x.x.x`, `172.16-31.x.x`, `192.168.x.x`

**DNS** translates domain names to IP addresses:
1. You type `www.example.com`
2. DNS resolver finds the IP address `93.184.216.34`
3. Your browser connects to that IP address

> In Module 03 of the bootcamp, you will create VPCs using private IP ranges like `10.0.0.0/16`.

<!-- Speaker notes: Emphasize that private IP ranges are used inside cloud networks (VPCs). Students do not need to memorize the ranges, but they should recognize that 10.x.x.x addresses are private. DNS is critical for Route 53 in Module 07. Approximately 4 minutes. -->

---

## Ports and TCP vs. UDP

| Port | Service | Protocol |
|------|---------|----------|
| 22 | SSH (remote access) | TCP |
| 80 | HTTP (web, unencrypted) | TCP |
| 443 | HTTPS (web, encrypted) | TCP |
| 3306 | MySQL | TCP |

| Feature | TCP | UDP |
|---------|-----|-----|
| Reliability | Guaranteed delivery | No guarantee |
| Speed | Slower | Faster |
| Use case | Web, APIs, databases | Streaming, gaming |

<!-- Speaker notes: Tell students to memorize three ports: 22 (SSH), 80 (HTTP), 443 (HTTPS). In Module 03 of the bootcamp, they will configure security groups that open specific ports. TCP vs UDP matters for understanding why most cloud services use TCP. Approximately 4 minutes. -->

---

## Knowledge Check: Which Port Does HTTPS Use?

A) 22
B) 80
C) 443
D) 3306

<!-- Speaker notes: Answer: C) 443. Port 22 is SSH, port 80 is unencrypted HTTP, and port 3306 is MySQL. Ask students to raise hands or use a quick poll. This reinforces the port table from the previous slide. Approximately 2 minutes. -->

---

# How the Internet Works

<!-- Speaker notes: Transition slide. This section takes approximately 8 minutes across two slides. It builds directly on the networking concepts just covered. -->

---

## HTTP Request/Response Cycle

1. **DNS lookup:** Browser resolves the domain name to an IP address
2. **TCP connection:** Browser establishes a connection to the server
3. **HTTP request:** Browser sends a request (method, path, headers)
4. **Server processing:** Server processes the request
5. **HTTP response:** Server returns a status code and body

```
GET /index.html HTTP/1.1
Host: www.example.com
```

<!-- Speaker notes: Walk through each step. Emphasize that this cycle happens every time you load a web page or call an API. In Module 09 (Serverless), students will build APIs that handle these HTTP requests through API Gateway. Approximately 4 minutes. -->

---

## HTTP Status Codes

| Code Range | Category | Common Codes |
|------------|----------|-------------|
| 200-299 | Success | 200 OK, 201 Created |
| 300-399 | Redirect | 301 Moved Permanently |
| 400-499 | Client error | 400 Bad Request, 403 Forbidden, 404 Not Found |
| 500-599 | Server error | 500 Internal Server Error, 503 Unavailable |

- **403** means a permissions problem
- **404** means the resource was not found
- **502** means the server behind a load balancer is not responding

<!-- Speaker notes: Status codes are your first clue when troubleshooting cloud applications. Students will encounter these codes when working with API Gateway, ALB health checks, and Lambda function responses. Approximately 4 minutes. -->

---

## Key Takeaways

- Networking is how computers communicate. IP addresses identify devices, ports identify services, and DNS translates names to addresses.
- HTTP powers the web and cloud APIs. Know GET, POST, and status codes (200, 403, 404, 500).

<!-- Speaker notes: Two key takeaways for this module. Reinforce that every networking concept covered today connects directly to a bootcamp module: VPCs, security groups, Route 53, and API Gateway. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: Networking Commands and HTTP Requests**

What you will do:
- Test connectivity with `ping`
- Look up domain names with `nslookup`
- Make HTTP requests with `curl`

**Duration:** 10 to 12 minutes
**No cloud resources created. Everything runs on your local machine.**

Questions?

<!-- Speaker notes: Walk through the lab structure briefly. Remind students that the lab is fully guided with exact commands and expected output for every step. The lab uses curl to make HTTP requests, which connects directly to the REST API concepts in Module 04. Take 2 to 3 minutes for questions before transitioning to the lab. -->
