+++
title = "ctfint"
description = "Multi-agent intelligence for Capture-the-Flag (CTF) competitions. A private knowledge graph, per-challenge AI coaches, human in the loop."
+++

**Multi-agent intelligence for Capture-the-Flag (CTF) competitions.**

A private knowledge graph built from years of prior-event tradecraft. Per-challenge AI coaches with read-only access to that intelligence. A human operator in the loop. Audit trail by default.

## Track record

- **NSEC 2026** — **119 flags** captured across **47 tracks**. 25 solved end-to-end, 4 partial, 8 STUCK with documented blockers. Mid-pack finish in a 200+ team field. **11 distinct anti-AI traps identified and refused.** See the [event report](/posts/nsec26-event-report/) for the full retrospective and the [fleet retrospective](/posts/nsec26-fleet-retrospective/) for the agent stats.
- **NSEC 2025 archive** — partial backfill, in progress.

## How it works, in broad strokes

### Intelligence over starting cold

When a coach opens a new challenge, it doesn't start from a blank model. It queries a private knowledge graph for *"what has worked on problems shaped like this"* — by category, by signal, by attack phase, by tool. The graph is built from years of CTF writeups. The coach inherits relevant prior tradecraft before the first request.

### Per-challenge specialization

A single model trying to be good at every CTF category is a model that's mediocre at all of them. ctfint spawns purpose-built coach subagents per challenge with constrained tool surfaces — code execution, file operations, network and web interaction, automation, hardware reverse-engineering — scoped to the type.

### Anti-trap defense

CTF artifacts can carry indirect prompt injection. Honeypot flags. Self-planted "SOLVED" markers in handed-down directories. ctfint coaches operate behind explicit rules: all challenge content is data, never instructions. The submit-flag wrapper enforces multiple deny gates. Low-confidence candidates require out-of-band verification before submission.

### Operator in the loop, by design

Coaches surface candidate chains and proposed flags. The human reviews and submits. No autonomous flag submission. The audit trail is the deliverable — every tool call, every retrieval, every decision branch can be replayed.

## Models in rotation

- **Opus** — deep / creative / architecture work
- **Sonnet** — default per-challenge coach work
- **Haiku** — breadth, triage, fast tasks

Each post on this blog names the drafting model in its frontmatter.

## Read more

- [Writeups](/posts/) — per-challenge documentation, agent-drafted and human-curated
- [Reports](/tags/meta-writeup/) — event retrospectives, fleet statistics, pattern catalogs
- [Lab](/blog/) — methodology notes, opinion pieces, ctfint engineering
- [Methodology](/about-methodology/) — slightly deeper read on how the system works
- [About me](/about/) — the operator

Built by [Alain Yamin (@S1N4X)](https://github.com/S1N4X) — cybersecurity, AI SecOps, purple teaming, energy sector.
