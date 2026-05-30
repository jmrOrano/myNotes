

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



### **SSL/TLS Certificate and Its role in HTTPS/SSL/TLS**
*Overview: ssl cert is the key uses to decrypt incoming https traffic*

#### **Why use https?**
- **Encryption** (Confidentiality), without it, data travels on plain text, everyone intercepting the network can read it. Everyone can read the password you entered.
- **Authentication** (Trust), https verifies that youre actually talking with the legit website. Withtout https, attacker can trick you into thinking youre on legit site even though youre not. *Imagine going into fake bank website*. The SSL Certificate proves the servers identity.
- **Data Integrity**, ensures that data hasnt been tampered with during transmission. If someone tries to modify youll detect it. 
- **Compliance & Trust**
	-  Search engines (Google) rank HTTPS sites higher
	- Browsers warn users when sites aren't HTTPS
	- Payment processors (PCI DSS) require HTTPS
	- Regulations (GDPR, HIPAA, etc.) often mandate encryption for sensitive data
	- Users see the padlock icon—a visual signal of security

**Real-world impact:** Without HTTPS, logging into Gmail on a coffee shop WiFi could expose your credentials to anyone on that network. With HTTPS, your password is encrypted from your browser to Google's servers.

#### **What is SSL Certificate?**
*Overview: a digital file that enables HTTPS*
Its human readable form its like a random text. But it contains the ff:
- **The website's public key** (used for encryption)
- **The website's identity information** (domain name, company name, location)
- **A digital signature** from a trusted Certificate Authority (CA) that says "I verified this is legit"
- **Validity dates** (when the certificate expires)
- **Other metadata** (encryption algorithm used, etc.)

#### **Where do we get SSL Certificate?**

**CERTIFICATE AUTHORITIES(CAs)** - This is the primary and most common way.
*A **Certificate Authority** is a trusted organization that issues SSL certificates. They verify your identity and then digitally sign a certificate for you.*

Examples of well-known CAs:
- DigiCert
- Let's Encrypt (free)
- Sectigo
- GoDaddy
- Comodo
- GlobalSign

**HOW YOU GET ONE FROM A CA?
- You generate a certificate request (CSR - Certificate Signing Request) on your web server
- You submit it to a CA
- The CA verifies you own the domain (via email, DNS records, or file upload)
- The CA signs it with **their private key** (making it trustworthy)
- You download the certificate and install it on your web server**

>[!Note] With cloudflare setup
>With use of cloudflare. They became an intermediary between you and the CA. 
>- in generating cert from them, they are the one who generates the CSR

##### **SELF-SIGNED CERTIFICATES (not recommended for production)**
You can technically create your own certificate without a CA, but browsers will warn users "this site is not secure" because no trusted authority vouched for it.

>[!Question] Why Self-signed is not recommended.
>Think of it this way:
> *Imagine someone hands you an ID card that says "I am a legitimate person, trust me" — but **they made the ID themselves.** There's no government, no authority that verified and issued it. Would you trust it?*
>
>That's exactly what a self-signed certificate is. **The website is vouching for itself.**
>- A legitimate cert says: **"DigiCert/Let's Encrypt verified I am really example.com"**
>- A self-signed cert says: **"Trust me bro, I am really example.com"**
>
>**No trusted CA signed it = browsers don't trust it = users see this:**
> ⚠️ "Your connection is not private" / "This site is not secure"
> 
>>[!Note] **But here's the nuance — encryption still works!**
>>
>>
>>A self-signed cert **still encrypts traffic** just fine. The encryption itself isn't broken. The problem is purely >**identity verification** — you can't confirm WHO you're encrypting with.
>>
>>This is dangerous because:
>>
>>- An attacker could set up a fake site
>>- Use a self-signed cert on it
>>- Your traffic is encrypted... but to the **wrong server**


#### **Keys and Cryptography**

###### **Assymetric Encryption** 
- uses **two mathematically linked keys** — what one key encrypts, only the other can decrypt.
- the public and private key is used to verify the identity of the owner and its public key so that trust is built

- **Public Key** — shared with everyone openly. Anyone can have it.
- **Private Key** — kept secret. Only the owner has it. Never shared.

##### **What is Symmetric Key algorithm?**
Its is use once the connection is established. Used to encrypt and decrypt all the traffic between the server and the client.


##### **How does things work?**
	**What is TLS Handshake?**


**How does it work?**
Behind an HTTPs an SSL cert plays a role. By definition an SSL Cert is a webserver's digital signature

Step1: Client browser requests secure pages from a server(`https example.com`)

Step2: The example.com server sends its public with its SSL Certificate. Which is digitally signed by a CA

Step3: Once the client's browser gets the certificate, it will check the issuers digital signature to make the cert is valid(the digital signature is made by CA's private key). *Note: browsers are installed with many majors CA's public key.*

Step4: Once the verification is done, its time to exchange a secret. The client's browser creates a one [[#**What is Symmetric Key?**|symmetric key or shared secret]] . It keeps one and gives a copy to the web server. It uses the web server's public key to encrypt the secret and sends it to the web server. 

Step5: When the web server gets the encrypted symmetric key, it uses its private key to decrypt it. Now the web server gets the browser's shared key.

From now on. the traffic between them will be encrypted and decrypted with the same key.



### **ARP (Address Resolution Protocol)** 


OTHER CONCEPTS:
INTERNAL HTTPS VS EXTERNAL HTTPS