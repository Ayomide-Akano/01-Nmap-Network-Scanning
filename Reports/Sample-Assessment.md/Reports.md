# Network Assessment Report

---

## Executive Summary

A network reconnaissance assessment was conducted to identify accessible hosts, open ports, exposed services, and potential security risks within the testing environment.

The assessment was performed using Nmap in an authorized laboratory environment.

---

## Scope

Target

192.168.1.10

Assessment Type

Internal Network Assessment

Tools Used

- Nmap
- Kali Linux

---

## Methodology

The assessment consisted of:

- Host Discovery
- Port Enumeration
- Service Detection
- Operating System Detection
- NSE Enumeration

---

## Findings

| Port | Service | Risk |
|------|----------|------|
|22|SSH|Low|
|80|HTTP|Medium|
|445|SMB|High|

---

## Risk Analysis

### SSH

Remote administration enabled.

Recommendation:

Restrict access using firewall rules.

---

### HTTP

Web service detected.

Recommendation

Keep software updated.

---

### SMB

SMB exposed.

Recommendation

Disable SMBv1.

Restrict access.

Apply latest patches.

---

## Overall Risk Rating

Medium

---

## Recommendations

- Disable unnecessary services
- Restrict management ports
- Apply security patches
- Enable logging
- Review firewall rules

---

## Conclusion

The assessment identified several exposed services requiring review. Implementing the recommendations above will significantly improve the security posture of the environment.
