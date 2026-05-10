---
tags:
  - networking
summary: IP packet, routing, versions (IPv4/IPv6)
use_cases:
  - web
  - routing
---

# IP

## Overview

<span class="abbr" data-title="Internet Protocol">IP</span> is a ==network-layer== protocol responsible for delivering packets across networks.

**Characteristics:**
- Connectionless: no setup before sending data
- Best-effort: no guarantee of delivery, order, or duplication
- Datagram-based: each packet is handled independently

**Functions:**
- Logical addressing
- Routing (path selection across networks)

## IPv4 Packet Structure

**Key Header Fields:**

- Source IP / Destination IP
	Identify sender and receiver

- <span class="abbr" data-title="Time To Live">TTL</span>
	Decremented at each hop
	Prevents infinite routing loops

- Protocol
	Indicates upper-layer protocol
	Examples: TCP = 6, UDP = 17

- Total Length
	Entire packet size (header + data)

- Fragmentation Fields
	- Identification: identifies fragments of same packet
	- Flags (MF): more fragments indicator
	- Fragment Offset: position of fragment

## Packet Forwarding Process

1. Host creates IP packet
2. Router/host checks routing table
3. Uses **Longest Prefix Match**
	Select the route with the longest matching prefix of the destination IP address in binary
4. Determines:
	- next hop
	- outgoing interface
5. Forwards packet hop-by-hop until destination

## Same <span class="abbr" data-title="Local Area Network">LAN</span> vs. Different LAN

**Same LAN:**
- Use [[ARP|ARP]] to resolve destination MAC
- Send frame directly

**Different LAN:**
- Send packet to **default gateway**
- Actual destination MAC = gateway MAC
- Gateway forwards packet onward

## Packet Fragmentation

- When it occurs: packet size > <span class="abbr" data-title="Maximum Transmission Unit">MTU</span>
- Fragmented by: sender or intermediate routers (IPv4)
- Reassembled by: destination host
- Fields used:
	- Identification:
		Identifies fragments belonging to the same original packet
	- Fragment Offset:
		Indicates the position of the fragment in the original packet
	- MF (More Fragments) flag:
		Indicates if more fragments follow (1 = yes, 0 = last fragment)

## <span class="abbr" data-title="Network Address Translation">NAT</span>

- Definition:
	private IP -> public IP
- Benefits:
	Conserves IPv4 addresses
	Hides internal network
- Modifications:
	- Source IP: Replaced with the public IP of the NAT device
	- Source port (<span class="abbr" data-title="Port Address Translation">PAT</span>): Rewritten to a new port to distinguish multiple connections

## Subnetting

- <span class="abbr" data-title="Classless Inter-Domain Routing">CIDR</span> Notation:
	`/n`: number of bits used for the network prefix
- Example:
	`192.168.1.0/24`: first 24 bits are the network, last 8 bits are for hosts

## IPv4 vs. IPv6

| Feature        | IPv4                 | IPv6                    |
| -------------- | -------------------- | ----------------------- |
| Address length | 32-bit               | 128-bit                 |
| Fragmentation  | routers can fragment | routers do not fragment |
| Header         | complex              | simplified              |
