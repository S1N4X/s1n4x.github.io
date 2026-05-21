+++
title = "Radio beacon"
date = 2026-05-19
categories = ["nsec26"]
tags = ["agent-slop", "rf", "stuck"]
model = "Opus 4.7"
draft = false
+++
Status: **STUCK** — 0/unknown sub-flags captured

## Context

> Woah that’s crazy! I was doing my stuff and suddenly found out a transmission at 146.565 MHz appearing out of nowhere, calling us out! I hope you have stuff like SDRs, rigs, directional antennas. There seems to be two different types of messages in there. One of these seem obvious, the other one, not so much. Come see us at the RF table if you need anything.

## STUCK Rationale

See artifacts for in-progress investigation notes.

## Artifacts

- Artifact directory: `nsec/radio-beacon/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59798-radio-beacon.md`

## Radio beacon (Topic 59798)

Status: **STUCK** — 0/unknown sub-flags captured

## Artifacts

- Artifact directory: `nsec/radio-beacon/artifacts`

## STUCK Rationale

- - [CODEX-workflow-fix] 2026-05-16 14:45 EDT: fixed both Codex and Claude Code CTFINT skills so future orchestrators/coaches must refresh askgod, rank by CFSS, skip completed aliases, and skip physical/device tracks unless explicitly requested. Patched skill mirrors: `<operator-codex-skills>/ctfint_workflow`, `ctfint_coach-spawn`, `ctfint_askgod-submit` and `<repo>/.claude/skills/ctfint_workflow`, `ctfint_coach-spawn`, `ctfint_askgod-submit`. Also updated `nsec/team-status/challenge-inventory.py` to emit `open_priorities_nonphysical` in `nsec/.challenge-inventory.json` and a "Low-Hanging Open Tracks (non-physical)" section in `nsec/ASKGOD-INVENTORY.md`. Current CFSS queue: `renewable-energy-mobility` 2/7, `weather-station` 1/4, `helios-fleet-network` 2/5, `monsatan-impact-study` 2/5, `monsatan-defacing` 5/6, then `apt438`, `fossilco`, `announcement-board`. Physical excluded by default: `badge-firmware`, `crystal`, `grid-alignment`, `monsatan-kiosk`, `plant-watering`, `radio-beacon`, `infinite-energy`.

### From `59798-radio-beacon.md`

## Context

RF challenge. Discourse body mentions a transmission on `146.565 MHz` (2 m amateur
band) with two message types, and tells participants to go to the RF table if they
need access. The challenge is physical — no remote artifact, no remote endpoint.

## Recon

- Discourse topic 59798 inspected: no URL, no filename, no attachment.
- `nsec/` search for `radio`, `beacon`, `59798` — only unrelated audio
  (`team-status/ole-celebration.wav`) and Missing-Bus artifacts.
- No `radio-beacon/` directory exists.
- `CANDIDATE_FLAGS.txt` previously listed "RADIO BEACON (59798)" but the candidates
  were sourced from `keyfob.complex16s`, which is the Missing-Bus IQ. Local Discord
  notes confirm this was an early-event misclassification.

## Attempted Exploitation Chains

| Vector | Result |
|---|---|
| DNS / URL lookup for `radio-beacon.ctf` and variants | NXDOMAIN |
| Re-using Missing-Bus IQ as a Radio Beacon source | Misattribution; bytes belong to Missing-Bus (decoded keyfob 0xdc8fd0) |
| Static analysis of any local artifact | None exist for this track |

## Captures

(None — `captures: []` above.)

## STUCK Rationale

Cannot proceed without a venue SDR pickup at 146.565 MHz. Operator must:
1. Carry an SDR or handheld receiver to the RF table.
2. Capture the transmission live (Morse / DTMF / APRS / AFSK / FSK / SSTV /
   spectrogram-text are all plausible — Discourse mentions "two message types").
3. Save the recording to `nsec/radio-beacon\` and triage offline.

## Anti-Trap Notes

The local `keyfob.complex16s` IQ file is **not** Radio Beacon material. A prior agent
mis-tagged it as Radio Beacon while brute-forcing demod variants. Submissions
journal does not contain a Radio-Beacon FAIL row, so the misclassification did not
generate a deny. If a future agent revisits this track, the keyfob IQ MUST be
rejected as out-of-scope (it decodes to `0xdc8fd0` for trolley-bus 1/4).

## Artifacts

- `nsec/.challenge-inventory.json` — `untouched_inventory[topic_id=59798]`
- `nsec/writeups\59798-radio-beacon.md` — this file (replaces earlier stub)
- Misattribution context: `nsec/CANDIDATE_FLAGS.txt`, see also
  `nsec/missing-bus\artifacts\keyfob.complex16s`

## Designer Intel (Discord, post-event 2026-05-19)

**Designer:** va2io. **Full sub-flag breakdown (from #va2io channel msg 1505672584526827611):**

| Sub-flag | Method | Points |
|---|---|---|
| Morse | LED morse code decode | 2 |
| Packet AFSK AX.25 (APRS) | RF capture + APRS decode | 2 |
| Geocaching | Coordinate hunt | 4 |
| ALFA + BRAVO + CHARLIE combination | Phonetic combo | 1 |
| FOX hunt | Find hidden transmitter (bike) | **20** |

**Total: 29 pts**

**FOX HUNT intended path** (va2io 1505672584526827611 + tedre191 1505962405635686471):
- Transmitter was hidden on a **bike parked near the venue** (b13bs 1506384156408545350)
- Intended method: directional antenna + signal strength measurements (not just triangulation)
- Reference video: https://www.youtube.com/watch?v=PN-c5DQFuhI ("Fox hunting 101")

Our UNTOUCHED status reflects no SDR hardware/RF expertise on team, plus on-venue presence required. The 29 pts available made this the largest single missed opportunity.

*See _DISCORD-INTEL-ENRICHMENT-2026-05-19.md for the full cross-track designer-confirmed solution catalog and writeup links.*


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: radio-beacon ]
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
