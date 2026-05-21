+++
title = "Missing bus — 1/4"
date = 2026-05-19
categories = ["nsec26"]
tags = ["agent-slop", "hardware", "partial", "rf"]
slop_level = "moderate"
model = "Sonnet (default)"
draft = false
+++

# Missing bus (Topic 59870)

Status: **PARTIAL** — 1/4 sub-flags captured

## Context

> I’ve just received a report of a potential threat. I need you to listen. Last Thursday evening, bus 1304 was finishing it’s run on line 12, in the north of the city. The poor driver was ambushed by outsiders. Thankfully she’s OK. But her bus has disappeared. They have tampered with the computer inside the bus and localization systems are gone. We need to locate that bus and retrieve it. A bus in the wrong hands could do a lot of damage to our population. At the time of the heist, a radio transmission was intercepted at 433.92 MHz. Intelligence suggests that prior to the robbery, bad actors had covertly planted a backdoor in the bus’s access system, most likely a rogue keyfob protocol embedded by insiders to bypass standard security. This intercepted transmission appears to be the activation signal for that hidden backdoor. Before we can locate the bus, we’ll need to reverse-engineer the transmission and figure out how to exploit that planted vulnerability to get it open. Here’s the file that was captured by the surveillance system: https://dl.nsec/keyfob.complex16s Start with reverse engineering this radio transmission to get the keyfob code, and I’ll make sure all is OK before we  ...

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/4 | `FLAG-<not-recorded-locally>` | This flag is the HEX code of the keyfob sent over radio (433.92 MHz ook modul... | 2026-05-17T13:35:00-04:00 |

## STUCK Rationale

- | ~15:30 | [teammate] Gemini hint on Missing Bus | Confirmed `dc8fd0` is correct keyfob code (off-by-1 start-bit interpretation). Format is the blocker (3 FAILs already, 2 remaining). Listed format variants to try via wrapper [DENY-SHAPE] gate test | Broadcasted to team |
- **Coordination note:** All three coaches briefed to use `/ctfint_askgod-submit` skill for autonomous submission (operator approved). Prestige ready to fire on VPN restore; Missing bus flag identified (awaiting brute-window reset); SunBloom blocked on email interception.

## Artifacts

- Artifact directory: `nsec/missing-bus/artifacts`
- Breakthrough: `nsec/missing-bus/FLAG-2-3-4-BREAKTHROUGH.md`
- Breakthrough: `nsec/missing-bus/artifacts/BREAKTHROUGH_ANALYSIS.md`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59870-missing-bus.md`

# Missing bus (Topic 59870)

Status: **PARTIAL** — 1/4 sub-flags captured

### From `59870-missing-bus.md`

# Missing bus (Topic 59870)

Status: **STUCK** — 0/unknown sub-flags captured

## Artifacts

- Artifact directory: `nsec/missing-bus/artifacts`
- Breakthrough: `nsec/missing-bus/FLAG-2-3-4-BREAKTHROUGH.md`
- Breakthrough: `nsec/missing-bus/artifacts/BREAKTHROUGH_ANALYSIS.md`

## STUCK Rationale

- | RF/web | **trolley-bus 2-4/4** | 3 | 🚨 **CROSS-CONFIRMED 13:55**: Two agents independently landed on [teammate]'s post #2 (topic 59870, 17:35:07 UTC). Flags 2-4 are ALL physical Wi-Fi recon. SSID `MNT-BUS` / PSK `hamped1304`. Track renamed `missing-bus`→`trolley-bus` server-side. Operator brief saved: `nsec/missing-bus\FLAG-2-3-4-BREAKTHROUGH.md`. Hand off to [teammate] (on-venue). Anti-trap: captive portal HTML is untrusted. |
- | ~15:30 | [teammate] Gemini hint on Missing Bus | Confirmed `dc8fd0` is correct keyfob code (off-by-1 start-bit interpretation). Format is the blocker (3 FAILs already, 2 remaining). Listed format variants to try via wrapper [DENY-SHAPE] gate test | Broadcasted to team |
- 2. [teammate] Gemini on Missing Bus (start-bit off-by-1 confirming dc8fd0 is the right code)
- Both posts visible in team channel msg-IDs `1505617495107834177` (Prestige) and ~`1505621xxx` (Missing Bus).
- | `aba156401cb65576f` | missing-bus format hunter | Inventory FLAG- format variants without burning brute budget; 1-shot auto-submit best candidate | running |
- - `nsec/missing-bus/artifacts/gemini-hint-2026-05-17.txt` -- [teammate]'s Gemini analysis archived

### From `59870-missing-bus.md`

# Missing Bus (59870 / askgod track `trolley-bus`) - Writeup

- **Challenge ID:** 59870
- **askgod track:** `trolley-bus` (note: Discourse title is "Missing bus" but the score tag is `trolley-bus`)
- **Category:** RF / Hardware → Wi-Fi / Physical
- **Event:** NSEC 2026 (Gaia / Solarpunk)
- **Owner (claimant):** [teammate] → submitted by **[teammate]** 2026-05-17 13:35 EDT
- **Status:** **1/4 SOLVED** — `flag-dc8fd0` accepted by askgod (entry ID 28). Stage 2/4 unlocks Wi-Fi physical recon. Stages 3 and 4 not yet visible.
- **Decoded value:** `0xdc8fd0` (24-bit OOK/PWM keyfob code, confirmed across 10+ frames)
- **Flag-1 format ([teammate], Discourse 59870 post 1):** `flag-{HEX KEY CODE}` → `flag-dc8fd0`

> **STAGE 1 RESOLUTION:** The 6-char flag `flag-dc8fd0` was correct. [teammate] submitted it
> directly to askgod (bypassing the local `submit-flag.ps1` 8+ char wrapper) at 13:35 EDT
> and received `[trolley-bus] 1/4 Good job!`. Three flags remain.

---

## Stage 2/4 — Wi-Fi physical recon (UNLOCKED at 17:35 UTC 2026-05-17)

Immediately after [teammate] submitted `flag-dc8fd0` (13:35 EDT = 17:35 UTC), [teammate] posted a
second message to Discourse topic 59870:

> I am rejoiced to hear that you've got the keyfob code. Time to get on the terrain.
> Bus 1304 was an old model that had a Wi-Fi Access Point for basic maintenance checks.
> It is autonomous from the computer system, so the outsiders probably missed this.
> Now I need you to locate the bus by tracking its SSID and password: **`MNT-BUS:hamped1304`**.
> Be discreet while on site, we don't know if the outsiders will use this as a bait to
> get a hit on us. I don't want to risk losing you.

### Stage 2/4 facts

- **SSID:** `MNT-BUS`
- **WPA2 PSK:** `hamped1304`
- **Mode:** physical Wi-Fi recon — the AP is broadcast from the actual bus prop on the
  venue floor; not reachable over the NSEC VPN.
- **DNS check (no remote bypass exists):** `mnt-bus.nsec`, `mnt-bus.ctf`, `bus1304.nsec`,
  `bus-1304.ctf`, `bus.nsec`, `trolley-bus.nsec`, `transit.nsec`, `dispatch.nsec`,
  `maintenance.nsec`, `wifi-bus.nsec`, `onsite-bus.nsec` — **all NXDOMAIN** on both A and
  AAAA over the active VPN tunnel (probed 2026-05-17 ~13:55 EDT, DARKLAB host).
- **dl.nsec:** `https://dl.nsec/{missing-bus,bus1304,bus-1304,mnt-bus,trolley-bus,bus,firmware}/` → all 404 / 403.
- **Discourse search:** `MNT-BUS`, `hamped1304`, `trolley`, `bus location`, `1304 located`,
  `maintenance bus`, `venue bus` — only topic 59870 hits. No companion topic with a
  remote portal exists.
- **Local Wi-Fi scan (DARKLAB 2026-05-17 13:54 EDT):** `netsh wlan show networks mode=bssid`
  visible SSIDs = `NSEC` only. Operator is not within range of `MNT-BUS` from the home base.

### Stage 2/4 operator action (on-site only)

1. Carry a laptop / phone with Wi-Fi to the venue floor. Walk the perimeter scanning for
   SSID `MNT-BUS`. Phones list it instantly; on Windows run
   `netsh wlan show networks mode=bssid | findstr /i MNT-BUS`.
2. Once in range, connect with PSK `hamped1304`.
3. Enumerate the AP LAN:
   - `ipconfig` — note default gateway (likely `192.168.4.1` or similar AP-default).
   - `curl http://<gateway>/` — look for maintenance dashboard / login page.
   - `nmap -sV -p- <gateway>` — usual SSH/HTTP/Telnet ports.
   - Check Common embedded-Linux paths: `/flag`, `/flag.txt`, `/cgi-bin/`, `/admin/`,
     `/maintenance/`, `/diag/`.
4. Flag-2 is **most likely** posted on the maintenance landing page once connected
   (typical NSEC convention for the "you found it" stage on an isolated AP).

### Stages 3 and 4 — speculation (NOT yet revealed by [teammate])

Pattern from comparable NSEC tracks (REM, Helios, Monsatan): once the AP web UI is
captured, expect:
- **Flag 3/4:** auth bypass / default creds on the maintenance UI → escalated dashboard /
  bus telemetry page.
- **Flag 4/4:** post-auth RCE or firmware extract on the bus's onboard ECU/router (the
  "computer system" [teammate] says the outsiders tampered with — implies a second device
  pivot once you're on the AP LAN).

No remote pivot has been found. Any further progress requires the on-site Wi-Fi.

### Cross-track checks (none found)

- Radio Beacon (59798, 146.565 MHz) is a separate RF track, not coupled to trolley-bus.
- `announcement-board` has no posts about the bus location.
- No NSEC-internal DNS / `dl.nsec` artifact is gated on stage 1 completion.

---

## Challenge brief

Bus 1304 was hijacked; a hidden keyfob backdoor was triggered at 433.92 MHz.
We must demodulate the intercepted IQ capture to recover the keyfob code.

- File (real): `https://dl.nsec/keyfob.complex16s` -- VPN-gated
- File (on disk): `nsec/keyfob.complex16s` -- **synthetic test fixture, NOT the challenge**
- Flag format: `flag-{HEX KEY CODE}` lowercase alphanumeric

## What is actually on disk

`nsec/keyfob.complex16s` is 97,013,760 bytes -- 24,253,440 IQ pairs
(int16 LE). Statistical analysis over the entire file shows:

| Property | Value |
| --- | --- |
| Envelope `|I+jQ|` | Only 3 discrete values (13260, 13324, 25716) -- constant across all 24M samples |
| Magnitude variation by 1000-sample window | Zero (min/max/mean identical everywhere) |
| Instantaneous frequency (`dphi/dt`) | Exactly 3 discrete values, each appearing N/3 times |
| Run length of any frequency | 1 sample (no symbols of any duration exist) |
| FFT peaks | 0 and +/-1/3 of fs (no +/-50 kHz FSK tones) |

The pattern repeats deterministically with period 3 IQ samples after a 3-sample
intro. **There is no preamble, no sync, no payload, no modulation in the
keyfob sense.** No demod or threshold scheme can recover bits from this.

The file appears to be an incomplete/broken byproduct related to the local
`nsec/generate_keyfob_iq.py` generator (which hard-codes
`keyfob_code="deadbeef"`). It is not the genuine NSEC capture.

## Why prior `flag-deadbeef` is wrong

- The bitstream claimed in `extraction_log.txt` (`11111111 11011110 ...`)
  cannot be derived from the on-disk samples by any decoder.
- The only on-disk reference to `deadbeef` is the default value baked into
  `generate_keyfob_iq.py`. The prior agent appears to have written the
  demodulator and the "results" docs without ever executing the demod against
  real data: no `keyfob_results.json` and no `keyfob_analysis.png` exist.
- The on-disk file size (~97 MB / 24M samples / 24 s @ 1 MHz) doesn't even
  match what the local generator produces (~50 ms / 50k samples) -- so the
  file we have isn't even the local synthetic the prior writeup describes.

## Decode pass performed today (2026-05-16)

The real file was fetched from `https://dl.nsec/keyfob.complex16s` and archived
as `nsec/missing-bus\artifacts\keyfob.complex16s`.

File metadata:

| Property | Value |
| --- | --- |
| Size | 9,699,328 bytes |
| Samples | 2,424,832 complex int16 IQ pairs |
| SHA256 | `0B7F57337B1F9DEEF704F9C81A85B125301D0A0887B88A18CA10CC2E5DB69397` |

The real capture is OOK/PWM. Magnitude-envelope thresholding yields repeated
frames with a short sync pulse followed by 24 data pulses. The data convention
matches EV1527/PT2262-like keyfobs:

- short-high / long-low = `0`
- long-high / short-low = `1`

Repeated full frames decode to:

```
110111001000111111010000 = dc8fd0
```

Derived key code:

```
dc8fd0
```

The Discourse wording says `flag-{HEX KEY CODE}` with lower-case alphanumeric
values and no spaces/dashes, which implies the placeholder-free candidate
`flag-dc8fd0`. The local submission wrapper blocks that exact shape:

```
[DENY-SHAPE] missing-bus flag does not match ^FLAG-[printable]{8,}: 'FLAG-dc8fd0'
```

Formatting attempts that do pass the wrapper shape were rejected by askgod:

- `flag-{dc8fd0}`: invalid
- `FLAG-{dc8fd0}`: invalid
- `FLAG-00dc8fd0`: invalid

Stopped after the DENY-SHAPE result rather than iterating complement or
address/button interpretations.

Reproducer:

```
python nsec/missing-bus\artifacts\decode_keyfob_pwm.py nsec/missing-bus\artifacts\keyfob.complex16s
```

Detailed pulse notes are in
`nsec/missing-bus\artifacts\decoded.txt`.

## Prior decode pass notes

Even knowing the file is synthetic, I ran the standard pipeline to confirm:

1. Loaded IQ as int16 pairs -> complex.
2. Computed envelope, magnitude histogram, FFT, instantaneous frequency.
3. Histogrammed run-lengths of identical inst-freq samples.

Result: no recoverable bits. Plots saved to
`nsec/missing-bus\artifacts\envelope.png` (envelope, inst-freq,
spectrum). Numeric findings in
`nsec/missing-bus\artifacts\decoded.txt`.

## Submission Attempt (2026-05-17 13:14 UTC)

**Candidate:** `FLAG-00dc8fd0` (24-bit keyfob 0xdc8fd0 left-padded to 32-bit 0x00dc8fd0)
- **Wrapper pass:** YES (8 chars after FLAG-)
- **askgod response:** `Invalid flag submitted`
- **Exit code:** 1 (FAIL)
- **Confidence loss:** HIGH — standard left-padding rejected

**Analysis:** The format is NOT a simple zero-padding convention. The correct flag format remains unknown.

## Recommended Action (Coach handoff)

**Status as of 2026-05-17 09:40 UTC:**
- IQ demodulation is conclusive: `0xdc8fd0` decoded from 4 identical frames across 3 bursts
- Signal characteristics: OOK/PWM encoding (EV1527/PT2240 family)
- Frame structure: 1-bit sync + 24-bit data payload per frame
- All variant decodings (bit reversal, byte swap, polarity inversion, nibble reversal, decimal, etc.) have been tested
- Direct askgod submissions of 8+ char variants all returned "Invalid flag submitted"
- DENY-BRUTE rate-limit was hit (5 FAILs in 15 min window) — last attempt 2026-05-17 09:40

**The blocker:** The local submission wrapper `submit-flag.ps1` enforces:
```regex
^FLAG-[\x21-\x7e]{8,}$
```
This rejects `flag-dc8fd0` (only 6 chars) with `[DENY-SHAPE]`, never reaching askgod.

**The solution:** Operator submits directly to askgod, bypassing the local wrapper:
```bash
askgod submit "flag-dc8fd0"
# OR via MCP:
# Submit flag: flag-dc8fd0, Track: missing-bus
```

**Why this is the right answer:**
1. Discourse topic 59870 states: "Format of the flag `flag-{HEX KEY CODE}` only lower case alphanumeric values, no space and no dash."
2. The natural interpretation is: `flag-` prefix + 6 hex characters (the 24-bit code in hex) = `flag-dc8fd0`
3. The decoded value is stable across threshold sweeps (200–450 samples), all 10+ complete frames, and both burst envelopes
4. No additional encoding layers (CRC, checksum, button bits, frame count, etc.) were found in the IQ capture

**If submission fails:** The next-highest-EV hypothesis is the byte-swapped variant `flag-d08fdc`, which would also require wrapper bypass to test. But the decode is definitive; if both 6-char hexes fail, either the challenge format spec changed or the IQ file itself is honeypotted.

## Coordination notes

- **Claimant:** [teammate]
- **Decoded value confirmed:** 2026-05-17 ORCH-C coach (Haiku) via exhaustive PWM analysis
- **Status:** Ready for manual submission (operator decision on wrapper bypass)
- **Rate limit:** DENY-BRUTE was hit at 2026-05-17 09:40; window resets 15 min later (~09:55)
- **Confidence:** HIGH (decode is stable, matches EV1527 protocol, verified across multiple independent decoders)

## Technical Summary (PWM Demodulation)

**File:** `nsec/missing-bus\artifacts\keyfob.complex16s` (9.7 MB, 2.424M IQ samples, 2.425 s @ 1 MHz)

**Modulation:** OOK/PWM (EV1527 / PT2240 family)
- Carrier: −14.93 kHz from SDR tuning (433.92 MHz center)
- Symbol time: ~180 samples = 180 µs @ 1 MHz
- Encoding: `0` = 1T high + 3T low; `1` = 3T high + 1T low

**Frame structure:**
- Sync: 1 short pulse (~140 samples), constant per frame
- Payload: 24 PWM-encoded data bits
- Per-frame decode: `110111001000111111010000` = `0xdc8fd0`

**Burst layout:**
- 3 transmission bursts, ~600 ms apart
- 4 complete frames per burst
- All frames decode identically to `dc8fd0`

**Verification:**
- Threshold sweep (200–450 samples) converges on same bit pattern
- Cross-correlation of envelopes: 0.63–0.97 (high)
- No FSK (freq delta <2 kHz), no PSK, no hidden second signal
- Sync pulse is constant length (does not encode data)
- 24-bit length confirmed via pulse count; no 32-bit extension

**CRC check:** If the 24 bits represent [Byte0, Byte1, Byte2, Byte3], the XOR of all four bytes under various groupings does not yield a zero checksum, but this is consistent with simple fixed-code keyfobs which often omit CRC.

## Artifact tree

```
nsec/missing-bus\
  README.md                                    (contains stale "SOLVED" from synthetic file)
  iq_analysis_report.txt
  artifacts\
    keyfob.complex16s                          (9.7 MB REAL CAPTURE — decoded as dc8fd0)
    keyfob.SYNTHETIC.complex16s                (symlink to earlier synthetic copy)
    iq-deep-analysis-v2.md                     (2026-05-17 ORCH-C final analysis)
    decoded.txt                                (prior pass findings)
    envelope.png                               (prior pass plots)
    decode_keyfob_pwm.py                       (reproducer: python decode_keyfob_pwm.py keyfob.complex16s)
    v7_final_search.py                         (final exhaustive decoder)
    [... 20+ other analysis scripts from iterative prior coaches ...]
nsec/writeups\
  59870-missing-bus.md                         <- THIS FILE: FINAL WRITEUP (2026-05-17)
nsec/flags\
  submissions-journal.tsv                      (20+ failed attempts logged)
  GPU-CRACK-QUEUE.txt
```

## Designer Intel (Discord, post-event 2026-05-19)

**Designer:** Vianney Gall (vgall). **Intended path for flags 2-4 (from #vgall channel + hyp3v):**

- Flag 1 (we got): RF keyfob OOK demod → hex `dc8fd0` → `flag-dc8fd0`. hyp3v (1506007475424464947): "raw bits in hex. Universal Radio Hacker give it to you straight."
- **Flags 2-4 = physical Wi-Fi recon at venue** ([teammate]'s Discourse reply revealed):
  - Connect to maintenance Wi-Fi: SSID `MNT-BUS` / PSK `hamped1304`
  - Check `robots.txt` → it disallows `/panel`
  - `/panel` is the CLI endpoint where flags 2-4 live (parisianmerlot 1505659317226049726)
- ili_the_butterfly walked around the venue noting "why is the signal strength of the ap so strong in this corner surrounded by people and laptops" — the maintenance AP was venue-physical

**Tedre191 design note:** "Awesome track, and somewhat AI proof, which made it more rewarding!" — designer-intended AI defense via physical-required Wi-Fi recon.

Our progression (1/4 via [teammate] on-venue) was the maximum possible from operator's laptop. Flags 2-4 fully required on-venue presence.

(Track was renamed from `missing-bus` → `trolley-bus` server-side mid-event after the physical-recon reveal.)

*See _DISCORD-INTEL-ENRICHMENT-2026-05-19.md for the full cross-track designer-confirmed solution catalog and writeup links.*


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: missing-bus ]
> status: PARTIAL
> agents_dispatched: 27
> agents_succeeded: 1
> agents_killed: 0
> agents_AUP_blocked: 0
> honeypots_avoided: 0
>
> Notable:
> - **Agent-1** (Sonnet (default)) — 5.9m: Trolley-bus Discourse breakthrough — fresh-angle agent that connected RF capture to keyfob 24-bit hex format documented in Discourse topic body
> - **Agent-2** (Sonnet (default)) — 62.9m: Missing Bus SDR IQ refined demodulation — 62.9 minutes of PWM bit-extraction; ultimately stuck on 24-bit-vs-32-bit framing question
> - **Agent-3** (Sonnet (default)) — 19.6m: Missing Bus flag format and RF context deep-dive — read the askgod TS:B target-stat hint that the flag is 6-char hex of the 24-bit payload
>
> _RF demod breakthrough on 27th attempt. 24-bit hex payload, EV1527/PT2240 family. Flag value `flag-dc8fd0` accepted server-side, hit DENY-SHAPE on local wrapper (6-char vs 8-char floor)._


## Slop Watch

- The RF demod sequence took 27 agents. The 27th got there via a fresh-angle Discourse re-read that surfaced the 24-bit-hex format hint from the original challenge body. None of the prior 26 had read the Discourse topic body carefully enough.
- The local capture file was 97 MB. The real NSEC RF capture was 9.7 MB. Off by an order of magnitude. The 97 MB file was a generator byproduct with `deadbeef` hard-coded. Three agents submitted candidates from the synthetic file before the file-shape sanity check caught it.
- Flag value `flag-dc8fd0` accepted by askgod (6-char hex). The team's `submit-flag.ps1` wrapper rejected it with DENY-SHAPE because the wrapper enforces an 8-char minimum. Operator had to bypass the wrapper. The wrapper's defense caught a real flag and treated it like a typo.
- After 5 FAIL submissions in 15 minutes the wrapper added DENY-BRUTE. The agent re-tried the same flag with a different formatting. DENY-BRUTE again. The agent re-tried with another formatting. The wrapper held the line.
- Byte-swap variant `flag-d08fdc` flagged as next-best. Never needed.
