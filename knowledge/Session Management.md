---
tags:
  - networking
  - session
summary: session + cookie vs. JWT + header
use_cases:
  - web
  - api
  - microservices
---

# Session Management

## Mechanisms

- Session-based (Session + Cookie)
- Token-based (JWT + Header)

## Comparison: Session + Cookie vs. JWT + Header

**Content**
- Session: `session_id` (random identifier)
- JWT: `token` (user info signed with server secret/private key)

**Usage**
- Session: lookup user data on server by `session_id`
- JWT: verify signature + parse payload (no lookup needed)

**Storage Location**
- Session
	Client: Cookie (browser-managed)
	Transport: `Cookie: session_id=abc123` (auto-sent by browser)
	Server: session store (memory / DB)
- JWT
	Client: JS storage (memory / localStorage / etc.)
	Transport: `Authorization: Bearer <token>` (not auto-sent by browser)
	Server: None

**Security**
- Session
	- Pros
		Can revoke immediately (delete session)
		Safer with HttpOnly cookie (prevents JS access)
	- Cons
		Vulnerable to <span class="abbr" data-title="Cross-Site Request Forgery">CSRF</span> (browser auto-sends cookies)
		Requires server storage
- JWT
	- Pros
		Stateless (no server storage)
		No CSRF (headers are not auto-sent)
		Works across Web, Mobile, Services
	- Cons
		Hard to revoke (valid until expiration)
		Vulnerable to <span class="abbr" data-title="Cross-Site Scripting">XSS</span> if stored in localStorage (JS can access token)

## Attack Surface

| Attack      | Session          | JWT                 |
| ----------- | ---------------- | ------------------- |
| XSS         | low (HttpOnly)   | high (JS readable)  |
| CSRF        | high (auto send) | low (manual header) |
| Token Theft | medium           | high impact         |

