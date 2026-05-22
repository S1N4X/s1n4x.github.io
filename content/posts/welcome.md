+++
title = "Welcome to the CTF! - 1/1"
date = 2026-05-20
categories = ["nsec26"]
tags = ["info", "solved"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** - 1/1 sub-flags captured

## Context

> Welcome to the CTF! To make your life easier with some of the challenges, a shell server is available for your use. ssh root@shell.ctf Password: shellftw!

## Artifacts

- Artifact directory: `nsec/welcome/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59942-welcome.md`

## Welcome to the CTF! (Topic 59942)

Status: **SOLVED** - 1/1 sub-flags captured

## Artifacts

- Artifact directory: `nsec/welcome/artifacts`

### From `59942-welcome-ctf.md`

## Welcome to the CTF (Challenge 59942)

## Context

A brief setup challenge pointing teams to a shared IPv6-capable jump host (`shell.ctf`) for use in other challenges. The challenge brief acts as a "Rule flag" / setup acknowledgement - the flag is automatically granted when the team checks in.

**Target Information:**
- Hostname: `shell.ctf`
- OS: Ubuntu 24.04.4 LTS
- Kernel: Linux 7.0.5-zabbly+ (LXC/Incus container)
- SSH Credentials: `root` / `shellftw!`
- IPv6 Address: `9000:6666:6666:6666:216:3eff:feb1:8d80/64`
- IPv4: Container-internal only (127.0.0.1)

## Recon

Comprehensive enumeration of the shared jump host produced no on-box flag file. The "flag" for this challenge is administered through askgod's rule-flag automation, not extracted from the box.

| Location | Status | Details |
|----------|--------|---------|
| `/etc/motd` | Clean | Stock Ubuntu message |
| `/etc/issue` | Clean | Stock Ubuntu |
| `/etc/profile` | Clean | Stock Ubuntu |
| `/root` | Checked | Only standard dotfiles |
| `/opt`, `/srv`, `/var/www` | Empty | No files |
| Full filesystem grep | No hits | `grep -ril 'FLAG-\|flag{' /root /etc /opt /srv /var` returned nothing |
| Filename search | No hits | `find / -name '*flag*'` returned no matching files |

## Exploitation

No technical exploitation required. The flag for this challenge is granted automatically by the askgod platform once the team is registered and checked in. The box itself is a utility/jump host shared across all NSEC 2026 teams.

## Captures

### Flag 1/1 - Rule flag (welcome)

- **askgod entry:** #155 "Rule flag" 1 pt
- **Timestamp:** 2026-05-15 21:23 EDT
- **Method:** Team check-in / askgod automatic rule-flag grant
- **Value:** Redacted (askgod-only - value never appeared on the team box)

## Cross-Track References

`shell.ctf` is the primary pivot point used by many other tracks:
- IPv6 connectivity to the rest of the NSEC challenge network
- Reverse shell catching (e.g., used for `monsatan-sprinklers` exploit driver)
- Out-of-band scans against `apt438`, `aquifer`, `monsatan.ctf`, REM pods, etc.

## Artifacts

- `nsec/welcome\recon.txt` - Team 061 recon notes
- `nsec/shellctf_known_hosts` - SSH known_hosts entries
