# Scenario 01 — Host Discovery & Network Mapping

## Objective

Perform a structured network reconnaissance assessment against an authorized **Metasploitable 2** laboratory system.

The assessment progresses from host discovery to port scanning, service identification, operating system detection, and default NSE enumeration.

The objective is to demonstrate how Nmap can be used to:

- Identify reachable hosts
- Identify open, closed, and filtered ports
- Determine running services
- Identify service versions
- Estimate the target operating system
- Collect additional information using Nmap's default NSE scripts
- Document and interpret reconnaissance findings

> **Authorization:** This assessment is performed against a deliberately vulnerable Metasploitable 2 virtual machine in an authorized laboratory environment. No unauthorized systems are being scanned.

---

# Lab Environment

## Target System

| Attribute | Value |
|---|---|
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Assessment Tool | Nmap |
| Assessment Type | Network Reconnaissance |
| Environment | Authorized Virtual Lab |

## Assessment Scope

The assessment is limited to the Metasploitable 2 virtual machine at:

```text
192.168.43.56
```
---

# Assessment Workflow

```
Determine Lab Network
        │
        ▼
Host Discovery
        │
        ▼
TCP Port Scanning
        │
        ▼
Service & Version Detection
        │
        ▼
Operating System Detection
        │
        ▼
Default NSE Enumeration
        │
        ▼
Evidence Collection
        │
        ▼
Analysis & Documentation
```

---

# Step 1 - Host Discovery

## Goal

Before performing network-wide host discovery, identify the network interface and subnet used by the Nmap assessment machine.

This prevents the assessment from relying on an assumed network range.

### Linux

Identify the active network interfaces:
```
ip addr
```

View the routing table:
```
ip route
```
### Windows

Display the network configuration:
```
ipconfig
```

### Evidence

Record:

- Nmap scanning machine IP address
- Network interface
- Subnet
- Default gateway where applicable
- Route used to reach 192.168.43.56

### Why This Step Matters

Understanding the local network topology helps determine the correct scope for host discovery and explains how the scanning machine communicates with the Metasploitable 2 target.

---

# Step 2 — Host Discovery

## Goal

Identify active hosts within the authorized laboratory network.

### Command

Once the correct lab subnet has been confirmed, perform a ping scan against that subnet.

For example, if the lab network is confirmed to be **192.168.43.0/24:**
```
nmap -sn 192.168.43.0/24
```
> Important: Use the subnet confirmed from your network configuration. Do not assume **192.168.43.0/24** if your lab configuration shows a different network.

### Expected Information

The scan may identify:
- Hosts that are reachable
- IP addresses
- Hostnames where available
- MAC addresses where Nmap can obtain them

### Evidence

Record the actual output from the scan.

Host discovery results:

> Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-08 23:04 +0100\
Nmap scan report for 192.168.43.1\
Host is up (0.043s latency).\
MAC Address: 16:11:14:81:F0:22 (Unknown)\
**Nmap scan report for 192.168.43.56**\
**Host is up (0.00098s latency).**\
**MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)**\
Nmap scan report for Saint (192.168.43.155)\
Host is up.\
Nmap done: 256 IP addresses (3 hosts up) scanned in 3.19 seconds\


### Analysis

After completing the scan, record:
| Host | IP Address | Status |MAC Address| Notes |
|---|---|---|---|---|
| TBD | TBD| TBD | TBD | TBD |




### Why This Step Matters

Host discovery establishes which systems are reachable before performing more detailed scanning.

In a larger environment, this can reduce unnecessary scanning traffic and help establish an initial asset inventory.

### Security Insight

Host discovery provides an initial view of the systems exposed within a network segment.

From a defensive perspective, unexpected hosts discovered during an assessment may indicate:

- Unknown assets
- Misconfigured devices
- Unauthorized systems
- Poor network segmentation

---

# Step 3 — TCP Port Scanning

## Goal

Identify TCP ports that are open, closed, or filtered on the Metasploitable 2 target.

### Command

```bash
nmap -sS 192.168.43.56
```

### What This Scan Does

The TCP SYN scan sends SYN probes to TCP ports and analyzes the responses to determine their state.

Common results include:

- State	Meaning
- Open	An application is listening on the port
- Closed	The host is reachable but no application is listening
- Filtered	Filtering prevents Nmap from determining the port state

### Evidence

Record the actual scan output:

TCP scan results:

> Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-08 23:10 +0100\
Nmap scan report for 192.168.43.56\
Host is up (0.00018s latency).\
Not shown: 977 closed tcp ports (reset)\
PORT     STATE SERVICE\
21/tcp   open  ftp\
22/tcp   open  ssh\
23/tcp   open  telnet\
25/tcp   open  smtp\
53/tcp   open  domain\
80/tcp   open  http\
111/tcp  open  rpcbind\
139/tcp  open  netbios-ssn\
445/tcp  open  microsoft-ds\
512/tcp  open  exec\
513/tcp  open  login\
514/tcp  open  shell\
1099/tcp open  rmiregistry\
1524/tcp open  ingreslock\
2049/tcp open  nfs\
2121/tcp open  ccproxy-ftp\
3306/tcp open  mysql\
5432/tcp open  postgresql\
5900/tcp open  vnc\
6000/tcp open  X11\
6667/tcp open  irc\
8009/tcp open  ajp13\
8180/tcp open  unknown\
MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)\

Nmap done: 1 IP address (1 host up) scanned in 0.87 seconds\


### Analysis

After running the scan, identify:

- Number of open ports
- Important exposed ports
- Unexpected services
- Closed ports
- Filtered ports, if present

Record the findings:

| Port | Protocol | State | Service | Observation |
|---|---|---|---|---|
| TBD	| TCP	| TBD	| TBD	| TBD |
| TBD	| TCP	| TBD	| TBD	| TBD |
| TBD | TCP | TBD	| TBD	| TBD |

### Why This Step Matters

Every exposed network service represents part of the system's attack surface.

Identifying open ports provides the foundation for subsequent service enumeration and security assessment.

### Security Insight

An open port does not automatically mean that a vulnerability exists.

It indicates that a network service is accessible and may require further investigation.


---

# Step 4 — Service & Version Detection

## Goal

Determine which applications and service versions are running on the discovered open ports.

### Command
```
nmap -sV 192.168.43.56
```

### Expected Information

Nmap may identify:

- Service name
- Service version
- Application information
- Protocol information
- Product/vendor information where available

### Evidence

Record the actual output:

Service detection results:
> Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-08 23:16 +0100\
Nmap scan report for 192.168.43.56\
Host is up (0.00023s latency).\
Not shown: 977 closed tcp ports (reset)\
PORT     STATE SERVICE     VERSION\
21/tcp   open  ftp         vsftpd 2.3.4\
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)\
23/tcp   open  telnet      Linux telnetd\
25/tcp   open  smtp        Postfix smtpd\
53/tcp   open  domain      ISC BIND 9.4.2\
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)\
111/tcp  open  rpcbind     2 (RPC #100000)\
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)\
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)\
512/tcp  open  exec?\
513/tcp  open  login       OpenBSD or Solaris rlogind\
514/tcp  open  shell?\
1099/tcp open  java-rmi    GNU Classpath grmiregistry\
1524/tcp open  bindshell   Metasploitable root shell\
2049/tcp open  nfs         2-4 (RPC #100003)\
2121/tcp open  ftp         ProFTPD 1.3.1\
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5\
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7\
5900/tcp open  vnc         VNC (protocol 3.3)\
6000/tcp open  X11         (access denied)\
6667/tcp open  irc         UnrealIRCd\
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)\
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1\
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :\
SF-Port514-TCP:V=7.99%I=7%D=9/8%Time=6AA08932%P=x86_64-pc-linux-gnu%r(NULL\
SF:,2C,"\x01Couldn't\x20get\x20address\x20for\x20your\x20host\x20\(Saint\)\
SF:\n");\
MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)\
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel\
\
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .\
Nmap done: 1 IP address (1 host up) scanned in 138.31 seconds\


### Analysis

For each significant service, document:

| Port | Service | Version | Security Observation |
|---|---|---|---|

TBD | TBD | TBD | TBD |
TBD | TBD | TBD | TBD |
TBD | TBD | TBD | TBD |
TBD | TBD | TBD | TBD |


### Why This Step Matters

Knowing that a port is open is only the beginning of an assessment.

Identifying the software and version provides additional context for determining:

- Whether the software is outdated
- Whether the service is unnecessarily exposed
- Whether additional enumeration is appropriate
- Whether a known vulnerability may warrant further investigation

> Important: A detected software version alone does not prove that a vulnerability exists. Vulnerability claims require additional evidence.

### Security Insight

Service enumeration allows an assessor to move from simply identifying an exposed port to understanding what application is responsible for that exposure.

This information can later be correlated with vendor documentation, security advisories, and vulnerability databases.

---

# Step 5 — Operating System Detection
## Goal

Estimate the operating system and device characteristics of the target.

### Command
```
nmap -O 192.168.43.56
```

### Expected Information

Nmap may provide:

- Operating system family
- OS version estimates
- Kernel information where detectable
- Device type
- Network distance
- OS confidence information

### Evidence

Record the actual output:

Operating system detection results:
> Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-08 23:21 +0100\
Nmap scan report for 192.168.43.56\
Host is up (0.00087s latency).\
Not shown: 977 closed tcp ports (reset)\
PORT     STATE SERVICE\
21/tcp   open  ftp\
22/tcp   open  ssh\
23/tcp   open  telnet\
25/tcp   open  smtp\
53/tcp   open  domain\
80/tcp   open  http\
111/tcp  open  rpcbind\
139/tcp  open  netbios-ssn\
445/tcp  open  microsoft-ds\
512/tcp  open  exec\
513/tcp  open  login\
514/tcp  open  shell\
1099/tcp open  rmiregistry\
1524/tcp open  ingreslock\
2049/tcp open  nfs\
2121/tcp open  ccproxy-ftp\
3306/tcp open  mysql\
5432/tcp open  postgresql\
5900/tcp open  vnc\
6000/tcp open  X11\
6667/tcp open  irc\
8009/tcp open  ajp13\
8180/tcp open  unknown\
MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)\
**Device type: general purpose**\
**Running: Linux 2.6.X**\
**OS CPE: cpe:/o:linux:linux_kernel:2.6**\
**OS details: Linux 2.6.9 - 2.6.33**\
**Network Distance: 1 hop**\
\
**OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .**\
Nmap done: 1 IP address (1 host up) scanned in 2.39 seconds\


### Analysis

Document:

- Detected/estimated operating system:

*TBD*

- Device type:

*TBD*

- Network distance:

*TBD*

- Additional observations:

*TBD*


### Why This Step Matters

Operating system identification provides additional context for understanding the target and selecting appropriate security controls or follow-up assessment techniques.

### Security Insight

OS detection is an estimate, not definitive proof of the operating system.

Results can be affected by:

- Firewalls
- Packet filtering
- Network conditions
- Insufficient response data
- Customized or unusual network stacks

Therefore, OS detection results should be treated as an assessment indicator rather than absolute confirmation.


---

# Step 6 — Default NSE Enumeration
## Goal

Collect additional information about discovered services using Nmap's default NSE script set.

### Command
```
nmap -sC 192.168.43.56
```

### What This Can Provide

Depending on the services exposed by the target, default NSE scripts may provide information such as:

- Service-specific details
- HTTP information
- SSH information
- SSL/TLS information
- SMB information
- DNS information
- Other protocol-specific metadata

The exact results depend on the services discovered on the target.

### Evidence

Record the actual output:

Default NSE results:
> [Actual Nmap output will be inserted here]

### Analysis

Identify interesting findings such as:

- Service banners
- Web application information
- Hostnames
- Authentication-related information
- SSL/TLS details
- Protocol configuration information
- Additional service metadata

Record significant observations:

| Service | NSE Information | Security Observation|
|---|---|---|
| TBD	| TBD	| TBD |
| TBD	| TBD	| TBD |
| TBD	| TBD	| TBD |


### Why This Step Matters

Service enumeration provides additional context beyond simply identifying an open port.

This information can help security professionals understand how exposed services are configured and determine appropriate follow-up investigation.

> Note: NSE scripts can interact with services. Although the default script set is commonly used for enumeration, it should still be used only against systems within the authorized assessment scope.

---

# Step 7 — Evidence Collection
## Goal

Preserve accurate evidence from the assessment.

For each major scan, capture:

- Command executed
- Target IP
- Date and time of assessment
- Terminal output
- Relevant screenshots
- Observations
- Interpretation

### Evidence Checklist

- [ ] Network configuration recorded
- [ ] Host discovery output captured
- [ ] TCP scan output captured
- [ ] Service/version scan output captured
- [ ] OS detection output captured
- [ ] NSE output captured
- [ ] Screenshots collected
- [ ] Interesting findings documented


### Evidence Storage

Screenshots for this scenario will be stored in:

Screenshots/

If scan output files are generated, they can be stored with the appropriate assessment evidence.

---


# Step 8 — Document Findings
## Finding Summary

After completing the scans, summarize the results.

| Category | Result |
|---|---|
| Target | Metasploitable 2 |
| Target IP	| 192.168.43.56 |
| Host Status | TBD |
| Open TCP Ports | TBD |
| Identified Services | TBD |
| Service Versions | TBD |
| Operating System | TBD |
| NSE Observations | TBD |
| Potential Security Concerns | TBD |

## Security Observations

The following section will be completed after the actual assessment.

Potential areas of investigation include:

- Excessively exposed network services
- Outdated software versions
- Insecure protocols
- Unnecessary services
- Weak service configurations
- Information disclosure
- Clear-text communication
- Legacy protocols

These are assessment categories, not confirmed vulnerabilities. Findings must be supported by evidence collected during the lab.


### Assessment Analysis

After completing the scans, answer the following questions.

1. What hosts were discovered?

TBD

2. Which TCP ports were open?

TBD

3. Which services were identified?

TBD

4. Which service versions were detected?

TBD

5. What operating system did Nmap estimate?

TBD

6. What additional information was discovered through NSE?

TBD

7. Which findings require further investigation?

TBD

### Assessment Summary

The assessment followed a structured reconnaissance workflow against the authorized Metasploitable 2 laboratory system.

The process progressed from:

```
Network Identification
        ↓
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
```

The final assessment conclusions will be based exclusively on the results obtained during the practical lab.

### Lessons Learned

This scenario demonstrates that effective network reconnaissance is a progressive process.

Rather than immediately performing extensive scans, an assessment can begin by establishing network scope and identifying reachable hosts before moving toward increasingly detailed enumeration.

The exercise also demonstrates the importance of distinguishing between:

- What Nmap directly observed
- What Nmap estimated
- What requires additional investigation
- What can and cannot be considered a confirmed vulnerability

Accurate interpretation is therefore just as important as executing the scanning commands.

### Related Commands

-sn — Host discovery
-sS — TCP SYN scanning
-sV — Service and version detection
-O — Operating system detection
-sC — Default NSE scripts

### References

Nmap Reference Guide
Nmap Documentation
RFC 793 — Transmission Control Protocol

### Assessment Status

Status: In Progress

Target: 192.168.43.5610.0.2.15

Environment: Authorized Metasploitable 2 Laboratory

>Next Action: Execute the assessment commands and replace the TBD sections with verified results and screenshots.
