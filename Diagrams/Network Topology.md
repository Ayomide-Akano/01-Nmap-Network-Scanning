# Network Topology

The lab network consists of a scanning machine, the Metasploitable 2 target, and the network gateway.

The topology provides the environment used throughout the practical Nmap assessments.

## Diagram

```text
                         Lab Network
                     192.168.43.0/24
                              |
                              |
                       Network Gateway
                       192.168.43.1
                              |
              +---------------+---------------+
              |                               |
              |                               |
       Scanning Machine                 Metasploitable 2
       192.168.43.155                   192.168.43.56
            Linux                            Target
              |                               |
              |--------- Nmap Scans ----------|
```

**svg**

---

## Explanation

### Step 1

The scanning machine is connected to the `192.168.43.0/24` network.

---

### Step 2

The scanner identifies active hosts on the network.

The Metasploitable 2 machine responds as an active target at:

**192.168.43.56**

---

### Step 3

Nmap then performs TCP, UDP, service enumeration, OS detection, and NSE-based assessment against the target.

---

## Why Is The Topology Important?

The topology establishes the relationship between:

Scanner

↓

Network

↓

Target

This helps explain where packets originate and where they are being sent during the assessment.

---

## Benefits

- Provides network context
- Shows the scanner and target relationship
- Helps explain packet flow
- Supports practical Nmap assessment documentation
```
