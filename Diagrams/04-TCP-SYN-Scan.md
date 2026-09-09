# TCP SYN Scan (Half-Open Scan)

The TCP SYN Scan is one of Nmap's most popular scanning techniques.

Unlike a normal TCP connection, the handshake is intentionally never completed.

## Diagram

```text
Scanner                               Target
   |                                     |
   |----------- SYN -------------------->|
   |                                     |
   |<-------- SYN / ACK -----------------|
   |                                     |
   |----------- RST --------------------->|
   |                                     |
 Connection Immediately Terminated
```

---

## Explanation

### Step 1

The scanner sends a SYN packet.

---

### Step 2

If the target replies with SYN/ACK,

the port is considered

**OPEN**

---

### Step 3

Instead of sending ACK,

Nmap sends RST.

This immediately closes the connection.

No TCP session is established.

---

## Why Is It Called Half-Open?

A normal TCP connection completes:

SYN

↓

SYN/ACK

↓

ACK

The SYN Scan stops here:

SYN

↓

SYN/ACK

↓

RST

Only half of the TCP handshake is completed.

---

## Benefits

- Fast
- Efficient
- Generates less application-level logging
- Ideal for reconnaissance
