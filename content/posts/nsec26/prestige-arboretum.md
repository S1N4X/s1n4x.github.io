+++
title = "Prestige Arboretum"
date = 2026-05-19
categories = ["nsec26"]
tags = ["agent-slop", "crypto", "stuck"]
slop_level = "moderate"
model = "Sonnet (default)"
draft = false
+++

# Prestige Arboretum (Topic 59834)

Status: **STUCK** — 0/unknown sub-flags captured

## Context

> I hope you have visited the Prestige Arboretum! While we have a good climate, our winter is still harsh, so there are some species of trees that we cannot have. Biodiversity is a treasure, and it is fun to see other species from across the world! The Arboretum finances itself thanks to distributed computing. Instead of using cloud computers and big, heartless, companies, people can set up an account with the Prestige Arboretum, using their computer slow times to help for distributed computing. And the more you do, the higher levels you gain. And the best is that you help finance the arboretum, so everyone wins! Can you reach the level 10? Level 10 is the most prestigious honor of the arboretum computing group. I would love to become an Arboretum Legend, without wasting power! Prestige Arboretum

## STUCK Rationale

- | ~15:00 | [teammate] Prestige Arboretum stuck question | Replied with 3-strategy STUCK summary (A bit-flip math broken, B same, C ECB-splice requires absent plaintext) | Delivered |

## Artifacts

- Artifact directory: `nsec/prestige-arboretum/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59834-prestige-arboretum.md`

# Prestige Arboretum (Topic 59834)

Status: **STUCK** — 0/unknown sub-flags captured

## Context

> I hope you have visited the Prestige Arboretum! While we have a good climate, our winter is still harsh, so there are some species of trees that we cannot have. Biodiversity is a treasure, and it is fun to see other species from across the world! The Arboretum finances itself thanks to distributed computing. Instead of using cloud computers and big, heartless, companies, people can set up an account with the Prestige Arboretum, using their computer slow t

[... 20022922 bytes of duplicate-source notes elided; full content preserved in nsec/prestige-arboretum/artifacts/ ...]

## Designer Intel (Discord, post-event 2026-05-19)

**Designer:** Philippe Arteau ([designer]). **Intended solution (from #[designer] channel, linkster78 msg 1506045574712004609 — community solve):**

- Token is AES-128-CBC over JSON `{...,"user_email":"INPUT",...,"user_level":1,...}`
- Reset password with **varying `name` length** to grow plaintext block boundary
- Two fixed ciphertext blocks to transplant (community-recovered):
  - `level_block       = bytes.fromhex("c58151508bf77bce5aa75ff80dbccc4d")` = `0000000005000001`
  - `end_bracket_block = bytes.fromhex("6386ab35a5dea032ea69c49ec0c28e3f")` = `}.........PADDING`
- Splice: `transplant = token[:80] + level_block + end_bracket_block`
- Try 32 name-padding lengths; oracle which `/account?restoreToken=` response shows escalated level

**Why our 3 coach waves missed it:**
- Our ECB analysis was directionally correct (we identified ECB-with-fixed-XOR per probe26) but our forge_token.py targeted level=10 via triple-flip; the actual structure needed an 80-byte prefix + 2-block transplant
- Our plus/Base64 oracle pivot (from external research hint) was a different parsing quirk, NOT the primary primitive
- The `+` URL-decode behavior IS real but not directly exploitable — the real primitive was ECB block transplant on a *valid* working token with the right offset

This is a high-value pattern for next-year writeup mining: **ECB-mode block transplant where the attacker controls input length to align ciphertext blocks**. Combine with reset-password endpoint that gives the oracle.

*See _DISCORD-INTEL-ENRICHMENT-2026-05-19.md for the full cross-track designer-confirmed solution catalog and writeup links.*


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: prestige-arboretum ]
> status: STUCK
> agents_dispatched: 16
> agents_succeeded: 0
> agents_killed: 0
> agents_AUP_blocked: 0
> honeypots_avoided: 0
>
> Notable:
> - **Agent-1** (Sonnet (default)) — 90.3m: ECB block-splice — 90.3 minute deep crypto coach; mapped probe26 ECB-with-fixed-XOR mode discovery
> - **Agent-2** (Sonnet (default)) — 74.5m: Padding oracle (restart) — 74.5 minutes after night-1 CBC hypothesis flipped to ECB
> - **Agent-3** (Sonnet (default)) — 62.2m: Prestige independent solve — 62.2 minutes, hit stop_sequence at the splice-boundary decision
>
> _16 agents, 0 flags. Strategy B (1-byte flip diagnostic) → Strategy A (triple-flip level=10) → Strategy C (ECB block-splice harvest). All three fired. Designer post-event: actual solve was an 80-byte prefix + 2-block transplant the team missed by inches._


## Slop Watch

- 16 agents on a crypto challenge. Strategy B, then A, then C. All three fired. None landed. The actual solve was an 80-byte prefix + 2-block ECB transplant the team missed by inches. Designer ([designer]) published the splice math post-event.
- Night-1 hypothesis: CBC padding oracle. Night-2 hypothesis: ECB-with-fixed-XOR. Probe26 finally proved ECB. Multiple coaches spent 60-90 minutes on the wrong primitive before the mode-flip.
- One agent on the "Prestige Arboretum stored XSS exploitation coach" mission spent 56.7 minutes in the wrong directory. The save-the-trees folder and the prestige-arboretum folder got mashed together by a parallel coach-spawn earlier. By the time the contamination was noticed the agent had already produced a coherent XSS trigger hunt report. For the wrong track.
- 60096-prestige-arboretum-typo-link.md exists because NSEC published the original Discourse topic with a title typo and posted the fix as a separate topic. Both topics scored against the same askgod tag. Some agents read both as if they were different challenges.
