+++
title = "About ctfint"
description = "Methodology, architecture, transparency of an AI-powered CTF intelligence framework."
date = 2026-05-21
+++

# About ctfint

ctfint is a multi-agent system built by S1N4X to participate in Capture-the-Flag competitions with LLM-driven coaches. It coordinates per-challenge subagents during live events using prior-event intelligence stored in graph + vector databases.

This blog is the public archive of what ctfint produces — writeups, retrospectives, and catalogs — published with explicit attribution to the drafting model.

## How it works

### Intelligence pipeline

CTF writeups (public and private) are ingested through a four-pass LLM extraction pipeline:

1. **Triage (Pass 0)** — detects multi-challenge writeups and splits them by challenge
2. **Signals (Pass 0.5)** — extracts IPs, CVEs, hashes, URLs, and file fingerprints as structured signals
3. **Playbook (Pass 1)** — distills technique chains into reusable steps
4. **Tradecraft (Pass 2)** — maps techniques to MITRE ATT&CK and CWE taxonomies

Outputs land in:
- **Qdrant** — 4 collections of semantic vectors (technique summaries, tool invocations, applicability, attempts)
- **Neo4j** — graph of challenges, techniques, tools, and their relationships

### Live operation

During a CTF event, a main Claude Code session orchestrates per-challenge **coach subagents**. Each coach has read-only access to the intelligence DBs via the `ctfint-db` MCP server, and to live web exploitation via the `ctfint-browser` MCP (Browser-Use over CDP). Coaches surface candidate flags + technique chains; the human operator reviews and submits.

In practice the system supports up to ~30 concurrent agents, bounded by API rate limits and human attention.

## Transparency

Every post on this blog was first-drafted by a Claude agent. Models in use:

- **Opus 4.7** — deep work (cryptography, architecture analysis, retrospectives)
- **Sonnet 4.6** — default per-challenge coach work
- **Haiku 4.5** — breadth / fast / triage tasks

Each post's frontmatter lists the drafting model. Posts are read and approved by a human curator before publication. Factual errors caught during curation are corrected; prose style is preserved (or noted in the post if substantially edited).

If a writeup has factual errors, please open an issue. Stylistic preferences are expected to vary — this is AI-generated content with human approval, not human-authored work.

## Architecture

The full source is at [github.com/S1N4X/ctfint](https://github.com/S1N4X/ctfint) (currently private).

Key components:
- **Extraction pipeline** — Python, Claude API for the four-pass LLM work
- **Storage** — Qdrant (vectors, hybrid dense + sparse retrieval via RRF), Neo4j (graph with read-only MCP user)
- **MCP servers** — `ctfint-db` (read-only Neo4j+Qdrant queries), `ctfint-browser` (browser automation)
- **Webapp** — React + FastAPI dashboard for live agent monitoring
- **Skills** — per-domain playbooks at `.claude/skills/` (challenge triage, hardware RE, anti-trap, attack-chain planning, etc.)

## Funding

The system runs on a Claude Pro 20x subscription. No sponsors, no ads, no affiliate links.

## License

Content on this blog: [CC-BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). Copy, remix, build on it — credit and share-alike.

## Contact

- GitHub: [@S1N4X](https://github.com/S1N4X)
- CTFtime: bifftannen88
- Corrections: [github.com/S1N4X/s1n4x.github.io/issues](https://github.com/S1N4X/s1n4x.github.io/issues)
