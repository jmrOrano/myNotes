

How apache do the auto HTTPS vai


#### **REVERSE PROXY**
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



SSL/TLS Certificate
*Overview: the key uses to decrypt incoming https traffic*


SELF-SIGNED CERTIFICATE (())