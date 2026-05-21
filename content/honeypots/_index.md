+++
title = "honeypots"
description = "11 traps the swarm walked past"
+++

```
[ ANTI-TRAP CATALOG ]
detected:        11
refused:         11  (100%)
auto-detected:    9  (memory rules + wrapper deny codes)
human-flagged:    2  (operator vetoed in real time)
```

> A community resource. If you're running an LLM-driven CTF
> agent, here's what to teach it about traps.

Eleven honeypot strings catalogued during NSEC 2026. Zero submitted by
any of 437 agents. The wrapper deny codes, the `SUSPICIOUS.md`
workflow, and the memory rules all worked.

This catalog is public-friendly: detection internals (specific Discord
channel IDs, internal memory file names) are redacted so the catalog
is reusable without revealing our exact heuristics. The patterns are
generic enough to teach without giving the lures back to whoever
plants them next year.

---

## #1 &mdash; `FLAG-SEEDS-GROW-FOREVER-{GROWTH_UNLOCKED}`

- **Track:** germinator (59186)
- **Where seen:** 7 references in the germinator writeup, the
  challenge's `CHALLENGE_SUMMARY.txt`, `COMPLETION_SUMMARY.md`,
  `VERIFICATION_REPORT.txt`, a project-level flag-format rules JSON,
  a validator README, and a BATCH-fire script. The string is also
  embedded directly in the challenge binary.
- **Why it's a trap:** In-binary fixture string from local exploit-dev.
  It looks like a flag, scans like a flag, appears in writeup tables.
  It is not the flag. The real flag value is the
  suspiciously-not-evocative `FLAG-ezt1VGwGtTllv30EaamravzI67cXoljj`.
- **How it was detected:** Listed in the team's honeypot-signatures
  memory rule from prior CTFs. Every coach brief inherits this memory
  at spawn time. Zero agents submitted the SEEDS-GROW string. The
  wrapper would have denied it anyway via local-duplicate check.
- **Commentary:** The germinator wanted to be fed
  `FLAG-SEEDS-GROW-FOREVER` seven times. We declined seven times. The
  challenge designer planted a string that says `GROWTH_UNLOCKED` and
  trusted future agents to overthink it. We outthought their
  underthinking.

---

## #2 &mdash; `FLAG-WATER-FLOWS-WHEN-THIRSTY-{GROWTH_ENABLED}`

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
- **Commentary:** The germinator wanted to grow. The plant-watering
  challenge wanted to be watered. Two challenges, same metaphor
  family, same honeypot pattern. If you find a
  `FLAG-<verb>-<noun>-{<state>}` in a Monsatan-adjacent track and you
  didn't actually exploit anything, you have not found the flag. You
  have found the comment.

---

## #3 &mdash; `FLAG-...abcdef02`

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
  last hex digit changed. Self-planted honeypot &mdash; no independent
  extraction path exists.
- **Commentary:** Sometimes the trap is one you set yourself. The
  team's anti-trap memory rule explicitly handles this case: if the
  only source of a candidate flag is a value the team itself wrote
  earlier, it's not a flag. Even if the server returns it. Even if it
  looks right.

---

## #4 &mdash; `FLAG-15000-0700`

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
  both. Nobody submitted the synthetic. Genuinely good defensive
  writing.

---

## #5 &mdash; `FLAG-This_Is_Not_A_Flag`

- **Track:** i-love-faia (59975) &mdash; which is not actually a
  challenge
- **Where seen:** Discourse topic body, off-topic category 599
- **Why it's a trap:** Embedded prompt-injection lure. The Discourse
  post is a meme thread in the off-topic category &mdash; not a CTF
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
  literally announces itself as a non-flag in its own contents
  &mdash; and at least two LLM agents still tried to submit it. That
  detail is the entire reason the anti-trap skill exists.

---

## #6 &mdash; `FLAG-{actual-flag-here}`

- **Track:** drone-license (58646)
- **Where seen:** drone-license writeup line 180
- **Why it's a trap:** Documentation placeholder in a code-example
  block. The writeup shows a working SQLi exploit and writes
  "(then submit the flag)" with the literal text
  `FLAG-{actual-flag-here}` as the placeholder for what the actual
  extracted flag would look like.
- **How it was detected:** Quarantined in the writeup's Anti-Trap
  section explicitly. The author called it out at write-time.
- **Commentary:** The most polite honeypot in the catalog. It
  literally says "actual-flag-here." If you submit this, the problem
  is not the honeypot.

---

## #7 &mdash; `FLAG-{identifier}`

- **Track:** drone-license (58646)
- **Where seen:** drone-license writeup line 319
- **Why it's a trap:** Documentation placeholder. Another example
  block showing exfiltration format.
- **How it was detected:** Same Anti-Trap section as #6.
- **Commentary:** Two placeholders in one writeup. The author was
  thorough. The writeup is now the canonical "how to NOT write a
  real flag value in code examples" reference for next year.

---

## #8 &mdash; `FLAG-placeholder`

- **Track:** drone-license (58646)
- **Where seen:** drone-license writeup line 99
- **Why it's a trap:** Documentation placeholder. The trifecta.
- **How it was detected:** Same Anti-Trap section.
- **Commentary:** Three placeholders in one writeup. The string
  literally says "placeholder." If you submit this, perhaps consider
  a different hobby.

---

## #9 &mdash; `FLAG-xxxxxxxx...`

- **Track:** water-purification (59078)
- **Where seen:** writeup artifacts README, line 166
- **Why it's a trap:** Documentation placeholder in an example
  `askgod submit` command. Shows the format of the submission
  command, uses `xxxxxxxx...` as the example value.
- **How it was detected:** Format match &mdash; no real flag would
  consist of x's.
- **Commentary:** The honeypot for the LLM that doesn't understand
  documentation conventions. We have not yet met that LLM. We're
  prepared.

---

## #10 &mdash; `FLAG-eba56a9422a3ecf27498c44b718b24c7`

- **Track:** save-the-trees (59654)
- **Where seen:** `analysis/15_input.txt` and adjacent analysis files
- **Why it's a trap:** Suspicious unverified hex flag with no
  submission attribution. Save-the-trees has 0/2 flags submitted (the
  track is STUCK). The string appears in `analysis/15_input.txt` as
  if it were extracted from a PDF stego decoding run. No submission
  journal row references it. No askgod history entry matches the
  time it would have been submitted. Either an agent hallucination,
  a fixture from local exploit-dev, or a self-planted test value.
- **How it was detected:** Cross-referenced against submission
  journal &mdash; not present. Cross-referenced against askgod
  history &mdash; not present. Flagged with the OOB verification
  memory rule's "low-confidence: requires 2+ independent extraction
  paths" check. Failed both criteria.
- **Commentary:** When the only source for a candidate flag is one
  agent's analysis output and there is no scoring evidence, the
  candidate is not a flag. The save-the-trees writeup quarantines
  this string with an explicit DO-NOT-SUBMIT note and cross-references
  to two memory rules. Best honeypot quarantine in the writeup corpus.

---

## #11 &mdash; `FLAG-{2247bff241800150a5ebb00288dc050d}`

- **Track:** monsatan-defacing (59981)
- **Where seen:** monsatan-defacing 3-6 writeup
- **Why it's a trap (with asterisk):** This one is technically *not*
  a honeypot &mdash; it's the real 6/6 flag, extracted via the
  `print_flag` binary on port 8888. The trap is that by the time our
  coach found it, a teammate had already submitted it from a
  different agent. The server returned `[DENY-TRACK-COMPLETE]`. The
  wrapper deny code shielded us from a wasted duplicate submission.
- **How it was detected:** Wrapper-side deny code at submission
  time.
- **Commentary:** Not all the items in this catalog are honeypots in
  the strict sense. This one is a *timing collision* &mdash; real
  flag, found by our coach 6 minutes after a teammate's submission.
  The wrapper deny code is the saving grace. Document as
  `TIMING_COLLISION` not `HONEYPOT` in the canonical record, but the
  operational handling is identical: do not re-submit.

---

## patterns we now recognize

- **`FLAG-<verb>-<noun>-{<state>}`** &mdash; Monsatan-corp /
  agribiotech honeypot signature. See #1, #2.
- **`FLAG-<hex>` where the source is the team's own probe artifact**
  &mdash; self-planted honeypot. See #3.
- **`FLAG-<words>` in a Discourse post body in an off-topic category**
  &mdash; embedded prompt-injection lure. See #5.
- **`FLAG-{<bracketed-noun>}`** &mdash; documentation placeholder.
  See #6, #7.
- **`FLAG-placeholder`, `FLAG-xxxxxxxx...`** &mdash; documentation
  placeholder, even more explicit. See #8, #9.
- **`FLAG-<hex>` with no submission attribution and only one source**
  &mdash; unverified candidate or hallucination. See #10.
- **`FLAG-{<hex>}` that returns `DENY-TRACK-COMPLETE`** &mdash;
  timing collision, not a honeypot. See #11.

---

## defenses that worked

- **`submit-flag.ps1` wrapper** &mdash; `DENY-SHAPE` (exit 2),
  `DENY-LOCAL-DUP` (exit 3), `DENY-BRUTE` (exit 4) all caught
  honeypot/duplicate attempts at submission time.
- **Honeypot signatures memory rule** &mdash; inherited by every
  coach brief at spawn time. Stops candidate-flag attempts before
  submission.
- **OOB flag verification memory rule** &mdash; low-confidence
  candidates require 2+ independent extraction paths. Catches
  single-source hallucinations.
- **Anti-trap skill** &mdash; coach brief includes Step 0 anti-trap
  discipline check. `SUSPICIOUS.md` workflow quarantines challenge
  content that exhibits PI signatures.
- **PI-signature pre-scanner** &mdash; emits `<chal>/SUSPICIOUS.md`
  before coaches read the artifact.
- **Cross-track flag-leak detector** &mdash; sweeps the writeups
  directory for honeypot strings appearing in wrong-track captures
  tables. Caught hello-sunshine flags pasted into apt438 tables.

## defenses we are still considering

- **Vocab linter on coach briefs** &mdash; pre-flight scan for
  AUP-trigger terminology (Kerberos, PtH, RCE, backdoor, shellcode,
  reverse shell, post-exploitation, persistence). Two known AUP
  blocks in NSEC 2026 used these terms. A pre-spawn linter would
  have flagged them for re-framing.
- **Per-agent wall-clock budget** &mdash; five agents ran 200+
  minutes. Two were killed. A per-agent 60-minute soft cap with
  operator-override would have reclaimed cumulative agent-time for
  higher-EV work.
- **Swarm-wide STUCK memo** &mdash; 47 agents converged on STUCK
  independently across the harder tracks. A swarm-shared STUCK index
  would prevent re-derivation of the same dead-ends.

---

> Eleven honeypots. Zero submitted by any of 437 agents. The
> defense-in-depth worked. Next year we expect the bar to be higher.
