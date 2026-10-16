---
title: "Full Spectrum Cyber Range Enterprise 101"
date: 2026-08-04 12:00:00 +0300
categories: [Projects, Cyber Range]
tags: [active-directory, wazuh, security-onion, kali, purple-team, docker, virtualbox]
pin: true
toc: true
description: Enterprise-grade hybrid cyber range — Windows AD, Linux identity, Postfix, Docker microservices, Wazuh + Security Onion telemetry, and a full Kali kill chain from recon to RDP lateral movement.
---

> Full project write-up mirrored from GitHub — source: [Leshan3D/Full-Spectrum-Cyber-Range-Enterprise-101](https://github.com/Leshan3D/Full-Spectrum-Cyber-Range-Enterprise-101)
{: .prompt-info }

## Overview (from repo)

![VirtualBox](https://img.shields.io/badge/Hypervisor-VirtualBox-blue)
![Docker](https://img.shields.io/badge/Containerization-Docker-2496ED)
![Active Directory](https://img.shields.io/badge/Identity-Active_Directory-blueviolet)
![Wazuh](https://img.shields.io/badge/SIEM-Wazuh-orange)
![Security Onion](https://img.shields.io/badge/NSM-Security_Onion-success)
![Kali Linux](https://img.shields.io/badge/Red_Team-Kali_Linux-red)

<img width="401" height="191" alt="project-x-guide" src="https://github.com/user-attachments/assets/2650aa82-4ae8-4165-bc61-6383869d65ca" />

## 📌 Executive Summary
**Project-X (Enterprise 101)** is an enterprise-grade hybrid cyber range engineered from bare-metal virtual machines to simulate a modern corporate infrastructure under active attack. 

The environment features a fully promoted **Windows Active Directory (AD DS)** domain, Linux cross-platform identity integration (**Winbind/Samba/Kerberos**), internal mail routing (**Postfix**), and containerized microservices managed via **Docker**. To evaluate enterprise security posture, the environment was configured with custom defensive monitoring (**Wazuh SIEM** & **Security Onion NSM**) alongside intentionally staged vulnerabilities. A full offensive Cyber Kill Chain was then executed from a **Kali Linux** attack platform—leveraging industry-standard toolsets like **NetExec**, **Evil-WinRM**, **Hydra**, **SecLists**, and **XFreeRDP** to test initial access, brute-forcing, lateral movement, and post-exploitation response.

---

## 🛠️ Tooling & Technology Stack

### 🏗️ Infrastructure, Identity & Containerization
* **Virtualization & Networking:** VirtualBox (Custom isolated NAT Network Subnet).
* **Directory & Identity Services:** Windows Server Active Directory Domain Services (AD DS), DNS, Group Policy, `Winbind`, `Samba`, `Kerberos` (`krb5`).
* **Microservice Orchestration:** **Docker** (Containerized deployment of supporting lab applications and isolated services).
* **Mail Infrastructure:** **Postfix MTA** (Internal corporate mail relay).
* **Operating Systems:** Windows Server, Windows 11 Enterprise, Ubuntu Server 22.04, Ubuntu Desktop 22.04, Kali Linux.
<img width="1600" height="833" alt="image" src="https://github.com/user-attachments/assets/8c5a6796-58f1-4f47-8a0c-65d6ceea7607" />

### 🛡️ Defensive Operations & Telemetry (Blue Team)
* **Wazuh SIEM:** Centralized log aggregation, File Integrity Monitoring (FIM), and host-based intrusion detection (HIDS).
* **Security Onion:** Network Security Monitoring (NSM), packet capture (PCAP), and intrusion detection (Suricata/Zeek).
<img width="1600" height="849" alt="image" src="https://github.com/user-attachments/assets/eec05075-0a05-422e-bb08-1b44982df073" />

### ⚔️ Offensive Security & Exploitation (Red Team)
* **NetExec (NXC):** Network service enumeration, credential spraying, and protocol-level remote command execution.
* **Evil-WinRM:** WinRM protocol shell access for Windows post-exploitation and interactive command execution.
* **Hydra:** Fast, multi-threaded password brute-forcing across network authentication protocols.
* **SecLists:** Industry-standard dictionaries for user/password fuzzing and payload construction.
* **XFreeRDP:** Command-line Remote Desktop Protocol (RDP) client for graphical post-exploitation and session hijacking.
* **Custom Phishing Scripts:** Python/Bash mail delivery scripts interfacing with Postfix for social engineering initial access.

---

## 🏗️ Phase 0: Network Architecture & Containerized Provisioning

1. **Subnet & Virtual Network Architecture:**
   * Architected an isolated VirtualBox custom NAT network acting as the internal enterprise domain controller subnet, routing traffic securely between corporate assets, security servers, and the red team infrastructure.
2. **Containerized Infrastructure Deployment (Docker):**
   * Utilized **Docker** to rapidly spin up modular, isolated microservices and secondary lab utilities on Ubuntu Server endpoints, enforcing resource isolation and mirroring modern containerized enterprise workflows.

---

## 🔧 Phase 1: Hybrid Active Directory & Cross-Platform Identity Management

1. **Domain Controller & Active Directory Deployment:**
   * Deployed Windows Server, promoted it to a Domain Controller (DC), and configured root Active Directory forest policies, DNS forward/reverse lookup zones, and Organizational Units (OUs).
2. **Linux Active Directory Integration (Winbind / Samba):**
   * Configured Ubuntu endpoints with `winbind`, `samba`, and `pam_winbind` alongside `krb5.conf` to allow centralized Active Directory domain accounts to authenticate directly into Linux workstations via SSH and GUI logins.
3. **Internal Corporate Mail Relay (Postfix):**
   * Provisioned a dedicated **Postfix Mail Transfer Agent (MTA)** on Ubuntu Server to handle internal domain communications—establishing an authentic attack surface for email-based threats.

---

## 🛡️ Phase 2: Security Telemetry & Vulnerability Staging

1. **SIEM & Network Security Monitoring (NSM):**
   * Deployed **Wazuh SIEM Manager** and **Security Onion** on dedicated Ubuntu security nodes.
   * Forwarded system, authentication, and process events from Windows/Linux endpoints to Wazuh while tapping virtual network interfaces via Security Onion for full packet capture and signature matching.
<img width="1600" height="894" alt="image" src="https://github.com/user-attachments/assets/99d056a5-3f37-41be-822e-e0ae31f69fe4" />

2. **Controlled Security Controls Degradation:**
   * Selectively configured weak service accounts, exposed WinRM endpoints, and enabled RDP services on target endpoints to simulate real-world technical debt and misconfigurations.

---

## ⚔️ Phase 3: The Cyber Attack Lifecycle (Red Team Operations)
<img width="359" height="268" alt="attack motion" src="https://github.com/user-attachments/assets/e073f50a-eb63-4d33-bdfc-e5209316090a" />

Operating from the Kali Linux system, a multi-stage attack campaign was launched against the enterprise network:

```text
[Recon & Fuzzing] ➔ [Initial Access / Brute-Force] ➔ [Post-Exploitation & WinRM] ➔ [Lateral Movement & RDP]
  (NetExec / SecLists)     (Postfix Phishing / Hydra)         (Evil-WinRM)               (XFreeRDP)



1. **Reconnaissance & Network Service Mapping**
Executed protocol-level discovery across SMB, WinRM, and RDP using NetExec:

```bash
# Enumerating active domain hosts and exposed SMB shares
netexec smb 192.168.100.0/24 --shares
```

2. **Initial Access & Credential Access**
Phishing Campaign: Dispatched spoofed corporate emails containing malicious payloads through the internal Postfix MTA to compromise initial user credentials.

Targeted Password Brute-Forcing: Deployed Hydra paired with targeted wordlists from SecLists against exposed service ports to identify weak password policies:

```bash
# Multi-threaded dictionary attack against exposed domain services
hydra -L /usr/share/seclists/Usernames/top-usernames.txt -P /usr/share/seclists/Passwords/Common-Credentials/10k-most-common.txt 192.168.100.10 smb
```

3. **Remote Shell Access & Command Execution**
WinRM Remote Exploitation: Upon acquiring valid domain admin/user credentials, established an interactive PowerShell shell session using Evil-WinRM:

```bash
# Authenticating into target Windows host via WinRM
evil-winrm -i 192.168.100.10 -u 'DomainAdmin' -p 'P@ssword123!'
```

Protocol-Wide Execution: Executed remote commands across multiple subnet endpoints simultaneously using NetExec to establish persistence.

4. **Graphical Lateral Movement (GUI Hijacking)**
Utilized XFreeRDP to establish authenticated remote desktop sessions on compromised Windows assets, enabling visual reconnaissance, registry inspection, and local data exfiltration:

```bash
# Spawning interactive GUI RDP session
xfreerdp /v:192.168.100.10 /u:'DomainAdmin' /p:'P@ssword123!' /dynamic-resolution /cert:ignore
```
