# TCP Three-Way Handshake

## Overview

The TCP 3-way handshake is a three-step process used by the Transmission Control Protocol to create a safe and reliable connection between a client and a server before sending any data. Every TCP connection starts with a 3-way handshake. The handshake begins with a client sending a packet with the SYN flag set saying, “Hello, I want to talk to you” the server responds with a packet with the SYN and ACK flags set saying, “Hi, I’m willing and able to chat,” and then finally, the client sends a packet with the ACK flag set that acknowledges the response of the server, and then the data transfer can begin.
