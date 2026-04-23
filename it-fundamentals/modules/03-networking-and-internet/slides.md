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

## Prerequisites and Agenda

**Prerequisites:** Terminal skills from Module 02 (`curl` is used in the lab)

**Agenda:**
1. The TCP/IP model
2. IP addresses (private vs. public)
3. DNS (Domain Name System)
4. Ports and TCP vs. UDP
5. HTTP request/response cycle
6. HTTP methods and status codes
7. HTTPS and TLS

<!-- Speaker notes: This is the most content-heavy module. Approximately 1 minute. -->

---

# Networking Fundamentals

<!-- Speaker notes: Transition slide. Networking is how computers communicate. Every cloud service, website, and API call depends on networking. Understanding the basics here makes VPCs, security groups, and load balancers in the bootcamp much easier. -->

---

## The TCP/IP Model

| Layer | Name | Purpose | Example Protocols |
|-------|------|---------|-------------------|
| 4 | Application | Network services for apps | HTTP, HTTPS, DNS, SSH, FTP |
| 3 | Transport | Reliable or fast delivery | TCP (reliable), UDP (fast) |
| 2 | Internet | Routes data between networks | IP, ICMP |
| 1 | Network Access | Physical transmission | Ethernet, Wi-Fi |

- Data passes through all four layers when traveling between computers
- Your browser request goes down all 4 layers, across the network, and back up all 4 layers on the server

<!-- Speaker notes: Draw the TCP/IP layers on the whiteboard and trace a web request through all four layers. This visual makes the abstract model concrete. Approximately 4 minutes. -->

---

## IP Addresses

An IP address is a unique numerical label assigned to every device on a network.

**IPv4 format:** four numbers separated by dots (0-255 each)
```
192.168.1.100    (private)
10.0.0.1         (private)
93.184.216.34    (public)
```

- **Private IPs** are used within local networks, not routable on the public internet
- **Public IPs** are assigned by ISPs and are routable on the internet
- Your request goes: private IP, through router's public IP, across the internet, to server's public IP

<!-- Speaker notes: Emphasize that private IP ranges are used inside cloud networks (VPCs). Students do not need to memorize the ranges, but they should recognize that 10.x.x.x addresses are private. Approximately 3 minutes. -->

---

## Private IP Address Ranges

| Range | Class | Common Use |
|-------|-------|------------|
| 10.0.0.0 to 10.255.255.255 | Class A | Large networks, cloud VPCs |
| 172.16.0.0 to 172.31.255.255 | Class B | Medium networks |
| 192.168.0.0 to 192.168.255.255 | Class C | Home and small office networks |

> In Module 03 of the bootcamp, you will create VPCs using these private IP ranges (e.g., `10.0.0.0/16`).

<!-- Speaker notes: The 10.x.x.x range is the most common in AWS VPCs. Students will use it in Module 03 when creating their first VPC. Approximately 2 minutes. -->

---

## DNS (Domain Name System)

DNS translates domain names to IP addresses so you do not need to memorize numbers.

**DNS resolution process:**
1. You type `www.example.com` in your browser
2. Your computer asks a DNS resolver for the IP address
3. The resolver queries DNS servers (root, TLD, authoritative)
4. The resolver returns `93.184.216.34` to your computer
5. Your browser connects to that IP address

> **Bootcamp connection:** DNS is the foundation for Amazon Route 53 in Module 07: Load Balancing and DNS.

<!-- Speaker notes: DNS is critical for Route 53 in Module 07. Students will configure DNS records to point domain names to load balancers and other AWS resources. Approximately 3 minutes. -->

---

## Ports

A port identifies a specific service on a computer. IP address = building address, port = apartment number.

| Port | Protocol | Service |
|------|----------|---------|
| 22 | TCP | SSH (secure remote access) |
| 80 | TCP | HTTP (web, unencrypted) |
| 443 | TCP | HTTPS (web, encrypted) |
| 3306 | TCP | MySQL database |
| 5432 | TCP | PostgreSQL database |
| 3389 | TCP | RDP (Windows remote desktop) |

> Memorize three ports: **22** (SSH), **80** (HTTP), **443** (HTTPS).

<!-- Speaker notes: When students configure security groups in Module 03, they will specify which ports to open. Understanding ports now makes that configuration intuitive. Approximately 3 minutes. -->

---

## TCP vs. UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| Reliability | Guaranteed delivery; retransmits lost packets | No guarantee; packets may be lost |
| Speed | Slower (overhead for reliability) | Faster (no overhead) |
| Connection | Connection-oriented (handshake first) | Connectionless (send and forget) |
| Use cases | Web, email, APIs, databases | Video streaming, gaming, DNS lookups |

- Most cloud services use TCP because reliability matters for API calls and database queries
- UDP is used for real-time applications where speed matters more than reliability

<!-- Speaker notes: TCP vs UDP matters for understanding why most cloud services use TCP. Security groups in the bootcamp will specify TCP or UDP for each rule. Approximately 2 minutes. -->

---

## Knowledge Check: Which Port Does HTTPS Use?

A) 22
B) 80
C) 443
D) 3306

<!-- Speaker notes: Answer: C) 443. Port 22 is SSH, port 80 is unencrypted HTTP, and port 3306 is MySQL. Ask students to raise hands or use a quick poll. This reinforces the port table from the previous slide. Approximately 2 minutes. -->

---

# How the Internet Works

<!-- Speaker notes: Transition slide. This section takes approximately 8 minutes. It builds directly on the networking concepts just covered. -->

---

## HTTP Request/Response Cycle

1. **DNS lookup:** Browser resolves the domain name to an IP address
2. **TCP connection:** Browser establishes a connection (three-way handshake)
3. **HTTP request:** Browser sends a request with method, path, and headers
   ```
   GET /index.html HTTP/1.1
   Host: www.example.com
   ```
4. **Server processing:** Server processes the request and prepares a response
5. **HTTP response:** Server returns a status code and body

<!-- Speaker notes: Walk through each step. This cycle happens every time you load a web page or call an API. In Module 09 (Serverless), students will build APIs that handle these HTTP requests through API Gateway. Approximately 4 minutes. -->

---

## HTTP Methods

| Method | Purpose | Example |
|--------|---------|---------|
| GET | Retrieve data | Load a web page, fetch a list of users |
| POST | Create new data | Submit a form, create a new user |
| PUT | Replace existing data | Update an entire user record |
| PATCH | Partially update data | Change just the user's email |
| DELETE | Remove data | Delete a user account |

- GET and POST are the most common methods
- REST APIs use all five methods (covered in Module 04)

<!-- Speaker notes: These methods map directly to CRUD operations: Create (POST), Read (GET), Update (PUT/PATCH), Delete (DELETE). Students will use these when building APIs in Module 09. Approximately 2 minutes. -->

---

## HTTP Status Codes

| Code Range | Category | Common Codes |
|------------|----------|-------------|
| 200-299 | Success | 200 OK, 201 Created, 204 No Content |
| 300-399 | Redirect | 301 Moved Permanently, 302 Found |
| 400-499 | Client error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found |
| 500-599 | Server error | 500 Internal Server Error, 502 Bad Gateway, 503 Unavailable |

- **403** = permissions problem
- **404** = resource not found
- **502** = server behind a load balancer is not responding

<!-- Speaker notes: Status codes are your first clue when troubleshooting cloud applications. Students will encounter these codes when working with API Gateway, ALB health checks, and Lambda function responses. Approximately 3 minutes. -->

---

## HTTPS and TLS

- **HTTPS** = HTTP with encryption (TLS/Transport Layer Security)
- The padlock icon in your browser means the connection is encrypted
- TLS prevents anyone on the network from reading or modifying traffic

In cloud computing:
- You configure TLS certificates on load balancers and API endpoints
- Port 443 is HTTPS (encrypted), port 80 is HTTP (unencrypted)
- Always use HTTPS for production applications

<!-- Speaker notes: In Module 07, students will configure TLS certificates on Application Load Balancers. In Module 13 (Security in Depth), they will learn about certificate management with ACM. Approximately 2 minutes. -->

---

## Bootcamp Connections

> **Networking:** VPCs, subnets, and security groups in Module 03: Networking Basics
> **DNS:** Route 53 domain management in Module 07: Load Balancing and DNS
> **HTTP/APIs:** API Gateway and Lambda in Module 09: Serverless
> **HTTPS/TLS:** Certificate management in Module 13: Security in Depth

<!-- Speaker notes: Every networking concept covered today connects directly to a bootcamp module. This slide summarizes all the connections. Approximately 1 minute. -->

---

## Instructor Notes: Common Questions

**Q: How much networking do I need to know?**
Understand IP addresses, ports, DNS, and TCP vs. UDP. Module 03 of the bootcamp covers VPCs and subnets in detail.

**Q: What is the difference between HTTP and HTTPS?**
HTTPS is HTTP with TLS encryption. Port 80 is HTTP (unencrypted), port 443 is HTTPS (encrypted). Always use HTTPS for production.

<!-- Speaker notes: These are the two most common questions. Approximately 2 minutes. -->

---

## Key Takeaways

- The TCP/IP model has 4 layers: Application, Transport, Internet, Network Access
- IP addresses identify devices; ports identify services; DNS translates names to addresses
- Private IPs (10.x.x.x) are used inside cloud VPCs; public IPs are internet-routable
- Memorize ports 22 (SSH), 80 (HTTP), 443 (HTTPS)
- HTTP methods: GET (read), POST (create), PUT (update), DELETE (remove)
- Status codes: 200 (OK), 403 (forbidden), 404 (not found), 500 (server error)
- HTTPS encrypts traffic with TLS; always use it for production

<!-- Speaker notes: Seven key takeaways covering all major topics. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: Networking Commands and HTTP Requests**

What you will do:
- Test connectivity with `ping`
- Look up domain names with `nslookup`
- Make HTTP requests with `curl` and inspect status codes

**Duration:** 10 to 12 minutes
**No cloud resources created. Everything runs on your local machine.**

Questions?

<!-- Speaker notes: The lab uses curl to make HTTP requests, which connects directly to the REST API concepts in Module 04. Take 2 to 3 minutes for questions before transitioning to the lab. -->
