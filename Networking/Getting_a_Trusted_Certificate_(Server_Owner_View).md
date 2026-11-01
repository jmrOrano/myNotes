*October 31, 2026*
*Related: [[Chain_of_Trust_(Certificate_Authority)]], [[Nextcloud AIO - Cloudflare + NPM]]*

## The Question
As a server owner, how do I get a CA's trust?

## Short Answer
You don't ask a Root CA directly. You get a certificate from a CA that **browsers and operating systems already trust**.

---

## ==🟢How to Get Trusted==

==**1. Pick a trusted CA**==
- DigiCert
- GlobalSign
- Sectigo
- Let's Encrypt

==**2. Prove you own the domain**==

The CA must confirm you're authorized to use the domain. Common methods:
- **DNS validation**: add a specific record to your DNS.
- **HTTP validation**: place a verification file on your server.
- **Email validation**: confirm via an authorized email address for the domain.

==**3. The CA issues the certificate**==

Once verified, the CA issues a certificate for your domain.

**==4. Install it on your server==**

You usually configure three things:
- **Server certificate** (for your domain)
- **Intermediate certificate(s)** (the chain of trust)
- **Private key** (keep it secure, it stays with you only)

> The Root CA certificate normally doesn't need to be installed on the server, because it's already in the client's or browser's trust store.

---

## ==🟢Simple Illustration==

```
Trusted Root CA            <- already trusted by browser / OS
      | authorizes
Intermediate CA            <- issues certificates
      | issues
Your Server Certificate    <- www.yourdomain.com
```

---

## ==🟢What About Internal Servers?==

If it's an internal company system and you don't want to buy a public certificate, you can run your own **Private CA**.

The catch: the IT team must **install and trust the Root CA certificate** on every company-managed device. Otherwise the browser still shows a certificate warning.

---

## Key Takeaway

> The CA gives trust to your certificate, but the **browser or client decides** whether that CA is trusted.
