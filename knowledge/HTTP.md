---
tags:
  - networking
  - http
summary: request/response messages, headers, status codes, versions (HTTP/1.1/2/3)
use_cases:
  - api
  - web
---

# HTTP

## What is HTTP

<span class="abbr" data-title="HyperText Transfer Protocol">HTTP</span> is an ==application-layer== protocol used for communication between clients and servers.

- Stateless (each request is independent, and the server does not remember any previous requests)
- Request–Response model
- Runs over [[TCP|]] (HTTP/1.1, HTTP/2) or QUIC (HTTP/3)

## Message Structure

### Request

```text
<method> <path> <version>
<header-name>: <value>
...

<body>
```

**Examples:**

```text
GET / HTTP/1.1
Host: www.example.com
User-Agent: curl/7.0
Accept: */*
```

```text
POST /login HTTP/1.1
Host: www.example.com
Content-Type: application/json
Content-Length: 27

{"username":"user","pwd":"123"}
```

**HTTP Methods:**

- GET: retrieve resource
- POST: submit data
- PUT: replace resource
- PATCH: partial update
- DELETE: remove resource
- HEAD: headers only
- OPTIONS: supported methods

**Common headers:**

- Host
	Target domain name
	Example: `www.google.com`
- User-Agent
	Client (browser / tool)
	Example: `Mozilla/5.0`, `curl/7.0`
- Accept
	Types the client can handle (<span class="abbr" data-title="Multipurpose Internet Mail Extensions">MIME</span> type)
	Example: `text/html`, `application/json`, `*/*`
- Accept-Encoding
	Supported compression methods
	Example: `gzip`, `br`, `deflate`
- Accept-Language
	Preferred languages
	Example: `en-US`, `zh-CN`
- Authorization
	Authentication credentials
	Example: `Bearer <token>`, `Basic <base64>`
- Cookie
	Stored client state
	Example: `session_id=abc123`
- If-None-Match
	Conditional request (paired with ETag)
	Example: `If-None-Match: "abc123"`
- If-Modified-Since
	Conditional request (paired with Last-Modified)
	Example: `If-Modified-Since: Wed, 21 Oct 2026 07:00:00 GMT`

### Response

```text
<version> <status-code> <reason>
<header-name>: <value>
...  
  
<body>
```

**Examples:**

```text
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 31
  
<html><body>Hello</body></html>
```

```text
HTTP/1.1 404 Not Found
Content-Type: application/json
  
{"error":"not found"}
```

**Status codes:**

- 1xx: Informational
	- 100 Continue
	- 101 Switching Protocols
	- 102 Processing

- 2xx: Success
	- ==200 OK== (Request succeeded, response contains the requested data)
	- 201 Created
	- 202 Accepted
	- 204 No Content
	- 206 Partial Content

- 3xx: Redirection
	- 300 Multiple Choices
	- ==301 Moved Permanently== (Resource has a new permanent URL)
	- ==302 Found== (Temporary redirect)
	- 303 See Other
	- ==304 Not Modified== (Resource not changed, use cached version)
	- 307 Temporary Redirect
	- 308 Permanent Redirect

- 4xx: Client Error
	- ==400 Bad Request== (Request format is invalid)
	- ==401 Unauthorized== (Authentication required)
	- 402 Payment Required
	- ==403 Forbidden== (Authenticated but not allowed)
	- ==404 Not Found== (Resource does not exist)
	- 405 Method Not Allowed
	- 406 Not Acceptable
	- 408 Request Timeout
	- 409 Conflict
	- 410 Gone
	- 413 Payload Too Large
	- 414 URI Too Long
	- 415 Unsupported Media Type
	- 416 Range Not Satisfiable
	- 418 I'm a teapot
	- 422 Unprocessable Entity
	- 429 Too Many Requests
	- 431 Request Header Fields Too Large
	- 451 Unavailable For Legal Reasons

- 5xx: Server Error
	- ==500 Internal Server Error== (Generic server failure)
	- 501 Not Implemented
	- ==502 Bad Gateway== (Server got invalid response from upstream service)
	- ==503 Service Unavailable== (Server overloaded or down)
	- ==504 Gateway Timeout== (Upstream service did not respond in time)
	- 505 HTTP Version Not Supported

**Common headers:**

- Content-Type
	Type of the response body (MIME type)
	Example: `text/html`, `application/json`
- Content-Length
	Size of the response body (in bytes)
	Example: `1234`
- Content-Encoding
	Compression used on response body
	Example: `gzip`, `br`
- Set-Cookie
	Client state
	Example: `session_id=abc123; Path=/; HttpOnly`
- Cache-Control
	Caching rules for the response
	Example: `max-age=3600`, `no-cache`, `no-store`
- ETag
	Resource version identifier (paired with If-None-Match)
	Example: `"abc123"`
- Last-Modified
	Last modification time of resource (paired with If-Modified-Since)
	Example: `Wed, 21 Oct 2026 07:00:00 GMT`

## Versions

**HTTP/1.0**
- Connection: short-lived (one request per TCP connection)
- Protocol: text-based
- Limitation:
	- High overhead (frequent TCP setup/teardown)

**HTTP/1.1**
- Connection: keep-alive (persistent by default)
- Protocol: text-based
- Features:
	- Reuse TCP connection for multiple requests
	- Requests/responses are matched by order (due to lack of identifiers, relying on TCP stream order)
- Limitation:
	- Head-of-line blocking (requests must be handled in the same order as requests)

**HTTP/2**
- Connection: single persistent connection
- Protocol: binary
- Features:
	- Each request/response is assigned a stream ID (explicit identification)
	- Multiplexing (multiple requests/responses in parallel over one connection)
	- Header compression (HPACK)
- Improvement:
	- Eliminates application-layer (HTTP/1.1) head-of-line blocking
- Limitation:
	- Transport-layer head-of-line blocking (TCP enforces in-order delivery, so a lost packet blocks all subsequent data across all streams)

**HTTP/3
- Connection: based on QUIC ([[UDP|UDP]])
- Protocol: binary
- Features:
	- Multiplexing with independent streams (no cross-stream blocking)
	- Faster connection setup (QUIC integrates transport + TLS, supports 1-<span class="abbr" data-title="Round Trip Time">RTT</span> / 0-RTT)
- Improvement:
	- Eliminates TCP head-of-line blocking
	- Lower latency and better performance on unstable networks

## HTTPS

HTTP over <span class="abbr" data-title="Transport Layer Security">TLS</span>

Provides:
- Encryption (data is encrypted during transmission)
- Integrity (data cannot be modified without detection)
- Authentication (server’s identity is verified via certificates)