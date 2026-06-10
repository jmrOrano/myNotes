

*Dear me: Before arraning your notes in order. PLEASE produce atleast a readable and structured template*



Do search SIEM
### PERMISSONS

- Check for file with `suid` `-rwsr-xr-x root root file`
	- `find / -perm -4000 2>/dev/null`



TYPOSQUATTING
PHSING WITH VALID CERTS


PRACTICAL TLS/ SSL DEEPDIVE
https://www.youtube.com/watch?v=HMoFvRK4HUo&list=PLIFyRwBY_4bTwRX__Zn4-letrtpSj1mzY


**From Bandit wargames**
[[Whatis#**What is Base64?**|Base64]]
[[Whatis#**What is hexdump?**|xxd Hexdump]]
Binwalk
steghide

CTF-style
 - appended bytes
 - EXIF metadata
 - LSB Manipulation
 - stegenography

---

### **How professionals use hexdump**
*For reference about what is hexdump read here: [[Whatis#**What is hexdump?**|What is hexdump]]*

Pros reading hex dumps are essentially doing **pattern recognition**. \

#### **1. Magic bytes** — the first thing they check
*Every file format has a "signature" at the very start. Pros have these memorized:

| What you see at offset 0 | What is means                      |
| :----------------------: | ---------------------------------- |
|          1F 8B           | gzip file                          |
|         42 5A 68         | bzip2file                          |
|       50 4B 03 04        | ZIP file (also .docx, .xlsx, .jar) |
|         FF D8 FF         | JPEG image                         |
|       89 50 4E 47        | PNG image                          |
|       7F 45 4C 46        | ELF binary (Linux executable)      |
|          4D 5A           | Windows .exe                       |
> *If a file claims to be a JPEG but the first bytes aren't `FF D8 FF`, something is wrong — corrupted, renamed, or tampered with.*

#### **2. Structural expectation** — knowing what "normal" looks like
*Different data has a characteristic "texture" in hex:*

- **Plain text** — (like a password): you'll see bytes mostly in the `20`–`7E` range, which is the ASCII printable range. The right-hand ASCII column in `xxd` will show readable characters.
- **Compressed data**: —  looks like random noise — bytes spread all over `00`–`FF` with no pattern. If you see a "compressed" file that has huge runs of `00 00 00 00`, that's suspicious — good compression shouldn't have that.
- **Executable code**: — dense, chaotic, but with readable strings scattered in (function names, error messages).
- **Encrypted data**: — looks like compressed data (random noise), but _perfectly_ uniform — no patterns at all.

---

>**Corrupted Files** — suppose a PNG should begin with:
```
89 50 4E 47
```
But instead :
```
89 50 4E 00
```
One byte changed. And the image viewer might fail. 

---

>**Network Analysis** — Suppose you're using `tcpdump` or `wireshark`.
You see: 
```
47 45 54 20
or
50 4F 53 54
```
Convert to ASCII:
```
GET
or
POST
```

---

>**Malware Analysis** — A suspicious executable contains:
```
63 6D 64 2E 65 78 65
or
70 6F 77 65 72 73 68 65 6C 6C
```
ASCII:
```
cmd.exe
or
powershell
```

>**Reverse Engineering** — suppose a program stores:
```
41 64 6D 69 6E
```
ASCII:
```
Admin
```
A reverse engineer may discover usernames, messages, URLs, API keys, file paths, and so on.

---

>**Looking for suspicious repetition** — Imagine a file contain varied data.

Instead you see this for a thousands of bytes
```
00 00 00 00 00 00 00
```
That can indicate:
- erased data
- uninitialized memory
- corruption
- wiped storage

Or:
```
FF FF FF FF FF
```
That can also be meaningful depending on the context.

---

>**Checksums and length fields** — Many formats store the _expected_ file size or a checksum inside the file itself. 

Pros cross-check:
- Does the length field in the header match the actual file size?
- Does the CRC (checksum) at the end match a recalculation?
A mismatch here almost always means corruption or tampering. `gzip` actually has this built in — it stores a CRC32 at the end, which is why `gunzip` sometimes says "CRC error" when a file is damaged.

---

##### **The practical workflow**
When a pro suspects something is wrong, they typically go:
```
xxd suspicious_file | head -4     # check magic bytes
xxd suspicious_file | tail -4     # check the end (some formats store metadata here)
xxd suspicious_file | grep -v "00 00 00 00"  # look for meaningful structure
```

>**The bigger secret** — Most professionals don't memorize random hex values.
They memorize common patterns.

ASCII Ranges:
```
41-5A = A-Z
61-7A = a-z
30-39 = 0-9
```

>*A good exercise: run `xxd` on a `.jpg`, a `.png`, and a plain `.txt` file side by side. You'll immediately see how different their "textures" are — and that intuition is exactly what pros are using.*

---



### CHECK IF LOGS IS TAMPERED(INCOMPLETE)

#### Journal Integrity checks (Systemd logs)
#### File integrity monitoring (the real answer)

#### Tripwire 
Similar idea  but older but still used

#### Immutable / append-only logs (prevention>detection)

#### Remote logging (best-real-world defense)

#### Auditd

#### The uncomfortable truth

#### Mental Model