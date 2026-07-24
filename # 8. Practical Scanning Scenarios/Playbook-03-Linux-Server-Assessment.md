# Playbook 03 - Linux Server Assessment

## Objective

Assess a Linux server to identify exposed services, verify system characteristics, enumerate network services, and document security-relevant observations.

This playbook is intended for authorized security assessments and infrastructure reviews.

---

# Assessment Workflow

```
Target Linux Server
        │
        ▼
Host Discovery
        │
        ▼
TCP SYN Scan
        │
        ▼
Service Version Detection
        │
        ▼
Operating System Detection
        │
        ▼
SSH Enumeration
        │
        ▼
FTP Enumeration (If Present)
        │
        ▼
NFS Enumeration (If Present)
        │
        ▼
RPC Enumeration
        │
        ▼
Safe NSE Enumeration
        │
        ▼
Document Findings
```

---

# Step 1 – Verify Host Availability

## Goal

Confirm that the server is reachable.

### Command

```bash
nmap -sn <target>
```

### Expected Output

- Live host
- Response latency

### Why This Step Matters

Verifying host availability prevents unnecessary scanning of unreachable systems.

---

# Step 2 – TCP SYN Scan

## Goal

Identify exposed TCP services.

### Command

```bash
nmap -sS <target>
```

### Expected Output

- Open ports
- Closed ports
- Filtered ports

---

# Step 3 – Service Version Detection

## Goal

Identify running applications.

### Command

```bash
nmap -sV <target>
```

### Expected Output

- SSH
- Apache/Nginx
- FTP
- RPC
- NFS
- Database services

---

# Step 4 – Operating System Detection

## Goal

Estimate the operating system.

### Command

```bash
nmap -O <target>
```

### Expected Output

- Linux distribution family
- Kernel estimate
- Device type

---

# Step 5 – SSH Enumeration

## Goal

Review SSH configuration.

### Command

```bash
nmap --script ssh2-enum-algos,ssh-hostkey <target>
```

### Collect

- Host keys
- Supported ciphers
- Key exchange algorithms
- MAC algorithms

---

# Step 6 – FTP Enumeration (If Present)

### Command

```bash
nmap --script ftp-anon,ftp-syst <target>
```

### Collect

- Anonymous login status
- FTP software
- Operating system information

---

# Step 7 – NFS Enumeration (If Present)

### Command

```bash
nmap --script nfs-showmount,nfs-ls <target>
```

### Collect

- Exported directories
- File permissions
- Shared resources

---

# Step 8 – RPC Enumeration

### Command

```bash
nmap --script rpcinfo <target>
```

### Collect

- RPC programs
- Versions
- Registered services

---

# Step 9 – Safe NSE Enumeration

### Command

```bash
nmap -sC <target>
```

### Collect

Additional information exposed by detected services.

---

# Step 10 – Document Findings

Record

- Open services
- Versions
- SSH configuration
- NFS exports
- FTP configuration
- Operating system
- Interesting observations

---

# Reporting Considerations

Document only verified findings.

Do not assume vulnerabilities exist simply because software versions appear outdated.

Include screenshots where appropriate.

---

# Lessons Learned

Linux servers frequently expose infrastructure services beyond SSH. A structured workflow helps ensure important services such as NFS and RPC are not overlooked during assessments.

---

# Professional Notes

This playbook provides a repeatable approach for Linux server assessments. The information collected can support asset inventories, hardening reviews, and vulnerability assessments, and should be combined with additional validation before making security recommendations.

---

# Related Commands

- `-sn`
- `-sS`
- `-sV`
- `-O`
- `-sC`
- `ssh2-enum-algos`
- `ftp-anon`
- `nfs-showmount`
- `rpcinfo`

---

# References

- Nmap Reference Guide
- CIS Benchmarks for Linux

# Professional Notes

This playbook provides a repeatable methodology for assessing web servers using Nmap. It emphasizes safe reconnaissance, accurate documentation, and validation of findings before drawing conclusions.

In professional engagements, the results of this playbook are often combined with additional tools such as web proxies, vulnerability scanners, or manual testing to build a more complete picture of the target environment.
