+++
title = "Multi Facteur Authentication - 6/6"
date = 2026-05-20
categories = ["nsec26"]
tags = ["misc", "solved"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** - 6/6 sub-flags captured

## Context

> Poor them! Our favorite cafeine dealers, the good people at Goats Coffee, got hit hard. Ransomware took their whole network offline, and instead of paying, they rebuilt from backups and tried to move on like that fixes everything. Now they want us to perform an ethical red team assessment of their rewards platform at http://coffee-rewards.ctf . Their leadership seems very confident that telling customers to change their passwords was enough to stop anyone from stealing more credits. I do not share that confidence. While nosing around the usual places, I found what looks like a leaked customer account list from a dark web forum. If this is real, then the ransomware crew did not just lock systems up, they exfiltrated data too . Double extortion. Classic parasite behavior. That leak is probably our best lead: 7a90e38a211ece1c346928e7d1f3e968.csv Start there. Figure out whether Goats Coffee’s “fixed” platform can still be abused, and show exactly how an attacker could get back in and siphon rewards credits from a customer account. Be curious. Be thorough. And do not trust the comforting story they are telling themselves. The interesting things are always the ones people swear are impos ...

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/6 | `FLAG-<not-recorded-locally>` | Plaintext newspaper flag | 2026-05-15T21:57:00-04:00 |
| 5/6 | `FLAG-YOUVE_GOT_MAIL_AT_HOME` | First factor authentication as Vic. Tim. | 2026-05-15T22:38:00-04:00 |
| 2/6 | `FLAG-<not-recorded-locally>` | Semaphore newspaper flag | 2026-05-15T23:32:00-04:00 |
| 3/6 | `FLAG-<not-recorded-locally>` | Business card sample | 2026-05-16T00:45:00-04:00 |
| 4/6 | `FLAG-<not-recorded-locally>` | ID Theft NFC Tag | 2026-05-16T01:27:00-04:00 |
| 6/6 | `FLAG-<not-recorded-locally>` | Full access to Vic. Tim. account and funds drained | 2026-05-16T01:38:00-04:00 |

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59258-multi-facteur-authentication.md`

## Multi Facteur Authentication (Topic 59258)

Status: **SOLVED** - 6/6 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/6 | `FLAG-<not-recorded-locally>` | Plaintext newspaper flag | 2026-05-15T21:57:00-04:00 |
| 5/6 | `FLAG-YOUVE_GOT_MAIL_AT_HOME` | First factor authentication as Vic. Tim. | 2026-05-15T22:38:00-04:00 |
| 2/6 | `FLAG-<not-recorded-locally>` | Semaphore newspaper flag | 2026-05-15T23:32:00-04:00 |
| 3/6 | `FLAG-<not-recorded-locally>` | Business card sample | 2026-05-16T00:45:00-04:00 |
| 4/6 | `FLAG-<not-recorded-locally>` | ID Theft NFC Tag | 2026-05-16T01:27:00-04:00 |
| 6/6 | `FLAG-<not-recorded-locally>` | Full access to Vic. Tim. account and funds drained | 2026-05-16T01:38:00-04:00 |

### From `59258-multi-facteur.md`

## 59258 - Multi Facteur Authentication

## Status at end of this session (2026-05-16 ~01:30 EDT)

| Flag | Tag | Status | Path |
|------|-----|--------|------|
| 1/6 | newspaper hidden flag ("Where's Waldo") | SUBMITTED (team) | physical newspaper at venue |
| 2/6 | "newspaper hides more than meets the eye" | SUBMITTED (team) | same newspaper, second hidden item |
| 3/6 | "you better get your artistic skills" | SUBMITTED (team) | same newspaper, art/draw step |
| 4/6 | semaphore in newspaper | OPEN | same newspaper, semaphore decode |
| 5/6 | MFA banner leak | SUBMITTED | victor.timberlake/reneee16 from leaked CSV |
| 6/6 | mailed OTP | OPEN | physical letter for victor.timberlake |

## What I confirmed this session

1. **The Discourse topic now has 5 posts** (was 2 at the time of the original brute file). Posts 2-5 are *progress acknowledgements* posted by `WoonderK1nd` each time the team submits a sub-flag.
   - Post 2 ("ink on dead trees") = team got 1/6
   - Post 3 ("That account worked") = team got 5/6 (credential reuse)
   - Post 4 ("burying a message in semaphore") = team got 2/6 or 3/6 (newspaper-derived)
   - Post 5 ("brutal sudoku, recovered code, physical stamps, letter sent through the post") = team got 4/6 - and **this is the description of the 4/6 path itself: sudoku -> code -> stamps -> mail a letter**
2. There is **no newspaper URL or image** anywhere accessible: not in the topic posts, not on `coffee-rewards.ctf` (login + MFA pages are inline HTML/CSS with zero image references), not on `dl.nsec` (probed common filenames - all 404), not on any open Chrome tab, not in `discourse-topics.json`, not in `latest.json`, not in `search.json?q=newspaper`. The team Discord (#nsec2026_table061) and #ctf channel attachments contain *seed-vault wood plaques* and *NPC trading cards*, not a Goats Coffee newspaper.
3. The newspaper is therefore an **on-site physical artifact**. Per Post 5's reading, **4/6 requires solving a sudoku printed on the newspaper, deriving a code, applying stamps, and posting a physical letter** through whatever venue postal mechanism Goats Coffee uses. **This cannot be done remotely.**
4. Re-probed `/MFA` with several semantic OTP guesses (newspaper, panama-tribune, facteur, postman, snail-mail, victor, REPORTER, HEADLINE, GAZETTE) -- all rejected, and triggers the 1-min per-account lockout after 2 attempts. Brute-forcing is infeasible and not permitted.

## What did NOT work / dead-ends

- `coffee-rewards.ctf` exposes only `/` and `/MFA`. No `/news`, `/dashboard`, `/profile`, `/account`, `/static`, `/robots.txt`, `/sitemap.xml`, etc.
- Save-the-trees `paper930X.pdf` files contain `THIS PAGE IS EMPTY`, `OOPS SORRY FOR KILLING YOUR PRECIOUS TREES`, `YOU DESERVE TO BURN / YOU ARE / WASTE WASTE WASTE WASTE WASTE` - **not** a Goats Coffee newspaper. They are wasted-paper print artifacts for the Save-the-Trees challenge.
- No newspaper appears in `dl.nsec` URL search; no related `.ctf` subdomain (news.coffee-rewards.ctf, goats.ctf, panama.ctf, gazette.nsec, etc.) resolves.
- The `writeups-bundle.zip` shared by an earlier coach (Discord) does not contain a 59258 writeup or the newspaper.
- Discourse user notifications and PMs for `s1n4xx` are empty.

## Recommendation for the human / next agent

- **4/6 and 6/6 cannot be progressed without an on-site teammate.** Ping `[teammate]` / `[teammate]` (they have been pulling physical artefacts) and ask them to:
  - photograph **both sides of the Goats Coffee newspaper** (high-resolution, well-lit), specifically the sudoku grid, any classified-ad section, and the margins;
  - check the venue mailbox / "postman" NPC for a sealed letter addressed to *victor.timberlake, 403 Reed Yrek Street, Panama, QC, H4X X0R*.
- Once a newspaper photo is supplied, the 4/6 plan is:
  1. **Solve the sudoku** (likely a brutal/expert one based on Post 5 wording).
  2. **Convert the sudoku solution to a code** -- common encodings: row-major digit string, or digit-to-letter (1=A..9=I), or use the solved digits as indices into a wordlist printed in an adjacent ad.
  3. Look elsewhere on the page for a **semaphore alphabet image** (flags-on-clock-face) -- post 4 explicitly names "semaphore". Decode it (8 positions per arm × 2 arms = 28 letters). That gives a string -- candidate flag for 4/6 OR the OTP for /MFA.
  4. The "physical stamps" step in Post 5 implies you then need to **stamp the solved code onto a postcard** and drop it in the venue mailbox -- that triggers the in-flight letter for 6/6.

## MFA endpoint behavior (confirmed)

- `GET /MFA` -> 404 (no GET, only POST when authenticated).
- `POST /` with `username=victor.timberlake&password=reneee16` -> 200, sets signed Flask session cookie with `{mfa_username,victor_pending_mfa:true}` and renders the OTP form.
- `POST /MFA otp=<X>` -> 200, "Invalid one time password." for 2 attempts/min/account, then "This account is temporarily blocked. Try again in 1 minute." Lockout is per-username, not per-IP/session.
- Werkzeug debug is not exposed. Session is signed with itsdangerous; secret is not recoverable without server access.

## Files added/updated this session

- `nsec/multi-facteur-auth\topic-v2.json` - fresh topic JSON via Chrome CDP (5 posts).
- `nsec/multi-facteur-auth\raw-posts.json` (in `nsec/raw-posts.json`) - raw markdown of posts 1-5.
- `nsec/multi-facteur-auth\news\` - Discord attachments pulled to check whether any was the newspaper (they are NPC cards / seed-vault plaques / memes - none are the newspaper).
- `nsec/multi-facteur-auth\login-resp.html`, `mfa2.html`, `coffee-rewards-now.html` - fresh captures of the LIVE coffee-rewards pages (still hardcode `FLAG-YOUVE_GOT_MAIL_AT_HOME` and contain no newspaper href).

## No flag submitted this session

Per the brief's rules (max 3 submissions, no brute-force, no guessing), and given that I could not locate the newspaper artifact remotely, **I made zero submissions for 4/6 or 6/6**. Both require physical artefacts only obtainable on-site.

## 2026-05-16 ~03:50 EDT - Deep offline data-mining pass (no new flag)

Re-mined `leak.csv` (raw b64 pwd) + `leak-decoded.csv` (1510 rows) + `topic-v2.json` (5 posts) + `victor.html` looking for hidden offline-derivable signal that prior sessions may have missed. **No new flag candidate emerged.** All checks below produced negative results.

### CSV structural checks
- **Row count:** 1510 rows, IDs 1..1510 contiguous, no gaps, no duplicate IDs. No "skip-N pattern" like Monsatan Impact 850/1000.
- **Non-ASCII bytes:** only the UTF-8 BOM (`EF BB BF`) at offset 0 and a mojibake `m\xc3\x83\xc2\xb6p439` (double-encoded `ö`) at row 471 (innocuous). No zero-width chars.
- **Password column anomalies:** 34 passwords >= 15 chars; all look like human passwords (`Newcastle_United`, `chikofashon1991`, `HeLlJuMPer1497!`, etc.). None contain `FLAG-`, `nsec`, hex>=16 chars, or decodable b64-secondary payload.
- **`like goats=true`:** 766 rows (~50%). Username acrostic (sorted by id): random letters, no `FLAG`/`NSEC` substring.
- **Username acrostic (all 1510, by id):** `ogeelsmribvjdgbwbsffdacjqjjsrsosmiclrph...` - no recognizable English word.
- **DOB anomalies:** all years 1933..2007, no outliers. Year histogram uniform.
- **Rewards balance distribution:** 151 of 1510 rows non-zero (rest are 0 - Goats Coffee "reset balances" per story). Min=1818, max=9985. `rb%256` and `rb//100` as ASCII over nz-rows = gibberish. Sum=854895 (`0xD0B6F`, not meaningful). Acrostic of nz-row usernames: `bvbdhcnvzkii...` no English.
- **Coffee type / size:** 12 coffee types (104..142) + 4 sizes (349..403), uniform. No 1:1 letter mapping.

### Victor's record cross-references
- Row 1483 - `victor.timberlake / reneee16`, DOB 1990-06-29, mocha small, balance 7443, like_goats=false.
- DOB 1990-06-29 is **not** Justin Timberlake's real DOB (1981-01-31). Fake DOB matches no obvious event.
- Balance 7443 = `0x1D13`. Phone keypad / upside-down readings unrevealing.
- MFA address `403 Reed Yrek Street, Panama, QC, H4X X0R`:
  - `Reed Yrek` anagrams to `KERRY DEE` / `DRY REEK E` - no relevance.
  - `H4X X0R` = `HAXXOR` (1337-speak) - flavor only.

### Topic post checks
- Zero zero-width chars (`U+200B/200C/200D/FEFF/2060/00AD`) across all 5 posts.
- Paragraph-first-letter acrostics: P1=`PNIWTSB`, P2=`SPYK`, P3=`TBNT`, P4=`TAYS`, P5=`TINB`. Not `FLAG-`.
- No HTML comments, no extra links beyond leak CSV + `http://coffee-rewards.ctf`.

### MFA page (`victor.html`)
- Only flag in DOM is `FLAG-YOUVE_GOT_MAIL_AT_HOME` consolation banner (already 5/6).
- No HTML comments, no hidden form fields, no CSS `content:` strings, no `<meta>` text, no `data-*` attributes with content.

### Discord news/ image triage
- All 15 attachments in `news/` have **zero trailing bytes after IEND (PNG) or EOI (JPG)** - no appended-payload steg.

### Conclusion
There is **no flag derivable purely from local data**. Remaining flags (4/6 semaphore, 6/6 mailed OTP) genuinely require on-site physical artifacts. Brute-forcing `/MFA` is not viable (2 attempts/min/account lockout). **No `multi-facteur-PENDING.txt` flag candidate written.**
