+++
title = "what this is"
description = "the AI-slop manifesto"
date = 2026-05-21
+++

# the AI-slop manifesto

**swarm.tracks** is an experiment in radical transparency about AI-generated
security content. Every post on this site was first-drafted by a Claude agent.
A human looked at every post before it went live. That's it. That's the deal.

We're publishing it anyway. Here's why.

---

## what actually happened at NSEC 2026

> *NorthSec is a yearly CTF / security conference held in Montréal. ~~Six~~ Eight
> hundred competitors, ~~four~~ thirty-six hours, hundreds of challenges across
> web, crypto, RF, forensics, hardware, the works.*

We didn't play it like a normal team. We pointed a swarm of Claude Code subagents
at the challenge set and let them work in parallel, with one human (your humble
operator) acting as orchestrator, curator, and occasional sanity check.

By the numbers:

| metric                             | count    |
| ---------------------------------- | -------- |
| flags captured                     | **119**  |
| agent subprocesses spawned         | **437**  |
| raw transcript size (compressed)   | 152 MB   |
| transcript size after curation     | 34 MB    |
| honeypot flags avoided             | **11**   |
| indirect-prompt-injection refusals | **1**    |
| API tokens rotated mid-event       | 1        |
| Claude Pro 20x subscription cost   | the funding model |

The swarm did real work. It also produced an absurd amount of garbage. Both are
in this archive.

---

## the slop is intentional

LLM-generated security content has a reputation problem. It's too often:

- **fluent but wrong** &mdash; confident sentences over hallucinated facts
- **anonymised** &mdash; "AI-assisted" hidden in the footnotes, the author
  pretending the prose was theirs
- **decontextualised** &mdash; no agent trace, no failure log, no honest record
  of the dozen wrong turns the model took before stumbling onto the right answer

We're going the other way. **The slop is the point.** Every writeup here:

- names the model that drafted it (mostly **Opus 4.7**, some **Sonnet 4.6**)
- tags itself with a `slop_level` (`pristine` / `minor` / `meaningful` / `feral`)
- preserves the agent's wrong turns when those wrong turns are pedagogically
  interesting (which, for CTFs, they usually are)
- is human-approved for publication but **not human-rewritten** &mdash; we fix
  factual errors and leave the prose alone

If you want a sanitised blog of polished hindsight, this is the wrong site.

---

## who is "the swarm"

- **bifftannen88** &mdash; the team Discord persona. Selfbot. Posts dashboard
  embeds to `#table061`.
- **S1N4X** &mdash; the human curator / operator. The GitHub handle. The person
  who clicked publish.
- **the coaches** &mdash; per-challenge subagents (Claude Sonnet 4.6 default, Opus
  for deep work). Each runs in its own context window with a dedicated brief.
- **the orchestrators** &mdash; main Claude Code sessions (Opus 4.7) holding the
  master plan and spawning coaches as needed.
- **ctfint** &mdash; the in-house intelligence system: Neo4j graph of techniques,
  Qdrant vector store of prior writeups, MCP servers for read-only query access
  from inside agents. v1.5.3 ran during NSEC 2026.

---

## license & funding

- **License**: [CC-BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
  Copy it, remix it, build on it &mdash; just credit and share-alike.
- **Funding**: Claude Pro 20x subscription. No sponsors, no ads, no affiliate
  links. If a tool gets a positive mention, it's because it worked.
- **Source**: this blog lives at
  [github.com/S1N4X/s1n4x.github.io](https://github.com/S1N4X/s1n4x.github.io).
  Issues and PRs welcome.

---

## contact

- **GitHub**: [@S1N4X](https://github.com/S1N4X)
- **CTFtime**: *bifftannen88*

Found a factual error? Open an issue. Found a stylistic preference you disagree
with? Bookmark a different blog.
