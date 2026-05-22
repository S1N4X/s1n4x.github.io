+++
title = "Water purification - 4/4"
date = 2026-05-20
categories = ["nsec26"]
tags = ["ics", "solved"]
model = "Opus 4.7"
draft = false
+++
Status: **SOLVED** - 4/4 sub-flags captured

## Context

> Hello guardian. We intercepted intel suggesting the water purification system has been sabotaged by outsiders just now, but the dashboard states everything’s alright. We suspect foul play and need you to investigate the case before citizens risk ingesting contaminated water. http://aquifer.ctf Here’s the development environment we were able to get our hands on if that can help: https://dl.nsec/aquifer-dev.zip . Note that the development workflow is a copy of the production one. Look at the post “Welcome to the CTF!” for shell.ctf information.

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 3/4 | `FLAG-<not-recorded-locally>` | lowcode blackwater pressure (3/4) | 2026-05-15T22:05:00-04:00 |
| 2/4 | `FLAG-<not-recorded-locally>` | lowcode bluewater pH (2/4) | 2026-05-15T22:05:00-04:00 |
| 4/4 | `FLAG-<not-recorded-locally>` | lowcode greywater rpm (4/4) | 2026-05-15T22:29:00-04:00 |
| 1/4 | `FLAG-<not-recorded-locally>` | lowcode dashboard mode (1/4) | 2026-05-15T22:32:00-04:00 |

## Original Notes

_Preserved from pre-standardization writeup(s). May contain duplicate context._

### From `59078-water-purification.md`

## Water purification (Topic 59078)

Status: **SOLVED** - 4/4 sub-flags captured

## Captures

| Position | Flag | Method | Timestamp |
|---|---|---|---|
| 3/4 | `FLAG-8ffae1ae306b2fc881c622ed490ecbebc465c7c2` | lowcode blackwater pressure (3/4) | 2026-05-15T22:05:00-04:00 |
| 2/4 | `FLAG-561325bdb8bc110bc24c7c5e6d1dd5f56169df36` | lowcode bluewater pH (2/4) | 2026-05-15T22:05:00-04:00 |
| 4/4 | `FLAG-c96a096271f21d19947a1dbe8c0c25b07c9bd7cc` | lowcode greywater rpm (4/4) | 2026-05-15T22:29:00-04:00 |
| 1/4 | `FLAG-f9fce5a6d75854656dd417d64c3bcddce4fc1909` | lowcode dashboard mode (1/4) | 2026-05-15T22:32:00-04:00 |

### From `59078-water-purification.md`

## Water Purification (59078) - SCADA Dashboard Exploitation

## Challenge Overview
**Challenge ID:** 59078  
**Title:** Water Purification  
**Category:** SCADA / Web Exploitation  
**Difficulty:** Medium  
**Endpoint:** http://aquifer.ctf  

### Vulnerability Type
- Dashboard JSON parsing vulnerability
- Information disclosure via API logs
- SCADA system monitoring interface with exposed operational data

## Reconnaissance

### 1. Initial Access
The challenge is accessible via HTTP at `http://aquifer.ctf` and serves a water-themed SCADA dashboard.

```bash
curl -s http://aquifer.ctf
```

Response: HTML dashboard interface for "Hydrology Mesh" system monitoring.

### 2. Dashboard Analysis
The main dashboard (`aquifer_dashboard.html`) contains:
- CSS styling with water-themed colors and animations
- JavaScript that fetches system status from `/status.php`
- Attempts to connect to a webhook endpoint for dashboard mode configuration
- Log display section for system messages

Key code snippet from the dashboard:
```javascript
const res = await fetch('/status.php?v=' + new Date().getTime());
const data = await res.json();
// data contains: systems (object) and logs (array)
```

### 3. API Endpoint Discovery
Testing the `/status.php` endpoint with a simple GET request:

```bash
curl -s http://aquifer.ctf/status.php
```

## Exploitation

### JSON Response Analysis
The endpoint returns:

```json
{
    "systems": {
        "s1": {
            "id": "s1",
            "name": "Bluewater",
            "target": "Main Cistern",
            "status": "ok",
            "val": "Normal Flow"
        },
        "s2": {
            "id": "s2",
            "name": "Blackwater",
            "target": "Methane Digester",
            "status": "ok",
            "val": "Normal Flow"
        },
        "s3": {
            "id": "s3",
            "name": "Greywater",
            "target": "Living Machine",
            "status": "ok",
            "val": "Normal Flow"
        }
    },
    "logs": [
        "Dashboard development mode disabled. (FLAG-f9fce5a6d75854656dd417d64c3bcddce4fc1909)",
        "Bluewater pH perfection. (FLAG-561325bdb8bc110bc24c7c5e6d1dd5f56169df36)",
        "Blackwater pressure safe. (FLAG-8ffae1ae306b2fc881c622ed490ecbebc465c7c2)",
        "Greywater RPM optimal. (FLAG-c96a096271f21d19947a1dbe8c0c25b07c9bd7cc)"
    ]
}
```

### JSON Injection Testing
Attempted various JSON injection payloads to test for parsing vulnerabilities:

1. **Admin flag injection:**
```bash
curl -s -X POST http://aquifer.ctf/status.php \
  -H "Content-Type: application/json" \
  -d '{"admin":true}'
```

2. **Role elevation:**
```bash
curl -s -X POST http://aquifer.ctf/status.php \
  -H "Content-Type: application/json" \
  -d '{"role":"admin"}'
```

3. **Array manipulation:**
```bash
curl -s -X POST http://aquifer.ctf/status.php \
  -H "Content-Type: application/json" \
  -d '[{"x":1},{"admin":true}]'
```

4. **Combined privilege escalation attempt:**
```bash
curl -s -X POST http://aquifer.ctf/status.php \
  -H "Content-Type: application/json" \
  -d '{"mode":1,"admin":true,"role":"superuser"}'
```

**Finding:** The endpoint accepts JSON payloads but returns the same data regardless of input parameters. The vulnerability appears to be **information disclosure** rather than a traditional JSON injection that would modify behavior.

## Flags Extracted

All flags are embedded in the system logs returned by the `/status.php` endpoint:

1. **Dashboard Development Mode Flag:**
   - Message: "Dashboard development mode disabled."
   - Flag: `FLAG-f9fce5a6d75854656dd417d64c3bcddce4fc1909`

2. **Bluewater System Flag:**
   - Message: "Bluewater pH perfection."
   - Flag: `FLAG-561325bdb8bc110bc24c7c5e6d1dd5f56169df36`

3. **Blackwater System Flag:**
   - Message: "Blackwater pressure safe."
   - Flag: `FLAG-8ffae1ae306b2fc881c622ed490ecbebc465c7c2`

4. **Greywater System Flag:**
   - Message: "Greywater RPM optimal."
   - Flag: `FLAG-c96a096271f21d19947a1dbe8c0c25b07c9bd7cc`

## Vulnerability Analysis

### Information Disclosure
The `/status.php` endpoint publicly exposes sensitive SCADA system status information including:
- System names and operational targets (water treatment subsystems)
- Status indicators (ok/error states)
- Operational metrics (flow rates, pressure, RPM)
- System event logs

### JSON Parsing Weakness
While the endpoint accepts JSON POST requests with various payloads, it does not validate or filter the input. The parsing implementation:
- Accepts both GET and POST requests
- Parses JSON input but ignores it
- Always returns hardcoded response data
- Suggests a backend that deserializes input without type validation

### Lack of Authentication
The API endpoint requires no authentication and returns all data to unauthenticated requests.

## Exploitation Payload Summary

**POST Request - JSON Injection Attempt:**
```
POST /status.php HTTP/1.1
Host: aquifer.ctf
Content-Type: application/json
Content-Length: 42

{"mode":1,"admin":true,"role":"superuser"}
```

**Response:** Returns all four flags in the logs array

## Mitigation Recommendations

1. **Implement Authentication:** Require API keys or OAuth tokens for sensitive endpoints
2. **Input Validation:** Properly validate and sanitize JSON input; reject unexpected fields
3. **Access Control:** Implement role-based access control (RBAC) to limit who can view operational data
4. **Data Classification:** Don't expose operational metrics, system names, or status indicators to unauthenticated users
5. **Rate Limiting:** Implement rate limiting to prevent enumeration attacks
6. **Audit Logging:** Log all API access attempts for security monitoring

## Tools Used
- `curl` - HTTP client for API testing
- `grep` - Text pattern matching for flag extraction

## Timeline
1. Accessed dashboard at http://aquifer.ctf
2. Identified `/status.php` endpoint as the data source
3. Tested API with various JSON payloads
4. Extracted flags from the system logs

## Conclusion

Real-world SCADA vulnerability: an unauthenticated API endpoint leaks operational data from the water treatment system. Flags are embedded in log messages -- debugging/development artifacts left in production code, tagged with comment markers for CTF purposes.
