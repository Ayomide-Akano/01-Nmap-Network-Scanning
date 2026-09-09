# Case Study 03 — Firewall Evasion & Filtering Analysis

## Executive Summary

This case study documents an authorized security assessment of a Metasploitable 2 laboratory system focused on firewall behavior, TCP filtering, packet manipulation, and alternative Nmap scanning techniques.

The assessment examined multiple scanning approaches, including ACK, FIN, NULL, Xmas, fragmented SYN, custom MTU, decoy, source-port manipulation, invalid TCP checksum, and MAC address spoofing scans.

The objective was to understand how different probe types affect Nmap's interpretation of TCP ports and to identify differences between standard scanning and alternative scanning techniques.

The assessment demonstrated that the same target can produce significantly different results depending on the scanning technique used.

The ACK scan reported all 1,000 tested ports as unfiltered. FIN, NULL, and Xmas scans identified 12 ports as `open|filtered`, while the baseline SYN scan identified selected services as open.

Fragmentation, custom MTU, decoy scanning, and source-port manipulation produced results consistent with the baseline SYN scan in this laboratory environment.

Invalid checksum and MAC-spoofed scans produced `filtered` results for the tested ports.

The results reinforce the importance of understanding Nmap port-state classifications and comparing multiple scan techniques before drawing security conclusions.

---

# 1. Assessment Overview

## Assessment Objective

The objective of this assessment was to investigate:

- TCP filtering behavior
- Alternative TCP scanning techniques
- Firewall-related port-state interpretation
- Packet fragmentation
- Custom MTU behavior
- Decoy scanning
- Source-port manipulation
- Invalid packet handling
- MAC address spoofing
- Differences between alternative scans and a standard SYN scan

The assessment was performed strictly within an authorized laboratory environment.

---

# 2. Scope

## Target

| Parameter | Value |
|---|---|
| Target | Metasploitable 2 |
| IP Address | `192.168.43.56` |
| Network | `192.168.43.0/24` |
| Environment | VirtualBox laboratory |
| Assessment Type | Authorized security assessment |
| Primary Tool | Nmap 7.99 |

## Testing Scope

The following Nmap techniques were evaluated:

1. ACK scan
2. FIN scan
3. NULL scan
4. Xmas scan
5. Fragmented SYN scan
6. Custom MTU
7. Decoy scan
8. Source-port manipulation
9. Bad TCP checksum
10. MAC address spoofing

A standard SYN scan against selected ports was used as the primary comparison baseline.

---

# 3. Methodology

The assessment followed a comparative scanning methodology.

First, the target was scanned using a standard SYN scan to establish the expected port states.

Alternative scanning techniques were then performed against the same target.

The results were compared based on:

- Port state
- Number of ports identified
- Services identified
- Scan duration
- Differences from the baseline
- Observed network behavior

This approach allowed the assessment to distinguish between:

- Confirmed open services
- Filtered ports
- Unfiltered ports
- Ambiguous `open|filtered` results
- Changes caused by different packet characteristics

---

# 4. Baseline Assessment

The baseline scan was:

```bash
sudo nmap -sS -p 21,22,23,25,53,80,139,445 192.168.43.56
```

The result was:

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

The scan completed in:

```text
1.06 seconds
```

This result was used as the comparison point for the alternative scanning techniques.

---

# 5. ACK Scan Analysis

## Command

```bash
sudo nmap -sA -p 1-1000 192.168.43.56
```

## Result

The scan reported:

```text
All 1000 scanned ports on 192.168.43.56 are in ignored states.
Not shown: 1000 unfiltered tcp ports (reset)
```

The scan completed in:

```text
1.32 seconds
```

## Assessment

The ACK scan classified all 1,000 tested TCP ports as unfiltered.

This indicates that the ACK probes received responses rather than being silently filtered.

The result does not indicate that the ports were open.

Instead, it provides information about the filtering behavior observed by the ACK probes.

## Security Significance

ACK scans can provide useful information when investigating packet-filtering behavior.

In this laboratory environment, the result suggests that the tested ports were reachable by the ACK probes and were not being silently filtered.

---

# 6. FIN Scan Analysis

## Command

```bash
sudo nmap -sF -p 1-1000 192.168.43.56
```

## Result

The scan identified:

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

Duration:

```text
2.43 seconds
```

## Assessment

The FIN scan identified the same 12 ports that appeared within the first 1,000 TCP ports during previous scanning exercises.

However, the ports were classified as:

```text
open|filtered
```

rather than `open`.

This means Nmap could not definitively determine whether the ports were open or filtered using the FIN probe.

---

# 7. NULL Scan Analysis

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

Duration:

```text
2.38 seconds
```

## Assessment

The NULL scan produced the same port-state pattern as the FIN scan:

- 988 closed ports
- 12 `open|filtered` ports

This demonstrates that alternative TCP probes can produce ambiguous port-state classifications even when the same services are known to be open from a standard SYN scan.

---

# 8. Xmas Scan Analysis

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

Duration:

```text
3.53 seconds
```

## Assessment

The Xmas scan produced the same result as the FIN and NULL scans.

The 12 identified ports were classified as:

```text
open|filtered
```

The remaining 988 ports were reported as closed.

The Xmas scan was also the slowest of the three alternative TCP scans tested.

---

# 9. Alternative TCP Scan Comparison

| Technique | Closed | Open\|Filtered | Duration |
|---|---:|---:|---:|
| FIN | 988 | 12 | 2.43 sec |
| NULL | 988 | 12 | 2.38 sec |
| Xmas | 988 | 12 | 3.53 sec |

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

## Assessment

The consistency of these results demonstrates that the three alternative TCP scan types produced similar classifications against the target.

However, `open|filtered` should not be interpreted as confirmation that the services are open.

The standard SYN scan provides stronger evidence of an open service in this assessment.

---

# 10. Fragmentation Analysis

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

Duration:

```text
1.50 seconds
```

## Assessment

The fragmented SYN scan identified all eight selected ports as open.

The result matched the baseline SYN scan.

Therefore, fragmentation did not produce an observable change in port-state classification against this target.

---

# 11. Custom MTU Analysis

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

Duration:

```text
1.45 seconds
```

## Assessment

The custom MTU scan produced the same result as both the baseline SYN scan and fragmented SYN scan.

All eight selected ports were identified as open.

This demonstrates that changing the MTU did not produce a different observable port-state result in this laboratory environment.

---

# 12. Decoy Scan Analysis

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

Duration:

```text
1.34 seconds
```

## Assessment

The decoy scan successfully identified all six selected services as open.

The result was consistent with the corresponding baseline scan.

The assessment confirms that the decoy scan executed successfully in the laboratory.

However, this assessment did not include packet capture, firewall logs, IDS/IPS logs, or external monitoring evidence.

Therefore, the results cannot establish how effective the decoy technique would be against a specific monitoring system.

---

# 13. Source Port Manipulation Analysis

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

Duration:

```text
1.32 seconds
```

## Assessment

The source-port manipulation scan produced the same results as the baseline SYN scan.

All eight selected ports were reported as open.

No observable difference was identified in the laboratory environment.

This demonstrates that manipulating the source port does not necessarily change the result when the network path does not apply filtering rules based on the manipulated source port.

---

# 14. Invalid TCP Checksum Analysis

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

Duration:

```text
2.33 seconds
```

## Assessment

The invalid-checksum scan produced a significant difference from the baseline.

The baseline SYN scan identified the tested services as open.

The bad-checksum scan classified all six tested ports as filtered.

This demonstrates that malformed packets can result in substantially different Nmap classifications.

However, the result alone does not prove that a firewall caused the filtering.

The behavior could involve packet validation or rejection by the target's network stack or another component along the network path.

---

# 15. MAC Address Spoofing Analysis

## Command

```bash
sudo nmap -sS --spoof-mac 0 -p 21,22,23,25,53,80 192.168.43.56
```

## Nmap Output

Nmap reported:

```text
Spoofing MAC address 4E:99:9D:B5:8C:51 (No registered vendor)
```

The scan then produced:

```text
PORT   STATE     SERVICE
21/tcp filtered  ftp
22/tcp filtered  ssh
23/tcp filtered  telnet
25/tcp filtered  smtp
53/tcp filtered  domain
80/tcp filtered  http
```

Nmap reported the target MAC as:

```text
44:85:00:63:0A:5B (Intel Corporate)
```

Duration:

```text
2.19 seconds
```

## Assessment

The scan successfully attempted MAC-address spoofing.

Unlike the baseline scan, all six tested ports were classified as filtered.

This represents a significant change in observed results.

However, the scan output does not identify the precise reason for this behavior.

The result may involve interactions between:

- MAC address changes
- ARP behavior
- VirtualBox networking
- Bridged networking
- The target's network stack
- Network-path behavior

Therefore, the assessment records this as an observed laboratory behavior rather than definitive proof of MAC-based filtering.

---

# 16. Comparative Assessment

| Scan Technique | Result | Comparison |
|---|---|---|
| ACK | 1,000 unfiltered | Different from SYN |
| FIN | 12 open\|filtered | Ambiguous compared with SYN |
| NULL | 12 open\|filtered | Ambiguous compared with SYN |
| Xmas | 12 open\|filtered | Ambiguous compared with SYN |
| Fragmented SYN | 8 open | Same as baseline |
| Custom MTU | 8 open | Same as baseline |
| Decoy | 6 open | Same as baseline |
| Source Port 53 | 8 open | Same as baseline |
| Bad Checksums | 6 filtered | Different from baseline |
| MAC Spoofing | 6 filtered | Different from baseline |
| Baseline SYN | 8 open | Reference |

---

# 17. Findings

## F-01 — Alternative TCP Scans Produced Ambiguous Port States

**Risk Level: Informational**

FIN, NULL, and Xmas scans consistently classified 12 ports as `open|filtered`.

The standard SYN scan identified corresponding services as open.

### Security Impact

An analyst relying exclusively on alternative TCP scan results could incorrectly interpret an exposed service as uncertain or filtered.

### Recommendation

Use alternative scan types alongside standard SYN or Connect scans and correlate results before determining the actual exposure of a service.

---

## F-02 — ACK Scan Demonstrated Unfiltered TCP Ports

**Risk Level: Informational**

The ACK scan reported all 1,000 tested ports as unfiltered.

### Security Impact

The result provides information about the target's response to ACK probes and indicates that the tested traffic was not silently filtered.

### Recommendation

Use ACK scans as a supplementary technique when investigating firewall and packet-filtering behavior.

---

## F-03 — Packet Manipulation Did Not Necessarily Change Service Visibility

**Risk Level: Informational**

Fragmented SYN, custom MTU, decoy, and source-port scans produced results consistent with the baseline SYN scan.

### Security Impact

The laboratory results demonstrate that changing packet construction or source characteristics does not automatically change the target's observed service exposure.

### Recommendation

Always compare alternative scan techniques with a known baseline instead of assuming that a particular technique will produce different results.

---

## F-04 — Malformed Packets Produced Different Port Classifications

**Risk Level: Informational**

The bad-checksum scan classified all six tested ports as filtered, despite the baseline identifying them as open.

### Security Impact

Malformed traffic can produce different reconnaissance results from normal probes.

### Recommendation

When analyzing unusual scan results, investigate packet handling and filtering behavior before concluding that a service is unavailable.

---

## F-05 — MAC Spoofing Produced Different Scan Results

**Risk Level: Informational**

MAC-address spoofing was successfully attempted and produced filtered classifications for the six tested ports.

### Security Impact

The result demonstrates that changing the source MAC address can affect observed scanning behavior in a virtualized network environment.

### Recommendation

When conducting authorized assessments involving MAC manipulation, correlate Nmap results with ARP tables, packet captures, switch logs, and virtualization configuration where available.

---

# 18. Risk Summary

| Finding | Risk |
|---|---|
| Alternative TCP scan ambiguity | Informational |
| ACK filtering observation | Informational |
| Packet manipulation behavior | Informational |
| Malformed packet response | Informational |
| MAC spoofing behavior | Informational |

These findings describe the behavior of scanning techniques rather than confirmed vulnerabilities in the target.

The underlying service exposure identified in previous assessments remains the more significant security concern.

---

# 19. Security Analysis

The most important result from this assessment is the difference between **what a scan reports** and **what the target actually exposes**.

For example, the baseline SYN scan reported:

```text
21/tcp open ftp
22/tcp open ssh
23/tcp open telnet
25/tcp open smtp
53/tcp open domain
80/tcp open http
```

The FIN, NULL, and Xmas scans instead reported:

```text
21/tcp open|filtered
22/tcp open|filtered
23/tcp open|filtered
25/tcp open|filtered
53/tcp open|filtered
80/tcp open|filtered
```

The bad-checksum scan reported:

```text
21/tcp filtered
22/tcp filtered
23/tcp filtered
25/tcp filtered
53/tcp filtered
80/tcp filtered
```

The underlying target had not necessarily changed between these scans.

The probe itself had changed.

This demonstrates why security professionals must understand Nmap's scanning methodology before interpreting results.

---

# 20. Defensive Considerations

Organizations should not rely on a single firewall rule or filtering mechanism to prevent reconnaissance.

Defensive monitoring should consider:

- Stateful firewall policies
- Network segmentation
- IDS/IPS monitoring
- Centralized logging
- SIEM correlation
- Detection of unusual TCP flag combinations
- Detection of malformed packets
- Monitoring for abnormal scanning patterns
- MAC-address and ARP anomaly monitoring

Alternative scan techniques may produce different network traffic patterns, making behavioral detection important.

---

# 21. Limitations

### Laboratory Environment

The assessment was conducted against Metasploitable 2 in a VirtualBox laboratory environment.

### No Dedicated Firewall Analysis

The assessment did not use a dedicated firewall appliance or firewall log collection.

Therefore, filtering behavior observed in Nmap output cannot automatically be attributed to a firewall.

### No Packet Capture

Packet captures were not collected during the assessment.

A packet capture would provide additional evidence regarding exactly how each probe was handled.

### No IDS/IPS Logs

No external IDS, IPS, SIEM, or firewall logs were available to validate the behavior of the decoy or other manipulation techniques.

### Limited Port Selection

Several techniques were tested against selected ports rather than the complete TCP port range to keep the assessment efficient.

### Virtualized Networking

MAC spoofing results may have been influenced by the VirtualBox bridged networking environment.

---

# 22. Lessons Learned

### 1. Scan type matters

Different Nmap techniques can produce different classifications for the same target.

### 2. `open|filtered` requires caution

An `open|filtered` result does not prove that a port is open.

### 3. ACK scans provide different information

ACK scanning is useful for studying filtering behavior rather than directly identifying open services.

### 4. Baselines are essential

Alternative scanning techniques become much more useful when compared against a standard scan.

### 5. Unexpected results require investigation

Filtered results from malformed packets or MAC spoofing should not automatically be attributed to a firewall.

### 6. Evidence should determine conclusions

The assessment demonstrates the importance of distinguishing observed behavior from assumptions about the underlying cause.

---

# 23. Conclusion

This assessment demonstrated how Nmap's scanning techniques can affect the visibility and classification of network services.

The target produced consistent results across FIN, NULL, and Xmas scans, with 12 ports classified as `open|filtered`.

The ACK scan provided a different perspective by reporting all 1,000 tested ports as unfiltered.

Fragmentation, custom MTU, decoy scanning, and source-port manipulation produced results consistent with the baseline SYN scan.

Invalid checksum and MAC-spoofing scans produced filtered results, demonstrating that changes in packet characteristics can significantly affect the observed result.

The assessment therefore reinforces a fundamental principle of network security assessment:

> **Nmap output must be interpreted according to the scanning technique that generated it.**

No individual scan should be treated as a complete representation of the target's security posture.

A reliable assessment should combine multiple scanning techniques, compare results against baselines, and investigate unexpected classifications before drawing conclusions.

---

# 24. Evidence

The primary evidence for this assessment consists of the Nmap scan outputs collected during the laboratory exercises.

Techniques tested:

```text
-sA
-sF
-sN
-sX
-f
--mtu 24
-D RND:5
--source-port 53
--badsum
--spoof-mac 0
```

Target:

```text
192.168.43.56
```

Nmap version:

```text
7.99
```

---

# 25. Related Documentation

### Practical Scenario

[`../Practical Scanning Scenarios/03-Firewall-Evasion-and-Filtering-Analysis.md`](../Practical%20Scanning%20Scenarios/03-Firewall-Evasion-and-Filtering-Analysis.md)

### Command Reference

[`../Commands/Nmap-Command-Reference.md`](../Commands/Nmap-Command-Reference.md)

### Scenario 01 Case Study

[`01-Metasploitable2-Security-Assessment.md`](01-Metasploitable2-Security-Assessment.md)

### Scenario 02 Case Study

[`02-TCP-and-UDP-Port-Scanning-Assessment.md`](02-TCP-and-UDP-Port-Scanning-Assessment.md)

---

# 26. Metadata

| Field | Value |
|---|---|
| Project | Nmap Network Scanning Portfolio |
| Case Study | 03 |
| Assessment Topic | Firewall Evasion & Filtering Analysis |
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Nmap Version | 7.99 |
| Environment | VirtualBox Laboratory |
| Assessment Type | Authorized Security Assessment |
| Evidence Type | Actual Nmap Scan Results |
| Status | Completed |
```
