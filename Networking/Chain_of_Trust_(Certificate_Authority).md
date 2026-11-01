*October 31, 2026*
*Related: [[RANDOM FOR NOW#SSL/TLS Termination|SSL/TLS Termination]], [[Tools#openssl|openssl]], [[Nextcloud AIO - Cloudflare + NPM]]*

## The Question
When I visit a website, how does my browser know it's the **real** site and not an imposter?

Answer: a **Certificate Authority (CA)** vouches for the site. Here's the story, step by step.

---

## Before Anything Happens
- My browser/OS **already has a pre-installed list of trusted CAs** (called *root CAs*). I didn't add them. They came with the browser.
- The website owner **already got a certificate** from a CA (e.g. Let's Encrypt). The CA checked they own the domain, then **signed** the certificate.

A certificate is basically: *"This public key belongs to `example.com`. Signed, CA."*

---

## The Walkthrough

**1. I open my browser and visit `https://example.com`**

**2. The server sends back its certificate**
- It says: "I'm `example.com`, here's my public key."
- It is signed by an **Intermediate CA**.

**3. My browser asks: "Do I trust this signer?"**
- The Intermediate CA is **not** in my pre-installed list.
- But the server also sent the Intermediate's certificate.
- That one says: "I was signed by **Root CA X**."

**4. My browser checks: "Is Root CA X in my list?"**
- Yes. It's pre-installed.

**5. Now the browser verifies the chain, going back down**
- Root CA X's signature on the Intermediate: valid.
- Intermediate's signature on the website certificate: valid.

```
Root CA X          <- already in my browser (trusted)
   | signed
Intermediate CA
   | signed
example.com cert   <- what the server showed me
```

**6. A few quick checks**
- Is the certificate expired? No.
- Does the name on it match `example.com`? Yes.

**7. One last thing: "Are you really the owner?"**
- Anyone can copy a certificate (it's public).
- So the server must **prove** it has the matching **private key**, by signing something during the handshake.
- Only the real owner can do that.

**8. Verified. Lock icon appears. Encrypted connection starts.**

If any step fails, I get the **"Your connection is not private"** warning.

---

## Why the Chain?
The root CA keeps its key locked away and rarely uses it. The intermediate does the daily signing. If an intermediate gets hacked, only that one is cancelled, not the whole system.

---

## My Setup
- **Let's Encrypt cert**: browsers trust it (its chain ends at a root in their list).
- **Cloudflare Origin Cert**: browsers do **not** trust it. Only Cloudflare does. That's fine, because browsers talk to Cloudflare, and Cloudflare talks to my server.

```
Browser <-- Cloudflare's cert --> Cloudflare <-- Origin Cert --> My server
```

---

## Remember
1. The CA **vouches for identity**. It doesn't encrypt anything.
2. The CA **never sees** the site's private key.
3. Trust starts from the **pre-installed list** in the browser.

---

## Try It
```bash
openssl s_client -connect cloud.oranolab.com:443 -showcerts
```

>[!Question] Expand later when curious
>- How the TLS handshake works
>- How revoked certificates are detected (OCSP / CRL)
>- Certificate Transparency logs
