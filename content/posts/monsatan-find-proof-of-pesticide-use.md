+++
title = "[Monsatan] Find proof of pesticide use - 1/1"
date = 2026-05-20
categories = ["nsec26"]
tags = ["solved", "web"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** - 1/1 sub-flags captured

## Context

> We all know that Monsatan’s marketing department loves the word “sustainable.” Seed-to-table transparency, regenerative farming, zero synthetic inputs - the whole pitch. I’ve done some snooping around. A comrade from an allied bastion shared that Monsatan has been quietly purchasing pesticides from Verdachem Industries, one of the shadiest agrochemical outfits still operating in non-green communities. We need hard proof that Monsatan is buying from Verdachem before we can take action. We found a good place to start: an unsecured portal for procurement. Time to dig some dirt. **B2B Portal.**

B2B procurement portal exploitation challenge in the Monsatan track. The team needs to surface evidence of pesticide purchases by accessing confidential procurement documents that the B2B portal does not properly access-control.

The track's askgod tag is `monsatan-invoices` (server-side rename of the Discourse "B2B" title).

## Recon

- **Web target**: B2B portal serving invoice / procurement documents
- **Stack**: standard HTML + JSON REST API
- **Document endpoint**: `/document/<id>` (or equivalent) backed by sequential numeric IDs
- **Auth posture**: only the invoice metadata listing is gated; the document-fetch path has **no authorization check**

Artifacts captured during recon:
- `nsec/monsatan-b2b/artifacts/topic.html` - Discourse topic snapshot
- `nsec/monsatan-b2b/artifacts/nodes.json`, `node3.json` - portal page-tree dumps
- `nsec/monsatan-b2b/artifacts/bundle-dump.json`, `bundle-grep.txt` - bundled API responses
- `nsec/monsatan-b2b/artifacts/matches.json` - enumeration result hits

## Exploitation

### IDOR via sequential document IDs

The document endpoint uses contiguous integer IDs. Iterating through IDs surfaces all stored procurement documents, including ones marked confidential that prove Monsatan's pesticide purchases.

The flag is embedded inside one of the confidential invoice documents - specifically the Verdachem pesticide procurement record.

**Pattern**: classic IDOR - authorization is enforced on the *listing* but not on the *resource fetch*. Bumping `id` past the listed range exposes restricted documents.

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/1 | `FLAG-{e2749e7242053c78fe45594f6882f18c}` | IDOR - sequential invoice ID enumeration, no authorization on `/document/<id>` | 2026-05-15T22:38:00-04:00 |

**askgod entry**: `monsatan-invoices` 1/1 (askgod #62, 2 pts) - "Sequential IDs are bad IDs. At least the government isn't requiring them."

## Anti-Trap Notes

- Single-page web target, no untrusted text-content prompts to quarantine
- Flag value matches the `FLAG-{<hex>}` Monsatan-corp format (not the `FLAG-<words>` badge/quantum format)
- Re-submission attempt 2026-05-17 17:14 returned DUP (askgod cache confirmation that this is the true unique flag)

## Cross-Track References

This track shares thematic context with (but is distinct from):
- `monsatan-chatbot` (58682) - LLM chatbot exfiltrating Monsatan internal data
- `monsatan-impact-study` (59546) - Monsatan-funded research evidence
- `monsatan-pesticide` (59982) - supply-chain order forgery
- `monsatan-defacing` - Monsatan public site defacement

The IDOR pattern (sequential IDs, missing authz on resource fetch) is a recurring Monsatan-corp anti-pattern across the suite.

## Artifacts

- Artifact directory: `nsec/monsatan-b2b/artifacts/`
  - `topic.html` - Discourse topic snapshot
  - `nodes.json` - portal node-tree dump
  - `node3.json` - single-node detail dump
  - `bundle-dump.json` - bundled API responses
  - `bundle-grep.txt` - grepped extraction (FLAG location)
  - `matches.json` - enumeration result set
- Flag values archive: `nsec/flags/monsatan-b2b.txt`
