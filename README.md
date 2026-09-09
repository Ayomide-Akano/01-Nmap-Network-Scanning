# Nmap Network Scanning Lab

A practical network reconnaissance and security assessment project demonstrating the use of Nmap for host discovery, TCP and UDP port scanning, service and version enumeration, operating system detection, Nmap Scripting Engine (NSE)-based reconnaissance, network filtering analysis, scan optimization, evidence collection, and structured security documentation.

This repository combines hands-on network security practice with technical documentation, showing how technical procedures, networking concepts, observations, practical evidence, security findings, and assessment recommendations can be organized into clear and reusable documentation.

---

## Project Overview

This project explores the practical use of Nmap for network discovery and security assessment within an authorized laboratory environment.

The project uses an intentionally vulnerable Metasploitable 2 system as the primary assessment target and progresses from basic network discovery to a consolidated security assessment.

**The project covers:**

- Host discovery
- Network mapping
- TCP and UDP port scanning
- TCP SYN and Connect scanning
- FIN, NULL, and Xmas scanning
- Service and version enumeration
- Operating system detection
- Nmap Scripting Engine (NSE)
- FTP, SMB, HTTP, SMTP, and MySQL enumeration
- Scan optimization and timing
- Firewall and packet-filtering analysis
- Packet fragmentation and MTU testing
- Decoy and source-port techniques
- MAC address spoofing
- Nmap output formats
- Practical network scanning scenarios
- Evidence collection
- Security findings analysis
- Risk prioritization
- Security assessment documentation
- Technical reporting

The documentation is designed to demonstrate both hands-on network security skills and the ability to communicate technical procedures, observations, evidence, and findings clearly.

---

## Objectives

The primary objectives of this project are to:

- Discover live hosts within an authorized laboratory network
- Map the target network
- Identify open, closed, and filtered ports
- Compare TCP and UDP scanning behavior
- Enumerate running services and their versions
- Perform operating system detection
- Explore Nmap Scripting Engine capabilities
- Understand different network scanning techniques
- Examine how packet filtering affects scan results
- Explore scan timing and performance optimization
- Preserve practical scan evidence
- Document technical procedures in a structured format
- Analyze scan results and identify security-relevant observations
- Develop structured security findings
- Prioritize identified risks
- Produce professional security assessment documentation

---

## Lab Environment

### Assessment Machine

- **Operating System:** Kali Linux
- **Primary Tool:** Nmap
- **Nmap Version:** 7.99
- **Supporting Tools:** Hping3 and other network-analysis utilities where applicable

### Target Environment

- **Target:** Metasploitable 2
- **Target IP:** `192.168.43.56`
- **Scanner IP:** `192.168.43.155`
- **Network:** `192.168.43.0/24`
- **Gateway:** `192.168.43.1`
- **Environment:** Virtualized laboratory
- **Purpose:** Educational security assessment and technical documentation

> **Note:** Actual scan results, screenshots, and findings are documented only where the corresponding assessment was performed in the authorized laboratory environment.

---

## Prerequisites

Readers following the practical sections should have:

- Basic knowledge of computer networking
- Familiarity with IPv4 addressing and CIDR notation
- Basic understanding of TCP/IP
- Basic Linux command-line experience
- Access to an authorized laboratory environment
- Kali Linux or another environment with Nmap installed

---

## Skills Demonstrated

### Technical Skills

- Network reconnaissance
- Host discovery
- Network mapping
- TCP and UDP port scanning
- Service and version enumeration
- Operating system fingerprinting
- Nmap Scripting Engine (NSE)
- FTP, SMB, HTTP, SMTP, and MySQL enumeration
- Scan timing and optimization
- Firewall and packet-filtering analysis
- Packet behavior analysis
- Basic vulnerability reconnaissance
- Network security assessment
- Risk identification and prioritization
- Security remediation planning

### Documentation Skills

- Technical procedure writing
- Structured Markdown documentation
- Technical concept explanation
- Command reference development
- Step-by-step instructional writing
- Information architecture
- Evidence-based documentation
- Security assessment reporting
- Technical findings analysis
- Evidence organization
- Cross-referencing technical documentation
- Technical communication

---

## Documentation Workflow

The repository follows a structured workflow for turning a technical security task into usable documentation:

```text
Technical Concept
       │
       ▼
Command / Technique
       │
       ▼
Practical Scanning Scenario
       │
       ▼
Actual Assessment
       │
       ▼
Evidence / Screenshots
       │
       ▼
Analysis & Interpretation
       │
       ▼
Case Study
       │
       ▼
Final Security Assessment Report
```

This structure separates:

- What the technique is
- How the technique is performed
- What actually happened during the assessment
- What evidence was collected
- What the results mean from a security perspective

---

# Practical Scanning Scenarios

The practical work is divided into six scenarios that progressively build toward a complete security assessment.

### Scenario 01 — Host Discovery and Network Mapping

Focuses on:

- Host discovery
- Network mapping
- TCP port discovery
- Service and version detection
- Operating system detection
- Initial target enumeration

[View Scenario 01](./Practical%20Scanning%20Scenarios/Scenario%2001-Host-Discovery-and-Network-Mapping.md)

---

### Scenario 02 — TCP and UDP Port Scanning

Focuses on:

- TCP SYN scanning
- TCP Connect scanning
- FIN scanning
- NULL scanning
- Xmas scanning
- Full TCP port scanning
- UDP scanning
- Targeted UDP scanning
- Combined TCP and UDP scanning
- UDP service detection
- Comparing scan visibility and scope

[View Scenario 02](./Practical%20Scanning%20Scenarios/02-TCP-and-UDP-Port-Scanning.md)

---

### Scenario 03 — Firewall Evasion and Filtering Analysis

Focuses on:

- ACK scanning
- FIN, NULL, and Xmas scans
- Packet fragmentation
- Custom MTU
- Decoy scanning
- Source-port manipulation
- Invalid checksum testing
- MAC address spoofing
- Baseline comparison
- Packet-filtering behavior

[View Scenario 03](./Practical%20Scanning%20Scenarios/03-Firewall-Evasion-and-Filtering.md)

---

### Scenario 04 — Scan Optimization, Timing and Output Formats

Focuses on:

- Timing templates
- Scan delays
- Retry and timeout controls
- Maximum packet rates
- Verbose scanning
- Reason reporting
- Normal output
- XML output
- Grepable output
- Evidence preservation

[View Scenario 04](./Practical%20Scanning%20Scenarios/04-Scan-Optimization-Timing-and-Output-Formats.md)

---

### Scenario 05 — NSE Service Enumeration

Focuses on targeted Nmap Scripting Engine enumeration of:

- FTP
- SMB
- HTTP
- SMTP
- MySQL

The scenario demonstrates how NSE can provide additional service, configuration, authentication, and security-relevant information beyond basic port scanning.

[View Scenario 05](./Practical%20Scanning%20Scenarios/05-NSE-Service-Enumeration.md)

---

### Scenario 06 — Comprehensive Security Assessment

Scenario 06 is the capstone assessment.

It consolidates the evidence and findings generated during Scenarios 01–05 into a complete security assessment workflow:

**Host Discovery**

→ **TCP / UDP Scanning**

→ **Service Enumeration**

→ **OS Detection**

→ **NSE Enumeration**

→ **Filtering Analysis**

→ **Evidence Collection**

→ **Security Findings**

→ **Risk Prioritization**

→ **Remediation Recommendations**

> **Important:** Scenario 06 is an analytical capstone based on the evidence generated during Scenarios 01–05. It does not represent a separate laboratory execution or a new set of commands.

[View Scenario 06](./Practical%20Scanning%20Scenarios/06-Comprehensive-Security-Assessment.md)

---

# Repository Structure

```text
01-Nmap-Network-Scanning/
│
├── Commands/
│   ├── README.md
│   └── Nmap-Command-Reference.md
│
├── Concepts/
│   ├── README.md
│   ├── IP-Identification.md
│   ├── TCP-Reset.md
│   └── ICMP-Messages.md
│
├── Diagrams/
│   ├── README.md
│   └── 01–08 technical diagrams
│
├── Practical Scanning Scenarios/
│   ├── README.md
│   └── 01–06 practical scenarios
│
├── Case-Studies/
│   ├── README.md
│   └── 01–06 security assessment case studies
│
├── References/
│   ├── README.md
│   ├── Nmap-References.md
│   └── TCP-Networking-References.md
│
├── Screenshots/
│   ├── README.md
│   └── Scenario-01–05 evidence
│
├── report/
│   └── Final-Security-Assessment-Report.md
│
└── README.md
```

---

# Documentation Sections

## Commands

Contains the practical Nmap command reference developed during the project.

Topics include:

- Host discovery
- Port scanning
- Service enumeration
- Operating system detection
- NSE
- Firewall and filtering techniques
- Scan optimization
- Timing
- Output formats

**Purpose:** Understand what commands are available, how they are structured, and what each technique is used for.

[Explore Commands](./Commands/)

---

## Concepts

Contains concise explanations of networking and Nmap-related concepts used throughout the project.

Topics include:

- IP identification
- TCP behavior
- TCP Reset (RST)
- ICMP messages
- Port and packet behavior

**Purpose:** Understand the networking principles behind the scanning techniques.

[Explore Concepts](./Concepts/)

---

## Diagrams

Contains visual explanations of networking and Nmap processes.

The directory includes:

1. Network Topology
2. TCP Three-Way Handshake
3. Host Discovery and Network Mapping
4. TCP SYN Scan
5. TCP and UDP Visibility
6. Filtering and ACK Analysis
7. NSE Enumeration Workflow
8. Comprehensive Security Assessment

**Purpose:** Make technical processes easier to understand and communicate visually.

[Explore Diagrams](./Diagrams/)

---

## Practical Scanning Scenarios

Contains the hands-on laboratory exercises performed during the project.

The six scenarios progress from basic discovery and scanning techniques to a consolidated security assessment.

**Purpose:** Demonstrate practical application of Nmap techniques within an authorized laboratory.

[Explore Practical Scenarios](./Practical%20Scanning%20Scenarios/)

---

## Screenshots

Contains visual evidence captured during the practical scanning scenarios.

Screenshots are organized into:

- Scenario-01
- Scenario-02
- Scenario-03
- Scenario-04
- Scenario-05

Scenario 06 does not have a separate screenshot directory because it consolidates evidence generated during the previous practical scenarios.

**Purpose:** Provide visual evidence of the practical work performed.

[Explore Screenshots](./Screenshots/)

---

## Case Studies

Contains structured security assessment case studies derived from the practical scan results.

The case studies move from technical observations to:

- Security findings
- Risk assessment
- Risk prioritization
- Evidence mapping
- Remediation recommendations
- Assessment limitations
- Security conclusions

**Purpose:** Demonstrate the ability to interpret technical scan results as security findings.

[Explore Case Studies](./Case-Studies/)

---

## References

Contains the technical sources used to research and develop the project's Nmap documentation, networking concepts, and diagrams.

The reference collection includes:

- Official Nmap documentation
- Nmap Reference Guide
- Nmap Network Scanning
- Nmap Scripting Engine documentation
- TCP networking references
- TCP three-way handshake resources

**Purpose:** Provide a research trail supporting the technical documentation and concepts.

[Explore References](./References/)

---

## Final Report

Contains the final professional security assessment report.

The report consolidates the technical evidence and findings generated throughout the project and includes:

- Executive summary
- Assessment methodology
- Technical observations
- NSE assessment
- Security findings
- Risk prioritization
- Remediation strategy
- Evidence summary
- Assessment limitations
- Lessons learned
- Final security assessment

**Purpose:** Demonstrate professional security assessment and technical reporting.

[View Final Report](./report/Final-Security-Assessment-Report.md)

---

# Key Assessment Results

The assessment identified a broad network attack surface on the Metasploitable 2 target.

Observed services included:

- FTP
- SSH
- Telnet
- SMTP
- DNS
- HTTP
- RPCBind
- SMB
- Remote shell services
- NFS
- MySQL
- PostgreSQL
- VNC
- X11
- IRC
- AJP
- Apache Tomcat
- Java RMI

Additional NSE enumeration identified security-relevant conditions including:

- Anonymous FTP access
- Plaintext FTP communication
- SMBv1 support
- Disabled SMB message signing
- Anonymous SMB read/write access
- Web information disclosure
- Legacy application and database services
- Legacy SMTP security characteristics
- Root shell service exposure on TCP port `1524`

The overall laboratory assessment was rated:

> **HIGH RISK**

with the root shell service identified as the most critical observed exposure.

> **Assessment note:** Risk ratings represent analyst-level assessment based on observed exposure and configuration. They are not formal CVSS scores.

---

# Security Assessment Findings

The consolidated assessment identified the following findings:

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

These findings are documented in greater detail within the corresponding case studies and final security assessment report.

---

# Learning Outcomes

Through this project, I developed practical experience with:

- Host discovery and network reconnaissance
- TCP and UDP scanning
- Port-state interpretation
- Service and version detection
- Operating system fingerprinting
- Nmap scripting
- Scan optimization
- Firewall and filtering behavior
- Packet-response analysis
- Security-focused analysis of scan results
- Evidence collection
- Technical documentation
- Security findings analysis
- Risk prioritization
- Security assessment reporting

The project also strengthened my ability to transform technical information into structured, readable, and task-oriented documentation.

---

# Evidence-Based Approach

The project follows an evidence-based assessment approach.

Technical results were documented through:

- Nmap command outputs
- Screenshots
- Service and version results
- NSE results
- Scan comparisons
- Output files
- Practical scenario documentation
- Case studies
- Final security assessment report

Practical findings were added after the relevant assessment activities were performed in the authorized laboratory environment.

Scan results and screenshots were not fabricated to complete the documentation.

Service versions and scan results were not automatically treated as proof of exploitability. Where appropriate, findings are described as observed exposure, configuration weakness, or security risk.

---

# Intended Audience

This project is intended for:

- Cybersecurity students
- Network security learners
- SOC and security operations trainees
- Junior security practitioners
- Network administrators learning Nmap
- Technical readers learning network reconnaissance
- Technical writers documenting cybersecurity procedures
- Anyone studying Nmap within an authorized laboratory environment

---

# How to Use This Repository

The documentation can be explored progressively depending on the reader's goal.

### 1. Start with Concepts

Learn the networking and scanning concepts required to understand Nmap behavior.

[Go to Concepts](./Concepts/)

### 2. Review the Command Reference

Explore Nmap commands, options, syntax, use cases, and expected behavior.

[Go to Commands](./Commands/)

### 3. Study the Diagrams

Use the visual explanations to understand packet flows, scanning techniques, and related networking concepts.

[Go to Diagrams](./Diagrams/)

### 4. Work Through Practical Scenarios

Apply the documented techniques within an authorized laboratory environment.

[Go to Practical Scenarios](./Practical%20Scanning%20Scenarios/)

### 5. Review Evidence

Examine screenshots and supporting evidence from completed assessments.

[Go to Screenshots](./Screenshots/)

### 6. Read the Case Studies

Follow documented assessments from scope and methodology through analysis and security findings.

[Go to Case Studies](./Case-Studies/)

### 7. Review the Final Report

See how technical observations and findings were transformed into a structured security assessment report.

[Go to Final Report](./report/Final-Security-Assessment-Report.md)

### 8. Consult the References

Review the technical sources used to support the documentation.

[Go to References](./References/)

---

# Scope and Authorization

All scanning activities documented in this repository are intended for authorized laboratory environments.

Nmap can generate network traffic and may interact with systems in ways that trigger security controls or monitoring systems.

**Do not scan systems, devices, applications, or networks without explicit authorization from the owner or responsible administrator.**

The practical assessments in this repository were performed within a controlled virtualized laboratory environment using an intentionally vulnerable Metasploitable 2 target.

No unauthorized or destructive testing was performed.

---

# Assessment Limitations

The project has several limitations:

- Testing was performed against an intentionally vulnerable Metasploitable 2 system.
- The environment was virtualized and may not reproduce behavior found on production networks.
- No destructive testing was performed.
- No unauthorized exploitation was performed.
- Service version identification does not independently prove exploitability.
- Filtered scan results do not conclusively prove the presence of a firewall.
- NSE enumeration was targeted toward selected services rather than being an exhaustive assessment of every available NSE script.
- Risk ratings represent analyst-level assessment rather than formal CVSS scoring.
- Timing and performance observations were specific to the laboratory environment and should not be treated as universal benchmarks.

---

# Project Status

**Status: Completed**

The portfolio currently contains:

- Command reference
- Networking and Nmap concepts
- Eight technical diagrams
- Six practical scanning scenarios
- Practical screenshots for Scenarios 01–05
- Six security assessment case studies
- Technical references
- Final security assessment report
- Root project documentation

Scenario 06 serves as the capstone assessment and consolidates evidence from Scenarios 01–05 rather than representing a separate laboratory execution.

---

# Project Outcomes

This project demonstrates the progression from learning individual Nmap techniques to performing structured security analysis.

The completed workflow is:

```text
Discover
   ↓
Scan
   ↓
Enumerate
   ↓
Analyze
   ↓
Collect Evidence
   ↓
Identify Findings
   ↓
Prioritize Risk
   ↓
Recommend Remediation
   ↓
Report
```

The project therefore demonstrates not only the ability to execute Nmap commands, but also the ability to interpret technical evidence, document observations, develop security findings, prioritize risk, and communicate recommendations professionally.

---

# Disclaimer

This repository is intended for **educational purposes and authorized security assessments only.**

The techniques and commands documented here should only be used against systems and networks for which the tester has explicit permission to perform security testing.

The author is not responsible for unauthorized or inappropriate use of the techniques documented in this repository.

---

# About the Project

**Project:** Nmap Network Scanning Lab

**Focus:** Network Discovery, Reconnaissance, and Security Assessment

**Documentation Format:** Markdown

**Primary Tool:** Nmap

**Environment:** Authorized Virtualized Laboratory

**Target:** Metasploitable 2
