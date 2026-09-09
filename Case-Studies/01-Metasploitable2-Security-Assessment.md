# Case Study 01 — Metasploitable 2 Security Assessment

## Executive Summary

This case study documents a network security assessment performed against an intentionally vulnerable **Metasploitable 2** virtual machine within an authorized laboratory environment.

The assessment was conducted from a Linux-based scanning machine connected to the same local network as the target.

The target was identified at:

```text
192.168.43.56
```

The assessed network was:

```text
192.168.43.0/24
```

Nmap identified the target as an active Linux system and discovered **23 open TCP ports** exposing a wide range of services, including FTP, SSH, Telnet, SMTP, DNS, HTTP, SMB, NFS, MySQL, PostgreSQL, VNC, IRC, and Apache Tomcat/AJP.

Further service enumeration revealed numerous legacy software versions and several security-relevant configurations.

Notable observations included:

- Anonymous FTP access was enabled.
- FTP communications were reported as plaintext.
- SSLv2 was supported by the SMTP service.
- SMB message signing was disabled.
- NFS was exposed.
- Multiple database services were directly accessible.
- Legacy remote-access protocols were exposed.
- Multiple outdated web and application services were exposed.
- A Metasploitable root shell service was identified on TCP port `1524`.

The assessment demonstrates how network reconnaissance can reveal a broad attack surface before any exploitation is attempted.

No exploitation, credential attacks, persistence, denial-of-service testing, or data extraction were performed.

---

# 1. Assessment Overview

## 1.1 Assessment Objective

The primary objective was to perform network reconnaissance and security enumeration against the Metasploitable 2 laboratory system.

The assessment was designed to demonstrate the following security assessment activities:

- Host discovery
- Network mapping
- TCP port scanning
- Service enumeration
- Version detection
- Operating system identification
- NSE-based enumeration
- Security finding identification
- Risk analysis
- Security recommendations
- Technical documentation

---

## 1.2 Assessment Type

**Assessment Type:** Network Security Assessment

**Primary Tool:** Nmap 7.99

**Target:** Metasploitable 2

**Environment:** Authorized security laboratory

**Testing Approach:** Non-exploitative reconnaissance and enumeration

---

# 2. Scope

## 2.1 Target

The assessment was performed against:

```text
192.168.43.56
```

## 2.2 Network

The target was located on:

```text
192.168.43.0/24
```

## 2.3 Out-of-Scope Activities

The following activities were not performed:

- Exploitation
- Password cracking
- Credential attacks
- Privilege escalation
- Persistence
- Denial-of-service testing
- Data destruction
- Unauthorized access to external systems

The assessment remained within the controlled laboratory environment.

---

# 3. Lab Environment

| Component | Details |
|---|---|
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Network | `192.168.43.0/24` |
| Scanner | Linux workstation |
| Scanner IP | `192.168.43.155` |
| Network Mode | Bridged |
| Scanning Tool | Nmap 7.99 |
| Assessment Date | September 8, 2026 |

The use of a bridged network adapter allowed the scanning machine and Metasploitable 2 to communicate directly on the same local network.

---

# 4. Methodology

The assessment followed a structured reconnaissance methodology.

```text
Host Discovery
      ↓
Port Scanning
      ↓
Service Enumeration
      ↓
OS Detection
      ↓
NSE Enumeration
      ↓
Evidence Collection
      ↓
Security Analysis
      ↓
Risk Assessment
      ↓
Recommendations
```

## 4.1 Host Discovery

The first stage identified active hosts within the laboratory network.

Command:

```bash
nmap -sn 192.168.43.0/24
```

The scan identified three active hosts:

| IP Address | Identification |
|---|---|
| `192.168.43.1` | Network device / gateway |
| `192.168.43.56` | Metasploitable 2 target |
| `192.168.43.155` | Linux scanning machine |

---

# 5. Network Discovery Results

The target was successfully identified as an active host:

```text
Nmap scan report for 192.168.43.56
Host is up (0.00098s latency).
MAC Address: 08:00:27:DB:2A:1B
Oracle VirtualBox virtual NIC
```

The VirtualBox MAC address provided additional confirmation that the discovered system was the intended virtual machine.

---

# 6. Port Scanning

## 6.1 TCP SYN Scan

The following command was used:

```bash
nmap -sS 192.168.43.56
```

The scan identified **23 open TCP ports**.

| Port | State | Service |
|---:|---|---|
| `21/tcp` | Open | FTP |
| `22/tcp` | Open | SSH |
| `23/tcp` | Open | Telnet |
| `25/tcp` | Open | SMTP |
| `53/tcp` | Open | DNS |
| `80/tcp` | Open | HTTP |
| `111/tcp` | Open | RPCBind |
| `139/tcp` | Open | NetBIOS |
| `445/tcp` | Open | Microsoft-DS / SMB |
| `512/tcp` | Open | exec |
| `513/tcp` | Open | login |
| `514/tcp` | Open | shell |
| `1099/tcp` | Open | Java RMI |
| `1524/tcp` | Open | Bind Shell |
| `2049/tcp` | Open | NFS |
| `2121/tcp` | Open | FTP |
| `3306/tcp` | Open | MySQL |
| `5432/tcp` | Open | PostgreSQL |
| `5900/tcp` | Open | VNC |
| `6000/tcp` | Open | X11 |
| `6667/tcp` | Open | IRC |
| `8009/tcp` | Open | AJP13 |
| `8180/tcp` | Open | HTTP / Tomcat |

Nmap reported:

```text
Not shown: 977 closed tcp ports (reset)
```

---

# 7. Service and Version Enumeration

## 7.1 Service Detection

The following command was used:

```bash
nmap -sV 192.168.43.56
```

The assessment identified the following services and versions:

| Port | Service | Version |
|---:|---|---|
| `21` | FTP | vsftpd 2.3.4 |
| `22` | SSH | OpenSSH 4.7p1 Debian 8ubuntu1 |
| `23` | Telnet | Linux telnetd |
| `25` | SMTP | Postfix smtpd |
| `53` | DNS | ISC BIND 9.4.2 |
| `80` | HTTP | Apache httpd 2.2.8 |
| `111` | RPCBind | Version 2 |
| `139` | NetBIOS | Samba 3.X–4.X |
| `445` | SMB | Samba 3.X–4.X |
| `512` | exec | Unidentified |
| `513` | login | rlogind |
| `514` | shell | Unidentified |
| `1099` | Java RMI | GNU Classpath grmiregistry |
| `1524` | Bind Shell | Metasploitable root shell |
| `2049` | NFS | Versions 2–4 |
| `2121` | FTP | ProFTPD 1.3.1 |
| `3306` | MySQL | 5.0.51a-3ubuntu5 |
| `5432` | PostgreSQL | 8.3.0–8.3.7 |
| `5900` | VNC | Protocol 3.3 |
| `6000` | X11 | Access denied |
| `6667` | IRC | UnrealIRCd |
| `8009` | AJP13 | Apache Jserv Protocol v1.3 |
| `8180` | HTTP | Apache Tomcat 5.5 |

---

# 8. Operating System Identification

The following command was used:

```bash
nmap -O 192.168.43.56
```

Nmap reported:

```text
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop
```

## Analysis

The target was identified as a general-purpose Linux system using a Linux 2.6.x kernel fingerprint.

The result is an Nmap OS detection estimate and should not be treated as absolute proof of the exact kernel version.

---

# 9. NSE Enumeration

The following command was used:

```bash
nmap -sC 192.168.43.56
```

NSE enumeration provided additional information about several exposed services.

---

# 10. Security Findings

## Finding F-01 — Excessive Network Attack Surface

### Description

The target exposed **23 TCP services** across multiple protocols and application categories.

### Evidence

The TCP SYN scan identified 23 open ports, including:

```text
21, 22, 23, 25, 53, 80, 111, 139, 445,
512, 513, 514, 1099, 1524, 2049, 2121,
3306, 5432, 5900, 6000, 6667, 8009, 8180
```

### Security Impact

Every exposed service represents an additional component requiring security configuration, monitoring, patching, and access control.

A large exposed service footprint increases the number of potential entry points available to an attacker.

### Risk

**High**

### Recommendation

Disable unnecessary services and restrict required services using host-based and network-level firewall controls.

---

# Finding F-02 — Anonymous FTP Access

### Description

Anonymous FTP access was confirmed on TCP port `21`.

### Evidence

NSE reported:

```text
ftp-anon: Anonymous FTP login allowed (FTP code 230)
```

### Security Impact

Anonymous FTP can allow unauthenticated users to access files or directories exposed by the FTP configuration.

The actual impact depends on the permissions and contents of the accessible directories.

### Risk

**Medium**

### Recommendation

Disable anonymous FTP unless it is explicitly required.

If anonymous access is required, restrict accessible directories and permissions and ensure that sensitive information cannot be accessed.

---

# Finding F-03 — Plaintext FTP Communication

### Description

The FTP service was identified as transmitting control and data connections in plaintext.

### Evidence

NSE reported:

```text
Control connection is plain text
Data connections will be plain text
```

### Security Impact

Plaintext FTP does not inherently protect credentials or transferred information from network interception.

### Risk

**Medium**

### Recommendation

Replace FTP with SFTP or appropriately configured FTPS where secure file transfer is required.

---

# Finding F-04 — Legacy Remote Access Services

### Description

Multiple legacy remote-access services were exposed.

### Evidence

The assessment identified:

```text
23/tcp   Telnet
512/tcp  exec
513/tcp  login
514/tcp  shell
5900/tcp VNC
```

### Security Impact

Legacy remote-access services can introduce additional authentication and confidentiality risks.

Telnet and related legacy protocols may transmit information without modern encryption protections.

### Risk

**High**

### Recommendation

Disable unnecessary remote-access services.

Use modern secure administration mechanisms such as SSH and restrict administrative services to trusted networks.

---

# Finding F-05 — SSLv2 Supported

### Description

The SMTP service supported the obsolete SSLv2 protocol.

### Evidence

NSE reported:

```text
SSLv2 supported
```

The scan also identified several SSLv2 cipher suites.

### Security Impact

SSLv2 is an obsolete cryptographic protocol and should not be enabled on modern systems.

Supporting obsolete cryptographic protocols can expose systems to known protocol-level weaknesses.

### Risk

**High**

### Recommendation

Disable SSLv2 and other obsolete cryptographic protocols.

Configure the service to use current secure TLS versions and strong cipher suites.

---

# Finding F-06 — SMB Message Signing Disabled

### Description

SMB message signing was identified as disabled.

### Evidence

NSE reported:

```text
smb-security-mode:
account_used: guest
authentication_level: user
challenge_response: supported
message_signing: disabled
```

Nmap further reported:

```text
message_signing: disabled (dangerous, but default)
```

### Security Impact

When SMB signing is not enforced, certain network attack scenarios involving SMB traffic may become more feasible depending on authentication and network configuration.

### Risk

**High**

### Recommendation

Enable SMB message signing where appropriate and restrict SMB access to trusted systems and network segments.

---

# Finding F-07 — NFS Service Exposed

### Description

Network File System functionality was exposed through RPC and NFS services.

### Evidence

The assessment identified:

```text
111/tcp   rpcbind
2049/tcp  NFS
```

RPC enumeration also identified:

```text
mountd
nlockmgr
status
```

### Security Impact

Improperly configured NFS exports can expose filesystems or sensitive information to unauthorized systems.

### Risk

**High**

### Recommendation

Review NFS exports and restrict access to authorized hosts.

Ensure that unnecessary exports are removed and that write permissions are appropriately controlled.

---

# Finding F-08 — Legacy Database Services Exposed

### Description

Database services were directly accessible over the network.

### Evidence

The following services were detected:

```text
3306/tcp  MySQL 5.0.51a-3ubuntu5
5432/tcp  PostgreSQL 8.3.0-8.3.7
```

### Security Impact

Directly exposed database services increase the attack surface and may provide attackers with additional opportunities for unauthorized access if authentication or access controls are weak.

### Risk

**High**

### Recommendation

Restrict database services to authorized application hosts and administrative networks.

Upgrade outdated database software and enforce strong authentication and least-privilege access.

---

# Finding F-09 — Legacy Web and Application Services

### Description

The target exposed multiple legacy web and Java application services.

### Evidence

The assessment identified:

```text
80/tcp    Apache httpd 2.2.8
8009/tcp  Apache Jserv / AJP
8180/tcp  Apache Tomcat 5.5
```

### Security Impact

Legacy web and application platforms may contain known vulnerabilities or insecure configurations.

The scan alone does not prove exploitability, but the identified versions warrant further security review.

### Risk

**High**

### Recommendation

Upgrade unsupported application software and restrict administrative or backend connectors such as AJP to trusted systems.

---

# Finding F-10 — Legacy IRC Service

### Description

An IRC service was exposed on TCP port `6667`.

### Evidence

Nmap identified:

```text
UnrealIRCd 3.2.8.1
```

The service identified itself as:

```text
irc.Metasploitable.LAN
```

### Security Impact

Legacy services increase attack surface and may contain outdated software or insecure configurations.

### Risk

**Medium**

### Recommendation

Disable the service if it is not required.

If IRC functionality is required, use a supported implementation and restrict access appropriately.

---

# Finding F-11 — Metasploitable Root Shell Service

### Description

TCP port `1524` was identified as a bind shell associated with the Metasploitable environment.

### Evidence

Nmap identified:

```text
1524/tcp open bindshell Metasploitable root shell
```

### Security Impact

A network-accessible root shell represents a critical security exposure in a real production environment.

In this assessment, the service is part of the intentionally vulnerable Metasploitable 2 laboratory environment.

### Risk

**Critical**

### Recommendation

In a real production environment, unauthorized network-accessible root shells should be removed immediately.

Host and network firewall rules should prevent unnecessary direct access to privileged shell services.

---

# 11. Risk Summary

| Finding | Risk |
|---|---|
| F-01 — Excessive Network Attack Surface | High |
| F-02 — Anonymous FTP Access | Medium |
| F-03 — Plaintext FTP | Medium |
| F-04 — Legacy Remote Access Services | High |
| F-05 — SSLv2 Supported | High |
| F-06 — SMB Signing Disabled | High |
| F-07 — NFS Exposed | High |
| F-08 — Legacy Database Services | High |
| F-09 — Legacy Web/Application Services | High |
| F-10 — Legacy IRC Service | Medium |
| F-11 — Root Shell Service | Critical |

---

# 12. Attack Surface Overview

The assessment revealed several major categories of exposed functionality.

## Remote Administration

```text
SSH
Telnet
exec
login
shell
VNC
```

## File Transfer

```text
FTP
FTP
```

## Web Services

```text
Apache HTTP
Apache Tomcat
AJP
```

## Database Services

```text
MySQL
PostgreSQL
```

## Network File Sharing

```text
SMB
NFS
```

## Infrastructure Services

```text
DNS
RPCBind
SMTP
```

## Other Services

```text
IRC
Java RMI
X11
```

This breadth of exposed functionality demonstrates why attack-surface reduction is an important component of network security.

---

# 13. Security Analysis

The most significant characteristic of the target is not one isolated service but the **combination of numerous exposed legacy services**.

A production server would normally be expected to expose only the services necessary to perform its intended business function.

In contrast, this system exposes services across:

- Remote administration
- File transfer
- Databases
- Web applications
- Network file sharing
- Mail
- DNS
- Remote graphical access
- Legacy Unix services
- IRC
- Java application infrastructure

This creates a broad attack surface.

The assessment also demonstrated that basic port scanning alone would not have revealed the full security picture.

For example:

```text
Basic Port Scan
      ↓
21/tcp open
      ↓
Service Detection
      ↓
vsftpd 2.3.4
      ↓
NSE Enumeration
      ↓
Anonymous FTP Enabled
```

The same progression applies to SMB, SMTP, NFS, databases, and other services.

This demonstrates the value of combining:

```text
Discovery
+
Enumeration
+
Contextual Analysis
```

rather than relying on a single Nmap scan.

---

# 14. Recommendations

## Priority 1 — Reduce Exposed Services

Identify services that are not required and disable them.

Network filtering should restrict access to services that must remain available.

---

## Priority 2 — Remove Legacy Protocols

Where possible:

- Disable Telnet
- Disable legacy remote-login services
- Replace plaintext FTP
- Disable SSLv2
- Restrict legacy application protocols

---

## Priority 3 — Upgrade Legacy Software

Review and upgrade outdated services including:

```text
vsftpd
OpenSSH
BIND
Apache
Samba
MySQL
PostgreSQL
UnrealIRCd
Tomcat
```

Software should be maintained on supported versions with current security updates.

---

## Priority 4 — Secure SMB

Enable SMB message signing where appropriate.

Restrict SMB access to trusted hosts and networks.

Review guest access and authentication configuration.

---

## Priority 5 — Secure NFS

Review all NFS exports.

Restrict access to authorized systems and remove unnecessary exports.

---

## Priority 6 — Protect Databases

Restrict MySQL and PostgreSQL network access.

Database services should be accessible only from systems that legitimately require them.

---

## Priority 7 — Secure Web/Application Services

Review Apache, Tomcat, and AJP configurations.

Remove unnecessary application connectors and restrict administrative interfaces.

---

## Priority 8 — Monitor the Network

Organizations should combine:

- Firewall logging
- IDS/IPS
- Centralized logging
- SIEM monitoring
- Network segmentation
- Endpoint monitoring

to identify suspicious reconnaissance and unauthorized access attempts.

---

# 15. Evidence

The assessment generated several forms of evidence.

## Host Discovery Evidence

```text
nmap -sn 192.168.43.0/24
```

Result:

```text
3 hosts up
```

---

## Port Scan Evidence

```text
nmap -sS 192.168.43.56
```

Result:

```text
23 open TCP ports
```

---

## Service Enumeration Evidence

```text
nmap -sV 192.168.43.56
```

Result:

```text
Multiple legacy services and versions identified
```

---

## OS Detection Evidence

```text
nmap -O 192.168.43.56
```

Result:

```text
Linux 2.6.X
```

---

## NSE Evidence

```text
nmap -sC 192.168.43.56
```

Important observations included:

```text
Anonymous FTP allowed
SSLv2 supported
SMB signing disabled
NFS/RPC services exposed
MySQL information
VNC information
IRC information
Tomcat information
```

---

# 16. Evidence Storage

Screenshots associated with the assessment should be stored in:

```text
Screenshots/
```

Recommended files:

```text
Screenshots/
├── 01-host-discovery.png
├── 02-port-scan.png
├── 03-service-version-detection.png
├── 04-os-detection.png
└── 05-nse-enumeration.png
```

Raw scan output can also be preserved where appropriate.

Recommended evidence organization:

```text
Screenshots/
    └── Assessment screenshots

report/
    └── Final security assessment report

Case-Studies/
    └── Case study analysis
```

---

# 17. Assessment Limitations

This assessment was intentionally limited to network reconnaissance and enumeration.

The following were not performed:

- Exploitation
- Credential attacks
- Password cracking
- Privilege escalation
- Persistence
- Data exfiltration
- Denial-of-service testing

Consequently, the assessment cannot establish that every identified service or software version is exploitable.

Further vulnerability validation would be required before making definitive exploitability claims.

---

# 18. Lessons Learned

## Network Discovery

Host discovery provides the foundation for identifying systems within an assessment scope.

## Port Scanning

Open ports provide visibility into the target's exposed attack surface.

## Service Enumeration

Service-version detection provides additional context that can identify legacy technologies requiring further review.

## NSE

NSE scripts can reveal configuration information that is not visible from a basic port scan.

## Security Analysis

Technical scan results must be translated into meaningful security findings.

## Evidence-Based Reporting

Security findings should be supported by observable evidence rather than assumptions.

---

# 19. Conclusion

The assessment successfully identified and analyzed the Metasploitable 2 system at:

```text
192.168.43.56
```

The target exposed **23 TCP services**, representing a broad attack surface across remote administration, file transfer, web applications, databases, network file sharing, infrastructure services, and legacy applications.

Further enumeration identified multiple security-relevant conditions, including anonymous FTP access, plaintext FTP communication, SSLv2 support, disabled SMB message signing, exposed NFS, legacy database services, and a network-accessible root shell service associated with the intentionally vulnerable Metasploitable environment.

The assessment demonstrates the importance of progressing beyond basic port discovery.

A structured security assessment should move from:

```text
Host Discovery
      ↓
Port Discovery
      ↓
Service Identification
      ↓
Configuration Enumeration
      ↓
Security Analysis
      ↓
Risk Assessment
      ↓
Recommendations
```

The findings documented in this case study provide the foundation for the project's final security assessment report.

---

# 20. Related Documentation

## Practical Scanning Scenario

See:

```text
../Practical Scanning Scenarios/01-Host-Discovery-and-Network-Mapping.md
```

## Nmap Command Reference

See:

```text
../Commands/Nmap-Command-Reference.md
```

## Nmap Concepts

See:

```text
../Concepts/
```

## Nmap Diagrams

See:

```text
../Diagrams/
```

## Assessment Evidence

See:

```text
../Screenshots/
```

## Final Security Assessment Report

The findings from this case study will be used to create the project's formal security assessment report in:

```text
../report/
```

---

# Assessment Metadata

| Field | Value |
|---|---|
| Assessment | Metasploitable 2 Security Assessment |
| Target | `192.168.43.56` |
| Network | `192.168.43.0/24` |
| Scanner | Linux |
| Scanner IP | `192.168.43.155` |
| Tool | Nmap 7.99 |
| Open TCP Ports | 23 |
| Assessment Type | Network Security Assessment |
| Exploitation | Not performed |
| Status | Completed |
```

Paste that **entire block** into the new GitHub file and save it.

Once you've done that, **don't create the final `report/` yet**. First tell me you've pasted it, and we'll do a quick professional review of the case study structure before turning it into the formal assessment report.
