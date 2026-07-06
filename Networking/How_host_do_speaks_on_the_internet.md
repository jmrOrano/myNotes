# Everything a Host Does to Speak on the Internet

---

## Part 1 — Same Network (No Router)

> **Scenario:** Host A and Host B are directly connected (or on the same local network).
> Even if there's a switch between them, the steps are the same — hosts don't know or care what's in between.

### The Setup

```
┌──────────────────────┐          ┌──────────────────────┐
│        Host A        │          │        Host B        │
│                      │          │                      │
│  MAC  : a2a2         │──────────│  MAC  : b3b3         │
│  IP   : 10.1.1.22    │          │  IP   : 10.1.1.33    │
│  Mask : 255.255.255.0│          │  Mask : 255.255.255.0│
└──────────────────────┘          └──────────────────────┘
```

---

### Step 1 — Host A wants to send data to Host B

Host A knows Host B's **IP address** (think of it like knowing someone's home address).
It wraps the data with a **Layer 3 (L3) header** containing the source and destination IPs.

```
┌──────────┬───────────────────────────────┐
│   Data   │  L3 Header                    │
│          │  Src IP : 10.1.1.22 (Host A)  │
│          │  Dst IP : 10.1.1.33 (Host B)  │
└──────────┴───────────────────────────────┘
```

> 💡 **Analogy:** L3 is like writing the city and street address on a package — it tells you *where* it's going.

---

### Step 2 — Host A also needs a Layer 2 header (the problem)

Data can't travel on a physical wire using just an IP address.
It also needs a **Layer 2 (L2) header** with the actual hardware address (MAC address) of the next destination — this is what makes the hop-to-hop delivery possible.

```
┌──────────┬─────────────────┬──────────────────────────────┐
│   Data   │  L3 Header      │  L2 Header                   │
│          │  Src IP: 10.1.1.22 │  Src MAC: a2a2            │
│          │  Dst IP: 10.1.1.33 │  Dst MAC: ???  ← PROBLEM  │
└──────────┴─────────────────┴──────────────────────────────┘
```

Host A knows the **IP** of Host B, but **not its MAC address**.
This is where **ARP** steps in.

> 💡 **Analogy:** You know your friend's home city (IP), but you don't have their door number (MAC). You need to ask around to find it.

---

### Step 3 — ARP to the rescue

**ARP (Address Resolution Protocol)** is used to find out *"Who has this IP? Tell me your MAC address."*

#### ARP Request (Host A shouts to everyone)

Host A broadcasts an ARP Request to the **entire local network**.
It's like shouting in a room: *"Hey! Who has the IP 10.1.1.33? I'm 10.1.1.22 (MAC: a2a2)."*

```
Host A                                       Everyone on the network
   │                                                    │
   │──── ARP Request (Broadcast) ──────────────────────▶│
   │                                                    │
   │   ARP Payload:                                     │
   │   "Who has IP 10.1.1.33?"                          │
   │   "I am 10.1.1.22, MAC: a2a2"                      │
   │                                                    │
   │   L2 Header:                                       │
   │   Src MAC : a2a2                                   │
   │   Dst MAC : ffff.ffff.ffff (Broadcast)             │
```

> 💡 `ffff.ffff.ffff` is a special reserved MAC address meaning **"deliver to everyone"** on the local network.

#### ARP Cache — Where mappings are stored

Every device keeps an **ARP Cache** — a small notebook that records IP → MAC mappings so it doesn't have to ask again next time.

```
Host A ARP Cache (before response):        Host B ARP Cache (before request arrives):
┌──────────────┬────────────┐              ┌──────────────┬────────────┐
│ IP           │ MAC        │              │ IP           │ MAC        │
├──────────────┼────────────┤              ├──────────────┼────────────┤
│ 10.1.1.33   │ Resolving…  │              │ (empty)      │            │
└──────────────┴────────────┘              └──────────────┴────────────┘
```

---

### Step 4 — Host B responds

Host B receives the broadcast. It recognizes that the ARP request is asking for **its own IP**.
It now knows Host A's IP and MAC (they were included in the ARP request), so it updates its own cache.

Then it sends back a **unicast ARP Response** — directly to Host A (no shouting needed this time).

```
Host B                                              Host A
   │                                                   │
   │──── ARP Response (Unicast) ──────────────────────▶│
   │                                                   │
   │   "I have IP 10.1.1.33, my MAC is b3b3"           │
   │                                                   │
   │   L2 Header:                                      │
   │   Src MAC : b3b3                                  │
   │   Dst MAC : a2a2  (directly to Host A)            │
```

After receiving the response, both caches are now populated:

```
Host A ARP Cache (after response):         Host B ARP Cache (after ARP request):
┌──────────────┬────────────┐              ┌──────────────┬────────────┐
│ IP           │ MAC        │              │ IP           │ MAC        │
├──────────────┼────────────┤              ├──────────────┼────────────┤
│ 10.1.1.33   │ b3b3        │              │ 10.1.1.22   │ a2a2        │
└──────────────┴────────────┘              └──────────────┴────────────┘
```

---

### Step 5 — Data is finally sent

With the MAC address resolved, Host A can now complete the L2 header and send the data.

```
┌──────────┬─────────────────────┬──────────────────────┐
│   Data   │  L3 Header          │  L2 Header           │
│          │  Src IP: 10.1.1.22  │  Src MAC: a2a2       │
│          │  Dst IP: 10.1.1.33  │  Dst MAC: b3b3  ✓    │
└──────────┴─────────────────────┴──────────────────────┘

Host A ──────────────────────────────────────────▶ Host B
                   (Data delivered!)
```

When Host B receives the data, it strips off the L2 header (job done for this hop) and reads the data. If Host B needs to reply, it already has Host A's MAC in its cache — **no broadcast needed**.

---

### Full Flow Summary (Local Network)

```
Host A                                                        Host B
  │                                                              │
  │  1. Create Data + L3 header (knows dst IP)                   │
  │  2. Need L2 header → MAC unknown → trigger ARP               │
  │                                                              │
  │────── ARP Request (Broadcast: ffff.ffff.ffff) ─────────────▶ │
  │                                                              │  3. Host B learns Host A's MAC
  │                                                              │     Updates ARP cache
  │◀───── ARP Response (Unicast: directly to Host A) ───────────-│
  │                                                              │
  │  4. Host A learns Host B's MAC                               │
  │     Updates ARP cache                                        │
  │     Completes L2 header                                      │
  │                                                              │
  │════════════════ Data / L3 / L2 ════════════════════════════▶ │
  │                                                              │  5. Host B receives data
  │                                                              │     Strips L2, reads payload
```

---

## Part 2 — Foreign Network (Going Through a Router)

> **Scenario:** Host A wants to send data to Host C, which is on a **different network**.
> A router sits in between.

### The Setup

```
┌──────────────┐         ┌──────────────┐         ┌──────────────┐
│    Host A    │         │    Router    │         │    Host C    │
│              │         │              │         │              │
│ IP: 10.1.1.1 │─────────│ IP: 10.1.1.254│────────│ IP: 10.2.2.1 │
│ MAC: aaaa    │         │ MAC: rrrr    │         │ MAC: cccc    │
│ GW: 10.1.1.254│        └──────────────┘         └──────────────┘
└──────────────┘
```

> 💡 **GW (Gateway / Default Gateway)** = the router's IP address. It's the "exit door" of your local network — preconfigured on Host A.

---

### Key Difference from Part 1

| Situation | Who Host A sends the data to |
|---|---|
| Same network | Directly to Host B (ARP for Host B's MAC) |
| Different network | First to the **Router** (ARP for the Router's MAC) |

The **L3 header still has Host C's IP** as the destination — that never changes across the whole journey.
But the **L2 header only goes as far as the next hop** (the router), and gets replaced at each hop.

---

### The Process

```
Host A                           Router                         Host C
  │                                 │                              │
  │  1. Creates L3 header           │                              │
  │     Src IP: 10.1.1.1            │                              │
  │     Dst IP: 10.2.2.1 (Host C)   │                              │
  │                                 │                              │
  │  2. Checks: Is 10.2.2.1 in      │                              │
  │     my network? NO → use GW     │                              │
  │                                 │                              │
  │  3. ARP for Router's MAC        │                              │
  │     (GW IP: 10.1.1.254)         │                              │
  │                                 │                              │
  │──── ARP Request (Broadcast) ───▶│                              │
  │◀─── ARP Response (Unicast) ─────│                              │
  │     "Router MAC is: rrrr"       │                              │
  │                                 │                              │
  │  4. L2 header complete          │                              │
  │     Dst MAC: rrrr (Router)      │                              │
  │                                 │                              │
  │═══ Data / L3 / L2[dst:rrrr] ══▶│                              │
  │                                 │                              │
  │                                 │  5. Router strips L2,        │
  │                                 │     Reads L3 dst IP ,        │
  │                                 │     ARP for Host C's MAC     │
  │                                 │                              │
  │                                 │══ Data / L3 / L2[dst:cccc] ▶│
  │                                 │                              │
  │                                 │                     6. Host C receives data
```

---

### Why does Host A ARP for the Router, not Host C?

Because **Host C is on a completely different network** — Host A can't reach it directly.
Think of it like sending a package overseas: you don't hand it to your recipient personally. You give it to a **courier (the router)**, who then handles the rest of the delivery.

> 💡 Host A's job is only to get the package to the router. The router figures out the next step.

---

### Summary of Key Concepts

| Term                | What it is                          | Analogy                                   |
| ------------------- | ----------------------------------- | ----------------------------------------- |
| **IP Address**      | Logical address for routing         | Home address (city + street)              |
| **MAC Address**     | Physical address for local delivery | Your actual door number                   |
| **ARP**             | Protocol to find MAC from IP        | Asking a neighbor "which door is theirs?" |
| **ARP Cache**       | Saved IP→MAC mappings               | Your contacts list                        |
| **Broadcast**       | Send to everyone on local network   | Shouting in a room                        |
| **Unicast**         | Send directly to one device         | Whispering to one person                  |
| **Default Gateway** | Router's IP — your network's exit   | The post office / courier                 |
| **L3 Header**       | Carries source/destination IP       | Shipping label (stays the whole trip)     |
| **L2 Header**       | Carries source/destination MAC      | Handoff receipt (replaced each hop)       |

##### Layer Placement for Real world
```
Application Layer
  ├─ Netcat, Browser, SSH, etc.
  ↓
Socket API (user → kernel boundary)
  ↓
TCP/IP Stack (kernel networking subsystem)
  ├─ TCP (3-way handshake, reliability)
  ├─ IP (routing)
  ├─ ICMP (errors, diagnostics)
  ↓
Link Layer
  ├─ Ethernet / Wi-Fi
  ↓
Hardware
```
>**Everything eventually becomes bytes for transmission**

>What about encryption you may ask?
>Encryption is usually implemented above the socket layer.
>For example, when your browser visits an HTTPS site:
```
Browser
   ↓
HTTP
   ↓
TLS encryption
   ↓
Socket API
   ↓
TCP/IP stack
```

