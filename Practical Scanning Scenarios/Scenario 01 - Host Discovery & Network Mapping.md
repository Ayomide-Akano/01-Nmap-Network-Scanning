# Practical Scanning Scenario 01 — Host Discovery & Network Mapping

## Objective

The objective of this assessment is to perform network discovery and reconnaissance against an intentionally vulnerable Metasploitable 2 system within an authorized laboratory environment.

The assessment demonstrates how Nmap can be used to:

- Identify active hosts on a network
- Identify exposed TCP services
- Enumerate service versions
- Identify the target operating system
- Perform basic NSE enumeration
- Collect security-relevant evidence
- Analyze exposed services
- Document security observations
- Produce a professional security assessment

> **Authorization Notice:** This assessment was performed against a deliberately vulnerable laboratory system owned and controlled for security testing and educational purposes.

---

# Lab Environment

## Network Configuration

The Metasploitable 2 virtual machine was configured using a **Bridged Adapter** so that it could communicate directly with the Linux scanning machine on the same local network.

| Component | Value |
|---|---|
| Network | `192.168.43.0/24` |
| Metasploitable 2 | `192.168.43.56` |
| Scanning Machine | `192.168.43.155` |
| Gateway / Network Device | `192.168.43.1` |
| Network Mode | Bridged |
| Target Platform | Metasploitable 2 |
| Scanner | Nmap 7.99 |

The scanning machine was identified during host discovery as:

```text
Saint (192.168.43.155)
```

The target system was identified as:

```text
192.168.43.56
```

---

# Assessment Scope

The assessment focused on the following target:

```text
192.168.43.56
```

The following activities were performed:

1. Host discovery
2. TCP port scanning
3. Service and version detection
4. Operating system detection
5. NSE-based enumeration
6. Security observation and analysis

No exploitation was performed as part of this scenario.

---

# Assessment Workflow

The assessment followed this workflow:

```text
Network Discovery
       ↓
Host Identification
       ↓
TCP Port Scanning
       ↓
Service Enumeration
       ↓
Operating System Detection
       ↓
NSE Enumeration
       ↓
Evidence Collection
       ↓
Security Analysis
       ↓
Case Study Documentation
```

---

# Step 1 — Network Discovery

## Objective

The first stage was to identify active hosts within the local laboratory network.

The network range assessed was:

```text
192.168.43.0/24
```

## Command

```bash
nmap -sn 192.168.43.0/24
```

## Observed Results

Nmap identified three active hosts:

| IP Address | Hostname | Status | MAC / Vendor |
|---|---|---|---|
| `192.168.43.1` | — | Up | Unknown |
| `192.168.43.56` | — | Up | Oracle VirtualBox virtual NIC |
| `192.168.43.155` | `Saint` | Up | Local scanning machine |

The target system was confirmed as:

```text
192.168.43.56
```

## Evidence

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-08 23:04 +0100

Nmap scan report for 192.168.43.1
Host is up (0.043s latency).
MAC Address: 16:11:14:81:F0:22 (Unknown)

Nmap scan report for 192.168.43.56
Host is up (0.00098s latency).
MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap scan report for Saint (192.168.43.155)
Host is up.

Nmap done: 256 IP addresses (3 hosts up) scanned in 3.19 seconds
```

## Analysis

The discovery scan confirmed that the Metasploitable 2 system was reachable from the Linux scanning machine.

The target's VirtualBox MAC address also provided additional confirmation that the host was the intended virtual laboratory machine.

---

# Step 2 — TCP Port Scanning

## Objective

After confirming the target was reachable, a TCP SYN scan was performed to identify exposed TCP services.

## Command

```bash
nmap -sS 192.168.43.56
```

## Results

Nmap identified **23 open TCP ports**.

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
| `1099/tcp` | Open | Java RMI Registry |
| `1524/tcp` | Open | Ingreslock / Bind Shell |
| `2049/tcp` | Open | NFS |
| `2121/tcp` | Open | FTP |
| `3306/tcp` | Open | MySQL |
| `5432/tcp` | Open | PostgreSQL |
| `5900/tcp` | Open | VNC |
| `6000/tcp` | Open | X11 |
| `6667/tcp` | Open | IRC |
| `8009/tcp` | Open | AJP13 |
| `8180/tcp` | Open | HTTP / Unknown |

Nmap reported:

```text
Not shown: 977 closed tcp ports (reset)
```

## Analysis

The target exposes a large number of network services compared with a typical hardened server.

The exposed services span several categories:

- Remote administration
- File transfer
- Web services
- Database services
- Network file sharing
- Remote desktop
- RPC services
- Mail services
- IRC
- Java application services

This significantly increases the system's attack surface.

Several services, including Telnet, FTP, VNC, SMB, NFS, database services, and legacy remote-login services, require additional security review.

---

# Step 3 — Service and Version Enumeration

## Objective

Service detection was performed to identify the applications and versions running on the discovered ports.

## Command

```bash
nmap -sV 192.168.43.56
```

## Results

| Port | Service | Detected Version |
|---:|---|---|
| `21/tcp` | FTP | vsftpd 2.3.4 |
| `22/tcp` | SSH | OpenSSH 4.7p1 Debian 8ubuntu1 |
| `23/tcp` | Telnet | Linux telnetd |
| `25/tcp` | SMTP | Postfix smtpd |
| `53/tcp` | DNS | ISC BIND 9.4.2 |
| `80/tcp` | HTTP | Apache httpd 2.2.8 |
| `111/tcp` | RPCBind | 2 |
| `139/tcp` | NetBIOS | Samba 3.X - 4.X |
| `445/tcp` | SMB | Samba 3.X - 4.X |
| `512/tcp` | exec | Unidentified |
| `513/tcp` | login | OpenBSD or Solaris rlogind |
| `514/tcp` | shell | Unidentified |
| `1099/tcp` | Java RMI | GNU Classpath grmiregistry |
| `1524/tcp` | Bind Shell | Metasploitable root shell |
| `2049/tcp` | NFS | Versions 2–4 |
| `2121/tcp` | FTP | ProFTPD 1.3.1 |
| `3306/tcp` | MySQL | 5.0.51a-3ubuntu5 |
| `5432/tcp` | PostgreSQL | 8.3.0–8.3.7 |
| `5900/tcp` | VNC | Protocol 3.3 |
| `6000/tcp` | X11 | Access denied |
| `6667/tcp` | IRC | UnrealIRCd |
| `8009/tcp` | AJP13 | Apache Jserv Protocol v1.3 |
| `8180/tcp` | HTTP | Apache Tomcat 5.5 |

## Service Information

Nmap identified the target as running Linux/Unix-based services.

The scan reported:

```text
Service Info:
Hosts: metasploitable.localdomain, irc.Metasploitable.LAN
OSs: Unix, Linux
CPE: cpe:/o:linux:linux_kernel
```

## Analysis

The service enumeration stage revealed that the target contains numerous legacy applications and protocols.

Examples include:

- `vsftpd 2.3.4`
- `OpenSSH 4.7p1`
- `Apache 2.2.8`
- `BIND 9.4.2`
- `Samba 3.0.x`
- `MySQL 5.0.51a`
- `PostgreSQL 8.3.x`
- `Apache Tomcat 5.5`
- `UnrealIRCd`

The age and number of these services indicate a significantly outdated system and a broad attack surface.

However, service-version detection alone does not prove that a particular vulnerability is exploitable. Further validation would be required before making an exploitability claim.

---

# Step 4 — Operating System Detection

## Objective

Nmap OS detection was used to estimate the operating system and kernel family of the target.

## Command

```bash
nmap -O 192.168.43.56
```

## Results

Nmap reported:

```text
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop
```

## Analysis

The target was identified as a general-purpose Linux system running a kernel in the Linux 2.6.x family.

The operating-system result is an Nmap fingerprinting estimate rather than absolute proof of the exact kernel version.

The result is consistent with the expected operating system of the Metasploitable 2 laboratory system.

---

# Step 5 — NSE Enumeration

## Objective

Nmap's default NSE scripts were used to gather additional information about selected services and network protocols.

## Command

```bash
nmap -sC 192.168.43.56
```

## Key Findings

### 5.1 Anonymous FTP Access

Nmap identified anonymous FTP access:

```text
21/tcp open ftp

|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
```

The FTP service also reported:

```text
Control connection is plain text
Data connections will be plain text
vsFTPd 2.3.4
```

### Security Observation

Anonymous FTP access can expose files or information to unauthenticated users depending on the server configuration.

The use of plaintext FTP also means that credentials and data transmitted through the protocol are not inherently protected by encryption.

### Risk Consideration

**Risk Level: Medium**

The actual impact depends on what files are accessible through anonymous FTP and how the service is configured.

---

# 5.2 SSH Host Keys

The SSH service exposed the following host keys:

```text
1024 DSA
2048 RSA
```

Nmap identified:

```text
OpenSSH 4.7p1 Debian 8ubuntu1
```

### Security Observation

The detected OpenSSH version is significantly outdated.

Older SSH implementations may contain known weaknesses or lack modern security improvements.

However, the version information alone does not establish that the service is currently exploitable.

---

# 5.3 SMTP Enumeration

The SMTP service exposed several supported commands:

```text
PIPELINING
SIZE 10240000
VRFY
ETRN
STARTTLS
ENHANCEDSTATUSCODES
8BITMIME
DSN
```

Nmap also detected SSLv2 support:

```text
SSLv2 supported
```

The scan identified several SSLv2 cipher suites.

### Security Observation

SSLv2 is an obsolete and insecure protocol.

Its presence indicates that the service supports legacy cryptographic protocols that should not normally be enabled on a modern production system.

### Risk Level

**Risk Level: High**

The exact risk depends on whether the service is reachable from untrusted networks and whether stronger protocols are also enforced.

---

# 5.4 DNS Enumeration

Nmap identified:

```text
ISC BIND 9.4.2
```

The DNS NSE output also returned:

```text
bind.version: 9.4.2
```

### Security Observation

The detected BIND version is very old.

Legacy DNS software should be reviewed for unsupported versions, known vulnerabilities, unnecessary exposure, and insecure configuration.

---

# 5.5 RPC and NFS Enumeration

The RPC information exposed several services:

```text
rpcbind
nfs
mountd
nlockmgr
status
```

NFS was available on:

```text
2049/tcp
2049/udp
```

### Security Observation

NFS exposure can create significant security risk if exports are improperly configured.

An exposed NFS service should be reviewed to determine:

- Which directories are exported
- Which hosts are permitted
- Whether write access is allowed
- Whether authentication controls are appropriate
- Whether unnecessary exports are enabled

The current scan confirms NFS exposure but does not by itself establish the permissions of individual exports.

---

# 5.6 MySQL Enumeration

Nmap identified:

```text
3306/tcp open mysql
MySQL 5.0.51a-3ubuntu5
```

The MySQL NSE script returned protocol and capability information.

### Security Observation

A database service exposed directly to the network increases the attack surface.

The security of the service depends on authentication, network restrictions, user privileges, encryption, and database configuration.

The scan confirms exposure but does not establish whether unauthorized database access is possible.

---

# 5.7 PostgreSQL Enumeration

Nmap identified:

```text
5432/tcp open postgresql
PostgreSQL DB 8.3.0 - 8.3.7
```

Nmap also identified an SSL certificate associated with the service.

### Security Observation

The detected PostgreSQL version is from an outdated software generation.

Database services should generally be restricted to trusted application hosts or administrative networks rather than unnecessarily exposed to broad network segments.

---

# 5.8 VNC Enumeration

Nmap identified:

```text
5900/tcp open vnc
VNC (protocol 3.3)
```

The NSE scan reported:

```text
VNC Authentication (2)
```

### Security Observation

VNC provides remote graphical access and should therefore be strongly restricted.

The scan confirms that authentication is present, but it does not establish the strength of the configured credentials.

---

# 5.9 SMB Enumeration

Nmap identified Samba services on:

```text
139/tcp
445/tcp
```

The SMB enumeration returned:

```text
OS: Unix (Samba 3.0.20-Debian)
Computer name: metasploitable
Domain name: localdomain
FQDN: metasploitable.localdomain
```

The SMB security mode reported:

```text
account_used: guest
authentication_level: user
challenge_response: supported
message_signing: disabled
```

Nmap explicitly reported:

```text
message_signing: disabled (dangerous, but default)
```

### Security Observation

SMB message signing being disabled means SMB traffic is not protected by mandatory message-signing controls.

This can increase the risk of certain man-in-the-middle and SMB relay scenarios depending on the surrounding network configuration.

### Risk Level

**Risk Level: High**

The actual exploitability and impact depend on network architecture, authentication configuration, and other security controls.

---

# 5.10 IRC Enumeration

Nmap identified:

```text
6667/tcp open irc
```

The NSE script identified:

```text
Unreal3.2.8.1
irc.Metasploitable.LAN
```

### Security Observation

The IRC service is exposed and is running an old software version.

Unnecessary legacy services should be disabled where they are not required.

---

# 5.11 Apache Tomcat Enumeration

Nmap identified:

```text
8180/tcp open http
Apache Tomcat/5.5
```

The service was identified as:

```text
Apache Tomcat/Coyote JSP engine 1.1
```

The AJP service was also exposed:

```text
8009/tcp open ajp13
```

### Security Observation

The presence of both HTTP application services and AJP increases the application-layer attack surface.

AJP should only be exposed where required and should be appropriately restricted.

---

# Step 6 — Security Findings

Based on the evidence collected during the assessment, the following security observations were identified.

| ID | Finding | Evidence | Risk |
|---|---|---|---|
| F-01 | Large number of exposed services | 23 open TCP ports | High |
| F-02 | Anonymous FTP access | `ftp-anon: Anonymous FTP login allowed` | Medium |
| F-03 | Plaintext FTP | FTP control/data connections reported as plain text | Medium |
| F-04 | Legacy SSH implementation | OpenSSH 4.7p1 | Medium |
| F-05 | SSLv2 supported | SMTP NSE enumeration | High |
| F-06 | Legacy DNS implementation | BIND 9.4.2 | Medium |
| F-07 | NFS exposed | Port 2049 and RPC enumeration | High |
| F-08 | SMB message signing disabled | NSE reported signing disabled | High |
| F-09 | Legacy database services exposed | MySQL 5.0.51a and PostgreSQL 8.3.x | High |
| F-10 | VNC exposed | Port 5900 | Medium |
| F-11 | Legacy IRC service exposed | UnrealIRCd 3.2.8.1 | Medium |
| F-12 | Legacy Tomcat/AJP services exposed | Ports 8180 and 8009 | High |
| F-13 | Legacy remote-access services exposed | Telnet, rlogin, rexec/rsh-related ports | High |

> **Important:** Risk ratings in this document represent an initial assessment based on observed exposure and configuration. They are not a substitute for a complete vulnerability assessment or exploitation validation.

---

# Step 7 — Attack Surface Analysis

The assessment identified a broad attack surface.

## Remote Access Services

The target exposes:

```text
22/tcp   SSH
23/tcp   Telnet
512/tcp  exec
513/tcp  login
514/tcp  shell
5900/tcp VNC
```

These services provide multiple potential remote-access paths.

Legacy plaintext or weakly protected remote-access protocols should generally be replaced or restricted.

---

## File Transfer Services

The system exposes:

```text
21/tcp   FTP
2121/tcp FTP
```

Anonymous FTP access was explicitly confirmed on port 21.

This should be investigated to determine what resources are available to unauthenticated users.

---

## Web Services

The target exposes:

```text
80/tcp
8180/tcp
8009/tcp
```

Detected technologies include:

```text
Apache httpd 2.2.8
Apache Tomcat 5.5
Apache Jserv / AJP
```

These services should be reviewed for:

- Unnecessary exposure
- Outdated software
- Weak application configuration
- Administrative interfaces
- Insecure connectors
- Missing access controls

---

## Database Services

The following database services were exposed:

```text
3306/tcp   MySQL
5432/tcp   PostgreSQL
```

Database services should normally be restricted to systems that require direct access.

---

## Network File Sharing

The target exposes:

```text
139/tcp
445/tcp
2049/tcp
```

These correspond to SMB/NetBIOS and NFS functionality.

Network file-sharing services require careful access-control configuration because excessive exposure can reveal sensitive information or provide additional attack paths.

---

# Step 8 — Evidence Collection

The following evidence should be preserved in the repository.

## Recommended Evidence Files

```text
Screenshots/
├── 01-host-discovery.png
├── 02-port-scan.png
├── 03-service-version-detection.png
├── 04-os-detection.png
└── 05-nse-enumeration.png
```

If terminal screenshots are captured, they should clearly show:

- The Nmap command
- Target IP address
- Relevant output
- Date/time where visible

Screenshots should not contain unrelated personal information.

---

# Step 9 — Assessment Summary

The assessment successfully identified the Metasploitable 2 system at:

```text
192.168.43.56
```

The target was confirmed to be reachable from the scanning machine on the same `192.168.43.0/24` network.

Nmap identified:

```text
23 open TCP ports
```

Service enumeration identified numerous legacy services and software versions.

The NSE assessment additionally confirmed several important configuration observations, including:

- Anonymous FTP access
- Plaintext FTP communication
- SSLv2 support
- NFS exposure
- SMB message signing disabled
- Multiple legacy services
- Exposed database services
- Exposed remote-access services
- Exposed web and application services

The system therefore presents a significantly larger attack surface than would normally be expected from a hardened production server.

---

# Step 10 — Recommendations

Based on the observations from this assessment, the following security improvements are recommended.

## 1. Reduce the Attack Surface

Disable services that are not required.

Particular attention should be given to:

```text
Telnet
FTP
rlogin
rexec
rsh
IRC
VNC
Unnecessary RPC services
```

---

## 2. Restrict Network Exposure

Use firewall rules and network segmentation to ensure that services are accessible only from authorized systems.

Database services such as:

```text
MySQL
PostgreSQL
```

should not be unnecessarily exposed to untrusted networks.

---

## 3. Replace Insecure Protocols

Where possible:

- Replace Telnet with SSH
- Replace plaintext FTP with SFTP or FTPS
- Disable obsolete cryptographic protocols
- Remove unnecessary legacy remote-login services

---

## 4. Disable SSLv2

SSLv2 should be disabled and replaced with modern TLS configurations.

The SMTP service should be reviewed to ensure that only secure cryptographic protocols and appropriately strong cipher suites are permitted.

---

## 5. Review Anonymous FTP

Anonymous FTP access should be disabled unless there is a specific business requirement for it.

If anonymous access is required, the accessible directory structure and permissions should be tightly restricted.

---

## 6. Secure SMB

SMB configuration should be reviewed and message signing should be enabled where appropriate.

SMB access should also be restricted to trusted hosts and network segments.

---

## 7. Review NFS Configuration

NFS exports should be reviewed to determine:

- Exported directories
- Allowed clients
- Read/write permissions
- Authentication requirements
- Unnecessary exports

---

## 8. Upgrade Legacy Software

The assessment identified multiple legacy software versions.

These should be replaced with supported versions where possible.

Software requiring review includes:

```text
vsftpd 2.3.4
OpenSSH 4.7p1
BIND 9.4.2
Apache 2.2.8
Samba 3.0.x
MySQL 5.0.51a
PostgreSQL 8.3.x
UnrealIRCd 3.2.8.1
Apache Tomcat 5.5
```

---

## 9. Restrict Remote Administration

Remote-access services should be limited to authorized administrative networks.

Strong authentication should be enforced, and unnecessary remote-access protocols should be disabled.

---

# Step 11 — Lessons Learned

This assessment demonstrated several important principles of network security assessment.

### 1. Host discovery establishes the assessment scope

Before scanning services, it is important to identify which systems are actually reachable.

### 2. Open ports represent attack surface

An open port does not automatically mean a vulnerability exists, but every exposed service represents another component that must be secured and maintained.

### 3. Version detection provides useful context

Service versions help security professionals identify outdated technologies and determine which areas require further investigation.

### 4. NSE provides deeper visibility

NSE scripts can reveal configuration information that is not immediately visible from a basic port scan.

### 5. Scan results require interpretation

Nmap reports technical observations.

The security professional must translate those observations into:

```text
Evidence
   ↓
Security Meaning
   ↓
Risk
   ↓
Recommendation
```

### 6. Evidence should support every finding

Security findings should be based on observable evidence rather than assumptions.

---

# Assessment Limitations

This assessment was limited to network reconnaissance and enumeration.

The following activities were **not** performed:

- Exploitation
- Credential attacks
- Password cracking
- Privilege escalation
- Persistence
- Data extraction
- Denial-of-service testing

Therefore, the assessment does not claim that every identified service is exploitable.

The findings represent observations made during network scanning and service enumeration.

---

# Final Assessment Conclusion

The Nmap assessment successfully mapped the Metasploitable 2 laboratory target and identified a broad range of exposed services.

The target exposed **23 TCP services**, including remote-access, file-transfer, web, database, network-file-sharing, and application services.

Service enumeration revealed numerous legacy technologies, while NSE enumeration identified additional security-relevant configurations such as anonymous FTP access, SSLv2 support, exposed NFS functionality, and disabled SMB message signing.

The assessment demonstrates how a structured Nmap workflow can progress from basic host discovery to service enumeration and security analysis.

The results will be used as the technical evidence for the project's first security assessment case study.

---

# Related Documentation

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

## Screenshots

Assessment screenshots should be stored in:

```text
../Screenshots/
```

## Case Study

The findings from this practical scenario will be used to create:

```text
../Case-Studies/01-Metasploitable2-Security-Assessment.md
```

---

# Assessment Status

**Status:** Completed

**Target:** `192.168.43.56`

**Network:** `192.168.43.0/24`

**Assessment Type:** Network Discovery & Security Enumeration

**Nmap Version:** `7.99`

**Open TCP Ports:** `23`

**Exploitation Performed:** No

**Evidence Collected:** Yes

**Case Study:** Next stage
```
