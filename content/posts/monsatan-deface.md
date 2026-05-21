+++
title = "Monsatan - Deface the website — 6/6"
date = 2026-05-20
categories = ["nsec26"]
tags = ["agent-slop", "devops", "solved", "web"]
model = "Sonnet (default)"
draft = false
+++
Status: **SOLVED** — 6/6 sub-flags captured

## Context

> Monsatan has been feeding us lies along with our food for decades. Their “GreenPromise™” campaign is a smokescreen. Behind the solar panels and the composting PR stunts, they’re still doing what they’ve always done: monopolizing the food supply, patenting seeds, and silencing dissent. They have made us dependant to them and we deserve our freedom from corporations. Time to give them a little PR boost. Their web development team doesn’t seem to understand anything about security. We need to send the world a message of hope and make Monsatan suffer. Try to find how we can deface their website and post our message: We will not back down until Monsatan is crumbling in its filth . Monsatan

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/6 | `FLAG-<not-recorded-locally>` | Found in the README of the gitlab server | 2026-05-15T23:39:00-04:00 |
| 2/6 | `FLAG-<not-recorded-locally>` | Found in a file in the commit history. | 2026-05-15T23:41:00-04:00 |
| 4/6 | `FLAG-<not-recorded-locally>` | Extracted from the CI env variable | 2026-05-16T00:01:00-04:00 |
| 5/6 | `FLAG-<not-recorded-locally>` | Found in the security advisory of a test package in the dart registry. | 2026-05-16T00:41:00-04:00 |
| 6/6 | `FLAG-<not-recorded-locally>` | Injected in the manifesto once the defacing is done | 2026-05-17T12:36:00-04:00 |

## Artifacts

- Artifact directory: `nsec/monsatan-deface/artifacts`

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59981-monsatan-deface.md`

## Monsatan - Deface the website (Topic 59981)

Status: **SOLVED** — 6/6 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 1/6 | `FLAG-{5b1f539e271e02b0b9ca87e310af0e29}` | Found in the README of the gitlab server | 2026-05-15T23:39:00-04:00 |
| 2/6 | `FLAG-{j2970ax5s3zq6k17l97jg2k84hd12o9f}` | Found in a file in the commit history. | 2026-05-15T23:41:00-04:00 |
| 4/6 | `FLAG-<not-recorded-locally>` | Extracted from the CI env variable | 2026-05-16T00:01:00-04:00 |
| 5/6 | `FLAG-<not-recorded-locally>` | Found in the security advisory of a test package in the dart registry. | 2026-05-16T00:41:00-04:00 |
| 6/6 | `FLAG-<not-recorded-locally>` | Injected in the manifesto once the defacing is done | 2026-05-17T12:36:00-04:00 |

## Artifacts

- Artifact directory: `nsec/monsatan-deface/artifacts`

### From `monsatan-defacing-3-6-v2.md`

## monsatan-defacing 3/6 v2 — build-deface-recon branch exploration

**Date:** 2026-05-16
**Team:** 061 ([operator])
**Status:** STUCK — no NEW flag found. All 4 candidate flags I discovered were already submitted by prior agents.

## Scope and chain

The fresh intel said the previous attacker's `build-deface-recon` branch (fetched into the local clone) had a unique git-archaeology lead. I explored that branch, walked the entire failed-pipeline timeline, and pulled raw job traces from the public GitLab UI (no auth needed for `/jobs/<id>/raw`).

## What is `build-deface-recon`?

It is a private attacker branch the previous CTFer pushed into `monsatan/website` using the leaked PAT (`<REDACTED:gitlab-pat>`). The branch consists of a single rotating file `tools/legacy_env_setup.sh` whose contents the attacker re-wrote across 11 commits while iterating on their exploit. Because `.gitlab-ci.yml` matches `/^build-.*/` and runs the script as `sudo -E -u gitlab-runner-basic tools/legacy_env_setup.sh` from the `dependency_check` job, each push fired a real CI run whose trace is now publicly readable.

The commit sequence (newest first) shows the attacker's progression:
1. `MON-999 chore(ci): legacy setup` — first runner foothold
2. `MON-999 recon v2` — id, env, ssh, /home, etc
3. `MON-999 chmod +x` — perms fix
4. `MON-999 leak attempt` — **mask-bypass leak of the `$FLAG` env var via base64, char-by-char, hex** (this trace contains the CI-env flag)
5. `recon3..6` — pivoted to investigating dartreg, the 8888 listener, and other monsatan tools
6. `evil upload`, `finalize upload`, `evil 1.0.3` — uploaded a malicious `is_even` Dart package with a `hook/build.dart` that rewrites `web/index.html` to insert the deface H1
7. `more recon`, `probe ws` — probed the website server IPv6 `9000:d37e:c40b:f108:216:3eff:fe19:738e`

## Infrastructure facts extracted from the recon traces

- **Website server**: `[9000:d37e:c40b:f108:216:3eff:fe19:738e]` — ports 22 and 80 open; serves the Flutter web app; reachable at `http://monsatan.ctf/`
- **GitLab server**: `[9000:d37e:c40b:f108:216:3eff:feaa:1b50]`
- **dartreg server**: `[9000:d37e:c40b:f108:216:3eff:feaa:1b4f]` (host header `dartreg.monsatan.ctf`)
- **Runner**: `monsatan-defacing-gitlab-runner` (eth0 `9000:d37e:c40b:f108:216:3eff:feaa:1b60`)
- **Local 8888 listener** on the runner — bound to `*:8888`, identified as `gitlab_restart_listener.py` in `/usr/local/bin/`
- **Pre-installed PyInstaller-wrapped binaries** in `/usr/local/bin/monsatan/`: `print_flag`, `jfrog`, `syft`, `slack_notify`, `jira_transition`, `check_jira_refs`, `check_snyk`, `trufflehog`, `sonar-scanner`. The check_jira_refs binary "has expired" with notice `Please contact your provider jahidulhamid@yahoo.com` (lol)
- **Jira API**: `https://jira.internal.monsatan.nsec/` (only used by CI's `jira_transition` Pyinstaller binary, not reachable from VPN)
- **CI deploy**: `scp -r -i $DEPLOY_PRIVATE_KEY ./build/web/* root@[$WEBSITE_SERVER_HOST]:/opt/website/`
- **dartreg upload flow**: GET `/api/packages/versions/new` → upload to returned URL → finalize with `Location: /api/packages/versions/new/finalize/<UUID>`

## Public unauthenticated endpoints that worked

- `http://gitlab.monsatan.ctf/monsatan/website/-/pipelines.json?per_page=100` — list every pipeline
- `http://gitlab.monsatan.ctf/monsatan/website/-/pipelines/<ID>.json` — pipeline+job details
- `http://gitlab.monsatan.ctf/monsatan/website/-/jobs/<ID>/raw` — **full raw trace, no auth**
- `http://gitlab.monsatan.ctf/monsatan/website/-/jobs/<ID>/trace` — JSON trace
- `http://gitlab.monsatan.ctf/monsatan/website/-/jobs/<ID>/artifacts/download` — zipped artifact (worked for 118 build, 119 sbom)
- `http://gitlab.monsatan.ctf/monsatan/website/-/jobs/<ID>/artifacts/raw/<PATH>` — single artifact file
- `http://gitlab.monsatan.ctf/api/v4/projects/3` — project metadata (no auth)
- `http://gitlab.monsatan.ctf/api/v4/projects/3/trigger/pipeline` — works with the README-leaked trigger token `<REDACTED:gitlab-trigger-token>` to run any ref including non-default branches

## Things that did NOT work

- The leaked PAT `<REDACTED:gitlab-pat>` has scope `read_repository` only — sufficient for git clone of private branches, but every `/api/v4/` endpoint except `/projects/3` returns `403 insufficient_scope`. Cannot list jobs/MRs/issues via API.
- dartreg `/api/packages/...` requires a valid bearer (`DART_REGISTRY_TOKEN`) which is CI-masked; cannot hit it directly from VPN.
- Triggered pipelines 52 (main), 53 (build-deface-recon), 54 (v-99.0-deface) — all currently failing because the dartreg backend is returning `500 Internal Server Error trying to find package analyzer at https://dartreg.monsatan.ctf` so `flutter pub get` blows up. The `website_deploy` stage never runs.
- `monsatan.ctf` serves the clean (un-defaced) build. The defaced commit `d60e6af3` is `main` HEAD locally, but the deployed `/opt/website/index.html` is the previous build — last successful deploy was pipeline 27 (job 121) on 2026-04-25 before the defacement.

## Flags collected from this investigation (all already submitted)

| flag | source | inferred slot |
|---|---|---|
| `FLAG-{5b1f539e271e02b0b9ca87e310af0e29}` | README.md tail (commit d169542) | 1/6 README |
| `FLAG-{j2970ax5s3zq6k17l97jg2k84hd12o9f}` | deleted `ci_test.sh` (commit 8b7491d) | 2/6 commit history |
| `FLAG-{136da0151bcdd1dfa3386519c98684af}` | job 159 raw trace — base64=`RkxBRy17MTM2ZGEwMTUxYmNkZDFkZmEzMzg2NTE5Yzk4Njg0YWZ9` + char-by-char dump of `$FLAG` env var (mask bypass) | 4/6 CI env |
| `FLAG-{8a8c0784c7f3e9ee548aa4d8bab836e1}` | job 181 raw trace — embedded in `id` field of `dartreg.monsatan.ctf/api/packages/test_package/advisories` JSON response | 5/6 dartreg BONUS |

## What's missing — 3/6 and 6/6

I scanned every job trace (jobs 64–242, covering every non-skipped CI run since the project was created) with `grep -oaE "FLAG-\{[a-zA-Z0-9_-]+\}"`. Only the 2 traces above produced flag matches. The build artifacts (`build/web/main.dart.js`, `build/sbom.json`, `index.html`) contain no flag. The compiled flutter site on the deploy target also contains no flag. The PyInstaller-wrapped tools in `/usr/local/bin/monsatan/` are not reachable from the VPN and all the attacker's strings-on-them attempts returned only obfuscated binary noise.

The intel's positional guess for 3/6 (read pipeline log between commit history and CI env) appears wrong, or the 3/6 flag lives outside the website project entirely. Strongest unexplored candidates:

1. **Listener on port 8888 (`gitlab_restart_listener.py`)** — the prior attacker confirmed a service is bound but only reachable from the runner namespace. Triggering a fresh pipeline that POSTs to `http://localhost:8888/` from inside the runner and returns the response in the trace would expose its functionality. The attacker tried a few methods (GET/POST/PUT, /restart, /api, /print_flag) but didn't paste decoded payloads. This is the most promising next move: push a new `legacy_env_setup.sh` payload to `build-*` and watch the trace.
2. **Trigger a tag-based deploy** — the deploy job is gated on `$CI_COMMIT_TAG =~ /^v-.*/`; if dartreg is fixed and a `v-something` tag is pushed (or a pipeline trigger with `ref=v-99.0-deface` once the registry stabilises), the `website_deploy` script `scp -r -i $DEPLOY_PRIVATE_KEY` would run, leaking `$WEBSITE_SERVER_HOST` (masked) plus echoing scp output. The deface `<h1>` would also land on `monsatan.ctf` post-deploy and the visible page might then host the flag.
3. **Dartreg artifacts endpoint** — the URL `https://dartreg.monsatan.ctf/artifacts/9c9cce66-...` mentioned in recon3 returns gzipped tar archives. The previous attacker downloaded one (`tp.tgz`) but no FLAG matched in its contents. There may be more UUIDs accessible (the test_package advisory only showed one).
4. **`assets/logo.png` or any PNG** — could contain stego but I haven't run zsteg/strings beyond a basic grep.

## Submissions this session

- Attempted submit of `FLAG-{136da0151bcdd1dfa3386519c98684af}` → DUP
- Attempted submit of `FLAG-{8a8c0784c7f3e9ee548aa4d8bab836e1}` → DUP
- Attempted submit of `FLAG-{5b1f539e271e02b0b9ca87e310af0e29}` → DUP

All three were already on file. Net new submissions: **0**.

## Recommended next agent action

Push a tiny new commit on `build-deface-recon` whose `legacy_env_setup.sh` exclusively probes port 8888 (different verbs, paths `/print_flag`, `/restart`, `/status`, `/api/v1/...`, raw POST bodies like `{"cmd":"print_flag"}`, `action=print_flag`, etc.) and dumps the full response bodies (no head -10 truncation). The `gitlab_restart_listener.py` is the only resource on the runner that wasn't fully reverse-engineered, and it has the most obvious "callback to print_flag" plot in the challenge. The PAT has push access (visibility=public + bot user has write — confirmed via prior attacker's pushes), so a single `git push` from this clone should work despite scope=read_repository if you reuse the bot's auth in the URL.

Alternatively, wait for the dartreg server outage to clear and re-trigger pipeline on `v-99.0-deface` (now points to `d60e6af3` HEAD) — that will fire `website_build_tag` (uses `git checkout main` which still has the deface H1) followed by `publish_to_artifactory` and `website_deploy`. Successful deploy should drop the defaced index.html into `[9000:d37e:c40b:f108:216:3eff:fe19:738e]:/opt/website/` and may also append a flag via the post-deploy hook.

## 2026-05-17 port-8888 probe

**Pipeline:** 70 (triggered via push to `build-deface-recon`)
**Job:** 350 (dependency_check)
**Branch:** build-deface-recon (commit 9fbddde)

Pushed single commit with improved port 8888 probe script. Dartreg was fixed by this date; main pipelines were succeeding.

**Key findings from job 350 raw trace:**
- Port 8888 listener is a Python BaseHTTP server (`Server: BaseHTTP/0.6 Python/3.12.3`)
- `POST` requests with JSON `{"cmd":"print_flag"}` return HTTP 200 with body "Restart signal received."
- `POST` requests with form data `action=print_flag` also return HTTP 200 "Restart signal received."
- **Direct execution of `/usr/local/bin/monsatan/print_flag` binary outputs the flag:**
  ```
  FLAG-{2247bff241800150a5ebb00288dc050d}
  ```
- Flag appears both when running the binary directly and when calling with `--help`
- Port 8888 listener itself does NOT respond with the flag; the flag comes from the binary

**Submission attempt:**
Submit was rejected with `[DENY-TRACK-COMPLETE] monsatan-defacing maps to askgod 'monsatan-defacing' which is already 6/6 complete.` This indicates the track was completed before this probe, meaning this flag was likely submitted earlier by a prior agent (possibly via a different path). The flag `FLAG-{2247bff241800150a5ebb00288dc050d}` does not appear in the local submission history JSON, suggesting it may have been submitted through a direct API path or by another team member.

**Trace artifact:** `nsec/monsatan-deface\artifacts\probe-8888-2026-05-17.txt`

## Key file paths (absolute)

- `nsec/monsatan-deface\artifacts\website\` — local clone (with build-deface-recon fetched)
- `nsec/monsatan-deface\artifacts\website\.gitlab-ci.yml` — CI definition
- `nsec/flags\.submit-history.jsonl` — local submission log (all 7 prior attempts visible)
- `nsec/monsatan-deface\artifacts\probe-8888-2026-05-17.txt` — job 350 trace with 8888 probe output


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
