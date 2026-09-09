# ICMP Messages

## Overview

Internet Control Message Protocol (ICMP) is used by network devices and hosts to communicate network conditions and error information.

ICMP does not transport application data like TCP or UDP.

Instead, it provides information about conditions such as:

- Host reachability
- Network errors
- Unreachable destinations
- Timeouts
- Routing-related conditions

---

## Example

A host may respond to an ICMP Echo Request with an ICMP Echo Reply:

```text
Scanner
   |
   | ICMP Echo Request
   ↓
Target
   |
   | ICMP Echo Reply
   ↓
Scanner
```

A network device may also return an ICMP Destination Unreachable message when traffic cannot reach its destination.

For example:

```text
Scanner
   |
   | Probe
   ↓
Firewall / Router
   |
   | ICMP Destination Unreachable
   ↓
Scanner
```

The specific ICMP message can provide information about how the network or target is handling the traffic.

---

## Why Nmap Cares

Nmap can use ICMP responses as part of host discovery and network assessment.

ICMP responses can help Nmap determine whether a host is reachable and can also provide information about filtering behavior.

For example, an ICMP response may indicate that traffic is being rejected or that a destination is unreachable.

However, the absence of an ICMP response does not automatically mean that a host is offline.

Firewalls and network devices may block or filter ICMP traffic.

Therefore, Nmap can use multiple discovery and scanning techniques rather than relying on ICMP alone.

---

## Used In

- Host Discovery
- Network Mapping
- Filtering Analysis
- Firewall Analysis
- Network Troubleshooting
- ICMP Echo Discovery
- ICMP-based Reachability Testing
```


That keeps the portfolio consistent.
