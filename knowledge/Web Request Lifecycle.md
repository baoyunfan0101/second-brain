---
tags:
  - networking
  - web
summary: URL -> cache -> DNS -> TCP/TLS -> HTTP -> CDN -> response -> rendering
use_cases:
  - web
  - caching
---

# Web Request Lifecycle

## 1. User Inputs URL

The user enters:
`https://www.example.com`

This contains:
- Scheme: `https` (use <span class="abbr" data-title="Transport Layer Security">TLS</span>/HTTPS)
- Host: `www.example.com`
- Path: `/` (default root)

The browser prepares to resolve and fetch this resource.

---

## 2. Local Cache Check

Before any network request, the browser checks:
- Browser <span class="abbr" data-title="Domain Name System">DNS</span> cache
	domain -> <span class="abbr" data-title="Internet Protocol">IP</span> mapping, stored in browser memory
- OS DNS cache
	domain -> IP mapping, shared by all applications
- HTTP cache
	full URL -> resource, stored in browser memory/disk

If a valid cached response exists, it may skip some steps.

---

## 3. DNS Resolution (Domain -> IP)

Steps:
1. Query local DNS resolver (<span class="abbr" data-title="Internet Service Provider">ISP</span> or public DNS)
2. If not cached, recursive lookup:
   - Root DNS servers (`.`)
   - <span class="abbr" data-title="Top-Level Domain">TLD</span> servers (`.com`/`.org`/`.net`)
   - Authoritative DNS (`example.com`)

<span class="abbr" data-title="Content Delivery Network">CDN</span>:
- Request flow:
	client -> edge IP (CDN edge node) -> origin server (on cache miss)
- Routing strategies:
	- **Geo-based DNS** (<span class="abbr" data-title="Open Systems Interconnection">OSI</span> Layer 7 / Application Layer, DNS protocol):
		A DNS-level routing strategy that directs users to different IP addresses based on geographic location.
	- **Anycast routing** (OSI Layer 3 / Network Layer, BGP routing):
		A network-level routing strategy where multiple geographically distributed servers share the same IP, and the network routes traffic to the nearest one.
- Selection factors:
	- Client geographic location
	- Network latency
	- Server load
	- Routing policies

The DNS response directs the client to a nearby edge node.

---

## 4. TCP Handshake

The browser establishes a reliable connection to the edge server via [[TCP|TCP]].

---

## 5. TLS Handshake (HTTPS)

When [[HTTPS and TLS|HTTPS]] is used, the browser establishes a secure encrypted channel over the TCP connection.

---

## 6. Request Sent

The browser sends an [[HTTP|HTTP]] request.

---

## 7. CDN Handling

**Case A: Cache Hit**
- The edge server already has the resource
- It returns the response immediately

Advantages:
- Low latency
- No origin server involvement

**Case B: Cache Miss**
- Edge forwards request to origin server
- Origin processes request and returns data
- Edge caches the response
- Edge sends response back to client

Flow:
Client -> CDN -> Origin -> CDN -> Client

---

## 8. Response Returned

The edge server returns the [[HTTP|HTTP]] response (from cache or origin).

---

## 9. Browser Rendering Pipeline

1. Parse HTML -> <span class="abbr" data-title="Document Object Model">DOM</span> tree
2. Fetch sub-resources (additional requests):
   - <span class="abbr" data-title="Cascading Style Sheets">CSS</span>
   - JavaScript
   - Images
   - Fonts
3. Build <span class="abbr" data-title="Cascading Style Sheets Object Model">CSSOM</span>
4. Construct Render Tree (DOM + CSSOM)
5. Layout (calculate positions)
6. Paint (draw pixels)
7. Composite (final display)

---

## 10. Final Page Display

JavaScript may continue to:  
- Fetch data (AJAX / fetch)  
- Update DOM dynamically