---
tags:
  - networking
  - cryptography
summary: TLS records and handshake
use_cases:
  - system_design
  - protocols
  - web
---

# HTTPS and TLS

## What is HTTPS

<span class="abbr" data-title="Hypertext Transfer Protocol Secure">HTTPS</span> is an ==application-layer== protocol that sends HTTP data securely over a <span class="abbr" data-title="Transport Layer Security">TLS</span> connection on top of TCP.

- HTTP over TLS
	Encrypted HTTP communication over a reliable transport
- Confidentiality
	Data is encrypted
- Integrity
	Detects any tampering during transmission
- Authentication
	Verifies the identity of the server (via certificates)
- Default Port
	443

## What is TLS

<span class="abbr" data-title="Transport Layer Security">TLS</span> is a security protocol that provides encryption, integrity, and authentication between two communicating applications.

- Runs above TCP
	Uses TCP for reliable delivery
- Encryption
	Uses symmetric encryption for data transfer
- Key exchange
	Uses asymmetric cryptography during handshake
- Authentication
	Uses certificates issued by a <span class="abbr" data-title="Certificate Authority">CA</span>
- Stateless application layer
	Does not manage application data format (e.g., HTTP)

Know about [[Digital Signature|Digital Signature]].

## TLS Record Structure

**Overview:**

A TLS record consists of:
```text
[TLS Header | Encrypted Data]
```

- Header:
	Content Type (handshake / application data)
	Version
	Length
- Data:
	Encrypted payload (e.g., HTTP request)

## TLS Handshake

**Goal:**

- Negotiate encryption algorithms
- Authenticate server
- Establish shared symmetric key

**Logical steps:**

---

1. **ClientHello**
Client -> Server
```text
client_random = Random()

Send client_random + supported_versions + cipher_suites
```

---

2. **ServerHello** + **Certificate**
Server -> Client
```text
server_random = Random()
Certificate = {
	subject,
	server_public_key,
	validity,
	signature_by_CA
}

Send server_random + chosen_version + chosen_cipher_suite + Certificate
```

- Both sides obtain `client_random` and `server_random`
- Client verifies server identity and gets the public key
- Attacker: everything is visible

---

3. **Key Exchange**
**RSA:**
Client -> Server
```text
premaster_secret = Random()

Send Encrypt(premaster_secret, server_public_key)
```

Server -> Client
```text
sig = Sign(all_handshake_messages_so_far, server_private_key)

Send sig
```

Both:
```text
shared_secret = Derive(premaster_secret, client_random, server_random)
```

**ECDHE:**
Client -> Server
```text
a = Random()

Send g^a
```

Server -> Client
```text
b = Random()  
sig = Sign(all_handshake_messages_so_far, server_private_key)

Send g^b + sig
```

Both:
```text
shared_secret = (g^a)^b = (g^b)^a
```

- Both sides obtain `shared_secret`
- Client confirms server owns the private key
- Attacker: cannot derive `shared_secret`, cannot decrypt future communication

---

4. **Key Derivation**
Both:
```text
session_key = DeriveKey(shared_secret, client_random, server_random)
```

- Both sides derive the same `session_key`
- Attacker: cannot compute it

---

5. **Finished**
Client <-> Server
```text
handshake_hash = Hash(all_handshake_messages)
verify_data = MAC(handshake_hash, session_key)

Send Encrypt(verify_data, session_key)
```

---

**Result:**

Secure TLS connection established