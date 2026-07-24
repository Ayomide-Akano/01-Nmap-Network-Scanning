# Nmap Command Reference

## Overview

This document provides a categorized reference to commonly used Nmap commands for authorized network reconnaissance, service enumeration, operating system detection, firewall evasion, and reporting.

The commands documented here are intended for cybersecurity learning, lab exercises, vulnerability assessments, and penetration testing conducted within authorized environments.

> **Disclaimer**
>
> The commands in this document are provided for educational purposes and should only be used on systems or networks for which you have explicit authorization to perform security testing.

---

# Table of Contents

1. Host Discovery
2. Port Scanning
3. Service & Version Detection
4. Operating System Detection
5. Nmap Scripting Engine (NSE)
6. Firewall Evasion
7. Performance & Timing
8. Output Formats

---

# 1. Host Discovery

## Overview

Host discovery is the first phase of a network reconnaissance exercise. Its purpose is to determine which systems on a network are online before performing more detailed scans such as port scanning or service enumeration.

By identifying active hosts first, security professionals can reduce unnecessary traffic and focus their assessment on reachable devices.

---

## Ping Scan (`-sn`)

### Purpose

Discovers live hosts without performing a port scan.

### Syntax

```bash
nmap -sn <target>
```

### Example

```bash
nmap -sn 192.168.1.0/24
```

### How It Works

Nmap sends host discovery probes (such as ICMP Echo Requests and, depending on the environment, ARP or TCP probes) to determine whether a host is online. Since no port scan is performed, this scan is relatively fast.

### When to Use

- Identifying active hosts on a network
- Creating an inventory of live devices
- Preparing for further reconnaissance

### Notes

- Faster than a full port scan.
- Does not identify open ports.
- Some firewalls may block ICMP responses.

### Security Insight

Host discovery helps reduce scan time by focusing subsequent scans only on systems that respond. However, a host that does not respond to ping probes is not necessarily offline, as firewalls may block those probes.

---

## ARP Ping Scan (`-PR`)

### Purpose

Discovers live hosts on a local network using Address Resolution Protocol (ARP) requests instead of ICMP Echo Requests.

### Syntax

```bash
nmap -PR <target>
```

### Example

```bash
nmap -PR 192.168.1.0/24
```

### How It Works

When scanning devices on the same local network, Nmap sends ARP requests asking, "Who has this IP address?" Any device that owns the IP responds with its MAC address, confirming that it is online.

Unlike ICMP, ARP traffic is essential for communication within a local network and is rarely blocked by host firewalls.

### When to Use

- Scanning hosts on your local LAN
- Discovering active devices in a subnet
- Building an inventory of local network assets

### Notes

- Works only on the local network.
- Usually more reliable than ICMP host discovery.
- Cannot be used to discover hosts across the Internet.

### Security Insight

Many administrators block ICMP Echo Requests to reduce network visibility. However, ARP communication is still required for local devices to communicate, making ARP Ping one of the most reliable host discovery techniques on a LAN.

---

## Disable Host Discovery (`-Pn`)

### Purpose

Skips the host discovery phase and treats the target as if it is online, even if it does not respond to ping or ARP requests.

### Syntax

```bash
nmap -Pn <target>
```

### Example

```bash
nmap -Pn 192.168.1.15
```

### How It Works

Normally, Nmap first checks whether a host is online before scanning it. The `-Pn` option disables this check and proceeds directly to port scanning.

This is useful when a firewall blocks host discovery probes but the target still has accessible services.

### When to Use

- ICMP Echo Requests are blocked.
- The target is protected by a firewall.
- You know the target is online and want to skip host discovery.

### Notes

- Can significantly increase scan time.
- Every specified target is scanned, even if it is actually offline.
- Commonly used during penetration tests when ICMP is filtered.

### Security Insight

Blocking ICMP does not make a system invisible. If services such as SSH, HTTP, or RDP are exposed, Nmap can still discover them using `-Pn`. This is why disabling ping responses alone is not considered a complete security measure.

---

## Related Tools

Nmap provides host discovery capabilities using ICMP, ARP, and TCP probes.

For lower-level packet crafting and custom packet generation, see the companion repository:

**02-HPING3-Packet-Crafting**

---

# 2. Port Scanning

## Overview

Port scanning is the process of identifying open, closed, or filtered ports on a target system.

An open port usually indicates that a service is actively listening for incoming connections, while a closed or filtered port may indicate that no service is available or that a firewall is restricting access.

Port scanning is one of the most important phases of network reconnaissance because it helps identify the services running on a system and potential attack surfaces that may require further investigation.

---

## TCP SYN Scan (`-sS`)

### Purpose

Performs a TCP SYN scan, commonly referred to as a **half-open scan**, to identify open TCP ports without completing the full TCP three-way handshake.

### Syntax

```bash
nmap -sS <target>
```

### Example

```bash
nmap -sS 192.168.1.15
```

### How It Works

A TCP SYN scan sends a **SYN** packet to the target port.

The response determines the state of the port:

| Response | Port State |
|----------|------------|
| SYN/ACK | Open |
| RST | Closed |
| No Response or ICMP Error | Filtered |

When a SYN/ACK is received, Nmap immediately sends a **RST** packet instead of completing the TCP handshake.

Because the connection is never fully established, this technique is known as a **half-open scan**.

### When to Use

- Fast reconnaissance
- Security assessments
- Penetration testing
- Internal network discovery

### Advantages

- Fast
- Efficient
- Widely supported
- Generates less application-level logging than a full TCP connection

### Limitations

- Requires elevated privileges on most operating systems.
- May still be detected by intrusion detection and intrusion prevention systems.

### Security Insight

The SYN scan is one of the most commonly used reconnaissance techniques because it balances speed and accuracy. Although it does not complete the TCP handshake, modern firewalls, IDS, and IPS solutions can still detect SYN scanning behavior, especially when many ports or hosts are scanned rapidly.

### Related Diagram

See:

`diagrams/TCP-SYN-Scan.md`

for a packet-level illustration of how the TCP SYN Scan works.

---

## TCP Connect Scan (`-sT`)

### Purpose

Performs a full TCP connection by completing the TCP three-way handshake. This scan is typically used when elevated privileges are unavailable and a SYN scan cannot be performed.

### Syntax

```bash
nmap -sT <target>
```

### Example

```bash
nmap -sT 192.168.1.15
```

### How It Works

Unlike a SYN scan, a TCP Connect scan completes the full TCP handshake:

1. SYN
2. SYN/ACK
3. ACK

After the connection is established, Nmap immediately closes it.

Because the operating system's networking stack performs the connection, this scan is generally more noticeable in application logs.

### Port States

| Response | Port State |
|----------|------------|
| Connection Established | Open |
| RST | Closed |
| No Response / ICMP Error | Filtered |

### When to Use

- Running Nmap without administrative or root privileges.
- Performing basic network reconnaissance.
- Testing connectivity to TCP services.

### Advantages

- Does not require raw packet privileges on most operating systems.
- Reliable for identifying open TCP ports.

### Limitations

- Slower than a SYN scan.
- Completes the TCP handshake, making it more likely to appear in server and application logs.

### Security Insight

A TCP Connect scan is easier for defenders to observe because it establishes a complete TCP connection. Security monitoring tools and application logs often record these connections, making this scan less stealthy than a SYN scan.

### Related Diagram

See:

`diagrams/TCP-Three-Way-Handshake.md`

to understand how a complete TCP connection is established.

---

## UDP Scan (`-sU`)

### Purpose

Scans UDP ports to determine whether UDP-based services are open, closed, or filtered.

Unlike TCP, UDP does not establish a connection before transmitting data, making UDP scanning slower and often more difficult to interpret.

### Syntax

```bash
nmap -sU <target>
```

### Example

```bash
nmap -sU 192.168.1.15
```

### How It Works

Nmap sends UDP packets to the target port.

The response determines the state of the port:

| Response | Port State |
|----------|------------|
| UDP Response | Open |
| ICMP Port Unreachable | Closed |
| No Response | Open \| Filtered |

Because many UDP services do not respond to unexpected packets, Nmap often cannot distinguish between an open port and a filtered port when no response is received.

### Common UDP Services

| Port | Service |
|------|----------|
| 53 | DNS |
| 67/68 | DHCP |
| 69 | TFTP |
| 123 | NTP |
| 161 | SNMP |
| 500 | IKE/IPsec |

### When to Use

- Identifying UDP services running on a host.
- Assessing network infrastructure devices.
- Enumerating DNS, SNMP, NTP, and other UDP-based protocols.

### Advantages

- Discovers services that TCP scans cannot detect.
- Useful for identifying exposed infrastructure services.

### Limitations

- Generally slower than TCP scanning.
- Many UDP services do not respond, making results less conclusive.
- Firewalls frequently filter UDP traffic.

### Security Insight

Many organizations focus primarily on securing TCP services while overlooking UDP-based services. Misconfigured DNS, SNMP, or NTP services can expose valuable information to attackers or provide opportunities for amplification attacks.

### Related Diagram

*(Diagram to be added in a future update.)*

---

# Advanced TCP Scans

## Overview

Advanced TCP scans use unusual combinations of TCP flags to gather information about the state of a target port.

Unlike standard TCP Connect or SYN scans, these techniques do not attempt to establish a normal TCP connection. Instead, they rely on how operating systems implement the TCP protocol as defined in RFC 793.

These scan types are commonly used during penetration testing to gather information while attempting to bypass certain firewall rules or packet filtering mechanisms.

> **Note**
>
> Modern firewalls, intrusion detection systems (IDS), intrusion prevention systems (IPS), and some operating systems may detect or respond differently to these scans. Results can vary depending on the target environment.

---

## ACK Scan (`-sA`)

### Purpose

Determines whether a firewall is filtering TCP ports rather than identifying whether ports are open or closed.

Unlike SYN scans, ACK scans cannot determine if a port is open.

### Syntax

```bash
nmap -sA <target>
```

### Example

```bash
nmap -sA 192.168.1.15
```

### How It Works

The scanner sends a TCP packet with only the ACK flag set.

The target responds as follows:

| Response | Port State |
|-----------|------------|
| RST | Unfiltered |
| No Response / ICMP Error | Filtered |

### When to Use

- Firewall rule analysis
- Packet filtering assessment
- Security testing

### Advantages

- Helps identify firewall behavior.
- Useful for mapping filtering rules.

### Limitations

- Cannot determine whether a port is open.
- Often misunderstood by beginners.

### Security Insight

Security professionals frequently combine ACK scans with SYN scans to distinguish firewall filtering from host availability. An ACK scan provides information about the firewall rather than the service itself.

### Related Diagram

*(Diagram to be added later.)*

---

## FIN Scan (`-sF`)

### Purpose

Attempts to identify open TCP ports by sending packets with only the FIN flag set.

### Syntax

```bash
nmap -sF <target>
```

### Example

```bash
nmap -sF 192.168.1.15
```

### How It Works

According to RFC 793:

- Closed ports respond with RST.
- Open ports ignore the packet.

Therefore:

| Response | Port State |
|-----------|------------|
| No Response | Open \| Filtered |
| RST | Closed |

### When to Use

- Firewall testing
- IDS evaluation
- Alternative reconnaissance methods

### Advantages

- May bypass simple packet filters.
- Uses an unusual TCP flag combination.

### Limitations

- Ineffective against many Windows systems.
- Modern IDS/IPS solutions often detect it.

### Security Insight

FIN scans rely on operating systems following RFC 793. Because Windows TCP/IP implementations typically respond differently, results are generally more reliable against Unix and Linux systems.

### Related Diagram

*(Diagram to be added later.)*

---

## NULL Scan (`-sN`)

### Purpose

Attempts to identify open ports by sending a TCP packet with no flags set.

### Syntax

```bash
nmap -sN <target>
```

### Example

```bash
nmap -sN 192.168.1.15
```

### How It Works

The packet contains no TCP flags.

According to RFC 793:

| Response | Port State |
|-----------|------------|
| No Response | Open \| Filtered |
| RST | Closed |

### When to Use

- Firewall testing
- Packet filtering analysis
- Operating system fingerprinting

### Advantages

- Uses an uncommon packet type.
- Useful in certain penetration testing scenarios.

### Limitations

- Not reliable against Windows systems.
- Many modern firewalls detect this scan.

### Security Insight

NULL scans work because some TCP implementations simply ignore unexpected packets sent to open ports. Modern security devices, however, frequently recognize and log this behavior.

### Related Diagram

*(Diagram to be added later.)*

---

## XMAS Scan (`-sX`)

### Purpose

Attempts to identify open ports by sending packets with the FIN, PSH, and URG flags simultaneously.

### Syntax

```bash
nmap -sX <target>
```

### Example

```bash
nmap -sX 192.168.1.15
```

### How It Works

The TCP packet has three flags enabled:

- FIN
- PSH
- URG

This unusual combination makes the packet appear "lit up like a Christmas tree," which gives the scan its name.

According to RFC 793:

| Response | Port State |
|-----------|------------|
| No Response | Open \| Filtered |
| RST | Closed |

### When to Use

- Firewall testing
- IDS evaluation
- TCP stack analysis

### Advantages

- Useful for testing how systems handle unusual TCP packets.
- Can provide additional information during reconnaissance.

### Limitations

- Unreliable against Windows systems.
- Frequently detected by modern security products.

### Security Insight

Although XMAS scans were historically useful for bypassing simplistic packet filters, modern enterprise firewalls and intrusion detection systems generally recognize and alert on this scanning technique.

### Related Diagram

*(Diagram to be added later.)*

---

# 3. Service & Version Detection

## Overview

Once open ports have been identified, the next step is to determine which services are running on those ports and, where possible, identify their software versions.

Service and version detection helps security professionals understand the technologies deployed within an environment, identify outdated software, and determine whether known vulnerabilities may affect the target.

---
## Service Version Detection (`-sV`)

### Purpose

Identifies the application and version of services listening on open ports.

### Syntax

```bash
nmap -sV <target>
```

### Example

```bash
nmap -sV 192.168.1.15
```

### How It Works

After discovering open ports, Nmap sends carefully crafted probes to each service.

It then compares the responses against its service fingerprint database to identify:

- Service name
- Software version
- Vendor
- Sometimes the operating system

### Example Output

```text
22/tcp open  ssh   OpenSSH 9.3p1 Ubuntu
80/tcp open  http  Apache httpd 2.4.58
443/tcp open https Apache httpd 2.4.58
3306/tcp open mysql MySQL 8.0.39
```

### When to Use

- Vulnerability assessments
- Penetration testing
- Asset inventory
- Security audits

### Advantages

- Identifies software versions.
- Helps prioritize vulnerabilities.
- Improves asset visibility.

### Limitations

- Some services intentionally hide version information.
- Version detection increases scan duration.
- Firewalls or IPS devices may interfere with probes.

### Security Insight

Knowing that a service is running is useful. Knowing exactly which version is running is far more valuable because vulnerabilities are typically associated with specific software versions rather than the service itself.

### Related Diagram

*(Diagram to be added later.)*

---

## Aggressive Scan (`-A`)

### Purpose

Performs an aggressive scan by combining several advanced Nmap features into a single command.

### Syntax

```bash
nmap -A <target>
```

### Example

```bash
nmap -A 192.168.1.15
```

### What It Includes

The `-A` option enables:

- Operating System Detection (`-O`)
- Service Version Detection (`-sV`)
- Default NSE Scripts
- Traceroute

### When to Use

- Comprehensive host reconnaissance
- Security assessments
- Internal network inventories
- Lab environments

### Advantages

- Collects extensive information with a single command.
- Saves time during reconnaissance.
- Excellent for lab exercises and demonstrations.

### Limitations

- Generates more network traffic.
- Takes longer to complete.
- More likely to trigger IDS/IPS alerts.
- Not suitable for stealthy assessments.

### Security Insight

Although the Aggressive Scan is convenient, experienced penetration testers often prefer to run individual scans separately. This provides greater control over scan speed, noise level, and the amount of information collected at each stage.

### Related Diagram

*(Diagram to be added later.)*

---

# 4. Operating System Detection

## Overview

Operating System (OS) Detection attempts to identify the operating system running on a target host by analyzing how it responds to specially crafted network probes.

Unlike banner grabbing, which relies on services voluntarily revealing information, OS detection uses TCP/IP fingerprinting techniques to compare a target's network behavior against Nmap's extensive fingerprint database.

This capability helps security professionals understand the target environment, identify potential vulnerabilities associated with specific operating systems, and prioritize further assessment activities.

---
## Operating System Detection (`-O`)

### Purpose

Attempts to determine the operating system running on a target by analyzing responses to a series of specially crafted TCP, UDP, and ICMP probes.

### Syntax

```bash
nmap -O <target>
```

### Example

```bash
nmap -O 192.168.1.15
```

### How It Works

Nmap sends multiple carefully crafted packets to the target and analyzes characteristics of the responses, including:

- TCP Initial Sequence Numbers (ISN)
- TCP Window Size
- TCP Options
- Time To Live (TTL)
- IP Identification (IP ID)
- ICMP Error Messages
- Response Timing

These characteristics are compared against Nmap's fingerprint database to estimate the target operating system.

### Example Output

```text
Device type: general purpose
Running: Linux 5.X
OS CPE: cpe:/o:linux:linux_kernel:5
OS details: Linux 5.4 - 5.15
Network Distance: 1 hop
```

### When to Use

- Network reconnaissance
- Vulnerability assessments
- Asset identification
- Internal security audits
- Penetration testing

### Advantages

- Helps identify operating systems without authentication.
- Assists in selecting appropriate enumeration techniques.
- Supports vulnerability prioritization.

### Limitations

- Accuracy depends on the number of open and closed ports available.
- Firewalls and packet filtering may interfere with fingerprinting.
- Virtual machines and network devices may produce ambiguous results.

### Security Insight

OS detection is based on network behavior rather than explicit identification. Security controls such as firewalls, packet normalization, and TCP/IP stack modifications can reduce the accuracy of fingerprinting, making the detected operating system an educated estimate rather than a guaranteed result.

### Related Diagram

*(Diagram to be added later.)*

---

## OS Guessing (`--osscan-guess`)

### Purpose

Increases the aggressiveness of operating system detection by allowing Nmap to provide its best guess when an exact fingerprint match cannot be determined.

### Syntax

```bash
nmap -O --osscan-guess <target>
```

### Example

```bash
nmap -O --osscan-guess 192.168.1.15
```

### How It Works

When the collected fingerprint does not closely match an entry in Nmap's fingerprint database, this option instructs Nmap to return the closest matching operating systems instead of reporting that no exact match was found.

### When to Use

- Unknown environments
- Research labs
- Internal assessments
- Operating system fingerprint analysis

### Advantages

- Provides useful estimates when exact identification is not possible.
- Helps guide further investigation.

### Limitations

- Results are less reliable than standard OS detection.
- Should not be treated as definitive identification.

### Security Insight

Professional penetration testers use OS guessing as a starting point rather than a conclusion. Additional evidence from service banners, SMB enumeration, SSH fingerprints, and web technologies should always be combined before confidently identifying an operating system.

---

# 5. Nmap Scripting Engine (NSE)

## Overview

The Nmap Scripting Engine (NSE) extends Nmap beyond traditional port scanning by allowing users to execute scripts that perform service enumeration, vulnerability detection, authentication checks, and information gathering.

NSE scripts are written in the Lua programming language and are organized into categories based on their intended purpose.

By combining port scanning with scripting capabilities, Nmap becomes a powerful reconnaissance and security assessment tool.

---
## Default Script Scan (`-sC`)

### Purpose

Runs Nmap's default set of safe NSE scripts against discovered services.

### Syntax

```bash
nmap -sC <target>
```

### Example

```bash
nmap -sC 192.168.1.15
```

### How It Works

The `-sC` option executes the scripts categorized as **default**, which are designed to gather useful information without causing disruption to the target system.

These scripts automatically detect service-specific information and perform basic enumeration.

### Typical Information Collected

- HTTP Titles
- SSH Host Keys
- SSL Certificates
- SMB Information
- DNS Details
- FTP Anonymous Login Checks

### When to Use

- Initial service enumeration
- Vulnerability assessments
- Internal security reviews
- Authorized penetration testing

### Advantages

- Safe for most environments.
- Provides valuable reconnaissance information.
- Automatically selects appropriate scripts.

### Limitations

- Does not execute every available NSE script.
- Results depend on detected services.

### Security Insight

The default script scan is often the next step after identifying open ports. It provides a balance between information gathering and minimizing unnecessary network traffic.

--- 
## Vulnerability Detection Scripts

### Purpose

Executes NSE scripts designed to identify known vulnerabilities affecting services running on the target.

### Syntax

```bash
nmap --script vuln <target>
```

### Example

```bash
nmap --script vuln 192.168.1.15
```

### How It Works

The `vuln` script category executes multiple vulnerability detection scripts based on the services identified during scanning.

The scripts compare service behavior against known vulnerability signatures and configuration weaknesses.

### Common Checks

- SMB Vulnerabilities
- SSL/TLS Weaknesses
- HTTP Misconfigurations
- FTP Security Issues
- DNS Weaknesses

### When to Use

- Vulnerability assessments
- Internal audits
- Patch verification
- Security reviews

### Advantages

- Automates basic vulnerability detection.
- Saves time during assessments.
- Integrates with standard Nmap workflows.

### Limitations

- Does not replace a dedicated vulnerability scanner.
- Findings should always be verified manually.

### Security Insight

NSE vulnerability scripts are excellent for identifying common issues, but they should be viewed as indicators rather than proof of exploitation. Always validate findings before drawing conclusions or reporting vulnerabilities.

---
## SMB Enumeration

### Purpose

Collects information about SMB services exposed by Windows systems.

### Syntax

```bash
nmap --script smb-enum-shares,smb-enum-users <target>
```

### Example

```bash
nmap --script smb-enum-shares,smb-enum-users 192.168.1.20
```

### Information Collected

- Shared folders
- User accounts
- Domain information
- SMB configuration
- Network shares

### When to Use

- Active Directory assessments
- Internal penetration tests
- Windows network enumeration

### Advantages

- Provides valuable Windows reconnaissance.
- Assists privilege escalation planning.

### Limitations

- Depends on SMB accessibility.
- Access controls may limit results.

### Security Insight

SMB enumeration often reveals valuable information about an organization's internal network. Proper SMB configuration and access restrictions help reduce unnecessary exposure.

---
## HTTP Enumeration

### Purpose

Uses NSE scripts to gather information about web servers and web applications.

### Syntax

```bash
nmap --script http-enum,http-title <target>
```

### Example

```bash
nmap --script http-enum,http-title 192.168.1.15
```

### Information Collected

- Website title
- Common directories
- Default pages
- Web technologies
- Server banners

### When to Use

- Web application reconnaissance
- Security assessments
- Asset inventory

### Advantages

- Fast web reconnaissance.
- Identifies exposed content.
- Helps prioritize manual testing.

### Limitations

- Results depend on available NSE scripts.
- Dynamic applications may require additional testing.

### Security Insight

HTTP enumeration often reveals administrative interfaces, exposed documentation, and default pages that can assist further security testing. Proper hardening and access controls reduce unnecessary information disclosure.

---
## SSH Enumeration

### Purpose

Collects information about SSH services, including supported authentication methods, host keys, encryption algorithms, and protocol configuration.

### Syntax

```bash
nmap --script ssh2-enum-algos,ssh-hostkey <target>
```

### Example

```bash
nmap --script ssh2-enum-algos,ssh-hostkey 192.168.1.15
```

### Information Collected

- SSH protocol version
- Supported key exchange algorithms
- Encryption ciphers
- Message authentication algorithms (MACs)
- Server host keys

### When to Use

- SSH security assessments
- Cryptographic reviews
- Hardening verification
- Asset identification

### Advantages

- Identifies weak cryptographic algorithms.
- Reveals SSH configuration details.
- Helps verify compliance with security policies.

### Limitations

- Requires the SSH service to be accessible.
- Does not test credentials or authenticate users.

### Security Insight

SSH enumeration helps identify outdated cryptographic algorithms and weak configurations. Organizations should disable deprecated algorithms and use modern key exchange methods to reduce exposure.

---
## FTP Enumeration

### Purpose

Enumerates FTP services to identify server configuration, supported features, and potential anonymous access.

### Syntax

```bash
nmap --script ftp-anon,ftp-syst <target>
```

### Example

```bash
nmap --script ftp-anon,ftp-syst 192.168.1.20
```

### Information Collected

- Anonymous login availability
- FTP server software
- Operating system information
- Supported FTP features

### When to Use

- Legacy system assessments
- File server reviews
- Internal penetration testing

### Advantages

- Quickly identifies insecure FTP configurations.
- Detects anonymous file access.

### Limitations

- Limited to accessible FTP services.
- Some servers restrict banner information.

### Security Insight

Anonymous FTP access remains a common misconfiguration. Even read-only anonymous access may expose sensitive files, configuration backups, or software packages that assist further reconnaissance.

---
## MySQL Enumeration

### Purpose

Collects information about MySQL database services, including version details and server configuration.

### Syntax

```bash
nmap --script mysql-info <target>
```

### Example

```bash
nmap --script mysql-info 192.168.1.25
```

### Information Collected

- MySQL version
- Protocol version
- Capabilities
- Authentication plugin
- Server status

### When to Use

- Database security assessments
- Infrastructure reviews
- Asset discovery

### Advantages

- Identifies exposed database services.
- Supports vulnerability assessment planning.

### Limitations

- Information depends on server configuration.
- Some details may require authentication.

### Security Insight

Publicly exposed database servers significantly increase organizational risk. MySQL services should be restricted to trusted hosts whenever possible and protected with strong authentication and network segmentation.

---
## DNS Enumeration

### Purpose

Enumerates DNS services to gather information about records, name servers, and zone configuration.

### Syntax

```bash
nmap --script dns-recursion,dns-service-discovery <target>
```

### Example

```bash
nmap --script dns-recursion,dns-service-discovery 192.168.1.53
```

### Information Collected

- DNS recursion status
- Name server information
- Available DNS services
- DNS configuration details

### When to Use

- Infrastructure assessments
- DNS security reviews
- External reconnaissance

### Security Insight

Open DNS recursion can be abused in amplification attacks. Proper DNS configuration helps reduce abuse and information disclosure.

---
## SNMP Enumeration

### Purpose

Identifies information exposed by SNMP-enabled devices such as routers, switches, printers, and servers.

### Syntax

```bash
nmap --script snmp-info <target>
```

### Example

```bash
nmap --script snmp-info 192.168.1.1
```

### Information Collected

- Device description
- System uptime
- Contact information
- Hostname
- Network interfaces

### When to Use

- Network device assessments
- Infrastructure inventories
- Security audits

### Security Insight

Default or weak SNMP community strings can expose valuable infrastructure information. Organizations should use SNMPv3 where possible and disable unnecessary public community strings.

---
## SMTP Enumeration

### Purpose

Enumerates SMTP servers to identify supported commands, server capabilities, and user account validation features.

### Syntax

```bash
nmap --script smtp-commands,smtp-enum-users <target>
```

### Example

```bash
nmap --script smtp-commands,smtp-enum-users 192.168.1.25
```

### Information Collected

- Supported SMTP commands
- User enumeration (if permitted)
- Mail server software
- SMTP capabilities

### When to Use

- Email infrastructure assessments
- Internal penetration testing
- Security audits

### Advantages

- Identifies insecure SMTP configurations.
- Detects exposed mail server information.

### Limitations

- Many modern mail servers disable user enumeration.
- Some commands require authentication.

### Security Insight

Improperly configured SMTP servers may allow attackers to enumerate valid email accounts or expose unnecessary server information. Disabling unnecessary SMTP commands and restricting user enumeration helps reduce this risk.

---
## LDAP Enumeration

### Purpose

Collects information from LDAP services, commonly used within Active Directory environments.

### Syntax

```bash
nmap --script ldap-rootdse <target>
```

### Example

```bash
nmap --script ldap-rootdse 192.168.1.30
```

### Information Collected

- Domain naming contexts
- Directory capabilities
- Supported LDAP versions
- Server information

### When to Use

- Active Directory assessments
- Enterprise security reviews
- Internal penetration testing

### Advantages

- Provides valuable domain information.
- Supports Active Directory reconnaissance.

### Limitations

- Anonymous queries may be restricted.
- Access controls affect returned information.

### Security Insight

Improper LDAP configuration can expose directory information useful for reconnaissance. Restrict anonymous access and apply the principle of least privilege.

---
## NFS Enumeration

### Purpose

Enumerates Network File System (NFS) exports available on Unix and Linux systems.

### Syntax

```bash
nmap --script nfs-showmount,nfs-ls <target>
```

### Example

```bash
nmap --script nfs-showmount,nfs-ls 192.168.1.30
```

### Information Collected

- Exported directories
- File permissions
- Accessible shares
- NFS configuration

### When to Use

- Linux server assessments
- Storage security reviews
- Internal penetration testing

### Advantages

- Identifies exposed shared directories.
- Helps assess file-sharing security.

### Limitations

- Depends on NFS accessibility.
- Export permissions affect results.

### Security Insight

Misconfigured NFS exports may expose sensitive files or allow unauthorized access. NFS shares should be limited to trusted hosts and configured with appropriate permissions.

---
## RPC Enumeration

### Purpose

Enumerates Remote Procedure Call (RPC) services to identify registered programs and available network services.

### Syntax

```bash
nmap --script rpcinfo <target>
```

### Example

```bash
nmap --script rpcinfo 192.168.1.40
```

### Information Collected

- Registered RPC programs
- Program numbers
- Supported versions
- Communication protocols

### When to Use

- Linux server assessments
- NFS troubleshooting
- Network service discovery

### Advantages

- Reveals services that may not be obvious from port scans alone.
- Supports deeper infrastructure analysis.

### Limitations

- Information depends on RPC service availability.
- Firewalls may restrict access.

### Security Insight

RPC enumeration can reveal services that expand the attack surface. Unnecessary RPC services should be disabled, and access should be limited through firewall rules.

---
## RDP Enumeration

### Purpose

Collects information from Remote Desktop Protocol (RDP) services running on Windows systems.

### Syntax

```bash
nmap --script rdp-enum-encryption,rdp-ntlm-info <target>
```

### Example

```bash
nmap --script rdp-enum-encryption,rdp-ntlm-info 192.168.1.50
```

### Information Collected

- Supported encryption methods
- NTLM information
- Windows build details
- Domain information

### When to Use

- Windows assessments
- Active Directory reviews
- Remote access security audits

### Advantages

- Identifies RDP security settings.
- Assists in evaluating remote access configurations.

### Limitations

- Requires accessible RDP service.
- Some information may be restricted.

### Security Insight

RDP should be protected with Network Level Authentication (NLA), strong authentication mechanisms, and restricted network access to minimize exposure.

---
## SSL/TLS Enumeration

### Purpose

Enumerates SSL/TLS configurations to identify supported protocols, cipher suites, and certificate information.

### Syntax

```bash
nmap --script ssl-cert,ssl-enum-ciphers <target>
```

### Example

```bash
nmap --script ssl-cert,ssl-enum-ciphers 192.168.1.15
```

### Information Collected

- SSL/TLS versions
- Supported cipher suites
- Certificate details
- Certificate validity
- Key exchange methods

### When to Use

- HTTPS security reviews
- Compliance assessments
- Cryptographic audits

### Advantages

- Detects weak protocols and ciphers.
- Verifies certificate configuration.
- Supports security compliance efforts.

### Limitations

- Limited to services using SSL/TLS.
- Some protocol features require manual verification.

### Security Insight

Organizations should disable deprecated protocols such as SSLv2, SSLv3, TLS 1.0, and TLS 1.1 where appropriate, and prioritize modern TLS versions and strong cipher suites to reduce exposure to known cryptographic weaknesses.

---
