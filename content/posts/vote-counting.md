+++
title = "Vote Counting - 1/1"
date = 2026-05-20
categories = ["nsec26"]
tags = ["crypto", "solved"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** - 1/1 sub-flags captured

## Context

> The city is testing for a new system to elect a new leader for the neighborhood council. It was usually done in person, but they’ve tried doing it electronically this time. Things didn’t end up well, which sucks, because I think e-democracy brings tons of opportunities! They say that security upgrades were the cause. Always blame security! We would need to verify that there are no irregularities during the vote. For this, we’ll analyze the traffic sent to the voting system, and compare it to the official count. Here are the 5 candidates. You need to provide a tally of votes for each one. A - Jean Terine Deslois B - Krzysztof Lpwstr C - Didier Azerty D - Marie-Soleil UV E - Bérenger Rhino voting-count.zip Flag format : FLAG-VOTING-{A:(Number of vote for A),B:(Number of vote for B),C:(Number of vote for C),D:(Number of vote for D),E:(Number of vote for E)} Example : FLAG-VOTING-{A:1,B:2,C:3,D:4,E:5}

## Artifacts

- Artifact directory: `nsec/vote-counting/artifacts`
- Flag values archive: `nsec/flags/vote-counting.txt`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59474-vote-counting.md`

## Vote Counting (Topic 59474)

Status: **SOLVED** - 1/1 sub-flags captured

### From `59474-vote-counting.md`

## Vote Counting (Challenge 59474)

## Context

Network forensics challenge. Analyze a pcapng file containing voting traffic encrypted with TLS 1.3. Count actual votes cast per candidate and compare to official tallies to detect fraud.

The challenge ships `city_vote_capture.pcapng` plus SSL certificates that, due to TLS 1.3 AEAD, cannot be used to recover plaintext from RSA key alone.

## Recon

- pcapng contains 40 TLS streams targeting voting infrastructure
- Server exposes:
  - Port 9000 - HTML voting page enumeration target
  - Port 9001 - JSON vote-submission endpoint
- Provided RSA key permits no decryption against TLS 1.3 AEAD ciphersuites
- Each candidate's JSON payload has a unique deterministic size

## Exploitation

### Side-channel via TLS record size

Each candidate's JSON vote payload has a distinct on-the-wire size driven by candidate name and metadata length. After enumeration of the HTML voting page, the per-candidate payload sizes are known:

| Candidate | JSON payload | TLS record (after AEAD overhead) |
|-----------|--------------|----------------------------------|
| A (Jean Terine Deslois) | 180 B | 647 B |
| B (Krzysztof Lpwstr) | 71 B | 537 B |
| C (Didier Azerty) | 322 B | 789 B |
| D (Marie-Soleil UV) | 949 B | 1417 B |
| E (Bérenger Rhino) | 555 B | 1022 B |

Counting TLS application-data records per stream by exact byte size yields per-candidate vote tallies. The total across 40 sessions × N votes resolves to the final tally.

## Captures

### Flag 1/1 - Vote Counting (askgod #170 / 7 pts)

- **askgod entry:** vote-counting 1/1
- **Timestamp:** 2026-05-15 22:32 EDT
- **Method:** TLS record-size side-channel; tally vote streams by payload size

```
FLAG-VOTING-{A:1597,B:621,C:1108,D:573,E:101}
```

**Vote Summary:**
- Candidate A (Jean Terine Deslois): 1597 votes
- Candidate B (Krzysztof Lpwstr): 621 votes
- Candidate C (Didier Azerty): 1108 votes
- Candidate D (Marie-Soleil UV): 573 votes
- Candidate E (Bérenger Rhino): 101 votes
- Total: 4000 votes

## Anti-Trap Notes

The challenge ships an RSA key as a lure - using it directly fails because TLS 1.3 AEAD prevents server-key-only decryption. The intended-but-deceptive path is to attempt full plaintext recovery. The actual solve is metadata-only (record sizes), no decryption required.

## Artifacts

- `nsec/vote-counting/city_vote_capture.pcapng` - Original capture
- `nsec/vote-counting/server.key` - Provided RSA key (decryption red herring)
- Vote counting Python script (per-stream size histogram)


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: vote-counting ]
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
