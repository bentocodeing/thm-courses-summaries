# Module 3 - How The Web Works

# DNS in Detail

## Overview

DNS (Domain Name System) is a system that translates human-readable domain names into IP addresses that computers use to communicate on the internet. Instead of remembering complex IP addresses like 104.26.10.229, you can use domain names like tryhackme.com. DNS works much like an address system for the internet—just as every house has a unique address for receiving mail, every computer has its own unique IP address for communication.

## Domain Hierarchy

DNS operates in a hierarchical structure with multiple levels working together to resolve domain names.

**Top-Level Domain (TLD):** The rightmost part of a domain name, such as .com in tryhackme.com. TLDs are either generic (gTLD) like .com, .org, .edu, .gov, or country-code (ccTLD) like .ca (Canada), .co.uk (United Kingdom), .gov (government), etc. Historically, gTLDs indicated the domain's purpose (commercial, organization, education), while ccTLDs were for geographical purposes. Due to demand, new gTLDs now include .online, .club, .website, .biz, and many others. Over 2000 TLDs now exist.


**Second-Level Domain:** The part immediately before the TLD. In tryhackme.com, "tryhackme" is the second-level domain. It can contain up to 63 characters and can only use letters (a-z), numbers (0-9), and hyphens—but cannot start or end with hyphens, and cannot have consecutive hyphens.


**Subdomain:** Sits to the left of the second-level domain, separated by a period. For example, in admin.tryhackme.com, "admin" is the subdomain. Subdomains follow the same character restrictions as second-level domains (63 characters max, a-z, 0-9, hyphens). You can create multiple nested subdomains (like jupiter.servers.tryhackme.com), but the entire domain name cannot exceed 253 characters total.


## DNS Record Types

DNS isn't just for websites—multiple record types exist for different purposes. The most common ones include:

**A Record:** Resolves domain names to IPv4 addresses (e.g., 104.26.10.229). This is the standard record type for linking domain names to their web servers.


**AAAA Record:** The IPv6 equivalent of A records, resolving to IPv6 addresses (e.g., 2606:4700:20::681a:be5). As IPv6 adoption grows, these records become increasingly important.


**CNAME Record:** Stands for Canonical Name and allows one domain to point to another domain name. For example, TryHackMe's online shop (store.tryhackme.com) might use a CNAME record pointing to shops.shopify.com, which then requires another DNS lookup to find the actual IP address. This creates an extra step but enables flexibility in infrastructure.


**MX Record:** Mail eXchange records specify which servers handle email for the domain. They include a priority flag indicating which server to try first if the primary mail server is down. For example, tryhackme.com's MX record points to alt1.aspmx.l.google.com. Multiple nameservers can exist for a domain to provide redundancy.


**TXT Record:** Free-text fields for storing text-based data. Common uses include authenticating that you have authority to send emails on behalf of the domain (preventing spam and spoofed emails) and verifying domain ownership when signing up for third-party services.


## The DNS Request Process

When you request a domain name, a five-step process occurs:



    1. **Local Cache Check:** Your computer first checks its local DNS cache to see if you've recently resolved this domain. If found, the resolution ends immediately (common for popular sites like Google and Facebook). If not found, a request goes to your Recursive DNS Server.
    2. **Recursive DNS Server:** Usually provided by your ISP (but you can choose your own), this server also maintains a local cache of recently looked-up domains. If it finds a cached result locally, it returns this to your computer and the request ends. If not, it begins a journey through the internet's root DNS servers to find the answer.
    3. **Root Servers:** The backbone of the internet's DNS infrastructure. Root servers redirect you to the appropriate Top-Level Domain server based on your request. If you request [www.tryhackme.com](http://www.tryhackme.com/), the root server recognizes the .com TLD and refers you to the correct TLD server for .com addresses.
    4. **TLD Server:** Holds records showing where to find the authoritative server responsible for the specific domain. For tryhackme.com, the TLD server knows the nameservers are kip.ns.cloudflare.com and uma.ns.cloudflare.com. Multiple nameservers typically exist as backup in case one fails.
    5. **Authoritative DNS Server:** The definitive source storing all DNS records for the particular domain name and handling updates to those records. Depending on the record type, the server returns the appropriate response. The result is sent back to the Recursive DNS Server, where a local copy is cached for future requests, then relayed back to your original client.

**Caching and TTL:** Every DNS record comes with a TTL (Time To Live) value—a number in seconds indicating how long the response should remain cached locally before requiring a new lookup. Caching significantly reduces the need to make constant DNS requests, saving bandwidth and improving response times.


## Key Facts to Remember

- DNS resolves human-readable domains to IP addresses through a hierarchical system
- The domain hierarchy consists of TLD, second-level domain, and optional subdomains
- Multiple DNS record types serve different purposes: A (IPv4), AAAA (IPv6), CNAME (aliases), MX (email), TXT (text/authentication)
- DNS requests follow a five-step process: local cache → recursive server → root → TLD → authoritative server
- Each DNS record includes a TTL value specifying how long it can be cached
- Multiple nameservers provide redundancy for domain resolution
- Understanding DNS is critical for penetration testing, as DNS poisoning, enumeration, and spoofing are common attack vectors


## Red Team & Penetration Testing Relevance

DNS is a prime target for attackers. Penetration testers should understand how to: enumerate subdomains to discover hidden services, perform DNS queries to identify infrastructure, exploit DNS poisoning attacks to redirect traffic, and use DNS exfiltration to steal data. Attackers often target the caching mechanism to serve malicious responses, and DNS enumeration is one of the first reconnaissance steps in hacking assessments. Tools like nslookup and dig are essential for any security professional's toolkit.

***

# HTTP in Detail

## Overview: What is HTTP(S)?

HTTP (HyperText Transfer Protocol) is the foundational protocol for web communication, developed by Tim Berners-Lee between 1989-1991. It is the set of rules governing how data is transmitted between web browsers and servers, enabling the transfer of HTML, images, videos, and other content across the internet.

HTTPS is the secure version of HTTP. It encrypts data in transit, preventing eavesdropping and ensuring you're communicating with the legitimate server, not an imposter. This is critical for any authentication or sensitive data exchange.

## URLs and Request Structure

A URL (Uniform Resource Locator) is the address you use to access web resources. Each component serves a specific purpose:

**Scheme** (e.g., http, https, ftp) specifies the protocol for accessing the resource. **Host/Domain** is the domain name or IP address of the server you're accessing. **Port** is where you connect (80 for HTTP, 443 for HTTPS, but can be any port from 1-65535). **Path** identifies the specific file or resource location on the server.

**Query String** provides extra parameters—for example, `/blog?id=1` tells the server you want blog article with ID 1. **Fragment** references a specific location within a page and typically doesn't get sent to the server.

**User** credentials can be embedded in URLs for authentication. In a complete HTTP request, you send the method, HTTP version, and request line: `GET / HTTP/1.1` where GET is the method, / is the path, and HTTP/1.1 is the protocol version.

## HTTP Methods

HTTP methods tell the server what action the client wants to perform:

**GET** is used for retrieving information from a server. This is the most common method and should be idempotent—multiple identical GET requests produce the same result without changing server state.

**POST** is used for submitting data to the server and creating new records. POST requests typically include form data or JSON payloads in the request body. A successful POST often returns a 201 status code (Created).

**PUT** is used for updating existing resources on the server. You send the updated data to replace the entire resource at the specified path.

**DELETE** is used for removing resources from the server. A DELETE request to `/user/1` would delete the user with ID 1.

**Practical Note**: In security testing and penetration testing, you may encounter web applications that don't properly validate HTTP methods. An attacker might use PUT or DELETE on endpoints that only accept GET, or bypass security by changing the method.

## HTTP Status Codes

Status codes are grouped into five ranges, each with a specific meaning:

**100-199 (Information)**: Informational responses telling the client to continue sending their request. These are rarely seen in modern applications.

**200-299 (Success)**: The request was successful.

    - 200 OK: Standard successful response
    - 201 Created: A new resource was created (common after POST requests)

**300-399 (Redirection)**: The resource has moved or temporary conditions apply.

    - 301 Moved Permanently: The page has moved to a new location; search engines should update their index
    - 302 Found: A temporary redirect; the resource may return to this location later

**400-499 (Client Errors)**: The client made an error in their request.

    - 400 Bad Request: Malformed syntax or missing required parameters
    - 401 Not Authorised: Authentication required but not provided
    - 403 Forbidden: The server understands but refuses to grant permission, regardless of authentication
    - 404 Page Not Found: The resource doesn't exist
    - 405 Method Not Allowed: You sent the wrong HTTP method for that endpoint (e.g., GET when POST is required)

**500-599 (Server Errors)**: The server failed to process a valid request.

    - 500 Internal Service Error: Unexpected server error; often indicates a crash or code exception
    - 503 Service Unavailable: Server is overloaded or in maintenance

**Red Team Relevance**: Status codes leak information about application behavior. A 401 reveals authentication is required; a 403 means you're blocked; a 405 tells you which method the endpoint accepts. By probing different methods and observing responses, attackers can map the attack surface.

## Headers: The Silent Communicators

Headers are metadata sent with both requests and responses. Without proper headers, websites often don't render correctly in browsers.


**Common Request Headers** (sent by the client):

**Host** tells the server which website is being requested—essential for servers hosting multiple sites.

**User-Agent** identifies your browser and version (e.g., Mozilla/5.0 Firefox/87.0). Servers use this to format content appropriately; JavaScript and CSS features vary by browser.

**Content-Length** tells the server how much data to expect when sending form data or file uploads. The server uses this to ensure no data is lost.

**Accept-Encoding** lists compression methods your browser supports (gzip, deflate, etc.). Servers can compress responses, reducing bandwidth.

**Cookie** sends stored cookie data back to the server to maintain session state or user preferences.


**Common Response Headers** (sent by the server):

**Set-Cookie** instructs the browser to save data locally. On subsequent requests, the browser automatically resends this data, enabling session tracking and personalization without passwords on every request.

**Cache-Control** specifies how long the browser should store the response before requesting it again. Long cache times reduce server load; short times ensure fresh content.

**Content-Type** tells the browser what kind of data is coming (text/html, application/json, image/png, etc.). The browser uses this to parse and render the content correctly.

**Content-Encoding** specifies how the response was compressed (gzip, deflate). The browser automatically decompresses it.

**Red Team Relevance**: Missing or misconfigured security headers can expose vulnerabilities. Missing `X-Frame-Options` allows clickjacking. Missing `Content-Security-Policy` allows injection attacks. Headers also reveal software versions and technologies in use, aiding reconnaissance.

## Cookies: Stateless Protocol, Stateful Browsing

HTTP is stateless—each request is independent. Cookies solve this by storing data locally on your computer that gets sent back to the server with each request.

**Cookie Lifecycle**: The server sends a Set-Cookie header in a response. Your browser stores this data. On every subsequent request to that domain, the browser automatically includes the cookie data. The server reads the cookie to identify you, remember preferences, or maintain a session.

**Purpose**: Cookies are most commonly used for website authentication and session management. Instead of sending your password with every request, you log in once, receive a session token in a cookie, and the server uses that token to identify you on future requests.

**Token vs. Plaintext**: Cookie values are typically opaque tokens (unique secret codes), not plaintext passwords. A session token can't easily be converted back to reveal your password—it's a one-way identifier.

**Developer Tools Access**: You can view cookies sent by a website using browser developer tools (press F12, go to Network tab, click a request, and view the Cookies tab). This lets you see exactly what data the browser is sending.

**Red Team Relevance**: If a cookie value is predictable or uses weak randomization, attackers can forge session tokens and hijack accounts. If cookies are transmitted over HTTP (not HTTPS), attackers can intercept them. Cookie theft through XSS (Cross-Site Scripting) is a common attack vector—malicious JavaScript steals cookies and sends them to an attacker's server.

## The Complete Request/Response Cycle

A complete HTTP interaction involves a request line specifying the method and path, multiple headers providing metadata, and optionally a body containing data.


**Example Request**:

```javascript
textGET / HTTP/1.1
Host: tryhackme.com
User-Agent: Mozilla/5.0 Firefox/87.0
Referer: https://tryhackme.com/
[blank line signals end of headers]

```

The first line specifies the method (GET), path (/), and protocol version (HTTP/1.1). Host identifies the domain. User-Agent identifies the browser. Referer tells the server which page linked to this request. A blank line marks the end of headers.


**Example Response**:

```javascript
textHTTP/1.1 200 OK
Server: nginx/1.15.8
Date: Fri, 09 Apr 2021 13:34:03 GMT
Content-Type: text/html
Content-Length: 98

<html>
<head>
  <title>TryHackMe</title>
</head>
<body>
  Welcome To TryHackMe.com
</body>
</html>

```

The first line includes the protocol version, status code (200), and status message (OK). Server identifies the web server software. Date provides the current time. Content-Type tells the browser what type of data follows. Content-Length ensures the browser knows when the response ends. A blank line separates headers from the body. Lines 7-14 are the HTML response body.

## Practical Hacking Applications

**Reconnaissance**: Probing HTTP methods and observing status codes reveals what endpoints exist and what methods they accept. A 405 Method Not Allowed response tells you exactly which method the endpoint expects.

**Exploitation**: If a server accepts PUT or DELETE on endpoints it shouldn't, attackers can modify or delete data. If authentication is missing on sensitive endpoints, attackers access them directly. If headers reveal old software versions, attackers search for known vulnerabilities.

**Session Hijacking**: Weak cookie generation or transmission over HTTP allows attackers to steal or forge session tokens, impersonating users.

**Cache Poisoning**: Misconfigured cache headers allow attackers to inject malicious cached responses.

**Injection Attacks**: Missing Content-Security-Policy and X-Frame-Options headers enable XSS and clickjacking.

## Key Facts to Remember Before a Lab

- HTTP runs on port 80, HTTPS on 443
- GET retrieves data (idempotent), POST creates data (side effects), PUT updates, DELETE removes
- 2xx codes = success, 3xx = redirect, 4xx = client error (you made a mistake), 5xx = server error (server crashed)
- 401 = authentication required, 403 = authenticated but not permitted, 404 = resource doesn't exist
- Headers provide metadata; requests and responses both use them
- Cookies maintain state in a stateless protocol; stored as tokens, not plaintext
- HTTP requests always end with a blank line separating headers from the body
- Check your HTTP methods, headers, and status codes—they reveal vulnerabilities

***

# How Websites Work

## Overview

Websites operate through a client-server architecture where your browser (the client) makes requests to a web server, which responds with data that your browser uses to render the page. Understanding how websites are created and function is essential for both development and security testing. Websites are built using three core technologies: HTML for structure, CSS for styling, and JavaScript for interactivity.

## How Websites Work

**The Basic Request-Response Flow**

When you visit a website, your browser makes a request to a web server asking for information about the page you're visiting. The server responds with data that your browser uses to show you the page. A web server is simply a dedicated computer somewhere else in the world that handles your requests.


Websites have two major components:

1. **Front End (Client-Side)** — The way your browser renders a website. This is what you see and interact with.
2. **Back End (Server-Side)** — A server that processes your request and returns a response.


The browser communicates with the server through HTTP requests, and the server sends back HTML, CSS, and JavaScript that the browser interprets to display the page. The key principle to remember is that the client makes a request, and the server responds with data.

## HTML

**What HTML Is**

HyperText Markup Language (HTML) is the language websites are written in. Elements (also called tags) are the building blocks of HTML pages and tell the browser how to display content. Every website follows a standard structure.


**HTML Document Structure**

Every HTML document follows this basic template:

```javascript
text<!DOCTYPE html>
<html>
  <head>
    <title>Page Title</title>
  </head>
  <body>
    <h1>Example Heading</h1>
    <p>Example paragraph.</p>
  </body>
</html>

```

The key components are:

- **`<!DOCTYPE html>`** — Defines that the page is an HTML5 document. This standardization tells the browser to use HTML5 to interpret the page.
- **`<html>`** — The root element of the HTML page; all other elements nest inside it.
- **`<head>`** — Contains information about the page (such as the page title). Content in the head is not shown in the browser.
- **`<body>`** — Defines the HTML document's body; only content inside the body is shown in the browser.
- **`<h1>`** — Defines a large heading.
- **`<p>`** — Defines a paragraph.


**HTML Attributes and Styling**

Tags can contain attributes such as the `class` attribute, which can be used to style an element (e.g., make a tag a different color). Another common attribute is the `src` attribute, which is used on images to specify the location of an image.

Elements can have multiple attributes, each with its own unique purpose. Example: `<p attribute1="value1" attribute2="value2">`.

The `id` attribute is unique to the element. Unlike the `class` attribute where multiple elements can use the same class, an element must have a different `id` to identify them uniquely. Element ids are used for styling and to identify it by JavaScript.


**Other Important Tags**

- `<button>` — Creates a clickable button.
- `<img>` — Displays an image on the page.
- Lists and much more.


**Practical Security Note**

You can view the HTML of any website by right-clicking and selecting "View Page Source" (Chrome) or "Show Page Source" (Safari). This is crucial for security testing, as developers may have forgotten to remove sensitive information from the source code.

## JavaScript

**What JavaScript Does**

JavaScript (JS) is one of the most popular coding languages and allows pages to become interactive. HTML is used to create the website structure and content, while JavaScript is used to control the functionality of web pages. Without JavaScript, a page would not have interactive elements and would always be static. JS can dynamically update the page in real-time, such as changing the style of a button when a user clicks it or displaying moving animations.


**How JavaScript Is Included**

JavaScript is added within the page source code and can be either loaded within `<script>` tags or included remotely with the `src` attribute:

```javascript
javascript<script src="/location/of/javascript_file.js"></script>

```


**JavaScript Fundamentals**

JavaScript can select HTML elements and modify them. Example code that finds an HTML element with the id of "demo" and changes the element's contents to "Hack the Planet":

```javascript
javascriptdocument.getElementById("demo").innerHTML = "Hack the Planet";

```


**Event Handlers**

HTML elements can have events such as "onclick" or "onhover" that execute JavaScript when the event occurs. The following code changes the text of the element with demo ID when a button is clicked:

```javascript
xml<button onclick="document.getElementById('demo').innerHTML = 'Button Clicked';"> Click Me!</button>

```

Onclick events can also be defined inside the JavaScript script tags, not on elements directly.


**Practical Application**

The key principle is that when a user has control of how their input is displayed, they can submit malicious code. If the user adds their own HTML or JavaScript in a field, and that input is used on the page, they can control the page's appearance and functionality.

## Sensitive Data Exposure

**What Is Sensitive Data Exposure**

Sensitive Data Exposure occurs when a website doesn't properly protect (or remove) sensitive clear-text information to the end-user; it's usually found in a site's frontend source code. Websites are built using many HTML elements (tags), all of which can be seen by "viewing the page source." A website developer may have forgotten to remove login credentials, hidden links to private parts of the website, or other sensitive data shown in HTML or JavaScript.


**How Attackers Exploit It**

Sensitive information can be potentially leveraged to further an attacker's access within different parts of a web application. For example, there could be HTML comments with temporary login credentials. If you viewed the page's source code and found this, you could use these credentials to log in elsewhere on the application (or worse, use them to access other backend components of the site).


**Red Team Application**

Whenever you're assessing a web application for security issues, one of the first things you should do is review the page source code to see if you can find any exposed login credentials or hidden links. Viewing source code is the absolute first step in reconnaissance and exploitation.

## HTML Injection

**What Is HTML Injection**

HTML Injection is a vulnerability that occurs when unfiltered user input is displayed on the page. If a website fails to sanitize user input (filter any "malicious" text that a user inputs into a website), and that input is used on the page, an attacker can inject HTML code into a vulnerable website.


**How It Works**

Input sanitization is very important in keeping a website secure, as information a user inputs into a website is often used in other frontend and backend functionality. A vulnerability you'll explore in another lab is database injection, where you can manipulate a database lookup query to log in as another user by controlling input that's directly used in the query. For now, let's focus on HTML injection (which is client-side).


**The Attack Mechanism**

When a user has control of how their input is displayed, they can submit HTML or JavaScript code. The browser will then use that code on the page, allowing the user to control the page's appearance and functionality.

The general rule is: **Never trust user input.** To prevent malicious input, the website developer should sanitise everything the user enters before using it in a JavaScript function. In this case, the developer could remove any HTML tags from the user's input.


**Practical Example**

A typical HTML injection attack flow:

1. User types information into an input field.
2. The input is used in a JavaScript function to show the name on the page.
3. If the user adds their own HTML or JavaScript in the field, it's used in the sayHi function and added to the page — this means you can add your own HTML (such as an `<h1>` tag) and it will output your input as pure HTML.


**Red Team Relevance**

HTML injection allows attackers to manipulate what users see on a website. By injecting malicious links (such as to `http://hacker.com`), you could trick users into clicking on them. This is particularly dangerous when combined with other attack vectors like social engineering.

## Key Facts to Remember

**Core Concepts**

- Websites operate on a request-response model: browser requests → server responds
- Three layers work together: HTML (structure), CSS (styling), JavaScript (functionality)
- Always view page source first when testing for vulnerabilities


**HTML**

- `<!DOCTYPE html>`, `<html>`, `<head>`, `<body>` form the basic structure
- Tags are elements; attributes modify their behavior
- `id` attributes are unique; `class` attributes can be reused
- Source code is always visible to users via "View Page Source"


**JavaScript**

- Adds interactivity and dynamic updates to static HTML
- Uses `document.getElementById()` to select and modify elements
- Event handlers (onclick, onhover) trigger JavaScript functions
- Never trust user input that gets displayed on the page


**Sensitive Data Exposure**

- Check source code first for credentials, API keys, and hidden links
- Developers often forget to remove test data, comments with passwords, etc.
- This is your first step in reconnaissance before exploitation


**HTML Injection**

- Unsanitized user input can be rendered as HTML/JavaScript
- Always sanitize user input before displaying it
- Attackers can inject malicious links or modify page content
- Principle: Never trust user input


**For Penetration Testing**

- Start every assessment by viewing the page source
- Look for hardcoded credentials in HTML comments
- Test input fields for HTML/JavaScript injection
- Understand the client-server flow to identify where validation is missing
- Remember that client-side validation can be bypassed; server-side validation is what matters

***

# Putting it all together

## **How the Web Request Flow Works**

When you request a website, a sequence of components work together seamlessly. Your computer uses DNS to find the server's IP address, then communicates via the HTTP protocol to retrieve content. The web server responds with HTML, JavaScript, CSS, and images, which your browser renders for display. Several additional components enhance efficiency and security throughout this process.

## **Load Balancers: Distributing Traffic**

Load balancers sit in front of web servers to handle high traffic and ensure availability. When you send a request, the load balancer receives it first and forwards it to one of multiple backend servers using specific algorithms. The two common approaches are round-robin, which distributes requests sequentially to each server, and weighted balancing, which directs traffic to the least busy server based on current load. Load balancers continuously perform health checks on each server—periodic tests to verify it's responding correctly. If a server fails to respond appropriately, the load balancer temporarily stops routing traffic to it until it recovers. This prevents users from hitting unresponsive servers.

**Red Team Context**: Load balancers can be reconnaissance targets. Identifying the backend servers they manage could allow attackers to bypass the load balancer and hit individual servers directly, potentially exploiting unpatched or misconfigured instances.

## **Content Delivery Networks (CDNs)**

CDNs solve the problem of serving static files globally. They host copies of static assets (JavaScript, CSS, images, videos) on thousands of servers distributed worldwide. When a user requests a file, the CDN calculates the user's physical location and serves the content from the nearest server, dramatically reducing latency and bandwidth consumption. Instead of all traffic flowing to your origin server, the CDN handles it, allowing your website to scale without upgrading infrastructure.

## **Databases: Information Storage and Retrieval**

Websites need persistent storage for user data and dynamic content. Web servers communicate with databases to store and retrieve information. Databases range from simple text files to complex clusters of multiple servers (like MySQL, MSSQL, MongoDB, or PostgreSQL), each with specific strengths. Understanding which database type to use requires knowledge of your data structure, query patterns, and scaling needs.

**Red Team Context**: Databases are high-value targets. SQL injection, improper access controls, and data exfiltration attacks often target weak database security. Always treat database credentials and connections as sensitive.

## **Web Application Firewall (WAF): Your First Defense**

A WAF sits between the client's web request and the web server as a protective layer. Its primary role is to shield the server from hacking attacks and denial-of-service (DoS) attempts. The WAF analyzes incoming requests for common attack patterns (like SQL injection, XSS), distinguishes real browsers from bots, and implements rate limiting—restricting the number of requests from a single IP per second. If a request appears malicious or the rate limit is exceeded, the WAF drops it before it reaches your server.

**Red Team Context**: WAFs are detection points. Evasion techniques include: crafting requests that bypass signature-based detection, using legitimate traffic patterns to avoid rate limits, and exploiting WAF logic flaws. **Understanding WAF rules is crucial for successful penetration testing.**

## **How Web Servers Function**

**What is a Web Server?**

A web server is software that listens for HTTP connections and delivers web content to clients. Common examples include Apache, Nginx, IIS, and Node.js. It serves files from its root directory, a configurable location where your website files live. On Linux (Apache/Nginx), this is typically `/var/www/html`; on Windows (IIS), it's `C:\inetpub\wwwroot`. When you request a file like `https://www.example.com/picture.jpg`, the server looks for `/var/www/html/picture.jpg` and serves it directly from disk.


**Virtual Hosts: Multiple Sites, One Server**

Web servers can host multiple websites with different domain names on a single machine using virtual hosts. The server reads the hostname from the HTTP request header, matches it against its configuration, and serves the corresponding website. Each virtual host maps to a different root directory. For example, `one.com` might map to `/var/www/website_one` while `two.com` maps to `/var/www/website_two`. There's no limit to how many virtual hosts you can configure.

## **Static vs. Dynamic Content**

**Static Content** never changes. It includes pictures, JavaScript files, CSS stylesheets, and HTML that's served as-is directly from disk. The server doesn't modify it; it simply transmits the file to the client.

**Dynamic Content** changes based on requests. A blog homepage shows different posts depending on what's published. A search page displays different results based on your query. This interactivity requires backend processing.


The magic happens in the **Backend**—the processing layer hidden from browsers. Backend languages (PHP, Python, Ruby, Node.js, Perl, etc.) interact with databases, process user input, and generate custom HTML responses. Everything happening in the backend is invisible to clients; they only see the resulting HTML in their browser. The **Frontend** is everything users see—the HTML, rendered styling, and interactive elements.


**Red Team Context**: Backend code often contains logic flaws. Attackers exploit improper input validation, insecure database queries, and weak authentication in backend systems. Remember: never trust user input, even if client-side validation exists.

## **Key Practical Concepts to Remember**

- **DNS resolution** happens first—without it, your computer doesn't know which IP to connect to
- **Load balancers** enable scalability; single points of failure become distributed across multiple servers
- **CDNs** reduce latency globally; they're essential for modern high-traffic sites
- **WAFs** catch obvious attacks but sophisticated attackers bypass them through evasion
- **Virtual hosts** let one server appear as multiple websites
- **Backend code is invisible** to users—security flaws here are harder to spot but more dangerous
- **Static files** should be cacheable; dynamic content requires server-side processing
- Databases are frequently targeted in attacks; secure them aggressively

## **The Complete Web Request Sequence**

1. Client queries DNS for the domain's IP
2. DNS responds with the server's IP address
3. Client connects to the server's IP via HTTP
4. Client may hit a load balancer first, which forwards to a real backend server
5. Server processes the request (may query a database for dynamic content)
6. WAF inspects the request for threats
7. Server responds with HTML/CSS/JavaScript/media files
8. CDN may have served static files instead of the origin server
9. Client's browser renders the response

## **Red Team Summary**

To attack a web application, you need to understand each component's role. Reconnaissance identifies load balancers, WAF presence, and backend architecture. Exploitation targets weak links—poorly configured servers, unpatched software, insecure dynamic content generation, or database vulnerabilities. Defense-in-depth means compromising one component doesn't guarantee access to others.