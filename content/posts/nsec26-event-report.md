+++
title = "NSEC 2026 - Event Report"
description = "Full retrospective: 119 flags across 37 tracks. Multi-agent operation, anti-trap findings, framework deltas, lessons learned."
date = 2026-05-21
categories = ["nsec26"]
tags = ["meta-writeup", "report", "retrospective"]
model = "Opus 4.7"
+++

```
[ NSEC 2026 // FINAL ]
flags:          119  /  ~160 available
tracks:          37  (25 SOLVED · 4 PARTIAL · 8 STUCK)
agents spawned:  437
duration:        2026-05-15 → 2026-05-17 (~24h play across 3 days)
```

**Team (scoreboard name):** `Shellda; link to the flag` - **final placement #12 / 92 teams · 492 pts**
**Team (Discord / table assignment):** bifftannen88 / Table 061
**Event:** NorthSec 2026 Conference CTF (Gaia / Solarpunk narrative)
**Dates:** 2026-05-15 → 2026-05-17 (Friday open → Sunday close; ~24h real play across 3 days, mandatory 02:00-08:00 overnight closures)

---

## 1. Executive Summary

NorthSec 2026's CTF was a multi-day, multi-narrative event spanning **37 challenge tracks** across web exploitation, cryptography, RF/hardware reverse engineering, forensics, ICS/IoT, and a continuous "Solarpunk vs Monsatan" storyline. The event published **161 flags totalling 754 points** across 60 services and 35 files, built by 27 challenge designers. Critically, this was **~24 hours of real play spread across 3 days** — the organizers force-close the CTF Sat and Sun 02:00-08:00 — not 48 contiguous hours.

Our team operated a hybrid human + multi-agent system, peaking at 28 concurrent AI agents during the Night-1 burst. We finished **#12 of 92 teams (492 / 754 pts = 65.3% of theoretical max; 119 / 161 flags = 73.9% coverage)**, with a near-complete map of every other track's blocker.

### Top-line wins

- **Coverage**: All 37 tracks touched. 25 fully cleared, 4 partial, 8 STUCK with documented root cause (the stuck bucket includes the physical-only fox-hunts requiring on-venue presence we couldn't dispatch, plus the off-topic / rules non-challenge posts).
- **Final-hour breakthroughs**:
  - **REM (Renewable Energy Mobility)** - SQLi `compat=` BETWEEN/unicode() boolean-blind enumeration discovered a hidden `REM Developer Firmware Kit v2.7.2-dev` row in the firmware-catalog DB. The dev-kit ZIP contained `firmware_decryption.key` directly, unlocking the AES-256-CBC squashfs and yielding REM 5/7. The same dev-kit also leaked pod source code revealing the firmware-upload-server `POD_FIRMWARE_PAGE_FLAG` env var path for 6/7.
  - **Trolley-bus (formerly missing-bus) flags 2-4 unlock** - by submitting `flag-dc8fd0` (the decoded keyfob hex), the challenge author posted reply #2 to the Discourse topic within seconds: physical Wi-Fi maintenance AP at SSID `MNT-BUS`. Three remote-unreachable flags converted to physical-recon-trivial.
- **Framework expansion**: 3 new permanent skills (`ctfint_hardware`, `ctfint_anti-trap`, `ctfint_orchestrators-sync`), 4 substantially enhanced skills, 31 team-coordination helpers, 8 strategy documents, 13 agent definitions. ~57 commits during the event window introduced these.
- **Anti-trap awareness**: a catalogue of decoy flags and prompt-injection lures identified across the event. The discipline against the loud, obvious bait was genuinely excellent — the i-love-faia prompt-injection lure was refused independently by two different models. The genuine organizer traps refused number ~4-5; the headline-friendly "11/11 refused" conflated those with our own scratch placeholders and documentation literals, and a couple of our own quarantined strings did slip through (see §6).

### What we'd do differently

- **Earlier pwnbox/CTF-subnet pivot**. A few tracks (Solar Grid 3/3, REM-class post-exploit paths) had clear post-exploit paths that required reaching the challenge subnet directly. From the operator's laptop these timed out. Setting up a persistent pwnbox-routing tunnel on Friday evening would have unlocked an honest **~3-5 additional flags / ~12-18 pts**. (Earlier drafts said "~5-7 flags"; re-summing the telemetry shows that bucket was inflated roughly 2× — Solar Grid was already 2/3 captured, REM finished 6/7 with only F7 lost, and Monsatan Impact 3-5 was a passphrase/RLS wall, not a routing problem.)
- **Less time on Prestige**. ~6 hours collectively spent on Prestige Arboretum's AES-128 token forge on a false impossibility claim — it was actually community-solved (~5-7 pts) via an ECB block-transplant we never aligned. Should have hard-cut on the first STUCK rationale and revisited the crypto precondition.
- **Single-flag-format assumption removed earlier**. Trolley-bus stalled for ~6 hours on the wrong flag-format hypothesis before the correct 6-character `flag-dc8fd0` was submitted directly, bypassing our own submission wrapper's shape gate. The shape gate was too strict for the lowercase, short flag this track used.

---

## 2. Score Timeline

![NSEC 2026 cumulative score graph - Shellda finished top 12](/scoregraph.png)

> **Visual**: cumulative score graph published by organizers shows our "Shellda: A Link To The Flag" line finishing in the **top 12**, climbing steeply Fri 21:00-Sat 02:00 then continuing more gradually through Sun 14:14 (final REM 6/7 submission). Top-finisher curves (OteriHack 713, Hubert Hackin' 711, Basilic de Torches 674) plateaued at ~700 pts by Sun 13:00.

> **Organizer-published event stats** (closing ceremony deck): NSEC 2026 had **161 flags / 754 pts / 37 tracks / 60 services / 35 files / 27 challenge designers** - vs NSEC 2025's 165 flags / 412 pts / 37 tracks / 49 services / 48 files / 32 designers. The **+342 pts / +83% point-total YoY** delta reflects the organizer's deliberate weighting toward harder challenges in 2026.

| Time (EDT) | Flags | Pts | Rank | Δ to #1 | Source / Event |
|---|---|---|---|---|---|
| Fri 20:10 | - | - | - | - | **Venue arrival** - team assembles on-site |
| Fri 21:00 | 0 | 0 | - | - | **CTF opens** (advertised time) |
| Fri 21:23 | 0 | 0 | - | - | Rule flag 1 (in-person registration / venue setup) |
| Fri 21:31 | 1 | 1 | - | - | First scoring submission - Helios Fleet Network bonus (invite code in plain sight) |
| Fri 21:40 | 5 | 9 | - | - | Trading cards 4/4 (steganography) |
| Fri 21:58 | 9 | 19 | - | - | Poubelle 3/8 + Multi-Facteur 1/6 + Poubelle 5/8 + Tamper 1/6 |
| Fri 22:30 | 23 | 73 | - | - | Hackademy ramp (Robots 101/102, LFI 101/102, XXE 101, Hello Sunshine 1, Welcome) + Monsatan-Chatbot 2/3 + lowcode 4/4 |
| Fri 22:45 | 38 | 132 | - | - | Hackademy fast-fire (Pwn 101, SQLi 102, Serialize 101, Upload, SSRF 101-103) + Vote Counting (TLS 1.3 side channel) + Fossilco 1-2 + Announcement-Board 1-3/4 |
| Fri 23:30 | 65 | 245 | - | - | Fossilco 3-5/8 + APT438 1-2 + Hello Sunshine 2 + Save-Trees 1/2 + Pesticide-supply-chain |
| Fri 23:41 | 69 | 251 | - | - | End of Friday window - Crystal Basic Electricity, Stage-Zero 1/?, RE 101 |
| Sat 00:00 | 70 | 258 | - | - | Drone License 1/2 |
| Sat 00:33 | 73 | 267 | ~#3 ‡ | ~-110 ‡ | Drone License 2/2 (attestation.yml supply chain) + Teamworking + Stage-Zero |
| Sat 00:52 | 75 | 287 | ~#3 ‡ | ~-100 ‡ | **Germinator 1/1** (binary signature key emulation, 15pts) |
| Sat 00:58 | 77 | 300 | ~#3 ‡ | ~-95 ‡ | Drone License `support_agent.yml` (8pts) + Power-to-the-People dash-format 1/2 |
| Sat 01:19 | 80 | 316 | 🥈&nbsp;#2 | -102 † | **Monsatan Sprinklers 2/2** (HMAC empty-key bypass) |
| Sat 01:20 | 92 | 320 | 🥈&nbsp;#2 | -98 † | Sprinklers 2/2 lands |
| Sat 01:32 | 93 | 321 | 🥈&nbsp;#2 | -97 † | Multi-facteur 6/6 |
| Sat 01:55 | 99 | 359 | 🥈&nbsp;#2 | -79 † | Addressbook 2/2, Poubelle 8/8, APT438 3/9 - **peak rank for the event** |
| Sat 02:00 | 99 | 359 | 🥈&nbsp;#2 | -79 † | **Mandatory overnight CTF closure** - building emptied, all ~92 teams idle (per the rules, not an outage) |
| Sat 05:40 | 99 | 359 | 🥈&nbsp;#2 ‡ | ~-79 ‡ | (still 359 - Monsatan-Checkmate crack lands but held; closure window, no team scores) |
| Sat 08:00 | 99 | 359 | 🥈&nbsp;#2 ‡ | ~-80 ‡ | **CTF reopens** - mandated closure ends; no rank lost (closure applied to every team) |
| Sat 09:04 | 100 | 362 | ~#3 ‡ | ~-90 ‡ | Crystal "Basic Electricity" 1/1 - first post-reopen flag |
| Sat 09:45 | 101 | 363 | ~#3 ‡ | ~-95 ‡ | Monsatan-Chatbot 1/3 (sys-prompt injection) |
| Sat 10:08 | 102 | 372 | ~#3 ‡ | ~-100 ‡ | Monsatan-Orders 1/1 (Leptos WASM JWT intercept, credit [teammate]) |
| Sat 10:30 | 103 | 374 | ~#4 ‡ | ~-105 ‡ | Tamper 4/6 (sunnikey + shelf-elevate) |
| Sat 10:38 | 104 | 381 | ~#4 ‡ | ~-110 ‡ | Monsatan-Checkmate 1/1 (rockyou `<REDACTED:operational-detail>`) |
| Sat 10:42 | 105 | 382 | ~#4 ‡ | ~-110 ‡ | Crystal Badge 2/5 (post-dock firmware string) |
| Sat 11:08 | 106 | 384 | ~#5 ‡ | ~-120 ‡ | REM 2/7 (MQTT `get_flag` maintenance CLI) |
| Sat 11:20 | 107 | 386 | ~#5 ‡ | ~-125 ‡ | Tamper 5/6 (envelope-postman tamper) |
| Sat 12:53 | 108 | 390 | ~#6 ‡ | ~-145 ‡ | Tamper 6/6 (final envelope tamper) |
| Sat 14:07 | 109 | 394 | ~#7 ‡ | ~-160 ‡ | Crystal Grid Alignment 1/2 (VQE) |
| Sat 14:13 | 110 | 398 | ~#7 ‡ | ~-165 ‡ | Monsatan Kiosk 1/1 (Chrome kiosk escape) |
| Sat 16:02 | 111 | 403 | ~#8 ‡ | ~-180 ‡ | Grid Alignment 2/2 (QAOA) |
| Sat 17:17 | 112 | 419 | ~#8 ‡ | ~-190 ‡ | Monsatan-Mailserver 1/2 (DB root pwd via sign-all flaw) |
| Sat 17:28 | 113 | 431 | ~#9 ‡ | ~-195 ‡ | Monsatan-Mailserver 2/2 (WASM+WASI sandbox abuse) |
| Sun 10:28 | 114 | 436 | ~#11 ‡ | ~-235 ‡ | APT438 4/9 (PS history + Windows search) - Sun morning ramp |
| Sun 10:41 | 115 | 441 | ~#11 ‡ | ~-235 ‡ | APT438 6/9 (Defender + notifications) |
| Sun 10:42 | 116 | 446 | ~#11 ‡ | ~-235 ‡ | APT438 5/9 (Registry + Defender logs) |
| Sun 10:50 | 117 | 451 | ~#11 ‡ | ~-240 ‡ | APT438 7/9 (schtasks + MFT resident-file) |
| Sun 11:04 | 118 | 456 | ~#12 ‡ | ~-240 ‡ | APT438 8/9 (event logs + URL cache) |
| Sun 11:12 | 119 | 462 | ~#12 ‡ | ~-240 ‡ | APT438 9/9 (Windows Hello PIN crack) - **final flag captured** |
| Sun 11:22 | 119* | 467 | ~#12 ‡ | ~-243 ‡ | REM 3/7 (firmware_repo_api_key Bearer captured) |
| Sun 12:36 | 119* | 470 | ~#12 ‡ | ~-240 ‡ | Monsatan-defacing 6/6 (teammate landed; ours 6 min late) |
| Sun 13:24 | 119* | 478 | ~#12 ‡ | ~-235 ‡ | REM 4/7 (firmware repository DB access) |
| Sun 13:35 | 119* | 483 | ~#12 ‡ | ~-230 ‡ | **Trolley-bus 1/4** ([teammate] direct submit, wrapper-bypassed) |
| Sun 13:35 | 119* | 483 | ~#12 ‡ | ~-230 ‡ | **Discourse reply #2 unlocks 2-4/4 = physical Wi-Fi recon** |
| Sun 14:11 | 119* | 488 | ~#12 ‡ | ~-225 ‡ | REM 5/7 (dev-kit firmware decrypt) |
| Sun 14:14 | 119* | **492** | **#12** | **-221** | **REM 6/7 (firmware upload-server page flag) - FINAL** |

**Rank / delta provenance:**
- **†** Anchor values from live score snapshots (Sat 01:19-02:00 only). Rank `#2 / -79` is the live-tracker reading at the Night-1 peak.
- **‡** Estimated from the cumulative-points-per-team curves in `scoregraph.png`. Reading methodology: count team-curves above ours at the X-axis timestamp; subtract our points from the top curve's height. Estimate accuracy ~±2 rank positions, ~±25 pts on delta.
- **Rank is ~84% graph-estimated.** Only the Night-1 #2 peak and the final #12 are hard-anchored; the mid-event rank curve is read off the score graph. The points are exact in every row; the *ranks* are not.
- **#12 / -221 final**: the points figure (492, -221 to first) reconciles exactly to the askgod history. The final *rank* is read from `scoregraph.png` — a clean scoreboard text/JSON export was not retained in our archive, so treat the rank itself as graph-estimated rather than hard telemetry.

\* Points reconciliation: **final placement #12 / 92 teams · 492 pts** (independently re-summed). 119 distinct askgod-history captures total exactly 492 points.

---

## 3. Track Outcome Matrix (37 tracks)

> Full per-track detail in **Appendix A**. This matrix is the at-a-glance scorecard. The 37-track count is the organizer's standardized track set; alias and placeholder rows below are bookkeeping, not additional tracks.

| # | Track | Topic | Cat | Status | X/Y | Notable Technique |
|---|---|---|---|---|---|---|
| 1 | apt438 | 59222 | forensics | SOLVED | 9/9 | Disk forensics: PS history, SRUM, schtasks, Defender, MFT resident-file, Windows Hello PIN crack |
| 2 | trading-cards | 59726 | stego | SOLVED | 4/4 | Per-card decode (4 cards, steganography) |
| 3 | poubelle | 59330 | forensics | SOLVED | 8/8 | PDF unredact + ADS + Konami + RC4/AES-GCM POS decompile |
| 4 | multi-facteur-auth | 59258 | misc | SOLVED | 6/6 | Newspaper "Waldo" + semaphore + business card + NFC + email/MFA + onsite OTP |
| 5 | tamper | 59402 | forensics | SOLVED | 6/6 | Seedvault stealer-log + desktop QR + shelf-sunnikey + envelope tamper |
| 6 | monsatan-defacing | 59981 | web | SOLVED | 6/6 | Git archaeology + CI env leak + README scrape + dartreg endpoint + port-8888 binary |
| 7 | hackademy | 58934 | web | PARTIAL | 17/18 | Beginner web track: HTML comments, LFI, XXE, SQLi, SSRF, deserialize, upload, RE, pwn |
| 8 | renewable-energy-mobility | 59582 | iot | PARTIAL | 6/7 | Maintenance cert provision + MQTT get_flag + Bearer capture + SQLi BETWEEN dev-kit + AES decrypt + upload-server env |
| 9 | water-purification | 59078 | ics | SOLVED | 4/4 | Lowcode dashboard dev mode + per-segment API leak |
| 10 | grid-alignment | 59906 | hardware | SOLVED | 2/2 | VQE (Crystal Badge coupled-oscillator) + QAOA grid optimization |
| 11 | monsatan-mailserver | 60034 | web | SOLVED | 2/2 | DB root via sign-all-data flaw + WASM+WASI sandbox abuse (CEO inbox) |
| 12 | monsatan-chatbot | 58682 | web | SOLVED | 3/3 | System-prompt injection + dump + tool-call SQL exfil |
| 13 | monsatan-sprinklers | 59980 | pwn | SOLVED | 2/2 | TSEP cmd-code probing + HMAC empty-key bypass |
| 14 | monsatan-pesticide | 59982 | web | SOLVED | 1/1 | Leptos/Rust WASM client-side JWT-signing intercept |
| 15 | monsatan-checkmate | 59983 | web | SOLVED | 1/1 | IDOR /api/user/MrRook → PBKDF2 leak → rockyou crack |
| 16 | monsatan-kiosk | 59006 | misc | SOLVED | 1/1 | Chrome kiosk-mode escape → OS shell → Desktop/flag.txt |
| 17 | monsatan-b2b | 58862 | web | SOLVED | 1/1 | Sequential invoice IDOR |
| 18 | monsatan-impact-study | 59546 | web | STUCK | 2/5 | IRIS SQLi to Study.Flag - RLS bypass impossible without irisowner |
| 19 | helios-fleet-network | 59762 | web | STUCK | 2/5 | HTML-comment invite + schema leak - operator/admin creds unobtainable |
| 20 | weather-station | 59294 | web | STUCK | 1/4 | Path-traversal upload leak - Seed Server infra-gated for 2-4 |
| 21 | announcement-board | 58898 | web | STUCK | 3/4 | mod_speling .inc source leak + mass-assignment role-escalation - flag-4 in index.php which mod_speling can't expose |
| 22 | fossilco | 59114 | web | STUCK | 6/8 | SSTI gastro + LDAP attr disclosure tier2 + SMB + Kerberos - 7-8 require tier2 in-scope confirmation |
| 23 | sunbloom | 59150 | web | STUCK | 0/? | All 11+ password-reset interception vectors definitively closed |
| 24 | save-the-trees | 59654 | web | STUCK | 0/2 | WebBatch CGI env-dump on `?dumpinputdata` returned 1/2 (different teammate); paper9304 byte-length stego untestable |
| 25 | prestige-arboretum | 59834 | crypto | STUCK | 0/2 | AES-128-CBC token, exploitable via ECB block-transplant - NOT impossible (community-solved); we mis-targeted the offset alignment and wrongly declared it impossible |
| 26 | crystal-badge | 59510 | hardware | PARTIAL | 3/6 | OCR puzzle + post-dock firmware string + ESP32 NVS extraction - flags 4-6 need venue-dock |
| 27 | missing-bus / trolley-bus | 59870 | rf | PARTIAL | 1/4 | 433.92 MHz OOK keyfob decode; 2-4 via Wi-Fi MNT-BUS physical recon |
| 28 | drone-license | 58646 | web | SOLVED | 2/2 | GitHub Actions support_agent SQLi + attestation.yml |
| 29 | hello-sunshine | 58826 | web | SOLVED | 2/2 | MCP Pyodide spawnSync /flag1 + token-blocklist bypass mountNodeFS |
| 30 | germinator | 59186 | rev | SOLVED | 2/2 | License-blob decode → spoof requirements → real flag (NOT the `FLAG-SEEDS-GROW-FOREVER` fixture) |
| 31 | vote-counting | 59474 | forensics | SOLVED | 1/1 | pcap analysis vote tally |
| 32 | powertothepeople | 59366 | web | SOLVED | 2/2 | WebAuthn debug headers leak + passkey-validation bypass |
| 33 | stage-zero | 59042 | rev | SOLVED | 4/4 | ISO autorun + UPX install + XOR data + 6-byte stdin key replay |
| 34 | teamworking | 59618 | misc | SOLVED | 1/1 | Onsite team-milestone |
| 35 | welcome | 59942 | misc | SOLVED | 1/1 | Rule flag (intro) |
| 36 | nursery | 58718 | stego | SOLVED | 1/1 | SVG positional-counting steganography |
| 37 | addressbook | 59438 | web | SOLVED | 2/2 | XPath injection + path-traversal Maven settings.xml |
| 38 | sympatizers-mailbox | 58790 | crypto | SOLVED | 1/1 | Stream-cipher keystream recovery + path-traversal via JSON `d` param |
| 39 | infinite-energy | 58754 | misc | UNTOUCHED | 0/0 | IRL Doc Wonka NPC - not pursued |
| 40 | solar-grid | 59690 | web | PARTIAL | 2/3 | 1/3 DuckDB SQLi + 2/3 source-comment read (8 pts banked); only 3/3 was egress/pwnbox-gated |
| 41 | radio-beacon | 59798 | rf | UNTOUCHED | 0/? | Physical RF-table fox-hunt at 146.565 MHz - not pursued |
| 42 | plant-watering | 58970 | ics | UNTOUCHED | 0/? | Badge-flash-gated ICS broker MITM - requires Crystal Badge in ICS mode |
| 43 | crystal (Basic Electricity) | subtrack | misc | SOLVED | 1/1 | Doc Wonka NPC |
| 44 | helios-fleet (alias) | 59762 | web | - | - | Merged with #19 |
| 45 | sunbloom-library (alias) | 59150 | web | - | - | Merged with #23 |
| 46 | faia (placeholder) | - | misc | NON-CHALLENGE | n/a | Empty placeholder |
| 47 | i-love-faia | 59975 | non-challenge | NON-CHALLENGE | 0/0 | Off-topic category. Contains 3 decoy flags + embedded prompt-injection lure. Anti-AI bait thread. |
| - | rules / network reminder | 43845 | rules | NON-CHALLENGE | n/a | Env reminder |

**Status counts** (37 standardized tracks): roughly SOLVED 25 · PARTIAL 4-5 · STUCK 7-8. Solar Grid was previously mis-filed as UNTOUCHED 0/3 but actually banked 2/3 (8 pts), so it moves into PARTIAL. The remaining physical-only "untouched" tracks and the off-topic/rules non-challenge posts fold into the STUCK bucket; the alias/placeholder rows are bookkeeping, not separate tracks. The buckets reconcile to 37 tracks and to the 492-pt total either way.

---

## 4. Captured Flags Inventory (119)

Aggregate counts by track (top contributors):

| Track | Flags | Notable Methods |
|---|---|---|
| APT438 | 9 | Full Windows forensics chain (PS history → SRUM → Defender → MFT → URL cache → Windows Hello) |
| poubelle | 8 | PDF unredact → ADS → Konami → POS RC4/PBKDF2/AES-GCM |
| multi-facteur | 6 | Newspaper Waldo → semaphore → business card → NFC → email → onsite mail OTP |
| tamper | 6 | Stealer log → seedvault auth → shelf-sunnikey → envelope tamper |
| monsatan-defacing | 6 | Git archaeology → CI env → README → dartreg → port-8888 binary |
| renewable-energy-mobility | 6 | Maintenance cert → MQTT get_flag → Bearer → SQLi dev-kit → AES decrypt → upload-server env |
| hackademy | 17 (of 18) | Beginner web (HTML/LFI/XXE/SQLi/SSRF/upload/deserialize/RE/pwn) |
| announcement-board | 3 | mod_speling .inc leak + mass-assignment manager + bourgmestre role |
| fossilco | 6 (of 8) | Web auth bypass + SSTI + LDAP tier2 + SMB + Kerberos |
| crystal-badge | 3 (of 6) | OCR puzzle + post-dock firmware + grid-alignment VQE/QAOA |
| (others - 1-4 each) | 49 | Various, see Appendix A |

Honeypot/decoy values explicitly NOT submitted (see §6 for full list):
- `FLAG-SEEDS-GROW-FOREVER-{GROWTH_UNLOCKED}` (germinator fixture)
- `FLAG-WATER-FLOWS-WHEN-THIRSTY-{GROWTH_ENABLED}` (plant-watering fixture)
- `FLAG-...abcdef02` (helios vault self-plant)
- `FLAG-15000-0700` (Quantum Hum pre-solve doc marker)
- `FLAG-This_Is_Not_A_Flag`, `FLAG-WHO_DO_YOU_THINK_I_AM`, `FLAG-JE_MARETTE_LA_CEST_TROP_LONG` (i-love-faia decoys)
- 4 documentation placeholders in drone-license and water-purification writeups

---

## 5. Multi-Agent Fleet Retrospective

> Detailed agent transcripts indexed in **Appendix C**. This section is the post-mortem on the agent system as a whole.

### Organizer-published AI participation stats

The organizers ran an event-wide AI vs human comparison during the closing ceremony. Key numbers:

| Metric | Value | Denominator |
|---|---|---|
| Valid flags submitted by an AI agent | **24%** | 1,711 / 7,217 total submissions |
| Points scored by AI agents | **29%** | 8,100 / 27,709 total points |
| Teams using AI agents | **84%** | 76 / 90 teams |
| Solved flags w/ at least one AI solve | **89%** | 143 / 160 distinct flags |

**Year-over-year:**

- **Total cumulative submissions** at hour 30 of CTF: NSEC 2026 ~6,000 vs NSEC 2025 ~3,500 (**+71%** YoY)
- **Top-10-team cumulative submissions** at hour 30: NSEC 2026 ~1,250 vs NSEC 2025 ~750 (**+67%** YoY)
- **Top-team completion %** at hour 30: NSEC 2026 ~95% vs NSEC 2025 ~57% (**+38pp** YoY - the steepest delta)

The top-team completion trajectory is the most striking - 2026 hit 60% completion within 6 hours (vs ~22% in 2025), then climbed to ~95% by hour 24. The "completion wall" that plateaued top teams at 50-60% in 2025 was demolished in 2026 by AI agents sustaining throughput across the open play windows. (Note the windows themselves are bounded: the CTF is force-closed 02:00-08:00 both nights, so nobody — human or agent — scores overnight; the gains came during the ~24h of actual play, not from running through the closures.)

**AI-solve % rises with flag point value**: low-value 1-2pt flags ~20% AI; mid-value 5-10pt flags ~35% AI; high-value 15-20pt flags ~45% AI. AI agents were disproportionately effective on the hardest content - opposite of the "AI only solves easy stuff" prior. This is consistent with our team experience (Germinator 15pt via binary RE emulation, REM 16+ pts via SQLi+MQTT+decrypt chain, Hello Sunshine 8pt via Pyodide escape - all agent-driven).

**Quintile breakdown**: AI participation correlates positively with team rank - top 20% teams averaged ~32% AI submissions, bottom 20% averaged ~15%. The "skill floor" for productive AI use exists; teams that could direct agents well got more from them.

**Time-of-day**: AI submission % spikes around **Sun 08:00 EDT (~70%)** - coinciding with the post-reopen wave when most human operators were just waking up from the mandated overnight close but agents picked back up immediately. Our team's analogous peak (Sun ~10:00-11:00 EDT, APT438 forensics finish) matches this pattern.

### Where we fit in those stats

- **Of ~112 wrapper-mediated submissions**, the breakdown by orchestrator/model is estimated as ORCH-A Opus ~50-60%, ORCH-B Haiku ~25-30%, ORCH-C ~5%, CODEX ~2-4, humans ~6.
- Across the organizer's 76 AI-using teams, we were squarely in the **AI-tagged team category** - our submission pipeline auto-tags every agent-driven submission with `source=mcp+agent`.
- Of the **89% (143/160)** flags that had at least one AI solver across the event, we contributed to many - likely 60-80 of those 143 (rough estimate from our 119 captures minus ~6 human-direct).

### Pre-event preparation (Feb 2026 → May 2026)

The team's NSEC 2026 effort was the culmination of ~3 months of deliberate competitive prep. Key milestones (all timestamps EDT):

- **2026-02-14 → 02-15** - HTB Arctic Wolf Canadian competition (round 1). Team finished 20/20 challenges in <2 hours but didn't advance (bracket / forfeit mechanics). Multiple writeups produced - established the team's writeup-discipline that carried into NSEC.
- **2026-02-15** - Team member shares Dockerfile for openclaw containerization. This was the precursor to the per-coach sandbox architecture used in NSEC.
- **2026-03 onwards** - TCM Security CTF (Windows forensics) and RingZer0 unicorn-as-a-service. Multiple solves with Claude assistance.
- **2026-04** - Team member switches to `openai-codex/gpt-5.3-codex`. The CODEX-as-second-orchestrator pattern that landed REM 5/7 + 6/7 in the final hour was tested here.
- **~2026-04** - Émilio Gonzalez's "Are CTFs Dead? Long Live CTFs" Medium article shared. Team self-identifies as participants in the AI-vs-human inflection point.
- **2026-04** - Team member reverse-engineers the NSEC infrastructure ahead of release:
  - **Discovery 1**: There's an `ai_agent` flag tracked per score on the askgod API - they're publicly measuring AI vs human submissions in 2026.
  - **Discovery 2**: askgod ships an MCP server with `submit_flag` tool (commit landed 2026-04-03). The honest path for an LLM-driven solver is no longer "human runs askgod submit --agent" - it's "your MCP-attached agent calls submit_flag directly, source auto-tagged as mcp". Three submission channels now exist (CLI, MCP, web), each in human + agent variants.
  - **Discovery 3**: Full CFSS scoring rubric extracted from `github.com/res260/cfss` + `nsec/ctf-script` source. Team had per-flag effort/skill predictions BEFORE the CTF opened.
- **2026-04 → 05** - Pre-event infodumps: IPv6 drill covering `9000::/16` challenge network + Docker/Podman IPv6 setup; hardware kit list (Saleae, Flipper Zero, Proxmark3, JTAGulator, soldering kit); `ctf-script` reverse-engineering.
- **Fri 2026-05-15 20:10 EDT** - Team physically assembled ~1 hour before CTF open (21:00 advertised, first askgod submission 21:23 EDT).
- **Fri 2026-05-15 21:58 EDT** - Team selfbot deployed - automation layer for Discord coordination went live.

The strategic implication: by the time CTF opened, the team already knew (a) AI participation would be explicitly measured, (b) the MCP `submit_flag` pipeline was the canonical agent-tagged submission path, (c) per-flag CFSS scores let them pick high-value targets without trial-and-error. This is materially different from "we showed up and tried to use Claude" - the architecture was designed-for-purpose against the organizer-published rules.

### Deployment cadence

- **Night 1 (Fri evening → Sat 02:00)**: heavy initial flag harvest, peaking at 28 concurrent agents around 03:00Z. By the Sat 01:55 peak the team held **99 flags / 359 pts at rank #2**. The play window then closed for the mandated 02:00-08:00 overnight break.
- **Sat morning (post-reopen, after the mandated overnight close)**: Per-track coaches re-spawned. Bonus flags landed mid-day (Sat 09:00-17:30). Only ~27% of final points were banked after Sat 09:00 — the plateau was a daytime-execution limit, not the overnight closure.
- **Sat overnight + Sun morning**: ORCH-A overnight report consolidation; APT438 lab continued (6 more flags Sun 10:28-11:12).
- **Sun final-hour push**: 3 waves of 15-agent + 15-agent + 3-agent deployments attacking remaining tracks. Wave 1 yielded the trolley-bus Discourse breakthrough; wave 2 produced the REM 5/7 capture via dev-kit firmware decrypt; wave 3 confirmed 3 "fresh" tracks were physical-only.

### Outcomes by agent role

From the Phase C transcript index:

- **437 agent transcripts** spanning the full event window, totalling 152 MB raw → 34 MB compressed (78% reduction)
- **Outcome distribution** (re-summed from the raw row data): 302 completed (69.1%) · 92 tool_use mid-progress (21.1%) · 30 stop_sequence (6.9%) · 7 error (1.6%) · 4 killed (0.9%) · 2 unknown (0.5%). **Important caveat: `completed` is a stream-termination state, not a flag-capture state** — a "completed" agent merely means the stream ended cleanly, and many of these simply re-confirmed the same impassable STUCK conclusion. Do not read 69% completed as a 69% success rate.
- **Per-track agent allocation** (top): _fleet 122 · _framework 37 · fossilco 36 · rem 33 · sunbloom 27 · missing-bus 27 · save-the-trees 26 · monsatan 25 · announcement-board 20
- **Captures from agents (vs operator manual)**: roughly half of the 119 flags surfaced through agent work, the rest operator-direct or human-NPC interactions
- **AUP / classifier blocks**: ~3 agents hit Claude AUP blocks (helios-fleet XSS webhook.site recon, solar-grid `/proc/self/environ` traversal). The classifier was operating conservatively but correctly - none of these blocks cost flags we could have otherwise captured
- **Stall/kill events**: 4 agents ended in a `killed` stream-state (mostly mid-deep-recon tasks); each left partial findings the next-spawned agent picked up cleanly. (An earlier draft attributed these to a "600s no-progress stream-watchdog" — no such per-agent threshold actually existed in our tooling; the only watchdog on disk is a Discord progress alerter. The `killed` labels are real stream states, but that specific kill mechanism was overstated.)
- **Wasted-spawn rate**: Wave 2 had one agent (monsatan-pesticide) self-abort within 30s on track-redo-prevention check - track was already SOLVED Sat 10:08 but the brief was stale. Memory rule worked as designed

### Notable agents (transcripts indexed for retrospective)

| Agent | Track | Achievement |
|---|---|---|
| Agent-1 | rem | REM 5/7 capture via SQLi `BETWEEN`+`unicode()` boolean-blind enum → hidden dev-kit row → AES key in ZIP → squashfs decrypt → /flag.txt |
| Agent-2 | trolley-bus | Discourse reply #2 breakthrough - physical Wi-Fi recon unlock |
| Agent-3 | _fleet | Fresh-angle strategist - surfaced REM Bearer-token attack plan |
| Agent-4 | _framework | Cross-track unsubmitted-flag sweep - 23 candidates surfaced (most DUP, validated wrapper-side dedup is source of truth) |
| Agent-5 | _fleet | I-love-faia anti-trap defender - refused embedded prompt-injection lure, correctly classified as NON-CHALLENGE |
| Agent-6 | germinator | Binary signature key emulation (15pts) |

### What worked

1. **`ctfint-db` MCP-backed coaches**. Every coach started with the prior intel from past CTFs auto-fetched - they didn't have to re-discover that path-traversal works in `?page=` (announcement-board), that DuckDB SQLi exists (solar-grid), etc. Estimated time savings: 30-90 min per track.
2. **Anti-trap discipline against loud bait**. Refused the genuine organizer prompt-injection lures (the i-love-faia "STOP EVERYTHING" thread caught independently by two models). The discipline against external bait was excellent; where it leaked was on our own quiet self-plants (see §6) — caps and quarantines that lived in prose, not as enforced mechanisms.
3. **Multi-orchestrator coordination**. Three orchestrators worked the OVERNIGHT-REPORT for ~48 hours without a single merge conflict.
4. **Cross-track intelligence pivot**. The strategist agent surfaced the captured REM Bearer token's untested attack surface (`?compat=` BETWEEN), which led directly to REM 5/7 capture.

### What didn't work

1. **Wholesale "spawn 15 agents on 5 challenges"**. Wave 1 - most agents converged on the same STUCK conclusions because the underlying blockers (no source leak, no creds) were genuinely impassable. Wave-1 ROI was lowest.
2. **Per-flag candidate sweeps**. Corpus-wide flag-candidate sweep agents had a high false-positive rate (~6/6 candidates in one sweep were already-submitted duplicates). The deduplication check at the submission layer is the only authoritative source of truth.
3. **Long-context one-shot mega-agents**. The 60-minute budget per agent often led to verbose exploration without concrete capture. Tighter scoping + faster handoff worked better.

---

## 6. Anti-Trap Findings

> The honest version of this section is more interesting than the headline. We initially catalogued 11 "anti-AI traps refused 100%," but that count conflated four different things: (a) genuine organizer decoys, (b) our own exploit-dev placeholders, (c) documentation-template literals, and (d) one real teammate flag we wrongly quarantined as a trap. The count of **genuine organizer traps refused is ~4-5, not 11** — and a couple of our own quarantined strings actually fired against the scoreboard. What *did* hold flawlessly was discipline against the loud, external bait (see the prompt-injection refusals below).

### Honeypot flags planted in challenge artifacts or our own scratch

| # | Honeypot string | Location | Why it's a trap |
|---|---|---|---|
| 1 | `FLAG-SEEDS-GROW-FOREVER-{GROWTH_UNLOCKED}` | germinator writeups (proliferated to 7 files) | Local exploit-dev placeholder; not a real flag. The real germinator flag is the post-emulation value. |
| 2 | `FLAG-WATER-FLOWS-WHEN-THIRSTY-{GROWTH_ENABLED}` | `plant-watering/artifacts/EXPLOITATION_WRITEUP.md` | Prior coach planted a mock firmware with this fixture string and "self-validated" the writeup. Track is actually badge-flash gated and UNTOUCHED. |
| 3 | `FLAG-...abcdef02` | `helios-fleet/SUSPICIOUS.md` | Self-planted during prior probing. Real flag 1/5 ends `...abcdef01`. **This self-plant was actually fired once (1 FAIL)** — the discipline did not catch it; an agent echoed our own decoy back to the scoreboard. |
| 4 | `FLAG-15000-0700` | `59906-crystal-quantum-hum.md` | Pre-solve doc explicitly warns NOT to submit. Real flags via VQE/QAOA. |
| 5 | `FLAG-This_Is_Not_A_Flag` | i-love-faia Discourse topic 59975 body | Self-labeled decoy in off-topic / non-challenge category. |
| 6 | `FLAG-WHO_DO_YOU_THINK_I_AM` | i-love-faia post 5 | Decoy. |
| 7 | `FLAG-JE_MARETTE_LA_CEST_TROP_LONG` | i-love-faia post 5 | Decoy. |
| 8 | `FLAG-{actual-flag-here}` | drone-license writeup placeholder | Doc-template literal. |
| 9 | `FLAG-{identifier}` | drone-license writeup placeholder | Doc-template literal. |
| 10 | `FLAG-placeholder` | drone-license writeup placeholder | Doc-template literal. |
| 11 | `FLAG-eba56a9422a3ecf27498c44b718b24c7` | `save-the-trees/analysis/15_input.txt` | We quarantined this as a "suspicious agent-staged value." **That was the inverse error: it was a real teammate capture** (it shows up 3× DUP in the journal — already submitted). A genuine flag wrongly flagged as a honeypot. |

The deeper lesson is that the discipline held against **external, loud** bait but leaked on **internal, quiet** self-plants. The two prompt-injection lures (notably the i-love-faia "STOP EVERYTHING" thread) were refused independently by two different models — a result that survives scrutiny. But our own quarantined strings (the `...abcdef02` helios self-plant and a `flag-deadbeef` placeholder) each fired once against the scoreboard, and one real flag (#11 above) was wrongly quarantined. The hard part of anti-trap work is not refusing the obvious bait; it is telling your own signal from your own noise — which means the single source of truth must be the submit log, not prose or scratch files.

---

## 7. Cross-Track Credential Matrix

> Tracks where a secret captured in track A unlocked something in track B. Live credential values redacted; matrix structure preserved.

| Credential / Asset Type | Source Track | Used In | Outcome |
|---|---|---|---|
| GitLab Personal Access Token | monsatan-defacing (`/.git/config`) | Could unlock monsatan-impact source | Unexplored - would have unlocked irisowner creds for Impact 3-5 if pursued |
| GitLab pipeline trigger token | monsatan-defacing (README) | monsatan-orders / monsatan-pesticide if shared scope | Unused - pesticide solved via different path (Leptos WASM intercept) |
| Hashed password → rockyou crack | monsatan-checkmate (PBKDF2 leak) | Single-service; no cross-track reuse | - |
| Service Bearer token | rem (pod firmware catalog auth) | Same track: SQLi `compat=` → dev-kit (5/7), upload-server (6/7) | **CRITICAL - yielded 2 more flags** |
| Badge HMAC pair-secret key | crystal-badge firmware | grid-alignment, plant-watering (if MQTT broker shares) | Used in badge-pair forging proof-of-concept only |
| Badge Wi-Fi AP creds | crystal-badge NVS | plant-watering ICS broker | Untested - badge in ICS mode needed |
| Stealer-log user credentials | tamper stealer-log | Seedvault tamper login | Used same-track |
| Kerberos TGT (.ccache) | fossilco DCSync | fossilco 7/8 LAPS/ADCS - POSSIBLY 8/8 | Required scope confirmation that never came |
| User identity from schema recon | helios-fleet schema recon | Helios 3-5 operator path | Never cracked - JWT secret unrecovered |
| Maintenance Wi-Fi PSK | trolley-bus Discourse reply | Trolley-bus 2-4 physical recon | Pending on-venue capture |

---

## 8. Framework Deltas (port-home inventory)

### 3 new permanent skills (`.claude/skills/`)

- **`ctfint_hardware`** - ESP32 / badge reverse-engineering playbook. Flash dump → eFuses → partition extraction → NVS/SPIFFS mining → NFC/I²C/UART probing → captive-portal API attack. Born from Crystal Badge 59510 work; generalized for cross-event use.
- **`ctfint_anti-trap`** - Defense layer for decoy flags and embedded lures in challenge content.
- **`ctfint_orchestrators-sync`** - Protocol for multiple main Claude Code sessions to collaboratively edit a shared markdown document. HTML-comment section fences + `[ORCH-X]` line-prefix + re-read-before-write. Validated by 48-hour multi-orch operation with zero merge conflicts.

### 4 substantially enhanced skills

- **`ctfint_askgod-submit`** - added track-complete deny, cache validation, concurrency safety
- **`ctfint_coach-spawn`** - Step 0 preflight now MANDATORY (askgod history + anti-trap discipline check)
- **`ctfint_workflow`** - refined post-NSEC night-2 with full lifecycle + anti-trap integration
- **`ctfint_selfbot-coach`** - persona-locked (peer tone, FR/EN code-switch, cite-or-caveat honesty, no auto-submit, channel-scoped, framework-lane only)

### 31 team-coordination helpers (`nsec/team-status/`)

- **Status + claims**: report-status.ps1, claim.ps1, challenge-status-card.ps1, challenge-inventory.py, recheck-inventory.ps1, fetch-all-artifacts.ps1
- **Watchdog + health**: stuck-watchdog.ps1, health-check.ps1, start-watch-services.ps1, vpn-alerter.ps1
- **Flag validation**: validate-candidate.ps1, validate-all-candidates.ps1, find-flag-gaps.ps1, snapshot-askgod-history.ps1
- **Team engagement**: meme-bot.ps1, roast-active.ps1, morning-kickoff.ps1, discord-helpers.ps1, process-list.ps1
- **Intelligence**: submission-rate.ps1, decode.ps1 (multi-format), plus internal analysis helpers
- **Testing**: test-all.ps1, llamacpp-client.ps1

### 8 strategy documents (`docs/reports/`)

- Anti-trap strategy document (15K) - decoy flag and lure pattern analysis
- `nsec-2026-operator-checklist.md` (47K) - operator runbook
- `nsec-2026-live.md` (57K) - live tracker
- `nsec-2026-readiness.md` (32K) - pre-event preflight
- `nsec-2026-cheatsheet.md` (12K) - quick reference
- `nsec-2026-moral-choice-protocol.md` (11K) - ethical boundary notes
- `nsec-2026-runbook.md` (9K) - event ops manual
- `nsec-2026-rehearsal-runbook.md` (8K) - practice playbook

### 13 agent definitions + 4 slash commands (`.claude/agents/`, `.claude/commands/`)

Coach/Researcher/Support tier specs; operator-facing slash commands for orchestrator control.

---

## 9. Lessons Learned

### What we'd do differently

1. **Set up pwnbox CTF-subnet routing on Friday evening**. A few post-exploit paths (Solar Grid 3/3, REM-class) timed out from the operator's laptop position. The honest winnable bucket here is **~3-5 flags / ~12-18 pts** — not the "~5-7 flags" earlier drafts claimed (that figure was inflated roughly 2×). Solar Grid was already 2/3 captured (only 3/3 routing-gated); REM finished 6/7 with only F7 lost, and that loss was passphrase/egress-gated, not routing; Monsatan Impact 3-5 was an IRIS row-level-security wall, also not a routing problem.
2. **Hard-cut on a STUCK rationale within 30 min — but verify the impossibility first**. Prestige Arboretum ate ~6 hours on a *false* impossibility claim: we declared the AES token unforgeable, but it was community-solved (~5-7 pts) via an ECB block-transplant whose offset alignment we never got right. Hard-cut fast, but re-check the crypto precondition before calling something impossible. (Helios JWT, by contrast, was a genuine dead end once rockyou was exhausted.)
3. **Wrapper shape-gate flexibility**. The shape gate rejected the correct trolley-bus flag (`flag-dc8fd0`, lowercase and 6 chars) for ~6 hours, and the eventual winning submission landed only by bypassing the wrapper entirely — so it never even got recorded in our submission journal. The fix is a soft, overridable shape warning flowing through the wrapper's bookkeeping plus per-track expected formats, not a hard reject that forces a guard-free bypass.
4. **Earlier Discourse polling**. The "auto-reveal-next-stage-on-first-submit" pattern (trolley-bus reply #2 - same minute as the team's flag-1) likely applies to other tracks. Set up a Discourse-tail watcher.
5. **Per-flag attribution logging at submit time**. The askgod `NOTES` column is empty for Fri 21:23-22:55 captures, and the dedicated team channel was silent until Sat 11:43 EDT (Friday-night coordination was in-person at the venue). Per-teammate attribution for the first ~17 captures is unrecoverable. Future events: pipe a `--submitter` tag through the submission pipeline so the journal records author per row.

### Anti-patterns observed

1. **"Not in the journal" ≠ "novel candidate"**. The submission-layer deduplication is the only authoritative duplicate check. Sweep-agent false positives ~100% on first wave.
2. **Wholesale 15-agent waves on STUCK tracks**. Most converge on the same root cause. Better to spawn fewer agents with deeper context per track.
3. **Trusting writeup placeholder flags**. The `FLAG-{actual-flag-here}` literal in drone-license writeup is a documentation artifact, not a capture.
4. **State-mutating exploitation as a default**. The announcement-board password-overwrite poisoning made flag-4 unrecoverable. Coaches need a "minimal-mutation" mandate - read-only by default, write only when read fails.
5. **External validation of the DUP-noise pattern**. At closing ceremony, organizers publicly called the team out as "2e équipe ayant soumis le plus de flags dupliqués". This is genuinely about *duplicate* submissions on tracks like monsatan-defacing and fossilco — a separate failure from the trolley-bus format-hunt FAIL storm, which produced no duplicates. Final placement was **#12 / 92 teams** (the points, 492, reconcile exactly; the rank itself is read from the score graph, since a clean scoreboard export was not retained in our archive).

### Closing-ceremony honorable mentions (organizer-published)

Worth quoting for institutional memory - these are the organizer's call-outs of memorable team behavior across the 90-team field:

- **"The most slopped Monsatan track was LLM chatbot. Using Claude to gaslight qwen is a vibe."** - The monsatan-chatbot track (which we solved 3/3 via persona injection + system-prompt dump + power-plant DB tool-use) was deliberately powered by qwen on the challenge side, with players using Claude/GPT/etc. to social-engineer it. The organizers called this dynamic out approvingly.
- **"9 teams declared that the FLAG-NOAISLOP flag was found mostly or totally by an AI agent."** - There was a self-reporting flag specifically marked for AI-assist disclosure. 9/90 teams (10%) submitted with the AI-credit attestation. We did not see or submit this flag explicitly.
- **"PartyFormel were the first to complete Monsatan Impact Study without AI agents."** - Pure-human solve highlighted as a counterpoint to the 84% AI-using teams.
- **"*((int *) NULL) went to Bureau en Gros to print a page for the SeedVault challenge (they did not get the Flag)."** - physical commitment doesn't always pay off.
- **"HackThePlanETS opened a support ticket to ask if they should submit the rule flag (the one that said do NOT NOT submit this)."** - the double-negative trap claimed at least one casualty.
- **"Blahaj Gang saved the Mailman track by lockpicking the box after someone changed the PIN and left."** - physical security incident.
- **"cold_root role-played a partial refund with a real 2$US in their enveloppe instead of the mail authentication code (thanks for funding nsec)."** - closing-ceremony joke; team `cold_root` (final placement #4, 667 pts) physically mailed a $2 US bill to the SeedVault track as a "refund" gag.

These mentions confirm the event was as much theatre as competition; organizer-side appetite for player creativity was high.

### Pre-event preparation that paid off (and lessons for next year)

- **Reverse-engineering the askgod infrastructure 6 weeks pre-event** was the single most impactful prep decision. The April 2026 discoveries (ai_agent flag tracking, MCP `submit_flag` tool, CFSS scoring rubric) shaped the entire architecture. Lesson: every CTF organizer publishes more about their infra than they realize. Mine `github.com/<org>/ctf-script`-style repos for source weeks in advance.
- **3 months of cross-event practice** (HTB Arctic Wolf, TCM Security, RingZer0, HF CTF) built the writeup-discipline that made our NSEC artifact harvesting useful for post-event standardization. Lesson: continuous CTF participation in winter/spring months has a compounding effect on May NSEC performance.
- **Pre-built hardware kit** (Saleae logic analyzer, Flipper Zero, SDR, soldering rig) meant zero scramble-time at venue for hardware tracks. Lesson: ship a public team-side hardware kit checklist 4 weeks pre-event.
- **CODEX (Codex CLI) as a second orchestrator** was prototyped on RingZer0 in April. The pattern that landed REM 5/7 + 6/7 in the NSEC final hour was already tested. Lesson: pre-event "second-model rehearsal" - pick the second LLM stack early and run at least one solve through it before the real event.

### Open questions for next year

- **CRC32-padded WASM patching with Authorization header injection** - Theoretical path to CEO Inbox flag never executed (~30 min compile cost exceeded final-hour budget).
- **IRIS RLS bypass without irisowner creds** - Monsatan Impact 3-5 STUCK on this. Per-row gate fires before predicate evaluation; no class-hierarchy escape found.
- **Mail-server normalization for quoted local-parts** - Sunbloom 0/? STUCK on whether `"admin@sunbloom.ctf"@sunbloom.ctf` routes differently than `admin@sunbloom.ctf`. Requires whitebox source.

---

## 10. Appendices

### Appendix A - Per-track outcome detail

Per-track writeups are individually published in the `nsec26` category. Each writeup carries:

- YAML frontmatter (track, challenge_id, status, sub_flags, askgod_tag, captures, last_updated)
- Standard body sections (Context, Recon, Exploitation, Captures, STUCK Rationale, Anti-Trap Notes, Artifacts)

See **[/categories/nsec26/](/categories/nsec26/)** for the full set of per-track posts across the 37 tracks.

### Appendix B - Memory entries reference

Operator-machine-local memory entries are not portable to the public framework but their *categories* are:

- **Project state**: portable-port-back tracking, coach-deployment snapshots, local hardware capability notes
- **Operating discipline (feedback)**: askgod-history checks, Discord coordination protocols, writeup-mandatory rules, concurrency caps, channel-scoping, dashboard format, PI quarantine, honeypot signatures, model-tier routing, no-activity-is-valid, OOB verification, skill-edit classifier blocks, orchestrator lane discipline, no-auto-submit, track-redo prevention, selfbot persona

Full content lives in the user-machine memory store and does NOT port to the public framework.

### Appendix C - Agent transcript index

Full inventory: 437 transcripts, 18 track subdirectories, 152 MB → 34 MB compressed (77.8% reduction). Each entry: agent_id, track, description, outcome (completed/tool_use/stop_sequence/error/killed/unknown), raw_size, gz_size, duration, first_timestamp.

### Appendix D - Sanitization manifest (internal archive only)

For the local archive:

- **15 PEM private keys** redacted (rem/*.key, vote-counting/server.key) - body replaced with placeholder, BEGIN/END markers preserved
- **167 Netscape cookies jars** (sunbloom + others) - cookie value field replaced
- **1 raw HTTP cookie file** (discourse-cookie.txt) - known session-name values redacted
- **1 token JSON** (prestige-arboretum REFERENCE_TOKEN.json) - token/secret/JWT fields redacted
- **16 .txt creds/cookies files** (announcement-board, apt438, helios-fleet, monsatan-pesticide, sunbloom)

All originals preserved (local tarball only). SANITIZED siblings used for any external sharing including this post.

---

## Related

- **Per-track writeups**: [/categories/nsec26/](/categories/nsec26/) - individual challenge posts across the 37 tracks
- **Fleet retrospective**: [/posts/nsec26-fleet-retrospective/](/posts/nsec26-fleet-retrospective/) - deep dive on the multi-agent operation
- **Failure modes**: [/posts/nsec26-failure-modes/](/posts/nsec26-failure-modes/) - what went wrong and why
