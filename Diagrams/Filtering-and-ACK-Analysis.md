# Filtering and ACK Analysis

Nmap can use different TCP packet types to determine how packets are handled by a target or network device.

The TCP ACK scan is particularly useful for identifying whether ports are filtered or unfiltered.

## Diagram

```text
Scanner                              Target
   |                                    |
   |----------- TCP ACK --------------->|
   |                                    |
   |                                    |
   |<----------- RST -------------------|
   |                                    |
   |                                    |
   |        Port = UNFILTERED           |
   |                                    |
   
   OR
   
   |----------- TCP ACK --------------->|
   |                                    |
   |         Packet Filter              |
   |              X                     |
   |                                    |
   |         No Response                |
   |                                    |
   |          Port = FILTERED            |
```

---

## Explanation

### Step 1

The scanner sends a TCP ACK packet to the target.

---

### Step 2

If the target responds with a TCP RST,

Nmap considers the port:

**UNFILTERED**

---

### Step 3

If the packet is blocked or no response is received,

Nmap may classify the port as:

**FILTERED**

---

## Why Is Filtering Analysis Important?

Filtering analysis helps determine how network devices handle scanning traffic.

It can provide evidence of:

- Packet filtering
- Firewall behavior
- Network access controls
- Packet handling differences

However, a **filtered** result alone does not prove that a specific firewall is responsible.

---

## Benefits

- Identifies filtered ports
- Helps analyze firewall behavior
- Provides additional network visibility
- Supports filtering analysis
- Helps interpret Nmap port states
```
