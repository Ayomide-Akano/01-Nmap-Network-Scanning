# TCP Three-Way Handshake

Before understanding how Nmap performs TCP scans, it is important to understand how a normal TCP connection is established.

## Diagram

```text
Client                                   Server
  |                                         |
  |------------- SYN ---------------------->|
  |                                         |
  |<----------- SYN / ACK ------------------|
  |                                         |
  |------------- ACK ---------------------->|
  |                                         |
  |========= TCP Connection Established ====|
```

---

## Explanation

### Step 1 – SYN

The client sends a SYN (Synchronize) packet requesting to establish a TCP connection.

---

### Step 2 – SYN/ACK

The server responds with a SYN/ACK packet indicating:

- I received your request.
- I'm willing to establish a connection.

---

### Step 3 – ACK

The client acknowledges the server's response by sending an ACK packet.

The TCP connection is now fully established and data transmission can begin.

---

## Why This Matters

Understanding the TCP three-way handshake is essential because most Nmap TCP scan types intentionally interrupt or manipulate this process to determine whether ports are open, closed, or filtered.
