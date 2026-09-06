# TCP Three-Way Handshake

## Overview

The TCP 3-way handshake is a three-step process used by the Transmission Control Protocol to create a safe and reliable connection between a client and a server before sending any data. Every TCP connection starts with a 3-way handshake. The handshake begins with a client sending a packet with the SYN flag set saying, “Hello, I want to talk to you” the server responds with a packet with the SYN and ACK flags set saying, “Hi, I’m willing and able to chat,” and then finally, the client sends a packet with the ACK flag set that acknowledges the response of the server, and then the data transfer can begin.

---

## The Three Steps

### Step 1: SYN (Client to Server)
- The client starts the connection. 
- It sends a packet with the SYN (Synchronize) flag. 
- It includes a random starting number called the Initial Sequence Number (ISN), let's call it X.

### Step 2: SYN-ACK (Server to Client)
- The server gets the SYN packet.
- It replies with a packet that has both the SYN and ACK (Acknowledgment) flags turned on.
- It sends its own random starting number, let's call it Y.
- It also sends an acknowledgment number set to X + 1, which means "I received your starting number X".

### Step 3: ACK (Client to Server)
- The client gets the server's reply.
- It sends back a final packet with the ACK flag turned on.
- It sets the acknowledgment number to Y + 1, which means "I received your starting number Y".
- Both sides are now fully connected and ready to send data.

---

## Why It Matters
- Reliability: Both devices agree on starting numbers so they can track lost or out-of-order packets.
- Sync: It makes sure both the sender and receiver are awake and ready before actual traffic starts.
- Full-Duplex: It opens a two-way street where both devices can talk at the same time
