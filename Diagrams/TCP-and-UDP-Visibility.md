# TCP and UDP Visibility

TCP and UDP use different transport protocols and therefore require different scanning approaches.

Nmap can assess both protocols to provide a broader view of the network attack surface.

## Diagram

```text
                     Scanner
                        |
                +-------+-------+
                |               |
                |               |
             TCP Scan        UDP Scan
                |               |
                |               |
                v               v
           TCP Ports        UDP Ports
                |               |
          +-----+-----+     +---+---+
          |           |     |       |
        Open        Closed Open   Open|Filtered
          |           |     |       |
          +-----+-----+     +---+---+
                |               |
                +-------+-------+
                        |
                  Attack Surface
```
---

## Explanation

### Step 1

The scanner performs a TCP scan to identify TCP services.

---

### Step 2

The scanner performs a UDP scan to identify UDP services.

UDP scanning can produce states such as:

**OPEN**

**OPEN|FILTERED**

**CLOSED**

---

### Step 3

The results from both scans are combined to provide a broader view of the target's exposed services.

---

## Why Is TCP/UDP Visibility Important?

A TCP-only scan does not provide complete visibility into UDP services.

For example, the assessment identified UDP services including:

- DNS
- NetBIOS
- RPC
- NFS

Combining TCP and UDP scanning therefore provides a more complete attack-surface view.

---

## Benefits

- Improves service visibility
- Identifies UDP services
- Supports attack-surface analysis
- Reduces blind spots
- Provides broader network assessment coverage
```
