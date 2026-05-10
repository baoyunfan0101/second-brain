---
tags:
  - networking
  - tcp_ip
summary: TCP segment structure, three-way handshake, four-way teardown, reliability, retransmission, flow control, congestion control
use_cases:
  - protocols
  - web
---

# TCP

## What is TCP

<span class="abbr" data-title="Transmission Control Protocol">TCP</span> is a ==transport-layer== connection-oriented protocol that provides reliable, ordered data delivery between processes over an IP network.

- Connection-oriented
	Uses a three-way handshake to establish a connection
- Reliable
	Ensures data is delivered without loss, duplication, or corruption
- Ordered
	Data is received in the same order it was sent (via sequence numbers)
- Byte-stream
	Treats data as a continuous stream (no message boundaries)
- Flow control
	Uses a sliding window to match sender rate to receiver capacity
- Congestion control
	Adjusts sending rate based on network conditions

## TCP Segment Structure

**Overview:**

A TCP segment consists of:
```text
[TCP Header | Data]
```

- Header: control information
- Data: application payload (e.g., HTTP request)

**Key Header Fields:**

- Source Port (16 bits): sender's port  
- Destination Port (16 bits): receiver's port
- Sequence Number (seq) (32 bits):
	Byte index of the first data byte in this segment
- Acknowledgment Number (ack) (32 bits):
	Next expected byte from the peer (valid when ACK=1)
- Header Length (Data Offset):  
	Length of TCP header
- Flags (Control Bits):
	==SYN==: synchronize sequence numbers **(consumes 1 seq number)**
	ACK: acknowledgment field is valid
	==FIN==: finish sending **(consumes 1 seq number)**
	RST: reset connection
	PSH: push data to application immediately
	URG: urgent pointer is valid
- Window Size (rwnd):
	Receiver's available buffer
- Checksum:
	Error detection for header + data
- Options:
	MSS, Window Scale, SACK, etc.

**Example TCP Segment:**

Client sends an HTTP request:

TCP Header:
- Source Port: 52345
- Destination Port: 80
- Sequence Number: 1000
- Acknowledgment Number: 2000
- Flags: ACK=1, PSH=1
- Window Size: 65535

Data:
```text
GET /index.html HTTP/1.1
Host: example.com
```

## TCP Three-Way Handshake

**Goal:**

- Confirm bidirectional communication
- Synchronize initial sequence numbers

**Steps:**

1. Client -> Server
	SYN = 1
	seq = x
- Meaning: request connection, initial sequence number x

2. Server -> Client
	SYN = 1, ACK = 1
	seq = y
	ack = x + 1
- Meaning: accept connection, confirm client

3. Client -> Server
	ACK = 1
	ack = y + 1
- Meaning: confirm server

**Result:**

Connection established

## TCP Four-Way Teardown

**Goal:**

- Close connection in both directions (full-duplex)

**Steps:**

1. Client -> Server
	FIN = 1
- Meaning: client finished sending

2. Server -> Client
	ACK = 1
- Meaning: acknowledge client's FIN

3. Server -> Client
	FIN = 1
- Meaning: server finished sending

4. Client -> Server
	ACK = 1
- Meaning: acknowledge server's FIN

**Result:**

- Server: immediately CLOSED
- Client: CLOSED after 2<span class="abbr" data-title="Maximum Segment Lifetime">MSL</span>
	Handle lost ACK (allow retransmission)
	Ensure old packets disappear (avoid affecting new connection)

## Reliability

- **Sequence Number**
	Ensures in-order delivery
	Detects lost or duplicate segments

- **ACK + Acknowledgment Number (ack)**
	Split the TCP segment and a pseudo header (a virtual structure of key fields) into groups, compute a cumulative checksum, and verify integrity across all groups
	Indicates the next expected byte
	**Cumulative ACK**: acknowledges all data up to `ack - 1`

- **Checksum**
	Detects data corruption
	Corrupted segments are discarded

- **Retransmission**
	Lost data is resent

## Retransmission

- **Timeout Retransmission (<span class="abbr" data-title="Retransmission Timeout">RTO</span>)**
	Triggered by no ACK is received within a timeout

- **Fast Retransmit**
	Triggered by **3 duplicate ACKs** (with the same acknowledgment number)
	Retransmit immediately without waiting for timeout

## Flow Control

**Problem:** sender is faster than receiver

**Solution:** receiver controls sender rate
- Sliding Window
	Receiver advertises `rwnd` (receive window) in ACK

## Congestion Control

**Problem:** network is overloaded

**Sender-side variables:**

- `cwnd` (congestion window)
- `ssthresh` (slow start threshold)

Sending window: `min(rwnd, cwnd)`

**Mechanisms:**

1. Slow Start
	Start with `cwnd = 1 MSS`
	Exponential growth (`cwnd *= 2` per <span class="abbr" data-title="Round Trip Time">RTT</span>)
	Exit when:
	- `cwnd >= ssthresh`
		-> enter <u>Congestion Avoidance</u>
	- Or  packet loss detected
		- via timeout (indicating the network is unavailable)
			-> enter <u>Slow Start</u>
		- Or via duplicate ACKs (indicating the network is not fully congested)
			-> enter <u>Fast Recovery</u>

2. Congestion Avoidance
	Start when `cwnd >= ssthresh`
	Linear growth (`cwnd += 1 MSS` per RTT)

3. Fast Retransmit
	Triggered by duplicate ACKs

4. Fast Recovery
	After packet loss via duplicate ACKs:
	- `ssthresh = cwnd / 2`
	- `cwnd = ssthresh` (instead of resetting to 1 <span class="abbr" data-title="Maximum Segment Size">MSS</span>)  
		-> enter <u>Congestion Avoidance</u>