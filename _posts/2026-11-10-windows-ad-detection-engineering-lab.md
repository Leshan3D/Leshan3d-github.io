---
title: "Windows & Active Directory Detection Engineering Lab"
date: 2026-11-10 12:00:00 +0200
categories: [Projects, Detection Engineering]
tags: [windows, active-directory, detection-engineering, sysmon, suricata, elastic, samba-ad, powershell]
pin: true
toc: true
description: A single-endpoint Windows and Samba AD lab tracing LibreOffice-driven PowerShell, domain discovery, controlled collection and uploads, Run-key persistence, firewall impairment, and evidence correlation across Winlogbeat, Sysmon, Suricata, and receiver logs.
image:
  path: /assets/img/posts/windows-ad-detection-lab-topology.svg
  alt: Logical topology for the Windows and Active Directory detection engineering lab
---

> Full technical case study: [Windows & Active Directory Detection Engineering Lab](https://leshan3d.github.io/windows-ad-detection-engineering-lab/) · [Source repository](https://github.com/Leshan3D/windows-ad-detection-engineering-lab)
{: .prompt-info }

I built this single-endpoint lab to investigate a practical detection-engineering question: **what can I actually establish when document-driven PowerShell activity continues through domain discovery, collection, controlled HTTP transfer, persistence, and defense impairment?**

The workstation is Windows 11 Pro joined to a Samba Active Directory domain. Winlogbeat ships Windows Security and Sysmon events to Elasticsearch; Suricata observes the VM's QEMU TAP traffic and ships EVE records through Filebeat. A controlled Kali HTTP service provides staging, check-in, and a size-limited receiver. Kali's own receiver logs remain on the operator and are manually correlated; they are not represented as Elasticsearch telemetry.

## What I investigated

- LibreOffice ODT macro execution launching PowerShell, followed by identity and domain discovery.
- Collection of a planted `README.txt` from a Samba share and archive preparation.
- Manual and document-driven HTTP uploads to a controlled receiver, with endpoint/network correlation and receiver-side size/hash receipts.
- User Run-key persistence and a subsequent logon callback.
- Windows firewall impairment and restoration.
- Credential-access investigation, with artifact inspection carefully distinguished from evidence of credential capture.

## Detection-engineering findings

- Sysmon running did not mean its Operational channel was subscribed in Winlogbeat. Correcting the ingestion configuration restored the expected event path.
- Configuration syntax validation alone did not prove that a Sysmon change had been applied; elevation was required.
- Security 4688 records process creation and ancestry; it does not contain HTTP connection details. WFP 5156 records permitted connection metadata, while Suricata supplies the HTTP URI and receiver logs supply request IDs and file receipts.
- A PowerShell user-agent alert is a useful pivot, not proof of exfiltration. The upload conclusion became stronger when process context, WFP, Suricata's HTTP/flow records, and the receiver's size/hash receipt agreed.
- A search for NPPSpy matched `Test-Path` and Notepad artifact inspection. Those records do not prove credential dumping or successful password capture.

This was a manual lab case study. **Privilege escalation and lateral movement were not performed**; the report does not claim full ATT&CK coverage, production deployment, measured detection efficacy, or a false-positive rate.

### Project links

- [Read the full case study and copyable KQL queries](https://leshan3d.github.io/windows-ad-detection-engineering-lab/)
- [Browse the repository and evidence register](https://github.com/Leshan3D/windows-ad-detection-engineering-lab)
