# Playbook 09 - Full Enterprise Security Assessment

## Objective

Perform a structured enterprise-wide network security assessment using Nmap as the primary network discovery and enumeration tool.

This playbook provides a repeatable methodology for planning, executing, documenting, and reporting an authorized security assessment.

---

# Assessment Lifecycle

```

                    Engagement Planning
                             │
                             ▼
                  Scope Definition
                             │
                             ▼
                Rules of Engagement
                             │
                             ▼
                  Network Discovery
                             │
                             ▼
                   Asset Inventory
                             │
                             ▼
                 Service Discovery
                             │
                             ▼
              Operating System Detection
                             │
                             ▼
               Service Enumeration
                             │
                             ▼
            Infrastructure Classification
                             │
                             ▼
                Security Analysis
                             │
                             ▼
                  Risk Assessment
                             │
                             ▼
               Report Preparation
                             │
                             ▼
               Executive Presentation

```

---

# Phase 1 – Engagement Planning

## Objective

Understand the assessment before touching the network.

## Activities

- Review engagement objectives
- Identify stakeholders
- Confirm authorization
- Review assessment scope
- Confirm assessment window
- Identify communication channels
- Identify emergency contacts

## Deliverables

- Approved Scope
- Rules of Engagement
- Assessment Schedule

---

# Phase 2 – Scope Definition

Clearly identify:

- Target IP ranges
- Domains
- Cloud assets
- Excluded systems
- Critical business systems
- Production limitations

Example:

Scope

192.168.10.0/24

Excluded

192.168.10.50

Reason

Production Database

---

# Phase 3 – Rules of Engagement

Confirm:

✔ Approved scanning windows

✔ Approved scan intensity

✔ Approved NSE scripts

✔ Emergency stop procedure

✔ Reporting requirements

---

# Phase 4 – Network Discovery

## Goal

Identify every active device.

### Command

```bash
nmap -sn 192.168.10.0/24
```

Collect

- IP
- Hostname
- Vendor
- MAC

---

# Phase 5 – Asset Inventory

Classify assets.

Example

| Asset | Type |
|--------|------|
| DC01 | Domain Controller |
| FS01 | File Server |
| SQL01 | Database Server |
| WEB01 | Web Server |
| FW01 | Firewall |
| SW01 | Core Switch |
| AP01 | Wireless Controller |

---

# Phase 6 – Port Discovery

Command

```bash
nmap -sS <target>
```

Collect

- Open Ports
- Closed Ports
- Filtered Ports

---

# Phase 7 – Service Detection

Command

```bash
nmap -sV <target>
```

Collect

- Service
- Vendor
- Version

---

# Phase 8 – Operating System Detection

Command

```bash
nmap -O <target>
```

Collect

- Operating System
- Kernel Estimate
- Device Type

---

# Phase 9 – Service Enumeration

Perform safe service enumeration using appropriate NSE scripts.

Examples

- HTTP
- HTTPS
- DNS
- LDAP
- SMB
- SSH
- FTP
- MySQL
- MSSQL

Document all confirmed observations.

---

# Phase 10 – Infrastructure Classification

Classify discovered assets.

Examples

Servers

- Domain Controllers
- File Servers
- Web Servers
- Database Servers

Network Devices

- Firewalls
- Routers
- Switches
- Load Balancers

Endpoints

- Windows Workstations
- Linux Workstations
- macOS Devices

Other

- Printers
- IoT Devices
- Cameras
- NAS Appliances

---

# Phase 11 – Security Analysis

Review findings for:

- Legacy protocols
- Unsupported operating systems
- Unnecessary exposed services
- Weak encryption protocols
- Inconsistent configurations
- Missing segmentation
- Unexpected devices

---

# Phase 12 – Risk Assessment

Prioritize observations based on:

- Business Impact
- Likelihood
- Asset Criticality
- Exposure
- Ease of Remediation

Example

| Observation | Risk |
|-------------|------|
| Unsupported Windows Server | High |
| Anonymous FTP Enabled | High |
| SMBv1 Enabled | High |
| Self-Signed Certificate | Medium |
| Legacy TLS Version | Medium |

---

# Phase 13 – Documentation

Record

- Commands executed
- Screenshots
- Scan outputs
- Asset inventory
- Findings
- Recommendations

Maintain clear evidence for every reported observation.

---

# Phase 14 – Reporting

Prepare two reports.

## Executive Report

Audience

- Management
- Executives
- Clients

Contents

- Scope
- Overall Risk
- Key Findings
- Business Impact
- Recommendations

---

## Technical Report

Audience

- Security Teams
- System Administrators
- Engineers

Contents

- Commands Used
- Scan Results
- Screenshots
- Technical Findings
- Evidence
- Recommendations

---

# Assessment Deliverables

- Executive Summary
- Technical Report
- Asset Inventory
- Service Inventory
- Network Diagram (if applicable)
- Risk Register
- Recommendations

---

# Reporting Checklist

- [ ] Scope documented
- [ ] Rules of Engagement confirmed
- [ ] Hosts identified
- [ ] Ports documented
- [ ] Services identified
- [ ] Operating systems identified
- [ ] Evidence collected
- [ ] Screenshots captured
- [ ] Findings validated
- [ ] Risk assigned
- [ ] Recommendations prepared
- [ ] Reports reviewed

---

# Lessons Learned

A successful enterprise assessment depends on more than technical scanning. Careful planning, accurate documentation, validation of findings, and clear communication are essential to producing actionable results.

---

# Professional Notes

Nmap is one component of a comprehensive security assessment. In professional engagements, its results are typically correlated with vulnerability scanners, configuration reviews, log analysis, and manual validation to develop a complete understanding of the environment. Every assessment should be conducted within the agreed scope and rules of engagement, with findings verified before they are reported.

---

# References

- Nmap Reference Guide
- NIST SP 800-115 (Technical Guide to Information Security Testing and Assessment)
- NIST Cybersecurity Framework (CSF)
- CIS Controls
- OWASP Testing Guide
