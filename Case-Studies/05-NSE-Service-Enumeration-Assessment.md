# Case Study 05 — NSE Service Enumeration Assessment

## Executive Summary

This case study documents a targeted Nmap Scripting Engine (NSE) assessment of a Metasploitable 2 laboratory host at `192.168.43.56`.

The assessment was designed to demonstrate a service-driven NSE methodology rather than indiscriminately executing large numbers of scripts.

Previously identified services were used to determine which NSE scripts were relevant:

```text
Service discovered
        ↓
Relevant NSE scripts selected
        ↓
Additional information gathered
        ↓
Security significance interpreted
        ↓
Finding documented
```

Five service categories were selected for focused enumeration:

- FTP
- SMB
- HTTP
- SMTP
- MySQL

The assessment identified several significant security weaknesses, including anonymous FTP access, plaintext FTP communication, SMBv1 support, disabled SMB message signing, anonymous read/write access to selected SMB shares, exposure of legacy web technologies, and exposure of a legacy MySQL service.

The assessment also demonstrated that NSE vulnerability scripts do not necessarily produce a vulnerability result for every service. Several scripts returned negative, incomplete, or unsupported results, reinforcing the importance of interpreting NSE output in the context of the broader assessment rather than treating individual script results as definitive security conclusions.

> **Assessment Environment:** Authorized laboratory environment using the intentionally vulnerable Metasploitable 2 system.

---

# 1. Assessment Overview

## 1.1 Target

| Item | Details |
|---|---|
| Target | Metasploitable 2 |
| IP Address | `192.168.43.56` |
| Scanner | Linux system |
| Scanner IP | `192.168.43.155` |
| Nmap Version | 7.99 |
| Environment | Virtualized laboratory |
| Assessment Type | Authorized security assessment |

---

## 1.2 Assessment Objective

The objective was to demonstrate the practical use of NSE for targeted service enumeration.

The assessment focused on answering the following questions:

1. What additional information can NSE obtain from previously discovered services?
2. Can service-specific scripts reveal configuration weaknesses?
3. Can NSE identify additional attack-surface information?
4. What security significance can be derived from the observed results?
5. What are the limitations of relying on individual NSE scripts?

---

# 2. Methodology

The assessment followed a service-driven methodology.

Rather than attempting to execute every NSE script category available in Nmap, services were selected based on the attack surface identified during previous scanning exercises.

The workflow was:

```text
Previous Port and Service Discovery
              ↓
       Service Selection
              ↓
     NSE Script Selection
              ↓
      Targeted Enumeration
              ↓
      Evidence Collection
              ↓
 Security Significance Analysis
              ↓
       Security Findings
```

The selected services were:

| Service | Port | Reason for Selection |
|---|---:|---|
| FTP | 21 | FTP service previously identified |
| SMTP | 25 | SMTP service previously identified |
| HTTP | 80 | Web service previously identified |
| SMB | 139/445 | Samba services previously identified |
| MySQL | 3306 | Database service previously identified |

---

# 3. Baseline Service Confirmation

Before performing targeted NSE enumeration, a service/version scan was performed.

### Command

```bash
sudo nmap -sS -sV -p 21,25,80,139,445,3306 192.168.43.56
```

### Observed Services

| Port | Service | Version |
|---:|---|---|
| 21/tcp | FTP | vsftpd 2.3.4 |
| 25/tcp | SMTP | Postfix smtpd |
| 80/tcp | HTTP | Apache httpd 2.2.8 (Ubuntu) DAV/2 |
| 139/tcp | NetBIOS/SMB | Samba 3.X–4.X |
| 445/tcp | SMB | Samba 3.X–4.X |
| 3306/tcp | MySQL | MySQL 5.0.51a-3ubuntu5 |

The host was identified as:

```text
metasploitable.localdomain
```

with the operating system classified as Unix.

### Assessment Significance

The baseline scan provided the information required to select service-specific NSE scripts.

This demonstrates an important principle:

> **NSE script selection should be driven by the services and attack surface identified during reconnaissance.**

---

# 4. Finding Summary

The following findings were identified during the assessment.

| ID | Finding | Severity |
|---|---|---|
| F-01 | Anonymous FTP Access | Medium |
| F-02 | Plaintext FTP Communication | Medium |
| F-03 | SMBv1 Supported | High |
| F-04 | SMB Message Signing Disabled | High |
| F-05 | Anonymous SMB Read/Write Access | High |
| F-06 | Web Application and Information Disclosure | Medium |
| F-07 | Legacy Database Service Exposed | High |
| F-08 | SMTP Capability and Legacy Security Exposure | Medium |

> **Severity Note:**  
> These severity ratings are assessment-level judgments based on the observed exposure and configuration within the laboratory environment. They are not formal CVSS scores.

---

# 5. Finding F-01 — Anonymous FTP Access

## Severity

**Medium**

## Description

The FTP service on port `21/tcp` permitted anonymous authentication.

### Evidence

```bash
sudo nmap -p 21 --script ftp-anon 192.168.43.56
```

The NSE script returned:

```text
ftp-anon: Anonymous FTP login allowed (FTP code 230)
```

The FTP service was identified as:

```text
vsftpd 2.3.4
```

## Security Impact

Anonymous FTP access allows users to connect without providing a normal authenticated account.

Depending on the server's directory and file permissions, this can result in unauthorized access to files or other FTP resources.

Even when anonymous access is intentionally configured, it should be restricted to a clearly defined public area and monitored appropriately.

## Recommendation

- Disable anonymous FTP access unless it is explicitly required.
- If anonymous access is required, restrict it to a dedicated directory.
- Apply appropriate read/write permissions.
- Monitor anonymous FTP activity.
- Prefer secure file-transfer mechanisms for sensitive data.

---

# 6. Finding F-02 — Plaintext FTP Communication

## Severity

**Medium**

## Description

The FTP server reported that both its control and data connections were transmitted in plaintext.

### Evidence

```bash
sudo nmap -p 21 --script ftp-syst 192.168.43.56
```

The NSE output reported:

```text
Control connection is plain text
Data connections will be plain text
```

The service was identified as:

```text
vsFTPd 2.3.4
```

## Security Impact

Plaintext FTP communication can expose credentials and transferred data to network interception.

This is particularly relevant when FTP traffic crosses networks that cannot be fully trusted.

## Recommendation

- Replace traditional FTP with SFTP or another encrypted file-transfer solution.
- If FTP must be retained, enforce an appropriately secured encrypted implementation.
- Avoid transmitting credentials or sensitive files over plaintext FTP.

---

# 7. Finding F-03 — SMBv1 Supported

## Severity

**High**

## Description

SMB enumeration identified support for the legacy SMBv1 protocol.

### Evidence

```bash
sudo nmap -p 139,445 --script smb-protocols 192.168.43.56
```

The script reported:

```text
NT LM 0.12 (SMBv1) [dangerous, but default]
```

## Security Impact

SMBv1 is an obsolete protocol and increases the attack surface of systems that continue to support it.

Its presence is particularly concerning on an externally or broadly accessible network service.

## Recommendation

- Disable SMBv1 where it is not required.
- Use modern SMB protocol versions.
- Restrict SMB access through network segmentation and firewall rules.
- Review legacy systems that may depend on SMBv1 before removal.

---

# 8. Finding F-04 — SMB Message Signing Disabled

## Severity

**High**

## Description

The SMB security configuration indicated that message signing was disabled.

### Evidence

```bash
sudo nmap -p 139,445 --script smb-security-mode 192.168.43.56
```

The NSE result reported:

```text
account_used: guest
authentication_level: user
challenge_response: supported
message_signing: disabled (dangerous, but default)
```

## Security Impact

SMB message signing provides integrity protection for SMB communications.

Disabling message signing reduces the protection available against certain attacks involving manipulation or interception of SMB traffic.

The risk is increased by the presence of other SMB weaknesses identified during this assessment.

## Recommendation

- Enable SMB message signing where appropriate.
- Review SMB security policies across affected systems.
- Restrict SMB exposure to trusted network segments.
- Monitor unusual SMB authentication and connection activity.

---

# 9. Finding F-05 — Anonymous SMB Read/Write Access

## Severity

**High**

## Description

SMB share enumeration identified anonymous read/write access to selected shares.

### Evidence

```bash
sudo nmap -p 139,445 --script smb-enum-shares 192.168.43.56
```

The scan identified:

```text
\\192.168.43.56\IPC$
Anonymous access: READ/WRITE
```

and:

```text
\\192.168.43.56\tmp
Anonymous access: READ/WRITE
```

Other discovered shares included:

```text
ADMIN$
opt
print$
```

with no anonymous access reported.

## Security Impact

Anonymous read/write access represents a significant access-control weakness.

An unauthenticated network user may be able to interact with shared resources without providing valid credentials.

The actual impact depends on the contents of the shares and the permissions enforced by the underlying system.

## Recommendation

- Disable anonymous SMB access unless explicitly required.
- Review permissions for all SMB shares.
- Apply least-privilege access controls.
- Remove unnecessary shares.
- Restrict SMB access to trusted hosts and networks.
- Monitor access to sensitive shared resources.

---

# 10. Finding F-06 — Web Application and Information Disclosure

## Severity

**Medium**

## Description

HTTP enumeration revealed server technology, application information, supported HTTP methods, and several potentially interesting web resources.

### Evidence — HTTP Headers

```bash
sudo nmap -p 80 --script http-headers 192.168.43.56
```

The server disclosed:

```text
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
```

### Evidence — HTTP Title

```bash
sudo nmap -p 80 --script http-title 192.168.43.56
```

Result:

```text
Metasploitable2 - Linux
```

### Evidence — HTTP Methods

```bash
sudo nmap -p 80 --script http-methods 192.168.43.56
```

Supported methods:

```text
GET
HEAD
POST
OPTIONS
```

### Evidence — HTTP Enumeration

```bash
sudo nmap -p 80 --script http-enum 192.168.43.56
```

The following resources were identified:

```text
/tikiwiki/
/test/
/phpinfo.php
/phpMyAdmin/
/doc/
/icons/
/index/
```

## Security Impact

The web service disclosed information about the underlying technology stack and exposed several application resources.

Of particular interest were:

- `phpinfo.php`
- phpMyAdmin
- Tikiwiki
- Test pages
- Potential directory listings

These resources can increase the amount of information available during reconnaissance and may expose additional attack surfaces.

The enumeration results alone do not prove that each discovered resource is vulnerable.

## Recommendation

- Remove unnecessary test and diagnostic files.
- Restrict administrative applications such as phpMyAdmin.
- Remove unnecessary applications and directories.
- Disable directory listing where it is not required.
- Avoid exposing detailed server information unnecessarily.
- Keep web server and application software supported and up to date.
- Review all discovered web resources for authentication and authorization controls.

---

# 11. Finding F-07 — Legacy Database Service Exposed

## Severity

**High**

## Description

The MySQL service was accessible on port `3306/tcp` and was identified as an older MySQL installation.

### Evidence

```bash
sudo nmap -p 3306 --script mysql-info 192.168.43.56
```

The service reported:

```text
Protocol: 10
Version: 5.0.51a-3ubuntu5
```

Additional protocol and capability information was returned.

The baseline scan also confirmed:

```text
3306/tcp open mysql MySQL 5.0.51a-3ubuntu5
```

## Security Impact

Direct network exposure of a database service increases the attack surface of a host.

The identified MySQL version is also legacy software within the context of this intentionally vulnerable laboratory system.

The NSE enumeration did not return a specific MySQL vulnerability result, and therefore the assessment does not claim that a particular MySQL vulnerability was successfully identified through NSE.

## Recommendation

- Restrict database access to authorized hosts and applications.
- Avoid exposing database services unnecessarily.
- Apply network segmentation and firewall controls.
- Upgrade unsupported database software.
- Require strong authentication.
- Encrypt database connections where appropriate.
- Monitor database authentication and connection activity.

---

# 12. Finding F-08 — SMTP Capability and Legacy Security Exposure

## Severity

**Medium**

## Description

SMTP enumeration revealed several server capabilities and additional legacy security information.

### Evidence — SMTP Commands

```bash
sudo nmap -p 25 --script smtp-commands 192.168.43.56
```

The server reported:

```text
PIPELINING
SIZE 10240000
VRFY
ETRN
STARTTLS
ENHANCEDSTATUSCODES
8BITMIME
DSN
```

The SMTP server was identified as:

```text
Postfix smtpd
```

### User Enumeration Result

```bash
sudo nmap -p 25 --script smtp-enum-users 192.168.43.56
```

The script returned:

```text
Method RCPT returned a unhandled status code.
```

No user list was obtained.

### Vulnerability Script Result

```bash
sudo nmap -p 25 --script "smtp-vuln*" 192.168.43.56
```

The result was:

```text
smtp-vuln-cve2010-4344:
The SMTP server is not Exim: NOT VULNERABLE
```

The vulnerability check therefore did not identify the tested Exim-specific vulnerability because the target was running Postfix.

## Additional Evidence from Default NSE

The default NSE scan also identified:

```text
SSLv2 supported
```

and an expired SMTP certificate:

```text
Not valid before: 2010-03-17T14:07:45
Not valid after: 2010-04-16T14:07:45
```

## Security Impact

The SMTP service exposed several capabilities that provide additional information about its configuration.

The support for legacy SSLv2 and the expired certificate represent security weaknesses in the laboratory configuration.

The SMTP user-enumeration attempt did not produce usable account information, so no successful user enumeration finding is claimed.

## Recommendation

- Disable obsolete SSL/TLS protocols.
- Replace expired certificates.
- Review SMTP commands and disable unnecessary functionality.
- Review whether `VRFY` and other information-disclosure functionality are required.
- Restrict SMTP access where appropriate.
- Use current supported cryptographic configurations.

---

# 13. NSE Results That Did Not Produce Vulnerability Findings

An important part of the assessment was recognizing that not every NSE script produces a vulnerability finding.

Several examples were observed.

## FTP

The FTP vulnerability scripts returned no specific vulnerability result.

```text
21/tcp open ftp
```

No vulnerability was reported.

---

## SMB

Two SMB vulnerability checks returned:

```text
smb-vuln-ms10-061: false
smb-vuln-ms10-054: false
```

One script returned:

```text
smb-vuln-regsvc-dos:
ERROR: Script execution failed
```

The error does not indicate that the target is vulnerable.

It indicates that the particular script did not complete successfully.

---

## SMTP

The SMTP vulnerability script reported:

```text
The SMTP server is not Exim: NOT VULNERABLE
```

This means the tested Exim vulnerability did not apply to the Postfix service.

---

## MySQL

The MySQL vulnerability scripts did not return a specific vulnerability finding.

The service remained identifiable as:

```text
MySQL 5.0.51a-3ubuntu5
```

---

## Assessment Interpretation

These results demonstrate why NSE output must be interpreted carefully.

A result such as:

```text
NOT VULNERABLE
```

may only mean:

> The specific vulnerability tested by that script was not identified under the conditions of the scan.

It does **not** necessarily mean:

> The entire service is secure.

Similarly, a script execution error does not indicate that the target is vulnerable.

---

# 14. Combined NSE Assessment

A combined targeted NSE scan was performed after the individual service checks.

### Command

```bash
sudo nmap -sV -p 21,25,80,139,445,3306 --script "ftp-anon,ftp-syst,smb-protocols,smb-security-mode,smb-os-discovery,http-title,http-headers,http-methods,smtp-commands,mysql-info" 192.168.43.56
```

The combined scan reconfirmed several major observations.

## FTP

```text
vsftpd 2.3.4
Anonymous FTP login allowed
Plaintext FTP control and data connections
```

## SMB

```text
Samba 3.0.20-Debian
SMBv1 supported
Guest account used
Message signing disabled
```

The host was also identified as:

```text
metasploitable
metasploitable.localdomain
```

## HTTP

```text
Apache/2.2.8 (Ubuntu) DAV/2
PHP/5.2.4-2ubuntu5.10
Metasploitable2 - Linux
```

## SMTP

```text
Postfix smtpd
PIPELINING
VRFY
ETRN
STARTTLS
```

## MySQL

```text
MySQL 5.0.51a-3ubuntu5
Protocol 10
```

### Assessment Significance

The combined scan demonstrated how targeted NSE scripts can gather useful information efficiently after the attack surface has already been mapped.

---

# 15. Default NSE Validation

An additional default NSE scan was performed against the selected ports.

### Command

```bash
sudo nmap -sV -sC -p 21,25,80,139,445,3306 192.168.43.56
```

The scan reconfirmed several findings from the targeted enumeration.

### FTP

```text
Anonymous FTP login allowed
vsFTPd 2.3.4
```

### SMTP

```text
SSLv2 supported
Expired certificate
Postfix smtpd
```

### SMB

```text
Guest account used
Message signing disabled
Samba 3.0.20-Debian
SMB2 protocol negotiation failed
```

### HTTP

```text
Apache httpd 2.2.8
Metasploitable2 - Linux
```

### MySQL

```text
MySQL 5.0.51a-3ubuntu5
Protocol 10
```

The default scan therefore provided additional confirmation of the targeted NSE assessment.

---

# 16. Evidence Matrix

| Finding | Evidence Source | Key Evidence |
|---|---|---|
| F-01 | `ftp-anon` | Anonymous FTP login allowed |
| F-02 | `ftp-syst` | FTP control/data connections plaintext |
| F-03 | `smb-protocols` | SMBv1 supported |
| F-04 | `smb-security-mode` | Message signing disabled |
| F-05 | `smb-enum-shares` | Anonymous READ/WRITE on IPC$ and tmp |
| F-06 | `http-headers`, `http-enum`, `http-title`, `http-methods` | Technology disclosure and web resources |
| F-07 | `mysql-info` | MySQL 5.0.51a exposed |
| F-08 | `smtp-commands`, `smtp-vuln*`, `-sC` | SMTP capabilities, SSLv2 and expired certificate |

---

# 17. Risk Overview

The assessment identified a concentration of weaknesses around legacy services and permissive configurations.

```text
                 NSE Assessment
                       │
        ┌──────────────┼──────────────┐
        │              │              │
      FTP            SMB            HTTP
        │              │              │
 Anonymous         SMBv1         Information
  Access          Signing Off      Disclosure
 Plaintext        Guest Access    Legacy Stack
   Traffic        RW Shares
        │              │
        └──────────────┼──────────────┘
                       │
                 SMTP / MySQL
                       │
             Legacy Services
             & Configuration
                       │
                       ↓
              Increased Attack
                   Surface
```

The most significant observations were associated with SMB because multiple weaknesses were identified simultaneously:

- Legacy SMBv1
- Guest access
- Disabled message signing
- Anonymous read/write share access

These weaknesses compound the overall risk of the SMB attack surface.

---

# 18. Recommendations Summary

| Priority | Recommendation |
|---|---|
| High | Disable SMBv1 |
| High | Review and restrict anonymous SMB access |
| High | Review SMB share permissions |
| High | Enable SMB message signing where appropriate |
| High | Restrict network access to database services |
| Medium | Disable anonymous FTP where unnecessary |
| Medium | Replace plaintext FTP |
| Medium | Remove unnecessary web applications and diagnostic files |
| Medium | Disable unnecessary directory listings |
| Medium | Disable obsolete SSL/TLS protocols |
| Medium | Replace expired certificates |
| Medium | Review unnecessary SMTP functionality |
| General | Upgrade legacy and unsupported services |

---

# 19. Assessment Limitations

This assessment has several limitations that should be considered when interpreting the findings.

## 19.1 Laboratory Environment

The assessment was performed against an intentionally vulnerable Metasploitable 2 system in a virtualized environment.

Results may therefore differ from those obtained against a modern production system.

## 19.2 NSE Script Coverage

Only selected NSE scripts were used.

The objective was targeted service enumeration rather than exhaustive NSE coverage.

## 19.3 Script Results Are Not Definitive

A script returning no vulnerability result does not prove that a service is secure.

Likewise, a failed script execution does not prove vulnerability.

## 19.4 No Exploitation Was Performed

This case study focused on reconnaissance and enumeration.

No exploitation was required to establish the documented configuration and exposure findings.

## 19.5 Version Information Is Not Proof of Exploitability

The presence of an old service version can indicate increased risk, but version information alone does not establish that a particular vulnerability is exploitable.

---

# 20. Lessons Learned

This assessment demonstrated several practical NSE concepts.

### 1. NSE should follow reconnaissance

Service discovery should normally come before service-specific NSE selection.

### 2. Script selection should be relevant

Running scripts based on the services actually discovered produces more meaningful assessment results than indiscriminately running large numbers of scripts.

### 3. NSE can reveal configuration weaknesses

NSE identified security-relevant configuration details such as:

- SMB message-signing status
- Anonymous FTP access
- Anonymous SMB share permissions
- Supported SMB protocols
- HTTP resources
- SMTP capabilities

### 4. Negative results require context

A vulnerability script returning `false` or `NOT VULNERABLE` only addresses the specific test performed.

### 5. Enumeration results must be interpreted

NSE output is evidence.

The security assessor must determine what that evidence means within the context of the target environment.

---

# 21. Conclusion

The NSE service enumeration assessment successfully demonstrated the progression from basic service discovery to targeted security enumeration.

The assessment used previously identified services to select relevant NSE scripts for:

- FTP
- SMB
- HTTP
- SMTP
- MySQL

The enumeration produced information that was not available through basic port scanning alone.

The most significant observations included:

- Anonymous FTP access
- Plaintext FTP communication
- SMBv1 support
- Disabled SMB message signing
- Guest SMB access
- Anonymous read/write access to selected SMB shares
- Web application and technology disclosure
- Legacy MySQL exposure
- Legacy SMTP security configuration

The assessment also demonstrated the importance of understanding NSE limitations. Some vulnerability scripts produced negative results, while others returned incomplete or unsupported results. These outcomes were interpreted as individual test results rather than proof of overall service security.

The overall workflow can be summarized as:

```text
Service discovered
        ↓
Relevant NSE scripts selected
        ↓
Additional information gathered
        ↓
Security significance interpreted
        ↓
Findings documented
        ↓
Recommendations developed
```

This approach demonstrates practical understanding of Nmap NSE as a security assessment tool rather than simply demonstrating the ability to execute NSE commands.

---

## Scope Limitation

NSE enumeration in this scenario was intentionally limited to selected services relevant to the discovered attack surface.

The assessment did not attempt to execute every NSE script category or every available service-specific script.

**Additional NSE enumeration can be performed as an extension exercise or assessment task.**
```
