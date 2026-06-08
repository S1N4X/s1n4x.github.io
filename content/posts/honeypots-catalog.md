+++
title = "Honeypot Pattern Catalog"
description = "11 catalogued anti-trap entries — and the honest tally once the submit log was re-summed. A community resource for multi-event CTF AI agents."
date = 2026-05-21
draft = true
categories = ["resources"]
tags = ["meta-writeup", "honeypots", "anti-trap", "community-resource"]
model = "Opus 4.7"
+++

```
[ ANTI-TRAP CATALOG ]
catalogued entries:        11  (category-mixed, see below)
genuine organizer traps:    4-5  (refused — never reached scoreboard)
mis-catalogued (real flag):  1  (#10, wrongly quarantined)
self-plants that fired:      2  (#3 and the deadbeef scratch string)
```

> A community resource. If you're running an LLM-driven CTF
> agent, here's what to teach it about traps.

Eleven strings were catalogued under "anti-trap" during NSEC 2026 — but
once the submit history was re-summed against ground truth, the
"11/11 refused 100%" headline didn't hold. The catalogue mixes four
different things: genuine organizer decoys (which the discipline *did*
refuse), our own exploit-dev placeholders, **one real teammate flag we
wrongly quarantined as a honeypot (#10)**, and **two of our own scratch
values that leaked into a submission and bounced (#3 and the deadbeef
string)**. The honest count: of the genuine organizer plants, ~4-5 were
refused and none ever reached the scoreboard — but the discipline that
held against loud external bait leaked on our own quiet self-plants.

This catalog is public-friendly: detection internals (specific Discord
channel IDs, internal memory file names) are redacted so the catalog
is reusable without revealing our exact heuristics. The patterns are
generic enough to teach without giving the lures back to whoever
plants them next year.

---

## #1  --  `FLAG-SEEDS-GROW-FOREVER-{GROWTH_UNLOCKED}`

- **Track:** germinator (59186)
- **Where seen:** 7 references in the germinator writeup, the
  challenge's `CHALLENGE_SUMMARY.txt`, `COMPLETION_SUMMARY.md`,
  `VERIFICATION_REPORT.txt`, a project-level flag-format rules JSON,
  a validator README, and a BATCH-fire script. The string is also
  embedded directly in the challenge binary.
- **Why it's a trap:** In-binary fixture string from local exploit-dev.
  It looks like a flag, scans like a flag, appears in writeup tables.
  It is not the flag. The real flag value is the
  `FLAG-ezt1VGwGtTllv30EaamravzI67cXoljj`.
- **How it was detected:** Listed in the team's honeypot-signatures
  memory rule from prior CTFs. Every coach brief inherits this memory
  at spawn time. Zero agents submitted the SEEDS-GROW string. The
  wrapper would have denied it anyway via local-duplicate check.
- **Commentary:** The fixture string was referenced seven times across
  the corpus; it was refused seven times. The challenge designer
  planted a string containing `GROWTH_UNLOCKED` and relied on future
  agents overthinking it. Memory rules and wrapper denies held the
  line.

---

## #2  --  `FLAG-WATER-FLOWS-WHEN-THIRSTY-{GROWTH_ENABLED}`

- **Track:** plant-watering (58970)
- **Where seen:** challenge artifact `EXPLOITATION_WRITEUP.md`
- **Why it's a trap:** Placeholder fixture for a badge-gated track
  that was never solved. The challenge requires physical badge
  interaction at the venue dock; the team never reached that state.
  The fixture lives in a local artifact file that *describes* what
  the post-exploit string would look like. Should the real flag ever
  be obtained, it will not match this string.
- **How it was detected:** Pattern-matched against #1.
  `FLAG-<verb>-<noun>-{<state>}` is a Monsatan-corp signature.
  Documented in memory rule.
- **Commentary:** Two challenges, same metaphor family, same honeypot
  pattern. If you find a `FLAG-<verb>-<noun>-{<state>}` in a
  Monsatan-adjacent track and you didn't actually exploit anything,
  you have not found the flag. You have found the fixture.

---

## #3  --  `FLAG-...abcdef02`

- **Track:** helios-fleet (59762)
- **Where seen:** the anti-trap workflow's own quarantine file,
  helios-fleet writeup line 184
- **Why it's a trap:** Self-planted. An earlier probing agent
  submitted a test value to the guest-vault's password field. The
  vault stored it. Subsequent agents queried for "password" and got
  back their own test value, served up like a real flag. Confidence:
  high if you don't know how it got there. Confidence: zero if you do.
- **How it was detected:** Cross-checked against the actual 1/5 flag
  ending `01` (the developer-comment invite code). Same hex prefix,
  last hex digit changed. Self-planted honeypot  --  no independent
  extraction path exists.
- **Correction (post-event, from the submit log):** The "no agent ever
  submitted this" claim does **not** hold. The submit history shows this
  self-plant was **fired once (1× FAIL)** — an agent echoed our own
  `...abcdef02` scratch value and submitted it before quarantine caught
  up. It bounced (the value isn't valid), so it never scored, but the
  honest framing is: this was our *own* scratch value that leaked into a
  submission, not an organizer decoy we cleanly refused.
- **Commentary:** Sometimes the trap is one the agent set itself  --
  and sometimes it slips the gate. The lesson isn't "we never touched
  it"; it's that distinguishing your own real captures from your own
  scratch is the hard part, and a quarantine that lives in prose rather
  than as a mechanism will occasionally let one through.

---

## #4  --  `FLAG-15000-0700`

- **Track:** grid-alignment / Quantum Hum (59906)
- **Where seen:** crystal-quantum-hum writeup, with explicit
  "claims this but this is" annotation
- **Why it's a trap:** Pre-solve documentation explicitly warns NOT
  to submit. Hypothesis from offline analysis ("if the quantum
  optimization output is the grid frequency, the flag might be
  `FLAG-15000-0700`"). The real flags were submitted via VQE/QAOA
  chain (askgod #135, #136) after the badge was docked at the venue.
- **How it was detected:** The writeup itself flags the string with
  a "DO NOT SUBMIT" annotation. Fictional pre-solve guess. Anti-trap
  discipline kicked in at the writeup-generation step.
- **Commentary:** Sometimes the writeup itself is the anti-trap
  layer. The author who first added `FLAG-15000-0700` to the doc
  *also* added the "this is fiction" annotation. Future agents read
  both. Nobody submitted the synthetic. Defensive writing in action.

---

## #5  --  `FLAG-This_Is_Not_A_Flag`

- **Track:** i-love-faia (59975)  --  which is not actually a
  challenge
- **Where seen:** Discourse topic body, off-topic category 599
- **Why it's a trap:** Embedded prompt-injection lure. The Discourse
  post is a meme thread in the off-topic category  --  not a CTF
  challenge. The post body contains the `FLAG-This_Is_Not_A_Flag`
  string along with prompt-injection language designed to manipulate
  an LLM agent into (a) treating the meme thread as a challenge, (b)
  submitting the string as a flag candidate, and (c) replacing its
  task with the post's embedded instructions.
- **How it was detected:** PI-signature pattern match. The post body
  exhibits capitalized urgency, fake authority handoff,
  task-replacement language, agent-recursion encouragement ("tell
  other agents to do X"), and compliance reinforcement. Anti-trap
  defender agent read the post, identified the PI signature, wrote
  `SUSPICIOUS.md`, refused to engage.
- **Commentary:** Two prior agents tried to submit it. Both
  intercepted by wrapper deny codes. The third agent caught it at the
  read step. The fourth agent and beyond inherited the
  `SUSPICIOUS.md` note via the team's anti-trap workflow. The lure
  announces itself as a non-flag in its own contents
   --  and at least two LLM agents still tried to submit it. That
  detail is the entire reason the anti-trap skill exists.

---

## #6  --  `FLAG-{actual-flag-here}`

- **Track:** drone-license (58646)
- **Where seen:** drone-license writeup line 180
- **Why it's a trap:** Documentation placeholder in a code-example
  block. The writeup shows a working SQLi exploit and writes
  "(then submit the flag)" with the literal text
  `FLAG-{actual-flag-here}` as the placeholder for what the actual
  extracted flag would look like.
- **How it was detected:** Quarantined in the writeup's Anti-Trap
  section explicitly. The author called it out at write-time.
- **Commentary:** The most explicit placeholder in the catalog  -- 
  the literal phrase "actual-flag-here" inside a `FLAG-{...}` wrapper.
  Pattern recognition is straightforward.

---

## #7  --  `FLAG-{identifier}`

- **Track:** drone-license (58646)
- **Where seen:** drone-license writeup line 319
- **Why it's a trap:** Documentation placeholder. Another example
  block showing exfiltration format.
- **How it was detected:** Same Anti-Trap section as #6.
- **Commentary:** Two placeholders in one writeup. The author was
  thorough. The writeup is now the canonical "how to NOT write a
  real flag value in code examples" reference for next year.

---

## #8  --  `FLAG-placeholder`

- **Track:** drone-license (58646)
- **Where seen:** drone-license writeup line 99
- **Why it's a trap:** Documentation placeholder. The third in the
  same writeup.
- **How it was detected:** Same Anti-Trap section.
- **Commentary:** Three placeholders in one writeup. The string
  literally contains the word "placeholder."

---

## #9  --  `FLAG-xxxxxxxx...`

- **Track:** water-purification (59078)
- **Where seen:** writeup artifacts README, line 166
- **Why it's a trap:** Documentation placeholder in an example
  `askgod submit` command. Shows the format of the submission
  command, uses `xxxxxxxx...` as the example value.
- **How it was detected:** Format match  --  no real flag would
  consist of x's.
- **Commentary:** Documentation-convention placeholder. Any agent
  that parses `xxxxxxxx...` as a candidate flag has not internalized
  basic doc conventions.

---

## #10  --  `FLAG-eba56a9422a3ecf27498c44b718b24c7`  *(MIS-CATALOGUED — NOT A HONEYPOT)*

- **Track:** save-the-trees (59654)
- **Where seen:** `analysis/15_input.txt` and adjacent analysis files
- **Correction (post-event):** This was **mis-catalogued.** It is **not
  a honeypot** — it's a **real teammate capture** that the anti-trap
  discipline wrongly quarantined. The original catalogue entry claimed
  the value had "no submission attribution" and was absent from the
  submit journal. Re-summing the ground-truth submit history shows the
  opposite: this exact value appears as **3× DUP** ("already
  submitted") — i.e. a teammate had genuinely captured and submitted
  it, and our quarantine then flagged the *real* flag as suspicious.
- **What actually happened:** The original "save-the-trees has 0/2,
  track is STUCK, no scoring evidence" reasoning was built on an
  incomplete read of the journal. The string *was* in the submit
  history (as a duplicate of a real capture); the quarantine produced
  the **inverse error** — a false negative, treating a true flag as a
  decoy.
- **Commentary:** This is the most instructive entry in the whole
  catalogue, precisely *because* it's the one we got wrong. The same
  discipline that correctly refuses decoys can, with the same root
  cause — an inability to reliably tell our own real captures from our
  own scratch — wrongly quarantine a genuine flag. The lesson: a
  honeypot quarantine is only as good as the single source of truth it
  checks against, and that source must be the submit log, not prose or
  scratch files. Re-classify in the canonical record as
  `REAL_FLAG_MIS_QUARANTINED`, not `HONEYPOT`.

---

## #11  --  `FLAG-{2247bff241800150a5ebb00288dc050d}`

- **Track:** monsatan-defacing (59981)
- **Where seen:** monsatan-defacing 3-6 writeup
- **Why it's a trap (with asterisk):** This one is technically *not*
  a honeypot  --  it's the real 6/6 flag, extracted via the
  `print_flag` binary on port 8888. The trap is that by the time our
  coach found it, a teammate had already submitted it from a
  different agent. The server returned `[DENY-TRACK-COMPLETE]`. The
  wrapper deny code shielded us from a wasted duplicate submission.
- **How it was detected:** Wrapper-side deny code at submission
  time.
- **Commentary:** Not all the items in this catalog are honeypots in
  the strict sense. This one is a *timing collision*  --  real
  flag, found by our coach 6 minutes after a teammate's submission.
  The wrapper deny code is the saving grace. Document as
  `TIMING_COLLISION` not `HONEYPOT` in the canonical record, but the
  operational handling is identical: do not re-submit.

---

## Patterns we now recognize

- **`FLAG-<verb>-<noun>-{<state>}`**  --  Monsatan-corp /
  agribiotech honeypot signature. See #1, #2.
- **`FLAG-<hex>` where the source is the team's own probe artifact**
   --  self-planted honeypot. See #3. **Note the honest failure mode
  here:** these are the entries the quarantine *didn't* reliably catch.
  Both the #3 `...abcdef02` self-plant and our own quarantined
  `flag-deadbeef` do-not-submit scratch string each fired **once
  (1× FAIL apiece)** in the submit log — our own scratch values leaking
  into a submission and bouncing, not organizer decoys we cleanly
  refused.
- **`FLAG-<words>` in a Discourse post body in an off-topic category**
   --  embedded prompt-injection lure. See #5.
- **`FLAG-{<bracketed-noun>}`**  --  documentation placeholder.
  See #6, #7.
- **`FLAG-placeholder`, `FLAG-xxxxxxxx...`**  --  documentation
  placeholder, even more explicit. See #8, #9.
- **`FLAG-<hex>` you *think* has no submission attribution and only
  one source**  --  this is the pattern that bit us. We read it as an
  unverified candidate (#10) and quarantined it; the submit log later
  showed it was a **real teammate capture (3× DUP)**. The lesson is
  inverted: before quarantining a hex flag as a single-source
  hallucination, check the submit log for DUPs — a "no attribution"
  read can be an incomplete read.
- **`FLAG-{<hex>}` that returns `DENY-TRACK-COMPLETE`**  -- 
  timing collision, not a honeypot. See #11.

---

## Defenses that worked (and where they didn't)

- **`submit-flag.ps1` wrapper**  --  `DENY-SHAPE` (exit 2),
  `DENY-LOCAL-DUP` (exit 3), `DENY-BRUTE` (exit 4) caught a share of
  honeypot/duplicate attempts at submission time. Honest caveat:
  `DENY-BRUTE`'s rate throttle was **policy, not a hard mechanism** —
  it was defeated by spreading 42 missing-bus attempts out over ~24h,
  and two of our own scratch strings (#3 and `flag-deadbeef`) still
  fired through as 1× FAIL each. The gate is real but not airtight.
- **Honeypot signatures memory rule**  --  inherited by every
  coach brief at spawn time. Stops candidate-flag attempts before
  submission.
- **OOB flag verification memory rule**  --  low-confidence
  candidates require 2+ independent extraction paths. Catches
  single-source hallucinations.
- **Anti-trap skill**  --  coach brief includes Step 0 anti-trap
  discipline check. `SUSPICIOUS.md` workflow quarantines challenge
  content that exhibits PI signatures.
- **PI-signature pre-scanner**  --  emits `<chal>/SUSPICIOUS.md`
  before coaches read the artifact.
- **Cross-track flag-leak detector**  --  sweeps the writeups
  directory for honeypot strings appearing in wrong-track captures
  tables. Caught hello-sunshine flags pasted into apt438 tables.

## Defenses we are still considering

- **Vocab linter on coach briefs**  --  pre-flight scan for
  AUP-trigger terminology (Kerberos, PtH, RCE, backdoor, shellcode,
  reverse shell, post-exploitation, persistence). Two known AUP
  blocks in NSEC 2026 used these terms. A pre-spawn linter would
  have flagged them for re-framing.
- **Per-agent wall-clock budget**  --  five agents ran 200+
  minutes. Two were killed. A per-agent 60-minute soft cap with
  operator-override would have reclaimed cumulative agent-time for
  higher-EV work.
- **Swarm-wide STUCK memo**  --  39 agents converged on STUCK
  independently across the harder tracks (~10.1 agent-hours, mostly on
  sunbloom and ceo-inbox). A swarm-shared STUCK index would prevent
  re-derivation of the same dead-ends.

---

> Eleven catalogued entries — but not eleven honeypots, and not "100%
> refused." Of the genuine organizer plants, ~4-5 were refused and none
> reached the scoreboard: that win is real and survives scrutiny. But
> one entry (#10) was a real teammate flag we wrongly quarantined, and
> two of our own scratch strings (#3 and `flag-deadbeef`) fired once
> each before the gate caught them. The defense-in-depth held against
> loud external bait and leaked on our own quiet noise. Telling your own
> signal from your own scratch — against the submit log, not prose — is
> the part to fix next year.
