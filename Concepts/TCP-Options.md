# TCP Options

## Overview

TCP Options extend the functionality of the TCP protocol.

Examples include:

- MSS
- Window Scaling
- SACK
- Timestamp

---

## Common Options

| Option | Purpose |
|---------|----------|
| MSS | Maximum Segment Size |
| Window Scale | Supports larger windows |
| SACK | Selective Acknowledgment |
| Timestamp | RTT measurement |

---

## Why Nmap Cares

Different operating systems advertise TCP options in different orders.

The

order,

presence,

and values

are all useful fingerprints.

---

## Used In

- OS Detection
- Packet Analysis
