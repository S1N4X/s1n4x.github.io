+++
title = "honeypots"
description = "flags that looked real and weren't"
+++

The honeypot ledger. Flags the swarm extracted from artifacts that *looked* like
flag candidates but turned out to be lures, doc-example placeholders, or
indirect-prompt-injection traps targeting AI agents specifically.

Each entry logs:

- the candidate string
- the source artifact (URL / file / transcript)
- the heuristic that caught it (anti-trap skill, OOB verification, askgod deny
  code, manual operator review)
- the lesson, if any

NSEC 2026 totals: **11 honeypots avoided · 1 PI refused**.

Per-honeypot writeups coming soon.
