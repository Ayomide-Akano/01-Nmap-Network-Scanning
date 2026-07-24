# IP Identification (IP ID)

## Overview

Every IPv4 packet contains an Identification field.

It is primarily used to reassemble fragmented packets.

---

## Example

Packet 1

ID = 4010

Packet 2

ID = 4011

Packet 3

ID = 4012

---

## Why Nmap Cares

Operating systems generate IP IDs differently.

Some

- Increment sequentially

Others

- Randomize values

These behaviors contribute to operating system fingerprinting.

---

## Used In

- OS Detection
- Fragmentation Analysis
