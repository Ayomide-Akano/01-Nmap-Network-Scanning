# NSE Enumeration Workflow

Nmap Scripting Engine (NSE) allows Nmap to perform additional service-specific enumeration and security checks.

The workflow used in this project was driven by services discovered during scanning.

## Diagram

```text
                 Service Discovery
                        |
                        v
              Identify Exposed Service
                        |
                        v
               Select Relevant NSE
                    Scripts
                        |
                        v
              Run NSE Enumeration
                        |
                        v
              Gather Additional
                  Information
                        |
                        v
              Interpret Security
                   Significance
                        |
                        v
              Document Evidence
```

---

## Explanation

### Step 1

The scanner first identifies exposed services using port and service/version scanning.

---

### Step 2

Relevant NSE scripts are selected based on the services discovered.

For example:

**FTP → FTP NSE scripts**

**SMB → SMB NSE scripts**

**HTTP → HTTP NSE scripts**

---

### Step 3

The selected scripts gather additional information about the target service.

The results are then interpreted from a security perspective.

---

## Why Is NSE Enumeration Important?

NSE can provide information that basic port scanning does not reveal.

Examples include:

- Anonymous FTP access
- SMB protocol information
- SMB security configuration
- HTTP headers
- HTTP directories
- SMTP capabilities
- MySQL information

---

## Benefits

- Provides service-specific information
- Supports security assessment
- Helps identify configuration weaknesses
- Produces useful evidence
- Enables targeted enumeration
```
