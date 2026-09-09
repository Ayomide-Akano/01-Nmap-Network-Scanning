# Nmap References

This document contains the primary technical references used to research, understand, and document the Nmap techniques, networking concepts, NSE functionality, scanning behavior, and security assessment methods demonstrated throughout this project.

The references are primarily based on official Nmap documentation and related technical resources.

---

## Official Nmap Documentation

### Nmap Official Website

[svg](https://nmap.org/)

The official Nmap project website provides information about Nmap, its capabilities, downloads, documentation, and related tools.

**Reference:**

https://nmap.org/

---

### Nmap Documentation

[svg](https://nmap.org/docs.html)

The official Nmap documentation hub containing links to the Reference Guide, Nmap book, installation documentation, NSE documentation, and other resources.

**Reference:**

https://nmap.org/docs.html

---

### Nmap Reference Guide

[svg](https://nmap.org/book/man.html)

The primary command and option reference for Nmap. It covers host discovery, port scanning, service and version detection, OS detection, NSE, timing and performance, firewall/IDS evasion, spoofing, output, and other Nmap functionality.

**Reference:**

https://nmap.org/book/man.html

---

## Nmap Network Scanning

[svg](https://nmap.org/book/)

### Nmap Network Scanning — The Official Nmap Project Guide

[svg](https://nmap.org/book/)

Gordon "Fyodor" Lyon's official guide to Nmap. The book provides detailed explanations of network discovery, port scanning, service detection, OS detection, firewall and IDS evasion, scan optimization, NSE, and practical Nmap usage.

**Author:** Gordon "Fyodor" Lyon

**Reference:**

https://nmap.org/book/

---

### Nmap Network Scanning — Table of Contents

[svg](https://nmap.org/book/toc.html)

Used as a navigation reference for the official Nmap book and its chapters covering the techniques explored throughout this project.

**Reference:**

https://nmap.org/book/toc.html

---

## Nmap Scripting Engine (NSE)

[svg](https://nmap.org/nsedoc/)

### NSE Documentation Portal

[svg](https://nmap.org/nsedoc/)

The official NSE documentation portal containing documentation for Nmap scripts, libraries, and script categories.

**Reference:**

https://nmap.org/nsedoc/

---

### NSE Usage and Examples

[svg](https://nmap.org/book/nse-usage.html)

Provides documentation for using NSE, including the `-sC` option, `--script`, script categories, script arguments, and other NSE-related options.

**Reference:**

https://nmap.org/book/nse-usage.html

---

### NSE Categories

[svg](https://nmap.org/nsedoc/categories/)

Documents the categories used to organize NSE scripts, including:

- `auth`
- `broadcast`
- `brute`
- `default`
- `discovery`
- `dos`
- `exploit`
- `external`
- `fuzzer`
- `info`
- `intrusive`
- `malware`
- `safe`
- `version`
- `vuln`

**Reference:**

https://nmap.org/nsedoc/categories/

---

## Nmap Output and Evidence

[svg](https://nmap.org/book/man.html)

The Nmap Reference Guide was used to understand Nmap output formats and evidence preservation, including:

- Normal output
- XML output
- Grepable output
- Output aggregation
- Verbose output
- Scan result reporting

**Reference:**

https://nmap.org/book/man.html

---

## Networking and Security Concepts

[svg](https://nmap.org/book/)

The networking concepts documented in the `Concepts/` directory were studied alongside the Nmap documentation and practical scan results.

Topics include:

- TCP connections
- TCP SYN and RST behavior
- ICMP messages
- Port states
- Host discovery
- TCP and UDP communication
- Packet filtering
- Network mapping

The official Nmap documentation provides supporting explanations of these concepts in the context of network discovery and security scanning.

**Reference:**

https://nmap.org/book/

---

## Responsible and Authorized Scanning

[svg](https://nmap.org/book/intro.html)

Nmap documentation discusses legal and responsible use of network scanning and emphasizes the importance of understanding the implications of scanning systems without authorization.

This project was conducted within an intentionally vulnerable, controlled laboratory environment using Metasploitable 2.

**Reference:**

https://nmap.org/book/intro.html

---

## Reference Usage

These references supported different parts of the portfolio:

| Portfolio Section | Primary Reference Area |
|---|---|
| Commands | Nmap Reference Guide |
| Concepts | Nmap documentation and Nmap Network Scanning |
| Diagrams | Nmap Network Scanning |
| Practical Scenarios | Nmap Reference Guide and Nmap Network Scanning |
| NSE Enumeration | NSE Documentation Portal |
| Case Studies | Nmap documentation and practical scan evidence |
| Output Formats | Nmap Reference Guide |
| Scan Optimization | Nmap Reference Guide and Nmap Network Scanning |
| Firewall/Filtering Analysis | Nmap Reference Guide and Nmap Network Scanning |

---

## Notes

The references in this document were used as technical research sources and documentation references.

The practical results presented in this portfolio were generated independently within the project's authorized laboratory environment.

Observed scan results, service versions, response behavior, and security findings are based on the actual laboratory environment and should not be interpreted as direct results from the referenced documentation.
```
