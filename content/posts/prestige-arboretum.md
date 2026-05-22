+++
title = "Prestige Arboretum"
date = 2026-05-19
categories = ["nsec26"]
tags = ["crypto", "stuck"]
model = "Sonnet (default)"
draft = false
+++
Status: **STUCK** - 0/unknown sub-flags captured

## Context

> I hope you have visited the Prestige Arboretum! While we have a good climate, our winter is still harsh, so there are some species of trees that we cannot have. Biodiversity is a treasure, and it is fun to see other species from across the world! The Arboretum finances itself thanks to distributed computing. Instead of using cloud computers and big, heartless, companies, people can set up an account with the Prestige Arboretum, using their computer slow times to help for distributed computing. And the more you do, the higher levels you gain. And the best is that you help finance the arboretum, so everyone wins! Can you reach the level 10? Level 10 is the most prestigious honor of the arboretum computing group. I would love to become an Arboretum Legend, without wasting power! Prestige Arboretum

## STUCK Rationale

- | ~15:00 | [teammate] Prestige Arboretum stuck question | Replied with 3-strategy STUCK summary (A bit-flip math broken, B same, C ECB-splice requires absent plaintext) | Delivered |

## Artifacts

- Artifact directory: `nsec/prestige-arboretum/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59834-prestige-arboretum.md`

## Prestige Arboretum (Topic 59834)

Status: **STUCK** - 0/unknown sub-flags captured

## Context

> I hope you have visited the Prestige Arboretum! While we have a good climate, our winter is still harsh, so there are some species of trees that we cannot have. Biodiversity is a treasure, and it is fun to see other species from across the world! The Arboretum finances itself thanks to distributed computing. Instead of using cloud computers and big, heartless, companies, people can set up an account with the Prestige Arboretum, using their computer slow t

[... 20022922 bytes of duplicate-source notes elided; full content preserved in nsec/prestige-arboretum/artifacts/ ...]

## Designer Intel (Discord, post-event 2026-05-19)

**Designer:** Philippe Arteau ([designer]). **Intended solution (from #[designer] channel, linkster78 msg 1506045574712004609 - community solve):**

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
- The `+` URL-decode behavior IS real but not directly exploitable - the real primitive was ECB block transplant on a *valid* working token with the right offset

This is a high-value pattern for next-year writeup mining: **ECB-mode block transplant where the attacker controls input length to align ciphertext blocks**. Combine with reset-password endpoint that gives the oracle.

*See _DISCORD-INTEL-ENRICHMENT-2026-05-19.md for the full cross-track designer-confirmed solution catalog and writeup links.*
