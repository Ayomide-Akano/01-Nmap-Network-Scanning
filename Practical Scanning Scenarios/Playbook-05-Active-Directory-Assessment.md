# Playbook 05 - Active Directory Assessment

## Objective

Identify Active Directory infrastructure components, enumerate exposed directory services, and collect information that supports asset inventory and security reviews.

This playbook is intended for authorized security assessments and enterprise infrastructure documentation.

---

# Assessment Workflow

```
Target Enterprise Network
         │
         ▼
Host Discovery
         │
         ▼
Identify Domain Controllers
         │
         ▼
DNS Enumeration
         │
         ▼
LDAP Enumeration
         │
         ▼
SMB Enumeration
         │
         ▼
Kerberos Detection
         │
         ▼
Global Catalog Detection
         │
         ▼
RDP Enumeration
         │
         ▼
Safe NSE Enumeration
         │
         ▼
Document Findings
```

---

# Active Directory Ports

| Service | Port |
|----------|------|
| DNS | 53 |
| Kerberos | 88 |
| LDAP | 389 |
| SMB | 445 |
| LDAPS | 636 |
| Global Catalog | 3268 |
| Global Catalog SSL | 3269 |
| RDP | 3389 |

---

# Step 1 – Discover Live Hosts

## Goal

Identify active systems within the target subnet.

### Command

```bash
nmap -sn 192.168.1.0/24
```

### Collect

- Active IP addresses
- Hostnames (where available)
- MAC addresses (local subnet)

---

# Step 2 – Identify Domain Controllers

## Goal

Locate systems exposing common Active Directory services.

### Command

```bash
nmap -sS -p53,88,389,445,636,3268,3269,3389 <target>
```

### Expected Indicators

- DNS
- Kerberos
- LDAP
- SMB
- Global Catalog

Servers exposing several of these services are often Domain Controllers.

---

# Step 3 – Service Version Detection

### Command

```bash
nmap -sV <target>
```

### Collect

- Microsoft DNS
- LDAP versions
- SMB versions
- Kerberos service
- IIS (if installed)

---

# Step 4 – LDAP Enumeration

## Goal

Collect directory information.

### Command

```bash
nmap --script ldap-rootdse <target>
```

### Collect

- Naming Contexts
- Supported LDAP Versions
- Directory Capabilities
- RootDSE Information

---

# Step 5 – SMB Enumeration

### Command

```bash
nmap --script smb-enum-shares,smb-enum-users <target>
```

### Collect

- Shares
- User Accounts
- Domain Information
- SMB Configuration

---

# Step 6 – DNS Enumeration

### Command

```bash
nmap --script dns-service-discovery,dns-recursion <target>
```

### Collect

- DNS Services
- Recursion Status
- Server Information

---

# Step 7 – RDP Enumeration

### Command

```bash
nmap --script rdp-enum-encryption,rdp-ntlm-info <target>
```

### Collect

- Computer Name
- Domain Name
- Encryption Settings
- Authentication Information

---

# Step 8 – Safe NSE Enumeration

### Command

```bash
nmap -sC <target>
```

### Collect

Additional information exposed by default scripts.

---

# Step 9 – Document Findings

Record

- Domain Controller IPs
- Hostnames
- Open Services
- LDAP Information
- SMB Information
- DNS Configuration
- RDP Configuration
- Observations

---

# Reporting Considerations

Document only confirmed observations.

Do not infer vulnerabilities solely from service versions or banners.

Differentiate observed configurations from assumptions.

---

# Lessons Learned

Active Directory environments expose several interconnected services that together support authentication, directory access, file sharing, and remote administration. Understanding how these services relate to one another improves enterprise asset discovery and security assessments.

---

# Professional Notes

Nmap is effective for identifying Active Directory-related services and gathering initial configuration details. Comprehensive Active Directory assessments typically combine these findings with directory administration tools and other authorized security assessment techniques.

---

# Related Commands

- `-sn`
- `-sS`
- `-sV`
- `ldap-rootdse`
- `smb-enum-users`
- `smb-enum-shares`
- `dns-recursion`
- `rdp-enum-encryption`
- `-sC`

---

# References

- Microsoft Active Directory Documentation
- Nmap Reference Guide
- Microsoft Security Baselines
