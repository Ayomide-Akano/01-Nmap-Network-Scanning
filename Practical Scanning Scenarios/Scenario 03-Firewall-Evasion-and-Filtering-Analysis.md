# Scenario 03 — Firewall Evasion & Filtering Analysis

## Executive Summary

This practical scenario examined how different Nmap scanning and packet-manipulation techniques affect the interpretation of network services and port states.

The assessment was performed against an authorized Metasploitable 2 laboratory system at `192.168.43.56`.

The techniques investigated included:

- TCP ACK scanning
- FIN scanning
- NULL scanning
- Xmas scanning
- IP packet fragmentation
- Custom MTU fragmentation
- Decoy scanning
- Source port manipulation
- Invalid TCP checksum testing
- MAC address spoofing

The objective was not to bypass security controls in a production environment, but to observe how different probe types and packet characteristics influence Nmap's results.

The results demonstrated several important differences:

- The ACK scan reported all 1,000 tested TCP ports as **unfiltered**.
- FIN, NULL, and Xmas scans reported the same 12 known ports as **open|filtered**.
- Fragmented SYN scanning produced the same open-port results as the normal SYN scan for the tested ports.
- Custom MTU scanning produced the same results as fragmented scanning.
- The decoy scan successfully identified the expected open services.
- Source-port manipulation also produced the same open-port results as the baseline scan.
- Bad-checksum scanning caused the tested ports to appear **filtered**.
- MAC spoofing also caused the tested ports to appear **filtered**.

These results show that Nmap results must be interpreted in the context of the scan technique used. A port reported as `open|filtered` does not necessarily mean that the port is open, while a `filtered` result can reflect how the target or network stack handles the particular probe.

---

# 1. Assessment Overview

## Target

| Parameter | Value |
|---|---|
| Target | Metasploitable 2 |
| IP Address | `192.168.43.56` |
| Network | `192.168.43.0/24` |
| Scanning System | Linux |
| Environment | Authorized VirtualBox laboratory |
| Purpose | Firewall/filtering and Nmap scan-technique analysis |

## Scope

The assessment focused on TCP scanning techniques and packet manipulation methods associated with firewall and filtering analysis.

The following techniques were tested:

1. ACK scanning
2. FIN scanning
3. NULL scanning
4. Xmas scanning
5. Packet fragmentation
6. Custom MTU
7. Decoy scanning
8. Source port manipulation
9. Bad TCP checksums
10. MAC address spoofing

The assessment remained limited to the designated laboratory target.

---

# 2. Methodology

The scenario used a combination of baseline and alternative Nmap scans.

A standard SYN scan against selected ports was used as the comparison point:

```bash
sudo nmap -sS -p 21,22,23,25,53,80,139,445 192.168.43.56
```

Alternative scanning techniques were then applied to the same target.

The results were compared based on:

- Port state
- Number of ports reported
- Services identified
- Scan duration
- MAC address reported
- Differences from the baseline SYN scan

This approach allowed the behavior of each technique to be evaluated using actual laboratory evidence.

---

# 3. ACK Scan — Filtering Analysis

## Command

```bash
sudo nmap -sA -p 1-1000 192.168.43.56
```

## Result

```text
Starting Nmap 7.99 at 2026-09-09 02:33 +0100
Nmap scan report for 192.168.43.56
Host is up (0.00054s latency).

All 1000 scanned ports on 192.168.43.56 are in ignored states.
Not shown: 1000 unfiltered tcp ports (reset)

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.32 seconds
```

## Observation

The ACK scan classified all 1,000 tested TCP ports as:

```text
unfiltered
```

Nmap reported:

```text
1000 unfiltered tcp ports (reset)
```

This indicates that the ACK probes received responses rather than being silently filtered.

The scan did not identify ports as open because ACK scanning is primarily useful for determining filtering behavior rather than identifying open services.

## Security Insight

The result provides evidence that, for this laboratory target and scan path, the tested TCP ports were not being silently filtered from the perspective of the ACK probes.

This demonstrates why an ACK scan can provide information that a normal SYN scan does not directly provide.

---

# 4. FIN Scan

## Command

```bash
sudo nmap -sF -p 1-1000 192.168.43.56
```

## Result

```text
Not shown: 988 closed tcp ports (reset)

PORT    STATE         SERVICE
21/tcp  open|filtered ftp
22/tcp  open|filtered ssh
23/tcp  open|filtered telnet
25/tcp  open|filtered smtp
53/tcp  open|filtered domain
80/tcp  open|filtered http
111/tcp open|filtered rpcbind
139/tcp open|filtered netbios-ssn
445/tcp open|filtered microsoft-ds
512/tcp open|filtered exec
513/tcp open|filtered login
514/tcp open|filtered shell
```

Scan duration:

```text
2.43 seconds
```

## Observation

The FIN scan reported the same 12 ports identified within the first 1,000 TCP ports during the previous scanning exercises.

However, instead of reporting them as `open`, Nmap reported them as:

```text
open|filtered
```

This is an important distinction.

The result does not prove that these ports are open. It means Nmap could not definitively distinguish between an open port and a filtered port using the FIN probe.

---

# 5. NULL Scan

## Command

```bash
sudo nmap -sN -p 1-1000 192.168.43.56
```

## Result

```text
Not shown: 988 closed tcp ports (reset)

PORT    STATE         SERVICE
21/tcp  open|filtered ftp
22/tcp  open|filtered ssh
23/tcp  open|filtered telnet
25/tcp  open|filtered smtp
53/tcp  open|filtered domain
80/tcp  open|filtered http
111/tcp open|filtered rpcbind
139/tcp open|filtered netbios-ssn
445/tcp open|filtered microsoft-ds
512/tcp open|filtered exec
513/tcp open|filtered login
514/tcp open|filtered shell
```

Scan duration:

```text
2.38 seconds
```

## Observation

The NULL scan produced essentially the same result as the FIN scan:

- 988 ports reported as closed
- 12 ports reported as `open|filtered`

This demonstrates that different TCP probe types can produce similar classifications when the target responds in a way that does not allow Nmap to definitively establish the port state.

---

# 6. Xmas Scan

## Command

```bash
sudo nmap -sX -p 1-1000 192.168.43.56
```

## Result

```text
Not shown: 988 closed tcp ports (reset)

PORT    STATE         SERVICE
21/tcp  open|filtered ftp
22/tcp  open|filtered ssh
23/tcp  open|filtered telnet
25/tcp  open|filtered smtp
53/tcp  open|filtered domain
80/tcp  open|filtered http
111/tcp open|filtered rpcbind
139/tcp open|filtered netbios-ssn
445/tcp open|filtered microsoft-ds
512/tcp open|filtered exec
513/tcp open|filtered login
514/tcp open|filtered shell
```

Scan duration:

```text
3.53 seconds
```

## Observation

The Xmas scan produced the same port-state pattern as the FIN and NULL scans.

The 12 identified ports were reported as:

```text
open|filtered
```

while the remaining 988 ports were reported as closed.

The scan also took slightly longer than the FIN and NULL scans.

---

# 7. FIN, NULL and Xmas Comparison

The three scans produced remarkably similar results.

| Scan | Ports Tested | Closed | Open\|Filtered | Time |
|---|---:|---:|---:|---:|
| FIN | 1–1000 | 988 | 12 | 2.43 sec |
| NULL | 1–1000 | 988 | 12 | 2.38 sec |
| Xmas | 1–1000 | 988 | 12 | 3.53 sec |

The same 12 ports were identified:

```text
21
22
23
25
53
80
111
139
445
512
513
514
```

## Security Insight

The consistency across these scans suggests that the target's TCP/IP behavior produced similar responses to these alternative TCP probes.

However, these results should not be interpreted as independent confirmation that all 12 ports are open.

The `open|filtered` classification represents uncertainty.

This is an important lesson when analyzing Nmap results:

> Different scan types can identify the same network area while providing different levels of certainty about the actual port state.

---

# 8. Fragmented SYN Scan

## Command

```bash
sudo nmap -sS -f -p 21,22,23,25,53,80,139,445 192.168.43.56
```

## Result

```text
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
23/tcp  open  telnet
25/tcp  open  smtp
53/tcp  open  domain
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

Scan duration:

```text
1.50 seconds
```

## Observation

The fragmented SYN scan successfully identified all eight selected ports as open.

This matched the baseline SYN scan.

The use of packet fragmentation therefore did not produce a different port-state result against this particular laboratory target.

## Security Insight

Packet fragmentation is a technique that changes how probes are constructed and transmitted.

However, the laboratory result demonstrates that using fragmentation does not automatically result in different scan results.

Network devices, firewalls, and operating systems may handle fragmented packets differently.

---

# 9. Custom MTU

## Command

```bash
sudo nmap -sS --mtu 24 -p 21,22,23,25,53,80,139,445 192.168.43.56
```

## Result

```text
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
23/tcp  open  telnet
25/tcp  open  smtp
53/tcp  open  domain
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

Scan duration:

```text
1.45 seconds
```

## Observation

The custom MTU scan produced exactly the same port-state results as the fragmented SYN scan.

All eight selected ports were identified as open.

This provides another example of why scan results should be evaluated against a baseline rather than assuming that a particular scanning technique will automatically change the outcome.

---

# 10. Fragmentation vs Custom MTU

| Technique | Result | Time |
|---|---|---:|
| Fragmented SYN (`-f`) | 8 open | 1.50 sec |
| Custom MTU (`--mtu 24`) | 8 open | 1.45 sec |
| Baseline SYN | 8 open | 1.06 sec |

The results were consistent across all three scans.

The baseline was slightly faster, but the difference was small in this laboratory environment.

---

# 11. Decoy Scan

## Command

```bash
sudo nmap -sS -D RND:5 -p 21,22,23,25,53,80 192.168.43.56
```

## Result

```text
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
23/tcp  open  telnet
25/tcp  open  smtp
53/tcp  open  domain
80/tcp  open  http
```

Scan duration:

```text
1.34 seconds
```

## Observation

The decoy scan successfully identified all six selected services as open.

The result was consistent with the normal SYN scan.

## Security Insight

The decoy technique introduces additional apparent scan sources in the generated traffic.

The purpose of this laboratory test was to demonstrate the technique and observe the target's response.

The scan result itself does not provide evidence that the decoy technique successfully concealed the originating system from every possible monitoring or logging mechanism.

---

# 12. Source Port Manipulation

## Command

```bash
sudo nmap -sS --source-port 53 -p 21,22,23,25,53,80,139,445 192.168.43.56
```

## Result

```text
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
23/tcp  open  telnet
25/tcp  open  smtp
53/tcp  open  domain
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

Scan duration:

```text
1.32 seconds
```

## Observation

The source-port manipulation scan produced the same results as the baseline SYN scan.

All eight selected ports were reported as open.

## Security Insight

Changing the source port did not produce a visible difference against this laboratory target.

This demonstrates an important point:

> A technique designed to test filtering behavior does not necessarily produce a different result when the target or network path does not apply rules based on the manipulated attribute.

The result should therefore be interpreted as an observation about this specific lab environment rather than proof that source-port manipulation is ineffective in all environments.

---

# 13. Bad TCP Checksums

## Command

```bash
sudo nmap -sS --badsum -p 21,22,23,25,53,80 192.168.43.56
```

## Result

```text
PORT   STATE     SERVICE
21/tcp filtered  ftp
22/tcp filtered  ssh
23/tcp filtered  telnet
25/tcp filtered  smtp
53/tcp filtered  domain
80/tcp filtered  http
```

Scan duration:

```text
2.33 seconds
```

## Observation

Unlike the baseline SYN scan, all six tested ports were reported as:

```text
filtered
```

This was the most significant change observed during the scenario.

The baseline SYN scan identified the same services as open, whereas the invalid-checksum scan resulted in filtered classifications.

## Security Insight

The result demonstrates how malformed packets can produce substantially different Nmap classifications.

The scan does not prove that a firewall was responsible for the result.

The observed behavior may involve packet validation or rejection by the target operating system, network stack, or an intermediate filtering device.

Therefore, the appropriate conclusion from the laboratory evidence is:

**Invalid TCP checksum probes did not receive responses that allowed Nmap to classify the tested ports as open.**

---

# 14. MAC Address Spoofing

## Command

```bash
sudo nmap -sS --spoof-mac 0 -p 21,22,23,25,53,80 192.168.43.56
```

## Result

Nmap generated a random MAC address:

```text
Spoofing MAC address 4E:99:9D:B5:8C:51 (No registered vendor)
```

The scan then reported:

```text
PORT   STATE     SERVICE
21/tcp filtered  ftp
22/tcp filtered  ssh
23/tcp filtered  telnet
25/tcp filtered  smtp
53/tcp filtered  domain
80/tcp filtered  http
```

The target MAC reported by Nmap was:

```text
44:85:00:63:0A:5B (Intel Corporate)
```

Scan duration:

```text
2.19 seconds
```

## Observation

The MAC-spoofed scan produced a different result from the baseline SYN scan.

The baseline reported:

```text
21/tcp open
22/tcp open
23/tcp open
25/tcp open
53/tcp open
80/tcp open
```

The MAC-spoofed scan reported:

```text
21/tcp filtered
22/tcp filtered
23/tcp filtered
25/tcp filtered
53/tcp filtered
80/tcp filtered
```

## Important Interpretation

The output confirms that Nmap successfully attempted MAC address spoofing.

However, the result does **not** establish exactly why the ports became filtered.

The change could be related to the behavior of the local network, ARP handling, VirtualBox bridging, the target's response path, or another component of the laboratory environment.

Therefore, this result should be documented as an observed laboratory behavior rather than a definitive demonstration of MAC-based filtering.

---

# 15. Baseline SYN Scan

## Command

```bash
sudo nmap -sS -p 21,22,23,25,53,80,139,445 192.168.43.56
```

## Result

```text
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
23/tcp  open  telnet
25/tcp  open  smtp
53/tcp  open  domain
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

Scan duration:

```text
1.06 seconds
```

This scan served as the primary comparison point for the other techniques.

---

# 16. Comparative Results

| Technique | Ports Tested | Result | Time |
|---|---|---|---:|
| ACK | 1–1000 | 1000 unfiltered | 1.32 sec |
| FIN | 1–1000 | 12 open\|filtered, 988 closed | 2.43 sec |
| NULL | 1–1000 | 12 open\|filtered, 988 closed | 2.38 sec |
| Xmas | 1–1000 | 12 open\|filtered, 988 closed | 3.53 sec |
| Fragmentation | 8 selected | 8 open | 1.50 sec |
| Custom MTU | 8 selected | 8 open | 1.45 sec |
| Decoy | 6 selected | 6 open | 1.34 sec |
| Source Port 53 | 8 selected | 8 open | 1.32 sec |
| Bad Checksums | 6 selected | 6 filtered | 2.33 sec |
| MAC Spoofing | 6 selected | 6 filtered | 2.19 sec |
| Baseline SYN | 8 selected | 8 open | 1.06 sec |

---

# 17. Key Findings

## F-01 — Alternative TCP Scans Produce Ambiguous Port States

**Risk Level: Informational**

FIN, NULL, and Xmas scans consistently reported the same 12 ports as:

```text
open|filtered
```

rather than definitively identifying them as open.

This demonstrates that alternative TCP scanning techniques can provide useful information while also introducing uncertainty into port-state interpretation.

### Recommendation

Security analysts should correlate alternative scan results with standard SYN or TCP Connect scans before concluding that a service is exposed.

---

## F-02 — ACK Scanning Revealed Unfiltered Ports

**Risk Level: Informational**

The ACK scan reported all 1,000 tested ports as unfiltered.

This indicates that the ACK probes received responses and were not silently blocked in the tested environment.

### Recommendation

Use ACK scans alongside SYN scans when investigating firewall and packet-filtering behavior.

---

## F-03 — Fragmentation Did Not Change Results in This Lab

**Risk Level: Informational**

Both fragmented SYN scanning and custom MTU scanning identified the same eight selected ports as open.

### Recommendation

Treat fragmentation as a technique for testing packet-handling behavior rather than assuming it will automatically produce different results.

---

## F-04 — Source Port Manipulation Produced No Observable Difference

**Risk Level: Informational**

The scan using source port 53 produced the same results as the baseline SYN scan.

### Recommendation

When assessing firewall behavior, compare manipulated-source-port scans with normal scans and document whether the network actually responds differently.

---

## F-05 — Invalid Checksums Changed Port Classification

**Risk Level: Informational**

The bad-checksum scan changed all six tested ports from `open` in the baseline scan to `filtered`.

This demonstrates that malformed traffic can produce substantially different scan results.

### Recommendation

When unusual scan results are observed, verify whether packet construction or network filtering is responsible before concluding that a service is unavailable.

---

## F-06 — MAC Spoofing Changed the Observed Results

**Risk Level: Informational**

The MAC-spoofed scan successfully generated a random MAC address and resulted in the six tested ports being classified as filtered.

However, the available evidence does not establish the exact reason for this behavior.

### Recommendation

MAC-address manipulation should be tested in controlled environments and its results interpreted alongside ARP, switching, virtualization, and network-path behavior.

---

# 18. Security Analysis

The primary lesson from this scenario is that **Nmap's results are strongly influenced by the type of probe being used**.

The same target produced different classifications depending on the scanning technique.

For example:

### Normal SYN Scan

```text
21/tcp open ftp
22/tcp open ssh
23/tcp open telnet
25/tcp open smtp
53/tcp open domain
80/tcp open http
```

### FIN / NULL / Xmas

```text
21/tcp open|filtered ftp
22/tcp open|filtered ssh
23/tcp open|filtered telnet
25/tcp open|filtered smtp
53/tcp open|filtered domain
80/tcp open|filtered http
```

### Bad Checksum

```text
21/tcp filtered ftp
22/tcp filtered ssh
23/tcp filtered telnet
25/tcp filtered smtp
53/tcp filtered domain
80/tcp filtered http
```

Therefore, a security analyst should avoid interpreting an individual scan result in isolation.

---

# 19. Attack Surface Implications

The scenario also reinforces the broad attack surface already identified during the previous assessments.

The target exposed services including:

- FTP
- SSH
- Telnet
- SMTP
- DNS
- HTTP
- RPC
- NetBIOS
- SMB

Alternative scanning techniques did not remove these services from the network.

Instead, they changed how the services were observed and classified.

This distinction is important:

**Scan-evasion techniques affect reconnaissance behavior and packet construction; they do not inherently change the security state of the underlying service.**

---

# 20. Evidence Summary

The following evidence was collected during the assessment.

### ACK Scan

```text
1000 unfiltered tcp ports (reset)
```

### FIN Scan

```text
12 open|filtered
988 closed
```

### NULL Scan

```text
12 open|filtered
988 closed
```

### Xmas Scan

```text
12 open|filtered
988 closed
```

### Fragmentation

```text
8 open
```

### Custom MTU

```text
8 open
```

### Decoy

```text
6 open
```

### Source Port Manipulation

```text
8 open
```

### Bad Checksums

```text
6 filtered
```

### MAC Spoofing

```text
6 filtered
```

### Baseline

```text
8 open
```

---

# 21. Limitations

Several limitations should be considered when interpreting the results.

### 1. Controlled Laboratory Environment

The target was a deliberately vulnerable Metasploitable 2 virtual machine running inside a VirtualBox laboratory environment.

Results may differ substantially on modern production systems.

### 2. No Dedicated Firewall Appliance

The results demonstrate packet and TCP/IP behavior observed in the laboratory, but they do not establish the behavior of a specific commercial firewall.

### 3. Limited Port Selection

Some techniques were tested against selected ports rather than the entire TCP range to reduce unnecessary scan time.

### 4. MAC Spoofing Result

The MAC-spoofed scan produced filtered results, but the available scan output does not identify the exact mechanism responsible for the change.

### 5. Bad Checksum Result

The filtered results demonstrate a change in Nmap classification but do not independently prove that a firewall caused the filtering.

### 6. Decoy Verification

The scan successfully executed with decoys, but this assessment did not include packet capture or external logging analysis to determine exactly how the decoy traffic appeared to a monitoring system.

---

# 22. Lessons Learned

This scenario demonstrated several practical lessons.

### Lesson 1 — Port states depend on scan technique

The same target can produce:

```text
open
open|filtered
filtered
unfiltered
```

depending on the probe being used.

### Lesson 2 — `open|filtered` is not the same as `open`

FIN, NULL, and Xmas scans produced `open|filtered` results.

These should not be treated as definitive evidence that the service is accessible.

### Lesson 3 — ACK scans are useful for filtering analysis

The ACK scan showed all 1,000 tested ports as unfiltered, providing information that was different from the SYN scan.

### Lesson 4 — Manipulation techniques require baseline comparisons

Fragmentation, custom MTU, source-port manipulation, bad checksums, and MAC spoofing were much easier to interpret because their results could be compared with a normal SYN scan.

### Lesson 5 — Unexpected results require cautious interpretation

The bad-checksum and MAC-spoofing scans produced filtered results.

However, the scan output alone does not identify the precise cause.

A professional assessment should avoid making unsupported assumptions.

---

# 23. Conclusion

Scenario 03 demonstrated how Nmap can be used to study firewall filtering, TCP probe behavior, packet manipulation, and reconnaissance techniques in an authorized laboratory environment.

The most significant observation was that changing the structure or characteristics of the probe could significantly change Nmap's interpretation of the target.

FIN, NULL, and Xmas scans produced `open|filtered` results, while ACK scanning classified the tested ports as unfiltered.

Fragmentation, custom MTU, decoy scanning, and source-port manipulation produced results consistent with the baseline SYN scan.

In contrast, invalid TCP checksums and MAC-address spoofing resulted in filtered classifications for the tested ports.

The scenario therefore reinforces a fundamental network-security principle:

> **Nmap results must be interpreted in context.**

A single scan technique does not provide a complete picture of a target. Security analysts should compare multiple scan types, understand the meaning of each port state, and consider network-path and packet-handling behavior before drawing conclusions.

---

# 24. Related Documentation

### Command Reference

[`../Commands/Nmap-Command-Reference.md`](../Commands/Nmap-Command-Reference.md)

### Scenario 01

[`01-Host-Discovery-and-Network-Mapping.md`](../Practical%20Scanning%20Scenarios/01-Host-Discovery-and-Network-Mapping.md)

### Scenario 02

[`02-TCP-and-UDP-Port-Scanning-Assessment.md`](../Case-Studies/02-TCP-and-UDP-Port-Scanning-Assessment.md)

### Case Study 01

[`01-Metasploitable2-Security-Assessment.md`](../Case-Studies/01-Metasploitable2-Security-Assessment.md)

---

# 25. Metadata

| Field | Value |
|---|---|
| Project | Nmap Network Scanning Portfolio |
| Scenario | 03 |
| Topic | Firewall Evasion & Filtering Analysis |
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Nmap Version | 7.99 |
| Environment | VirtualBox Laboratory |
| Assessment Type | Authorized Security Assessment |
| Focus | Firewall behavior, filtering, TCP probes and packet manipulation |
| Evidence | Actual laboratory scan outputs |
| Status | Completed |
```
