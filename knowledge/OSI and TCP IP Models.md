---
tags:
  - networking
  - osi
  - tcp_ip
summary: OSI (7-layer model) and TCP/IP (4-layer model), ARP
use_cases:
  - communication
  - protocols
---

#  OSI and TCP IP Models

## <span class="abbr" data-title="Open Systems Interconnection">OSI</span> (7-layer model)

1. Physical
	- Bits (0/1)
	- Hardware: cables, signals

2. Data Link
	- Frames
	- <span class="abbr" data-title="MAC">MAC</span> addressing
	- Local network communication (Ethernet)
	- Protocols: [[ARP|ARP]]

3. Network
	- <span class="abbr" data-title="Internet Protocol">IP</span> addressing
	- Routing (path selection across networks)
	- Protocols: [[IP|IP]]

4. Transport
	- Process-to-process communication
	- Reliability, flow control
	- Protocols: [[TCP|TCP]]/[[UDP|UDP]]

5. Session
	- Session management (establish / maintain / terminate)

6. Presentation
	- Data representation (encoding, encryption, compression)

7. Application
	- Application-layer services
	- Protocols: [[HTTP|HTTP]], [[Web Request Lifecycle|DNS]], <span class="abbr" data-title="File Transfer Protocol">FTP</span>

## TCP/IP (4-layer model)

1. Link (Network Interface)
	- OSI Physical + Data Link
	- Local transmission (Ethernet, Wi-Fi)

2. Internet
	- OSI Network
	- IP, routing

3. Transport
	- OSI Transport
	- TCP/UDP

4. Application
	- OSI Session + Presentation + Application
	- HTTP, DNS, TLS