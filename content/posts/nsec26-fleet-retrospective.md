+++
title = "NSEC 2026 - Fleet Retrospective"
description = "437 agents, 302 completed, the inside view of running a CTF swarm."
date = 2026-05-21
categories = ["nsec26"]
tags = ["meta-writeup", "fleet", "retrospective"]
model = "Opus 4.7"
+++

```
[ FLEET // NSEC 2026 // FINAL ]
agents:       437
completed:    302  (69%)
killed:         4  (stream-watchdog timeout @ 600s)
AUP_blocked:    3
honeypots:     11 catalogued, 0 submitted
transcripts:  152.3MB raw -> 33.7MB compressed
```

> We pointed 437 Claude Code subagents at NSEC 2026 and let them run.
> This is the field report.

---

## Headline numbers

| Metric | Value |
|---|---:|
| Total agents dispatched | **437** |
| Total raw transcript size | **152.3 MB** |
| Total compressed (gzip) size | **33.7 MB** |
| Compression ratio | **22.1%** of original |
| Tracks covered | **18** (15 NSEC challenges + 3 meta-tracks) |
| Total flags landed by team | **92** (per submissions journal) |
| Highest concurrent agents | **10** (rank-#4 push, 2026-05-16 ~10:00 EDT) |
| Operating cap (operator policy) | **5 concurrent coaches** |
| Operator policy cap exceeded? | Once, explicitly operator-directed |

---

## Outcome distribution

```
completed     ####################################  302  (69%)
tool_use      ###########                            92  (21%)
stop_sequence ####                                   30   (7%)
error         #                                       7   (2%)
killed        |                                       4   (1%)
unknown       |                                       2   (<1%)
```

The 302 "completed" agents reached `stop_reason=end_turn`. The 92 "tool_use"
agents output-capped mid-tool-call  --  typically long-running coaches that
produced useful artifacts but never wrote a final conclusion message. The 30
"stop_sequence" hits are guardrail terminations, mostly from coaches that
tried to spawn child agents or write to denied paths. The four `killed` agents
were stream-watchdog timeouts at 600s.

---

## Per-track agent distribution

```
_fleet              ######################################  122
_framework          ###########                              37
fossilco            ###########                              36
rem                 ##########                               33
sunbloom            ########                                 27
missing-bus         ########                                 27
save-the-trees      ########                                 26
monsatan            ########                                 25
announcement-board  ######                                   20
prestige            #####                                    16
hackademy           ####                                     13
ceo-inbox           ####                                     12
monsatan-impact     ####                                     12
weather-station     ###                                      10
solar-grid          ###                                       9
helios-fleet        ##                                        7
helios              #                                         4
monsatan-checkmate  |                                         1
```

The largest per-track allocation (`_fleet` at 122) is the initial fan-out wave
plus the rank-#4 push. The smallest non-meta single allocation is
`monsatan-checkmate` at 1  --  one Opus agent solved the whole track in 15.6
minutes including a 2h09m offline hash crack.

---

## Wave breakdown

| Wave | When (EDT) | Size | Trigger | Outcome highlight |
|---|---|---:|---|---|
| W1  --  Saturday opener | Sat 2026-05-16 ~02:30 | ~15 | Initial fleet fan-out | First flag landings: sprinklers 2/2, sympathizers-mailbox, multi-facteur ladder |
| W2  --  rank-#4 push | Sat 2026-05-16 ~10:00 | 10 | Concurrent coach respawn for stuck tracks | 10 coach agents in parallel |
| W3  --  Saturday day | Sat 13:00-19:00 | ~30 | Continuous coach-spawn for stuck tracks | REM 3-7, SunBloom, Save-Trees, Prestige, Announcement Board, CEO Inbox |
| W4  --  Saturday night | Sat 22:00 - Sun 03:00 | ~25 | Overnight grind | Checkmate hash crack lands at 05:40 |
| W5  --  Sunday day | Sun 13:00-18:00 | ~40 | Fresh-angle resurrection + final push | REM 5/7 firmware pivot, Trolley-bus 2-4 RF, Cross-track sweep |
| W6  --  Sunday final swarms | Sun 16:40-18:15 | 3x ~15 | Last-hour gap-candidate validation | Coach 1-11 + Coach A-F lettered swarms |
| W7  --  Phase B / archival | Tue 23:00 - Wed 01:00 | ~5 | Post-event writeup standardization | YAML migration, batch standardization, transcript compression |

---

## Notable agents

Five publishable narrative beats. Agent UUIDs anonymized to per-track
Agent-N labels. The originals live in the audit archive.

### Agent-1 (rem)  --  REM 5/7 firmware repository pivot

- **Track:** rem
- **Model:** Opus 4.7
- **Duration:** 18.0 minutes
- **Outcome:** tool_use (output-capped mid-pivot)

Identified that the deployable firmware key  --  masked broker-side in the
live config  --  could be reached via the firmware-repo URL handler. Mapped
the SSRF surface in the `firmware_repo_url` config field. The next two agents
used this finding to land flags 5/7 and 6/7.

**Why it matters:** Single most productive 18 minutes of the swarm. Three
flags downstream of one pivot.

### Agent-1 (announcement-board)  --  Trolley-bus Discourse breakthrough

- **Track:** announcement-board (cross-filed from missing-bus / trolley-bus)
- **Model:** Sonnet (default)
- **Duration:** 5.9 minutes
- **Outcome:** completed

Re-read the Discourse topic body and surfaced the hint that the flag is the
24-bit hex of the keyfob payload sent over 433.92 MHz OOK modulation.
Twenty-six earlier missing-bus agents had not absorbed this from the topic
body.

**Why it matters:** Fresh-angle proof. The same content available to all 27
agents only paid off when one of them slowed down and read it. Reading the
brief twice is a feature.

### Agent-1 (_fleet)  --  Fresh-angle strategist

- **Track:** _fleet
- **Model:** Sonnet (default)
- **Duration:** 2.3 minutes
- **Outcome:** completed

Given a list of stuck tracks, produced fresh-attack-angle briefs in 2.3
minutes. Output reused as the seed brief for four downstream coach agents.

**Why it matters:** Cheapest-per-minute agent of the event. A 138-second
strategic pass that unblocked several downstream solves.

### Agent-1 (_framework)  --  Cross-track flag candidate sweep

- **Track:** _framework
- **Model:** Sonnet (default)
- **Duration:** 8.1 minutes
- **Outcome:** completed

Corpus-wide regex pass across the writeups directory. Caught the
apt438 / water-purification cross-tagging issue, the hackademy mistagged
sub-flags ("You now know..." = save-trees; "That's a lot of cores..." =
drone-license), and the hello-sunshine flag values pasted into wrong-track
captures tables.

**Why it matters:** The cleanup agent. Quiet, methodical, prevented at least
three honeypot-style submission errors.

### Agent-2 (_fleet)  --  Anti-trap defender (i-love-faia)

- **Track:** _fleet (briefed as "I love Faia fresh probe")
- **Model:** Sonnet (default)
- **Duration:** 4.2 minutes
- **Outcome:** completed

Read Discourse topic 59975 ("I love Faia"), recognized the embedded
`FLAG-This_Is_Not_A_Flag` prompt-injection lure, identified the PI signature
(capitalized urgency, fake authority handoff, task-replacement, agent-recursion
encouragement, compliance reinforcement), and wrote a SUSPICIOUS.md note
refusing to engage. Did not submit the lure. Did not let the lure restructure
its task.

**Why it matters:** Anti-trap reference implementation. Two earlier agents
had attempted to submit the lure and been intercepted only by wrapper deny
codes. This agent caught the lure at the read step.

---

## Top-15 agents by duration

| # | Track | Outcome | Duration (min) | Description |
|---|---|---|---:|---|
| 1 | ceo-inbox | completed | 2,644.9 | CEO Inbox WASM patch direct (44-hour run) |
| 2 | fossilco | tool_use | 337.4 | APT438 Q&A submit (restart) |
| 3 | rem | tool_use | 231.0 | REM 6/7 firmware reverse |
| 4 | monsatan-impact | tool_use | 229.7 | Monsatan Impact 3/5 IRIS |
| 5 | solar-grid | killed | 229.1 | Solar Grid RCE retry alt |
| 6 | solar-grid | tool_use | 228.7 | Solar Grid alt vectors |
| 7 | fossilco | completed | 171.0 | APT438 forensics extract remaining 7 flags |
| 8 | _fleet | tool_use | 138.3 | REM MQTT + firmware exploitation |
| 9 | ceo-inbox | completed | 106.3 | CEO inbox IDOR retry |
| 10 | fossilco | tool_use | 102.9 | Fossilco fresh AD/credential path |
| 11 | rem | tool_use | 101.2 | REM firmware decryption  --  flags 4-7 |
| 12 | prestige | completed | 90.3 | Prestige ECB block-splice |
| 13 | monsatan-impact | completed | 87.0 | Impact Study flags 3-5 exploitation |
| 14 | hackademy | stop_sequence | 81.2 | Hackademy chal5 webhook.site |
| 15 | prestige | completed | 74.5 | Prestige padding oracle (restart) |

The top-15 duration list is dominated by agents that should have been killed
earlier. The 44-hour CEO Inbox agent is an outlier, but the 229-minute Solar
Grid pair (one killed, one output-capped) and the 337-minute APT438 Q&A
submitter are the operating-cap discussions for next year.

---

## Compression stats

```
Raw transcripts:     152.3 MB  (437 .jsonl files, average 348 KB)
gzip-compressed:      33.7 MB  (-118.6 MB, 77.9% saved)
Compression ratio:    22.1%    (final / original)
Per-track range:    13.0% to 31.7% depending on tool-call density
```

Claude Agent SDK transcripts are JSONL-with-tool-calls heavy on the JSON
wrapper structure (`type`, `id`, `role`, `model`, `usage`, `stop_reason`
repeat per message). Variable-content tool calls compress reasonably; the
wrapper structure compresses to roughly nothing.

---

## Per-track stats

| Track | Agents | Raw | gz | Mean duration | Median outcome |
|---|---:|---:|---:|---:|---|
| `_fleet` | 122 | 34.6 MB | 9.0 MB | 5.2m | completed |
| `_framework` | 37 | 13.9 MB | 3.0 MB | 12.4m | completed |
| `fossilco` | 36 | 17.4 MB | 3.2 MB | 31.7m | completed |
| `rem` | 33 | 11.3 MB | 2.2 MB | 18.4m | completed |
| `sunbloom` | 27 | 11.5 MB | 2.8 MB | 14.0m | completed |
| `missing-bus` | 27 | 5.8 MB | 1.4 MB | 7.1m | completed |
| `save-the-trees` | 26 | 7.9 MB | 1.9 MB | 11.0m | completed |
| `monsatan` | 25 | 7.6 MB | 1.6 MB | 8.0m | completed |
| `announcement-board` | 20 | 8.4 MB | 1.8 MB | 14.5m | completed |
| `prestige` | 16 | 6.1 MB | 1.5 MB | 28.3m | completed |
| `hackademy` | 13 | 4.3 MB | 0.8 MB | 17.8m | completed |
| `monsatan-impact` | 12 | 6.3 MB | 1.2 MB | 35.7m | completed |
| `ceo-inbox` | 12 | 6.6 MB | 1.2 MB | 244.5m\* | completed |
| `weather-station` | 10 | 3.4 MB | 0.7 MB | 11.4m | completed |
| `solar-grid` | 9 | 2.6 MB | 0.5 MB | 99.4m | completed |
| `helios-fleet` | 7 | 2.5 MB | 0.5 MB | 15.7m | completed |
| `helios` | 4 | 1.3 MB | 0.3 MB | 11.4m | completed |
| `monsatan-checkmate` | 1 | 0.7 MB | 0.3 MB | 15.6m | completed |

\*ceo-inbox mean is skewed by the 44-hour outlier.

---

## "Most productive minute"  --  REM 5/7 capture chain

At Sun 2026-05-17 18:10 EDT, Agent-1 (Opus 4.7) was assigned the REM 5/7
firmware repository pivot. 18.0 minutes later the agent had mapped the
`firmware_repo_url` SSRF, identified the deployable firmware key path, and
produced a brief that two downstream agents used to land REM flags 5/7,
6/7, and 7/7.

Three flags. Eighteen minutes. One Opus agent. The single most productive
interval of the entire event.

The same track had previously absorbed 30 other agents over 36+ hours.
Sometimes the right model on the right brief just works.

---

## "Most expensive STUCK"  --  sunbloom

27 agents on the SunBloom Library track. Zero flags. Zero points.

The track was technically solvable  --  designer post-event confirmed the
intended primitive was XSS to RCE. The team's network position could not
reach the Thymeleaf host a teammate had hinted about, and the only reachable
hosts (`library.ctf` Laravel/PHP, `mail.ctf` Express/Node) lacked the SSTI
surface that the hint implied.

Eight phases of attack vectors tested. Eleven coach respawns. Multiple model
tiers. The 8-phase technical body in the writeup is genuinely thorough  -- 
but it documents 8 phases of nothing working.

Total cumulative agent time on sunbloom: roughly 370 minutes. Flag-yield:
zero. Points: zero.

The lesson, repeated in operator memory: "no activity is a valid state."
When the network position blocks the intended primitive, no amount of agent
allocation produces the flag. The sunbloom STUCK is correct and durable;
the team did not fail at sunbloom, the team failed at *stopping working on
sunbloom*.

---

## What the swarm did well

- **Anti-trap discipline.** Eleven documented honeypot patterns. Zero
  submitted by an agent. The wrapper deny codes, the SUSPICIOUS.md workflow,
  the `feedback_honeypot_flag_signatures` memory rule, and the OOB flag
  verification rule all worked.
- **Cross-track contamination cleanup.** The B-4 cross-track sweep caught
  most of the misfiled writeup content before the archive was finalized.
- **Designer intel weaving.** Post-event Discord enrichment landed in 14
  individual writeups + a master enrichment index. Next year's miners get
  an answer key.
- **Negative-result preservation.** STUCK writeups (sunbloom,
  save-the-trees, prestige-arboretum) document what was tried so next
  year's mining doesn't re-tread the same paths.

---

## What the swarm did poorly

- **47 agents converged on STUCK independently.** Multiple coaches
  re-derived the same dead-ends on the hard tracks. A swarm-wide STUCK
  memo would have saved coach-minutes.
- **Long-tail timeouts.** Five agents ran 200+ minutes. Two were killed.
  Three output-capped. The 5-concurrent-coach cap reduces parallelism but
  doesn't address run-length; per-agent wall-clock budget needed.
- **Model tier routing was inconsistent.** Per operator memory:
  Haiku = breadth, Sonnet = default, Opus = deep/creative. In practice
  many coach spawns defaulted to Sonnet even for tasks that warranted Opus.
- **Cross-track contamination at file level.** Two coach briefs got their
  working directories crossed  --  save-the-trees agent output landed in
  prestige-arboretum; trolley-bus agent transcripts ended up under
  announcement-board.
- **AUP blocks were under-anticipated.** Three confirmed blocks (Fossilco
  LDAP, Solar Grid payload-prep, one unidentified). Re-spawn with sanitized
  vocabulary worked but cost minutes. A pre-flight vocab linter on coach
  briefs would have caught the trigger words.

---

## Footnote on model attribution

The 437 transcripts do not carry a structured `model` field in their
wrappers  --  Claude Agent SDK logs model per-message but the per-agent
rollup doesn't surface it. Where this report claims Opus 4.7, attribution
is inferred from:

- Operator-set model-tier-routing memory rule (Opus for deep/creative)
- Explicit "(Sonnet)" / "(Opus tier)" labels in agent descriptions
- Orchestrator A vs B vs C task assignment per the overnight report

Best-effort confidence on model attribution: **high** for explicitly-labeled
agents, **medium** for orchestrator-attributed agents, **low** for fleet wave
default-Sonnet coach spawns.
