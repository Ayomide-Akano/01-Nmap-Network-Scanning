# TCP Reset (RST)

## Overview

A TCP Reset (RST) is a TCP packet used to immediately terminate or reject a TCP connection.

Unlike the normal TCP connection process, an RST indicates that the connection should not continue.

For example, when a host receives a TCP SYN packet for a closed port, it may respond with:

```text
SYN →
← RST/ACK
```

This response tells the sender that the requested TCP connection is not available.

---

## Example

A scanner sends a SYN packet:

```text
Scanner
   |
   | SYN
   ↓
Target: TCP/23
   |
   | RST/ACK
   ↓
Scanner
```

The RST response indicates that the target is rejecting the connection attempt.

For a typical SYN scan:

```text
SYN → Open Port
← SYN/ACK
```

```text
SYN → Closed Port
← RST/ACK
```

---

## Why Nmap Cares

Nmap uses TCP responses to determine the state of ports.

A reset response is especially important during TCP scanning because it commonly indicates that a port is closed.

This allows Nmap to distinguish between different responses such as:

- `open`
- `closed`
- `filtered`

The exact interpretation depends on the scan type and the response received.

TCP Reset behavior is therefore an important part of understanding how Nmap determines port states.

---

## Used In

- TCP SYN Scanning
- TCP Connect Scanning
- ACK Scanning
- FIN Scanning
- NULL Scanning
- Xmas Scanning
- Port State Detection
- Firewall and Filtering Analysis
```

