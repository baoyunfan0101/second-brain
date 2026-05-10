---
tags:
  - networking
  - lan
  - arp
summary: request/reply, broadcast/unicast
use_cases:
  - packets
  - protocols
  - web
---

# ARP

## What is ARP

<span class="abbr" data-title="Address Resolution Protocol">ARP</span> is a ==link-layer== protocol that resolves an IP address to a MAC address within a <span class="abbr" data-title="Local Area Network">LAN</span>.

- Purpose:
	Resolve [[IP|IP]] -> <span class="abbr" data-title="MAC">MAC</span>
- Scope:
	Does not cross routers

## ARP Resolution Process

- Sender: `192.168.1.10` (`3C:7A:91:2F:4B:8C`)
- Target: `192.168.1.20` (unknown MAC)

1. Check ARP Cache
	- ARP cache on sender: no entry for `192.168.1.20`

2. ARP request (broadcast)
	- dst MAC = `FF:FF:FF:FF:FF:FF` (all hosts in LAN)
	- src MAC = `3C:7A:91:2F:4B:8C`
	- msg: Who has `192.168.1.20`? Tell `192.168.1.10`

3. ARP reply (unicast)
	- dst MAC = `3C:7A:91:2F:4B:8C` (sender MAC)
	- src MAC = `8E:21:5D:9A:3C:77`
	- msg: `192.168.1.20` is at `8E:21:5D:9A:3C:77`

4. Update ARP Cache
	- ARP cache on sender: `192.168.1.20` -> `8E:21:5D:9A:3C:77`

5. Send data frame
	- dst MAC = `8E:21:5D:9A:3C:77`
	- src MAC = `3C:7A:91:2F:4B:8C