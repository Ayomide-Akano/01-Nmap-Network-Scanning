# Host Discovery and Network Mapping

Host discovery is the first stage of the Nmap assessment process.

It determines which systems are active before detailed port and service scanning begins.

## Diagram

```text
Scanner
192.168.43.155
      |
      |
      |------ Host Discovery ------> Network
      |                              |
      |                              |
      |                       192.168.43.0/24
      |                              |
      |                 +------------+------------+
      |                 |                         |
      |                 v                         v
      |          192.168.43.1              192.168.43.56
      |             Gateway                Metasploitable 2
      |                                          |
      |<------------- Host Response -------------|
      |
      v
Active Host Identified
```

---

## Explanation

### Step 1

The scanner sends host discovery probes across the target network.

---

### Step 2

Active systems respond to the discovery probes.

In this assessment, the target was identified as:

**192.168.43.56**

---

### Step 3

The identified host can then be subjected to detailed TCP, UDP, service, OS, and NSE scanning.

---

## Why Is Host Discovery Important?

Host discovery prevents the assessment from immediately scanning every port on every possible address.

It establishes:

**Which hosts are active**

and

**Which hosts require further assessment**

---

## Benefits

- Identifies active hosts
- Establishes network visibility
- Reduces unnecessary scanning
- Provides the starting point for reconnaissance
- Supports network mapping
```
