+++
title = "Community Alliance Sympatizers Mailbox — 1/1"
date = 2026-05-20
categories = ["nsec26"]
tags = ["agent-slop", "crypto", "solved"]
slop_level = "minor"
model = "Opus 4.7"
draft = false
+++

# Community Alliance Sympatizers Mailbox (Topic 58790)

Status: **SOLVED** — 1/1 sub-flags captured

## Context

> As you know, we are one of the many enclaves that are members of the Community Alliance. We have enclaves, and sympathizers, all across the world. We do have a website that allows us to look at sympathizers letters, which make us remember why we fight. I have received information about a document that was received that is worth our attention, but we haven’t been able to extract the source. We want you to dig on the sympathizer fanmail archive and retrieve the original document named letter.txt . Contact me as soon as you get it. I fear that the content is highly confidential, so your discretion would be advised. Sympatizers mailbox

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/1 | `FLAG-<not-recorded-locally>` | Change to every byte to reveal the plaintext then XOR. | 2026-05-16T00:55:00-04:00 |

## Artifacts

- Artifact directory: `nsec/sympatizers-mailbox/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `58790-sympatizers-mailbox.md`

# Community Alliance Sympatizers Mailbox (Topic 58790)

Status: **SOLVED** — 1/1 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/1 | `FLAG-<askgod-only-no-local-record>` | Change to every byte to reveal the plaintext then XOR. | 2026-05-16T00:55:00-04:00 |

## Artifacts

- Artifact directory: `nsec/sympatizers-mailbox/artifacts`

### From `58790-sympatizers-mailbox.md`

## Context

Topic 58790 — "CA Sympathizer Mailbox", askgod tag `docgen`, message *"Change to every byte to reveal the plaintext then XOR."* Single-flag crypto challenge worth 8 points, difficulty `I/E:M` (Intermediate, Effort:Medium). Submitted 2026-05-16 00:55:50 EDT by a coach agent, with the journal note `ctfint coach agent :: stream-cipher-keystream-recovery + path-traversal via JSON value in encrypted d param`.

The target is a Thymeleaf/Spring Java web app at `http://docgen.ctf` themed as a fan-mail mailbox ("Welcome to the CA Sympathizer Mailbox! Where voices of support find a place to be heard"). The user-facing flow is: visit `/showLetter`, get redirected to `/loadLetter?d=<base64-token>` which renders one of the supporters' letters by `file=`-style lookup.

The askgod hint — "change to every byte to reveal the plaintext then XOR" — is the canonical bullet-point recipe for a stream-cipher / byte-level keystream-recovery attack. The `d=` parameter is a per-request encrypted JSON envelope whose ciphertext is XOR-combined with a stream keystream byte by byte (no diffusion).

## Recon

Source materials in `C:\ctfint\nsec\sympatizers-mailbox\`:

- `index.html` (Spring/Thymeleaf SPA) — confirms framework via `xmlns:sec="http://www.thymeleaf.org/extras/spring-security"`
- `showLetter.html`, `loadLetter-empty.html`, `showLetter-full.html` — saved responses showing the token redirect flow
- `act.json`, `mappings.json` — request/response fixtures
- `topic.json`, `topic-raw.json` — Discourse scrape
- Multiple exploration scripts: `advanced_exploit.py`, `exploit_mailbox.py`, `exploit_traversal.py`, `template_injection.py`, `find_flag.py`, `find_download.py`, `explore_endpoints.py`, plus PowerShell harnesses (`probe.ps1`, `probe2.ps1`, `probe3.ps1`, `sweep.ps1`)
- Final exploit: `artifacts/padding_oracle_exploit.py` (the name is a holdover from the initial padding-oracle hypothesis; the actual successful approach is keystream recovery, not padding-oracle).

The breakthrough script is `artifacts/padding_oracle_exploit.py`, whose docstring captures the chain in eight bullet points (verbatim from the file header):

> KEY FINDING: `/loadLetter?d=<base64>` token is plaintext-length (82 bytes for the letter.txt session token). This is a stream cipher / byte-level CFB — each ciphertext byte affects exactly one plaintext byte (no diffusion).

> The keystream changes on each fresh token request (per-request nonce), so the exploit must be done end-to-end per session.

The recovered plaintext structure is documented in the script:

```
byte  0   : '{'
bytes 1-21: '"<key1>":"<value1>",'      (21 chars, varies but consistent format)
byte 22   : '"'
bytes 23-26: 'file'
byte 27   : '"'
byte 28   : ':'
byte 29   : '"'
bytes 30-57: './docs/bobby_0750387d3120.md'  (28-char path - LEAKED via FNF error)
byte 58   : '"'
bytes 59-81: ',"<key3>":"<value3>"}'   (23 chars)
```

The "file not found" verbose error response leaks the path `./docs/bobby_0750387d3120.md` plaintext-style. Once you have a known plaintext / ciphertext pair for bytes 30..57, you have the keystream for those positions and can substitute in any other 28-character path (padded with trailing `/` because Java's `new File(...)` normalizes trailing slashes on regular files).

## Exploitation

The exploit logic (from `artifacts/padding_oracle_exploit.py`):

1. **Acquire a fresh ciphertext token** — call `/showLetter?file=letter.txt` and capture the `Location: /loadLetter?d=<base64>` redirect. The base64 token decodes to 82 ciphertext bytes.
2. **Recover keystream bytes 30..57** by XORing the captured ciphertext slice with the known-plaintext path `./docs/bobby_0750387d3120.md` (28 chars).
3. **Forge a new ciphertext token** that swaps the path slice for an arbitrary 28-character target path (e.g. `./docs/../../../etc/hosts`, padded with `/`).

```python
new_ct[PATH_OFFSET + i] = orig_ct[PATH_OFFSET + i] ^ KNOWN_PATH[i] ^ new_path[i]
```

4. **Replay the forged token** at `/loadLetter?d=<forged_b64>` and read the file contents from the rendered `<div id="result">` block.
5. **Hunt the flag** — the exploit's `main()` walks a candidate list (`/opt/docgen/flag`, `/opt/docgen/flag.txt`, `/opt/docgen/.flag`, `/opt/docgen/docs/flag`, `/root/flag`, `/flag`, `/srv/flag`, etc.) and returns the first match containing the string `FLAG`.

The flag lives at one of those `/opt/docgen/` or `/root/` candidate paths (the script writes the winning content to `flag.txt`). Submitted under tag `docgen` at 2026-05-16 00:55:50 for 8 points.

## Captures

| Position | Flag | Method | When |
|---|---|---|---|
| 1/1 | (value-redacted, askgod #46) | Stream-cipher keystream recovery on `/loadLetter?d=` envelope + path-traversal in the JSON `file` value | 2026-05-16 00:55:50 |

## Anti-Trap Notes

No honeypot strings or PI lures observed. The `padding_oracle_exploit.py` filename is misleading — the script is keystream-recovery, not padding-oracle. Future readers should not assume CBC/PKCS#7 from the filename alone.

## Artifacts

- `C:\ctfint\nsec\sympatizers-mailbox\artifacts\padding_oracle_exploit.py` — **THE** working exploit (despite the filename)
- `C:\ctfint\nsec\sympatizers-mailbox\exploit_traversal.py` — earlier path-traversal probe (no crypto component) for the parameter-name enumeration phase
- `C:\ctfint\nsec\sympatizers-mailbox\index.html`, `showLetter.html`, `loadLetter-empty.html` — captured Spring/Thymeleaf surface
- `C:\ctfint\nsec\sympatizers-mailbox\advanced_exploit.py`, `exploit_mailbox.py`, `exploit_mailbox.ps1`, `template_injection.py`, `find_flag.py`, `find_download.py`, `explore_endpoints.py` — exploration scripts
- `C:\ctfint\nsec\sympatizers-mailbox\topic.json`, `topic-raw.json` — Discourse scrape
- `nsec/flags/submissions-journal.tsv` row 2026-05-16T00:55:50 — submission attribution


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: docgen ]
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
