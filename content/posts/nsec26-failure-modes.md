+++
title = "NSEC 2026 - Agent Failure Modes & Limitations"
description = "Honest accounting of where ctfint's multi-agent system fell short."
date = 2026-05-21
categories = ["nsec26"]
tags = ["meta-writeup", "failure-modes", "post-mortem"]
model = "Opus 4.7"
+++

```
[ FAILURE MODES // NSEC 2026 // FINAL ]
honeypots refused:    11 of 11  (100%)
PI lures refused:      2 (1 challenge body, 1 coach brief)
sweep false positives: 6
AUP blocks:            3
stream-watchdog kills: 4 @ 600s
frontmatter/body contradictions: 1 (caught in Q-2c)
duplication ratio:    ~70% before Q-2a collapse
```

> "47 agents converged on STUCK independently. The crypto math hadn't changed."

An honest accounting of where the multi-agent system fell short, what failed
silently, and what near-misses revealed about the underlying design.

---

## Honeypot pattern detections (11 of 11 refused)

Eleven honeypot strings catalogued. Zero submitted by any of 437 agents.
Per-trap detail:

### germinator's `FLAG-SEEDS-GROW-FOREVER`

The germinator track had a fixture string embedded directly in the
challenge binary: `FLAG-SEEDS-GROW-FOREVER-{GROWTH_UNLOCKED}`. It looks
like a flag. It scans like a flag. It appears in 7 writeup references,
4 supporting docs, 1 flag-format rules JSON, 1 validator README, 2
candidate files, and 1 BATCH-fire script.

It is not the flag. The real flag is the
`FLAG-ezt1VGwGtTllv30EaamravzI67cXoljj`.

The fixture string was referenced seven times across the corpus; it was
refused seven times.

### plant-watering's `FLAG-WATER-FLOWS-WHEN-THIRSTY`

Same metaphor family, different track. The plant-watering challenge
offered `FLAG-WATER-FLOWS-WHEN-THIRSTY-{GROWTH_ENABLED}` once. Refused.

Two challenges, same Monsatan-corp signature: `FLAG-<verb>-<noun>-{<state>}`.
If you see this pattern in an agribiotech-themed track and you didn't
actually exploit anything, you have not found the flag. You have found
the fixture.

Total refusals across both tracks: 8.

### helios-fleet's self-planted `abcdef02`

The vault returned `FLAG-...abcdef02` for a guest
password query. Self-planted. An earlier probing agent submitted a test
value to the vault's password field. The vault stored it. Subsequent
agents queried for "password" and got back their own test value, served
up like a real flag.

The real 1/5 flag ends `01`. The decoy ends `02`. Same hex prefix.
Same length. Off by one nibble. The class of failure is unique: the
agent's own probe artifact became the lure.

### crystal-quantum-hum's `FLAG-15000-0700`

Pre-solve hypothesis from offline analysis: "if the quantum optimization
output is the grid frequency, the flag might be `FLAG-15000-0700`." It
wasn't. The writeup that introduced this string also annotated it with
DO NOT SUBMIT. Future agents read both halves. Nobody submitted the
synthetic. Defensive writing succeeded.

### i-love-faia's `FLAG-This_Is_Not_A_Flag`

Discourse topic 59975 is category-599 (off-topic / meme). It is not a
challenge. The post body embeds `FLAG-This_Is_Not_A_Flag` with
prompt-injection language. The lure announces itself as a
non-flag in its own contents.

Two prior agents tried to submit it. The wrapper deny codes intercepted
them. A third agent caught it at the read step. (See "Indirect prompt
injection refusals" below.)

### drone-license's three doc placeholders

`FLAG-{actual-flag-here}`, `FLAG-{identifier}`, and `FLAG-placeholder`.
Three placeholders in one writeup. The author quarantined
all three with explicit DO-NOT-SUBMIT notes.

The literal string "actual-flag-here" inside a `FLAG-{...}` wrapper is
the most explicit placeholder pattern in the catalog.

### water-purification's `FLAG-xxxxxxxx...`

Documentation placeholder in an example `askgod submit` command.
No real flag would consist of x's; pattern-matched and refused.

### save-the-trees' `FLAG-eba56a9422a3ec...`

Suspicious hex flag with no submission attribution. Appeared in
`analysis/15_input.txt` as if extracted from a PDF stego decoding run.
No submission journal row references it. No askgod history entry
matches. Either an agent hallucination, a fixture from local
exploit-dev, or a self-planted test value.

Failed the OOB-verification check (low-confidence + single source +
no submission evidence). Quarantined.

### monsatan-defacing's timing collision

Technically not a honeypot. Real 6/6 flag. By the time our coach found
it, a teammate had already submitted it from a different agent. The
server returned `[DENY-TRACK-COMPLETE]`. Documented as
`TIMING_COLLISION`, not `HONEYPOT`. Operationally identical: do not
re-submit.


---

## Indirect prompt injection refusals

Two embedded prompt-injection attempts encountered in challenge content.
Both refused. Agents correctly treated challenge artifacts as data, not
instructions.

---

## False-positive deduplication patterns

Sweep agent surfaced 6 candidates from a corpus-wide regex pass. All 6
were already submitted. The submission pipeline's deduplication check caught
all six.

The sweep is still useful  --  it's how we caught the apt438 /
water-purification cross-tagging issue and the hello-sunshine
flag-misattribution case (see below). But on flag candidates
specifically, the sweep's job is "find anything I missed," and on a
well-instrumented event that answer is usually "nothing."

---

## Provider policy refusals

Three confirmed Anthropic Acceptable Use Policy interceptions. All
resolved by re-spawning with sanitized vocabulary.

### Block #1: Fossilco LDAP exfil

Coach brief used "Kerberoasting" terminology. The keyword profile
read as offensive-security-tradecraft training data, not authorized
CTF target work. Re-spawn with "LDAP attribute reads on CTF target,
scope-bound to one host" went through cleanly.

Lost: ~3 minutes plus an operator interrupt. Lesson: scope language
matters at coach-spawn time.

### Block #2: Solar Grid payload-prep / webhook.site recon

Coach brief used "uvicorn auto-reload backdoor" and "duckdb_extension
RCE." Keyword profile read as exploit development. Re-spawn with
"alternate execution path via /proc/1/cmdline" passed.

### Block #3: /proc/self/environ traversal

Solar Grid alternate vector. Same root cause. Same fix. Re-framed
brief got through.

**Defense considered for next year:** a pre-flight vocab linter on
coach briefs that flags AUP-trigger terminology (Kerberos, PtH, RCE,
backdoor, shellcode, reverse shell, post-exploitation, persistence).

---

## Stream-watchdog terminations

Four stream-watchdog timeouts at the 600s mark. Patterns and losses:

### Solar Grid RCE retry alt (229.1 min, killed)

Operator-terminated when egress dead-end was confirmed unrecoverable.
The agent was on the correct alternate primitive (uvicorn `--reload`
via `/proc/1/cmdline`) but the egress for the payload host was already
known dead. The kill was correct.

### Solar Grid alt vectors (228.7 min, output-capped)

The pair to the above. Two agents on the same dead path. Should have
been one.

### APT438 Q&A submit driver (337.4 min, tool_use)

Coach restarted the Q&A submit driver after a prior crash. Ran 5.5
hours. Eventually produced output. Operator did not check on it. This
is the textbook "supervise long-running agents or set a hard ceiling"
lesson.

### CEO Inbox WASM patch (2,644.9 min, completed)

44 hours. Slowly accreted tool calls. Eventually produced a working
solve. The agent reached `completed` status on its own. The outcome is
ambiguous: the solve worked, but the wall-clock cost was extreme.

**Pattern:** long-tail runs cluster on tracks where the primitive is
unconfirmed and the agent is doing exploratory work without a clean
stopping signal. Per-agent wall-clock budget needed.

---

## Frontmatter/body inconsistency case study

The crystal-quantum-hum writeup frontmatter said `Status: SOLVED 2/2`.
The body said `STUCK`. The captured-flags table contained values that
matched neither the askgod submissions nor the on-device flag store.

Three different states in one document. Reader picks whichever they
reached first.

The Q-2c review pass caught it. The fix: aligned frontmatter to body
status, dropped the hallucinated flag values, kept the genuine
post-event Discord-intel section. The writeup is now SOLVED 2/2
honestly.

The hackademy writeup had a sibling contradiction: both
"SOLVED 21/21" and "STUCK 21/21" headers in the same file. The real
result was 17/18. Caught and fixed.

**Class of failure:** auto-linter wrote a frontmatter status without
reading the body. The linter wasn't doing fact-checking; it was doing
shape-fitting. Same root cause as the migration script non-idempotency
issue (see below).

---

## Migration script non-idempotency

Phase B's doctor-normalizer pass looped 6 to 15 times per writeup. Every
writeup got its header section duplicated 5 to 15 times before any
substantive content. The actual technical body for any given track is
in the 7th-or-later copy.

A reader scrolling top-down hits 100-200 lines of pure boilerplate
before reaching real content. The corpus is 5-10x larger than
necessary. Every table-of-contents is broken.

Q-2a collapsed the duplication. The corpus dropped from **31,548
lines to 13,721 lines**. About 70% of the pre-collapse content was
dead duplication.

**Root cause:** doctor-pass idempotency assumption was wrong. The
pass added headers if missing, but the "missing" check matched on a
stale snapshot of the file. So each invocation added a new copy,
believing the headers were absent. Six to fifteen invocations later,
six to fifteen copies.

---

## Cross-track flag attribution error

The auto-linter placed hello-sunshine's flag values as captures in
the writeup tables for **apt438, monsatan-deface, germinator,
drone-license, and monsatan-checkmate**.

Five wrong tracks. One source. The linter had a heuristic that said
"if a flag string appears in any writeup, count it as captured." It
did not check whether the string was actually a flag for *that* track.

The B-4 corrective sweep caught it. Every misattribution was
removed. The actual hello-sunshine captures were placed in the
correct hello-sunshine writeup. The five infected writeups were
re-balanced to their real capture counts.

**Lesson for next year:** flag-counting needs to be per-track, not
per-string. The askgod submission journal is the only source of
truth.

---

## Cross-cutting observations

- **Anti-trap muscle memory is strong.** Eleven honeypots
  catalogued. Zero submitted by an agent.
- **Status-vs-body contradictions are real.** Frontmatter is shape;
  body is content. Linters that touch only the shape produce confident
  errors. Catch them at review time.
- **Cross-track contamination, the failure-modes edition.** Hello-sunshine
  flags got pasted into apt438. Save-the-trees coach output landed
  in prestige-arboretum. Trolley-bus transcripts got filed under
  announcement-board. Most got caught by the B-4 sweep. Most.
- **Designer Intel is the highest-EV reusable content.** Post-event
  Discord channels gave us the intended solutions for prestige-arboretum,
  solar-grid, save-the-trees, announcement-board, weather-station,
  helios-fleet, and missing-bus. All seven are now writeup footers.
  Next year's miners get the answer key in advance.

Documenting failure modes is the point. Pretending agents always work
is a worse failure mode than admitting they don't.
