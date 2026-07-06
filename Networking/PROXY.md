*June27,2026*
*A proxy is simply an intermediary*

Instead of:
```
Client ---> Server 
```
Youll have:
```
Client --------->Proxy ---------> Server
```
The client talks to the proxy, and the proxy talks to the destination on the client's behalf.

## HTTP Proxy
## HTTPS Proxy

## SOCKS PROXY (SOCK4 & SOCKS5)
*June27,2026*
*Socket Secure* — ==an application level protocol== that tells a client
> "Send your network traffic to me, and i will forward it to the destination"

```
SOCKS   -> The protocol
SOCKS Proxy -> The server that follows a SOCKS rules/protocol
SOCKS client -> Your app that uses SOCKS 
```

#### What Problem It Was Solving
---
Before SOCKS, if a machine was inside a **private network** (like a corporate firewall), it had no standard way to reach the outside internet — because the firewall blocked direct connections.

SOCKS was created to give internal machines a **generic, protocol-agnostic way** to tunnel traffic out through a single point — the SOCKS server.

#### Key Idea
---
SOCKS works at a very simple level:
> “I will create a TCP/UDP connection for you and pass your bytes through it.”

>**It does NOT:**
- understand HTTP
- modify packets
- inspect content (normally)
- cache data
>It is basically a **generic pipe**. 
>==It is completely **payload-blind**== — it operates below the application layer, so it doesn't know or care if you're tunneling HTTP, FTP, SSH, or anything else. It just moves bytes.

>SOCKS operates **on top of TCP (and sometimes UDP)**, and is implemented in user-space software (client + proxy server).

So in the TCP/IP model:
```
Application Layer   ← SOCKS lives here
Transport Layer     TCP / UDP
Internet Layer      IP
Link Layer          Ethernet / Wi-Fi
```

#### How It Works (Conceptually)
---
Instead of:
```
Your Machine ────────────────────→ Destination
```
With SOCKS:
```
Your Machine ──→ SOCKS Server ──→ Destination
                 (does the actual
                  TCP connection)
```
Your machine tells the SOCKS server:
> _"Hey, connect to 142.250.x.x on port 443 for me."_

==The server does it, and then just **pipes the bytes back and forth** — it doesn't inspect or care what's inside.

#### How it Works step by step
---
##### Step 0 — You configure a SOCKS proxy
```
SOCKS proxy = 127.0.0.1:1080
```
Now your application knows:
>“Before connecting anywhere, ask this proxy first.”

##### Step 1 — Client connects to SOCKS server
Your app (browser, SSH client, etc.) does:
```
Connect → SOCKS Proxy
```
>No destination server yet.

##### Step 2 — Client sends a request to SOCKS
It says:
```
Please connect me to:
Destination IP: 93.184.216.34
Destination Port: 80
```
Optionally:
- authentication (SOCKS5 supports this)
- protocol preferences

##### Step 3 — SOCKS proxy opens the real connection
The proxy now becomes the one doing the [[3-WAY_HANDSHAKE]]:
```
SOCKS Proxy → Destination Server
    SYN
    SYN-ACK
    ACK
```
>So the destination server thinks the proxy is the client

##### Step 4 — Tunnel is established
After connection succeeds:
```
Client ⇄ SOCKS Proxy ⇄ Destination Server
```
>Now SOCKS just forwards raw bytes in both directions.

#### VERSIONS
---

| SOCKS4              | SOCKS5(current standard)          |     |
| :------------------ | --------------------------------- | --- |
| TCP connection only | TCP and UPD                       |     |
| No Authentication   | Authentication Support(user/pass) |     |
| No UDP support      | Remote DNS resolution built-in    |     |
| No remote DNS       | IPV6 support                      |     |



## Reverse Proxy 