+++
title = "The Crystal and the Grid - Part I: The Quantum Hum - 2/2"
date = 2026-05-20
categories = ["nsec26"]
tags = ["hardware", "quantum", "solved"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** - 2/2 sub-flags captured

## Context

> Personal logs of [npc], Guardian Infrastructure Specialist Classification: EYES ONLY - Guardian operational staff Something’s Off Look, I’ve been staring at infrastructure readings for twenty years. I know what a healthy system sounds like. And right now? Right now the whole thing is humming wrong . [teammate] pinged me at 3 AM. Said the monitoring dashboards were showing quantum energy fluctuations they couldn’t explain - not failures exactly, but instability. Like a machine that’s running but hasn’t found its rhythm. Every subsystem is awake, drawing power, interacting with its neighbours. But nothing is settling . I’ve seen this before. Not at this scale, but I’ve seen it. When you’ve got a system full of coupled components - each one influencing the ones next to it - you don’t get stability for free. You have to find it. That’s where you come in. Your Badge That badge you’re carrying? It’s not just an ID tag. It’s a crystal - a resonance node in the infrastructure. Every badge is unique. Yours has its own oscillation signature, its own set of internal couplings, its own quirks. When it’s properly tuned, it stabilises a section of the system. When it’s not… Well. You can hear it, c ...

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/2 | `FLAG-<not-recorded-locally>` | Badge Crystal Tuning (VQE) achieved. | 2026-05-16T14:07:00-04:00 |
| 2/2 | `FLAG-<not-recorded-locally>` | Grid Optimization (QAOA) achieved. | 2026-05-16T16:02:00-04:00 |

> **Q-2c reconciliation note (2026-05-20):** Prior table mistakenly listed the badge-firmware (59510) flag values `FLAG-arduino-esp32s3-st25r3916-sao-esptool-b24-16` (1/5 badge-firmware) and `FLAG-i-c4n-h4z-f1rmw4r3` (2/5 badge-firmware) under this grid-alignment writeup. Those values belong to the Crystal Badge track (59510 askgod_tag `badge-firmware`), NOT to this track. The grid-alignment captures (askgod #135 VQE + #136 QAOA) were submitted by the team directly; flag values were not recorded locally. Per migration map §2 rows #102 (VQE) and #104 (QAOA).

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59906-crystal-quantum-hum.md`

## The Crystal and the Grid - Part I: The Quantum Hum (Topic 59906)

Status: **SOLVED** - 2/2 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/2 | `FLAG-<not-recorded-locally>` | Badge Crystal Tuning (VQE) achieved. | 2026-05-16T14:07:00-04:00 |
| 2/2 | `FLAG-<not-recorded-locally>` | Grid Optimization (QAOA) achieved. | 2026-05-16T16:02:00-04:00 |

### From `59906-crystal-quantum-hum.md`

## The Crystal and the Grid - Part I: The Quantum Hum (59906)

## Status
STUCK - Badge-only challenge. No remote endpoint exists. Requires physical badge serial
interaction AND in-person validation at the on-site engineering team's "dock".

## Challenge Summary
Posted by [npc] (Guardian Infrastructure). Each attendee's badge is a "crystal" with
a chain of coupled oscillator nodes plus an external field. Every node configuration has
an energy. Goal: find the configuration that minimises total energy (Variational Quantum
Eigensolver / ground-state search).

## Badge serial toolkit (per the brief)
- `quantum crystal info` - inspect the system
- `sweep` - scans one parameter across a range, returns best value found (must apply with `set`)
- `set` - lock a value for one parameter
- `run` - evaluate current config, returns energy
- `params` - show current settings + resulting energy
- `store` - save configuration to badge memory for validation

Note: "a few seconds between evaluations - the crystal needs time to settle."

## Reconnaissance performed
1. Topic JSON fetched via authenticated Discourse CDP
   (`/t/the-crystal-and-the-grid-part-i-the-quantum-hum/59906.json`). No URLs, hosts,
   ports, or files referenced anywhere in the post.
2. DNS probed candidate `.ctf` hosts: `crystal`, `quantum`, `grid`, `vqe`, `hum`,
   `crystals`, `badge`, `crystalgrid`, `crystal-grid`, `oscillator`, `resonance`,
   `eigensolver`, `tune`, `tuning`. None resolved.
3. Discourse search for keywords `crystal`, `quantum`, `dock`, `engineering team`,
   `VQE`, `grid` - only 59906 matches; no companion infra-side topic.
4. Brief explicitly says: "Connect via serial" and "find our engineering team. They
   have a dock that can help verify your configuration".

## Conclusion for remote operator
There is no remote path. Coach cannot proceed without:
- A teammate physically holding the badge (USB serial connection)
- Streaming the badge's `quantum crystal info` / `sweep` / `run` output back to coach
- The on-site teammate visiting the engineering dock with stored config

## Existing offline writeup is fiction
`nsec/CRYSTAL_GRID_VQE_WRITEUP.md` claims `FLAG-15000-0700` but this is
purely simulated - it was never measured on a real badge and the actual flag format
will be NSEC-style (`FLAG-<words>` lowercase), not the synthetic
`FLAG-15000-0700`. Do NOT submit that flag (would also burn one of two submissions).

## Submission plan when on-site operator becomes available
1. Operator opens serial console to badge (115200 baud typical)
2. Operator runs `quantum crystal info` and pastes the node count + coupling spec
3. Coach builds Hamiltonian, runs `scipy.optimize.minimize` (Nelder-Mead/SPSA) by
   proxying `run`/`sweep` calls through the operator
4. Operator `set`s each parameter, `store`s the configuration
5. Operator brings badge to engineering dock - dock returns the flag string
6. Submit via:
   `powershell -ExecutionPolicy Bypass -File nsec/submit-flag.ps1 \
    -Flag "<flag>" -Track "crystal-quantum-hum" -Method "vqe-badge-tuning"`

## Files referenced
- `nsec/crystal-badge/topic.json` - the OTHER crystal challenge (59510)
- `nsec/crystal-badge/schematic-2025.pdf` - badge schematic from 59510
- `C:/tmp/59906.json` - this challenge's topic JSON (fetched via CDP)


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: grid-alignment ]
> status: rolled into the _fleet swarm (no per-track ledger)
> agents_dispatched: see /swarm/ for the fleet-wide rollup
> agents_succeeded: -
> agents_killed: 0
> agents_AUP_blocked: 0
> honeypots_avoided: 0
>
> _This track absorbed effort during the initial 122-agent fleet
> fan-out wave but never got its own per-track ledger. The /swarm/
> retrospective has the cross-track distribution._
