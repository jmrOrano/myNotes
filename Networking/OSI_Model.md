# The OSI Model

> **What is it?** A framework that breaks down how data travels from one computer to another into 7 layers.
> Each layer has one specific job — and they all work together like an assembly line.

---

## The 7 Layers at a Glance

```
┌─────────────────────────────────────────────┐
│  Layer 7 — Application                      │
│  Layer 6 — Presentation                     │
│  Layer 5 — Session                          │
├─────────────────────────────────────────────┤
│  Layer 4 — Transport      (Service-to-Service) │
│  Layer 3 — Network        (End-to-End)         │
│  Layer 2 — Data Link      (Hop-to-Hop)         │
│  Layer 1 — Physical       (Bits on the wire)   │
└─────────────────────────────────────────────┘
```

> 💡 **Memory tip:** *"Please Do Not Throw Sausage Pizza Away"* — Physical, Data Link, Network, Transport, Session, Presentation, Application.

---

## Layer 1 — Physical Layer
**Goal: Move bits (1s and 0s) from one machine to another.**

Everything on a network starts here. Computers only understand 1s and 0s — and something has to physically carry those bits from point A to point B. That's Layer 1's only job.

```
Computer A                                    Computer B
[ 1 0 1 1 0 0 1 ] ══════════════════════════▶ [ 1 0 1 1 0 0 1 ]
                     (cable / wifi / fiber)
```

**What lives on Layer 1:**

| Device / Medium | Why it's Layer 1                             |
| --------------- | -------------------------------------------- |
| Ethernet cable  | Physically carries electrical signals (bits) |
| Fiber optic     | Carries bits as pulses of light              |
| WiFi            | Carries bits as radio waves                  |
| Repeater        | Just extends the wire — amplifies the signal |
| Hub             | Sends bits to everyone connected to it       |

> 💡 Don't get hung up on the word *"physical"* — WiFi is wireless but it's still Layer 1 because its only job is to carry 1s and 0s.

---

## Layer 2 — Data Link Layer
**Goal: Hop-to-Hop delivery — move data from one NIC to the next.**

Layer 2 sits on top of Layer 1. It's the one that actually puts bits *onto* the wire and pulls bits *off* the wire. It doesn't care about the full journey — just the next hop.

**Addressing scheme: MAC Addresses**
- 48 bits, written as 12 hex digits
- Every NIC (Network Interface Card) has one — burned in at the factory
- Format varies by OS:

```
Windows  →  00-1A-2B-3C-4D-5E
Linux    →  00:1A:2B:3C:4D:5E
Cisco    →  001A.2B3C.4D5E
```

**What lives on Layer 2:**

| Device | Role |
|---|---|
| NIC / WiFi Card | The physical endpoint of a hop |
| Switch | Directs frames within the same network |
| Bridge | Connects two network segments |

> 💡 **Analogy:** If the internet is a postal system, Layer 2 is the delivery driver handing a package from one house to the next. It only knows about the *immediate* next stop.

---

## Layer 3 — Network Layer
**Goal: End-to-End delivery — move data from the original source all the way to the final destination.**

While Layer 2 handles each individual hop, Layer 3 keeps the big picture. It knows where the packet started and where it ultimately needs to end up.

**Addressing scheme: IP Addresses**
- 32 bits, written as 4 octets (each 0–255)
- Example: `192.168.1.10`

```
Host A (10.1.1.1) ──▶ Router ──▶ Router ──▶ Host B (10.2.2.1)
     [L3 knows this full path the entire time]
     [L2 only knows about each individual arrow]
```

**What lives on Layer 3:**

| Device | Role |
|---|---|
| Router | Forwards packets between different networks |
| Any device with an IP | Considered a Layer 3 participant |

### Why do we need BOTH Layer 2 and Layer 3?

This is a common question. Here's the answer:

| Layer | Addressing | Purpose |
|---|---|---|
| L3 (IP) | End-to-End | Where does this ultimately need to go? |
| L2 (MAC) | Hop-to-Hop | Who do I hand it to *right now*? |

They serve different purposes and work *together*, not against each other.

> 💡 **Analogy:** L3 is the GPS destination entered into your phone. L2 is each individual turn you make to get there. You need both.

**ARP bridges the gap between L3 and L2** — it's how a device figures out the MAC address to use for the next hop given only an IP address. See: *How a Host Speaks on the Internet* notes.

---

## Layer 4 — Transport Layer
**Goal: Service-to-Service delivery — make sure the right data reaches the right program.**

Your computer has one IP address, but many programs running at once — a browser, a game, a chat app. They're all sending and receiving data at the same time. Layer 4 keeps them separated using **ports**.

```
┌────────────────────────────────────────────────────┐
│                   Your Computer                    │
│   IP: 10.1.1.5                                     │
│                                                    │
│   Chrome     → Port 54321  ──▶  Web Server :443   │
│   Discord    → Port 54400  ──▶  Discord    :443   │
│   Game       → Port 27890  ──▶  Game Srv   :27015 │
└────────────────────────────────────────────────────┘
```

**Two types of ports (0–65535 each):**

| Protocol | Favors | Use case |
|---|---|---|
| TCP | Reliability | Web browsing, file transfer — must arrive correctly |
| UDP | Speed/Efficiency | Video calls, gaming — fast is more important than perfect |

> 💡 **Analogy:** Your IP address is the apartment building. Ports are the individual unit numbers. The mailman (L3) delivers to the building; L4 makes sure the right tenant gets the envelope.

**Multiple tabs on the same website?** Each tab generates a *different random source port*, which is how your computer tracks them as separate connections even though they're all going to the same server IP.

---

## Layers 5, 6, 7 — Session, Presentation, Application

In practice, these three are treated as a **single Application Layer** in most real-world discussions (they're more distinct in theory than in practice).

| Layer | Name | Job |
|---|---|---|
| 7 | Application | The actual program/interface the user interacts with (browser, email client) |
| 6 | Presentation | Data formatting, encryption, encoding — making data readable |
| 5 | Session | Managing the connection between two applications |

### Layer 7-6 Spotlight — Encoding & Base64

This is where **Base64** lives.

Raw data (images, certificates, binary files) can't always be safely transmitted as-is through text-based systems — some characters get misread or corrupted. Layer 6 handles the job of encoding data into a safe format for transport, then decoding it on the other end.

**Base64** is one such encoding:

```
Binary data  →  [Base64 Encode]  →  Text string  →  Transport  →  [Base64 Decode]  →  Binary data
```

The characters it uses are always safe: `A–Z`, `a–z`, `0–9`, `+`, `/` — nothing that breaks a text protocol.

**Where you'll actually see this:**

The most common real-world example is **SSL/TLS certificates**. Open any `.pem` or `.crt` file and you'll see:

```
-----BEGIN CERTIFICATE-----
MIIDazCCAlOgAwIBAgIUXy3jmGSXoWBCeGaG4t6S7QkBnKMwDQYJKoZIhvcNAQEL
SGVsbG8gV29ybGQ9GVsbG8gV29ybGQ9GVsbG8gV29ybGQ=
-----END CERTIFICATE-----
```

That middle block is **Base64**. The actual certificate data underneath is binary — Base64 is just how it's safely transported as text across the internet.

> 💡 **Connection to Encapsulation:** Notice the pattern — encode → transport → decode. That's the same idea as encapsulation. Data gets *wrapped* into a safe format, travels, then gets *unwrapped* at the destination. Same concept, different layer.

---

## How Data Actually Flows — Encapsulation

When your app sends data, it travels *down* the OSI layers. Each layer wraps the data with its own header before passing it down. This process is called **Encapsulation**.

```
Application generates:   [ Data ]

Layer 4 adds TCP header: [ TCP Header | Data ]
                          └─── called a SEGMENT

Layer 3 adds IP header:  [ IP Header | TCP Header | Data ]
                          └─── called a PACKET

Layer 2 adds MAC header: [ MAC Header | IP Header | TCP Header | Data ]
                          └─── called a FRAME

Layer 1 converts:        [ 1 0 1 1 0 0 1 0 1 0 1 1 ... ]
                          └─── BITS on the wire
```

> 💡 Each layer only looks at *its own header*. Layer 3 doesn't care what's inside the data — it just reads the IP header and does its job. Layer 2 does the same with the MAC header.

---

## De-Encapsulation — The Receiving Side

On arrival, the process is reversed. Each layer strips off its own header and passes the rest up.

```
Layer 1:  Receives bits off the wire → converts to Frame
Layer 2:  Reads MAC header → checks if it's addressed here → strips MAC header → passes PACKET up
Layer 3:  Reads IP header → checks destination IP → strips IP header → passes SEGMENT up
Layer 4:  Reads TCP header → checks port → strips TCP header → sends DATA to correct application
Layer 7:  Application receives the data
```

```
Arriving bits  →  Frame  →  Packet  →  Segment  →  Data  →  App
                  (L1→L2)   (L2→L3)    (L3→L4)    (L4→L7)
```

---

## The Full Picture

```
SENDER                                      RECEIVER
─────────────────────────────────────────────────────
App                   [ Data ]                           [ Data ]     App
                ↓ wrap                                ↑ unwrap
L4              [ TCP | Data ]                     [ TCP | Data ]     L4
            ↓ wrap                               ↑ unwrap
L3         [ IP | TCP | Data ]                [ IP | TCP | Data ]     L3
      ↓ wrap                                ↑ unwrap
L2   [ MAC | IP | TCP | Data ]          [ MAC | IP | TCP | Data ]     L2
  ↓ convert                              ↑ convert
L1    [ 1 0 1 0 1 1 0 1 0 ... ]         [ 1 0 1 0 1 1 0 1 0 ... ]     L1

                            (wire / wifi)
```

---

## Summary Table

| Layer | Name | Goal | Addressing | Key Devices |
|---|---|---|---|---|
| 7–5 | Application | User-facing data, encoding | — | Browser, Email client |
| 4 | Transport | Service-to-service | Ports (TCP/UDP) | — |
| 3 | Network | End-to-end delivery | IP Address | Router |
| 2 | Data Link | Hop-to-hop delivery | MAC Address | Switch, NIC |
| 1 | Physical | Move bits on the wire | — | Cable, WiFi, Hub |
