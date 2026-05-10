---
tags:
  - networking
  - crypto
  - ecc
  - signature
summary: definition, sign/verify model, RSA/ECDSA/EdDSA algorithms
use_cases:
  - authentication
  - integrity
  - certificates
  - tls
  - https
---

# Digital Signature

## Definition

A digital signature is a cryptographic mechanism that proves:
- authenticity (who signed)
- integrity (message not modified)

## Model

**Sender:**
```text
h = Hash(message)
sig = Sign(h, algorithm, private_key)
```

Transmit:
```text
message + sig
```

**Receiver:**

For illustrative case:
```text
h = Hash(message)  
h' = Recover(sig, algorithm, public_key)
valid = (h == h')
```

Common:
```text
h = Hash(message)
valid = Verify(sig, h, algorithm, public_key)
```

## Common Signature Algorithms

### RSA

- Parameters:
	- `n` (modulus):
		`n = p * q` // product of two large primes
	- Private key:
		`(d, n)`
	- Public key:
		`(e, n)`
- Key relation:
	$$d \cdot e \equiv 1 \pmod{\varphi(n)}$$
- Idea:
	- Sign:
		`sig = h^d mod n`
	- Verify:
		`check (sig^e mod n) == h`

### ECDSA

- Parameters:
	- Curve / group:
		`E(F_p)` // elliptic curve over finite field
	- Base point:
		`G` // fixed generator point on the curve
	- Order:
		`n` // smallest positive integer such that $n \cdot G = O$
	- Private key:
		`d` // secret scalar such that $d \in [1, n-1]$
	- Public key:
		`Q = d * G`
- Idea:
	- Sign:
		```text
		k = random in [1, n-1] // fresh nonce per signature
		P = k * G // random curve point
		r = x(P) mod n // take x-coordinate
		s = k^{-1}(h + d * r) mod n
		sig = (r, s)
		```
	- Verify:
		```text
		w = s^{-1} mod n
		u1 = h * w mod n
		u2 = r * w mod n
		P = u1 * G + u2 * Q
		check x(P) mod n == r
		```

### EdDSA (Ed25519)

- Parameters:
	- Curve / group:
		`Ed25519` // twisted Edwards curve over finite field
	- Base point:
		`B` // fixed generator point on the curve
	- Order:
		`n` // smallest positive integer such that $n \cdot B = O$
	- Private key:
		`d` // secret scalar
	- Public key:
		`A = d * B`
- Idea:
	- Sign:
		```text
		r = Hash(prefix, message) mod n // deterministic nonce
		R = r * B // nonce point
		k = Hash(R, A, message) mod n // challenge
		s = r + k * d mod n
		sig = (R, s)
		```
	- Verify:
		```text
		k = Hash(R, A, message) mod n
		check s * B == R + k * A
		```