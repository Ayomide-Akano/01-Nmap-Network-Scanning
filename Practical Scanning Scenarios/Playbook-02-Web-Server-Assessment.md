# Playbook 02 - Web Server Assessment

## Objective

Assess a web server to identify exposed services, web technologies, SSL/TLS configuration, and publicly accessible resources.

This playbook is intended for authorized security assessments and asset inventory.

---

# Assessment Workflow

```
Target Web Server
        │
        ▼
Port Discovery
        │
        ▼
Service Detection
        │
        ▼
HTTP Enumeration
        │
        ▼
SSL/TLS Enumeration
        │
        ▼
Default NSE Scripts
        │
        ▼
Document Findings
```

---

# Step 1 - Port Discovery

## Goal

Identify web-related services.

### Command

```bash
nmap -p 80,443,8080,8443 <target>
```

### Expected Output

- HTTP
- HTTPS
- Alternate HTTP services

### Why This Step Matters

Web servers often expose multiple web services running on different ports.

---

# Step 2 - Service Detection

## Goal

Determine the software and version running on each web service.

### Command

```bash
nmap -sV -p 80,443 <target>
```

### Expected Output

- Apache
- Nginx
- IIS
- Caddy
- Lighttpd

Software version information

### Why This Step Matters

Knowing the server software helps determine patch levels and potential compatibility considerations.

---

# Step 3 - HTTP Enumeration

## Goal

Collect publicly available information exposed by the web server.

### Command

```bash
nmap --script http-title,http-enum <target>
```

### Expected Output

- Website title
- Common directories
- Default pages
- Server banners

### Why This Step Matters

Publicly exposed information can assist asset inventory and identify unnecessary information disclosure.

---

# Step 4 - SSL/TLS Enumeration

## Goal

Review the TLS configuration.

### Command

```bash
nmap --script ssl-cert,ssl-enum-ciphers -p443 <target>
```

### Expected Output

- Supported TLS versions
- Cipher suites
- Certificate details
- Certificate expiration

### Why This Step Matters

Strong TLS configuration is an important component of secure web services.

---

# Step 5 - Default NSE Scripts

## Goal

Perform additional safe enumeration.

### Command

```bash
nmap -sC -p80,443 <target>
```

### Expected Output

Additional HTTP information

SSL information

Server configuration details

---

# Step 6 - Document Findings

Record

- Open ports
- Server software
- Web technologies
- TLS versions
- Certificate information
- Interesting HTTP responses
- Security observations

---

# Reporting Considerations

Include

- Screenshots
- Commands used
- Server software versions
- Certificate information
- Recommendations where appropriate

Avoid assuming a vulnerability exists based solely on banners or version information. Validate findings before reporting them as security issues.

---

# Lessons Learned

This playbook demonstrates a structured approach to assessing web-facing services using safe reconnaissance and enumeration techniques.

---

# Related Commands

- `-p`
- `-sV`
- `http-title`
- `http-enum`
- `ssl-cert`
- `ssl-enum-ciphers`
- `-sC`

---

# References

- Nmap Reference Guide
- OWASP Web Security Testing Guide
