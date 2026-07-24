# TCP Window Size

## Overview

TCP Window Size determines how much data a receiver is willing to accept before sending an acknowledgment.

It is a flow-control mechanism that prevents a fast sender from overwhelming a slower receiver.

---

## Example

Window Size = 4096 Bytes

Sender

```
Send 4096 Bytes

↓

Wait

↓

Receive ACK

↓

Send Next 4096
```

---

## Why Nmap Cares

Many operating systems use characteristic default window sizes.

For example,

Linux,

Windows,

Cisco,

and BSD systems often advertise different TCP window sizes.

These values help Nmap build an operating system fingerprint.

---

## Used In

- OS Detection
- Traffic Analysis
- TCP Fingerprinting
