# Lab 00: Networking Commands and HTTP Requests

## Objective

Use networking commands to test connectivity, look up domain names, and make HTTP requests from the command line.

## Prerequisites

- A terminal application:
  - **macOS:** Terminal.app (pre-installed in Applications > Utilities)
  - **Windows:** Git Bash (bundled with [Git for Windows](https://git-scm.com/download/win))
  - **Linux:** Terminal (pre-installed)
- Module 02 (The Command Line) for basic terminal skills

## Duration

10 to 12 minutes

## Instructions

### Step 1: Test Network Connectivity with Ping (~3 min)

The `ping` command sends small packets to a server and measures the response time. The flag to limit the number of packets differs between operating systems.

**macOS/Linux:**

```bash
ping -c 3 google.com
```

**Windows (Git Bash):**

```bash
ping -n 3 google.com
```

**Windows (PowerShell):**

```powershell
ping -n 3 google.com
```

Expected output (times and IP will vary):

```
PING google.com (142.250.80.46): 56 data bytes
64 bytes from 142.250.80.46: icmp_seq=0 ttl=117 time=12.3 ms
64 bytes from 142.250.80.46: icmp_seq=1 ttl=117 time=11.8 ms
64 bytes from 142.250.80.46: icmp_seq=2 ttl=117 time=12.1 ms

--- google.com ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max/stddev = 11.8/12.1/12.3/0.2 ms
```

If you see replies, your computer can reach Google's servers. The `time` value shows the round-trip latency in milliseconds.

### Step 2: Look Up a Domain Name with nslookup (~3 min)

The `nslookup` command queries DNS to find the IP address associated with a domain name.

```bash
nslookup google.com
```

Expected output (addresses will vary):

```
Server:		192.168.1.1
Address:	192.168.1.1#53

Non-authoritative answer:
Name:	google.com
Address: 142.250.80.46
```

The `Server` line shows your DNS resolver (usually your router). The `Address` line under `Non-authoritative answer` shows the IP address that `google.com` resolves to.

### Step 3: Make an HTTP Request with curl (~4 min)

The `curl` command sends HTTP requests from the command line. The `-s` flag runs it in silent mode (no progress bar).

```bash
curl -s https://httpbin.org/ip
```

Expected output (your IP will differ):

```json
{
  "origin": "203.0.113.42"
}
```

This calls a public API that returns your public IP address in JSON format.

**View HTTP response headers:**

```bash
curl -s -I https://httpbin.org/ip
```

Expected output (values will vary):

```
HTTP/2 200
content-type: application/json
content-length: 32
```

The `-I` flag shows only the response headers. Notice the `200` status code (success) and the `content-type: application/json` header.

## Validation

Confirm the following before moving on:

- [ ] Running `ping -c 3 google.com` (or `ping -n 3 google.com` on Windows) returns replies with response times
- [ ] Running `nslookup google.com` returns an IP address
- [ ] Running `curl -s https://httpbin.org/ip` returns your public IP in JSON format

## Cleanup

No files or directories were created during this lab. No cleanup is needed.

## Challenge (Optional)

1. Use `nslookup` to look up the IP address for `aws.amazon.com`. Compare the result to `google.com`. Are they in the same IP range?

2. Use `curl` to make a request that returns a 404 status code:

   ```bash
   curl -s -o /dev/null -w "%{http_code}" https://httpbin.org/status/404
   ```

   Expected output: `404`

3. Try `curl -s https://httpbin.org/headers` to see what HTTP headers your terminal sends with each request.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
