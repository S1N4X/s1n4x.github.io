+++
title = "Seed Vault - 6/6"
date = 2026-05-20
categories = ["nsec26"]
tags = ["solved", "web"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** - 6/6 sub-flags captured

## Context

> In a world where nature has been commercialized in outside areas, it is our duty to preserve biodiversity. One of the ways that we have found for preserving biodiversity is the SeedVault. Now, we have a serious problem… An outsider was able to infiltrate it and get admin access to the SeedVault and its systems. Who knows what they will do with this! That’s why we’ll have to be smart, and not trigger any alerts. Mother nature counts on us! Covert work has started, and we got their computer infected by an infostealer malware. We’ve got the stealer log and now it’s time to put our plan in operation. Can you investigate it and try to find a way to reclaim our admin access? Stealer log

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/6 | `FLAG-<not-recorded-locally>` | Stealerlog data stolen from the outsider in autofill | 2026-05-15T21:58:00-04:00 |
| 2/6 | `FLAG-<not-recorded-locally>` | Stealerlog data stolen from the outsider in desktop screenshot | 2026-05-15T22:23:00-04:00 |
| 3/6 | `FLAG-<not-recorded-locally>` | Stealerlog creds used to login to seedvault.ctf | 2026-05-15T22:42:00-04:00 |
| 4/6 | `FLAG-<not-recorded-locally>` | Elevated to staff. You figured out the puzzle. | 2026-05-16T10:30:00-04:00 |
| 5/6 | `FLAG-<not-recorded-locally>` | Elevated to admin. However, did you tamper well enough? Time will tell | 2026-05-16T11:20:00-04:00 |
| 6/6 | `FLAG-<not-recorded-locally>` | Perfectly tampered envelopped. | 2026-05-16T12:53:00-04:00 |

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59402-seed-vault.md`

## Seed Vault (Topic 59402)

Status: **SOLVED** - 6/6 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/6 | `FLAG-outs1der_st3aler_from_the_fu7ure` | Stealerlog data stolen from the outsider in autofill | 2026-05-15T21:58:00-04:00 |
| 2/6 | `FLAG-<not-recorded-locally>` | Stealerlog data stolen from the outsider in desktop screenshot | 2026-05-15T22:23:00-04:00 |
| 3/6 | `FLAG-<not-recorded-locally>` | Stealerlog creds used to login to seedvault.ctf | 2026-05-15T22:42:00-04:00 |
| 4/6 | `FLAG-<not-recorded-locally>` | Elevated to staff. You figured out the puzzle. | 2026-05-16T10:30:00-04:00 |
| 5/6 | `FLAG-<not-recorded-locally>` | Elevated to admin. However, did you tamper well enough? Time will tell | 2026-05-16T11:20:00-04:00 |
| 6/6 | `FLAG-<not-recorded-locally>` | Perfectly tampered envelopped. | 2026-05-16T12:53:00-04:00 |

### From `tamper-seedvault-5-6.md`

## Tamper / SeedVault - 5-6/6 progression (writeup)

**Date**: 2026-05-16 ~15:20 UTC-4
**Team**: 061
**Submissions this session**: 0 (out of 3 max)
**Status**: 5/6 and 6/6 BLOCKED on physical/on-venue action. Web side fully recon'd.

## Track state (as of askgod history)

| Flag | Status | Submitted at | Submitted by |
|------|--------|--------------|--------------|
| 1/6  | DONE   | 2026/05/15 21:58 | autofill `sv_notes` (`FLAG-outs1der_st3aler_from_the_fu7ure`) |
| 2/6  | DONE   | 2026/05/15 22:23 | desktop screenshot QR |
| 3/6  | DONE   | 2026/05/15 22:42 | logged-in catalog (`FLAG-l0gg3d-in-n0w-3l3v4t3-pr1vs`) |
| 4/6  | DONE   | 2026/05/16 10:30 | elevate.php (physical riddle + SunniKey) |
| 5/6  | **TODO** | - | "become an admin" |
| 6/6  | **TODO** | - | "ensure the outsider cannot elevate" |

## This session's deliverables

### Stealer log retrieved + extracted

- Discourse topic 59402 post body fetched via authenticated headed Chrome CDP (`fetch-topic-59402.ps1`)
- Stealer log URL: `https://dl.nsec/ASHFATHER_HWID-9F2C-DEADBEEF.zip` (4.33 MB)
- Saved to `nsec/tamper\stealer-log\ASHFATHER_HWID-9F2C-DEADBEEF.zip`
- Extracted to `nsec/tamper\stealer-log\extracted\` - full LummaC2 dump (browsers, autofill, comms, FileGrabber, Wallets, Screen.png 4.3MB)

### Credentials confirmed working

```
URL: http://seedvault.ctf/login.php
Username: ashfather77
Password: soleil-vole-77!
```

Source: `extracted/Passwords.txt` entry #1 + `extracted/ImportantAutofills.txt`. Verified live: POST returns 302 redirect with a fresh `SVSESSID` cookie; subsequent GET of `/` returns 40164-byte authenticated page including the keeper-view flag `FLAG-l0gg3d-in-n0w-3l3v4t3-pr1vs` (already submitted for 3/6).

### Web-side recon (post-login as outsider)

Authenticated home (`home-authed.html`) shows:
- `[data-admin-menu]` populated with **only**: `<a href="elevate.php">Admin</a>` + Logout
- No staff/admin endpoints exposed for the outsider role
- Catalog visible - uniquely: `SV-07-FL3` "FLAG-l0gg3d-in-n0w-3l3v4t3-pr1vs" (keeper-view bonus)

Probed endpoints post-auth (all 404 or 0-byte):
```
/api.php, /api/elevate, /api/admin, /api/keeper
/letter.php, /envelope.php, /mail.php, /sunnikey.php
/postman.php, /seal.php, /proof.php, /code.php
/admin/elevate.php, /keeper.php, /keepers/
```

Only `/elevate.php` is the gating endpoint. It returns:
```
"Elevation refused. One of your answers is wrong."
```
on POST with arbitrary `riddle=&token=`. Form requires both:
- `riddle` - "What is the decoded name of the seed in the 3rd jar of the 2nd shelve?"
- `token` - "SunniKey · large hex code" (~6 char monospace)

Tested trivial bypass params (role=admin, level=admin, is_admin=1, promote=1, admin=true, type=admin, user_type=admin, bypass=1) - all return identical 5250-byte refusal. No type-juggling, no IDOR, no obvious server-side flag.

## Why 5/6 and 6/6 are physical-only

Discourse topic 59402 post #5 (revealed after 4/6 elevation) is explicit:

> "the **enveloppe** containing the access code is going to the outsider. However the postman is with us so we can intercept the enveloppe at that moment. Just go to him and ask him about 'the Seed Vault letter' and I think he will understand what you mean.
>
> Now, one thing that is really important: the enveloppe **must reach the outsider** so simply stealing it is not enough, you will need to tamper with it and make sure the outsider receives the tampered version without any doubts. [...] The outsider **must not** be able to elevate with his enveloppe.
>
> We have a table that have tools to open the enveloppe without raising suspicion. Go downstairs you will find it.
>
> Put back the enveloppe in circulation by putting it in the mail box of the post office."

The path for 5/6 + 6/6:
1. Find the postman at the NSEC venue
2. Ask him about "the Seed Vault letter"
3. Take the envelope downstairs to the tampering table (tools provided)
4. Open it without breaking the seal
5. Extract the admin access code (this is the 5/6 flag input or the 5/6 flag itself)
6. **Tamper** the content so a tampered code is in the envelope
7. Re-seal so the outsider doesn't notice
8. Drop it in the post office mailbox to keep the cover

**Net result**: the team gets admin access; outsider doesn't.

This is fully on-venue, with no remote attack surface. The web admin endpoint required for 5/6 will likely accept a second token from inside the envelope (similar to the SunniKey shape).

## Hypothesis about the riddle (for next agent)

The elevate.php riddle "decoded name of the seed in the 3rd jar of the 2nd shelve" - TABLETTE II · JAR 3 in the authenticated catalog is:
- Common: **Anatolia Drylands Emmer**
- Latin: **Triticum dicoccum**
- Keeper: Ferman Yıldırım
- SID: SV-07-0701

The word "**decoded**" suggests an obfuscation step. Possible interpretations:
- The seed's common name decoded from latin (Triticum dicoccum → Emmer wheat?)
- A QR/glyph encoded in the SVG of the specimen card (could embed text in path data)
- The codex agent and 4/6 solver presumably already established this; the 4/6 already submitted, so the working answer is known to someone on-team.

## Recommendation

**5/6 and 6/6 are unsolvable remotely.** Operator must go to the venue:
1. Locate postman
2. Ask for "the Seed Vault letter"
3. Use the downstairs tampering table
4. Submit the admin code (extracted from envelope) via elevate.php or another admin endpoint exposed only to a higher role
5. Put modified envelope back in mailbox

If operator returns with envelope contents (admin code + any new web URLs), this can be finished in 5 minutes.

## Files produced this session

- `nsec/fetch-topic-59402.ps1` - CDP fetcher for Discourse topic JSON
- `nsec/tamper\topic-59402.json` - full topic 59402 content (5 posts, 18.8 KB)
- `nsec/tamper\stealer-log\ASHFATHER_HWID-9F2C-DEADBEEF.zip` - original zip (4.33 MB)
- `nsec/tamper\stealer-log\extracted\` - full extraction tree (Passwords, Autofills, browsers, FileGrabber, etc.)
- `nsec/tamper\home-authed.html` - authenticated catalog snapshot (40 KB)
- `nsec/tamper\elevate-anon-look.html` - elevate.php form snapshot
- `nsec/tamper\elevate-bad-attempt.html` - elevate.php failure response (with `riddle=test&token=000000`)
- `nsec/tamper\login-result.html`, `home-codex-session.html` - auxiliary recon

## Submission log

**0 submissions used this session.** No flag available to submit. Continuing without burning rate-limit budget.


### From `tamper-seedvault.md`

## Tamper / SeedVault 4-6/6 - Recon Notes (partial)

**Status**: 1/6, 2/6, 3/6 already submitted (per askgod history breadcrumb).
**Blocker**: 4-6/6 require authenticated session as the "outsider" (admin).
**This agent did NOT submit any flags** - no creds locally, strict no-brute.

## Challenge narrative (from Discourse 59402 / "Seed Vault")

> "An **outsider** was able to infiltrate it and get **admin access** to the SeedVault and its systems. [...] we got their computer infected by an **infostealer malware**. We've got the stealer log and now it's time to put our plan in operation. Can you investigate it and try to find a way to **reclaim our admin access**?"

So:
- 1/6 = mine stealer log autofill → find creds
- 2/6 = mine stealer log desktop screenshot → second piece of intel
- 3/6 = **login to seedvault.ctf as the outsider** (already an admin!)
- 4-6/6 = use that admin session to "take control of the vault" (3 admin-only actions)

## Live host recon (2026-05-16 ~14:45 EDT)

`http://seedvault.ctf/` - Apache/2.4.58 (Ubuntu), PHP. HTTPS NOT served (port 443 closed).

### Endpoints confirmed

| Path | Status | Notes |
|------|--------|-------|
| `/` | 200 (38204) | Public home, index.php rewrite |
| `/index.php` | 200 | Anonymous landing - same as `/` |
| `/login.php` | 200 | Keeper sign-in form, POSTs to `login.php` with `username` + `password` |
| `/assets/` | 200 | **Directory listing enabled** - only `style.css` |
| `/data/` | 200 | **Directory listing enabled** - `seeds.php` (8.1K), `site.php` (5.2K) |
| `/includes/` | 200 | **Directory listing enabled** - see below |
| `/admin`, `/admin.php`, `/dashboard.php`, `/api*`, `/users*`, `/profile.php`, `/keeper.php`, `/vault.php`, `/seeds.php`, `/settings.php` | 404 | All blocked |
| `/register.php`, `/signup.php`, `/forgot.php`, `/reset*.php`, `/recover.php` | 404 | No self-registration / password reset |

### /includes/ files (all return 200/0-bytes when fetched standalone - they're `include`d)

- `auth.php` (3.8K) - login/auth helper
- `bonus_check.php` (431B) - **tiny file, very suspicious name**, likely a feature check on a bonus / referral / promo code path
- `footer.php` (1.8K)
- `header.php` (6.0K) - when fetched standalone returns 500 (uses globals)
- `mail_codes.php` (5.3K) - **mail-based code logic** - likely the "we don't email" disclaimer hides a way to email-link codes for keepers
- `seed_glyph.php` (11K) - SVG glyph generator
- `specimen.php` (3.4K) - seed-card renderer

### /data/ files

- `seeds.php` (8.1K) - seed catalog data
- `site.php` (5.2K) - site config (likely)

### Cookie / session

- `SVSESSID` - PHPSESSID-style, set on every request. No JWT, no signed cookie. Server-side session.
- No CSRF token visible on the login form.

### Login form behavior

- `POST /login.php` with `username=x&password=y` returns 200 + page with `<div class="error" role="alert">Invalid credentials.</div>` on failure.
- Array-juggling (`username[]=admin&password[]=x`) → **HTTP 500 Internal Server Error** + 0-byte body. Suggests the code uses scalar-typed comparison (could be `===`, `crypt`, or `hash_equals`). Not exploitable as type-juggling auth-bypass.
- Classic SQLi (`admin' OR '1'='1'-- `, `admin' OR 1=1#`) → "Invalid credentials." Either parameterized or filtered. **Not** exploitable via the simple union.

### What 4-6/6 likely is (hypotheses)

1. **In-app admin panel** behind the `admin-burger` menu (visible empty `[data-admin-menu]` in `header.php` - likely populated server-side when `session.role == admin`). Hidden links populate after login.
2. **A "take control" action** - possibly a "claim", "lock", "freeze vault", or "rotate mesh keys" admin endpoint.
3. **`bonus_check.php`** - possibly checks a bonus / one-time admin code that the outsider had (could be in stealer log too).
4. **`mail_codes.php`** - codes sent via email; the disclaimer "we don't email" may be ironic and a keeper-mail backdoor exists.
5. The 6/6 might involve the **physical "SunniKey & large hex code"** (per Pivot1x10 intel) - a hardware element at the venue.

## What the next agent needs

**Hard blocker**: the actual outsider credentials from the stealer log.

The stealer log file is referenced in the NSEC platform Discourse topic 59402 ("Seed Vault"), with a link labeled "Stealer log". This file is **not** on disk anywhere under `nsec/`. It must be downloaded from the NSEC platform (Discourse attachment / `dl.nsec` link) by the previous agent who did 1-3/6.

`dl.nsec` browse is 403 / 404 for `/seedvault/`, `/stealer/`, `/seed-vault/`. The exact filename is unknown.

**Recommended next steps** (for an agent with the stealer log creds or platform access):

1. Pull the stealer log from the platform challenge page.
2. Extract autofill creds for `seedvault.ctf` from the browser-data dump in the log.
3. Login with those exact creds.
4. Walk the admin menu (the `[data-admin-menu]` populates for admin role).
5. Probe `/includes/bonus_check.php?code=X` post-auth, `/includes/mail_codes.php?action=` post-auth - they're includes, but probably also have entry-point shims.
6. The "take control" flag likely needs editing `seeds.php` data or triggering an admin action.

## Files produced this session

- `nsec/tamper\recon\index-anon.html` - anon landing snapshot
- `nsec/tamper\recon\index-anon.headers` - response headers
- `nsec/tamper\recon\login-fresh.html` - login form snapshot
- `nsec/tamper\recon\data-listing.html` - `/data/` directory listing
- `nsec/tamper\recon\includes-listing.html` - `/includes/` directory listing  
- (8 stub files for each PHP include, all 0-byte / 500 - confirms they're include-only)

## Submission history this session

**None**. No flags submitted, no rate-limit triggered. 0 / 3 submits used.


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: seed-vault ]
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
