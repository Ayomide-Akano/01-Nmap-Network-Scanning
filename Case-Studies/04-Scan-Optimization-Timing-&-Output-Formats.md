# Case Study 04 — Scan Optimization, Timing & Output Formats

## Executive Summary

This case study evaluates the effect of Nmap scan optimization, timing controls, packet-rate limits, retry settings, scan delays, and output formats during an authorized security assessment of a Metasploitable 2 laboratory system.

The assessment targeted `192.168.43.56` using TCP SYN scanning across ports `1-1000`.

Multiple scan configurations were compared against a baseline to determine how changes in scan parameters affected:

- Scan duration
- Consistency of discovered services
- Probe rate
- Scan evidence
- Output preservation

The assessment consistently identified 12 open TCP ports across the tested `1-1000` range.

The most significant performance difference was observed when maximum packet rates were changed. The scan configured with `--max-rate 100` required **10.87 seconds**, while the scan configured with `--max-rate 500` required **2.83 seconds**.

Timing templates `-T3` and `-T4` produced the same results as the baseline, with execution times of **1.14 seconds** and **1.02 seconds**, respectively.

The assessment also successfully generated Normal, XML, and Grepable output files, demonstrating practical methods for preserving and processing Nmap assessment evidence.

---

# 1. Assessment Overview

## 1.1 Objective

The objective of this assessment was to evaluate how Nmap optimization and timing parameters influence scan performance and evidence collection.

The assessment specifically examined:

1. Baseline SYN scanning
2. Normal timing (`-T3`)
3. Aggressive timing (`-T4`)
4. Scan delay
5. Retry and host-timeout controls
6. Maximum packet rates
7. Verbose output and scan reasons
8. Nmap output formats

---

# 2. Scope

## Target

| Field | Value |
|---|---|
| Target | Metasploitable 2 |
| IP Address | `192.168.43.56` |
| Port Range | TCP `1-1000` |
| Primary Scan Type | TCP SYN (`-sS`) |
| Nmap Version | 7.99 |
| Environment | Authorized laboratory |

The assessment was limited to the specified laboratory target.

---

# 3. Assessment Methodology

The assessment began with a baseline TCP SYN scan covering ports `1-1000`.

The baseline was then compared with alternative configurations designed to alter scan timing and performance.

The following controls were evaluated:

| Category | Parameter |
|---|---|
| Baseline | `-sS` |
| Timing | `-T3` |
| Timing | `-T4` |
| Scan pacing | `--scan-delay 100ms` |
| Retries | `--max-retries 1` |
| Host timeout | `--host-timeout 30s` |
| Packet rate | `--max-rate 100` |
| Packet rate | `--max-rate 500` |
| Evidence | `-v --reason` |
| Output | `-oN` |
| Output | `-oX` |
| Output | `-oG` |

The results were compared based on the observed output and execution times recorded during the laboratory exercise.

---

# 4. Baseline Assessment

## Command

```bash
sudo nmap -sS -p 1-1000 192.168.43.56
```

## Results

The baseline scan identified:

- **12 open TCP ports**
- **988 closed TCP ports**

### Open Services

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

The baseline became the reference point for the remaining performance tests.

---

# 5. Timing Analysis

## 5.1 Normal Timing — T3

### Command

```bash
sudo nmap -sS -T3 -p 1-1000 192.168.43.56
```

### Result

The scan identified the same 12 open TCP ports as the baseline.

### Performance

- Host latency: `0.0023s`
- Scan duration: **1.14 seconds**

### Comparison

The `-T3` scan was slightly slower than the baseline.

| Test | Duration | Open Ports |
|---|---:|---:|
| Baseline | 1.08s | 12 |
| `-T3` | 1.14s | 12 |

### Assessment

No change in the observed attack surface was identified.

The difference in execution time was small within this laboratory environment.

---

## 5.2 Aggressive Timing — T4

### Command

```bash
sudo nmap -sS -T4 -p 1-1000 192.168.43.56
```

### Result

The scan again identified the same 12 open TCP ports.

### Performance

- Host latency: `0.0013s`
- Scan duration: **1.02 seconds**

### Comparison

| Test | Duration | Open Ports |
|---|---:|---:|
| Baseline | 1.08s | 12 |
| `-T3` | 1.14s | 12 |
| `-T4` | 1.02s | 12 |

### Assessment

`-T4` produced the fastest result among these three tests.

However, the observed improvement was only approximately 0.06 seconds compared with the baseline.

This indicates that, within this low-latency virtualized laboratory, the timing-template difference was relatively small.

---

# 6. Scan Delay Assessment

## Command

```bash
sudo nmap -sS --scan-delay 100ms -p 21,22,23,25,53,80 192.168.43.56
```

## Result

All six tested ports were identified as open.

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

### Assessment

The scan delay successfully slowed the probing process while maintaining visibility of the six tested services.

The result demonstrates the performance trade-off associated with deliberately reducing scan speed.

---

# 7. Retry and Host Timeout Assessment

## Command

```bash
sudo nmap -sS --max-retries 1 --host-timeout 30s -p 1-1000 192.168.43.56
```

## Result

The scan identified the same 12 open TCP ports as the baseline.

### Performance

- Host latency: `0.0013s`
- Scan duration: **0.92 seconds**

### Comparison

| Test | Duration | Open Ports |
|---|---:|---:|
| Baseline | 1.08s | 12 |
| Retry/timeout configuration | 0.92s | 12 |

### Assessment

The reduced retry configuration completed faster than the baseline while producing the same observed results.

However, this result should not be interpreted as proof that reducing retries is always preferable.

In environments experiencing packet loss or unstable connectivity, fewer retries could potentially affect result reliability.

---

# 8. Packet Rate Analysis

## 8.1 Maximum Rate — 100

### Command

```bash
sudo nmap -sS --max-rate 100 -p 1-1000 192.168.43.56
```

### Result

The scan identified the same 12 open TCP ports.

### Performance

- Host latency: `0.0012s`
- Scan duration: **10.87 seconds**

---

## 8.2 Maximum Rate — 500

### Command

```bash
sudo nmap -sS --max-rate 500 -p 1-1000 192.168.43.56
```

### Result

The scan identified the same 12 open TCP ports.

### Performance

- Host latency: `0.00076s`
- Scan duration: **2.83 seconds**

---

## 8.3 Comparative Analysis

| Configuration | Duration | Open Ports |
|---|---:|---:|
| `--max-rate 100` | 10.87s | 12 |
| `--max-rate 500` | 2.83s | 12 |

The difference was significant.

The 500 packets-per-second configuration completed approximately **8.04 seconds faster** than the 100 packets-per-second configuration.

### Assessment

This was the most significant performance difference observed during the scenario.

The results demonstrate that packet-rate restrictions can have a substantial effect on scan duration even when the resulting port discovery remains unchanged.

The appropriate rate must therefore be selected based on the target environment, network conditions, and assessment requirements.

---

# 9. Verbose Output and Scan Reason Analysis

## Command

```bash
sudo nmap -sS -v --reason -p 21,22,23,25,53,80 192.168.43.56
```

## Result

All six selected ports were identified as open.

Nmap provided the following reasons:

| Port | State | Reason |
|---|---|---|
| 21/tcp | open | syn-ack ttl 64 |
| 22/tcp | open | syn-ack ttl 64 |
| 23/tcp | open | syn-ack ttl 64 |
| 25/tcp | open | syn-ack ttl 64 |
| 53/tcp | open | syn-ack ttl 64 |
| 80/tcp | open | syn-ack ttl 64 |

### Additional Evidence

The scan reported:

- ARP host discovery
- SYN stealth scanning
- Individual open-port discovery
- Raw packets sent: 7
- Raw packets received: 7

### Performance

- Host latency: `0.00097s`
- Scan duration: **1.04 seconds**

### Assessment

The combination of `-v` and `--reason` provided additional evidence explaining how Nmap reached the observed port-state classifications.

This is particularly useful when scan results need to be documented or reviewed.

---

# 10. Output Format Assessment

The assessment tested three Nmap output formats.

## 10.1 Normal Output

### Command

```bash
sudo nmap -sS -p 1-1000 -oN scenario4-normal.txt 192.168.43.56
```

### Verification

```bash
ls -lh scenario4-normal.txt
```

### Result

```text
-rw-r--r-- 1 root root 662 Sep  9 13:50 scenario4-normal.txt
```

### File Size

**662 bytes**

The Normal output file was successfully generated and preserved the scan results in a human-readable format.

---

## 10.2 XML Output

### Command

```bash
sudo nmap -sS -p 1-1000 -oX scenario4.xml 192.168.43.56
```

### Verification

```bash
ls -lh scenario4.xml
```

### Result

```text
-rw-r--r-- 1 root root 3.3K Sep  9 13:52 scenario4.xml
```

### File Size

**3.3 KB**

The XML output was successfully generated.

The structured format provides a machine-readable representation of the scan results for subsequent processing or integration.

---

## 10.3 Grepable Output

### Command

```bash
sudo nmap -sS -p 1-1000 -oG scenario4-grepable.txt 192.168.43.56
```

### Verification

```bash
ls -lh scenario4-grepable.txt
```

### Result

```text
-rw-r--r-- 1 root root 605 Sep  9 13:53 scenario4-grepable.txt
```

### File Size

**605 bytes**

The Grepable output was successfully generated and preserved in a compact text-oriented format.

---

# 11. Evidence Summary

| Evidence | Result |
|---|---|
| Baseline SYN scan | 12 open ports |
| `-T3` | 12 open ports |
| `-T4` | 12 open ports |
| `--scan-delay 100ms` | 6/6 selected ports open |
| `--max-retries 1` + timeout | 12 open ports |
| `--max-rate 100` | 12 open ports |
| `--max-rate 500` | 12 open ports |
| `-v --reason` | 6/6 selected ports open |
| Normal output | 662 bytes |
| XML output | 3.3 KB |
| Grepable output | 605 bytes |

---

# 12. Findings

## F-01 — Packet Rate Significantly Influenced Scan Duration

**Severity:** Informational

The maximum packet rate produced the most significant performance difference during the assessment.

### Evidence

- `--max-rate 100`: **10.87 seconds**
- `--max-rate 500`: **2.83 seconds**

### Assessment

Increasing the configured maximum packet rate resulted in substantially faster completion while the same 12 open ports were observed.

This demonstrates the importance of selecting an appropriate scan rate according to the environment.

---

## F-02 — Timing Templates Produced Consistent Results

**Severity:** Informational

The baseline, `-T3`, and `-T4` scans all identified the same 12 open ports.

### Evidence

| Configuration | Duration |
|---|---:|
| Baseline | 1.08s |
| `-T3` | 1.14s |
| `-T4` | 1.02s |

### Assessment

The timing-template comparison produced no observable difference in the identified attack surface.

The execution-time differences were small within the tested environment.

---

## F-03 — Reduced Retries Improved Observed Scan Time

**Severity:** Informational

The configuration using:

```text
--max-retries 1 --host-timeout 30s
```

completed in **0.92 seconds**, compared with **1.08 seconds** for the baseline.

### Assessment

Reducing retry attempts can improve performance against responsive targets.

However, reliability should be considered before applying aggressive retry limits to networks where packet loss may occur.

---

## F-04 — Scan Delay Increased Assessment Duration

**Severity:** Informational

The `--scan-delay 100ms` test took **1.46 seconds** while scanning only six selected ports.

### Assessment

Deliberately slowing probe transmission increases scan duration but may be appropriate where a controlled probing rate is required.

---

## F-05 — Multiple Output Formats Improved Evidence Preservation

**Severity:** Informational

The assessment successfully produced:

- `scenario4-normal.txt`
- `scenario4.xml`
- `scenario4-grepable.txt`

### Assessment

Maintaining scan results in multiple formats provides flexibility for documentation, structured processing, and command-line analysis.

---

# 13. Risk Summary

| Finding | Severity | Status |
|---|---|---|
| F-01 — Packet Rate Significantly Influenced Scan Duration | Informational | Observed |
| F-02 — Timing Templates Produced Consistent Results | Informational | Observed |
| F-03 — Reduced Retries Improved Observed Scan Time | Informational | Observed |
| F-04 — Scan Delay Increased Assessment Duration | Informational | Observed |
| F-05 — Multiple Output Formats Improved Evidence Preservation | Informational | Observed |

No direct vulnerability was assigned from the optimization tests themselves.

The purpose of this scenario was to evaluate scanning behavior and assessment efficiency rather than identify a new vulnerability.

---

# 14. Security and Operational Analysis

The results demonstrate that Nmap configuration can affect the operational characteristics of a security assessment without necessarily changing the discovered attack surface.

A scan can therefore produce the same port results while taking significantly different amounts of time depending on its configuration.

The most notable example was packet-rate control.

The 100 packets-per-second scan took 10.87 seconds, while the 500 packets-per-second scan took 2.83 seconds.

This difference demonstrates the trade-off between scan speed and traffic generation.

Similarly, retry controls can reduce scan duration but may introduce reliability considerations when network conditions are poor.

Scan delays provide the opposite approach by intentionally slowing probes.

Timing templates also require contextual interpretation. In this local laboratory, the difference between `-T3` and `-T4` was minimal. Therefore, the results should not be generalized to remote networks, congested networks, or high-latency environments.

---

# 15. Recommendations

Based on the observed results:

### 1. Establish a Baseline

Perform an initial baseline scan before applying optimization parameters.

This provides a reference for measuring changes in performance and result consistency.

### 2. Select Timing Carefully

Use timing settings appropriate for the target environment rather than automatically selecting the most aggressive option.

### 3. Control Packet Rates

Use packet-rate controls when scan traffic needs to be managed.

Higher rates may significantly reduce scan time, but network capacity and target behavior should be considered.

### 4. Use Retry Controls Carefully

Reducing retries can improve scan performance against responsive systems, but excessive reduction may affect reliability in environments with packet loss.

### 5. Use Scan Delays When Appropriate

A deliberate scan delay can be used when a slower probing rate is required.

### 6. Preserve Scan Evidence

Save important assessment results to files rather than relying exclusively on terminal output.

### 7. Select Appropriate Output Formats

Use Normal output for human-readable documentation, XML for structured processing, and Grepable output for text-oriented analysis where appropriate.

### 8. Preserve Raw Results

Keep the original Nmap output files alongside the assessment report so that findings can be traced back to the original evidence.

---

# 16. Limitations

## Laboratory Environment

The assessment was performed against a local virtualized Metasploitable 2 system.

The observed performance characteristics may differ significantly on remote networks.

## Single Target

Only one target host was assessed.

The results do not represent the performance characteristics of large-scale network scanning.

## Limited Timing Comparison

Only `-T3` and `-T4` were evaluated.

The complete range of Nmap timing templates was outside the scope of this practical scenario.

## Output Validation

The generated Normal, XML, and Grepable files were verified by file existence and size.

Their internal structures were not separately parsed or validated during this exercise.

## Performance Variability

Execution times may vary between scans because of system load, network conditions, virtualization, and other environmental factors.

Therefore, individual timing measurements should be treated as observations from this laboratory rather than universal benchmarks.

---

# 17. Lessons Learned

This assessment demonstrated that scan optimization is a balance between:

**Speed + Reliability + Network Impact + Evidence Quality**

The most important observation was the effect of packet-rate control.

A lower configured rate produced a significantly longer scan, while the higher rate completed much faster without changing the observed open ports.

The assessment also demonstrated that optimization does not always produce dramatic improvements. The difference between the baseline, `-T3`, and `-T4` scans was small because the target was local and highly responsive.

The retry test further demonstrated that reducing retries can improve scan speed, but this should be balanced against the possibility of packet loss in less reliable environments.

Finally, the output-format tests reinforced the importance of preserving raw assessment evidence.

---

# 18. Conclusion

The assessment successfully evaluated Nmap scan optimization, timing, rate control, evidence generation, and output preservation against the Metasploitable 2 laboratory target.

All major TCP SYN scans identified the same 12 open ports across the `1-1000` range.

The most significant performance difference was produced by maximum packet-rate control:

- `--max-rate 100` → **10.87 seconds**
- `--max-rate 500` → **2.83 seconds**

The timing-template tests produced much smaller differences:

- Baseline → **1.08 seconds**
- `-T3` → **1.14 seconds**
- `-T4` → **1.02 seconds**

The assessment also demonstrated successful generation of Normal, XML, and Grepable Nmap output files.

Overall, Scenario 04 demonstrates that effective network scanning requires more than selecting a scanning technique. A security professional must also understand how scan timing, packet rates, retries, delays, verbosity, and output formats influence the efficiency, reliability, and documentation of an assessment.

---

# 19. Evidence

The following laboratory evidence was generated during this assessment:

```text
scenario4-normal.txt
scenario4.xml
scenario4-grepable.txt
```

### Output Sizes

```text
scenario4-normal.txt       662 bytes
scenario4.xml               3.3 KB
scenario4-grepable.txt     605 bytes
```

The evidence was generated directly from Nmap 7.99 scans against the authorized laboratory target.

---

# 20. Related Documentation

### Practical Scenario

[`Practical Scanning Scenarios/04-Scan-Optimization-Timing-and-Output-Formats.md`](../Practical%20Scanning%20Scenarios/04-Scan-Optimization-Timing-and-Output-Formats.md)

### Nmap Command Reference

[`Commands/Nmap-Command-Reference.md`](../Commands/Nmap-Command-Reference.md)

### Scenario 01 — Host Discovery and Network Mapping

[`Case-Studies/01-Metasploitable2-Security-Assessment.md`](01-Metasploitable2-Security-Assessment.md)

### Scenario 02 — TCP and UDP Port Scanning

[`Case-Studies/02-TCP-and-UDP-Port-Scanning-Assessment.md`](02-TCP-and-UDP-Port-Scanning-Assessment.md)

### Scenario 03 — Firewall Evasion and Filtering Analysis

[`Case-Studies/03-Firewall-Evasion-and-Filtering-Analysis.md`](03-Firewall-Evasion-and-Filtering-Analysis.md)

---

# 21. Assessment Metadata

| Field | Value |
|---|---|
| Case Study | 04 |
| Title | Scan Optimization, Timing & Output Formats |
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Scanner | Linux |
| Nmap Version | 7.99 |
| Primary Scan | TCP SYN (`-sS`) |
| Port Range | `1-1000` |
| Environment | Authorized laboratory |
| Assessment Type | Scan performance and evidence analysis |
| Status | Completed |
```
