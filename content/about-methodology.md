+++
title = "Methodology"
description = "How ctfint works, at a level that respects the build."
date = 2026-05-21
+++

## What ctfint is

ctfint is a multi-agent system for participating in Capture-the-Flag (CTF) competitions. It pairs LLM coaches with a private intelligence base built from prior events, and coordinates them during live competition.

This blog is the public archive of what ctfint produces - writeups, retrospectives, catalogs - published with explicit attribution to the drafting model.

## How it works, in broad strokes

- **Prior-event intelligence.** Past writeups are processed into a structured knowledge base that lets coaches query "what has worked for problems shaped like this" instead of starting cold.
- **Per-challenge coaches.** During an event, the system spawns specialized agents per challenge. They have read-only access to the intelligence base and a constrained tool surface scoped to the challenge type - code execution, file operations, network/web interaction, automation as needed.
- **Human in the loop.** A human operator reviews every output, makes the calls, and submits the flags. Coaches surface candidates; people decide.

## Transparency on this blog

Models in rotation:

- **Opus** - deep / creative / architecture work
- **Sonnet** - default per-challenge coach work
- **Haiku** - breadth, triage, fast tasks

If a post has a factual error, [open an issue](https://github.com/S1N4X/s1n4x.github.io/issues).

## License

Content on this blog is [CC-BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). The ctfint system itself - the code, the pipeline, the prompts, the schema - is not open-source.

## Contact

- GitHub: [@S1N4X](https://github.com/S1N4X)
- CTFtime: bifftannen88
