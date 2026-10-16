---
title: "CTI MCP Gateway"
date: 2026-08-27 12:00:00 +0300
categories: [Projects, Threat Intel]
tags: [python, mcp, cti, stix, sigma, abuseipdb, virustotal]
pin: true
description: MCP-powered CTI pipeline — sanitizes untrusted threat intel against prompt injection, extracts and refangs IOCs, enriches via AbuseIPDB and VirusTotal, scores risk deterministically, then emits STIX 2.1 + SIGMA.
---

## Overview

Enterprise-grade **Model Context Protocol** gateway built with FastMCP. Ingests unstructured, untrusted threat intel, extracts IOCs, enriches via OSINT, scores risk (0-100, no LLM in the loop), and outputs STIX 2.1 bundles + SIGMA rules.

**Repo:** [Leshan3D/cti-mcp-gateway](https://github.com/Leshan3D/cti-mcp-gateway)

## Pipeline

```text
Raw report → Sanitizer → IoC Extractor → OSINT Enrichment → Risk Engine → STIX + SIGMA
```

- **Tier 1 Sanitizer:** strips zero-width chars, homoglyphs, HTML comments — defense vs Indirect Prompt Injection
- **Tier 2 Extractor:** refangs `hxxp`, `1[.]1[.]1[.]1`, filters RFC1918
- **Tier 3 Enrichment:** AbuseIPDB + VirusTotal
- **Tier 4 Scoring:** deterministic Python, 0-100 + verdict
- **Tier 5 Output:** STIX 2.1 JSON + SIGMA YAML over MCP stdio

## Security angle

Threat feeds are an IPI vector. Audit logs go to `stderr` to keep MCP stdout clean, and no LLM sits in the scoring path.

Code, architecture diagrams, and usage in the [repo](https://github.com/Leshan3D/cti-mcp-gateway).
