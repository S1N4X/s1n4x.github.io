+++
title = "swarm.tracks"
description = "what happens when AI agents play CTF"
+++

```
┌─────────────────────────────────────────────────────────┐
│  119 captured · 437 spawned · 152→34MB transcripts      │
│  11 honeypots avoided · 1 PI refused · 1 token rotated  │
└─────────────────────────────────────────────────────────┘
```

> We let the swarm run. This is what happened.

**swarm.tracks** is the public archive of a team experiment: we pointed a fleet of
Claude Code subagents at NSEC 2026 and let them try to solve real CTF challenges,
in parallel, with a human curator (S1N4X / the *bifftannen88* team) keeping them
out of obvious traps.

This site collects what came back. Not a polished tutorial blog. A field journal.

---

## navigate

- **[/posts](/posts)** &mdash; per-challenge writeups (the actual solves). Categorised
  by event (`nsec26`, `nsec25`, ...) and tagged by track (`web`, `crypto`, `rf`,
  `forensics`, ...).

- **[/swarm](/swarm)** &mdash; meta-posts about running the swarm itself: orchestrator
  protocols, coach-spawn templates, token budgets, what broke, what scaled.

- **[/slop](/slop)** &mdash; the funny / sad / surreal failure modes. Agent
  hallucinations, runaway loops, baroque cul-de-sacs. The slop is the point.

- **[/honeypots](/honeypots)** &mdash; flags that *looked* real and were not.
  Indirect prompt-injection lures, doc-example placeholders, decoy artifacts.
  We logged each one and the heuristic that caught it.

- **[/about](/about)** &mdash; the AI-slop manifesto. Read this first if the tone
  surprises you.

---

## scope

Currently archiving:

- **NSEC 2026** &mdash; primary event, ~119 flags captured by the swarm.
- **NSEC 2025** &mdash; partial backfill of prior-year writeups for delta analysis.
- More events to follow as we backfill.

All content first-drafted by Claude Opus 4.7 / Sonnet 4.6 agents during or shortly
after the event. Every post is then read, fact-checked, and approved by a human
before publication. The slop is intentional; the lies aren't.
