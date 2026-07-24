# Playbook 06 - Database Server Assessment

## Objective

Identify exposed database services, collect version and configuration information, and document observations that support security reviews and infrastructure inventories.

This playbook is intended for authorized security assessments and database infrastructure reviews.

---

# Assessment Workflow

```
Target Database Server
         │
         ▼
Host Discovery
         │
         ▼
Port Discovery
         │
         ▼
Service Detection
         │
         ▼
Database Enumeration
         │
         ▼
SSL/TLS Review (If Applicable)
         │
         ▼
Safe NSE Enumeration
         │
         ▼
Document Findings
```

---

# Common Database Ports

| Database | Port |
|-----------|------|
| MySQL | 3306 |
| PostgreSQL | 5432 |
| Microsoft SQL Server | 1433 |
| Oracle | 1521 |
| MongoDB | 27017 |
| Redis | 6379 |

---

# Step 1 – Verify Host Availability

### Command

```bash
nmap -sn <target>
```

---

# Step 2 – Discover Database Services

### Command

```bash
nmap -sS -p3306,5432,1433,1521,27017,6379 <target>
```

### Collect

- Open database ports
- Filtered ports
- Closed ports

---

# Step 3 – Service Version Detection

### Command

```bash
nmap -sV <target>
```

### Collect

- Database software
- Version
- Vendor

---

# Step 4 – Database Enumeration

### MySQL

```bash
nmap --script mysql-info <target>
```

### MSSQL

```bash
nmap --script ms-sql-info <target>
```

### PostgreSQL

```bash
nmap --script pgsql-brute <target>
```

*(Only use authentication-related scripts in environments where they are explicitly authorized and appropriate.)*

### Collect

- Database version
- Authentication methods
- Service information

---

# Step 5 – SSL/TLS Review

```bash
nmap --script ssl-cert,ssl-enum-ciphers <target>
```

Collect

- TLS versions
- Certificates
- Cipher suites

---

# Step 6 – Document Findings

Record

- Database software
- Version
- Open ports
- Encryption configuration
- Observations

---

# Reporting Considerations

Document confirmed observations.

Do not attempt authentication or access beyond the agreed assessment scope.

---

# Professional Notes

Database servers often contain highly sensitive information. Security assessments should prioritize accurate identification, secure communication settings, and adherence to the rules of engagement.

---

# References

- Nmap Reference Guide
- CIS Database Benchmarks
