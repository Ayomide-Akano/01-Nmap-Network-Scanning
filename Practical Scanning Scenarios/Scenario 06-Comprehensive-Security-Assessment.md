# Scenario 06 — Comprehensive Security Assessment

## Overview

This scenario is the capstone of the Nmap Network Scanning project.

Rather than focusing on a single Nmap feature, this assessment combines the techniques demonstrated throughout the previous practical scenarios into one structured security assessment.

The objective is to approach the target as a security analyst would:

> **Assess the machine, interpret the evidence, identify security risks, prioritize the findings, and recommend appropriate defensive actions.**

The earlier scenarios provide the individual skills and evidence used to support this assessment.

---

# Assessment Workflow

The assessment follows the complete security assessment lifecycle demonstrated throughout this project:

```text
Host Discovery
       ↓
TCP/UDP Scanning
       ↓
Service Enumeration
       ↓
OS Detection
       ↓
NSE Enumeration
       ↓
Filtering/Evasion Analysis
       ↓
Evidence Collection
       ↓
Security Findings
       ↓
Risk Prioritization
       ↓
Recommendations
       ↓
Final Security Assessment
```

This scenario therefore moves from **technical scanning** to **security analysis and reporting**.

---

# 1. Assessment Overview

| Item | Details |
|---|---|
| Assessment Type | Network Security Assessment |
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Scanner | Linux |
| Scanner IP | `192.168.43.155` |
| Environment | Virtualized Laboratory |
| Network | `192.168.43.0/24` |
| Tool | Nmap |
| Nmap Version | 7.99 |
| Authorization | Authorized laboratory assessment |
| Assessment Scope | Network exposure, services, protocols, configuration indicators and security risks |

---

# 2. Assessment Objective

The objective of this assessment is to perform a comprehensive security assessment of the target system using Nmap.

The assessment aims to:

- Identify the target host and its network position.
- Identify exposed TCP and UDP services.
- Determine service versions where possible.
- Identify operating system characteristics.
- Perform targeted NSE enumeration.
- Analyze filtering and packet-handling behavior.
- Preserve technical evidence.
- Identify security weaknesses.
- Prioritize findings according to their potential security impact.
- Provide practical remediation recommendations.
- Produce a final security assessment based on the collected evidence.

---

# 3. Assessment Methodology

The assessment was performed using a phased approach.

## Phase 1 — Host Discovery

The first phase identified active systems on the local network.

The target was identified as:

```text
192.168.43.56
```

The target was confirmed to be reachable from the scanning system.

Host discovery established the target's presence before more detailed scanning was performed.

---

# 4. Phase 2 — TCP Port Scanning

TCP scanning was used to identify exposed TCP services.

The assessment included:

- Default TCP SYN scanning
- Ports `1-1000`
- Full TCP port scanning
- TCP Connect scanning
- FIN scanning
- NULL scanning
- Xmas scanning

The default SYN scan identified a broad attack surface.

### Initial TCP Exposure

The target exposed multiple services including:

| Port | Service |
|---:|---|
| 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 111 | RPCBind |
| 139 | NetBIOS/SMB |
| 445 | SMB |
| 512 | exec |
| 513 | login |
| 514 | shell |
| 1099 | Java RMI |
| 1524 | bindshell |
| 2049 | NFS |
| 2121 | FTP |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 5900 | VNC |
| 6000 | X11 |
| 6667 | IRC |
| 8009 | AJP |
| 8180 | HTTP/Tomcat |

A full TCP scan identified additional exposed ports beyond the first 1000 ports.

Additional services included:

```text
3632
6697
8787
37541
41959
54536
57742
```

This demonstrated an important assessment principle:

> **Scanning only a limited port range can leave part of the attack surface undiscovered.**

---

# 5. Phase 3 — UDP Port Scanning

UDP scanning was performed separately because TCP scanning does not provide visibility into UDP services.

The assessment included:

- UDP top-port scanning
- UDP top 100 scanning
- Targeted UDP scanning
- Combined TCP/UDP scanning
- UDP service/version detection

Important UDP observations included:

| Port | State | Observation |
|---:|---|---|
| 53 | Open | DNS |
| 111 | Open | RPCBind |
| 137 | Open | NetBIOS Name Service |
| 68 | Open/Filtered | DHCP-related service |
| 69 | Open/Filtered | TFTP-related service |
| 138 | Open/Filtered | NetBIOS Datagram |
| 161 | Closed | SNMP |
| 500 | Closed | ISAKMP |
| 520 | Closed | Routing Information Protocol |
| 49152 | Closed | High UDP port |

The UDP assessment demonstrated that important services can remain hidden if an assessment only examines TCP.

---

# 6. Phase 4 — Service and Version Enumeration

Service enumeration was used to determine what applications and protocols were operating on the exposed ports.

Important observations included:

| Port | Service | Version / Information |
|---:|---|---|
| 21 | FTP | vsftpd 2.3.4 |
| 22 | SSH | OpenSSH 4.7p1 |
| 23 | Telnet | Linux telnetd |
| 25 | SMTP | Postfix smtpd |
| 53 | DNS | ISC BIND 9.4.2 |
| 80 | HTTP | Apache 2.2.8 |
| 139/445 | SMB | Samba 3.X–4.X |
| 3306 | MySQL | 5.0.51a |
| 5432 | PostgreSQL | 8.3.x |
| 5900 | VNC | Protocol 3.3 |
| 6667 | IRC | UnrealIRCd |
| 8009 | AJP | Apache Jserv Protocol 1.3 |
| 8180 | HTTP | Apache Tomcat 5.5 |

The discovered services indicate that the system exposes a large number of network-accessible applications.

Several services also appear to use legacy software versions.

### Security Significance

Version information is useful for security assessment because it allows an analyst to:

- Identify potentially outdated software.
- Determine whether legacy protocols are present.
- Research known security concerns.
- Prioritize services for further investigation.
- Identify systems requiring patching or replacement.

However:

> **A detected version alone does not prove that a specific vulnerability is exploitable.**

Further validation would be required before making a definitive exploitability claim.

---

# 7. Phase 5 — Operating System Detection

Nmap OS detection identified the target as a Linux-based system.

Observed information included:

```text
OS: Linux
Kernel Range: Linux 2.6.x
OS CPE: cpe:/o:linux:linux_kernel:2.6
Network Distance: 1 hop
```

The operating system detection results were consistent with the expected Metasploitable 2 laboratory environment.

An additional UDP service detection result produced a Windows-related service inference for NetBIOS.

This result was treated cautiously because service-level OS inference can differ from broader OS fingerprinting.

### Assessment Interpretation

The broader OS fingerprint was considered more useful for identifying the underlying platform, while individual service responses were treated as service-specific evidence rather than definitive operating system identification.

---

# 8. Phase 6 — NSE Service Enumeration

Nmap Scripting Engine enumeration was performed using a **service-driven approach**.

The objective was not to run every available NSE script.

Instead, scripts were selected according to services that were actually discovered.

The assessment followed this workflow:

```text
Service discovered
       ↓
Relevant NSE scripts selected
       ↓
Additional information gathered
       ↓
Security significance interpreted
       ↓
Evidence documented
```

The primary services investigated were:

- FTP
- SMB
- HTTP
- SMTP
- MySQL

---

# 9. FTP Enumeration

The FTP service on port `21` was identified as:

```text
vsftpd 2.3.4
```

NSE enumeration confirmed:

```text
Anonymous FTP login allowed
```

The FTP system also exposed information indicating plaintext control and data communication.

### Security Significance

Anonymous FTP access can provide unauthorized users with access to files or directories depending on the server configuration.

Plaintext FTP also exposes authentication and data to network interception when encryption is not used.

### Assessment

The combination of:

- Legacy FTP service
- Anonymous access
- Plaintext communication

represents an unnecessary security exposure.

---

# 10. SMB Enumeration

SMB services were identified on:

```text
139/tcp
445/tcp
```

NSE enumeration identified:

```text
Samba 3.0.20-Debian
```

The assessment identified:

- SMBv1 support
- Guest authentication
- Disabled SMB message signing
- Anonymous access to selected shares
- Read/write access to `IPC$` and `tmp`

Observed shares included:

```text
ADMIN$
IPC$
opt
print$
tmp
```

### Security Significance

The presence of SMBv1 represents a significant legacy protocol exposure.

Disabled SMB message signing reduces protection against certain types of traffic manipulation and impersonation attacks.

Anonymous read/write access to network shares can also increase the risk of unauthorized file access or modification.

### Assessment

SMB represented one of the more significant attack surfaces identified during the assessment.

---

# 11. HTTP Enumeration

The web service on port `80` was identified as:

```text
Apache/2.2.8 (Ubuntu) DAV/2
PHP/5.2.4-2ubuntu5.10
```

The HTTP title was:

```text
Metasploitable2 - Linux
```

NSE enumeration identified several accessible paths, including:

```text
/tikiwiki/
/test/
/phpinfo.php
/phpMyAdmin/
/doc/
/icons/
/index/
```

### Security Significance

The exposed web application surface provides additional opportunities for information gathering and application-level attacks.

The presence of `phpinfo.php` is particularly useful to an attacker because diagnostic pages can disclose environment and configuration information.

The presence of administrative or application directories also increases the amount of functionality exposed to unauthenticated users.

### Assessment

The web service should be reviewed for:

- Unnecessary exposed applications
- Information disclosure
- Administrative interfaces
- Legacy software
- Unnecessary directories
- Weak authentication controls

---

# 12. SMTP Enumeration

The SMTP service exposed several capabilities:

```text
PIPELINING
SIZE
VRFY
ETRN
STARTTLS
ENHANCEDSTATUSCODES
8BITMIME
DSN
```

NSE enumeration also identified legacy TLS-related characteristics during broader/default NSE validation, including SSLv2 support and an expired certificate.

The SMTP user enumeration attempt did not produce a usable user list.

### Security Significance

The presence of legacy protocol support and an expired certificate indicates that the service requires additional security hardening.

The failed user enumeration attempt was also recorded as evidence.

A failed enumeration attempt does not mean that the service is secure; it only means that the selected technique did not successfully retrieve the requested information.

---

# 13. MySQL Enumeration

The MySQL service was identified on:

```text
3306/tcp
```

Observed version:

```text
MySQL 5.0.51a-3ubuntu5
```

NSE enumeration provided protocol and server information.

However:

```text
mysql-users
```

did not return user information.

Similarly:

```text
mysql-databases
```

did not return database names.

### Security Significance

The database service is directly exposed to the network.

Database services should generally not be unnecessarily accessible from untrusted network segments.

The detected legacy version also increases the importance of patch and lifecycle management.

---

# 14. Phase 7 — Filtering and Evasion Analysis

The assessment also examined how the target responded to different packet types and scanning techniques.

The following techniques were tested:

- TCP ACK scanning
- FIN scanning
- NULL scanning
- Xmas scanning
- Fragmented packets
- Custom MTU
- Decoy scanning
- Source-port manipulation
- Invalid TCP checksums
- MAC address spoofing

---

## ACK Scan

The ACK scan produced:

```text
1000 unfiltered TCP ports
```

This indicated that the tested ports were not being filtered in a way that prevented the ACK probes from receiving responses.

However:

> **An ACK scan does not identify open ports.**

It primarily provides information about filtering behavior.

---

## FIN / NULL / Xmas Scans

FIN, NULL and Xmas scans produced results in the form:

```text
open|filtered
```

for ports that were identified as open by the SYN scan.

This demonstrated that different TCP probing techniques can produce different interpretations of the same target.

---

## Fragmentation

Fragmented SYN scanning successfully identified the selected open ports.

The result was consistent with the baseline SYN scan.

---

## Custom MTU

The custom MTU scan also successfully identified the selected open ports.

The results remained consistent with the baseline.

---

## Decoy Scanning

The decoy scan completed successfully against the selected ports.

The technique demonstrated that Nmap can generate additional apparent scanning sources during a scan.

This was treated as a demonstration of Nmap's packet-generation capabilities rather than evidence of a security control bypass.

---

## Source Port Manipulation

Using:

```text
--source-port 53
```

the selected ports were successfully identified as open.

This demonstrates why security monitoring and filtering should not rely solely on assumptions about source-port values.

---

## Invalid Checksums

The scan using:

```text
--badsum
```

returned the tested ports as filtered.

This result demonstrated that malformed packets can receive different treatment from normal probes.

---

## MAC Address Spoofing

MAC address spoofing produced filtered results for the selected ports.

Nmap confirmed that a spoofed MAC address was used.

The observed behavior was treated as an environmental result rather than proof of a specific firewall technology.

---

# 15. Phase 8 — Scan Optimization and Evidence Collection

The assessment also considered the operational impact of scan configuration.

The following techniques were evaluated:

- Timing templates
- Scan delay
- Retry limits
- Host timeout
- Packet rate
- Verbose output
- Reason reporting
- Normal output
- XML output
- Grepable output

Observed results demonstrated that scan configuration can significantly influence assessment duration.

For example:

| Technique | Observed Result |
|---|---|
| Baseline | 1.08 seconds |
| `-T3` | 1.14 seconds |
| `-T4` | 1.02 seconds |
| `--scan-delay 100ms` | 1.46 seconds |
| Reduced retries/timeout | 0.92 seconds |
| `--max-rate 100` | 10.87 seconds |
| `--max-rate 500` | 2.83 seconds |

These timings are specific to the laboratory environment.

They should not be treated as universal performance benchmarks.

---

# 16. Evidence Preservation

Multiple output formats were generated during the assessment.

### Normal Output

```text
-oN
```

Observed file size:

```text
662 bytes
```

### XML Output

```text
-oX
```

Observed file size:

```text
3.3K
```

### Grepable Output

```text
-oG
```

Observed file size:

```text
605 bytes
```

These outputs demonstrate the importance of preserving scan evidence in formats suitable for:

- Human review
- Documentation
- Parsing
- Reporting
- Future comparison

---

# 17. Consolidated Attack Surface

The assessment identified a broad network attack surface.

Major exposed service categories included:

### Remote Access

```text
SSH
Telnet
rlogin
rsh
VNC
```

### File Sharing

```text
FTP
SMB
NFS
```

### Databases

```text
MySQL
PostgreSQL
```

### Web/Application Services

```text
Apache HTTP
Tomcat
AJP
phpMyAdmin
TikiWiki
```

### Infrastructure Services

```text
DNS
RPCBind
NetBIOS
SMTP
```

### Other Services

```text
IRC
Java RMI
X11
```

This represents a substantial number of network-accessible services for a single host.

---

# 18. Security Findings

The following findings were consolidated from the evidence collected throughout the assessment.

| ID | Finding | Severity |
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
| F-13 | Metasploitable Root Shell Service | Critical |
| F-14 | Legacy IRC Service | Medium |

> **Severity ratings in this assessment represent analyst-level prioritization based on the observed exposure and configuration. They are not formal CVSS scores.**

---

# 19. Finding F-01 — Excessive Network Attack Surface

**Severity:** High

## Evidence

The assessment identified numerous exposed TCP and UDP services across multiple categories.

The full TCP scan identified additional services beyond the first 1000 ports.

## Security Impact

A large attack surface provides attackers with more potential entry points.

Every unnecessary exposed service increases:

- Attack opportunities
- Configuration complexity
- Patch management requirements
- Monitoring requirements
- Potential vulnerability exposure

## Recommendation

- Disable unnecessary services.
- Restrict services using host and network firewalls.
- Remove unused applications.
- Restrict administrative services to trusted management networks.
- Regularly review exposed ports.

---

# 20. Finding F-02 — Anonymous FTP Access

**Severity:** Medium

## Evidence

NSE confirmed:

```text
Anonymous FTP login allowed
```

## Security Impact

Anonymous access may allow unauthorized users to access files or directories.

Depending on permissions, this could lead to:

- Information disclosure
- Unauthorized file retrieval
- File modification
- Abuse of the service

## Recommendation

- Disable anonymous FTP unless explicitly required.
- Restrict FTP access to authorized users.
- Replace FTP with secure alternatives such as SFTP where appropriate.
- Review file and directory permissions.

---

# 21. Finding F-03 — Plaintext FTP Communication

**Severity:** Medium

## Evidence

FTP enumeration indicated plaintext control and data communication.

## Security Impact

Credentials and transmitted information may be exposed to network interception.

## Recommendation

- Replace FTP with SFTP or FTPS.
- Disable unencrypted authentication.
- Restrict access to trusted networks.

---

# 22. Finding F-04 — Legacy Remote Access Services

**Severity:** High

## Evidence

The assessment identified services including:

```text
Telnet
rlogin
rsh
VNC
```

## Security Impact

Legacy remote access protocols may provide weak or unencrypted authentication and communication.

They also increase the system's attack surface.

## Recommendation

- Disable Telnet.
- Disable rlogin/rsh.
- Use SSH for secure remote administration.
- Restrict VNC to trusted management networks.
- Require strong authentication.

---

# 23. Finding F-05 — SMBv1 Supported

**Severity:** High

## Evidence

NSE identified:

```text
NT LM 0.12 (SMBv1)
```

## Security Impact

SMBv1 is an obsolete protocol and should not normally be required on modern systems.

Legacy protocol support increases exposure to known weaknesses and compatibility-driven security risks.

## Recommendation

- Disable SMBv1.
- Require modern SMB versions.
- Restrict SMB exposure to trusted networks.
- Monitor SMB authentication and access.

---

# 24. Finding F-06 — SMB Message Signing Disabled

**Severity:** High

## Evidence

NSE reported:

```text
Message signing disabled
```

## Security Impact

Disabled SMB signing reduces protection against certain network-based impersonation and traffic manipulation attacks.

## Recommendation

- Enable SMB signing where appropriate.
- Apply modern SMB security policies.
- Restrict SMB traffic between trusted systems.

---

# 25. Finding F-07 — Anonymous SMB Read/Write Access

**Severity:** High

## Evidence

NSE identified anonymous access to:

```text
IPC$
tmp
```

with read/write capability reported for the relevant shares.

## Security Impact

Anonymous write access can allow unauthorized modification of files or other resources.

## Recommendation

- Disable guest access where unnecessary.
- Remove anonymous write permissions.
- Apply least-privilege permissions.
- Audit share and filesystem permissions.
- Restrict SMB access using network controls.

---

# 26. Finding F-08 — NFS Service Exposed

**Severity:** High

## Evidence

NFS was identified on:

```text
2049/tcp
```

RPC/NFS enumeration also identified the service during the assessment.

## Security Impact

Exposed NFS services can create significant risk when exports are improperly configured.

Potential concerns include:

- Unauthorized file access
- Excessive network exposure
- Weak export restrictions
- Misconfigured permissions

## Recommendation

- Restrict NFS to trusted systems.
- Review `/etc/exports`.
- Use least-privilege export permissions.
- Disable unused NFS services.

---

# 27. Finding F-09 — Legacy Database Services Exposed

**Severity:** High

## Evidence

The assessment identified:

```text
MySQL 5.0.51a
PostgreSQL 8.3.x
```

Both database services were network-accessible.

## Security Impact

Directly exposed database services increase the potential attack surface.

Legacy database versions may also lack modern security fixes and capabilities.

## Recommendation

- Upgrade unsupported database versions.
- Restrict database access to trusted application hosts.
- Disable remote database access where unnecessary.
- Use strong authentication.
- Apply network segmentation.

---

# 28. Finding F-10 — Legacy Web/Application Services

**Severity:** High

## Evidence

The assessment identified:

```text
Apache 2.2.8
PHP 5.2.4
Tomcat 5.5
AJP
```

The HTTP service also exposed several application paths.

## Security Impact

Legacy web and application software may contain known security weaknesses and may no longer receive security updates.

## Recommendation

- Upgrade unsupported software.
- Remove unnecessary applications.
- Disable unnecessary application connectors.
- Restrict administrative interfaces.
- Perform application-specific security testing.

---

# 29. Finding F-11 — Web Information Disclosure

**Severity:** Medium

## Evidence

The assessment identified:

```text
/phpinfo.php
```

along with multiple discoverable application and directory paths.

## Security Impact

Information disclosure can provide attackers with useful information about:

- Server configuration
- Software versions
- Application structure
- Technology stack

## Recommendation

- Remove diagnostic pages from production systems.
- Restrict administrative paths.
- Disable unnecessary directory exposure.
- Review HTTP response headers.

---

# 30. Finding F-12 — SMTP Legacy Security Exposure

**Severity:** Medium

## Evidence

SMTP enumeration identified several capabilities.

Broader/default NSE validation also identified:

```text
SSLv2 supported
Expired certificate
```

## Security Impact

Legacy protocol support and expired certificates weaken the overall security posture of the mail service.

## Recommendation

- Disable obsolete SSL/TLS protocols.
- Use current TLS configurations.
- Replace expired certificates.
- Review SMTP authentication settings.
- Restrict unnecessary SMTP functionality.

---

# 31. Finding F-13 — Metasploitable Root Shell Service

**Severity:** Critical

## Evidence

Port `1524` was identified as:

```text
bindshell
```

The service was associated with a root shell in the Metasploitable 2 environment.

## Security Impact

An exposed root shell represents a direct and severe compromise path.

If such a service existed on a real production system, an attacker able to reach it could potentially obtain privileged system access.

## Recommendation

- Immediately disable the service.
- Remove unauthorized shell listeners.
- Investigate how the service was enabled.
- Review system integrity.
- Rotate potentially compromised credentials.
- Perform incident-response procedures if this were a real environment.

---

# 32. Finding F-14 — Legacy IRC Service

**Severity:** Medium

## Evidence

IRC was identified on:

```text
6667/tcp
```

The service was identified as:

```text
UnrealIRCd
```

## Security Impact

Unnecessary legacy services increase attack surface and introduce additional patching and monitoring requirements.

## Recommendation

- Disable the service if unnecessary.
- Upgrade to a supported version if required.
- Restrict network access.
- Monitor for unauthorized use.

---

# 33. Risk Prioritization

The findings can be grouped into three broad priorities.

## Priority 1 — Immediate Attention

These findings represent the highest concern:

```text
F-13 — Metasploitable Root Shell Service
F-05 — SMBv1 Supported
F-06 — SMB Message Signing Disabled
F-07 — Anonymous SMB Read/Write Access
F-09 — Legacy Database Services Exposed
F-10 — Legacy Web/Application Services
F-08 — NFS Service Exposed
```

These issues should be addressed first because they represent significant opportunities for unauthorized access, information exposure or system compromise.

---

## Priority 2 — High Security Hardening

```text
F-01 — Excessive Network Attack Surface
F-04 — Legacy Remote Access Services
```

The objective should be to reduce the number of accessible services and eliminate insecure remote administration protocols.

---

## Priority 3 — Additional Hardening

```text
F-02 — Anonymous FTP Access
F-03 — Plaintext FTP Communication
F-11 — Web Information Disclosure
F-12 — SMTP Legacy Security Exposure
F-14 — Legacy IRC Service
```

These issues should also be remediated, particularly where the affected services are not required.

---

# 34. Recommended Remediation Strategy

A practical remediation sequence would be:

```text
1. Remove unauthorized/critical services
        ↓
2. Disable unnecessary network services
        ↓
3. Remove legacy protocols
        ↓
4. Restrict exposed management interfaces
        ↓
5. Upgrade unsupported software
        ↓
6. Harden authentication and permissions
        ↓
7. Restrict database/file-sharing services
        ↓
8. Remove information-disclosure pages
        ↓
9. Improve encryption and certificate management
        ↓
10. Rescan and validate remediation
```

---

# 35. Security Hardening Recommendations

## Network Exposure

- Reduce the number of publicly or broadly accessible ports.
- Apply host-based firewall rules.
- Segment management services.
- Restrict database and file-sharing services.

## Authentication

- Disable anonymous access.
- Disable guest access where unnecessary.
- Use strong authentication.
- Remove unnecessary remote login services.

## Encryption

- Replace plaintext protocols.
- Disable obsolete SSL/TLS protocols.
- Use current encryption standards.
- Maintain valid certificates.

## Software Management

- Upgrade unsupported operating systems.
- Upgrade legacy applications.
- Maintain regular security patching.
- Remove unnecessary software.

## File Sharing

- Disable SMBv1.
- Enable SMB signing where appropriate.
- Restrict NFS exports.
- Remove anonymous write permissions.

## Web Services

- Remove unnecessary applications.
- Remove diagnostic pages.
- Restrict administrative interfaces.
- Review exposed HTTP methods and directories.

---

# 36. Validation After Remediation

A security assessment should not end with recommendations.

After remediation, the target should be rescanned to verify that the identified exposures have been reduced.

Validation should include:

### Host Discovery

Confirm that only intended systems remain reachable.

### TCP Scanning

Verify that unnecessary TCP ports are closed or filtered.

### UDP Scanning

Verify that unnecessary UDP services are no longer exposed.

### Service Enumeration

Confirm that software versions and exposed services have changed as expected.

### NSE Validation

Re-run relevant NSE scripts against the remediated services.

### Configuration Verification

Confirm that:

- SMBv1 is disabled.
- Anonymous access is removed.
- Unnecessary services are stopped.
- Database exposure is restricted.
- Legacy protocols are disabled.

---

# 37. Evidence Matrix

| Evidence | Assessment Significance |
|---|---|
| Host discovery | Confirmed target availability |
| TCP SYN scan | Identified primary TCP attack surface |
| Full TCP scan | Identified additional services beyond port 1000 |
| UDP scanning | Identified UDP services not visible through TCP |
| Service detection | Identified applications and versions |
| OS detection | Identified Linux-based target characteristics |
| FTP NSE | Confirmed anonymous access and FTP characteristics |
| SMB NSE | Identified SMBv1, guest access and signing configuration |
| HTTP NSE | Identified web technologies and accessible paths |
| SMTP NSE | Identified mail capabilities and legacy security indicators |
| MySQL NSE | Identified database service information |
| ACK scan | Provided filtering behavior information |
| FIN/NULL/Xmas | Demonstrated alternative TCP response behavior |
| Fragmentation/MTU | Demonstrated packet manipulation techniques |
| Decoy scanning | Demonstrated alternative scan-source behavior |
| Source-port scan | Demonstrated source-port manipulation |
| Bad checksum scan | Demonstrated malformed-packet behavior |
| Output files | Preserved technical assessment evidence |

---

# 38. Assessment Limitations

This assessment was conducted in an intentionally vulnerable laboratory environment.

Therefore:

- The target was Metasploitable 2.
- The network was virtualized.
- Results may differ from production environments.
- Timing measurements should not be treated as universal benchmarks.
- Filtered results do not independently prove the presence of a specific firewall.
- Service version detection does not automatically prove exploitability.
- NSE results depend on the scripts selected.
- The assessment did not attempt to exploit the identified services.
- No credential attacks were performed.
- No destructive testing was performed.
- No exhaustive vulnerability assessment was performed.

The purpose of this exercise was to demonstrate network reconnaissance, evidence collection, security interpretation and assessment methodology using Nmap.

---

# 39. Analyst Lessons Learned

This assessment demonstrates several important principles of network security assessment.

## 1. Discovery Comes Before Analysis

An analyst cannot properly assess what they have not discovered.

Host discovery establishes the initial scope.

---

## 2. TCP Alone Is Not Enough

UDP services can remain invisible when an assessment focuses only on TCP.

A comprehensive assessment should consider both protocols.

---

## 3. Port Numbers Are Only the Beginning

Finding an open port does not tell the complete security story.

Service enumeration provides additional context about:

- Applications
- Versions
- Protocols
- Configurations
- Potential weaknesses

---

## 4. NSE Should Be Targeted

NSE becomes more useful when scripts are selected according to the actual attack surface.

The assessment demonstrated:

```text
Service discovered
        ↓
Relevant scripts selected
        ↓
Information gathered
        ↓
Security significance interpreted
```

This is more useful than indiscriminately running every available script.

---

## 5. Scanning Results Require Interpretation

Nmap provides technical observations.

The security analyst must convert those observations into meaningful findings.

For example:

```text
Port 445 open
       ↓
Samba detected
       ↓
SMBv1 supported
       ↓
Message signing disabled
       ↓
Guest access identified
       ↓
Anonymous write access identified
       ↓
High-risk security finding
```

The value of the assessment is therefore not just the scan output.

It is the **interpretation of the evidence**.

---

# 40. Final Security Assessment

The target system demonstrated a **high-risk security posture** within the laboratory environment.

The primary contributing factors were:

- Excessive exposed services
- Legacy remote access protocols
- Anonymous FTP access
- Plaintext FTP communication
- SMBv1 support
- Disabled SMB message signing
- Anonymous SMB read/write access
- Exposed NFS
- Legacy database services
- Legacy web/application services
- Information disclosure
- Legacy SMTP security characteristics
- Exposed IRC
- A root shell service

The most serious observation was the presence of the root shell service on port `1524`, which represents a critical security exposure in a real-world environment.

The assessment also demonstrated that a system can appear different depending on the scanning method used. TCP, UDP, NSE, filtering analysis and service enumeration each provided different pieces of information.

Therefore, a reliable security assessment should not depend on a single scan.

Instead, the analyst should combine multiple evidence sources to build a complete understanding of the target.

---

# 41. Overall Security Assessment Statement

> **The assessed host presents a broad and high-risk network attack surface characterized by numerous exposed services, legacy protocols, weak access controls, outdated applications and insecure service configurations. Immediate remediation should prioritize the removal of the exposed root shell, reduction of unnecessary network services, elimination of legacy protocols, restriction of file-sharing and database services, and strengthening of authentication and access controls.**

---

# 42. Capstone Conclusion

Scenario 06 brings together the practical skills developed throughout the Nmap project.

The progression was:

```text
Scenario 01
Host Discovery & Network Mapping
        ↓
Scenario 02
TCP & UDP Port Scanning
        ↓
Scenario 03
Firewall Evasion & Filtering Analysis
        ↓
Scenario 04
Scan Optimization, Timing & Output Formats
        ↓
Scenario 05
NSE Service Enumeration
        ↓
Scenario 06
Comprehensive Security Assessment
```

The earlier scenarios demonstrated **how to use Nmap**.

This final scenario demonstrates **how to think like a security analyst using Nmap**.

The assessment process moved beyond simply identifying open ports and instead focused on:

```text
Discovery
   ↓
Enumeration
   ↓
Evidence
   ↓
Interpretation
   ↓
Risk
   ↓
Prioritization
   ↓
Remediation
   ↓
Validation
```

This represents the central objective of the project:

> **Use technical evidence to understand a system's attack surface and communicate meaningful security risks and recommendations.**

---

# 43. Scope Limitation

NSE enumeration in Scenario 05 was intentionally limited to selected services relevant to the discovered attack surface.

Additional NSE categories and service-specific scripts can be performed as an extension exercise or assessment task.

Similarly, Scenario 06 focuses on Nmap-based network assessment and does not represent a complete penetration test or full vulnerability assessment.

---

# 44. Portfolio Evidence

This capstone assessment is supported by the evidence and practical work completed throughout the project.

### Supporting Scenarios

- [Scenario 01 — Host Discovery and Network Mapping](../Practical%20Scanning%20Scenarios/01-Host-Discovery-and-Network-Mapping.md)
- [Scenario 02 — TCP and UDP Port Scanning](../Practical%20Scanning%20Scenarios/02-TCP-and-UDP-Port-Scanning.md)
- [Scenario 03 — Firewall Evasion and Filtering Analysis](../Practical%20Scanning%20Scenarios/03-Firewall-Evasion-and-Filtering-Analysis.md)
- [Scenario 04 — Scan Optimization, Timing and Output Formats](../Practical%20Scanning%20Scenarios/04-Scan-Optimization-Timing-and-Output-Formats.md)
- [Scenario 05 — NSE Service Enumeration](../Practical%20Scanning%20Scenarios/05-NSE-Service-Enumeration.md)

### Supporting Case Studies

- [Case Study 01 — Metasploitable 2 Security Assessment](../Case-Studies/01-Metasploitable2-Security-Assessment.md)
- [Case Study 02 — TCP and UDP Port Scanning Assessment](../Case-Studies/02-TCP-and-UDP-Port-Scanning-Assessment.md)
- [Case Study 03 — Firewall Evasion and Filtering Analysis](../Case-Studies/03-Firewall-Evasion-and-Filtering-Analysis.md)
- [Case Study 04 — Scan Optimization, Timing and Output Formats](../Case-Studies/04-Scan-Optimization-and-Timing-and-Output-Formats.md)
- [Case Study 05 — NSE Service Enumeration Assessment](../Case-Studies/05-NSE-Service-Enumeration-Assessment.md)

---

# 45. Final Portfolio Outcome

With the completion of this capstone, the practical portion of the Nmap project demonstrates the ability to:

- Discover hosts.
- Identify TCP and UDP services.
- Perform targeted and full port scans.
- Enumerate services and versions.
- Identify operating system characteristics.
- Perform targeted NSE enumeration.
- Analyze filtering behavior.
- Understand scan optimization.
- Preserve technical evidence.
- Interpret security implications.
- Develop security findings.
- Prioritize risks.
- Recommend remediation.
- Validate security improvements.
- Produce a structured security assessment.

The project therefore progresses from **Nmap command execution** to **evidence-driven security assessment**.

---

## Final Assessment Status

```text
┌──────────────────────────────────────────────┐
│       NMAP SECURITY ASSESSMENT PROJECT       │
├──────────────────────────────────────────────┤
│ Host Discovery              ✓                │
│ TCP Scanning                ✓                │
│ UDP Scanning                ✓                │
│ Service Enumeration         ✓                │
│ OS Detection                ✓                │
│ NSE Enumeration             ✓                │
│ Filtering Analysis         ✓                │
│ Scan Optimization          ✓                │
│ Evidence Collection        ✓                │
│ Security Findings          ✓                │
│ Risk Prioritization        ✓                │
│ Recommendations             ✓                │
│ Comprehensive Assessment   ✓                │
└──────────────────────────────────────────────┘
```
