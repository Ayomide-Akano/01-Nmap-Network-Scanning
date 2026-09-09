# Scenario 04 — Scan Optimization, Timing & Output Formats

## Executive Summary

This practical scenario evaluates how Nmap scan performance and output behavior change when different timing, rate-control, retry, delay, verbosity, and output-format options are applied.

The assessment was conducted against the authorized Metasploitable 2 laboratory target at `192.168.43.56`.

The tests compared:

- Baseline SYN scanning
- Timing templates (`-T3` and `-T4`)
- Scan delays
- Retry and host-timeout controls
- Maximum packet rates
- Verbose output and scan reasons
- Normal output
- XML output
- Grepable output

The results showed that the different timing templates produced nearly identical results and very similar execution times in this local laboratory environment. Rate limiting had a much more visible effect on scan duration, with `--max-rate 100` taking significantly longer than `--max-rate 500`.

The output-format tests successfully generated Normal, XML, and Grepable Nmap output files, demonstrating how scan results can be preserved in different formats for documentation, automation, and analysis.

---

## 1. Assessment Overview

### Objective

The objective of this scenario was to understand how Nmap scan optimization and timing controls affect:

1. Scan duration
2. Scan consistency
3. Network traffic rate
4. Result visibility
5. Evidence collection
6. Output preservation

The scenario also examined how Nmap can save scan results in multiple formats for later analysis and reporting.

---

## 2. Lab Environment

| Component | Details |
|---|---|
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Scanner | Linux system |
| Scanner IP | `192.168.43.155` |
| Nmap Version | 7.99 |
| Network Type | Local laboratory network |
| Scan Type | TCP SYN (`-sS`) |
| Primary Port Range | `1-1000` |

All testing was performed within the authorized laboratory environment.

---

# 3. Methodology

The assessment followed a controlled comparison approach.

A baseline SYN scan was first performed against TCP ports `1-1000`.

The same scan scope was then tested using:

- `-T3`
- `-T4`
- `--scan-delay 100ms`
- `--max-retries 1`
- `--host-timeout 30s`
- `--max-rate 100`
- `--max-rate 500`

A smaller six-port scan was also performed with verbose output and `--reason` to observe how Nmap explains discovered port states.

Finally, the baseline scan results were exported using:

- Normal output (`-oN`)
- XML output (`-oX`)
- Grepable output (`-oG`)

---

# 4. Baseline Scan

## Command

```bash
sudo nmap -sS -p 1-1000 192.168.43.56
```

## Result

The baseline scan identified 12 open TCP ports and 988 closed ports.

| Port | State | Service |
|---|---|---|
| 21/tcp | open | ftp |
| 22/tcp | open | ssh |
| 23/tcp | open | telnet |
| 25/tcp | open | smtp |
| 53/tcp | open | domain |
| 80/tcp | open | http |
| 111/tcp | open | rpcbind |
| 139/tcp | open | netbios-ssn |
| 445/tcp | open | microsoft-ds |
| 512/tcp | open | exec |
| 513/tcp | open | login |
| 514/tcp | open | shell |

### Performance

- Host latency: `0.00059s`
- Scan duration: **1.08 seconds**
- MAC address: `08:00:27:DB:2A:1B`
- Vendor: Oracle VirtualBox virtual NIC

### Baseline Observation

The baseline establishes a reference point for comparing the effects of timing and optimization parameters.

---

# 5. Timing Template Comparison

Nmap provides timing templates that adjust multiple internal timing parameters.

Two timing levels were tested:

- `-T3` — Normal timing
- `-T4` — Aggressive timing

## 5.1 Normal Timing — T3

### Command

```bash
sudo nmap -sS -T3 -p 1-1000 192.168.43.56
```

### Result

The same 12 open TCP ports were discovered.

### Performance

- Host latency: `0.0023s`
- Scan duration: **1.14 seconds**
- Closed ports: 988

### Observation

Compared with the baseline:

- Open ports remained unchanged.
- Closed ports remained unchanged.
- Scan duration increased slightly from 1.08 seconds to 1.14 seconds.

This indicates that `-T3` did not materially change the result set in this local environment.

---

## 5.2 Aggressive Timing — T4

### Command

```bash
sudo nmap -sS -T4 -p 1-1000 192.168.43.56
```

### Result

The same 12 open TCP ports were discovered.

### Performance

- Host latency: `0.0013s`
- Scan duration: **1.02 seconds**
- Closed ports: 988

### Observation

The `-T4` scan completed slightly faster than both the baseline and `-T3` scan.

| Scan | Duration |
|---|---:|
| Baseline | 1.08s |
| `-T3` | 1.14s |
| `-T4` | 1.02s |

The results were identical across all three scans.

### Assessment

In this local laboratory environment, the difference between the timing templates was small. The `-T4` scan was the fastest of the three tests, but the improvement was only a fraction of a second.

This demonstrates that timing optimization may have a more noticeable effect in larger or higher-latency environments than in a small local virtualized network.

---

# 6. Scan Delay

## Command

```bash
sudo nmap -sS --scan-delay 100ms -p 21,22,23,25,53,80 192.168.43.56
```

## Result

All six selected TCP ports were reported as open.

| Port | State | Service |
|---|---|---|
| 21/tcp | open | ftp |
| 22/tcp | open | ssh |
| 23/tcp | open | telnet |
| 25/tcp | open | smtp |
| 53/tcp | open | domain |
| 80/tcp | open | http |

### Performance

- Host latency: `0.00083s`
- Scan duration: **1.46 seconds**

### Observation

The six selected ports were detected successfully.

The scan took longer than the smaller six-port scan performed without the delay in the later verbose test, demonstrating the performance cost associated with deliberately slowing probe transmission.

### Assessment

`--scan-delay 100ms` can be useful when a tester intentionally wants to reduce the pace of probing, but it increases scan duration.

---

# 7. Retry and Host Timeout Controls

## Command

```bash
sudo nmap -sS --max-retries 1 --host-timeout 30s -p 1-1000 192.168.43.56
```

## Result

The same 12 open TCP ports were identified.

### Performance

- Host latency: `0.0013s`
- Scan duration: **0.92 seconds**
- Closed ports: 988

### Observation

The scan completed faster than the baseline while producing the same observed port results.

This demonstrates that reducing the maximum number of retransmission attempts can reduce scanning time when the target responds consistently.

### Assessment

Retry and timeout controls can be useful for performance-sensitive assessments, particularly when the target is reliable and responsive.

However, aggressive reduction of retries can increase the possibility of missing results in environments experiencing packet loss or unstable connectivity.

---

# 8. Maximum Packet Rate Comparison

Two maximum packet-rate values were tested.

## 8.1 Maximum Rate — 100 Packets/Second

### Command

```bash
sudo nmap -sS --max-rate 100 -p 1-1000 192.168.43.56
```

### Result

The same 12 open TCP ports were discovered.

### Performance

- Host latency: `0.0012s`
- Scan duration: **10.87 seconds**
- Closed ports: 988

---

## 8.2 Maximum Rate — 500 Packets/Second

### Command

```bash
sudo nmap -sS --max-rate 500 -p 1-1000 192.168.43.56
```

### Result

The same 12 open TCP ports were discovered.

### Performance

- Host latency: `0.00076s`
- Scan duration: **2.83 seconds**
- Closed ports: 988

---

## 8.3 Rate Comparison

| Maximum Rate | Duration | Open Ports |
|---|---:|---:|
| `--max-rate 100` | 10.87s | 12 |
| `--max-rate 500` | 2.83s | 12 |

### Observation

The difference was significant.

Increasing the configured maximum rate from 100 to 500 packets per second reduced the observed scan duration from **10.87 seconds to 2.83 seconds**.

Both scans produced the same port results.

### Assessment

The test demonstrates the direct performance impact of rate limiting.

A lower maximum packet rate can deliberately slow scanning, while a higher rate can significantly reduce scan duration when the target and network can handle the traffic.

---

# 9. Verbose Output and Scan Reasons

## Command

```bash
sudo nmap -sS -v --reason -p 21,22,23,25,53,80 192.168.43.56
```

## Result

All six selected ports were identified as open.

| Port | State | Service | Reason |
|---|---|---|---|
| 21/tcp | open | ftp | syn-ack ttl 64 |
| 22/tcp | open | ssh | syn-ack ttl 64 |
| 23/tcp | open | telnet | syn-ack ttl 64 |
| 25/tcp | open | smtp | syn-ack ttl 64 |
| 53/tcp | open | domain | syn-ack ttl 64 |
| 80/tcp | open | http | syn-ack ttl 64 |

### Additional Information

Nmap reported:

- ARP host discovery
- SYN stealth scanning
- Discovery of each open port
- Scan completion information
- Raw packets sent: 7
- Raw packets received: 7

### Performance

- Host latency: `0.00097s`
- Scan duration: **1.04 seconds**

### Assessment

The `-v` option provided additional scan progress information, while `--reason` showed why Nmap classified each tested port as open.

For the observed open ports, the reason was a `syn-ack` response with TTL 64.

This provides useful supporting evidence when documenting scan results.

---

# 10. Output Formats

The same `1-1000` SYN scan was exported into different Nmap output formats.

---

## 10.1 Normal Output

### Command

```bash
sudo nmap -sS -p 1-1000 -oN scenario4-normal.txt 192.168.43.56
```

### Result

The scan completed successfully and produced the expected 12 open ports.

The resulting file was verified with:

```bash
ls -lh scenario4-normal.txt
```

### File Information

```text
-rw-r--r-- 1 root root 662 Sep  9 13:50 scenario4-normal.txt
```

### File Size

**662 bytes**

### Assessment

Normal output preserves the human-readable Nmap scan format and is suitable for direct review and documentation.

---

# 11. XML Output

## Command

```bash
sudo nmap -sS -p 1-1000 -oX scenario4.xml 192.168.43.56
```

## Result

The scan completed successfully and produced the expected 12 open ports.

The resulting file was verified with:

```bash
ls -lh scenario4.xml
```

### File Information

```text
-rw-r--r-- 1 root root 3.3K Sep  9 13:52 scenario4.xml
```

### File Size

**3.3 KB**

### Assessment

The XML output provides structured scan data that can be used for later processing, analysis, or integration with tools that support Nmap XML.

---

# 12. Grepable Output

## Command

```bash
sudo nmap -sS -p 1-1000 -oG scenario4-grepable.txt 192.168.43.56
```

## Result

The scan completed successfully and produced the expected 12 open ports.

The resulting file was verified with:

```bash
ls -lh scenario4-grepable.txt
```

### File Information

```text
-rw-r--r-- 1 root root 605 Sep  9 13:53 scenario4-grepable.txt
```

### File Size

**605 bytes**

### Assessment

Grepable output provides a compact representation of scan information that can be useful for command-line processing and simple text-based analysis.

---

# 13. Output Evidence Summary

| Format | Command | File | Size |
|---|---|---|---:|
| Normal | `-oN` | `scenario4-normal.txt` | 662 bytes |
| XML | `-oX` | `scenario4.xml` | 3.3 KB |
| Grepable | `-oG` | `scenario4-grepable.txt` | 605 bytes |

All three output formats were successfully generated and verified.

---

# 14. Comparative Performance Analysis

The primary performance results are summarized below.

| Test | Configuration | Ports Tested | Duration | Open Ports |
|---|---|---:|---:|---:|
| Baseline | Default SYN | 1-1000 | 1.08s | 12 |
| Timing | `-T3` | 1-1000 | 1.14s | 12 |
| Timing | `-T4` | 1-1000 | 1.02s | 12 |
| Delay | `--scan-delay 100ms` | 6 | 1.46s | 6 |
| Retry/Timeout | `--max-retries 1` + `--host-timeout 30s` | 1-1000 | 0.92s | 12 |
| Rate | `--max-rate 100` | 1-1000 | 10.87s | 12 |
| Rate | `--max-rate 500` | 1-1000 | 2.83s | 12 |
| Verbose | `-v --reason` | 6 | 1.04s | 6 |

### Key Performance Observation

The strongest performance difference observed during this scenario came from maximum packet-rate control.

`--max-rate 100` took **10.87 seconds**, while `--max-rate 500` took **2.83 seconds**.

The timing templates produced much smaller differences:

- Baseline: 1.08s
- `-T3`: 1.14s
- `-T4`: 1.02s

---

# 15. Findings

## F-01 — Timing Templates Produced Similar Results

**Severity:** Informational

The baseline, `-T3`, and `-T4` scans all identified the same 12 open TCP ports.

The observed execution times were also very close.

### Evidence

| Scan | Duration |
|---|---:|
| Baseline | 1.08s |
| `-T3` | 1.14s |
| `-T4` | 1.02s |

### Assessment

The local laboratory environment was sufficiently responsive that timing-template differences had limited practical impact on this scan.

---

## F-02 — Packet Rate Has Significant Performance Impact

**Severity:** Informational

The configured maximum packet rate had a significant effect on scan duration.

### Evidence

- `--max-rate 100`: 10.87 seconds
- `--max-rate 500`: 2.83 seconds

### Assessment

Rate limiting can substantially influence scan performance.

The appropriate rate should therefore be selected according to the target environment, network conditions, and assessment requirements.

---

## F-03 — Retry Reduction Can Improve Scan Speed

**Severity:** Informational

The scan using:

```text
--max-retries 1 --host-timeout 30s
```

completed in **0.92 seconds**, compared with **1.08 seconds** for the baseline scan.

The same 12 open ports were identified.

### Assessment

Reducing retries can improve performance against responsive targets, but the approach may be less reliable in environments experiencing packet loss or intermittent connectivity.

---

## F-04 — Scan Delay Increases Scan Duration

**Severity:** Informational

The `--scan-delay 100ms` test completed in 1.46 seconds while testing only six ports.

### Assessment

Deliberately introducing a scan delay reduces the probing rate and therefore increases the time required to complete a scan.

---

## F-05 — Multiple Output Formats Support Evidence Collection

**Severity:** Informational

The assessment successfully generated:

- Human-readable Normal output
- Structured XML output
- Compact Grepable output

### Assessment

Preserving scan results in multiple formats improves the ability to review, analyze, process, and document assessment evidence.

---

# 16. Security Analysis

This scenario demonstrated that Nmap scanning is not only about identifying open ports. Scan configuration can significantly affect the speed, behavior, and usefulness of an assessment.

The results demonstrate several important operational considerations.

### Performance

Higher packet rates can significantly reduce scan duration, as demonstrated by the difference between the 100 and 500 packet-per-second tests.

### Reliability

Reducing retries may improve speed but can become less reliable when packets are lost.

### Controlled Scanning

Scan delays provide a mechanism for deliberately reducing the pace of probing.

### Evidence Collection

Verbose output and scan reasons provide additional context for interpreting results.

### Reporting

Saving scan results in multiple formats makes it easier to preserve evidence and reuse results during technical reporting.

---

# 17. Practical Recommendations

Based on the observed results:

1. Establish a baseline scan before applying optimization parameters.
2. Compare timing changes against the baseline rather than assuming faster is always better.
3. Use higher packet rates only when the network and target can safely handle the additional traffic.
4. Avoid excessively aggressive rate settings in production environments.
5. Use retry controls carefully where packet loss may affect scan accuracy.
6. Use scan delays when a slower probing rate is appropriate.
7. Use `-v` and `--reason` when additional scan evidence is useful.
8. Save important assessment results to persistent output files.
9. Use Normal output for human-readable documentation.
10. Use XML when structured scan data may need to be processed by other tools.
11. Use Grepable output when simple command-line text processing is required.
12. Preserve raw scan results as evidence alongside the final security assessment.

---

# 18. Evidence Collected

The following evidence was generated during the assessment:

| Evidence | Description |
|---|---|
| Baseline scan | Default SYN scan of ports 1-1000 |
| T3 scan | Normal timing comparison |
| T4 scan | Aggressive timing comparison |
| Scan-delay test | 100ms delay on selected ports |
| Retry/timeout test | Maximum one retry and 30-second host timeout |
| Rate 100 | Maximum packet rate of 100 |
| Rate 500 | Maximum packet rate of 500 |
| Verbose/reason scan | Detailed six-port scan |
| `scenario4-normal.txt` | Normal Nmap output |
| `scenario4.xml` | XML Nmap output |
| `scenario4-grepable.txt` | Grepable Nmap output |

---

# 19. Limitations

The results should be interpreted within the context of the laboratory environment.

### 1. Local Network Conditions

The target was located on a local laboratory network, resulting in very low latency.

Timing differences may be more significant across remote or high-latency networks.

### 2. Virtualized Environment

The target was running in a VirtualBox environment, which may influence packet handling and timing behavior.

### 3. Single Target

The optimization tests were performed against one target host.

The results should not be treated as representative of every network environment.

### 4. Limited Timing Tests

Only `-T3` and `-T4` were tested in this scenario.

The full range of Nmap timing templates was not evaluated because the objective was to keep the practical scenario focused.

### 5. Output Verification

The output files were successfully generated and their file sizes verified. The internal contents of the XML and Grepable files were not separately parsed during this exercise.

---

# 20. Lessons Learned

This exercise demonstrated that Nmap scan optimization involves balancing **speed, reliability, network impact, and evidence quality**.

The most significant practical lesson was the effect of packet-rate control.

A maximum rate of 100 packets per second resulted in a 10.87-second scan, while increasing the maximum rate to 500 reduced the scan time to 2.83 seconds without changing the observed results.

The timing-template comparison also demonstrated that optimization parameters do not always produce dramatic improvements. In a fast local network, the difference between the baseline, `-T3`, and `-T4` scans was very small.

The output-format exercise demonstrated the importance of preserving scan results rather than relying only on terminal output.

---

# 21. Conclusion

Scenario 04 demonstrated how Nmap timing and optimization parameters can influence scanning performance while maintaining the same observed attack surface.

The assessment identified 12 open TCP ports across the `1-1000` port range on the Metasploitable 2 target.

The baseline, `-T3`, and `-T4` scans produced identical port results with execution times between 1.02 and 1.14 seconds.

The most significant performance difference occurred when controlling the maximum packet rate:

- `--max-rate 100` → **10.87 seconds**
- `--max-rate 500` → **2.83 seconds**

The assessment also successfully generated Normal, XML, and Grepable output files, demonstrating practical approaches for preserving Nmap scan evidence.

Overall, the exercise reinforced that effective Nmap usage requires more than selecting a scan type. The tester must also understand how timing, rate control, retries, delays, verbosity, and output formats affect the assessment process.

---

# 22. Related Documentation

### Nmap Command Reference

[`Commands/Nmap-Command-Reference.md`](../Commands/Nmap-Command-Reference.md)

### Scenario 01 — Host Discovery and Network Mapping

[`Practical Scanning Scenarios/01-Host-Discovery-and-Network-Mapping.md`](01-Host-Discovery-and-Network-Mapping.md)

### Scenario 02 — TCP and UDP Port Scanning

[`Practical Scanning Scenarios/02-TCP-and-UDP-Port-Scanning.md`](02-TCP-and-UDP-Port-Scanning.md)

### Scenario 03 — Firewall Evasion and Filtering Analysis

[`Practical Scanning Scenarios/03-Firewall-Evasion-and-Filtering-Analysis.md`](03-Firewall-Evasion-and-Filtering-Analysis.md)

### Case Study 01

[`Case-Studies/01-Metasploitable2-Security-Assessment.md`](../Case-Studies/01-Metasploitable2-Security-Assessment.md)

### Case Study 02

[`Case-Studies/02-TCP-and-UDP-Port-Scanning-Assessment.md`](../Case-Studies/02-TCP-and-UDP-Port-Scanning-Assessment.md)

### Case Study 03

[`Case-Studies/03-Firewall-Evasion-and-Filtering-Analysis.md`](../Case-Studies/03-Firewall-Evasion-and-Filtering-Analysis.md)

---

# 23. Assessment Metadata

| Field | Value |
|---|---|
| Scenario | 04 |
| Title | Scan Optimization, Timing & Output Formats |
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Scanner | Linux |
| Nmap Version | 7.99 |
| Primary Scan | TCP SYN (`-sS`) |
| Primary Port Range | `1-1000` |
| Environment | Authorized laboratory |
| Assessment Type | Practical Nmap performance and output analysis |
| Status | Completed |
```
