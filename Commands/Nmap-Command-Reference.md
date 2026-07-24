# Basic Nmap Commands

## Ping Scan

```bash
nmap -sn 192.168.1.0/24
```

Discovers live hosts.

---

## TCP SYN Scan

```bash
nmap -sS 192.168.1.10
```

Performs a stealth scan.

---

## Service Detection

```bash
nmap -sV 192.168.1.10
```

Identifies running services.

---

## Operating System Detection

```bash
nmap -O 192.168.1.10
```

Attempts to identify the operating system.

---

## Aggressive Scan

```bash
nmap -A 192.168.1.10
```

Performs OS detection, service detection, version detection, and traceroute.

---

## Scan All Ports

```bash
nmap -p- 192.168.1.10
```

Scans all TCP ports.
