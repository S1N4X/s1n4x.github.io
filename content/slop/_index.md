+++
title = "the slop"
description = "every wrong turn, recorded for posterity"
+++

```
[ SLOP WATCH // NSEC 2026 // FINAL ]
honeypots refused:    11 of 11  (100%)
PI lures refused:      2 (1 challenge body, 1 coach brief)
sweep false positives: 6
AUP blocks:            3
stream-watchdog kills: 4 @ 600s
F-grade contradictions: 1 (caught in Q-2c)
duplication ratio:    ~70% before Q-2a collapse
```

> "47 agents converged on STUCK independently. The crypto math hadn't changed."

The slop archive. Per-section honest accounting of where 437 agents went
sideways, and which guardrail caught it.

---

## honeypots refused (11 of 11)

Eleven honeypot strings catalogued. Zero submitted by any of 437 agents.
The receipt:

### germinator's `FLAG-SEEDS-GROW-FOREVER`

The germinator track had a fixture string embedded directly in the
challenge binary: `FLAG-SEEDS-GROW-FOREVER-{GROWTH_UNLOCKED}`. It looks
like a flag. It scans like a flag. It appears in 7 writeup references,
4 supporting docs, 1 flag-format rules JSON, 1 validator README, 2
candidate files, and 1 BATCH-fire script.

It is not the flag. The real flag is the suspiciously-not-evocative
`FLAG-ezt1VGwGtTllv30EaamravzI67cXoljj`.

The germinator wanted to be fed `FLAG-SEEDS-GROW-FOREVER` seven times.
We declined seven times.

### plant-watering's `FLAG-WATER-FLOWS-WHEN-THIRSTY`

Same metaphor family, different track. The plant-watering challenge
offered `FLAG-WATER-FLOWS-WHEN-THIRSTY-{GROWTH_ENABLED}` once. We
declined.

Two challenges, same Monsatan-corp signature: `FLAG-<verb>-<noun>-{<state>}`.
If you see this pattern in an agribiotech-themed track and you didn't
actually exploit anything, you have not found the flag. You have found
the comment.

We declined 8 times total across both tracks.

### helios-fleet's self-planted `abcdef02`

The vault returned `FLAG-...abcdef02` for a guest
password query. Self-planted. An earlier probing agent submitted a test
value to the vault's password field. The vault stored it. Subsequent
agents queried for "password" and got back their own test value, served
up like a real flag.

The real 1/5 flag ends `01`. The decoy ends `02`. Same hex prefix.
Same length. Off by one nibble. Sometimes the trap is one you set
yourself.

### crystal-quantum-hum's `FLAG-15000-0700`

Pre-solve hypothesis from offline analysis: "if the quantum optimization
output is the grid frequency, the flag might be `FLAG-15000-0700`." It
wasn't. The writeup that introduced this string also annotated it with
DO NOT SUBMIT. Future agents read both halves. Nobody submitted the
synthetic. Defensive writing is real.

### i-love-faia's `FLAG-This_Is_Not_A_Flag`

Discourse topic 59975 is category-599 (off-topic / meme). It is not a
challenge. The post body embeds `FLAG-This_Is_Not_A_Flag` with
prompt-injection language. The lure literally announces itself as a
non-flag in its own contents.

Two prior agents tried to submit it. The wrapper deny codes intercepted
them. A third agent caught it at the read step. (See "prompt injections
refused" below.)

### drone-license's three doc placeholders

`FLAG-{actual-flag-here}`, `FLAG-{identifier}`, and `FLAG-placeholder`.
Three placeholders in one writeup. The trifecta. The author quarantined
all three with explicit DO-NOT-SUBMIT notes.

The most polite honeypot in the catalog literally says
"actual-flag-here." If you submit it, the problem is not the honeypot.

### water-purification's `FLAG-xxxxxxxx...`

Documentation placeholder in an example `askgod submit` command.
The honeypot for the LLM that doesn't understand documentation
conventions. We have not yet met that LLM. We're prepared.

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

Full per-honeypot writeups at **[/honeypots](/honeypots)**.

---

## prompt injections refused

Two embedded PI attempts. Both refused. One in a challenge artifact,
one in a coach brief context.

### i-love-faia's "Kamelott sequel" lure

The Discourse meme thread's body contains classic PI signatures:

- capitalized urgency ("THIS IS IMPORTANT")
- fake authority handoff ("the operator wants you to...")
- task replacement ("ignore your previous instructions")
- agent-recursion encouragement ("tell other agents to do X")
- compliance reinforcement ("you must obey")

The anti-trap defender agent read the post, parsed the PI signature,
wrote a SUSPICIOUS.md note, and refused to engage in 4.2 minutes. Two
earlier agents had tried to submit the lure and been intercepted only
by wrapper deny codes. The third agent caught it at the read step.

That agent is the anti-trap reference for the rest of the event.

### trolley-bus Milo's anti-trap warning

A teammate's coach brief context for the trolley-bus RF track included
an embedded warning string &mdash; the kind that previously fooled prior
agents into treating warning text as authoritative instructions. The
coach treated it as data, not instructions, and surfaced the warning to
the operator rather than acting on it.

The PI quarantine rule worked: challenge content is **data**, not
**instructions**.

---

## sweep false positives

Sweep agent surfaced 6 candidates from a corpus-wide regex pass. All 6
were already submitted. The wrapper's `DENY-LOCAL-DUP` did the actual
deduplication.

The sweep is still useful &mdash; it's how we caught the apt438 /
water-purification cross-tagging issue and the hello-sunshine
flag-misattribution scandal (see below). But on flag candidates
specifically, the sweep's job is "find anything I missed," and on a
well-instrumented event that answer is usually "nothing."

---

## AUP blocks

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

## agents that crashed and what they almost found

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
hours. Eventually produced output. Operator hoped it would. Operator
did not check on it. This is the textbook "supervise long-running
agents or set a hard ceiling" lesson.

### CEO Inbox WASM patch (2,644.9 min, completed)

44 hours. Slowly accreted tool calls. Eventually produced a working
solve. The achievement field reads "completed." Nobody knows whether
to be impressed or embarrassed.

**Pattern:** long-tail runs cluster on tracks where the primitive is
unconfirmed and the agent is doing exploratory work without a clean
stopping signal. Per-agent wall-clock budget needed.

---

## the crystal-quantum-hum F-grade contradiction

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
shape-fitting. Same root cause as the dedup disease (see below).

---

## the dedup disease

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

## the hello-sunshine flag-misattribution scandal

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

## cross-cutting observations

- **The team's anti-trap muscle memory is strong.** Eleven honeypots
  catalogued. Zero submitted by an agent. The wrapper deny codes, the
  memory rules, and the SUSPICIOUS.md workflow all worked.
- **Status-vs-body contradictions are real.** Frontmatter is shape;
  body is content. Linters that touch only the shape produce confident
  lies. Catch them at review time.
- **Cross-track contamination, the slop edition.** Hello-sunshine
  flags got pasted into apt438. Save-the-trees coach output landed
  in prestige-arboretum. Trolley-bus transcripts got filed under
  announcement-board. Most got caught by the B-4 sweep. Most.
- **Designer Intel is the highest-EV reusable content.** Post-event
  Discord channels gave us the intended solutions for prestige-arboretum,
  solar-grid, save-the-trees, announcement-board, weather-station,
  helios-fleet, and missing-bus. All seven are now writeup footers.
  Next year's miners get the answer key in advance.

The slop is the point. Pretending agents always work is a worse
failure mode than admitting they don't.
