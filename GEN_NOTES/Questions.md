



---
# FS,PERMISSIONS,PROCESSES AND LOGS
>[!Danger] For attackers mindset
>
>**ABOUT FILESYSTEM & FILES**
>```
>-Where do attackers hide malicious fIles that ls wont show by default?
>-If a file is deleted, Is it really gone? How would it be recover?
>-How do they use the `/tmp` as staging area - why is it world-writeable?
>-How do they rename a malicious file to look innocent? — Can it be disguised by extension?
>-How do rootkits manipulate inodes or directory entries to hide files from `find` and ls?
>-What is slack space and can it store data in it to avoid detection?
>```
>
>**ABOUT PERMISSIONS & PRIVILAGE**
>```
>-What are SUID/SGID files — How do they abuse them to escalate  privilages?
>-How do they hunt for SUID/SGID files — and why are they dangerous?
>-How do they find world-writeable directories to drop a payload?
>-How do they abuse misconfigured `sudo` rules to become a root?
>-What does it mean for a file to be owned by root but writeable by other?
>```
>
>**ABOUT CREDENTIALS & SENSITIVE FILES**
>```
>-How is the `/etc/shadow` becomes a target?
>-How are password hashes structured? — what makes them crackable?
>-Where else are credentials commonly stored? (.bash_history, .env)
>-How do they find SSH private key left exposed on a system?
>```
>
>**ABOUT PROCESS AND PERSISTENCE**
>```
>-How do they run a process that survives logout?(nohup, cron, systemd services)
>-What is fileless malware — living only in memory, no file on disk?
>-How do rootkits hide processes from ps by manipulating /proc?
>-How do they established a backdoor that survives reboot?
>```
>
>**COVERING ATTACKS**
>```
>-Which log files record my activity — how do they tamper with them ?
>-How do they clear the .bash_history or prevent commands from being logged? 
>-How do they modify a file's timestamp to avoid looking suspicious?
>-How do they securely overwrite a file so forensics cant recover it?
>```

>[!Note] For defense side
>**ABOUT FILESYSTEM AND FILES**
>```
>-How do they find recently modified files in sensitive directories?
>-How do they find a malicious hidden files?
>-How do they detect a file that was renamed to look innocent?
>-If a file is deleted, How do they recover it? What forensic tools is use to read raw blocks?
>-How do they create a forensic disk image without altering evidence?(dd, dcfldd)
>-How do they mouint a disk image read-only so they dont contaminate the evidence?
>-What is slack space and what forensic evidence might live there?
>```
>
>**ABOUT PERMISSIONS AND HARDENING**
>```
>-How do they audit the SUID/SGID files—and which ones should not be there?
>-How do they hunt for SUID/SGID files — and why are they dangerous?
>-How do they find world-writeable files that an attacker could abuse?
>-What is the principle of least privilage and how do they enfore it?
>-How do they lock down a sudo so only specific commands are allowed?
>```
>
>**ABOUT CREDENTIALS & ACCESS **
>```
>-How do they audit `/etc/passw` and `/etc/shadow` for suspicious accounts?
>-How do they enforce a strong password policies and hashing algorithm?
>-How do they detect exposed credentials in config files, .env, history?
>-How do they monitor and restrict SSH key access? 
>```
>
>**PROCESS & SYSTEM MONITORING**
>```
>-How do they detect a suspicious process — high CPU, unknown name, no parent?
>-How do they use /proc to inspect what files and network connections a process has open?
>-How do they detect unauthorized cron jobs or systemd services addedd for persistence?
>-What is `lsof` and how does it reveal what a process is doing right now ?
>```
>
>**LOG ANALYSIS & DETECTION**
>```
>-Which logs matter most? — And what attack patterns do they reveal?
>-How do they use the `grep command` to hunt for brute force, failed logins, or lateral movement in logs?
>-How do they detect if logs have been tampered with or cleared?
>-How do they build a file integrity baseline and detect changes? (AIDE, tripwire)
>```

**About File Systems**
```
- How do attackers hide files on a Linux system?
  (hint: hidden files, alternate locations, inode manipulation)

- If a file is deleted, is it really gone? How would you recover it?
  (connects directly to your block + inode knowledge)

- How do rootkits manipulate inodes or directory entries
  to hide malicious files from ls and find?

- What is slack space and why do forensic investigators care about it?
  (deleted file remnants living in unused block space)

- How would you detect a file that was renamed to look innocent?
  (stat, inode numbers, file signatures vs extensions)
```

>**Navigation and file reading**
```
- Which Linux files and directories are most sensitive
  and why? (/etc/passwd, /etc/shadow, /var/log, /proc, /tmp)

- Why is /tmp a common staging ground for attackers?
  (world-writable, survives session, often not monitored)

- What logs exist on a Linux system and where?
  How would an attacker cover their tracks by manipulating them?

- What is /proc and why can it reveal running processes,
  network connections, and memory — without touching the disk?
```

>**From grep, find, and searching:**
```
- How do you hunt for SUID/SGID files — and why are they dangerous?
  find / -perm -4000 -type f

- How do you find world-writable files and directories?
  find / -perm -o+w -type f

- How do you search for files owned by root but writable by others?

- How would you grep through logs to detect brute force attempts,
  failed logins, or suspicious patterns?

- How do you find recently modified files in sensitive directories?
  (find /etc -mtime -1)
```


# NETWORKING

### About the 3 way handshake
*June 27, 2026*  
Reference : [[3-WAY_HANDSHAKE]]

>**What Protocol is being used?** 
>>*TCP mechanism, operating at Transport layer (L4) *

>**What type of data is sent during the handshake?**
>>*No application data is sent. Things are pure TCP control segments — just pure headers and flags (`SYN ACK, ISN(Initial Seq Number`)*

>**Does the 3-way handshake comes first ? or both machines needs to knows each other information first in accordance to tcp stack ?**
>> *It comes before any TCP application data. Buuuut — lower layers have to be ready before the TCP handshake can even begin. To physically send that SYN packet, the machine needs to know where to send it at every layer below.*
>>
>>Read it here for more : [[3-WAY_HANDSHAKE#Full Sequence Before Any Data Flows]]


### About SOCK
*June 27, 2026*
Reference: [[PROXY#SOCKS PROXY (SOCK4 & SOCKS5)]]

>**Does SOCKS always use loopback(127.0.0.1)?**
>>No. It is common but not required. Other uses are:
>>- Local SOCKS proxy 
>>	Example: SSH dynamic forwarding 
>>	```
>>	ssh -D 1080 <user>@<remoteHost>
>>	```
>>- Remote SOCKS Proxy — server can be anywhere
>>- Bound to All interface
>>	```
>>	0.0.0.0:1080
>>	```
>>	- Accessible from other machines
>>	- used in shared proxy servers
>>
>
>**Why loopback is commonly used?**
>>A. Security — only the local machine can access it
>>B. Control Boundary — creates a clean separation 
>>C. No Routing Overhead — Never leaves the host network stack


---

# USER MANAGEMENT


>**Whats the importance of having knowledge in User management when it comes to cybersecurity POV or attacker's POV ?**
>>
>>**Attacker' POV** — Why users matter
```
Every system breach eventually comes down to ONE thing:
"What can THIS user do?"
```
>>Privilege Escalation — Attackers rarely got root immediately. They usually
```
1. Compromise a low-privilege user (web app exploit, phishing, etc.)
2. Look for ways to become a HIGHER privilege user
3. Eventually reach root/admin
```
>>Things attackers look for regarding users.
```
- Weak/reused passwords
- Users with unnecessary sudo access
- SUID binaries (programs that run as a different user)
- Misconfigured permissions
- Default/leftover accounts nobody removed
- Users with same password across multiple machines
```
>>
>>**In Defender's POV — Why it matters**
>>*Follows the Principle of Least Privilege:*
```
Give each user ONLY the access they need — nothing more.

Think of it like hotel keycards — a guest's key only opens THEIR room, not the entire hotel. If a guest's key gets stolen, the damage is contained to one room.

If every user had master keys (root access), one compromised account = **entire system compromised.**
```
>>User management is also defense:
```
- enforce least privilege
- restrict sudo rules
- disable unused accounts
- rotate credentials
- separate service accounts
- monitor login behavior
```

## About Process
>How a Zombie process is used and created for potential attack?
>How does PID 1 (systemd) clean zombies automatically?


# DOCKER
What's the difference of reading the log file directly inside the container vs using the `docker logs` command? 