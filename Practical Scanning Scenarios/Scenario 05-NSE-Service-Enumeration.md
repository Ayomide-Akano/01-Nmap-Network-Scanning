# Scenario 05 — NSE Service Enumeration

## Objective

The objective of this scenario was to use the Nmap Scripting Engine (NSE) to perform targeted enumeration against selected services discovered during previous network scans.

Rather than running large numbers of NSE scripts indiscriminately, this assessment followed a service-driven approach:

```text
Service discovered
        ↓
Relevant NSE scripts selected
        ↓
Additional information gathered
        ↓
Security significance interpreted
        ↓
Assessment evidence documented
```

The selected services were:

- FTP
- SMB
- HTTP
- SMTP
- MySQL

The assessment was performed against the Metasploitable 2 laboratory host.

---

## Lab Environment

| Item | Value |
|---|---|
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Scanner | Linux system |
| Scanner IP | `192.168.43.155` |
| Nmap Version | 7.99 |
| Network Type | Virtualized laboratory environment |
| Assessment Type | Authorized security assessment |

> **Authorization Notice:**  
> All scanning and enumeration activities documented in this scenario were performed against an intentionally vulnerable laboratory system under authorized testing conditions.

---

# 1. Service Discovery Baseline

Before running individual NSE scripts, a targeted service/version scan was performed against the selected ports.

### Command

```bash
sudo nmap -sS -sV -p 21,25,80,139,445,3306 192.168.43.56
```

### Result

```text
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
25/tcp   open  smtp        Postfix smtpd
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
```

Additional service information identified the host as:

```text
Host: metasploitable.localdomain
OS: Unix
MAC Address: 08:00:27:DB:2A:1B
Vendor: Oracle VirtualBox virtual NIC
```

### Initial Interpretation

The baseline scan confirmed that the selected services were accessible and provided the service versions required to select relevant NSE scripts.

The assessment therefore proceeded on a service-by-service basis.

---

# 2. FTP Enumeration

## 2.1 Anonymous FTP Access

### Command

```bash
sudo nmap -p 21 --script ftp-anon 192.168.43.56
```

### Result

```text
21/tcp open  ftp
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
```

### Interpretation

The FTP service allowed anonymous authentication.

This is significant because anonymous FTP access can expose files or resources to unauthenticated users depending on the server configuration.

The result confirms that the FTP service should be reviewed for unnecessary anonymous access.

---

## 2.2 FTP Server Information

### Command

```bash
sudo nmap -p 21 --script ftp-syst 192.168.43.56
```

### Result

```text
FTP server status:
    Connected to 192.168.43.155
    Logged in as ftp
    TYPE: ASCII
    No session bandwidth limit
    Session timeout in seconds is 300
    Control connection is plain text
    Data connections will be plain text
    vsFTPd 2.3.4 - secure, fast, stable
```

### Interpretation

The NSE script provided additional information about the FTP session and confirmed that both the control and data connections were transmitted in plaintext.

The service was also identified as:

```text
vsFTPd 2.3.4
```

The plaintext nature of FTP creates a risk of credential and data exposure when the service is used across an untrusted network.

---

## 2.3 FTP Vulnerability Script Check

### Command

```bash
sudo nmap -p 21 --script "ftp-vuln*" 192.168.43.56
```

### Result

```text
21/tcp open  ftp
```

No specific vulnerability result was returned by the selected vulnerability scripts.

### Interpretation

The absence of a vulnerability result from this NSE check does **not** mean that the FTP service is secure.

Other evidence already established:

- Anonymous FTP access
- Plaintext FTP communication
- Legacy FTP software version

NSE vulnerability checks should therefore be treated as additional assessment evidence rather than as a complete vulnerability assessment.

---

# 3. SMB Enumeration

Ports `139/tcp` and `445/tcp` were selected for SMB enumeration because previous scans identified Samba services on both ports.

---

## 3.1 SMB Protocol Enumeration

### Command

```bash
sudo nmap -p 139,445 --script smb-protocols 192.168.43.56
```

### Result

```text
smb-protocols:
    dialects:
        NT LM 0.12 (SMBv1) [dangerous, but default]
```

### Interpretation

The SMB service supported SMBv1.

SMBv1 is a legacy protocol and its continued exposure increases the security risk of the service.

This finding is particularly significant when combined with other SMB configuration weaknesses identified during the assessment.

---

## 3.2 SMB Security Mode

### Command

```bash
sudo nmap -p 139,445 --script smb-security-mode 192.168.43.56
```

### Result

```text
smb-security-mode:
    account_used: guest
    authentication_level: user
    challenge_response: supported
    message_signing: disabled (dangerous, but default)
```

### Interpretation

The scan identified two important security observations:

1. The SMB enumeration used the `guest` account.
2. SMB message signing was disabled.

Disabled message signing reduces the protection available against certain network-level attacks involving SMB traffic.

The result should therefore be treated as a significant configuration weakness.

---

## 3.3 SMB Operating System and Host Information

### Command

```bash
sudo nmap -p 139,445 --script smb-os-discovery 192.168.43.56
```

### Result

```text
OS: Unix (Samba 3.0.20-Debian)
Computer name: metasploitable
Domain name: localdomain
FQDN: metasploitable.localdomain
System time: 2026-09-09T09:10:08-04:00
```

### Interpretation

The NSE script provided information that was not available from basic port scanning alone.

The scan identified:

- Operating system family: Unix
- Samba version: 3.0.20-Debian
- Computer name: `metasploitable`
- Domain: `localdomain`
- FQDN: `metasploitable.localdomain`

This information can assist an assessor in understanding the target environment and identifying additional areas for review.

---

## 3.4 SMB Share Enumeration

### Command

```bash
sudo nmap -p 139,445 --script smb-enum-shares 192.168.43.56
```

### Result

The following shares were identified:

| Share | Type | Anonymous Access |
|---|---|---|
| `ADMIN$` | IPC | None |
| `IPC$` | IPC | READ/WRITE |
| `opt` | Disk | None |
| `print$` | Disk | None |
| `tmp` | Disk | READ/WRITE |

The `IPC$` and `tmp` shares allowed anonymous read/write access.

### Interpretation

Anonymous read/write access to SMB shares represents a significant security concern.

In particular, the following were observed:

```text
\\192.168.43.56\IPC$
Anonymous access: READ/WRITE

\\192.168.43.56\tmp
Anonymous access: READ/WRITE
```

This demonstrates how NSE can move beyond simply identifying an SMB service and reveal potentially dangerous access-control configurations.

---

## 3.5 SMB Vulnerability Script Check

### Command

```bash
sudo nmap -p 139,445 --script "smb-vuln*" 192.168.43.56
```

### Result

```text
smb-vuln-regsvc-dos: ERROR: Script execution failed
smb-vuln-ms10-061: false
smb-vuln-ms10-054: false
```

### Interpretation

Two vulnerability checks returned negative results for the tested vulnerabilities.

One script produced an execution error:

```text
smb-vuln-regsvc-dos: ERROR: Script execution failed
```

The error does not indicate that the target is vulnerable. It only means that the script did not complete successfully.

More importantly, the assessment already identified significant SMB security weaknesses through configuration and enumeration scripts:

- SMBv1 supported
- Message signing disabled
- Guest account used
- Anonymous read/write access to selected shares

Therefore, the failed vulnerability script does not invalidate the other findings.

---

# 4. HTTP Enumeration

Port `80/tcp` was selected because previous scans identified an Apache HTTP service.

---

## 4.1 HTTP Page Title

### Command

```bash
sudo nmap -p 80 --script http-title 192.168.43.56
```

### Result

```text
http-title: Metasploitable2 - Linux
```

### Interpretation

The HTTP title identified the web application as the Metasploitable 2 Linux environment.

This provides basic application identification and confirms that the web service is associated with the target laboratory environment.

---

## 4.2 HTTP Headers

### Command

```bash
sudo nmap -p 80 --script http-headers 192.168.43.56
```

### Result

```text
Date: Wed, 09 Sep 2026 13:13:48 GMT
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
Connection: close
Content-Type: text/html
```

### Interpretation

The HTTP headers disclosed server and application technology information.

Observed technologies included:

```text
Apache/2.2.8 (Ubuntu) DAV/2
PHP/5.2.4-2ubuntu5.10
```

Technology disclosure can assist security assessment and attack-surface identification because it provides information about the underlying software stack.

The versions observed are also legacy versions within this intentionally vulnerable laboratory environment.

---

## 4.3 HTTP Methods

### Command

```bash
sudo nmap -p 80 --script http-methods 192.168.43.56
```

### Result

```text
Supported Methods: GET HEAD POST OPTIONS
```

### Interpretation

The server responded as supporting:

- GET
- HEAD
- POST
- OPTIONS

The presence of POST indicates that the server accepts methods capable of submitting data to web applications.

However, the scan does not by itself demonstrate that any of these methods are improperly configured or exploitable.

---

## 4.4 HTTP Directory and Resource Enumeration

### Command

```bash
sudo nmap -p 80 --script http-enum 192.168.43.56
```

### Result

The NSE script identified the following resources:

```text
/tikiwiki/       Tikiwiki
/test/           Test page
/phpinfo.php     Possible information file
/phpMyAdmin/     phpMyAdmin
/doc/            Potentially interesting directory w/ listing
/icons/          Potentially interesting folder w/ directory listing
/index/          Potentially interesting folder
```

### Interpretation

The web enumeration significantly expanded the understanding of the HTTP attack surface.

Notable findings included:

- Tikiwiki installation
- Test page
- `phpinfo.php`
- phpMyAdmin
- Potential directory listings

In particular, `phpinfo.php` may expose detailed PHP configuration information if accessible, while directory listings can disclose files and application resources.

The presence of phpMyAdmin also identifies a web-based database administration interface that warrants further review.

---

# 5. SMTP Enumeration

Port `25/tcp` was selected because previous scanning identified a Postfix SMTP service.

---

## 5.1 SMTP Command Enumeration

### Command

```bash
sudo nmap -p 25 --script smtp-commands 192.168.43.56
```

### Result

```text
smtp-commands:
metasploitable.localdomain,
PIPELINING,
SIZE 10240000,
VRFY,
ETRN,
STARTTLS,
ENHANCEDSTATUSCODES,
8BITMIME,
DSN
```

### Interpretation

The SMTP service disclosed its supported commands and capabilities.

Notable commands included:

```text
VRFY
ETRN
STARTTLS
```

This provides additional information about the SMTP service configuration and capabilities.

The availability of `VRFY` is noteworthy because SMTP user verification functionality can sometimes provide information about valid accounts, although this assessment did not establish successful user enumeration.

---

## 5.2 SMTP User Enumeration

### Command

```bash
sudo nmap -p 25 --script smtp-enum-users 192.168.43.56
```

### Result

```text
smtp-enum-users:
    Method RCPT returned a unhandled status code.
```

### Interpretation

The SMTP user enumeration script did not produce a list of valid users.

Instead, the SMTP server returned a status code that the script did not handle.

This result should not be interpreted as proof that user enumeration is impossible or that no accounts exist.

It simply means that this particular enumeration attempt did not produce usable results.

---

## 5.3 SMTP Vulnerability Script Check

### Command

```bash
sudo nmap -p 25 --script "smtp-vuln*" 192.168.43.56
```

### Result

```text
smtp-vuln-cve2010-4344:
    The SMTP server is not Exim: NOT VULNERABLE
```

### Interpretation

The vulnerability check identified the server as Postfix rather than Exim.

Therefore, the tested Exim-specific vulnerability did not apply to the target.

This demonstrates an important principle of NSE enumeration:

> A vulnerability script may return a negative result because the tested vulnerability is not relevant to the discovered service.

The result should not be interpreted as a general statement that the SMTP service is secure.

---

# 6. MySQL Enumeration

Port `3306/tcp` was selected because previous scans identified a MySQL database service.

---

## 6.1 MySQL Service Information

### Command

```bash
sudo nmap -p 3306 --script mysql-info 192.168.43.56
```

### Result

```text
Protocol: 10
Version: 5.0.51a-3ubuntu5
Thread ID: 9
Capabilities flags: 43564
Some Capabilities:
    Support41Auth
    ConnectWithDatabase
    Speaks41ProtocolNew
    SwitchToSSLAfterHandshake
    SupportsCompression
    SupportsTransactions
    LongColumnFlag
Status: Autocommit
```

### Interpretation

The NSE script provided detailed information about the MySQL service beyond the initial version detection.

The database service was identified as:

```text
MySQL 5.0.51a-3ubuntu5
```

The service exposed protocol and capability information that can assist with further assessment.

---

## 6.2 MySQL User Enumeration

### Command

```bash
sudo nmap -p 3306 --script mysql-users 192.168.43.56
```

### Result

No user information was returned.

### Interpretation

The script completed without producing a user list.

This means that the assessment did not obtain MySQL account information through this NSE check.

It does not establish that no database accounts exist.

---

## 6.3 MySQL Database Enumeration

### Command

```bash
sudo nmap -p 3306 --script mysql-databases 192.168.43.56
```

### Result

No database names were returned.

### Interpretation

The NSE script did not obtain database names from the target.

Again, this should be interpreted as an enumeration limitation rather than evidence that the MySQL server contains no databases.

---

## 6.4 MySQL Vulnerability Script Check

### Command

```bash
sudo nmap -p 3306 --script "mysql-vuln*" 192.168.43.56
```

### Result

```text
3306/tcp open mysql
```

No specific vulnerability result was returned.

### Interpretation

The vulnerability scripts did not identify a specific vulnerability during this test.

However, the service itself was confirmed to be an exposed legacy MySQL installation:

```text
MySQL 5.0.51a-3ubuntu5
```

The absence of a vulnerability result should therefore not be treated as proof of security.

---

# 7. Combined Targeted NSE Scan

After the individual service-specific checks, a combined NSE scan was performed to demonstrate how several relevant scripts can be applied during a single targeted assessment.

### Command

```bash
sudo nmap -sV -p 21,25,80,139,445,3306 --script "ftp-anon,ftp-syst,smb-protocols,smb-security-mode,smb-os-discovery,http-title,http-headers,http-methods,smtp-commands,mysql-info" 192.168.43.56
```

### Result Summary

The combined scan reconfirmed the following:

### FTP

```text
vsftpd 2.3.4
Anonymous FTP login allowed
Plaintext control and data connections
```

### SMTP

```text
Postfix smtpd
PIPELINING
VRFY
ETRN
STARTTLS
```

### HTTP

```text
Apache httpd 2.2.8
PHP/5.2.4-2ubuntu5.10
Metasploitable2 - Linux
GET HEAD POST OPTIONS
```

### SMB

```text
Samba 3.0.20-Debian
SMBv1 supported
Guest account used
Message signing disabled
Unix system
metasploitable.localdomain
```

### MySQL

```text
MySQL 5.0.51a-3ubuntu5
Protocol 10
Detailed capability information
```

### Interpretation

The combined scan demonstrated that NSE can efficiently gather additional service-level information once the relevant services have already been identified.

Rather than treating NSE as a collection of unrelated scripts, the assessment used the previously discovered attack surface to determine which scripts were relevant.

---

# 8. Default NSE Script Scan

As an optional final validation step, the common/default NSE script set was executed against the selected services.

### Command

```bash
sudo nmap -sV -sC -p 21,25,80,139,445,3306 192.168.43.56
```

### Key Results

The scan reconfirmed:

### FTP

```text
vsftpd 2.3.4
Anonymous FTP login allowed
Plaintext FTP connections
```

### SMTP

```text
Postfix smtpd
SSLv2 supported
```

The SMTP service also presented an expired certificate:

```text
Not valid before: 2010-03-17T14:07:45
Not valid after: 2010-04-16T14:07:45
```

### HTTP

```text
Apache httpd 2.2.8
Metasploitable2 - Linux
```

### SMB

```text
Guest account used
Message signing disabled
SMB2 protocol negotiation failed
OS: Unix (Samba 3.0.20-Debian)
Computer name: metasploitable
Domain: localdomain
FQDN: metasploitable.localdomain
```

The scan also reported:

```text
NetBIOS name: METASPLOITABLE
```

### MySQL

```text
MySQL 5.0.51a-3ubuntu5
Protocol: 10
Detailed capability information
```

### Interpretation

The default NSE scan provided additional confirmation of several observations made during the targeted enumeration.

It also revealed additional SMTP-related information, including SSLv2 support and an expired certificate, which had already appeared in earlier broader scanning work.

---

# 9. NSE Enumeration Summary

The following table summarizes the service-driven enumeration performed during this scenario.

| Service | Port(s) | NSE Scripts | Key Information Gathered | Security Significance |
|---|---:|---|---|---|
| FTP | 21 | `ftp-anon`, `ftp-syst`, `ftp-vuln*` | Anonymous access, plaintext communication, vsftpd version | Unauthenticated access and plaintext transmission |
| SMB | 139, 445 | `smb-protocols`, `smb-security-mode`, `smb-os-discovery`, `smb-enum-shares`, `smb-vuln*` | SMBv1, guest access, signing disabled, shares, host information | Legacy protocol and weak access/security configuration |
| HTTP | 80 | `http-title`, `http-headers`, `http-methods`, `http-enum` | Web technologies, methods, directories, phpMyAdmin, phpinfo | Increased web attack surface and information disclosure |
| SMTP | 25 | `smtp-commands`, `smtp-enum-users`, `smtp-vuln*` | SMTP capabilities and server behavior | Service capability disclosure; enumeration attempt unsuccessful |
| MySQL | 3306 | `mysql-info`, `mysql-users`, `mysql-databases`, `mysql-vuln*` | Protocol, version and capability information | Exposed legacy database service |

---

# 10. Key Security Observations

The targeted NSE enumeration produced several important observations.

## 10.1 Anonymous FTP Access

The FTP server permitted anonymous login.

This increases the possibility of unauthorized access to files or resources exposed through the FTP service.

---

## 10.2 Plaintext FTP Communication

The FTP server reported that both control and data connections were transmitted in plaintext.

Sensitive information transmitted through FTP could therefore be exposed to network interception.

---

## 10.3 Legacy SMBv1 Protocol

SMB enumeration identified:

```text
NT LM 0.12 (SMBv1)
```

The continued exposure of a legacy SMB protocol increases the attack surface of the host.

---

## 10.4 SMB Message Signing Disabled

The SMB security scan reported:

```text
message_signing: disabled
```

This represents a security configuration weakness and reduces the integrity protection available for SMB communications.

---

## 10.5 Anonymous SMB Read/Write Access

The share enumeration identified anonymous read/write access to:

```text
IPC$
tmp
```

This is a significant access-control concern because unauthenticated users may be able to interact with shared resources.

---

## 10.6 Web Application and Information Disclosure

HTTP enumeration identified:

```text
/tikiwiki/
/test/
/phpinfo.php
/phpMyAdmin/
/doc/
/icons/
/index/
```

The server also disclosed:

```text
Apache/2.2.8
PHP/5.2.4
```

This increases the amount of information available to an attacker during reconnaissance.

---

## 10.7 Legacy MySQL Exposure

The MySQL service was externally accessible on:

```text
3306/tcp
```

and identified as:

```text
MySQL 5.0.51a-3ubuntu5
```

Exposing a database service directly to the network increases the attack surface and should be carefully controlled in production environments.

---

# 11. Assessment Limitations

The NSE enumeration performed in this scenario has several limitations.

### 1. NSE is not a complete vulnerability assessment

A script returning no vulnerability result does not prove that a service is secure.

### 2. Some scripts produced limited results

For example:

```text
smtp-enum-users:
Method RCPT returned a unhandled status code.
```

and:

```text
smb-vuln-regsvc-dos:
ERROR: Script execution failed
```

These results indicate limitations of the individual enumeration attempts rather than confirmed security properties of the target.

### 3. Service versions do not automatically prove exploitability

The presence of an old software version provides useful assessment information but does not, by itself, prove that a particular vulnerability is exploitable.

### 4. Results are specific to the laboratory

The target is a deliberately vulnerable Metasploitable 2 system running inside a virtualized laboratory environment.

The observed results should therefore not be generalized directly to production systems.

---

# 12. Security Recommendations

Based on the observations from the NSE enumeration, the following controls should be considered for a production environment:

1. Disable anonymous FTP access unless there is a documented business requirement.

2. Replace plaintext FTP with secure alternatives such as SFTP or another encrypted file-transfer mechanism.

3. Disable legacy SMBv1 where it is not required.

4. Enable SMB message signing where appropriate.

5. Review SMB share permissions and remove anonymous read/write access.

6. Restrict access to database services such as MySQL using network segmentation and firewall rules.

7. Remove unnecessary or legacy web applications and administrative interfaces.

8. Review exposure of files such as `phpinfo.php` and disable unnecessary directory listings.

9. Minimize server version and technology disclosure where practical.

10. Review SMTP capabilities and disable unnecessary functionality.

11. Use current, supported versions of operating systems and network services.

---

# 13. Evidence-to-Interpretation Workflow

This scenario demonstrated the intended service-driven NSE workflow:

```text
Previous Network Scanning
          ↓
Service Identified
          ↓
FTP ──────────────→ FTP NSE Scripts
SMB ──────────────→ SMB NSE Scripts
HTTP ─────────────→ HTTP NSE Scripts
SMTP ─────────────→ SMTP NSE Scripts
MySQL ────────────→ MySQL NSE Scripts
          ↓
Additional Service Information
          ↓
Configuration / Exposure Identified
          ↓
Security Significance Interpreted
          ↓
Assessment Evidence Documented
```

This approach is more practical than indiscriminately running large numbers of NSE scripts against every possible service.

---

# 14. Conclusion

This scenario demonstrated how the Nmap Scripting Engine can be used to move from basic service discovery to targeted service enumeration.

The assessment began with services identified during previous scans and selected NSE scripts based on those services.

The results provided additional information that was not available from basic port scanning alone, including:

- Anonymous FTP access
- Plaintext FTP communication
- SMBv1 support
- SMB message-signing status
- SMB guest access
- SMB share permissions
- Host and domain information
- HTTP server and PHP information
- Web directories and applications
- SMTP capabilities
- MySQL protocol and capability information

The exercise demonstrated an important security assessment principle:

> **NSE is most effective when scripts are selected based on the services and attack surface already identified.**

The assessment did not attempt to run every available NSE script category. Instead, a limited set of relevant scripts was selected to demonstrate practical service enumeration.

### Scope Limitation

**NSE enumeration in this scenario was intentionally limited to selected services relevant to the discovered attack surface. Additional NSE categories and service-specific scripts can be performed as an extension exercise or assessment task.**
```
