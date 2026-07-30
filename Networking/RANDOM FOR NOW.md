

How apache do the auto HTTPS vai


### **REVERSE PROXY**
*Example: Nginx, Apache, HAProxy, Caddy, Traefik etc.*
*Overview: a server that is in front of backends servers and forwards clients request to them. From client's perspective, they're talking directly to the proxy, they never see the actual servers.*

Compare this to a **forward proxy**, which sits in front of the _client_ (e.g., a VPN or corporate proxy). A reverse proxy sits in front of the _server_.

**How it works**
```
ClientMachine --> request --> Reverse Proxy --> Backend Server(s) --|
Client Machine <-- reply <-- Reverse Proxy <-- Backend Server(s) <--|
```

**Why use reverse proxy?**
- ***Load balancing***
- ***SSL/TLS Termination***
- ***Security & hiding infrastructure***
- ***Caching***
- ***Compression***
- ***Single Entry Point for multipler services***
- ***Rate Limiting & DDos Protection***

>[!Note] 
>*I misunderstood how a reverse proxy do auto https. I thought its:*
>```
>Reverse proxy encrypts --> sends HTTPS to internet
>```
>But its acutally
>```
>Internet sends HTTPS --> Apache decrypts it using cert --> plain HTML internally
>```
>
>The word **"terminate"** just means **"this is where HTTPS stops."** Apache is the one holding the SSL certificate from Let's Encrypt. So when your browser sends an encrypted HTTPS request, Apache is the one that:
>- Receives the encrypted request
>- Uses the certificate to **decrypt** it
>- Now has a plain readable request
>- Forwards that plain request internally to Nextcloud


##### SSL/TLS Termination
*Overview: The first thing understand in reverse proxy is **who really the client browser talking to?** because this this is where the whole idea of **SSL/TLS Termination revolves***

**Without reverser proxy:**
- The encrypted connection hits the backend directly
- The backend has the certificate
- The backend will be the one directly do the decryption 
```
Client's Browser <----- HTTPS -----> Backend Server
```

**With reverse proxy**
- The RP the one receives the HTTPS traffic
- The RP has the one with SSL Certificate
- THe RP is the one that decrypts the encrypted traffic
- The RP is the one that pass the request to backend
```
Browser <----- HTTPS -----> Reverse Proxy <----- HTTP/HTTPS -----> Backend
```
This is where the *TLS Termination* term comes into. Because this is where the HTTPS connection from clients side comes to an end and this is where the part where the RP do the encryption and pass the request internally as HTTP(most of the time)



### Asymmetric Authentication
---
July 06, 2026
Example in this topic is: [[SSH_Setup#**SETTING IT UP WITH KEYPAIRS**|SSH with keypairs]](SSH public key authentication)

There are two things: 
- **Public Key**
- **Private Key**
Those two can be generated using tool `ssh-keygen`. 
- The Private key. Stays on the Client
- The Public Key. **Gets copied**(not move) to server side.

**The Authentication Flow is:**
1. You generate a key pair with `ssh-keygen`.
2. You keep the **private key** on your machine and place the **public key** in the server's `~/.ssh/authorized_keys`.
3. When you connect, the server sees that your public key is authorized and sends your client a **random challenge** (a piece of unpredictable data).
4. Your SSH client uses your **private key** to digitally **sign** that challenge and sends **only the signature** back to the server.
5. The server uses your **public key** (from `authorized_keys`) to verify the signature.
6. If the signature is valid, the server concludes:
> 	*"Only someone possessing the matching private key could have created this signature."*
7. Authentication succeeds, and the SSH session continues.

#### Misconception
**The server does not compare the private key to the public key.**
The server **never sees your private key**. Instead, it verifies that the signature produced by your client is mathematically valid for the stored public key. This is one of the fundamental properties of public-key cryptography.

---

### **ARP (Address Resolution Protocol)** 


OTHER CONCEPTS:
INTERNAL HTTPS VS EXTERNAL HTTPS

### SOCKETS
*June 27,2026*
*An OS-level endpoint for network communication.*

>It is how programs send/receive data.
```
Process A  ⇄  SOCKET  ⇄  TCP/IP stack  ⇄  network  ⇄  SOCKET  ⇄  Process B
```

>In simple term
- Os provides system call to manage communication like:
```
sockets() | bind () | 
listen()  | accept() | 
connect() | send() / recv()
```
These are the actual building blocks. They look like function in python perspective. Because they are function(but not in python.). Under the hood, they transition into the kernel via system call(syscall) interface

>Behind the scene when you call `connect()`
```
Your program (user space)
   ↓
libc wrapper (optional)
   ↓
syscall instruction
   ↓
Linux kernel (network stack)
   ↓
TCP/IP processing
   ↓
Network interface
```
So your program is basically saying: *"Kernel, please handle this network operation for me.”*

If confused you can refer to practical TCP/IP Stack
- [[How_host_do_speaks_on_the_internet#Layer Placement for Real world]]

### Ephemeral Port
July 07, 2026
is a short-lived, temporary network communication endpoint automatically assigned by an operating system to a client application for the duration of a single communication session. Once the session ends, the port is released and becomes available for reuse.

#### How They Work in Client-Server Connections

**When** a client (such as your web browser) wants to view a webpage, it must initiate a connection with a server.
- **The Server Side:** The server uses a fixed, "well-known" port to listen for incoming requests (e.g., **Port 80** for HTTP or **Port 443** for HTTPS). 
- **The Client Side:** The client's operating system automatically assigns a random, temporary port (an ephemeral port) as the return address for the communication. This ensures the server knows exactly where to send the webpage data back to your device. 

---


