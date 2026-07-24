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
