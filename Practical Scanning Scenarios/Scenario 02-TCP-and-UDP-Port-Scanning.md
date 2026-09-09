# Practical Scanning Scenario 02 — TCP and UDP Port Scanning

## Objective

The objective of this practical scenario is to examine how Nmap identifies TCP and UDP ports using different scanning techniques and to compare the results produced by each scan type.

The assessment focuses on:

- TCP SYN scanning
- TCP Connect scanning
- TCP FIN scanning
- TCP NULL scanning
- TCP Xmas scanning
- UDP scanning
- Top-port UDP scanning
- Targeted UDP scanning
- Combined TCP and UDP scanning
- UDP service/version detection
- Interpretation of `open`, `closed`, and `open|filtered` states
- Comparison of TCP and UDP attack surfaces

The assessment was performed against an intentionally vulnerable Metasploitable 2 virtual machine within an authorized laboratory environment.

---

## Lab Environment

| Component | Details |
|---|---|
| Target | Metasploitable 2 |
| Target IP | `192.168.43.56` |
| Scanning Machine | Linux |
| Scanning Machine IP | `192.168.43.155` |
| Network | `192.168.43.0/24` |
| Nmap Version | `7.99` |
| Environment | Authorized VirtualBox laboratory |

---

## Assessment Scope

The assessment was limited to the following target:

```text
192.168.43.56
```

The scans were performed against the target using different TCP and UDP scanning techniques.

No exploitation was performed during this scenario.

---

# 1. TCP Port Scanning

## 1.1 TCP SYN Scan

### Command

```bash
sudo nmap -sS 192.168.43.56
```

The TCP SYN scan was used as the primary TCP scanning baseline.

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 01:55 +0100
Nmap scan report for 192.168.43.56
Host is up (0.0037s latency).
Not shown: 977 closed tcp ports (reset)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
23/tcp    open  telnet
25/tcp    open  smtp
53/tcp    open  domain
80/tcp    open  http
111/tcp   open  rpcbind
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
512/tcp   open  exec
513/tcp   open  login
514/tcp   open  shell
1099/tcp  open  rmiregistry
1524/tcp  open  ingreslock
2049/tcp  open  nfs
2121/tcp  open  ccproxy-ftp
3306/tcp  open  mysql
5432/tcp  open  postgresql
5900/tcp  open  vnc
6000/tcp  open  X11
6667/tcp  open  irc
8009/tcp  open  ajp13
8180/tcp  open  unknown

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.49 seconds
```

### Observation

The default TCP SYN scan identified **23 open TCP ports**.

The scan also reported:

```text
977 closed tcp ports
```

This establishes that the target exposes a relatively large number of TCP services compared with the default Nmap TCP scanning range.

The discovered services include:

- FTP
- SSH
- Telnet
- SMTP
- DNS
- HTTP
- RPCBind
- NetBIOS
- SMB
- Remote shell services
- Java RMI
- NFS
- MySQL
- PostgreSQL
- VNC
- X11
- IRC
- AJP
- Tomcat

The large number and variety of exposed services indicate a broad TCP attack surface.

---

# 1.2 TCP SYN Scan — Ports 1–1000

### Command

```bash
sudo nmap -sS -p 1-1000 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 01:56 +0100
Nmap scan report for 192.168.43.56
Host is up (0.0031s latency).
Not shown: 988 closed tcp ports (reset)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
23/tcp    open  telnet
25/tcp    open  smtp
53/tcp    open  domain
80/tcp    open  http
111/tcp   open  rpcbind
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
512/tcp   open  exec
513/tcp   open  login
514/tcp   open  shell

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.20 seconds
```

### Observation

The ports 1–1000 scan identified **12 open TCP ports**.

The results demonstrate that restricting the scan to ports 1–1000 does not reveal all services exposed by the target.

Several additional services discovered during the full TCP scan were outside this range.

---

# 1.3 Full TCP Port Scan

### Command

```bash
sudo nmap -sS -p- 192.168.43.56
```

The `-p-` option was used to scan the complete TCP port range.

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 01:56 +0100
Nmap scan report for 192.168.43.56
Host is up (0.00056s latency).
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
23/tcp    open  telnet
25/tcp    open  smtp
53/tcp    open  domain
80/tcp    open  http
111/tcp   open  rpcbind
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
512/tcp   open  exec
513/tcp   open  login
514/tcp   open  shell
1099/tcp  open  rmiregistry
1524/tcp  open  ingreslock
2049/tcp  open  nfs
2121/tcp  open  ccproxy-ftp
3306/tcp  open  mysql
3632/tcp  open  distccd
5432/tcp  open  postgresql
5900/tcp  open  vnc
6000/tcp  open  X11
6667/tcp  open  irc
6697/tcp  open  ircs-u
8009/tcp  open  ajp13
8180/tcp  open  unknown
8787/tcp  open  msgsrvr
37541/tcp open  unknown
41959/tcp open  unknown
54536/tcp open  unknown
57742/tcp open  unknown

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 14.73 seconds
```

### Observation

The full TCP scan identified **31 open TCP ports**.

This was significantly more than the 12 ports identified in the 1–1000 scan.

Additional ports discovered outside the first 1,000 ports included:

```text
1099
1524
2049
2121
3306
3632
5432
5900
6000
6667
6697
8009
8180
8787
37541
41959
54536
57742
```

This provides an important practical demonstration:

> Limiting a scan to the most common or first 1,000 ports can miss services operating on higher-numbered ports.

The full-port scan also required considerably more time:

```text
1–1000 ports: 1.20 seconds
All TCP ports: 14.73 seconds
```

This demonstrates the trade-off between scan coverage and scan duration.

---

# 1.4 TCP Connect Scan

### Command

```bash
nmap -sT 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 01:58 +0100
Nmap scan report for 192.168.43.56
Host is up (0.0026s latency).
Not shown: 977 closed tcp ports (conn-refused)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
23/tcp    open  telnet
25/tcp    open  smtp
53/tcp    open  domain
80/tcp    open  http
111/tcp   open  rpcbind
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
512/tcp   open  exec
513/tcp   open  login
514/tcp   open  shell
1099/tcp  open  rmiregistry
1524/tcp  open  ingreslock
2049/tcp  open  nfs
2121/tcp  open  ccproxy-ftp
3306/tcp  open  mysql
5432/tcp  open  postgresql
5900/tcp  open  vnc
6000/tcp  open  X11
6667/tcp  open  irc
8009/tcp  open  ajp13
8180/tcp  open  unknown

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.37 seconds
```

### Observation

The TCP Connect scan identified the same **23 open TCP ports** observed in the default SYN scan.

The main visible difference in the output is the description of closed ports:

```text
977 closed tcp ports (conn-refused)
```

rather than:

```text
977 closed tcp ports (reset)
```

This reflects the different TCP scanning behavior used by the two techniques.

### SYN vs Connect Result

| Scan | Open TCP Ports | Scan Time |
|---|---:|---:|
| SYN `-sS` | 23 | 1.49 seconds |
| Connect `-sT` | 23 | 1.37 seconds |

In this laboratory run, both techniques produced the same set of open ports.

---

# 2. TCP FIN, NULL and Xmas Scans

The following scans were performed to observe how alternative TCP probe types classify the target's ports.

These scans are included in this scenario for comparison of TCP scanning behavior.

Detailed firewall-evasion analysis will be covered separately in Practical Scanning Scenario 03.

---

# 2.1 TCP FIN Scan

### Command

```bash
sudo nmap -sF 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 02:00 +0100
Nmap scan report for 192.168.43.56
Host is up (0.0013s latency).
Not shown: 977 closed tcp ports (reset)
PORT      STATE         SERVICE
21/tcp    open|filtered ftp
22/tcp    open|filtered ssh
23/tcp    open|filtered telnet
25/tcp    open|filtered smtp
53/tcp    open|filtered domain
80/tcp    open|filtered http
111/tcp   open|filtered rpcbind
139/tcp   open|filtered netbios-ssn
445/tcp   open|filtered microsoft-ds
512/tcp   open|filtered exec
513/tcp   open|filtered login
514/tcp   open|filtered shell
1099/tcp  open|filtered rmiregistry
1524/tcp  open|filtered ingreslock
2049/tcp  open|filtered nfs
2121/tcp  open|filtered ccproxy-ftp
3306/tcp  open|filtered mysql
5432/tcp  open|filtered postgresql
5900/tcp  open|filtered vnc
6000/tcp  open|filtered X11
6667/tcp  open|filtered irc
8009/tcp  open|filtered ajp13
8180/tcp  open|filtered unknown

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 2.39 seconds
```

### Observation

Unlike the SYN scan, the FIN scan classified the discovered ports as:

```text
open|filtered
```

This means Nmap could not definitively distinguish between an open port and a filtered port based on the responses received.

---

# 2.2 TCP NULL Scan

### Command

```bash
sudo nmap -sN 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 02:02 +0100
Nmap scan report for 192.168.43.56
Host is up (0.0012s latency).
Not shown: 977 closed tcp ports (reset)
PORT      STATE         SERVICE
21/tcp    open|filtered ftp
22/tcp    open|filtered ssh
23/tcp    open|filtered telnet
25/tcp    open|filtered smtp
53/tcp    open|filtered domain
80/tcp    open|filtered http
111/tcp   open|filtered rpcbind
139/tcp   open|filtered netbios-ssn
445/tcp   open|filtered microsoft-ds
512/tcp   open|filtered exec
513/tcp   open|filtered login
514/tcp   open|filtered shell
1099/tcp  open|filtered rmiregistry
1524/tcp  open|filtered ingreslock
2049/tcp  open|filtered nfs
2121/tcp  open|filtered ccproxy-ftp
3306/tcp  open|filtered mysql
5432/tcp  open|filtered postgresql
5900/tcp  open|filtered vnc
6000/tcp  open|filtered X11
6667/tcp  open|filtered irc
8009/tcp  open|filtered ajp13
8180/tcp  open|filtered unknown

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 2.28 seconds
```

### Observation

The NULL scan produced the same general classification observed with the FIN scan:

```text
open|filtered
```

The scan therefore did not provide the same definitive open-port classification obtained through the SYN and Connect scans.

---

# 2.3 TCP Xmas Scan

### Command

```bash
sudo nmap -sX 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 02:02 +0100
Nmap scan report for 192.168.43.56
Host is up (0.0028s latency).
Not shown: 977 closed tcp ports (reset)
PORT      STATE         SERVICE
21/tcp    open|filtered ftp
22/tcp    open|filtered ssh
23/tcp    open|filtered telnet
25/tcp    open|filtered smtp
53/tcp    open|filtered domain
80/tcp    open|filtered http
111/tcp   open|filtered rpcbind
139/tcp   open|filtered netbios-ssn
445/tcp   open|filtered microsoft-ds
512/tcp   open|filtered exec
513/tcp   open|filtered login
514/tcp   open|filtered shell
1099/tcp  open|filtered rmiregistry
1524/tcp  open|filtered ingreslock
2049/tcp  open|filtered nfs
2121/tcp  open|filtered ccproxy-ftp
3306/tcp  open|filtered mysql
5432/tcp  open|filtered postgresql
5900/tcp  open|filtered vnc
6000/tcp  open|filtered X11
6667/tcp  open|filtered irc
8009/tcp  open|filtered ajp13
8180/tcp  open|filtered unknown

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 2.45 seconds
```

### Observation

The Xmas scan also classified the discovered ports as:

```text
open|filtered
```

This differs substantially from the SYN and Connect scans, which identified the same services as definitively open.

---

# 2.4 TCP Scan Comparison

The results can be summarized as follows:

| Scan Type | Nmap Option | Observed Result |
|---|---|---|
| TCP SYN | `-sS` | Ports identified as `open` |
| TCP Connect | `-sT` | Ports identified as `open` |
| TCP FIN | `-sF` | Ports identified as `open|filtered` |
| TCP NULL | `-sN` | Ports identified as `open|filtered` |
| TCP Xmas | `-sX` | Ports identified as `open|filtered` |

The laboratory results demonstrate that different TCP scanning techniques can produce different interpretations of the same target.

The SYN and Connect scans provided definitive open-port results, while the FIN, NULL and Xmas scans produced `open|filtered` classifications for the same ports.

Detailed analysis of why these techniques behave differently in the presence of packet filtering is reserved for Practical Scanning Scenario 03 — Firewall Evasion & Filtering Analysis.

---

# 3. UDP Port Scanning

UDP scanning was performed separately because UDP does not use the same connection-oriented behavior as TCP.

UDP scanning also required significantly more time in this laboratory environment.

---

# 3.1 Top 20 UDP Ports

### Command

```bash
sudo nmap -sU --top-ports 20 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 02:05 +0100
Nmap scan report for 192.168.43.56
Host is up (0.0041s latency).

PORT      STATE         SERVICE
53/udp    open          domain
67/udp    closed        dhcps
68/udp    open|filtered dhcpc
69/udp    open|filtered tftp
123/udp   closed        ntp
135/udp   closed        msrpc
137/udp   open          netbios-ns
138/udp   open|filtered netbios-dgm
139/udp   closed        netbios-ssn
161/udp   closed        snmp
162/udp   closed        snmptrap
445/udp   closed        microsoft-ds
500/udp   closed        isakmp
514/udp   closed        syslog
520/udp   closed        route
631/udp   closed        ipp
1434/udp  closed        ms-sql-m
1900/udp  closed        upnp
4500/udp  closed        nat-t-ike
49152/udp closed        unknown

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 20.03 seconds
```

### Observation

The top-20 UDP scan identified:

- `53/udp` as **open**
- `137/udp` as **open**
- `68/udp` as **open|filtered**
- `69/udp` as **open|filtered**
- `138/udp` as **open|filtered**

The remaining tested ports were reported as closed.

The scan took:

```text
20.03 seconds
```

This is significantly longer than the TCP scans performed earlier.

---

# 3.2 Top 100 UDP Ports

### Command

```bash
sudo nmap -sU --top-ports 100 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 02:06 +0100
Nmap scan report for 192.168.43.56
Host is up (0.00092s latency).
Not shown: 93 closed udp ports (port-unreach)
PORT      STATE         SERVICE
53/udp    open          domain
68/udp    open|filtered dhcpc
69/udp    open|filtered tftp
111/udp   open          rpcbind
137/udp   open          netbios-ns
138/udp   open|filtered netbios-dgm
2049/udp  open          nfs

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 121.38 seconds
```

### Observation

Increasing the scan from the top 20 to the top 100 UDP ports identified additional services:

```text
111/udp  rpcbind
2049/udp nfs
```

The number of identified UDP services increased from the smaller scan.

The scan required:

```text
121.38 seconds
```

This demonstrates an important trade-off:

> Increasing UDP scan coverage can reveal additional services but may significantly increase scan duration.

---

# 3.3 Targeted UDP Scan

The UDP results were followed by a targeted scan against selected ports.

### Command

```bash
sudo nmap -sU -p 53,111,137,138,161,500 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 02:09 +0100
Nmap scan report for 192.168.43.56
Host is up (0.0042s latency).

PORT      STATE         SERVICE
53/udp    open          domain
111/udp   open          rpcbind
137/udp   open          netbios-ns
138/udp   open|filtered netbios-dgm
161/udp   closed        snmp
500/udp   closed        isakmp

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 3.83 seconds
```

### Observation

The targeted scan confirmed the results for the selected ports:

| Port | State | Service |
|---:|---|---|
| 53 | Open | DNS |
| 111 | Open | RPCBind |
| 137 | Open | NetBIOS Name Service |
| 138 | Open/Filtered | NetBIOS Datagram |
| 161 | Closed | SNMP |
| 500 | Closed | ISAKMP |

The targeted scan completed in only **3.83 seconds**, demonstrating why targeted scanning can be useful after a broader discovery scan has identified interesting ports.

---

# 4. Combined TCP and UDP Scan

### Command

```bash
sudo nmap -sS -sU --top-ports 20 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 02:09 +0100
Nmap scan report for 192.168.43.56
Host is up (0.0018s latency).

PORT      STATE         SERVICE
21/tcp    open          ftp
22/tcp    open          ssh
23/tcp    open          telnet
25/tcp    open          smtp
53/tcp    open          domain
80/tcp    open          http
110/tcp   closed        pop3
111/tcp   open          rpcbind
135/tcp   closed        msrpc
139/tcp   open          netbios-ssn
143/tcp   closed        imap
443/tcp   closed        https
445/tcp   open          microsoft-ds
993/tcp   closed        imaps
995/tcp   closed        pop3s
1723/tcp  closed        pptp
3306/tcp  open          mysql
3389/tcp  closed        ms-wbt-server
5900/tcp  open          vnc
8080/tcp  closed        http-proxy
53/udp    open          domain
67/udp    closed        dhcps
68/udp    open|filtered dhcpc
69/udp    open|filtered tftp
123/udp   closed        ntp
135/udp   open|filtered msrpc
137/udp   open          netbios-ns
138/udp   open|filtered netbios-dgm
139/udp   closed        netbios-ssn
161/udp   open|filtered snmp
162/udp   closed        snmptrap
445/udp   closed        microsoft-ds
500/udp   closed        isakmp
514/udp   closed        syslog
520/udp   closed        route
631/udp   open|filtered ipp
1434/udp  open|filtered ms-sql-m
1900/udp  closed        upnp
4500/udp  closed        nat-t-ike
49152/udp closed        unknown

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 8.75 seconds
```

### Observation

The combined scan provided a single view of selected TCP and UDP ports.

The results demonstrate that the same numeric port can have completely different TCP and UDP states.

For example:

```text
139/tcp  open
139/udp  closed
```

Similarly:

```text
445/tcp  open
445/udp  closed
```

This demonstrates that TCP and UDP services must be assessed independently.

---

# 5. UDP Service and Version Detection

After identifying interesting UDP ports, service detection was performed against selected ports.

### Command

```bash
sudo nmap -sU -sV -p 53,137,68,69,138,500,520,49152 192.168.43.56
```

### Result

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-09 02:13 +0100
Nmap scan report for 192.168.43.56
Host is up (0.00098s latency).

PORT      STATE         SERVICE      VERSION
53/udp    open          domain       ISC BIND 9.4.2
68/udp    open|filtered dhcpc
69/udp    open|filtered tftp
137/udp   open          netbios-ns   Microsoft Windows netbios-ns (workgroup: WORKGROUP)
138/udp   open|filtered netbios-dgm
500/udp   closed        isakmp
520/udp   closed        route
49152/udp closed        unknown

MAC Address: 08:00:27:DB:2A:1B (Oracle VirtualBox virtual NIC)

Service Info: Host: METASPLOITABLE; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/.

Nmap done: 1 IP address (1 host up) scanned in 107.89 seconds
```

### Observation

Service detection successfully identified:

```text
53/udp   ISC BIND 9.4.2
137/udp  Microsoft Windows NetBIOS Name Service
```

However, Nmap continued to classify several ports as:

```text
open|filtered
```

including:

```text
68/udp
69/udp
138/udp
```

This demonstrates that UDP service identification can be more difficult when Nmap does not receive a response that definitively establishes the state of a port.

### Important Note

The service-detection output reported:

```text
Service Info: Host: METASPLOITABLE; OS: Windows
```

This conflicts with the operating system identification obtained earlier in the assessment, where the target was identified as Linux 2.6.x.

The UDP service-detection result should therefore **not be treated as authoritative operating system identification**.

The earlier OS-detection result and broader assessment evidence identify the target as a Linux system. The Windows information appears to be associated with the NetBIOS service response rather than the actual underlying operating system.

This demonstrates an important security-assessment principle:

> Individual tool outputs should be interpreted in context rather than accepted without validation.

---

# 6. TCP vs UDP Comparison

The practical scans demonstrate significant differences between TCP and UDP reconnaissance.

## 6.1 Protocol Comparison

| Characteristic | TCP | UDP |
|---|---|---|
| Connection model | Connection-oriented | Connectionless |
| Scan examples used | SYN, Connect, FIN, NULL, Xmas | UDP scan |
| Result interpretation | Generally more direct | Often requires interpretation |
| Common states observed | Open, Closed, Open/Filtered | Open, Closed, Open/Filtered |
| Scan duration in this lab | Generally short | Significantly longer |
| Service discovery | Straightforward for responsive TCP services | Can require additional probing |
| Example open services | FTP, SSH, HTTP, SMB, MySQL | DNS, RPCBind, NetBIOS, NFS |

---

## 6.2 TCP Attack Surface

The full TCP scan identified **31 open TCP ports**.

Important exposed services included:

```text
21     FTP
22     SSH
23     Telnet
25     SMTP
53     DNS
80     HTTP
111    RPCBind
139    NetBIOS
445    SMB
1099   Java RMI
1524   Bindshell
2049   NFS
2121   FTP
3306   MySQL
3632   distccd
5432   PostgreSQL
5900   VNC
6000   X11
6667   IRC
6697   IRC over TLS
8009   AJP
8180   HTTP
```

Several additional high-numbered ports were also identified but were not associated with a known service by the basic port scan.

---

## 6.3 UDP Attack Surface

The top-100 UDP scan identified the following open or open|filtered services:

```text
53/udp    open          DNS
68/udp    open|filtered DHCP client
69/udp    open|filtered TFTP
111/udp   open          RPCBind
137/udp   open          NetBIOS Name Service
138/udp   open|filtered NetBIOS Datagram
2049/udp  open          NFS
```

The UDP results therefore revealed services that would not be visible through TCP-only scanning.

---

# 7. Scan Coverage Comparison

One of the most significant findings from this scenario was the difference between limited and full TCP scanning.

| Scan | Ports Examined | Open Ports Identified | Duration |
|---|---:|---:|---:|
| TCP SYN default | Nmap default TCP set | 23 | 1.49 sec |
| TCP SYN 1–1000 | 1,000 | 12 | 1.20 sec |
| TCP SYN full | 65,535 | 31 | 14.73 sec |
| UDP top 20 | 20 | 2 open + 3 open/filtered | 20.03 sec |
| UDP top 100 | 100 | 4 open + 3 open/filtered | 121.38 sec |
| Targeted UDP | 6 | 3 open + 1 open/filtered | 3.83 sec |

The results demonstrate that scan scope has a direct effect on:

- Service visibility
- Number of ports discovered
- Scan duration
- Amount of data requiring analysis

---

# 8. Key Findings

## Finding 01 — Expanded TCP Attack Surface

The full TCP scan identified **31 open TCP ports**, compared with only **12 ports** within the 1–1000 range.

This demonstrates that restricting reconnaissance to the first 1,000 ports can leave a significant portion of the target's exposed services undiscovered.

---

## Finding 02 — UDP Services Would Be Missed by TCP-Only Scanning

The UDP scans identified services including:

- DNS
- RPCBind
- NetBIOS Name Service
- NFS
- Potential TFTP and NetBIOS Datagram services

These services would not be identified through TCP-only scanning.

---

## Finding 03 — Alternative TCP Scans Produced Different Port States

SYN and Connect scans identified the target's exposed ports as:

```text
open
```

FIN, NULL and Xmas scans instead reported:

```text
open|filtered
```

This demonstrates that scan technique affects how Nmap interprets the target's responses.

Detailed filtering and evasion analysis will be performed in Practical Scanning Scenario 03.

---

## Finding 04 — UDP Scanning Requires Greater Time Investment

The top-100 UDP scan required:

```text
121.38 seconds
```

compared with:

```text
14.73 seconds
```

for the full TCP scan.

This demonstrates that UDP reconnaissance can require substantially more time and should be planned accordingly.

---

## Finding 05 — Targeted UDP Scanning Is More Efficient

After broader UDP scanning identified interesting ports, the targeted scan completed in:

```text
3.83 seconds
```

This demonstrates the value of narrowing subsequent scans to specific ports of interest.

---

# 9. Security Analysis

The combined TCP and UDP scans reveal a broad network attack surface on the Metasploitable 2 host.

The TCP scan identified numerous network-facing services, while UDP scanning revealed additional services that would have remained invisible during TCP-only reconnaissance.

The results also demonstrate that reconnaissance should not rely on a single scan type.

A TCP-only assessment could miss UDP services.

A limited port scan could miss services operating on higher-numbered ports.

A single TCP scan technique could also produce different results from alternative probe types.

A more complete reconnaissance workflow therefore benefits from combining:

```text
TCP Discovery
      ↓
UDP Discovery
      ↓
Full/Targeted Port Scanning
      ↓
Service Enumeration
      ↓
Security Analysis
```

---

# 10. Evidence Collected

The following scan outputs were collected during the assessment:

1. TCP SYN default scan
2. TCP SYN ports 1–1000
3. TCP SYN full-port scan
4. TCP Connect scan
5. TCP FIN scan
6. TCP NULL scan
7. TCP Xmas scan
8. UDP top-20 scan
9. UDP top-100 scan
10. Targeted UDP scan
11. Combined TCP and UDP scan
12. UDP service/version detection

Supporting screenshots may be stored in:

```text
../Screenshots/
```

---

# 11. Assessment Limitations

This scenario focused specifically on TCP and UDP port scanning.

The following activities were intentionally outside the scope of this scenario:

- Exploitation
- Vulnerability exploitation
- Credential attacks
- Password cracking
- Detailed firewall-evasion analysis
- Full NSE enumeration
- Comprehensive vulnerability assessment

Firewall and filtering behavior will be investigated separately.

NSE-based service enumeration will also be covered in a later practical scenario.

---

# 12. Lessons Learned

This practical scenario demonstrated several important reconnaissance principles.

### 1. Port range matters

A scan limited to ports 1–1000 identified 12 open TCP ports, while the full TCP scan identified 31.

### 2. TCP and UDP must be assessed separately

TCP scanning alone would not have revealed the UDP services discovered during this assessment.

### 3. Different TCP scan techniques can produce different classifications

SYN and Connect scans identified open services, while FIN, NULL and Xmas scans returned `open|filtered`.

### 4. UDP scanning can be significantly slower

The top-100 UDP scan required over two minutes in this environment.

### 5. Targeted scanning improves efficiency

Once interesting UDP ports were identified, scanning only those ports reduced the scan time considerably.

### 6. Tool output requires interpretation

The UDP service-detection scan reported Windows-related service information even though earlier OS detection identified the host as Linux.

This reinforces the importance of validating individual observations against the broader evidence.

---

# 13. Conclusion

The TCP and UDP port-scanning assessment successfully demonstrated how different Nmap scanning techniques can reveal different aspects of a target's network exposure.

The assessment identified:

- 31 open TCP ports during a full TCP scan
- Multiple UDP services during top-port scanning
- Additional TCP services outside the first 1,000 ports
- Different results between SYN, Connect, FIN, NULL and Xmas scans
- Significant differences in TCP and UDP scan duration
- The value of targeted follow-up scanning

The most important practical lesson was that **scan methodology directly affects visibility**.

A restricted TCP scan may miss high-numbered services, while TCP-only reconnaissance may completely miss UDP services. Alternative scan techniques can also produce different interpretations of the same target.

The results from this scenario will serve as a foundation for the next practical exercise:

```text
Practical Scanning Scenario 03
Firewall Evasion & Filtering Analysis
```

That scenario will investigate how techniques such as packet fragmentation, decoys, FIN/NULL/Xmas scans and other filtering-related techniques behave when examining network defenses.

---

## Related Documentation

### Practical Scanning Scenarios

```text
01-Host-Discovery-and-Network-Mapping.md
02-TCP-and-UDP-Port-Scanning.md
```

### Command Reference

```text
../Commands/Nmap-Command-Reference.md
```

### Case Studies

```text
../Case-Studies/
```

### Evidence

```text
../Screenshots/
```

### Security Reports

```text
../report/
```

---

## Assessment Status

**Status:** Completed

**Assessment Type:** Authorized Laboratory Exercise

**Primary Tool:** Nmap 7.99

**Target:** `192.168.43.56`

**Primary Focus:** TCP and UDP Port Scanning

**Exploitation Performed:** No

**Next Scenario:** Firewall Evasion & Filtering Analysis
```
