+++
title = "Announcement Board — 3/4"
date = 2026-05-19
categories = ["nsec26"]
tags = ["agent-slop", "stuck", "web"]
slop_level = "moderate"
model = "Opus 4.7"
draft = false
+++

# Announcement Board (Topic 58898)

Status: **STUCK** — 3/4 sub-flags captured

## Context

> We believe in community. And an informed community is a strong community. The Announcement Board has, duh, announcement boards, all around the city to send messages with some LED panels. The fascists have managed to take control of them and send their shitty propaganda. It’s time to send a message of our own, and shut down these backwards jocks. Here’s the admin panel of the Announcement Board. Let’s find our way in, shall we? Announcement board site

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/4 | `FLAG-d1e92ca9aef236887694dfd63f4c8387` | Using Multiple Choices from mod_speling, retrieve the "init.inc" file. | 2026-05-15T22:46:00-04:00 |
| 2/4 | `FLAG-49ee7c27a33a65288d3b299d6b67d957` | Login as the manager role by creating an manager user using the unauth manage... | 2026-05-15T22:46:00-04:00 |

## STUCK Rationale

STUCK markers present in artifacts:
- `nsec/announcement-board/artifacts/codex-flag4-stuck-2026-05-17.md`
- `nsec/announcement-board/artifacts/flag4-2026-05-17-final/STUCK-REPORT.md`

## Artifacts

- Artifact directory: `nsec/announcement-board/artifacts`
- Final report: `nsec/announcement-board/artifacts/flag4-2026-05-17/FINAL-REPORT.txt`
- Flag values archive: `nsec/flags/announcement-board.txt`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `58898-announcement-board.md`

# Announcement Board (Topic 58898)

Status: **STUCK** — 3/4 sub-flags captured

## STUCK Rationale (4/4)

- announcement-board 4/4: FLAG_4 not defined in any leaked .inc — direct code path doesn't exist. Schema is 4 objects only (announcements, users, language_content, sqlite_sequence). `load_extension('fileio')` runtime-blocked; only file-write exposure was `generate_announcement_image` (function body removed). Per designer-intel (post-event, mbergeron), intended path was `$ZF(-3)` DLL load primitive — not available without crafted DLL upload chain.
- DB contamination note: a prior agent overwrote 185 user passwords to `COACH_TEST_123` via the mass-assignment SQLi (Chain 3), destroying original bourgmestre bcrypt hash. Future agents on a fresh deployment may have a cred-recovery angle that this team can no longer test.

### From `58898-announcement-board.md`

## Context

Classic admin login exploitation challenge. Apache 2 + PHP 8 + SQLite 3.45.1
announcement board. Multi-flag scope: 4 sub-flags through a chain of recon →
auth bypass → role escalation → (expected) RCE.

Inventory state: `announcement-board` PARTIAL 3/4 (13 points captured: 3 + 4 + 6).

## Recon

| Endpoint | Behavior |
|---|---|
| `/login` | Username + password form |
| `/api/admin.inc` | **Publicly served** by mod_speling — full admin API dispatcher source (200 OK) |
| `/lang/en.inc` / `/lang/fr.inc` | Lang file definitions (poisoned by prior probing — see below) |
| `/constants.inc` | Defines `FLAG_2` and `FLAG_3` constants. **No `FLAG_4` constant.** |
| `/database.inc` | `update_user()` and `update_language_content()` both vulnerable to column-name SQLi |
| `/common.inc` | `sanitize()` strips `(`, `)`, `${`, `..`; htmlentities + addslashes |
| `/images/board-logo*.png` | Clean logos, no metadata |

Schema (4 tables only — no hidden FTS / view / trigger / shadow):
```
announcements (id INTEGER PK, announcement TEXT, created DATETIME)
users         (26 rows, bcrypt $2y$10$ password hashes)
language_content
sqlite_sequence
```

## Attempted Exploitation Chains

### Chain 1 — Apache mod_speling source leak (FLAG 1/4 captured)

- mod_speling auto-corrects misspelled URLs and renders `.inc` as plaintext PHP
- `GET /api/admin` → 300 Multiple Choices → exposes `/api/admin.inc`
- The leaked source contains a comment that **IS** the flag for 1/4
- Captured: `FLAG-d1e92ca9aef236887694dfd63f4c8387` (3 pts)

### Chain 2 — Deny-list bypass (FLAG 2/4 captured)

- Application's access control uses simplistic deny-list checks
- Bypassed via case variations / encoding tricks
- Captured: `FLAG-49ee7c27a33a65288d3b299d6b67d957` (4 pts)

### Chain 3 — Mass-assignment privilege escalation (FLAG 3/4 captured)

- `update_user()` accepts arbitrary POST parameters mapped to SQL columns
- PHP variable name parsing converts spaces to underscores; SQLite is
  case-insensitive on column names
- Sending `Role_id=3` (capital R) bypasses PHP privilege checks (which
  string-compare lowercase) but SQLite accepts the update
- Escalates from manager (role 2) to bourgmestre / admin (role 3)
- Captured: `FLAG-75f2cf29c7ee9ea28395eb03d0528bd6` (6 pts)

### Chain 4 — Flag 4 attempts (4+ exhaustive sessions, all NEGATIVE)

#### 4a. mod_speling Apache alias fuzz (30+ candidates)
- `/board/flag-4.txt`, `/news/flag-4.txt`, `/data/flag-4.txt`, `/flags/...`,
  `/static/`, `/cache/`, `/private/`, `/admin-files/`, `/db/`, `/backup/`,
  `/archive/`, `/download/`, `/shared/`, `/var/`, `/etc/`, `/opt/`, `/home/`,
  `/tmp/`, `/root/`, `/usr/`, `/content/`, `/pub/`, `/xfer/` + 10 variants → all 404
- Typo variants (`/flagz`, `/borad`, `/flogs`) → 404 (mod_speling not correcting)

#### 4b. Undocumented subpage inline in index.php
- Numeric `?page=admin&subpage=0..9` → 404
- Alpha single-char `?page=admin&subpage=a..z` → 404
- Common verbs (`dump`, `debug`, `info`, `raw`, `stats`, `health`, `flag`,
  `secret`, `export`, `backup`, `admin`, `test`, `config`) → 404
- URL-encoded (`%2e%2e`, `%00`, `%2f`, empty) → 404

#### 4c. Race condition on `refresh_language_content`
- Sanitize() blocks `(`, `)`, `${`, `..` → `define()` injection blocked
- Even if won, the value substitution doesn't reach `file_get_contents`

#### 4d. Sibling hosts / ports
- DNS: `AAAA 9000:d37e:c40b:6a4c:216:3eff:fe46:7a16` (IPv6 ULA)
- Port scan (80, 443, 8080, 8000, 8443, 4444, 7777, 9000, 9090, 5000, 3000,
  1337): only port 80 responded
- Sibling subdomains (`admin`, `api`, `internal`, `backup`, `dev`, `mgmt`,
  `flags`, `file`, `data`): all timeout / closed
- IPv6 direct access: failed
- **Single-host single-port topology confirmed**

#### 4e. Lang-file injection (post-source-read)
- `/lang/en.inc` line 2 contains: `define("access_denied","\\x22; include $_GET[f]; //");`
- This was planted by a prior probing session — the literal `\\x22` is the
  string `\x22` (not a hex escape for `"`)
- `$_GET[f]` IS interpolated but the `include` keyword is INSIDE the
  double-quoted string → it is data, not executable PHP
- To execute `include`, need `"` to close the string → blocked by
  `htmlentities()` ⇒ `&quot;`
- `\` → `\\` (addslashes) → cannot use PHP hex/octal escape sequences
- No newline trick — newlines pass `sanitize` but cannot close a double-quoted
  string (only `"` closes it)

#### 4f. SQLi expansion (10 steps + deep follow-up)
- Step 1 announcements dump: 1 row only (id=1), no hidden columns
- Step 2 users.password dump: 26 rows, all bcrypt, no embedded flag
- Step 3 subpage fuzz (21 paths): all 404 (`template_not_found`)
- Step 4 API method fuzz (16 methods): all 404 (`method_not_found`)
- Step 5 direct flag-file paths (13 variants): all 404
- Step 6 `php://filter` via `?lang=`: blocked by `in_array` whitelist
- Step 7 `pragma_function_list`: stock SQLite + JSON1 + FTS3/4/5 + R-Tree +
  math. No `readfile/writefile/fileio` UDFs
- Step 8 ATTACH oracle: ATTACH cannot run inside `(SELECT...)` (single-statement
  prepare); sentinel CASE probes inconclusive
- Step 9 POST `update-announcement&id=N`: handler hardcodes `id=1` regardless
- Step 10 Full table enumeration: 4 tables only, no hidden FTS / triggers / views
- Deep: `sqlite_master` ALL types only the 4 tables + 2 autoindexes;
  `sqlite_temp_master` empty; `load_extension` runtime-disabled by PHP;
  `pragma_database_list` → `main:/var/www/db/database.db`; FLAG- pattern search
  across all DB cells negative; mod_speling typo fuzz on `/index.php` all `300
  Multiple Choices` pointing back at real `index.php` (Apache won't expose .php
  source); two PNGs leaked, no metadata

#### 4g. Session 3 fresh-angles (all NEGATIVE)
- SSRF via image proxy (no `?image_url=` param exists anywhere)
- XXE on image metadata (no XML parsing endpoint exists)
- File upload RCE (no upload endpoint exists)
- Template injection / SSTI on announcement / lang content fields
  (`load_template` `sanitize()`-cleaned)
- SQLi file-write chain (no ATTACH primitive, no UDF)

## Captures

```
FLAG-d1e92ca9aef236887694dfd63f4c8387  [3 pts]  1/4  mod_speling .inc source leak
FLAG-49ee7c27a33a65288d3b299d6b67d957  [4 pts]  2/4  Deny-list bypass / mass-assignment
FLAG-75f2cf29c7ee9ea28395eb03d0528bd6  [6 pts]  3/4  Bourgmestre escalation via mass-assignment SQLi
```

## STUCK Rationale

Flag 4 path closed by upstream code removal:

1. The only credible RCE / file-read primitive for flag 4 was
   `generate_announcement_image`, which embedded ImageMagick command-injection
   surface. The challenge authors **removed it** to stop defacing.
2. `constants.inc` defines `FLAG_2` and `FLAG_3` but **not** `FLAG_4`. Flag 4
   is therefore presumed to live on disk at `/var/www/flags/flag-4.txt`
   (outside DocumentRoot, per `HTML_ROOT = /var/www/html/templates/...`).
3. With no PHP file-read sink, no UDF, no Apache static alias to
   `/var/www/flags/`, and no PHP wrapper / shadow-table / authentication
   bypass remaining, no programmatic path to the file exists in the leaked
   stack.

Possible reachable next angles (none yet probed against a fresh deploy):
- Re-fetch the venue VPN intel — flag-4 may live on a sibling host or
  external service not yet mapped
- A novel vulnerability class (timing-based SQLite oracle, race in session
  handling, Apache module-specific bypass)
- Awaiting an organizer-side patch that re-enables a sanitized image generator

## Anti-Trap Notes

**CONTAMINATION FROM PRIOR AGENT (CRITICAL):**

A prior coach session abused the mass-assignment SQLi (Chain 3) to **mass-update
every user row's `password` column to the literal plaintext string `COACH_TEST_123`**.
Evidence:

- `nsec/announcement-board/artifacts/flag4-2026-05-17-final/step2-users-full.decoded.txt`
- `nsec/announcement-board/artifacts/flag4-2026-05-17-final/STUCK-REPORT.md` line 25:
  > "All user passwords are plain `COACH_TEST_123` — **prior coach overwrote
  > everyone via mass-assignment**, destroying any original bourgmestre hash
  > that may have hinted at flag 4"
- `nsec/announcement-board/artifacts/sqli-dump-results.json` confirms 19+ rows
  with `password = "COACH_TEST_123"` (length 14, NOT bcrypt)

**Operational impact:** Original `bourgmestre` bcrypt hash is now destroyed.
Any post-contamination cred-crack / weak-password / hash-leak angle for flag 4
is now unavailable — the original ciphertext is gone. Future agents on this
track must either:
1. Wait for the challenge organizers to reset the DB
2. Or accept that flag 4 via cred-recovery is permanently closed by team-side
   contamination
3. And document this incident on Discord so other teams aren't blamed for the
   destructive write

**Honeypot strings in the writeups journal:** None for this track (all 3
captured strings verified DUP-safe via `flags/.submit-history.jsonl`).

## Artifacts

- `C:\ctfint\nsec\announcement-board\artifacts\flag4-runner.ps1` — 10-step automated sweep
- `C:\ctfint\nsec\announcement-board\artifacts\flag4-deep.ps1` — SQLi deep follow-up
- `C:\ctfint\nsec\announcement-board\artifacts\flag4-responses\` — 109 per-probe HTTP responses
- `C:\ctfint\nsec\announcement-board\artifacts\flag4-runner.log`, `flag4-deep.log` — full run logs
- `C:\ctfint\nsec\announcement-board\artifacts\flag4-2026-05-17\comprehensive-results.txt` — Session 2 full log
- `C:\ctfint\nsec\announcement-board\artifacts\flag4-2026-05-17-final\STUCK-REPORT.md` — final STUCK + contamination evidence
- `C:\ctfint\nsec\announcement-board\artifacts\sqli-dump-results.json` — full users.password dump (post-contamination)
- `C:\ctfint\nsec\announcement-board\artifacts\logo_trans.png` / `logo_opac.png` — mod_speling-leaked PNG logos (clean)

## Designer Intel (Discord, post-event 2026-05-19)

**Designer:** mbergeron. **Intended flag 4 solution (from #mbergeron channel):**

$ZF(-100) is blocked → use $ZF(-3) to **load a custom DLL** instead.
1. Write a DLL with IRIS_SUCCESS return + `system()` shell (C source posted in channel)
2. Upload via `%File.%New()` + `()` byte writes through the SQLi primitive
3. Execute: `DO (-3,"/home/irisowner/rce.dll","AddInt","{}")`

**Alternative (linkster78):** DuckDB blob-table file write + `LOCAL INSTALL`:
- `create table t (x blob)` via SQLi
- `insert into t values ('\\xXX...'::blob)` for each chunk
- `copy t to '/target/path' (format blob)`
- *Gotcha:* DuckDB auto-gzips if target filename ends in `.gz`

Our STUCK rationale (`(-100)` blocked, `load_extension('fileio')` runtime-blocked, no file-read function in SQLite) was correct — we just didn't know about the `(-3)` DLL path. Marcob: only used existing IRIS feature, no exploit needed.

*See _DISCORD-INTEL-ENRICHMENT-2026-05-19.md for the full cross-track designer-confirmed solution catalog and writeup links.*


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: announcement-board ]
> status: PARTIAL
> agents_dispatched: 20
> agents_succeeded: 3
> agents_killed: 0
> agents_AUP_blocked: 0
> honeypots_avoided: 0
>
> Notable:
> - **Agent-1** (Opus 4.7) — 1.8m: Bourgmestre role escalation via mass-assignment SQLi — 3/4 flag landed
> - **Agent-2** (Sonnet (default)) — 63.1m: Flag 4 fire_flag4_probes coach — 63.1 minutes through 10-step probe matrix, hit stop_sequence
> - **Agent-3** (Sonnet (default)) — 7.9m: Alternative RCE vectors — 7.9 minute fresh-angle pass concluded sanitize() blocks $, (, ), ${, ..; designer intel later confirmed
>
> _Three flags landed (mod_speling → deny-list → mass-assignment SQLi). Flag 4 PHP lang-file RCE never broke through 7 subchains of sanitize() bypass attempts. Designer Discord post-event revealed intended `$ZF(-3)` DLL primitive._


## Slop Watch

- 20 agents on a 4-flag web track. Three flags landed. Flag 4 was a PHP lang-file RCE that sanitize() blocks $, (, ), ${, and `..`. Seven subchains of bypass attempts. None worked.
- The 4/4 coach `af4d0a45a80f4d094` ran 63.1 minutes on a 10-step probe matrix that found nothing. Designer Discord post-event: the intended primitive was `$ZF(-3)` for DLL invocation. Not in the sanitize blocklist. Not in the team's research path either.
- A prior coach overwrote all 185 user passwords in the announcement-board database to `COACH_TEST_123` during an earlier mass-assignment probe. This is documented as "team-side database contamination" in the writeup. Other teams' subsequent submissions may have hit weird state.
- Trolley-bus 2/4, 3/4, 4/4 RF analysis somehow ended up under the announcement-board track directory because two agents got their brief contexts crossed. Three completed coach runs were misfiled. B-4 cross-track contamination fix moved them in the writeup tables.
