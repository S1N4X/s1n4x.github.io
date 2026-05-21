+++
title = "Reduce, reuse, recycle — 8/8"
date = 2026-05-20
categories = ["nsec26"]
tags = ["agent-slop", "forensics", "solved"]
slop_level = "minor"
model = "Opus 4.7"
draft = false
+++

# Reduce, reuse, recycle (Topic 59330)

Status: **SOLVED** — 8/8 sub-flags captured

## Context

> Hey folks, I was chatting with the new owner at the coffee shop near the park. The guy had trouble with his new POS and I helped him set it up. Now he wants to offer the old one to the community so that people can go online like the old Internet cafes of before. Much better to reuse old equipment than trash it! I still had a strange feeling after cleanup, so I exported what remained from that old system into one file to get a second opinion. Would you mind checking it and telling me if our setup is actually safe for everyone who uses it? Download here: 533b25dbc5b0335cda2170239b85d97bdf9e3a1f789c3f276941153b29efa8a8.wim . Thank you, seriously, this little cafe runs on trust and good people. This is why we’re here.

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 3/8 | `FLAG-<not-recorded-locally>` | QR code as file paths | 2026-05-15T21:50:00-04:00 |
| 5/8 | `FLAG-<not-recorded-locally>` | Cleartext credentials in an unpacked python script | 2026-05-15T21:58:00-04:00 |
| 7/8 | `FLAG-<not-recorded-locally>` | XORing the coupon code for profit | 2026-05-15T22:05:00-04:00 |
| 6/8 | `FLAG-<not-recorded-locally>` | Konami Coffee anyone? | 2026-05-16T01:32:00-04:00 |
| 4/8 | `FLAG-<not-recorded-locally>` | Plaintext attack on password-protected zip | 2026-05-16T01:38:00-04:00 |
| 8/8 | `FLAG-<not-recorded-locally>` | Manager password and goat rental | 2026-05-16T01:39:00-04:00 |
| 1/8 | `FLAG-<not-recorded-locally>` | Unredacting a PDF | 2026-05-16T01:41:00-04:00 |
| 2/8 | `FLAG-<not-recorded-locally>` | Alternate data stream and paint layers | 2026-05-16T01:51:00-04:00 |

## Artifacts

- Artifact directory: `nsec/poubelle/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59330-reduce-reuse-recycle.md`

# Reduce, reuse, recycle (Topic 59330)

Status: **SOLVED** — 8/8 sub-flags captured

## Artifacts

- Artifact directory: `nsec/poubelle/artifacts`

### From `59330-reduce-reuse-recycle.md`

# NSEC 2026 — Reduce, Reuse, Recycle (59330) Writeup

**Challenge ID**: 59330  
**Category**: Forensics / Windows Incident Response  
**Difficulty**: Medium-Hard  
**Status**: In Progress (awaiting .wim file)  
**Date**: 2026-05-15  

## Challenge Summary

A Point-of-Sale (POS) Windows system image in WIM (Windows Imaging Format) containing:
- Hardcoded credentials
- Homegrown/custom cryptographic implementation
- Sensitive application data

**Objective**: Extract and analyze the filesystem, recover credentials, identify custom crypto, and extract flags.

## Challenge Artifacts

| Artifact | Status | Location |
|----------|--------|----------|
| `.wim` file | ⏳ Not downloaded | TBD (challenge server) |
| SAM registry hive | ⏳ To be extracted | `Windows/System32/config/SAM` |
| SYSTEM registry hive | ⏳ To be extracted | `Windows/System32/config/SYSTEM` |
| Custom crypto scripts/binaries | ⏳ To be analyzed | TBD (filesystem scan) |

---

## Analysis Methodology

### Phase 1: Mount & Examine Image

#### 1.1 Identify WIM Structure
```bash
# List contents of WIM file
wimlib-imagex info reduce-reuse-recycle.wim

# Or using 7z
7z l reduce-reuse-recycle.wim | head -50
```

**Expected output**: Index of images within the WIM, filesystem structure overview.

#### 1.2 Mount the Image
```bash
# Create mount directory
mkdir -p /mnt/wim_mount

# Using wimlib (Linux/Mac)
wimlib-imagex mount reduce-reuse-recycle.wim 1 /mnt/wim_mount

# Or extract using 7z
7z x reduce-reuse-recycle.wim -o/extracted_wim/

# Or on Windows, mount directly
# Right-click WIM → Mount image (Windows 10/11)
```

**Expected**: Full Windows filesystem accessible for inspection.

---

### Phase 2: Registry Hive Extraction & Password Cracking

#### 2.1 Locate Registry Hives
```bash
# From mounted filesystem
ls -la /mnt/wim_mount/Windows/System32/config/

# Key files:
# - SAM (user accounts & password hashes)
# - SYSTEM (encryption keys for SAM)
# - SECURITY (additional credentials)
# - NTDS.DIT (if domain controller)
```

#### 2.2 Extract SAM/SYSTEM
```bash
# Copy to working directory
cp /mnt/wim_mount/Windows/System32/config/SAM ./
cp /mnt/wim_mount/Windows/System32/config/SYSTEM ./

# Verify extraction
file SAM
file SYSTEM
```

#### 2.3 Dump NTLM Hashes
**Using impacket (Python):**
```bash
python3 -c "
from impacket.examples import secretsdump
secretsdump.main(['-sam', 'SAM', '-system', 'SYSTEM', '-hashes', 'LMHASH:NTHASH', 'LOCAL'])
"

# Or using secretsdump.py directly
secretsdump.py -sam SAM -system SYSTEM LOCAL
```

**Expected output**:
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
User1:1000:00000000000000000000000000000000:e52cac67419a6a5a6f8345c7fcadc1c1:::
```

#### 2.4 Crack NTLM Hashes
**Using Hashcat (GPU-accelerated)**:
```bash
# Format: hashcat -m 1000 <hash_file> <wordlist>
echo "8846f7eaee8fb117ad06bdd830b7586c" > hashes.txt

hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt --force

# Or with rules
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

**Using John the Ripper**:
```bash
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt

# Show cracked passwords
john --format=NT --show hashes.txt
```

**Expected**: Plaintext passwords recovered from NTLM hashes.

---

### Phase 3: Search for Hardcoded Credentials

#### 3.1 Scan Configuration Files
```bash
# Windows event logs
find /mnt/wim_mount/Windows/System32/winevt/Logs -name "*.evtx" -exec strings {} \; | grep -i "password\|credential\|api_key\|secret" | head -50

# IIS logs (if POS web interface)
cat /mnt/wim_mount/Windows/System32/LogFiles/W3SVC1/u_ex*.log | grep -i "password\|auth"

# Registry text exports
reg export HKLM\Software registry_export.txt 2>/dev/null
grep -r "password\|credential\|key" registry_export.txt
```

#### 3.2 Application Data Search
```bash
# Common credential storage locations
find /mnt/wim_mount/Users -name "*.config" -o -name "*.xml" -o -name "*.ini" | xargs grep -l "password\|cred" 2>/dev/null

# ProgramFiles search (POS software)
find /mnt/wim_mount/"Program Files" -type f \( -name "*.exe" -o -name "*.dll" -o -name "*.config" -o -name "*.xml" \) -exec strings {} \; | grep -E "password|api_key|token|secret" | sort -u
```

#### 3.3 Browser & Database Credentials
```bash
# Firefox stored logins
strings /mnt/wim_mount/Users/*/AppData/Roaming/Mozilla/Firefox/*/logins.json

# Chrome stored passwords (encrypted, may require additional extraction)
find /mnt/wim_mount/Users -name "Local State" -path "*/Chrome/*" -exec cat {} \;

# SQL Server / MySQL connection strings
find /mnt/wim_mount -name "*.config" -exec grep -i "connection" {} + | grep -i "password"

# RDP history
strings /mnt/wim_mount/Users/*/NTUSER.DAT | grep -i "rdp\|server"
```

---

### Phase 4: Identify Custom Cryptography

#### 4.1 File Extension & Binary Signature Analysis
```bash
# Search for unusual file extensions
find /mnt/wim_mount -type f ! -name ".*" | sed 's/.*\.//' | sort | uniq -c | sort -rn

# Look for custom crypto indicators
find /mnt/wim_mount -type f -name "*.enc" -o -name "*.crypt" -o -name "*.cipher" -o -name "*.key" -o -name "*.priv"

# Hex dump suspicious files
xxd /mnt/wim_mount/path/to/suspicious.bin | head -50
```

#### 4.2 Source Code & Script Analysis
```bash
# Search for crypto-related keywords
find /mnt/wim_mount -type f \( -name "*.py" -o -name "*.js" -o -name "*.cs" -o -name "*.vb" \) -exec grep -l "encrypt\|decrypt\|cipher\|aes\|rsa\|des" {} \;

# Extract and analyze
strings /mnt/wim_mount/path/to/crypto_app.exe | grep -E "encrypt|decrypt|key|cipher|algorithm"
```

#### 4.3 Executable String Analysis
```bash
# Search binaries for hardcoded crypto keys, algorithms
strings /mnt/wim_mount/"Program Files"/*/bin/*.exe | grep -E "^[A-Fa-f0-9]{16,}$|^[A-Za-z0-9+/]{20,}$" | head -50

# Look for crypto library imports
strings /mnt/wim_mount/"Program Files"/*/bin/*.dll | grep -i "crypto\|cipher\|bcrypt\|openssl"
```

#### 4.4 Reverse Engineering Suspect Binaries
```bash
# If custom executable found
file /mnt/wim_mount/path/to/custom_crypto.exe
strings /mnt/wim_mount/path/to/custom_crypto.exe > crypto_strings.txt

# Use Ghidra or IDA to analyze
# Look for: key derivation functions, encryption loops, hardcoded constants
```

---

### Phase 5: Event Log & Activity Timeline

#### 5.1 Parse Windows Event Logs
```bash
# Extract event logs (EVTX format)
find /mnt/wim_mount/Windows/System32/winevt/Logs -name "*.evtx"

# Convert to text (using python-evtx or Tools)
python3 -m pip install python-evtx
python3 -c "
from Evtx.Evtx import Evtx
log = Evtx('/mnt/wim_mount/Windows/System32/winevt/Logs/Security.evtx')
for record in log.records():
    print(record.xml())
" | grep -i "password\|credential\|crypto\|access" | head -50
```

#### 5.2 Recent Files & Temp Directories
```bash
# Recently used files
strings /mnt/wim_mount/Users/*/AppData/Roaming/Microsoft/Windows/Recent/*.lnk

# Temp directories
find /mnt/wim_mount/Windows/Temp -type f | xargs ls -lah
find /mnt/wim_mount/Users/*/AppData/Local/Temp -type f | xargs ls -lah

# Recycle Bin
find /mnt/wim_mount/'$Recycle.Bin' -type f
```

---

### Phase 6: Data Extraction & Decryption

#### 6.1 Identify Encrypted Data
```bash
# High-entropy files (likely encrypted/compressed)
find /mnt/wim_mount -type f -exec sh -c 'entropy=$(xxd {} | awk "{s+=\$2} END {print s/NR}" | grep -oE "[0-9.]{1,5}"); if [ \$entropy -gt 7 ]; then echo "{} (entropy: \$entropy)"; fi' \; 2>/dev/null | head -20
```

#### 6.2 Attempt Decryption
```bash
# If passphrase/key found, decrypt using identified algorithm
# Example: AES-256-CBC with extracted key
openssl enc -aes-256-cbc -d -in encrypted_file -K <hex_key> -iv <hex_iv> -out decrypted_file

# Or use custom decryption script if algorithm identified
python3 decrypt_custom.py --input encrypted_data --key <passphrase>
```

---

## Suspected Findings (Placeholder)

| Category | Status | Finding |
|----------|--------|---------|
| Username | ⏳ Pending | TBD from SAM |
| Password Hash | ⏳ Pending | TBD from SAM |
| Cracked Password | ⏳ Pending | TBD from hashcat/John |
| Hardcoded API Key | ⏳ Pending | TBD from config files |
| DB Connection String | ⏳ Pending | TBD from app config |
| Custom Crypto Algorithm | ⏳ Pending | TBD from binary analysis |
| Encryption Key | ⏳ Pending | TBD from source/binaries |
| Encrypted Payload | ⏳ Pending | TBD from filesystem |

---

## Flags

### Flag 1: Hardcoded Administrator Password
**Source**: SAM hash cracking or plaintext in config  
**Value**: `FLAG{...}`  
**Extraction Method**: TBD

### Flag 2: Custom Crypto Algorithm Details
**Source**: Binary analysis / source code review  
**Value**: `FLAG{...}`  
**Extraction Method**: TBD

### Flag 3: Encrypted POS Data Decryption
**Source**: Decrypted database / application data  
**Value**: `FLAG{...}`  
**Extraction Method**: TBD

---

## Tools & Resources

### Pre-requisites
```bash
# Registry hive parsing
pip3 install impacket python-evtx

# Password cracking
sudo apt-get install hashcat john

# WIM mounting
sudo apt-get install wimtools     # Linux
brew install wimlib              # macOS
# Windows: built-in mount capability

# Binary analysis
# Ghidra (https://ghidra-sre.org/)
# IDA Free (https://www.hex-rays.com/ida-free/)
```

### Command Reference
```bash
# Mount WIM
wimlib-imagex mount image.wim 1 /mnt/wim

# Extract hashes
secretsdump.py -sam SAM -system SYSTEM LOCAL

# Crack with hashcat
hashcat -m 1000 hashes.txt rockyou.txt

# Crack with John
john --format=NT hashes.txt

# Search for keywords
grep -r "password\|credential\|key" /mnt/wim --include="*.xml" --include="*.config" --include="*.ini"

# Entropy analysis
find . -type f -exec sh -c 'entropy=$(...); [ $entropy -gt 7 ] && echo ...' \;
```

---

## Timeline & Notes

- **2026-05-15 21:00 EDT**: Challenge identified in triage
- **2026-05-15 22:00 EDT**: Writeup framework prepared (awaiting .wim file download)
- **Status**: Blocked on file availability — ready for Phase 1 execution once .wim is present

---

## Next Steps

1. **Download** `.wim` file from challenge server (approx. size TBD)
2. **Mount** using wimlib-imagex or 7z
3. **Execute** Phase 1–2 (registry extraction & password cracking)
4. **Conduct** Phase 3–4 (hardcoded creds, crypto analysis)
5. **Update** findings table and flags above
6. **Validate** all flags and submit to challenge server

---

**Writeup Status**: Framework ready • Awaiting challenge file  
**Last Updated**: 2026-05-15 22:15 EDT


### From `poubelle.md`

# NSEC 2026 — Poubelle / Reduce, Reuse, Recycle (59330) Writeup

**Track**: poubelle  
**Topic ID**: 59330  
**Category**: Forensics / Reverse Engineering / Cryptography  
**Difficulty**: Medium-Hard  
**Status**: 8/8 flags submitted (31 points total)  
**Date**: 2026-05-16

## Challenge Summary

A WIM (Windows Imaging Format) snapshot of a coffee shop POS workstation that the previous owner intended to recycle to the community. The user "goats" Shift-Deleted (or recycled) a number of sensitive files thinking that emptied the trash. The challenge ships only the user's `recycle_bin` folder with `$I*` metadata, `$R*` payload files, and one file carrying a hidden NTFS Alternate Data Stream.

Eight flags chain together forensics (Recycle Bin parsing, NTFS ADS), crypto (custom RC4, XOR with the window title, AES-GCM + PBKDF2, plaintext zip attack), and reverse engineering (PyInstaller + uncompyle6/xdis on the POS Tkinter app).

## Score breakdown

| # | Title | Pts | Hint | Method |
|---|---|---|---|---|
| 1 | Unredacting a PDF | 4 | Whoever approved the goats is not anonymous anymore | PDF text/redaction recovery |
| 2 | Alternate data stream and paint layers | 4 | I will be the first person to use that promo code | NTFS ADS `:promo` → HEIF iovl multi-layer |
| 3 | QR code as file paths | 4 | Plotting coordinates on a map is not encryption | Recycle Bin `$I` file metadata → binary coords → QR |
| 4 | Plaintext attack on password-protected zip | 4 | I was told to stop comparing myself to others | bkcrack known-plaintext on the zip |
| 5 | Cleartext credentials in an unpacked python script | 4 | Storing cleartext credentials is a questionable decision | pyinstxtractor + xdis bytecode strings |
| 6 | Konami Coffee anyone? | 3 | Cleartext instruction leading to authentication by nostalgia | Konami input on POS app → KONAMI_QR_INTEGER QR render |
| 7 | XORing the coupon code for profit | 4 | Hardcoded key and weak encoding is still a cleartext value | XOR `FREE_COUPON_XOR_B64` with `ROOT_TITLE = "COFFEE"` |
| 8 | Manager password and goat rental | 8 | The coffee shop might not have properly cleared their PC | Username `goats` + year 2026 → RC4 manager pw → PBKDF2 + AES-GCM + inner RC4 with free coupon |

## Artifacts (`/c/ctfint/nsec/poubelle/`)

| File | Purpose |
|---|---|
| `challenge.wim` | Original WIM image (32 MB) |
| `extracted/recycle_bin/...` | 7z-extracted recycle bin (no ADS) |
| `extracted_full_v2/...` | 7z `-sns` extraction with ADS preserved |
| `artifacts/point_of_sale.exe` | PyInstaller-packed Tkinter POS app (~12 MB) |
| `artifacts/point_of_sale.exe_extracted/point_of_sale.pyc` | Frozen bytecode (Python 3.8) |
| `artifacts/disasm.txt` | xdis-rendered bytecode disassembly |
| `artifacts/all_consts.txt` | Recursive co_consts dump of POSApp class |
| `artifacts/subvention_audit.pdf` | Municipal grant PDF — flag 1 |
| `artifacts/zootherapy_redacted.png` | Marketing PNG carrying `:promo` ADS — flag 2 |
| `artifacts/promo_ads.bin` / `promo.heic` | Extracted ADS, HEIF container |
| `artifacts/item_1_bgra_swap.png` | Background layer of HEIF iovl — contains flag 2 plaintext |
| `artifacts/extracted.zip` | Password-protected ZIP — flag 4 |
| `artifacts/parse_backup.py` | Renders QR from Recycle Bin coordinates (flag 3) |
| `artifacts/backup_qr.png` | Rendered QR-ish coordinate image |
| `artifacts/konami_qr.png` | KONAMI_QR_INTEGER rendered as QR (flag 6) |
| `artifacts/passwords.txt` | bkcrack output (flag 4 plaintext) |

## Flag 1 — Unredacting a PDF (already solved by teammate)

`subvention_audit.pdf` contains an "Approved by:" line followed by a redaction rectangle and the disclaimer "personnel identifier redacted under internal privacy policy." The redaction is a vector-drawn overlay; the original signature text is preserved underneath and can be recovered by re-rendering with the overlay removed (or by extracting the underlying text streams via a PDF tool that doesn't honor the overlay).

The teammate's submission credited "ovcrash" / [teammate]. Flag value not directly captured locally — see the askgod history entry (id 143) for record.

## Flag 2 — Alternate data stream + Paint layers

### NTFS ADS discovery

`7z l -slt challenge.wim` shows `Alternate Streams = 1` on `$RVH3NKM.png`. Default `7z x` strips the ADS; the `-sns` switch is required:

```bash
7z x -y -sns challenge.wim -o extracted_full_v2/
```

PowerShell confirms a single ADS named `promo` of 4 263 002 bytes:

```powershell
Get-Item '$RVH3NKM.png' -Stream * | Where Stream -ne ':$DATA'
# promo  4263002
```

Extracted with `Get-Content -Encoding Byte` and saved as `promo.heic`.

### HEIF iovl decoding

`file promo.heic` reports HEIF. ftyp brand is `mif1`/`heic` with compatible `mif1`, `gcmi`, `iso8`, `mif2`, `iaaf`. Primary item type is `iovl` (image overlay) with three `unci` (uncompressed image) source items.

Critical observations from box parsing:

* `ispe` declares 1024×1536.
* `uncC` profile `gene` declares 4×8-bit components in order `2, 1, 0, 3` → stored as BGRA, displayed as RGBA after channel swap.
* `iref` `dimg` ties item 4 (iovl) → {1, 2, 3}.
* All three iovl source extents are not raw 4 × 1024 × 1536 bytes; their sizes are 4 223 458 / 7 830 / 11 797 bytes. They're **raw DEFLATE** streams (`zlib.decompress(data, -15)`) that each decompress to exactly 6 291 456 bytes = 1024 × 1536 × 4.

### Layer stack

| Item | Decompressed | Content |
|---|---|---|
| 1 | 6 291 456 B | Full background — base image with the green "Pet Goats at a Coffee Shop" promo, **plus the flag text in cursive: `flag-layer_zootherapy_with_coffee_4_jitter`** |
| 2 | 6 291 456 B | Cream-colored ellipse, alpha-masked — the "Promotion starting soon" pill |
| 3 | 6 291 456 B | Dark-green text "Promotion starting soon" |

Items 2 and 3, drawn over item 1 by the iovl, are the MS Paint additions used to redact the original advertised flag. Rendering item 1 alone reveals the flag.

```python
import zlib, numpy as np
from PIL import Image
raw = zlib.decompress(open('item_1.bin','rb').read(), -15)
arr = np.frombuffer(raw, dtype=np.uint8).reshape((1536, 1024, 4))
Image.fromarray(arr[:, :, [2, 1, 0, 3]], 'RGBA').save('item_1.png')
```

The C2PA manifest in the PNG's `caBX` chunk (and the EXIF in HEIF item 5) confirms the workflow: GPT-4o (Sora / Truepic Lens CLI) generated the original, then Microsoft Paint edited it offline without a trusted certificate — exactly the redaction action.

`flag-layer_zootherapy_with_coffee_4_jitter` → submitted, +4 points.

## Flag 3 — QR code as file paths (existing)

Submitted earlier in the event. The Recycle Bin contained 475 `$I*` entries whose original paths were `C:\Users\goats\Desktop\Temporary USB Copy\backup\<10-bit-binary>\<10-bit-binary>`. Parsing the two binary numbers as `(y, x)` coordinates plots a 17 × 33 bitmap. A teammate decoded this into the flag.

`artifacts/parse_backup.py` re-renders the bitmap from `$I*` files.

## Flag 4 — Plaintext attack on the ZIP (existing)

`extracted.zip` is ZipCrypto-encrypted. The 3 JPGs inside (`lawnmower.jpg`, `open_bar.jpg`, `reading_your_soul.jpg`) had known plaintext prefixes (JPEG signature/JFIF header). `bkcrack` recovered the internal keys and produced the plaintext `passwords.txt`, which contained `flag-who_needs_a_password_manager_these_days`.

## Flag 5 — Cleartext credentials in unpacked Python (existing)

`pyinstxtractor.py` unpacks `point_of_sale.exe`. `xdis.load.load_module` on `point_of_sale.pyc` (Python 3.8 bytecode) exposes module constants without needing a working decompiler:

```python
LOGIN_USERNAME = "barista"
LOGIN_PASSWORD = "flag-hardcoding_credentials_into_my_app"   # <-- flag 5
MANAGER_USERNAME = "manager"
```

`uncompyle6` / `decompyle3` choke on the 3.8 bytecode under Python 3.12, but `xdis.disassemble_file` works fine.

## Flag 6 — Konami code (existing)

The POS app binds a global `<KeyPress>` handler that appends keysyms to a 10-deep deque. When the buffer matches `KONAMI_CODE = ["up", "up", "down", "down", "left", "right", "left", "right", "b", "a"]`, `_show_konami_popup` opens a "Backdoor" Toplevel that renders `KONAMI_QR_INTEGER` (an 841-bit integer) as a 29×29 QR via `_render_qr_unicode`.

Reproducing the render and reading it with OpenCV's `QRCodeDetector`:

```
data: flag-up_up_down_down_flag_flag
```

## Flag 7 — XOR coupon (existing)

`_decode_free_coupon` decodes `FREE_COUPON_XOR_B64` and XORs against `root.title()`. The login window title is `"COFFEE Point Of Sale Login"` but the actual XOR uses `ROOT_TITLE = "COFFEE"`:

```python
b64 = "JSMnIWg2LCIjMi0sLSgZIDcgJhArIyQrMBAyLiQxHDYpMxokMSoZMi0gHD80KSEwIDs="
key = b"COFFEE"
coupon = bytes(b ^ key[i % 6] for i, b in enumerate(base64.b64decode(b64))).decode()
# 'flag-something_free_means_that_you_are_the_product'
```

## Flag 8 — Manager flag (Goat rental)

The `Rent a goat` cart item is manager-only. `_checkout_cash` invokes `_decrypt_manager_flag(applied_coupon_code)` once the manager has logged in **and** completed TOTP MFA.

### Step 1 — Manager password

`_decrypt_manager_password` decrypts `MANAGER_PASSWORD_RC4_B64 = "KJN+uCuaL4P7J1loxw=="` with the runtime RC4 key:

```python
windows_username = os.environ.get("USERNAME", "") or getpass.getuser()
key = (windows_username.upper() + str(datetime.now().year)).encode()
```

The original system was user `goats` (visible in the Recycle Bin `$I` paths `C:\Users\goats\Desktop\...`) and the WIM was created in 2026 (file metadata; also matches the challenge year). Trying `("GOATS" + "2026").encode()` yields:

```
2sucres2laits
```

### Step 2 — KDF + AES-GCM + inner RC4

```python
key_material = ("2sucres2laits" + "4M3WOMFZ4WQOXF3EZRB6DGM56A7JJXN7" + "59051769").encode()
salt = base64.b64decode("7oNGBkr2uc4oHc9RAvuwIw==")
key = hashlib.pbkdf2_hmac("sha256", key_material, salt, 200000, dklen=32)

nonce = base64.b64decode("unFgeMhadTCs5TCQ")
blob  = base64.b64decode("dE6iSQZT5G4z2qH4ERpXnUb7vSsFLhwQzxh+toyMDxnsOGRMnRf9y3wiDB2n6BZUXbnDltIIAQ==")
cipher = AES.new(key, AES.MODE_GCM, nonce=nonce)
inner_rc4 = cipher.decrypt_and_verify(blob[:-16], blob[-16:])

free_coupon = "flag-something_free_means_that_you_are_the_product"
flag = rc4(inner_rc4, free_coupon.encode()).decode()
# 'flag-turn_grass_into_fertilizer_4_cheap'
```

Both the TOTP secret (`MANAGER_TOTP_SECRET_B32`) and the KDF timestep (`MANAGER_FLAG_KDF_TIMESTEP = 59051769`) are mixed into the key material, so live TOTP isn't needed — the static constants are sufficient.

`flag-turn_grass_into_fertilizer_4_cheap` → submitted, +8 points.

## Tools used

* `7z 23.01` for WIM listing + extracting with `-sns` to preserve NTFS ADS.
* PowerShell `Get-Item -Stream *` to enumerate ADS post-extraction.
* `pyinstxtractor` to unpack the PyInstaller exe.
* `xdis.load.load_module` + `xdis.disassemble_file` for Python 3.8 bytecode inspection from a Python 3.12 host (uncompyle6 / decompyle3 both fail on parts of the module).
* `pycryptodome` for AES-GCM / PBKDF2 reproduction.
* `Pillow + numpy + zlib` for HEIF iovl raw-DEFLATE layer decoding.
* `cv2.QRCodeDetector` for QR decoding.
* `bkcrack 1.7.1` (already used) for ZipCrypto known-plaintext attack on `extracted.zip`.

## Key takeaways

* PyInstaller-frozen Python is trivially reversible — never embed flags or credentials in `.pyc` constants.
* Custom RC4 + XOR for "secrets" buys nothing; constants are visible in the bytecode disassembly.
* NTFS ADS survives `7z x` by default? **No** — `-sns` is mandatory to preserve streams; otherwise the ADS payload is silently dropped.
* HEIF's `iovl` overlay item is a real redaction trap: hiding flag text behind a Paint-drawn overlay leaves it in plaintext on the bottom layer.
* `pbkdf2_hmac(... salt, iterations, dklen=...)` with a static salt and predictable inputs (manager password + B32 secret + fixed timestep) is decryptable offline.

---

**Submitted at**: 2026-05-16 01:51 EDT  
**Final score**: 31/31 points


---

## Swarm Trace

> [ AGENT TRANSCRIPT // TRACK: reduce-reuse ]
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
