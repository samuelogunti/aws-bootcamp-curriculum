# IT Fundamentals Module 03: Quiz

1. What is the primary purpose of DNS (Domain Name System)?

   A) To encrypt data sent between two computers
   B) To translate human-readable domain names into IP addresses
   C) To assign port numbers to running services
   D) To route data packets between networks

2. Which port number does HTTPS (encrypted web traffic) use by default?

   A) 22
   B) 80
   C) 443
   D) 3306

3. What does an HTTP 404 status code mean?

   A) The server encountered an internal error
   B) The request was successful
   C) The client is not authorized to access the resource
   D) The requested resource was not found on the server

---

<details>
<summary>Answer Key</summary>

1. **B) To translate human-readable domain names into IP addresses**
   DNS converts domain names like `www.example.com` into IP addresses like `93.184.216.34` so that computers can locate each other on the network. Without DNS, you would need to memorize numerical IP addresses for every website.
   Further reading: [What Is DNS? (Cloudflare Learning Center)](https://www.cloudflare.com/learning/dns/what-is-dns/)

2. **C) 443**
   Port 443 is the default port for HTTPS, which is HTTP with TLS encryption. Port 22 is used for SSH, port 80 is used for unencrypted HTTP, and port 3306 is used for MySQL databases.
   Further reading: [What Is HTTPS? (Cloudflare Learning Center)](https://www.cloudflare.com/learning/ssl/what-is-https/)

3. **D) The requested resource was not found on the server**
   A 404 status code means the server could not find the resource at the requested URL. This commonly occurs when a page has been moved or deleted, or when the URL contains a typo. A 500 code indicates a server error, 200 indicates success, and 403 indicates a permissions issue.
   Further reading: [HTTP 404 (MDN Web Docs)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/404)

</details>
