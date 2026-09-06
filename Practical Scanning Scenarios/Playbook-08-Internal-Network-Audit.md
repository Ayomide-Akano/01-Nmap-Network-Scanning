# Playbook 08 - Internal Network Audit

## Objective

Perform a structured internal network assessment to identify active assets, inventory exposed services, validate network segmentation, and document observations that support security reviews and operational improvements.

This playbook is intended for authorized internal security assessments.

---

# Assessment Workflow

```
Internal Network
        │
        ▼
Host Discovery
        │
        ▼
Network Inventory
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
Service Enumeration
        │
        ▼
Identify Critical Assets
        │
        ▼
Validate Network Segmentation
        │
        ▼
Document Findings
```

---

# Step 1 – Discover Active Hosts

## Goal

Identify every reachable device within the assessment scope.

### Command

```bash
nmap -sn 192.168.1.0/24
```

### Record

- IP Address
- Hostname
- MAC Address
- Vendor

---

# Step 2 – Inventory Open Services

### Command

```bash
nmap -sS 192.168.1.0/24
```

### Record

- Open Ports
- Closed Ports
- Filtered Ports

---

# Step 3 – Service Detection

### Command

```bash
nmap -sV 192.168.1.0/24
```

### Record

- Service Name
- Version
- Vendor

---

# Step 4 – Operating System Detection

### Command

```bash
nmap -O 192.168.1.0/24
```

### Record

- Windows
- Linux
- Network Devices
- Printers
- IoT Devices

---

# Step 5 – Safe Enumeration

### Command

```bash
nmap -sC 192.168.1.0/24
```

Collect additional service information where appropriate.

---

# Step 6 – Identify Critical Systems

Examples include:

- Domain Controllers
- File Servers
- Database Servers
- Backup Servers
- Hypervisors
- Firewalls
- Network Switches
- Wireless Controllers

---

# Step 7 – Validate Network Segmentation

Review whether systems are placed in appropriate network segments based on organizational design and security requirements.

Examples include:

- User VLANs
- Server VLANs
- Management Networks
- Guest Networks
- DMZ
- Backup Networks

---

# Step 8 – Document Findings

Record

- Total Hosts
- Critical Assets
- Unknown Devices
- Unsupported Systems
- Legacy Protocols
- Interesting Services
- Security Observations

---

# Reporting Checklist

- [ ] Asset inventory completed
- [ ] Open services documented
- [ ] Versions documented
- [ ] Critical assets identified
- [ ] Segmentation observations recorded
- [ ] Screenshots collected
- [ ] Findings validated
- [ ] Recommendations prepared

---

# Lessons Learned

Internal assessments provide visibility into an organization's assets and services. Maintaining an accurate inventory supports risk management, patching, and incident response.

---

# Professional Notes

Internal audits should be coordinated with system owners and conducted according to approved change management and assessment procedures. Findings from network discovery are often combined with configuration reviews and vulnerability assessments for a more complete security picture.

---

# References

- NIST Cybersecurity Framework (CSF)
- CIS Controls
- Nmap Reference Guide
