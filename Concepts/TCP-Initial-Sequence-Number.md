# TCP Initial Sequence Number (ISN)

## Overview

Every TCP connection begins with an Initial Sequence Number (ISN).

The ISN is a randomly generated value used to track data transmitted during a TCP session.

Sequence numbers help ensure that packets arrive in the correct order and that lost packets can be detected and retransmitted.

---

## Why Does It Exist?

Imagine sending a 50 MB file.

The file is divided into thousands of TCP segments.

Each segment receives a sequence number.

This allows the receiving computer to know:

- Which packet arrived.
- Which packet is missing.
- Which packet should arrive next.

---

## Example

Client

```
ISN = 124500
```

Server

```
ISN = 891230
```

Connection

```
Client ---------------------> Server

SYN Seq=124500

<---------------------

SYN ACK Seq=891230 Ack=124501

--------------------->

ACK Seq=124501 Ack=891231
```

---

## Why Nmap Cares

Different operating systems generate sequence numbers differently.

For example

- Windows
- Linux
- Cisco IOS
- FreeBSD

all have different ISN generation algorithms.

Nmap analyzes these patterns during OS fingerprinting.

---

## Used In

- OS Detection
- TCP Fingerprinting
- Firewall Analysis
