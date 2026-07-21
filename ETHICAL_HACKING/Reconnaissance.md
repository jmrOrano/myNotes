July 08, 2026


## Short Intro
---
***You cant attack what you don't understand***

### 2 Major Types of Information
---
Tools exist but tools dont know whats useful in the human sense. 
The human decides what matters and there are two (2) types of Personal Information.

- **Personal Information (PI)** - info that identifies a person
	- Name, Address, Email, Phone Number
- **Sensitive Personal Information (SPI)** - Info that requires stronger protection because misuse can cause significant harm
	- Bank acc info, Passwords, Passport number etc.

---
### Two Major Types of Recon
---
**There are two types of Recon how the Information is obtained**

- **Passive Recon** - stealthy-gathering method. No direct probes that touches the target infra
- **Active Recon** - Directly probing the target's private network system or infra

Then, there's the **technique** — a specific activity performed to obtained information.
If can be: 
- Search Engine Recon
- Enumeration
- Scanning
- Satellite Geospatial & Location OSINT
- Metadata Analysis
- Cross Platform Correlation 
- Etc.

Then, there's The **Tools** — used to execute techniques and to make things easier most of the time

---
### What to do with Information gathered? 
---

**The key idea is:**  *The usefulness of information depends on the **context, objective, and how it can be used**.*
This is where OSINT **transitions from information gathering to decision making**

**The action depends on the person's role:**
- **Threat actor:** exploit, phishing, credential attacks, lateral movement.
- **Penetration tester / bug bounty hunter:** identify attack paths and responsibly report vulnerabilities.
- **Investigator:** attribute identities, trace activity, or gather evidence.
- **Defender:** reduce exposure, fix misconfigurations, and strengthen security.

So, OSINT rarely ends with "I found information." It usually ends with:

> **"Based on what I learned, what should I do next?"**

> **Information is useful if it helps answer a question about the target or enables the next step in an attack or security assessment.**


*A birth date, for example, isn't valuable by itself—but it may be used in identity verification, password guessing, or to craft a convincing phishing message. 
An exposed subdomain isn't valuable by itself either—but it may reveal a forgotten application that's vulnerable.*

```
Reconnaissance
|
├── Passive Recon
│   |
│   ├── Methodology: OSINT
│   |
│   └── Techniques:
│       ├── Google Dorking
│       ├── Username enumeration
│       ├── Metadata analysis
│       └── WHOIS/DNS research
│
└── Active Recon
    |
    └── Techniques:
        ├── Port enumeration
        ├── Service enumeration
        ├── Directory discovery
        └── Banner grabbing
```

****

## OSINT
---
July 08, 2026
*Open Source Intelligence* 
-  a PROCESS or METHODOLOGY of collecting info from publicly available sources.
- NOT  a tool but, rathe a METHODOLOGY
- a PASSIVE TYPE of RECON
>There are thousands of tools out there. Covering them all in this md note will get noisy.  So refer to the open source website for more tools:

-  [OSINT-Framework](https://osintframework.com/)
- [Github-Repo-Source](https://github.com/lockfale/osint-framework)
---
### Google Dorking
---
July 08, 2026
- A **technique** used within OSINT 
- Uses GOOGLE SEARCH as a tool/platform to perform
- Uses ADVANCE OPERATORS (e.g `site:`, `filetype:`, etc) to find publicly extended information more effectively.
- Specialize in speed to find and answer questions. But target only the **indexed web content** 

**Typical Target to use:**
- Public Documents
- Domain and Subdomains
- Source 

---
#### How to google dork
*This note cant cover it all (i think)— so it would be better to search online for advance operators or cheat sheet.*
[CheatSheet](https://gist.github.com/sundowndev/283efaddbcf896ab405488330d1bbc06)

A simple mental model to appraoch:
```
Search Scope
    ↓
    site:

    What Resource?
         ↓
        filetype:, inurl:, intitle:

        What Content?
             ↓
             intext:, ""

          Filter Results
             ↓
             -, OR, ()
```

Using Operators: There are the `|-OR` , `&-AND`, `- Exclude`, `+ include` 
```
==========Combining Operators=============
(site:yourdomain.com | site:yourdomain2,com) & intext:"login"
site:yourdomain.com filetype:(pdf | xml | txt | docx | xls)

===========Exclude and Include=============
-site:yourdomain.com +site:yourdomain.*
```

Using `before/after` to filter by date:
```
site:yourdomain.com ext:env & (before:2026-12-31 after:2020-12-31)
```

You can also use it for people:
```
"Your Name" (site:facebook.com | site:instagram.com)
```

---


### Username OSINT
---
*July 12, 2026*
- A **technique** 

- People tend to reuse usernames across different websites.
- Threat actor or Investigator can correlate these accounts and build a profile of the same person.

#### Common Tools
---
- [Sherlock](https://github.com/sherlock-project/sherlock)
- [Maigret](https://github.com/soxoj/maigret)
- [Whatsmyname](https://whatsmyname.app)

#### Limitations
---
Finding `username` on two or more wbsites doesnt automatically mean they're the same peson.
It needs corroborating evidence such as:
- Same profile photo 
- Same bio
- Same linked website
- same email address
- cross-references between accounts
Professionals avoid concluding "same username = same person" without additional evidence.

This is also a good example of a broader OSINT principle:
> **One clue rarely proves anything. Multiple independent clues increase confidence.**
That mindset applies not only to username OSINT but to virtually every form of reconnaissance and intelligence gathering.

---
#### Workflow
---
For the goal of *"Oh this username on this website is the same person with the same username at this website"*  type of thing. Then the common work flow is:

```
1. Do username enumeration (via multiple tools)
2. Gather and Merge Results in single file
3. Perform some text processing (Optional/IF necessary)
4. Manual Verification
```
`Manual Verification` will take the most time doing, because tools are good at finding candidates, but **humans are better at judging relationship**



---
