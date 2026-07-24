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

---
