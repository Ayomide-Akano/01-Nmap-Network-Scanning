# Case Study 02 — TCP and UDP Port Scanning Assessment

## Executive Summary

This case study documents an authorized network reconnaissance assessment performed against a Metasploitable 2 virtual machine within a controlled laboratory environment.

The assessment focused on TCP and UDP port scanning using multiple Nmap scanning techniques to determine how different scan methods affect port visibility and state classification.

The assessment identified a broad network attack surface on the target.

A full TCP scan identified **31 open TCP ports**, while a TCP scan limited to ports 1–1000 identified only **12 open ports**. This demonstrated that restricting reconnaissance to common or lower-numbered ports can leave services operating on higher-numbered ports undiscovered.

UDP scanning identified additional services that were not visible through TCP scanning, including DNS, RPCBind, NetBIOS and NFS.

Alternative TCP scanning techniques also produced different results. TCP SYN and TCP Connect scans identified exposed ports as `open`, while FIN, NULL and Xmas scans classified the same ports as `open|filtered`.

The assessment did not include exploitation. The purpose was to understand reconnaissance behavior, compare scan techniques, identify exposed services, and evaluate the resulting attack surface.

---

# 1. Assessment Overview

| Item | Details |
|---|---|
| Assessment Name | TCP and UDP Port Scanning Assessment |
| Assessment Type | Network Reconnaissance / Security Assessment |
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Network | `192.168.43.0/24` |
| Scanning Machine | Linux |
| Scanning Machine IP | `192.168.43.155` |
| Nmap Version | `7.99` |
| Environment | Authorized VirtualBox Laboratory |
| Exploitation | Not performed |
| Assessment Status | Completed |

---

# 2. Assessment Scope

The assessment focused on identifying TCP and UDP services exposed by the target through different Nmap scanning techniques.

The following techniques were included:

### TCP

- TCP SYN scanning
- TCP Connect scanning
- TCP FIN scanning
- TCP NULL scanning
- TCP Xmas scanning
- Default TCP port scanning
- Ports 1–1000 scanning
- Full TCP port scanning

### UDP

- Top-20 UDP scanning
- Top-100 UDP scanning
- Targeted UDP scanning
- Combined TCP and UDP scanning
- UDP service/version detection

The assessment did not include exploitation or credential attacks.

Detailed firewall-evasion analysis is reserved for a separate practical scenario.

---

# 3. Assessment Methodology

The assessment followed a progressive reconnaissance methodology:

```text
Target Identification
        ↓
TCP SYN Baseline
        ↓
TCP Port-Range Comparison
        ↓
TCP Connect Scan
        ↓
Alternative TCP Scans
        ↓
UDP Discovery
        ↓
Targeted UDP Scanning
        ↓
Combined TCP/UDP Scan
        ↓
UDP Service Detection
        ↓
Attack Surface Analysis
        ↓
Security Findings
```

The objective was not simply to identify open ports, but to determine how scanning methodology affects the visibility and interpretation of network services.

---

# 4. TCP Scanning Results

## 4.1 TCP SYN Scan

The initial TCP SYN scan was performed using:

```bash
sudo nmap -sS 192.168.43.56
```

The scan identified **23 open TCP ports**.

The exposed services included:

| Port | State | Service |
|---:|---|---|
| 21 | Open | FTP |
| 22 | Open | SSH |
| 23 | Open | Telnet |
| 25 | Open | SMTP |
| 53 | Open | DNS |
| 80 | Open | HTTP |
| 111 | Open | RPCBind |
| 139 | Open | NetBIOS |
| 445 | Open | SMB |
| 512 | Open | Exec |
| 513 | Open | Login |
| 514 | Open | Shell |
| 1099 | Open | Java RMI Registry |
| 1524 | Open | Ingreslock / Bindshell |
| 2049 | Open | NFS |
| 2121 | Open | FTP |
| 3306 | Open | MySQL |
| 5432 | Open | PostgreSQL |
| 5900 | Open | VNC |
| 6000 | Open | X11 |
| 6667 | Open | IRC |
| 8009 | Open | AJP |
| 8180 | Open | Unknown |

The results demonstrate that the target exposes a large variety of network services.

---

# 5. Port Range Analysis

## 5.1 Ports 1–1000

The following scan was performed:

```bash
sudo nmap -sS -p 1-1000 192.168.43.56
```

It identified **12 open TCP ports**:

```text
21
22
23
25
53
80
111
139
445
512
513
514
```

The scan completed in approximately:

```text
1.20 seconds
```

---

## 5.2 Full TCP Port Range

A complete TCP port scan was then performed:

```bash
sudo nmap -sS -p- 192.168.43.56
```

The scan identified **31 open TCP ports**.

Additional services were discovered on higher-numbered ports, including:

```text
1099
1524
2049
2121
3306
3632
5432
5900
6000
6667
6697
8009
8180
8787
37541
41959
54536
57742
```

The full scan required approximately:

```text
14.73 seconds
```

---

## 5.3 Security Significance

The difference between the two scans demonstrates the importance of scan scope.

| Scan | Open TCP Ports | Duration |
|---|---:|---:|
| Ports 1–1000 | 12 | 1.20 sec |
| Full TCP range | 31 | 14.73 sec |

A scan limited to the first 1,000 ports identified less than half of the open TCP ports discovered through the full scan.

This means that relying exclusively on a limited port range can result in incomplete attack-surface visibility.

From a security assessment perspective, full-port scanning may therefore be appropriate when comprehensive service discovery is required and the additional scan time is acceptable.

---

# 6. TCP Connect Scan

The TCP Connect scan was performed using:

```bash
nmap -sT 192.168.43.56
```

The scan identified the same **23 open TCP ports** as the initial SYN scan.

The primary difference in the output was the representation of closed ports.

The SYN scan reported:

```text
977 closed tcp ports (reset)
```

while the Connect scan reported:

```text
977 closed tcp ports (conn-refused)
```

### Comparison

| Technique | Option | Open Ports | Duration |
|---|---|---:|---:|
| SYN Scan | `-sS` | 23 | 1.49 sec |
| Connect Scan | `-sT` | 23 | 1.37 sec |

In this laboratory environment, both techniques produced equivalent open-port results.

---

# 7. Alternative TCP Scanning Techniques

Alternative TCP scans were performed to observe how different probe types affected port-state classification.

The techniques tested were:

- FIN scan
- NULL scan
- Xmas scan

These scans were not used to perform exploitation.

---

## 7.1 FIN Scan

Command:

```bash
sudo nmap -sF 192.168.43.56
```

The scan classified the previously identified TCP ports as:

```text
open|filtered
```

rather than definitively identifying them as open.

---

## 7.2 NULL Scan

Command:

```bash
sudo nmap -sN 192.168.43.56
```

The scan also classified the exposed ports as:

```text
open|filtered
```

---

## 7.3 Xmas Scan

Command:

```bash
sudo nmap -sX 192.168.43.56
```

The Xmas scan produced the same general classification:

```text
open|filtered
```

---

## 7.4 Interpretation

The alternative TCP scans produced a different interpretation from the SYN and Connect scans.

| Scan | Result Observed |
|---|---|
| SYN | `open` |
| Connect | `open` |
| FIN | `open\|filtered` |
| NULL | `open\|filtered` |
| Xmas | `open\|filtered` |

This demonstrates that Nmap's reported port state depends partly on the type of probe used and the response received from the target.

A detailed investigation into the interaction between these scan types and firewall/filtering behavior will be performed in the dedicated Firewall Evasion & Filtering Analysis scenario.

---

# 8. UDP Scanning Results

UDP scanning was performed to identify services that would not be visible through TCP-only reconnaissance.

---

## 8.1 Top-20 UDP Scan

Command:

```bash
sudo nmap -sU --top-ports 20 192.168.43.56
```

The scan identified:

| Port | State | Service |
|---:|---|---|
| 53 | Open | DNS |
| 68 | Open\|Filtered | DHCP Client |
| 69 | Open\|Filtered | TFTP |
| 137 | Open | NetBIOS Name Service |
| 138 | Open\|Filtered | NetBIOS Datagram |

The remaining ports in the top-20 scan were reported as closed.

The scan required approximately:

```text
20.03 seconds
```

---

# 9. Top-100 UDP Scan

Command:

```bash
sudo nmap -sU --top-ports 100 192.168.43.56
```

The scan identified:

| Port | State | Service |
|---:|---|---|
| 53 | Open | DNS |
| 68 | Open\|Filtered | DHCP Client |
| 69 | Open\|Filtered | TFTP |
| 111 | Open | RPCBind |
| 137 | Open | NetBIOS Name Service |
| 138 | Open\|Filtered | NetBIOS Datagram |
| 2049 | Open | NFS |

The scan required:

```text
121.38 seconds
```

Compared with the top-20 scan, the broader UDP scan identified additional services:

```text
111/udp  RPCBind
2049/udp NFS
```

This demonstrates that increasing UDP scan coverage can reveal additional services, but at the cost of increased scan duration.

---

# 10. Targeted UDP Scan

A targeted UDP scan was performed against selected ports:

```bash
sudo nmap -sU -p 53,111,137,138,161,500 192.168.43.56
```

Results:

| Port | State | Service |
|---:|---|---|
| 53 | Open | DNS |
| 111 | Open | RPCBind |
| 137 | Open | NetBIOS Name Service |
| 138 | Open\|Filtered | NetBIOS Datagram |
| 161 | Closed | SNMP |
| 500 | Closed | ISAKMP |

The scan completed in approximately:

```text
3.83 seconds
```

This demonstrates the efficiency of targeted follow-up scanning after broader reconnaissance has identified ports of interest.

---

# 11. Combined TCP and UDP Scan

The following scan was performed:

```bash
sudo nmap -sS -sU --top-ports 20 192.168.43.56
```

The combined scan demonstrated that TCP and UDP services must be considered independently.

For example:

```text
139/tcp  open
139/udp  closed
```

and:

```text
445/tcp  open
445/udp  closed
```

The target therefore does not expose the same services through both protocols simply because the port numbers are identical.

The scan also identified several UDP ports as `open|filtered`, including:

```text
68/udp
69/udp
135/udp
138/udp
161/udp
631/udp
1434/udp
```

This further demonstrated the difference in result interpretation between TCP and UDP reconnaissance.

---

# 12. UDP Service Detection

Service detection was performed against selected UDP ports:

```bash
sudo nmap -sU -sV -p 53,137,68,69,138,500,520,49152 192.168.43.56
```

The scan identified:

| Port | State | Service / Version |
|---:|---|---|
| 53 | Open | ISC BIND 9.4.2 |
| 68 | Open\|Filtered | DHCP Client |
| 69 | Open\|Filtered | TFTP |
| 137 | Open | Microsoft Windows NetBIOS Name Service |
| 138 | Open\|Filtered | NetBIOS Datagram |
| 500 | Closed | ISAKMP |
| 520 | Closed | Route |
| 49152 | Closed | Unknown |

The service detection scan required approximately:

```text
107.89 seconds
```

---

# 13. Conflicting Service Information

The UDP service-detection scan produced the following information:

```text
Service Info: Host: METASPLOITABLE; OS: Windows
```

This differed from the operating-system detection performed during the previous assessment, which identified the target as:

```text
Linux 2.6.X
```

The Windows information should therefore not be treated as definitive operating-system identification.

The broader assessment identified the target as a Linux-based Metasploitable 2 system.

This observation demonstrates an important assessment principle:

> Individual service responses should be validated against the wider body of evidence before being treated as authoritative.

In this case, the NetBIOS response provided Windows-related service information even though the underlying target was previously identified as Linux.

---

# 14. Attack Surface Analysis

The combined assessment revealed both TCP and UDP exposure.

## TCP Attack Surface

The full TCP scan identified:

```text
31 open TCP ports
```

The exposed services included:

- FTP
- SSH
- Telnet
- SMTP
- DNS
- HTTP
- RPCBind
- NetBIOS
- SMB
- Remote shell services
- Java RMI
- NFS
- MySQL
- PostgreSQL
- VNC
- X11
- IRC
- AJP
- Tomcat
- Additional high-numbered services

This represents a broad network-facing attack surface.

---

## UDP Attack Surface

UDP reconnaissance identified open or potentially accessible services including:

- DNS
- RPCBind
- NetBIOS Name Service
- NFS
- Potential TFTP
- NetBIOS Datagram

These services would not have been visible through TCP-only scanning.

---

# 15. Security Findings

## F-01 — Broad TCP Attack Surface

**Risk Level:** High

### Observation

The full TCP scan identified **31 open TCP ports**.

The target exposes numerous network services, including remote administration, file sharing, databases, web services and other network protocols.

### Security Impact

A large number of exposed services increases the number of potential entry points that must be secured, monitored and maintained.

Every unnecessary network-facing service increases the potential attack surface.

### Recommendation

Organizations should:

- Disable unnecessary services
- Restrict access to required services
- Apply network segmentation
- Limit administrative services to trusted networks
- Monitor exposed services
- Regularly review listening ports

---

## F-02 — Limited Port Scans Can Miss Exposed Services

**Risk Level:** Medium

### Observation

The ports 1–1000 scan identified 12 open TCP ports, while the full-port scan identified 31.

### Security Impact

A reconnaissance process limited to the first 1,000 ports could fail to identify numerous network-facing services.

### Recommendation

Where assessment scope permits, security assessments should include broader or full-port scanning when comprehensive service discovery is required.

---

## F-03 — UDP Services Not Visible Through TCP Scanning

**Risk Level:** Medium

### Observation

UDP scanning identified services including DNS, RPCBind, NetBIOS and NFS.

### Security Impact

A TCP-only reconnaissance process would fail to identify these UDP services.

### Recommendation

Network assessments should include both TCP and UDP scanning when the objective is comprehensive service discovery.

---

## F-04 — Multiple Remote Access Services Exposed

**Risk Level:** High

### Observation

The target exposed several services associated with remote access, including:

```text
22/tcp   SSH
23/tcp   Telnet
512/tcp  exec
513/tcp  login
514/tcp  shell
5900/tcp VNC
```

### Security Impact

Multiple remote-access services increase the number of externally reachable administrative or interactive interfaces.

Some of these services are legacy protocols and may provide weaker security characteristics than modern alternatives.

### Recommendation

Organizations should:

- Disable unnecessary remote-access services
- Prefer secure administration protocols
- Restrict administrative access using firewall rules
- Use network segmentation
- Monitor remote-access activity

---

## F-05 — Database Services Exposed

**Risk Level:** High

### Observation

The target exposed:

```text
3306/tcp  MySQL
5432/tcp  PostgreSQL
```

### Security Impact

Network-accessible database services increase the attack surface and may expose sensitive application infrastructure if not properly restricted.

### Recommendation

Database services should generally be restricted to authorized application or administration networks.

Access should be controlled using:

- Firewall rules
- Network segmentation
- Authentication
- Encryption where appropriate
- Access monitoring

---

## F-06 — File and Network Sharing Services Exposed

**Risk Level:** High

### Observation

The assessment identified:

```text
139/tcp   NetBIOS
445/tcp   SMB
2049/tcp  NFS
137/udp   NetBIOS Name Service
2049/udp  NFS
```

### Security Impact

Exposed file-sharing and network-resource services can increase the potential attack surface and may expose information or resources to unauthorized users.

### Recommendation

Organizations should restrict file-sharing services to authorized networks and disable unnecessary protocols.

---

# 16. Risk Summary

| Finding | Risk | Priority |
|---|---|---|
| F-01 — Broad TCP Attack Surface | High | High |
| F-02 — Limited Scans Can Miss Services | Medium | Medium |
| F-03 — UDP Services Exposed | Medium | Medium |
| F-04 — Multiple Remote Access Services | High | High |
| F-05 — Database Services Exposed | High | High |
| F-06 — File and Network Sharing Services | High | High |

These risk ratings represent an initial assessment based on observed network exposure.

They are not formal CVSS scores.

The presence of an open port alone does not establish that the associated service is exploitable.

---

# 17. Key Assessment Observations

The assessment produced several important observations.

### Observation 1 — Scan scope affects visibility

```text
12 open TCP ports
        ↓
Ports 1–1000

31 open TCP ports
        ↓
Full TCP range
```

A broader scan revealed significantly more services.

---

### Observation 2 — TCP and UDP provide different visibility

TCP scanning identified 31 open TCP ports.

UDP scanning identified additional services that were not visible through TCP scanning.

Therefore:

```text
TCP Scan ≠ UDP Scan
```

Both protocols should be considered when comprehensive service discovery is required.

---

### Observation 3 — Scan techniques produce different results

The SYN and Connect scans identified ports as:

```text
open
```

while FIN, NULL and Xmas scans identified the same ports as:

```text
open|filtered
```

This demonstrates that scan technique affects Nmap's interpretation of target responses.

---

### Observation 4 — UDP scanning can be expensive

The top-100 UDP scan required approximately:

```text
121.38 seconds
```

while the full TCP scan completed in:

```text
14.73 seconds
```

This demonstrates the performance difference between TCP and UDP reconnaissance in this laboratory environment.

---

### Observation 5 — Targeted scanning improves efficiency

The targeted UDP scan completed in:

```text
3.83 seconds
```

after broader scanning had identified relevant ports.

This demonstrates how broad discovery followed by targeted investigation can improve reconnaissance efficiency.

---

# 18. Recommendations

Based on the observations from this assessment, the following general security recommendations are applicable to systems with similar exposure.

## 18.1 Reduce Unnecessary Services

Identify and disable services that are not required for the system's intended function.

---

## 18.2 Restrict Network Access

Use firewall rules and network segmentation to limit access to administrative, database and file-sharing services.

---

## 18.3 Assess Both TCP and UDP

Security assessments should include both protocols when comprehensive network-service discovery is required.

---

## 18.4 Review High-Numbered Ports

Organizations should not assume that services only operate on common or low-numbered ports.

Unexpected high-numbered listening ports should be investigated.

---

## 18.5 Protect Remote Administration

Remote-access services should be limited to authorized networks and protected using appropriate authentication and encryption.

---

## 18.6 Monitor Network Reconnaissance

Network monitoring and intrusion-detection controls should be used to identify unexpected reconnaissance activity.

---

# 19. Evidence

Evidence collected during this assessment included:

- TCP SYN scan output
- TCP Connect scan output
- TCP FIN scan output
- TCP NULL scan output
- TCP Xmas scan output
- TCP ports 1–1000 scan
- Full TCP port scan
- Top-20 UDP scan
- Top-100 UDP scan
- Targeted UDP scan
- Combined TCP/UDP scan
- UDP service/version detection output

Supporting screenshots can be stored in:

```text
../Screenshots/
```

---

# 20. Assessment Limitations

This assessment was performed against an intentionally vulnerable laboratory system.

The findings should therefore not be interpreted as representing the security posture of a production environment.

The assessment focused primarily on network-port discovery and protocol behavior.

The following were outside the scope:

- Exploitation
- Credential attacks
- Password cracking
- Full vulnerability validation
- Detailed firewall analysis
- Comprehensive NSE enumeration
- Malware analysis
- Privilege escalation

Additional analysis will be performed in subsequent practical scenarios.

---

# 21. Lessons Learned

This assessment demonstrated several practical reconnaissance principles.

### 1. Port range matters

Scanning only ports 1–1000 identified 12 open TCP ports, while a full TCP scan identified 31.

### 2. Different scan techniques produce different results

SYN and Connect scans provided definitive open-port classifications, while FIN, NULL and Xmas scans produced `open|filtered` results.

### 3. TCP and UDP must be assessed separately

UDP services can remain completely invisible during TCP-only reconnaissance.

### 4. UDP scanning requires patience

Broader UDP scans took significantly longer than TCP scans in the laboratory environment.

### 5. Targeted scanning is useful

After broad discovery, targeted scanning can reduce scan time and allow deeper investigation of specific services.

### 6. Nmap results require interpretation

Individual service responses can contain information that appears inconsistent with other assessment evidence.

Security analysts should validate findings rather than blindly accepting individual results.

---

# 22. Conclusion

The TCP and UDP Port Scanning Assessment successfully demonstrated the practical differences between multiple Nmap scanning techniques.

The assessment identified **31 open TCP ports** through full-range TCP scanning and several additional UDP services through UDP reconnaissance.

The comparison between limited and full TCP scanning demonstrated that scan scope directly affects network-service visibility.

The assessment also demonstrated that:

- SYN and Connect scans can produce similar results
- FIN, NULL and Xmas scans can produce different port-state classifications
- UDP services require separate reconnaissance
- Broader UDP scans require significantly more time
- Targeted scans can improve efficiency
- Tool output must be interpreted within the context of the overall assessment

The results provide a practical foundation for the next phase of the Nmap portfolio:

```text
Practical Scanning Scenario 03
Firewall Evasion & Filtering Analysis
```

The next scenario will build on the FIN, NULL and Xmas scan observations from this assessment and investigate how packet filtering and firewall behavior influence Nmap's results.

---

## Related Documentation

### Practical Scanning Scenario

```text
../Practical Scanning Scenarios/02-TCP-and-UDP-Port-Scanning.md
```

### Previous Scenario

```text
../Practical Scanning Scenarios/01-Host-Discovery-and-Network-Mapping.md
```

### Nmap Command Reference

```text
../Commands/Nmap-Command-Reference.md
```

### Evidence

```text
../Screenshots/
```

### Security Assessment Reports

```text
../report/
```

---

## Assessment Metadata

| Field | Value |
|---|---|
| Assessment | TCP and UDP Port Scanning |
| Target | `192.168.43.56` |
| Network | `192.168.43.0/24` |
| Nmap Version | `7.99` |
| Assessment Environment | Authorized Laboratory |
| Exploitation | Not Performed |
| Status | Completed |
```
