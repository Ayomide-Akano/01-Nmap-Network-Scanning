# Scenario 01 — Host Discovery & Network Mapping

## Objective

Identify active hosts on a network, discover open ports, determine running services, identify operating systems, and perform safe service enumeration.

This playbook is intended for authorized security assessments and internal asset discovery.

---

# Assessment Workflow

```
Target Network
      │
      ▼
Host Discovery
      │
      ▼
Port Scanning
      │
      ▼
Service Detection
      │
      ▼
Operating System Detection
      │
      ▼
Default NSE Scripts
      │
      ▼
Document Findings
```

---

# Step 1 - Host Discovery

## Goal

Identify which hosts are online.

### Command

```bash
nmap -sn 192.168.1.0/24
```

### Expected Output

- Live hosts
- IP addresses
- MAC addresses (local network)

### Why This Step Matters

Scanning inactive hosts wastes time and increases unnecessary network traffic. Host discovery focuses subsequent assessment efforts on reachable systems.

---

# Step 2 - Port Scanning

## Goal

Determine which TCP ports are open.

### Command

```bash
nmap -sS 192.168.1.15
```

### Expected Output

- Open ports
- Closed ports
- Filtered ports

### Why This Step Matters

Open ports indicate services that may require further investigation.

---

# Step 3 - Service Version Detection

## Goal

Identify applications running on open ports.

### Command

```bash
nmap -sV 192.168.1.15
```

### Expected Output

- Service names
- Software versions
- Vendor information

### Why This Step Matters

Software versions help identify outdated or unsupported applications that may require patching.

---

# Step 4 - Operating System Detection

## Goal

Estimate the operating system running on the target.

### Command

```bash
nmap -O 192.168.1.15
```

### Expected Output

- Operating system family
- Kernel version estimate
- Device type

### Why This Step Matters

Operating system identification supports vulnerability assessment and helps determine appropriate hardening guidance.

---

# Step 5 - Safe Enumeration

## Goal

Collect additional service information using default NSE scripts.

### Command

```bash
nmap -sC 192.168.1.15
```

### Expected Output

- HTTP titles
- SSL certificates
- SSH host keys
- SMB information
- DNS information

### Why This Step Matters

Enumeration provides additional context without performing intrusive testing.

---

# Step 6 - Document Findings

Record:

- Active hosts
- Open ports
- Running services
- Software versions
- Operating system
- Interesting observations
- Potential security concerns

---

# Reporting Considerations

When documenting results:

- Include screenshots where appropriate.
- Record command output accurately.
- Distinguish confirmed findings from assumptions.
- Avoid making vulnerability claims without supporting evidence.

---

# Lessons Learned

This playbook demonstrates a structured reconnaissance workflow that progresses from basic host discovery to service enumeration while minimizing unnecessary network traffic.

---

# Related Commands

- `-sn`
- `-sS`
- `-sV`
- `-O`
- `-sC`

---

# References

- RFC 793 (Transmission Control Protocol)
- Nmap Reference Guide
