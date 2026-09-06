# Case Study 01 - Home Lab Network Assessment

---

# Executive Summary

A structured network assessment was conducted within a controlled home laboratory to identify active hosts, enumerate exposed services, verify operating systems, and document the environment.

The assessment followed a phased methodology beginning with network discovery and ending with reporting.

No intrusive testing or exploitation was performed.

---

# Assessment Overview

Assessment Type

Internal Network Assessment

Environment

Home Laboratory

Authorization

Self-Owned Lab

Status

Completed

---

# Objective

The objective was to:

- Discover active hosts
- Identify exposed services
- Detect operating systems
- Build an asset inventory
- Practice professional reporting

---

# Scope

Network

192.168.1.0/24

Included

Entire subnet

Excluded

Internet Hosts

Production Systems

---

# Rules of Engagement

Assessment Window

09:00 – 11:30

Allowed Activities

- Host Discovery
- Port Scanning
- Service Detection
- OS Detection
- Safe NSE Scripts

Prohibited

- Password Attacks
- Denial-of-Service
- Exploitation
- Configuration Changes

---

# Lab Environment

| Device | Role |
|---------|------|
| Kali Linux | Assessment Workstation |
| Windows VM | Client System |
| Ubuntu Server | Linux Server |
| pfSense | Firewall |
| Home Router | Gateway |

---

# Tools Used

## Security Assessment Tools 
- Nmap
- Zenmap
- Wireshark
- hping3 (packet analysis only)


## Documentation Tools
- Markdown
- Visual Studio Code

---

## Assessment Methodology

The assessment was conducted using a phased network security assessment methodology. Each phase was completed within the defined scope and according to the established rules of engagement.

### Phase 1 — Network Discovery

The assessment began with network discovery to identify active hosts within the authorized `192.168.1.0/24` subnet.

The objective was to determine which systems were reachable before performing more detailed enumeration.

### Phase 2 — Host Identification

Discovered hosts were reviewed and documented to establish an initial asset inventory.

Each identified host was recorded based on its IP address and, where available, hostname and inferred role.

### Phase 3 — Port and Service Enumeration

Active hosts were scanned for open ports and exposed services.

Service and version detection were used where appropriate to determine the applications and services running on identified systems.

### Phase 4 — Operating System Detection

Nmap OS detection techniques were used to identify the likely operating system of discovered hosts.

The results were compared with the known laboratory environment where possible.

### Phase 5 — Additional Analysis

Additional network analysis was performed using available tools such as Wireshark and hping3 where required.

These tools were used for traffic and packet analysis only and were not used for exploitation or disruptive testing.

### Phase 6 — Findings and Risk Analysis

The collected information was reviewed to identify security-relevant observations, exposed services, and potential risks within the laboratory environment.

Findings were documented together with their potential impact and recommended remediation or security improvements.

### Phase 7 — Reporting

The assessment results, supporting evidence, observations, and recommendations were consolidated into this case study.

The final report provides a documented record of the assessment methodology and results. 
