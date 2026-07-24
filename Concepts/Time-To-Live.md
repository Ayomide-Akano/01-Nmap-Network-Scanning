# Time To Live (TTL)

## Overview

Time To Live (TTL) limits how long an IP packet can travel across a network.

Each router decreases the TTL value by one.

If TTL reaches zero, the packet is discarded.

---

## Why Does TTL Exist?

Without TTL,

packets caught in routing loops would travel forever.

TTL prevents endless circulation.

---

## Example

Computer

TTL = 64

↓

Router 1

TTL = 63

↓

Router 2

TTL = 62

↓

Router 3

TTL = 61

---

## Default TTL Values

| Operating System | Default TTL |
|------------------|------------:|
| Linux | 64 |
| Windows | 128 |
| Cisco IOS | 255 |

---

## Why Nmap Cares

TTL helps estimate

- Network distance
- Possible operating system
- Routing behavior

Although TTL alone cannot identify an OS, it contributes to the overall fingerprint used by Nmap.

---

## Used In

- OS Detection
- Traceroute
- Network Analysis
