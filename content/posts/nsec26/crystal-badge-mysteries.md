+++
title = "The Crystal (badge) mysteries — 3/6"
date = 2026-05-20
categories = ["nsec26"]
tags = ["agent-slop", "hardware", "partial"]
slop_level = "moderate"
model = "Opus 4.7"
draft = false
+++

# The Crystal (badge) mysteries (Topic 59510)

Status: **PARTIAL** — 3/6 sub-flags captured (1/5 OCR + 2/5 post-dock firmware string + 1/1 Basic Electricity physical onsite)

This writeup covers the Crystal Badge track (Discourse topic 59510) including its two paired askgod tags: `badge-firmware` (5-flag bonus track tied to the badge itself) and `crystal` (1-flag Basic Electricity physical-onsite). The separate `grid-alignment` track (Discourse topic 59906, VQE + QAOA quantum hum) is documented in `59906-crystal-quantum-hum.md` and is NOT covered here.

## Context

> Hey my friend! About that thing… the crystal device… badge that we carry as a personal solar-powered battery. There is a mystery or two on it! Here's a first one. You'll be on your own for the rest, but have fun exploring! And be proud to wear it! Renewable and wearable energy, this is high-tech fashion!
>
> You can start with this puzzle. [teammate] reported that scavengers explored an abandoned lab site. They helped with the creation of the wearable crystal. One thing that was found was some top-secret-looking folders with S1 markings (no idea what that means but sounds important). One paper was in bad shape, making the text near impossible to read. I've used some tools to salvage the text, but there's stuff missing. I am sure it's about the crystal device! See if you can cross-reference with other public sources to fill in the important bits.
>
> `____ __ _ ___ ______ ___ _______ an advanced communication devices for _ _____ ______ _______ _______ _ _ ______ _____ _ ______________ ___ ____ ____________ ___________ ____ __ ____ ______ _ _______ __ particularly useful for community building in off-the-grid conditions. ___ __ _____ _________ ______ ____________ _____ with the architecture provena ...`

**CTF**: NorthSec 2026 — "Gaïa / Solarpunk Guardians"
**Category**: Hardware / OSINT / Badge RE
**Difficulty**: Easy (puzzle 1) → Medium-Hard (bonus tracks via on-badge serial / NFC / SAO / venue dock)
**Working dir**: `nsec/crystal-badge\`

## Recon

### Phase 0 — OCR puzzle triage (2026-05-15)

The ships with a **single salvaged S1-lab document** with most letters redacted — a fill-in-the-blanks crossword that, once cracked, yields the flag of the form:

```
FLAG-[1]-[2]-[3]-[4]-[5]-[6]-[7]   (all lowercase, alphanumeric)
```

The puzzle text describes "an advanced communication device for community building in off-the-grid conditions" — i.e., the **NSEC 2026 badge** itself. Each blank refers to a hardware fact verifiable from public Espressif/ST docs AND from the physical badge.

| Blank | Hint phrase | Answer | Justification |
|---|---|---|---|
| [1] | "platform … programming using sketches" | **`arduino`** | Arduino IDE sketches |
| [2] | "built around the ____2_3 chip" | **`esp32s3`** | Confirmed by `esptool chip-id`: ESP32-S3 QFN56 rev v0.2 |
| [3] | "ST2______ provides … near field communications" | **`st25r3916`** | High-perf NFC reader/writer IC (ST datasheet) |
| [4] | "two ___ ports on each side" | **`sao`** | Shitty Add-On V1.69bis connectors (carried from 2025 badge) |
| [5] | "_______ supports read-flash and chip-id … from pip" | **`esptool`** | `pip install esptool` |
| [6] | "transmit B23 … receive Address ___" | **`b24`** | Adjacent edge-connector pin: B23=TX → B24=RX |
| [7] | "size of the flash is limited to only __ Mb" | **`16`** | Confirmed by `esptool flash-id`: 16 MB |

### Phase 1 — Physical badge inspection

**Form factor**: Hexagonal PCB with leaf/petal cutouts, green silkscreen, ~10 cm point-to-point.

**Front (decorative)**:
- "nsec" branding (silkscreen, large white)
- 4 character portraits at top: [teammate] / [npc] / Woonderk1nd / Axolotzl (same set as Trading Cards challenge 59726)
- Central stylized sun with radiating rays (Gaïa / solarpunk theme)
- Lanyard attached

**Back (functional)**:
- ESP32-S3 module (bottom-left, labelled "ESP32")
- LiPo battery pack (top-right, pouch w/ red+black wires) — untethered operation
- Large yellow disc, center = NFC antenna (ST25R3916 trace)
- ~18 SMD NeoPixel RGB LEDs around the perimeter
- Gold card-edge connector on the right = e-ink display module slot
- USB-C port (bottom-right)
- Tactile SMD buttons (BOOT/RESET + user inputs)
- Two SAO V1.69bis connector footprints

### Phase 1 chip + flash identification

```text
esptool v5.2.0
Chip type:          ESP32-S3 (QFN56) (revision v0.2)
Features:           Wi-Fi, BT 5 (LE), Dual Core + LP Core, 240MHz, Embedded PSRAM 8MB (AP_3v3)
Crystal frequency:  40MHz
USB mode:           USB-Serial/JTAG
MAC:                d0:cf:13:13:ea:c0
Detected flash size: 16MB
```

This confirms blank [7] = `16` and the puzzle's reference to `esptool` / pip / sketches / SAO ports / ESP32-S3.

### Submitted flag — Phase 1 (1/5)

```
FLAG-arduino-esp32s3-st25r3916-sao-esptool-b24-16
```

→ askgod #105 — `[badge-firmware] Information recovered! (1/5)` — 2 pts at 2026-05-16T03:07:24Z.

## Exploitation

### Phase 2 — Full flash dump (16 MB)

First attempt at 921600 baud failed at 21% with `Corrupt data, expected 0x1000 bytes but received 0xff3 bytes` — classic USB-CDC framing glitch on the ESP32-S3 native USB CDC peripheral when pushed too fast.

Retry at default 115200 baud + `--no-stub` (avoids loading the stub flasher that competes for the USB peripheral) — reliable but slow (~25 min for the full 16 MB).

```bash
python -m esptool --port COM3 --no-stub read-flash 0 0x1000000 badge_flash_16MB.bin
```

→ `artifacts/badge_flash_16MB.bin` — 16,777,216 bytes, SHA256 `a772ee6382afb6982eb9e35f110f6fc6760e9d27db94027501cc13c078456316`.

### Phase 3 — eFuse summary

- **No flash encryption, no secure boot** → full firmware readable
- `RD_DIS=0`, `WR_DIS=0` → all blocks readable/writable
- `BLOCK_USR_DATA` = all 0x00 (no per-badge user secret stored)
- `OPTIONAL_UNIQUE_ID` = `06 0b ba f5 d2 b7 30 00 8c 92 93 ce a8 36 29 19`
- PSRAM: 8 MB AP_3v3, Vendor: AP

### Phase 4 — Partition table (parsed from 0x8000)

| Offset | Size | Type | Label |
|---|---|---|---|
| 0x9000 | 24 KB | data | **nvs** |
| 0xf000 | 4 KB | data | phy_init |
| 0x10000 | 1.25 MB | app | **conference** (ACTIVE pre-dock) |
| 0x150000 | 1.25 MB | app | **ctf** (EMPTY 0xFF pre-dock — awaiting venue flash) |
| 0x290000 | 8 KB | data | otadata |
| 0x292000 | 64 KB | data | coredump |
| 0x2a0000 | 1.4 MB | data | **spiffs** (animations, lang files) |

### Phase 5 — Live serial (conference firmware)

Prompt is `> ` (default ESP-IDF `esp_console`). Subsystem map from firmware string at 0x11566: `controller, cli, buttons, nfc, dock` — five subsystems initialize in conference mode.

**Hidden test commands confirmed** (not in `help`): `docktest`, `pairtest`, `lighttest`, `animate` (accepts `<name|number>`, `load <json>`, `off`), `?` (alias for help).

**All 2025-source-derived CTF commands** (`firmware_select`, `nvs_*`, `quantum`, `qkd`, `sweep`, `mqtt`, `trivia`, etc.) → return `Unknown command` in 2026 conference firmware. The 2026 conference build was reorganized to be more restrictive than 2025; CTF-mode commands exist only in the dormant `ctf` partition that requires venue-flashing.

**Pre-flash NVS contents** (8 namespaces enumerated):
- ns=3 `wifi`: `ssid=NSEC-13EAC0`, `pass=pyfh49Jwld` (captive-portal credentials)
- ns=5 `ap.ssid` / `ap.passwd`: mirror of above
- ns=7 `contacts`: paired-attendee profile records (real NFC pairings)
- ns=8 `pairs`: 7 paired MACs
- ns=1 `social`: XOR-obfuscated JSON `{"social":21,"sponsor":0,"light":255}` (key = WiFi MAC from `phy/cal_mac`, named by firmware string `[social] SET %s = %u (stored with HWID XOR)`)

**Pairing key found in firmware strings**: `NSEC2026BADGE_PAIR_SECRET_KEY!!!` — used as HMAC key in NFC-DEP pairing verification. Plaintext-in-firmware secret → forge-and-replay attack to bump social value without physical pairing is offline-feasible.

### Phase 6 — Captive portal trigger discovered

`animate wifi_portal` plays the animation but does NOT start the SoftAP. After extensive probing, the trigger was found to be the **NFC mode menu cycle**: Reader → Pair → Emulator (NTAG213) → **WiFi Emulator**. Reaching the WiFi Emulator state fires `portal_start()` (callsite at flash offset 0x42010fd0).

```
[portal] AP started: SSID="NSEC-13EAC0" pass="pyfh49Jwld" @ 192.168.4.1
```

Phone connected to portal → captive-portal HTML form (`/api/config` for profile push, `/api/contacts`, `/api/social`) — exposed in firmware extraction (`captive_portal.html`, ~18.5 KB).

### Phase 7 — Venue dock flash (CTF firmware activation)

Operator dock-flashed the badge at ~10:30 EDT 2026-05-16. The dock issues an I²C opcode that triggers the badge to OTA-download the CTF firmware into the previously-empty `ctf` partition and reboot into CTF mode.

Dock I²C protocol (RE'd via Xtensa disasm — `artifacts/dock_re/dock_protocol.md`):
- Commands: `0x02`, `0x03`, `0x10`
- 7-entry opcode dispatch table at flash 0x42010af0
- Brute space if dock unavailable: 65 K

### Phase 8 — Post-dock CTF firmware extraction (Flag 2/5)

After dock-flash, operator dumped just the CTF partition:

```powershell
python -m esptool --port COM3 read-flash 0x150000 0x140000 ctf_postdock.bin
```

Strings categorization (`ctf-strings-extract.py`) on the 1.25 MB CTF binary surfaced **1 unique FLAG- candidate**:

```
FLAG-i-c4n-h4z-f1rmw4r3
```

**Anti-trap discipline applied** (`flag-context-check.py`):
- File offset: `0x13C3`
- Adjacent context: boot-banner block (`NorthSec Badge 2026 / BOOT SUCCESSFUL! / Build: May 14 2026 00:11:04 / Mode: ctf / FLAG-i-c4n-h4z-f1rmw4r3 / Animation filesystem initialized / ...`)
- Honeypot keyword scan in 1024-byte window: **0 hits** (no `test`/`example`/`dummy`/`placeholder`/`TODO`/`XXX`)
- Single occurrence in the binary

→ Submitted via wrapper → askgod #106 — `[badge-firmware] (2/5)` — 1 pt at 2026-05-16T10:42:43Z.

### Phase 9 — Basic Electricity physical-onsite (1/1 crystal)

The `crystal` askgod tag (separate from `badge-firmware`) is a single-flag track tied to a physical "Infinite Energy" scam crystal device at the venue. Operator captured it at 2026-05-16 09:04 EDT.

- askgod #37 — `[crystal] 1/1 GG. Infinite Energy is a Scam!` — 3 pts
- Flag value not recorded locally (physical onsite, manual operator submission)

## Captures

| Position | askgod # | Tag | Flag | Method | Timestamp |
|---|---|---|---|---|---|
| 1/5 | 105 | badge-firmware | `FLAG-arduino-esp32s3-st25r3916-sao-esptool-b24-16` | OCR puzzle decoded via Espressif/ST datasheets + NSEC 2025 source mining | 2026-05-15T23:07:00-04:00 |
| 2/5 | 106 | badge-firmware | `FLAG-i-c4n-h4z-f1rmw4r3` | Boot-banner string in `ctf_postdock.bin` after venue dock flash | 2026-05-16T10:42:00-04:00 |
| 1/1 | 37 | crystal | `FLAG-<onsite-physical-not-recorded-locally>` | Basic Electricity (Infinite Energy scam) — physical onsite | 2026-05-16T09:04:00-04:00 |

**Remaining (3/5 still open)**: badge-firmware 3/5, 4/5, 5/5 — likely require deeper CTF-firmware probing (post-flash NVS namespaces, ICS subsystem, full command dispatcher RE).

## STUCK / Blockers (active)

| Blocker | Current state | Next move |
|---|---|---|
| Post-dock NVS dump | Not yet captured — only the CTF app partition was pulled | Dump `0x9000` 24 KB post-dock to find new `ctf` / `quantum` / `qkd` / `codenames` namespaces |
| Post-dock SPIFFS dump | Not yet captured | Dump `0x2a0000` 1.4 MB post-dock — likely has CTF animations + hidden files |
| Full CTF command dispatcher RE | Strings extracted but strcmp cascade not yet RE'd | Continue Xtensa disasm of CTF binary at dispatcher entry |
| ICS subsystem (Plant Watering wiring) | `open_valve` / `mqtt_pub` strings present in CTF firmware but backend not reached | Connect to badge's ICS captive portal post-flash, MQTT scan |
| 2025-style commands in CTF firmware | Untested (`mystery`, `crystal`, `quantum`, `qkd`, `qkd-init`, `qkd2`, `unlocksafe`, `codenames`, `calibrate`, `sweep`, `mqtt`, `trivia`) | Probe each via serial REPL post-dock |
| Profile-save captive-portal trace | Operator can connect via phone but profile push not yet captured | Operator drives phone UI; coach captures `/api/config` POST |

## Anti-Trap Notes

- **Untrusted firmware artifact** — `ctf_postdock.bin` is vendor-flashed code not under our control; FLAG- candidates from such artifacts get full anti-trap discipline per `feedback_pi_content_quarantine` and `feedback_honeypot_flag_signatures`
- `FLAG-i-c4n-h4z-f1rmw4r3` passes all four anti-trap checks:
  - Single occurrence in binary (vs many = fixture)
  - 1024-byte honeypot keyword scan: 0 hits
  - Boot-banner adjacency (printed at boot, not buried in fixture text)
  - Build-banner co-location (same printf block as `Build:` date and `Mode: ctf`)
- OOB verification path: reboot badge → confirm flag appears in serial boot output → submit
- Submission confirmed via askgod (1 pt awarded, not DENY) — real flag, not honeypot
- Earlier framework-guess `FLAG-arduino-32-25dv-usb-esptool-c9-4` (2026-05-16 15:57) returned `Invalid flag submitted` — variant-guess on Flag 1/5 was wrong; the correct chain (`arduino-esp32s3-st25r3916-sao-esptool-b24-16`) had already been the right one
- 5 paired-attendee NFC contact records in `contacts/data` NVS blob are **real people**, not in-game NPCs — no OSINT per `feedback_badge_paired_contacts_are_real_people`
- Captive-portal credentials (`NSEC-13EAC0` / `pyfh49Jwld`) are per-badge specific and not for non-team Discord channels per `feedback_discord_only_bifftannen`

## Cross-Track References

- `grid-alignment` (Discourse topic 59906, VQE + QAOA) — completely separate quantum-tuning track that uses the badge as a substrate. Documented in `59906-crystal-quantum-hum.md`. NOT covered here.
- `plant-watering` (Discourse topic 58970) — Plant Watering ICS challenge wired through the CTF firmware's `[ics]` subsystem (`open_valve`, `mqtt_pub`). Same `ctf_postdock.bin` may surface clues.
- `trading-cards` (Discourse topic 59726) — 4 silkscreened portraits ([teammate] / [npc] / Woonderk1nd / Axolotzl) on the badge front match the four Trading Cards challenge characters.
- `nsec-2025-badge` (github.com/nsec/nsec-badge) — public 2025 ESP32-C3 source mined for command names + NVS conventions (`crystal-badge/artifacts/nsec_2025_intel.md`). 2025 commands largely absent from 2026 conference build but expected to reappear in CTF build.

## Artifacts

All under `nsec/crystal-badge\artifacts\`:

| File | Purpose |
|---|---|
| `photos/badge_pic{1,2}.jpg` | Component identification (front + back) |
| `badge_flash_16MB.bin` | Pre-venue full flash dump (16 MB, SHA256 `a772ee63...`) |
| `flash_dump.log` | esptool stdout, framing-error trace |
| `efuse_summary.txt` | No flash encryption, no secure boot, BLK3 user-data empty |
| `conference.bin` | Pre-flash conference firmware (1.25 MB, RE complete) |
| `nvs.bin` | Pre-flash NVS partition (24 KB, decoded) |
| `ctf.bin` | Pre-flash CTF partition (1.25 MB, all 0xFF — empty) |
| `ctf_postdock.bin` | **Post-flash CTF firmware (1.25 MB, populated — flag source)** |
| `coredump.bin` | Pre-flash coredump (64 KB) |
| `spiffs.bin` | Pre-flash SPIFFS (1.4 MB, 28 animation JSONs) |
| `captive_portal.html` | Extracted captive-portal HTML (18.5 KB) |
| `topic.json` | Discourse fetch (original challenge text + OCR puzzle) |
| `schematic-2025.pdf` | NSEC 2025 reference (SAO pinout, IR header, partition map conventions) |
| `firmware_re_deep.md` | Conference firmware RE writeup (Xtensa disasm, 11 cmds) |
| `nsec_2025_intel.md` | 2025 → 2026 source carry-over patterns |
| `animation_schema.md` | Complete JSON schema for `animate load` |
| `custom_animations.md` | 8 ready-to-load custom animations |
| `dock_re/dock_protocol.md` | I²C dock protocol RE (3 cmds, opcode table at 0x42010af0) |
| `dock_re/*.txt` | Xtensa disasm snapshots (ISR, on_event, dock_task, portal_start, opcode_table) |
| `dock_simulator_arduino.ino` | I²C-master Arduino sketch to simulate venue dock |
| `longpress_test.log` | Serial capture showing live portal-start event |
| `nvs_parser.py` | Pure-Python NVS partition reader |
| `decode-social-blob.py` | XOR-HWID decoder for `social/data` |
| `parse-partition-table.ps1` | ESP-IDF partition-table parser |
| `dump-ctf-partition.ps1` | esptool wrapper for post-flash partition dump |
| `inspect-ctf-bin.ps1` | First-look diagnostic (pre-flash, confirms all 0xFF) |
| `inspect-ctf-postdock.ps1` | Detailed scan of freshly-flashed CTF binary |
| `inspect-spiffs.py` | SPIFFS filename + keyword scan |
| `ctf-strings-extract.py` | Full strings categorization |
| `ctf_postdock_strings.txt` | 5748 strings from CTF binary |
| `ctf_postdock_summary.md` | Strings summary with subsystem tags + cmd candidates |
| `flag-context-check.py` | Honeypot-keyword scan around FLAG- candidate |
| `POST-FLASH-PLAYBOOK.md` | Three parallel harvest paths (REPL / dump / SPIFFS) |
| `NVS-DECODE-FINDINGS.md` | NVS namespace map + encoding writeup |
| `TEAM-CHEAT-SHEET-social-flags.md` | Teammate-facing reference card |
| `FLAG.txt` | Phase 0 draft (incorrect — superseded by submitted flag 1/5) |
| `INDEX.md`, `RESEARCH_NOTES.md`, `SOLUTION_SUMMARY.txt` | Earlier-session notes |

Parent dir (`crystal-badge/`):
- `BADGE-STATUS-20260517.md` — day-2 status snapshot
- `SUSPICIOUS.md` — anomaly log

Flag values archive: `nsec/flags\crystal-badge.txt`.

## Techniques Used

- **OSINT cross-reference** for OCR fill-in puzzle (Espressif catalog, ST product pages, NSEC GitHub)
- **USB enumeration sleuth** (`VID_303A&PID_1001` → ESP32-S3)
- **`esptool chip-id` / `flash-id`** for chip identification
- **`esptool read-flash --no-stub`** at low baud for reliability when stub flasher causes USB-CDC framing errors on ESP32-S3 native USB
- **ESP-IDF partition-table parsing** (offset 0x8000, 32-byte entries, magic `AA 50`)
- **Pure-Python NVS partition reader** — page state machine + entry bitmap + multi-entry blob reassembly
- **XOR-with-HWID obfuscation reversal** — firmware string named the technique; HWID = 6-byte WiFi MAC from `phy/cal_mac`
- **Boot-banner string locality** — single-occurrence FLAG- string adjacent to `BOOT SUCCESSFUL!` + `Mode: ctf` confirmed high confidence (vs honeypot)
- **NFC mode menu cycle probing** — discovered portal trigger requires reaching the WiFi Emulator state (4th in NFC mode cycle), not button long-press alone
- **Xtensa LX7 disasm** of firmware to RE dock I²C protocol + opcode dispatch table
- **2025 source mining** — pulled `github.com/nsec/nsec-badge` (2025 ESP32-C3 source) to predict 2026 command surface

## Tools Used

- `esptool.py` v5.2.0 — chip-id, flash-id, read-flash
- `espefuse` — eFuse summary
- `python3` + pure stdlib — NVS parser, XOR decoder, strings extractor, context analyzer
- `powershell` — partition-table parser, ESP-IDF image header inspector
- Custom Xtensa LX7 disassembler (`crystal-badge/artifacts/dock_re/xt_disasm.py`)
- Windows `Get-PnpDevice` for USB enumeration
- `python -m serial.tools.miniterm` for live serial

## Timeline

| Time (EDT) | Step | Outcome |
|---|---|---|
| 2026-05-15 22:56 | Discourse topic fetched | `topic.json` |
| 2026-05-15 23:11 | First-pass OCR solve (incorrect framework guess) | `artifacts/FLAG.txt` |
| 2026-05-16 02:48 | Badge USB-C plugged | Initially `Unknown Device`, wakes on button |
| 2026-05-16 02:51 | `esptool chip-id` succeeded | MAC + features captured |
| 2026-05-16 02:52 | `esptool flash-id` confirmed 16 MB | Validates blank [7] |
| 2026-05-16 03:07 | **Flag 1/5 submitted** | askgod #105: 2 pts |
| 2026-05-16 ~03:10 | 921600-baud dump failed at 21% | Switched to 115200 |
| 2026-05-16 03:47 | **Dump complete — 16 MB perfect** | SHA256 `a772ee63...` |
| 2026-05-16 03:50 | eFuse summary captured | No encryption, no secure boot |
| 2026-05-16 03:55 | Partition table decoded | `ctf` partition empty — awaits venue flash |
| 2026-05-16 04:00 | NVS extracted | SoftAP creds + 5 paired contacts |
| 2026-05-16 04:10 | Live serial session opened | Hidden cmds enumerated |
| 2026-05-16 04:25 | NSEC 2025 source mining done | All 2025 CTF cmds confirmed absent in 2026 conference firmware |
| 2026-05-16 04:30 | Custom `animate load <json>` accepted live | `animation_schema.md` published |
| 2026-05-16 05:00 | Firmware deep-mine done | 11 cmds; `portal_start()` at 0x42010fd0 |
| 2026-05-16 05:15 | Dock I²C RE done | Cmds 0x02/0x03/0x10; opcode table at 0x42010af0 |
| 2026-05-16 05:30 | **Live portal trigger confirmed** | NFC mode cycle → WiFi Emulator state fires `portal_start()` |
| 2026-05-16 05:45 | Phone connected to NSEC-13EAC0 | Captive portal HTML rendered |
| 2026-05-16 09:04 | **Basic Electricity flag captured onsite** | askgod #37: 3 pts (physical Infinite Energy scam) |
| 2026-05-16 ~10:30 | Operator dock-flashed badge | CTF partition populated |
| 2026-05-16 ~10:35 | `ctf_postdock.bin` dumped | 1.25 MB, valid ESP-IDF image |
| 2026-05-16 10:42 | **Flag 2/5 submitted** | askgod #106: 1 pt (boot-banner string) |

---

*Writeup last refreshed during NSEC 2026 archive consolidation (Q-2b). Crystal Badge-specific content only; the grid-alignment / VQE / QAOA track lives in `59906-crystal-quantum-hum.md`.*


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: crystal-badge ]
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
