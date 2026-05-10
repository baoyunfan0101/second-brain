---
tags:
  - networking
summary: UDP segment
use_cases:
  - web
  - streaming
  - gaming
  - dns
---

# UDP

## Overview

<span class="abbr" data-title="User Datagram Protocol">UDP</span> is a ==transport-layer== protocol that delivers data using independent packets.

**Characteristics:**
- Connectionless: no setup before sending data
- Best-effort: no guarantee of delivery, order, or duplication
- Datagram-based: each packet is handled independently

**Goal:**
- Fast, lightweight data delivery with minimal overhead

## UDP Segment Structure

**Key Fields:**
- Source Port / Destination Port
	Identify sending and receiving applications
- Length
	Total length of UDP segment (header + data)
- Checksum
	Basic error detection (optional in IPv4, mandatory in IPv6)

## Typical Use Cases

- Real-time applications:
	Video/voice streaming
	Online gaming
- Simple request-response:
	[[Web Request Lifecycle|DNS]] queries