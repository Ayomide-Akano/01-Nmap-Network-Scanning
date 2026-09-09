# Final Nmap Security Assessment Report

## Executive Summary

This report presents the final security assessment of a Metasploitable 2 host performed within an authorized virtualized laboratory environment using Nmap.

The assessment progressed from host discovery and network mapping through TCP and UDP port scanning, service and version enumeration, operating system detection, Nmap Scripting Engine (NSE) enumeration, filtering analysis, scan optimization, evidence collection, and security risk assessment.

The assessment identified a broad and largely legacy network attack surface containing multiple remote access services, file and network sharing services, database services, web/application services, and other network-facing services.

The most significant observation was the exposure of a root shell service on TCP port 1524. Other high-risk observations included SMBv1 support, disabled SMB message signing, anonymous SMB read/write access, NFS exposure, legacy database services, and multiple legacy application and remote access services.

The overall assessment result for the laboratory target is:

> **HIGH RISK**

The risk ratings in this report represent analyst-level assessment based on observed exposure and configuration. They are not formal CVSS scores.

No destructive testing or unauthorized exploitation was performed.

---

## Assessment Overview

| Item | Details |
|---|---|
| Assessment Type | Network Security Assessment |
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Scanner | Linux system |
| Scanner IP | `192.168.43.155` |
| Environment | Authorized virtualized laboratory |
| Primary Tool | Nmap |
| Nmap Version | 7.99 |
| Assessment Focus | Network discovery, enumeration, filtering analysis, NSE, and security assessment |
| Exploitation | Not performed |
| Overall Risk | **HIGH** |

---

## Objective

The objective of the assessment was to demonstrate a structured Nmap-based security assessment process and determine what could be learned about an intentionally vulnerable host through network scanning and service enumeration.

The assessment focused on:

- Identifying active hosts
- Mapping the target network
- Identifying exposed TCP services
- Identifying exposed UDP services
- Detecting service versions
- Identifying the target operating system
- Performing targeted NSE enumeration
- Examining packet filtering behavior
- Evaluating scan timing and optimization
- Preserving scan evidence
- Identifying security-relevant exposures
- Prioritizing findings
- Developing remediation recommendations

---

## Assessment Methodology

The assessment was performed progressively through six practical scenarios.

### Scenario 01 — Host Discovery and Network Mapping

The target network was mapped and active hosts were identified.

The assessment identified:

- Gateway: `192.168.43.1`
- Metasploitable 2: `192.168.43.56`
- Scanning Linux system: `192.168.43.155`

Service/version enumeration and operating system detection were then performed against the target.

---

### Scenario 02 — TCP and UDP Port Scanning

Multiple TCP and UDP scanning techniques were performed to compare visibility across protocols and scanning scopes.

The assessment demonstrated that:

- Default TCP scanning identified 23 open TCP ports.
- A 1–1000 TCP scan identified 12 open ports.
- A full TCP scan identified additional services beyond the first 1000 ports.
- UDP scanning identified services that were not visible through TCP scanning.
- Targeted UDP scanning provided faster examination of selected services.

---

### Scenario 03 — Firewall Evasion and Filtering Analysis

Different packet types and scanning techniques were compared to observe how the target environment responded.

The assessment included:

- ACK scanning
- FIN scanning
- NULL scanning
- Xmas scanning
- Packet fragmentation
- Custom MTU
- Decoy scanning
- Source-port manipulation
- Invalid checksum testing
- MAC address spoofing
- Baseline SYN scanning

The results demonstrated differences in packet handling and port-state visibility.

Filtered results were interpreted cautiously because filtering alone does not conclusively prove the presence of a firewall. Target, network, or virtualization behavior may also influence the results.

---

### Scenario 04 — Scan Optimization, Timing and Output Formats

The assessment examined the effect of timing and performance controls on scan duration.

Tests included:

- Default timing
- `-T3`
- `-T4`
- Scan delays
- Retry and timeout controls
- Maximum packet rates
- Verbose output
- Reason reporting
- Normal output
- XML output
- Grepable output

The results demonstrated that scan configuration can significantly affect assessment duration and evidence collection.

---

### Scenario 05 — NSE Service Enumeration

Targeted NSE enumeration was performed against selected services:

- FTP
- SMB
- HTTP
- SMTP
- MySQL

The enumeration provided additional information about service configuration, supported protocols, authentication behavior, exposed resources, and other security-relevant characteristics.

---

### Scenario 06 — Comprehensive Security Assessment

Scenario 06 serves as the capstone assessment.

It consolidates the technical evidence generated during Scenarios 01–05 into a complete security assessment workflow:

**Host Discovery**

→ **TCP/UDP Scanning**

→ **Service Enumeration**

→ **OS Detection**

→ **NSE Enumeration**

→ **Filtering Analysis**

→ **Evidence Collection**

→ **Security Findings**

→ **Risk Prioritization**

→ **Remediation Recommendations**

Scenario 06 does not represent a separate lab execution. It is the analytical consolidation of the evidence and results produced during the preceding scenarios.

---

# Key Technical Observations

## Network Attack Surface

The target exposed a large number of TCP services across multiple functional categories.

Observed services included:

- FTP
- SSH
- Telnet
- SMTP
- DNS
- HTTP
- RPC
- SMB
- Remote shell services
- NFS
- FTP on an alternate port
- MySQL
- PostgreSQL
- VNC
- X11
- IRC
- AJP
- Apache Tomcat
- Java RMI

The breadth of exposed services significantly increases the number of potential attack paths that require security management.

---

## UDP Exposure

UDP scanning identified several services that were not visible through TCP-only scanning.

Observed UDP services included:

- DNS
- RPCBind
- NetBIOS
- NFS-related services
- Additional ports reported as `open|filtered`

This demonstrated the importance of including both TCP and UDP scanning when performing a broader network assessment.

---

## Service and Version Exposure

Service enumeration identified numerous legacy software versions, including:

- vsftpd 2.3.4
- OpenSSH 4.7p1
- Apache 2.2.8
- BIND 9.4.2
- MySQL 5.0.51a
- PostgreSQL 8.3
- Samba 3.0.20-Debian
- Apache Tomcat 5.5

These versions indicate a heavily outdated laboratory system.

Version identification alone does not prove that a service is exploitable, but it provides important information for subsequent security assessment and remediation planning.

---

# NSE Assessment

Targeted NSE enumeration produced several significant observations.

### FTP

- Anonymous FTP login was permitted.
- FTP control and data communication were observed as plaintext.
- vsFTPd version 2.3.4 was identified.

### SMB

- SMBv1 was supported.
- SMB message signing was disabled.
- Guest authentication was observed.
- Anonymous access to selected shares was identified.
- The `IPC$` and `tmp` shares permitted anonymous read/write access.
- Samba 3.0.20-Debian was identified.

### HTTP

- Apache 2.2.8 was identified.
- PHP 5.2.4 was identified.
- HTTP methods included GET, HEAD, POST, and OPTIONS.
- Several discoverable directories and resources were identified, including:
  - `/tikiwiki/`
  - `/test/`
  - `/phpinfo.php`
  - `/phpMyAdmin/`
  - `/doc/`
  - `/icons/`
  - `/index/`

### SMTP

SMTP capabilities included:

- PIPELINING
- SIZE
- VRFY
- ETRN
- STARTTLS
- ENHANCEDSTATUSCODES
- 8BITMIME
- DSN

Additional NSE validation identified SSLv2 support and an expired certificate.

### MySQL

MySQL 5.0.51a-3ubuntu5 was identified.

The selected MySQL enumeration scripts did not return database or user information.

---

# Security Findings

| ID | Finding | Risk |
|---|---|---|
| F-01 | Excessive Network Attack Surface | High |
| F-02 | Anonymous FTP Access | Medium |
| F-03 | Plaintext FTP Communication | Medium |
| F-04 | Legacy Remote Access Services | High |
| F-05 | SMBv1 Supported | High |
| F-06 | SMB Message Signing Disabled | High |
| F-07 | Anonymous SMB Read/Write Access | High |
| F-08 | NFS Service Exposed | High |
| F-09 | Legacy Database Services Exposed | High |
| F-10 | Legacy Web/Application Services | High |
| F-11 | Web Information Disclosure | Medium |
| F-12 | SMTP Legacy Security Exposure | Medium |
| F-13 | Metasploitable Root Shell Service | **Critical** |
| F-14 | Legacy IRC Service | Medium |

---

# Risk Prioritization

## Critical Priority

### F-13 — Metasploitable Root Shell Service

TCP port `1524` exposed a service identified as a bindshell associated with a root shell on the intentionally vulnerable Metasploitable 2 system.

This represents the most significant observed exposure because the service indicates direct high-privilege remote access.

**Recommended action:**

- Remove the service.
- Restrict access to trusted administrative systems.
- Replace the vulnerable system or rebuild it using supported software.
- Verify that the port is no longer externally accessible after remediation.

---

## High Priority

### F-01 — Excessive Network Attack Surface

The target exposed numerous network-facing services across several protocols.

**Recommended action:**

- Disable unnecessary services.
- Restrict administrative services.
- Apply network segmentation.
- Permit only required ports through host and network firewalls.

---

### F-04 — Legacy Remote Access Services

Telnet and other legacy remote access services were exposed.

**Recommended action:**

- Disable Telnet and other unnecessary legacy remote access protocols.
- Use secure alternatives such as SSH.
- Restrict administrative access by source network.

---

### F-05 — SMBv1 Supported

SMBv1 was identified during NSE enumeration.

**Recommended action:**

- Disable SMBv1.
- Require modern SMB versions.
- Restrict SMB exposure to trusted network segments.

---

### F-06 — SMB Message Signing Disabled

SMB message signing was reported as disabled.

**Recommended action:**

- Enable SMB signing according to organizational requirements.
- Restrict SMB traffic across untrusted network boundaries.

---

### F-07 — Anonymous SMB Read/Write Access

Anonymous read/write access was observed on selected SMB shares.

**Recommended action:**

- Remove anonymous access.
- Apply least-privilege permissions.
- Review share permissions.
- Restrict write access to authorized users.

---

### F-08 — NFS Service Exposed

NFS was accessible from the network.

**Recommended action:**

- Restrict NFS access to trusted systems.
- Review export permissions.
- Apply least-privilege access controls.

---

### F-09 — Legacy Database Services Exposed

MySQL and PostgreSQL services were network accessible.

**Recommended action:**

- Restrict database services to application or administrative networks.
- Upgrade unsupported database software.
- Apply strong authentication and access controls.

---

### F-10 — Legacy Web/Application Services

Legacy Apache, PHP, Tomcat, and related application services were exposed.

**Recommended action:**

- Upgrade unsupported software.
- Remove unnecessary applications.
- Restrict administrative interfaces.
- Review application configuration and exposed directories.

---

# Medium Priority Findings

### F-02 — Anonymous FTP Access

Anonymous FTP access was permitted.

**Recommendation:** Disable anonymous FTP unless explicitly required and properly isolated.

---

### F-03 — Plaintext FTP Communication

FTP communication was observed as plaintext.

**Recommendation:** Replace FTP with a secure file transfer mechanism such as SFTP or another appropriately protected protocol.

---

### F-11 — Web Information Disclosure

Web enumeration identified several directories and resources, including `phpinfo.php` and phpMyAdmin.

**Recommendation:** Remove unnecessary information-disclosure resources and restrict administrative interfaces.

---

### F-12 — SMTP Legacy Security Exposure

SMTP enumeration identified legacy capabilities including SSLv2 support and an expired certificate.

**Recommendation:** Disable obsolete cryptographic protocols and maintain valid certificates and modern TLS configurations.

---

### F-14 — Legacy IRC Service

An IRC service was exposed on the target.

**Recommendation:** Disable unnecessary IRC services or restrict them to trusted systems.

---

# Remediation Strategy

Remediation should follow a risk-based sequence.

### Priority 1 — Remove High-Impact Remote Access

- Remove the root shell service.
- Disable unnecessary remote administration services.
- Replace insecure protocols with secure alternatives.

### Priority 2 — Reduce Network Exposure

- Disable unnecessary services.
- Restrict exposed ports.
- Apply host-based and network-level filtering.
- Segment sensitive services.

### Priority 3 — Harden SMB and File Sharing

- Disable SMBv1.
- Enable SMB signing where appropriate.
- Remove anonymous access.
- Review share permissions.

### Priority 4 — Modernize Legacy Services

- Upgrade unsupported operating systems and applications.
- Upgrade legacy database services.
- Upgrade web and application servers.

### Priority 5 — Harden Application and Protocol Configuration

- Remove information-disclosure resources.
- Disable obsolete cryptographic protocols.
- Replace plaintext protocols.
- Maintain valid certificates.

---

# Evidence Summary

The assessment evidence was generated through the practical scanning scenarios and preserved through screenshots and Nmap output files.

| Evidence Area | Supporting Portfolio Section |
|---|---|
| Host Discovery | Scenario 01 |
| TCP Scanning | Scenario 02 |
| UDP Scanning | Scenario 02 |
| Service Enumeration | Scenario 01 / Scenario 02 |
| OS Detection | Scenario 01 |
| Filtering Analysis | Scenario 03 |
| Scan Optimization | Scenario 04 |
| Output Formats | Scenario 04 |
| NSE Enumeration | Scenario 05 |
| Consolidated Assessment | Scenario 06 |
| Visual Evidence | Screenshots |
| Analytical Findings | Case Studies |

---

# Assessment Limitations

The assessment has several limitations:

- Testing was performed against an intentionally vulnerable Metasploitable 2 laboratory system.
- The environment was virtualized and may not reproduce behavior found on production networks.
- No destructive testing was performed.
- No unauthorized exploitation was performed.
- Service version identification does not independently prove exploitability.
- Filtered scan results do not conclusively prove the presence of a firewall.
- NSE enumeration was targeted toward selected services rather than being an exhaustive assessment of every available script.
- Risk ratings represent analyst-level assessment rather than formal CVSS scoring.

---

# Lessons Learned

The project demonstrated several important principles of network security assessment.

### 1. Discovery Comes Before Assessment

A security assessment begins with understanding what hosts and services are actually present.

### 2. TCP and UDP Provide Different Visibility

A TCP-only assessment can miss services exposed through UDP.

### 3. Scan Scope Matters

Limited port ranges can fail to reveal services running on higher ports.

### 4. Enumeration Provides Context

Service and version detection provide more useful assessment information than simply identifying an open port.

### 5. NSE Extends Enumeration

NSE can expose configuration and security-relevant information that basic scanning may not reveal.

### 6. Results Require Interpretation

Nmap output is evidence. Security findings require analysis and context.

### 7. Evidence Preservation Matters

Saving scan results in appropriate formats supports repeatability, comparison, and professional reporting.

### 8. Risk Prioritization Matters

A security assessment should distinguish between observations and the findings that require the greatest remediation attention.

---

# Final Security Assessment

The Metasploitable 2 target presented a **HIGH-RISK** security posture within the laboratory environment.

The assessment identified:

- A broad network attack surface
- Multiple legacy services
- Insecure remote access protocols
- Anonymous FTP access
- Plaintext FTP communication
- SMBv1 support
- Disabled SMB message signing
- Anonymous SMB read/write access
- NFS exposure
- Legacy database services
- Legacy web/application services
- Web information disclosure
- Legacy SMTP security characteristics
- A root shell service exposed on TCP port 1524

The most significant priority is removal or strict isolation of the root shell service, followed by reducing unnecessary network exposure and modernizing legacy services.

The assessment demonstrates the complete progression from network discovery to security analysis:

**Discover → Scan → Enumerate → Analyze → Identify Findings → Prioritize → Recommend Remediation**

---

# Portfolio Capstone

This report represents the final analytical output of the Nmap Network Scanning portfolio.

The project demonstrates practical experience with:

- Nmap host discovery
- TCP and UDP scanning
- Service and version detection
- Operating system detection
- NSE enumeration
- Packet and filtering analysis
- Scan optimization
- Output management
- Evidence collection
- Security finding development
- Risk prioritization
- Technical security reporting

The project therefore moves beyond command memorization and demonstrates how Nmap results can be interpreted and communicated as part of a structured security assessment.

---

# Responsible Use

Nmap and other network security tools should only be used against systems and networks for which appropriate authorization has been obtained.

All practical scanning documented in this portfolio was performed within a controlled laboratory environment using an intentionally vulnerable Metasploitable 2 target.

---

# Conclusion

This assessment demonstrated a structured approach to network security assessment using Nmap.

The project began with basic host discovery and progressed through increasingly detailed technical analysis before consolidating the results into a professional security assessment.

The final outcome demonstrates not only the ability to execute Nmap scans, but also the ability to interpret technical evidence, identify security risks, prioritize findings, and communicate remediation recommendations.

This represents the completed capstone of the Nmap Network Scanning portfolio.
```
