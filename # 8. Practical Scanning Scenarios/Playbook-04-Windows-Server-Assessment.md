# Playbook 04 - Windows Server Assessment

## Objective

Assess a Windows server to identify exposed services, enumerate Windows-specific protocols, collect Active Directory-related information (where applicable), and document findings for security review.

This playbook is intended for authorized security assessments and infrastructure reviews.

---

# Assessment Workflow

```
Target Windows Server
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
SMB Enumeration
         │
         ▼
RDP Enumeration
         │
         ▼
LDAP Enumeration (If Domain Joined)
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

Confirm that the Windows server is online.

### Command

```bash
nmap -sn <target>
```

### Expected Output

- Live host
- Response latency

### Why This Step Matters

Verifying connectivity ensures subsequent scans are performed only against reachable systems.

---

# Step 2 – TCP SYN Scan

## Goal

Identify exposed TCP services.

### Command

```bash
nmap -sS <target>
```

### Expected Output

Common Windows ports such as:

- 135 (RPC)
- 139 (NetBIOS)
- 445 (SMB)
- 3389 (RDP)
- 389 (LDAP)
- 636 (LDAPS)

---

# Step 3 – Service Version Detection

## Goal

Identify services and software versions.

### Command

```bash
nmap -sV <target>
```

### Expected Output

- Microsoft IIS
- SMB version
- RDP service
- LDAP service
- DNS service (if applicable)

---

# Step 4 – Operating System Detection

## Goal

Estimate the Windows operating system.

### Command

```bash
nmap -O <target>
```

### Expected Output

- Windows family
- Version estimate
- Device type

---

# Step 5 – SMB Enumeration

## Goal

Gather information exposed by SMB.

### Command

```bash
nmap --script smb-enum-shares,smb-enum-users <target>
```

### Collect

- Shared folders
- User accounts
- SMB configuration
- Domain information

---

# Step 6 – RDP Enumeration

## Goal

Review Remote Desktop configuration.

### Command

```bash
nmap --script rdp-enum-encryption,rdp-ntlm-info <target>
```

### Collect

- Computer name
- Domain name
- Encryption level
- Authentication information

---

# Step 7 – LDAP Enumeration (If Present)

## Goal

Collect directory information.

### Command

```bash
nmap --script ldap-rootdse <target>
```

### Collect

- Naming contexts
- LDAP capabilities
- Directory information

---

# Step 8 – Safe NSE Enumeration

### Command

```bash
nmap -sC <target>
```

### Collect

Additional service information safely exposed by default NSE scripts.

---

# Step 9 – Document Findings

Record

- Open ports
- Services
- Windows version estimate
- SMB information
- LDAP information
- RDP configuration
- Security observations

---

# Reporting Considerations

- Verify findings before reporting.
- Avoid assumptions based solely on banners.
- Clearly distinguish observed information from inferred information.
- Include screenshots and command outputs where appropriate.

---

# Lessons Learned

Windows servers commonly expose management and authentication services that provide valuable insight into enterprise environments. A structured enumeration process helps identify these services consistently.

---

# Professional Notes

This playbook is suitable for standalone Windows servers as well as systems joined to an Active Directory domain. Information gathered here often supports broader infrastructure assessments and security reviews.

---

# Related Commands

- `-sn`
- `-sS`
- `-sV`
- `-O`
- `-sC`
- `smb-enum-shares`
- `smb-enum-users`
- `ldap-rootdse`
- `rdp-enum-encryption`

---

# References

- Microsoft Windows Security Documentation
- Nmap Reference Guide
- CIS Microsoft Windows Benchmarks
