---
title: "Volt Typhoon LOLBin Detection Engineering"
date: 2026-09-11 12:00:00 +0300
categories: [Projects, Detection Engineering]
tags: [sigma, wazuh, opensearch, mitre-attack, lolbin, siem]
pin: true
description: Detection engineering lab emulating Volt Typhoon LOLBin techniques (netsh, schtasks, ntdsutil) — SIGMA rules, Wazuh PCRE2 detections, and OpenSearch SIEM visibility, with iterative false-positive tuning documented.
---

## Overview

Detection engineering lab emulating **Volt Typhoon** Living-off-the-Land techniques — `netsh`, `schtasks`, `ntdsutil` — with SIGMA rules, Wazuh PCRE2 detections, and OpenSearch dashboards.

> First-pass rules, tuned through several review iterations. Not yet validated at production scale.

**Repo:** [Leshan3D/Volt-Typhoon-LOLBin-Detection-Engineering](https://github.com/Leshan3D/Volt-Typhoon-LOLBin-Detection-Engineering)

## What it covers

| MITRE TTP | Technique | Tool |
|---|---|---|
| T1090.001 | Proxy: Internal Proxy | `netsh interface portproxy` |
| T1053.005 | Scheduled Task / Job | `schtasks.exe` |
| T1003.003 | OS Credential Dumping: NTDS | `ntdsutil` (emulated) |

- **Pipeline:** Windows 11 + Sysmon (EID 1) → Wazuh Manager (Docker) → OpenSearch dashboards
- **Detections:** SIGMA + `local_rules.xml` PCRE2, tuned for false positives
- **Emulation:**
```powershell
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=127.0.0.1 connectport=443 connectaddress=127.0.0.1
schtasks /create /tn "VaultMaintenance" /tr "cmd.exe /c echo VoltTyphoonTest" /sc daily /st 09:00 /f
```

## Why it matters

Signed LOLBins bypass hash/AV signatures. This project shows context-aware detection: correlating command-line args and parent process instead of just binary names.

Full write-up, rules, and tuning notes in the [repo](https://github.com/Leshan3D/Volt-Typhoon-LOLBin-Detection-Engineering).
