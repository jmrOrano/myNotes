# TCP 3-Way Handshake
*Reference: https://youtu.be/xMtP5ZB3wSk?si=jy3VdCyNjDpNl2zm*
*June 27, 2026*

---

## Overview

Before a client can receive web pages from a server, a TCP connection must be established through the 3-way handshake.

```
[Client]                          [Server]
   |                                  |
   |  --- SYN -------------------->   |   Step 1: Client initiates
   |  <-------------- SYN-ACK -----   |   Step 2: Server acknowledges
   |  --- ACK -------------------->   |   Step 3: Client confirms
   |                                  |
   |       Connection Established     |
```

---

## Simple Version

### Step 1 — Client sends SYN

```
[Client]  ---[ SYN ]---->  [Server]
```

The client sends a SYN (synchronize) segment to the server, asking for a connection.

> *"Hello server, can you open a connection for me?"*

---

### Step 2 — Server replies with SYN-ACK

```
[Client]  <--[ SYN-ACK ]---  [Server]
```

The server responds with SYN-ACK (synchronization + acknowledgement), meaning it received the request and agrees to open a connection.

---

### Step 3 — Client sends ACK

```
[Client]  ---[ ACK ]---->  [Server]
```

The client sends a final ACK to confirm. Connection is now established.

---

## Technical Version

Each segment carries control flags and sequence numbers that both sides use to track the conversation.

### Flags

| Flag  | Meaning     | When set to 1           |
| ----- | ----------- | ----------------------- |
| `SYN` | Synchronize | Requesting a connection |
| `ACK` | Acknowledge | Confirming receipt      |

---

### Step 1 — Client sends SYN

```
[Client]                                        [Server]
   |                                               |
   |    Seq# 9001  |  Ack# 0  |  SYN# 1            |
   |   ===============[ SYN ]==================>   |
   |                                               |
```

- `Seq# 9001` — Client's randomly chosen initial sequence number
- `Ack# 0`       — No acknowledgement yet (nothing received)
- `SYN# 1`       — SYN flag is ON, this is a connection request

---

### Step 2 — Server replies with SYN-ACK

```
[Client]                                        [Server]
   |                                               |
   |    Seq# 9001  |  Ack# 0  |  SYN# 1            |
   |   ===============[ SYN ]==================>   |
   |                        +1 (Seq# of client)    |
   |    SYN# 1  |  Ack# 9002  |  Seq# 5001         |
   |   <==============[ SYN-ACK ]===============   |
   |                                               |
```

- `SYN# 1`    — Server's SYN flag is ON (server also requesting connection back)
- `Ack# 9002` — Client's Seq# (9001) + 1, confirming it received the SYN
- `Seq# 5001` — Server's own randomly chosen initial sequence number

> In real life, sequence numbers can be very large values chosen randomly for security.

---

### Step 3 — Client sends ACK

```
[Client]                                        [Server]
   |                                               |
   |   Seq# 9001  |  Ack# 0  |  SYN# 1            |
   |   ===============[ SYN ]==================>   |
   |                        +1                     |
   |   SYN# 1  |  Ack# 9002  |  Seq# 5001         |
   |   <==============[ SYN-ACK ]===============   |
   |         +1 (Seq# of server)                   |
   |   Seq# 9002  |  Ack# 5002  |  SYN# 0         |
   |   ===============[ ACK ]===================>  |
   |                                               |
```

- `SYN# 0`    — No more connection request. Just an acknowledgement.
- `Ack# 5002` — Server's Seq# (5001) + 1, confirming receipt of SYN-ACK
- `Seq# 9002` — This is the client's 2nd segment, so Seq# increments to 9002

---

## Result

```
[Client]                                        [Server]
   |   ===============[ SYN ]==================>   |
   |   <==============[ SYN-ACK ]===============   |
   |   ===============[ ACK ]===================>  |
   |                                               |
   |         Connection fully established          |
```

- **Steps 1 and 2** establish the connection from client → server
- **Steps 2 and 3** establish the connection from server → client

Both sides have agreed to communicate. Data transfer can now begin.

---

## Why 3 Steps and Not 2?

Two steps would only confirm one direction. The server needs to verify that the client received *its* SYN-ACK as well, which is what Step 3 (the final ACK) accomplishes. This makes it a two-way verified, full-duplex connection.

## Full sequence Before any Data Flows
*June 27, 2026*

#### Overview
Above shows the process of the 3-way handshake. Buuuuuuuuuut —the lower layers have to be ready **before** the TCP handshake can even begin. TCP operates at Layer 4, but to physically send that SYN packet, the machine needs to know where to send it at every layer below.


### Full Sequence Before Any Data Flows

#### Step1 — DNS Resolution(if using a hostname)
```
App says: "connect to google.com"
   ↓
   OS:    "I don't know that IP, let me ask DNS"
   ↓
UDP packet sent to DNS server → response: "google.com = 142.250.x.x"
```
Now the machine knows the **destination IP**. But it still can't send anything yet.

#### Step2 — Routing Table Lookup
```
OS checks: Is 142.250.x.x on my local subnet?
   → No → I need to send this to my Default Gateway (e.g. 192.168.1.1)
```
The machine now knows **who to physically send the packet to** (the gateway), but it still needs a **MAC address** to build the frame.

#### Step3 — ARP (Address Resolution Protocol)
*Another example if needed is at: [[How_host_do_speaks_on_the_internet#Part 1 — Same Network (No Router)]]*

```
Machine checks ARP cache:
   → "Do I already know the MAC for 192.168.1.1?"
      → If YES: use it
      → If NO: broadcast ARP Request
```
**ARP Request** _(broadcasted to everyone on the LAN)_:
```
"Who has 192.168.1.1? Tell 192.168.1.5"
```
**ARP Reply** _(unicast back)_:
```
"192.168.1.1 is at AA:BB:CC:DD:EE:FF"
```
Now the MAC is cached and the machine can **build a complete Ethernet frame**.


#### Step4 — NOW the TCP-3-Way Handshake Happens
Only at this point does the SYN packet get sent — because now the machine has **everything it needs at every layer**:
```
Layer 4: TCP SYN segment (destination port, seq number)
Layer 3: IP header (source IP + destination IP)
Layer 2: Ethernet frame (source MAC + destination MAC ← from ARP)
Layer 1: Bits on the wire
```

#### Step5 — Data Transfer
After the handshake — actual payload flows.

>**The Mental Model**
Think of it as **prerequisites per layer**, resolved bottom-up before the handshake:

|Layer|Question that must be answered|Resolved by|
|---|---|---|
|Layer 7 (App)|What's the IP of the hostname?|**DNS**|
|Layer 3 (Network)|Where does this packet route to?|**Routing table**|
|Layer 2 (Link)|What's the MAC of the next hop?|**ARP**|
|Layer 4 (Transport)|Is the other machine ready to talk?|**TCP Handshake** ← _finally_|

So the handshake is actually **one of the last things** to happen before data flows — not the first. The whole lower-layer infrastructure has to be in place first.



---

>[!Question]- Questions to explore
> - What happens if the SYN packet is dropped or never received?
> - What is a SYN flood attack and how does it exploit this handshake?
> - What does the 4-way FIN handshake look like when closing a connection?
> - How does `tcpdump` or Wireshark capture and display these packets?
