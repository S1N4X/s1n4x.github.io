+++
title = "Monsatan - Checkmate — 1/1"
date = 2026-05-20
categories = ["nsec26"]
tags = ["crypto", "solved", "web"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** — 1/1 sub-flags captured

## Context

> I’ve got a person of interest and I need you to get me some details. I have been doing my OSINT and I found a post somewhere about meeting this creep on a matchmaking website for chess. The weirdo has been using it to meet people, and it is time for us to bring his actions to light. But I’ll need details. I know nothing about chess. Hopefully you can figure things out here: http://checkmate.ctf/ I attached a picture of what he looks like: The creep Oh, just so you know, he kinda happens to be a regional representative for Monsatan. Killing a bird with two stones.

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/1 | `FLAG-{7b8a881648447cd70180a775e531e218}` | Flag found in the location field of the target's contact info. | 2026-05-16T10:38:00-04:00 |

## Artifacts

- Artifact directory: `nsec/monsatan-checkmate/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59983-monsatan-checkmate.md`

## Monsatan - Checkmate (Topic 59983)

Status: **SOLVED** — 1/1 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/1 | `FLAG-<not-recorded-locally>` | Flag found in the location field of the target's contact info. | 2026-05-16T10:38:00-04:00 |

### From `59983-monsatan-checkmate.md`

## Monsatan - Checkmate (Topic 59983)

Status: **SOLVED** — 1/1 sub-flag captured

**Status correction note:** Prior writeup version said BLOCKED (could not crack PBKDF2 hash). Subsequently solved 2026-05-16 10:38 EDT via `rockyou.txt` crack producing `<REDACTED:operational-detail>` — see `flags/.submit-history.jsonl` 2026-05-16T14:38:24 (OK) and askgod #36. Prior linter version also showed wrong flag value `FLAG-b74d95e89c2c63a830efcdcf30` (that's a hello-sunshine flag); the real value is `FLAG-{7b8a881648447cd70180a775e531e218}`.

## Context

> I've got a person of interest and I need you to get me some details. I have been doing my OSINT and I found a post somewhere about meeting this creep on a matchmaking website for chess. The weirdo has been using it to meet people, and it is time for us to bring his actions to light. But I'll need details. I know nothing about chess. [...] http://checkmate.ctf/

Target: `MrRook` (Monsatan regional rep) on the chess-themed Tinder-clone matchmaking platform.

## Recon

- URL: `http://checkmate.ctf/` — Chess-themed Tinder-clone
- Stack: SvelteKit SPA front-end, JSON REST API at `/api/*`
- "Creep" = `MrRook` (Monsatan regional rep)
- AI-generated picture `mrrook_pic.png` (320 KB; PNG `tEXt` metadata reveals prompt: bio + seed `131281670`)

### API map (extracted from `chunk-CUiJ26rg.js`)

| Method | Endpoint | Auth |
|--------|----------|------|
| POST | `/api/login` | no |
| POST | `/api/register` | no |
| GET | `/api/next` | yes (Bearer JWT) |
| POST | `/api/user/{u}/swipe` | yes |
| GET | `/api/matches` | yes |
| **GET** | **`/api/user/{u}`** | **yes — leaks `passwordPbkdf2Hash` + `passwordSalt`** |
| GET | `/api/user/{u}/contact` | yes + mutual match |
| GET | `/api/user/{u}/dm` | yes + mutual match |
| POST | `/api/user/{u}/dm` | yes + mutual match |

### Vulnerability — Hash Disclosure (IDOR / info-leak)

`GET /api/user/{username}` returns the **full password hash and salt** of any user, including MrRook:

```json
{
  "username": "MrRook",
  "passwordPbkdf2Hash": "9Me0oGCiNvZb9eO85D0JZivzgBk1reME7Dl0/txcklU=",
  "passwordSalt": "FCdNj2Ds4xltNHXpKhB3yA==",
  "bio": "Tired of boring opening lines? I'm a desperado that will do my best to bring us to the late game. Looking for my Caïssa!",
  "rating": 1200,
  "profilePicture": "..."
}
```

### Hash scheme (reverse-engineered)

`PBKDF2-HMAC-SHA512(pw, salt, iter=1623, dklen=32)`.

Verified by registering throwaway accounts with known passwords, reading back the hash via the same IDOR, and brute-forcing parameters:

```python
hashlib.pbkdf2_hmac("sha512", b"a", base64.b64decode("saHeqiKOUeNUGThu3+/EHg=="),
                    1623, dklen=32) == base64.b64decode("GySm75AX8ifiFpYs/SzuYWvWCpV7auozH6jLpUM4OR0=")  # ✓
```

## Exploitation

### Stage 1 — Crack MrRook's PBKDF2 hash

GPU-accelerated `hashcat -m 12100` (PBKDF2-HMAC-SHA512) against `rockyou.txt` recovered the password:

```
<REDACTED:operational-detail>
```

(Prior coach attempt with 22-proc Python CPU at ~4 kh/s was infeasible. GPU made this minutes.)

### Stage 2 — Authenticate as MrRook + match path

1. `POST /api/login` with `MrRook` / `<REDACTED:operational-detail>` → received Bearer JWT
2. Swiped right on a target user from `/api/next` (the `liked` flag identifies who MrRook would mutually match)
3. After mutual match: `GET /api/user/{match}/contact` returned the matched user's contact info — flag embedded in the `location` field

### Flag location

The flag was found in the **location** field of the target's contact info (revealed only via authenticated mutual-match access).

## Captures

### Flag 1/1 — askgod #36 / 7 pts

- **Value:** `FLAG-{7b8a881648447cd70180a775e531e218}`
- **Method:** IDOR `/api/user/MrRook` → PBKDF2-SHA512 → rockyou crack `<REDACTED:operational-detail>` → swipe + match
- **Timestamp:** 2026-05-16 10:38 EDT
- **Wrapper attempt:** `flags/.submit-history.jsonl` 2026-05-16T14:38:24 → OK

## Anti-Trap Notes

The bio's `Caïssa!` is a strong red-herring: ~5000 themed variants (UTF-8/Latin-1/CP1252/NFC/NFD/mojibake) were tested and none matched. The real password (`<REDACTED:operational-detail>`) is a `rockyou.txt` entry — straightforward dictionary attack — but only **after** the wordlist scope expanded beyond theme-specific guessing.

## Artifacts

- `nsec/monsatan-checkmate/artifacts/` — Original artifacts
- `nsec/monsatan-checkmate/artifacts/mrrook_pic.png` — Target image (PNG tEXt metadata)
- `nsec/monsatan-checkmate/artifacts/chunk-CUiJ26rg.js` — Frontend API definition
- `nsec/monsatan-checkmate/artifacts/step_*_response.json` — Per-stage API response dumps
- `nsec/flags/.submit-history.jsonl` row at 2026-05-16T14:38:24 — OK submission

## Lessons

- Default to GPU hashcat for PBKDF2 / bcrypt / scrypt — CPU is uneconomic
- Don't theme-restrict wordlists too early; rockyou is the baseline
- IDOR returning hash+salt is a complete-credential-theft primitive


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: monsatan-checkmate ]
> status: SOLVED
> agents_dispatched: 1
> agents_succeeded: 1
> agents_killed: 0
> agents_AUP_blocked: 0
> honeypots_avoided: 0
>
> Notable:
> - **Agent-1** (Opus 4.7) — 15.6m: Auth bypass + hash params discovery → rockyou.txt crack → location-field flag retrieval (askgod #36, 7 pts)
>
> _One agent. Web auth/admin bypass + hash crack of `<REDACTED:operational-detail>` (PBKDF2-HMAC-SHA512 iter=1623) in 2h09m on CPU. GPU would have been minutes._
