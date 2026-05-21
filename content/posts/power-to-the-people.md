+++
title = "Power to the People Election Platform - 2/2"
date = 2026-05-20
categories = ["nsec26"]
tags = ["solved", "web"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** - 2/2 sub-flags captured

## Context

> Guardian, what I am about to share is extremely confidential. The new platform for the upcoming election will use an open-source passwordless authentication to validate citizens’ identity. However, we suspect the identification validation system is flawed. I want you to prove it, and for it, I am brave enough to let you use my own account, [teammate]@powertothepeople.ctf . Please investigate at once http://powertothepeople.ctf

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/2 | `FLAG-<not-recorded-locally>` | Flag after enabling the debug headers. | 2026-05-15T22:32:00-04:00 |
| 2/2 | `FLAG-<not-recorded-locally>` | Flag after bypassing the authentication system. | 2026-05-15T22:32:00-04:00 |

## Artifacts

- Artifact directory: `nsec/powertothepeople/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59366-power-to-the-people.md`

## Power to the People Election Platform (Topic 59366)

Status: **SOLVED** - 2/2 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/2 | `FLAG-{100abf499f63a4e3e9c5e57c4f27bded}` | Flag after enabling the debug headers. | 2026-05-15T22:32:00-04:00 |
| 2/2 | `FLAG-{eb3b1e6b31c0cc768ad46e2163912672}` | Flag after bypassing the authentication system. | 2026-05-15T22:32:00-04:00 |

## Artifacts

- Artifact directory: `nsec/powertothepeople/artifacts`
- Flag values archive: `nsec/flags/powertothepeople.txt`

### From `59366-power-to-the-people.md`

## Power to the People (Challenge 59366)

## Context

WebAuthn / Passkey authentication challenge. The target is a passwordless-login web application (`powertothepeople.ctf`) that uses WebAuthn credentials but has critical flaws in how it identifies users and validates credential ownership.

Two flags:
1. Debug-headers information disclosure
2. User-handle confusion → authentication bypass

## Recon

- Target: `http://powertothepeople.ctf/`
- Authentication: WebAuthn / passkey only
- Target account for hijack: `[teammate]@powertothepeople.ctf`
- Endpoints:
  - `POST /api/webauthn/register/begin` and `/register/finish`
  - `POST /api/webauthn/login/begin` and `/login/finish`
- WebAuthn assertion includes user-handle field - server reads it without verifying ownership against the assertion signature

## Exploitation

### Flag 1/2 - Debug headers (askgod #63 / 1 pt)

The server returns verbose WebAuthn debug headers on certain error paths, leaking internal state (challenge nonces, internal user IDs, credential format hints). Triggering the right error condition surfaces a debug header containing the flag string directly.

### Flag 2/2 - Passkey validation flaw (askgod #64 / 10 pts)

The authentication-finish endpoint extracts `user_id` from the client-supplied WebAuthn assertion data **without** validating that the signing credential actually belongs to the claimed user. Steps:

1. Attacker registers a passkey to their own account
2. Attacker logs in, intercepting the assertion
3. Attacker tampers with the `user_id` field, replacing it with the victim's (`[teammate]@powertothepeople.ctf`)
4. Attacker submits the assertion - the signature is valid (it's the attacker's key) but the server reads `user_id = [teammate]` and grants access to [teammate]'s session

The cryptographic binding between credential and user identity is missing on the server side.

## Captures

### Flag 1/2 - Debug headers (askgod #63 / 1 pt)

- **askgod entry:** powertothepeople 1/2
- **Timestamp:** 2026-05-15 22:32 EDT
- **Value:** `FLAG-{100abf499f63a4e3e9c5e57c4f27bded}`
- **Method:** WebAuthn debug headers leak

### Flag 2/2 - Auth bypass (askgod #64 / 10 pts)

- **askgod entry:** powertothepeople 2/2
- **Timestamp:** 2026-05-15 22:32 EDT
- **Value:** `FLAG-{eb3b1e6b31c0cc768ad46e2163912672}`
- **Method:** Passkey/user-handle confusion - sign with attacker key, submit with victim user_id

## Anti-Trap Notes

WebAuthn is **designed** to be phishing-resistant via cryptographic binding between credential and authenticated user. This implementation breaks the binding server-side - a real production-grade vulnerability pattern.

## Artifacts

- `nsec/powertothepeople/exploit.py` - WebAuthn assertion tampering script
- `nsec/powertothepeople/responses/` - Debug-header response captures


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: powertothepeople ]
> status: rolled into the _fleet swarm (no per-track ledger)
> agents_dispatched: see /swarm/ for the fleet-wide rollup
> agents_succeeded: -
> agents_killed: 0
> agents_AUP_blocked: 0
> honeypots_avoided: 0
>
> _This track absorbed effort during the initial 122-agent fleet
> fan-out wave but never got its own per-track ledger. The /swarm/
> retrospective has the cross-track distribution._
