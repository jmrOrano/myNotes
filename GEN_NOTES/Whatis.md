	***use H5*** 


---


---
##### **What is a kernel?**
*Compilation of codes — mostly written in C with small parts of assembly.*
*It sits between the apps and the hardware, like a middleman*

**What does it do?**
- CPU Scheduling — decides which process runs and when.
- Memory Management — assigns RAM so apps dont overwrite each others brain
- Device Control — talks to hardware (keyboard, ssd, GPU) through drivers.
- System calls bridge — apps cant touch hardware directly, so tehy ask kernel via `syscalls`
##### The `env` (environment variables)
A `environment variables` ay *configuration values na available sa shell at sa mga programs habang nag rurun sila*

**Paano ito nagana technically?**
--Kapag nag-run ka ng program:
1. Shell reads the environment variables
2. Then pinapasa niya ito sa program
3. Then program use them for behavior

**Example** 
***`export EDITOR=nano`*** :  kung may program na gumamit ng `$EDITOR`, automatic nano ang gagamtin. But this is only temporary session. 

**TEMPORARY VS PERSISTENT SESSION**
`export MYVAR=hello` : mawawala kapag isinara ang terminal
`export PATH=$PATH:/home/orano/mytools` : this one is automatic every shell session. 

---
##### GNU 
--isang **project at set ng tools** na nagbibigay ng **core utilities at software components** para sa isang free/open-source operating system.

**GNU ≈ Windows Command Prompt tools + system utilities**  
Pero mas malaki, mas open, at core part ng Linux ecosystem.

 **Core Idea**
Linux kernel lang = **heart of the OS** (handles hardware, processes, memory)  
Pero **para maging usable OS**, kailangan mo rin ng:

- shell (`bash`)
- file manipulation tools (`ls`, `cp`, `mv`, `rm`)
- text editors (`nano`, `vim`)
- compilers (`gcc`)
- system utilities
Lahat ito ay bahagi ng **GNU Project**.

**Think of Linux OS like:**
**Kernel** -> Linux  
**Core tools** -> GNU  
**UI** -> GNOME, KDE, Cinnamon  
**Apps** -> Package manager, Flatpak, Snap


---

##### Deamons
-- background process na laging tumatakbo para magbigay ng service
--Note : can be control by `systemctl`
- Hindi mo siya directly ini-interact (usually)
- Automatic siyang nagra-run
- Siya ang “gumagawa ng trabaho sa likod”

**2. Real-life analogy**
Isipin mo ang computer mo parang **restaurant**:
- Ikaw = customer
- Terminal/GUI = waiter
- Programs (ls, cat, etc.) = orders
- **Daemons = kitchen staff**
Hindi mo sila nakikita  
Hindi ka direktang nakikipag-usap sa kanila  
Pero sila ang gumagawa ng actual work

**3. Concrete examples**
May daemon na:
- nagma-manage ng network connection

*Example:*

**`Scheduler (cron)`** - nagpapatakbo ng scheduled task , like auto back up and update.
**`rsyslog`** - nag rerecord ng logs
**`NetworkManager`**
*Ikaw:*
`- click ka lang ng WiFi `
*Behind the scenes:*
`- daemon ang kumokontrol`
**`SH (remote login)`**
`sshd`   
- “d” = daemon
- naghihintay ng incoming connections


**4. Why background?**
Kasi hindi sila designed para kausapin directly tulad ng: **`ls  cat  nano`**
Instead:
- **nag-aantay sila ng events**
- then they act

 **5. Important behavior**
 
Ang daemon:
✔ starts automatically (boot)  
✔ runs continuously  
✔ reacts to requests/events

|                  flow                   |
| :-------------------------------------: |
|              System boots               |
|              Daemon starts              |
|                  waits                  |
| something happens (e.g wifi connection) |
|            daemon handles it            |

**6. May 'd' sa dulo**
Shortcut way para malaman:  
“Ah, service ito sa background”
`sshd`   → SSH daemon
`httpd`  → web server daemon
`crond ` → cron daemon


---

#### Regex-Regular Expression
*March 08, 2026*
--Use para sa precise na paghahanap ng text. 
--Commonly use with [[Commands#THE `grep` Command|grep]]

| expression | sample use                  | desc                                            |                                            |
| :--------: | --------------------------- | ----------------------------------------------- | ------------------------------------------ |
|    `^`     | `grep "^root" </etc/passwd` | means search for a line that starts with "root" |                                            |
|    `$`     | `grep "shore$" file.txt`    | search for a line that ends with "shore"        | useful pag nag hahanap ng specific ending. |
|    `.`     | `grep "b.t" file.txt`       | search any char between sa `b` and `t`          | usefull pag may uknown character sa gitna  |
|    `[]`    | `grep "s[ae]lls file.txt`   | searches for `a or e` between sa `s and ll`     | usefull pag may ilang possible characters. |
|   `[^]`    | `grep "sh[^e]ll`            | excludes the `e`                                | usefull for exclusion some certain         |
|  `[a-c]`   | `grep "d[a-c]g file.txt`    | paghahanap ng range `a-c`                       |                                            |
|            |                             |                                                 |                                            |


---

##### GPG Key
- isang **digital key** use para mag-sign in at mag verify ng files of packages.
- Parang **digital signature** sa documents, pero sa software.
- It has private and public key
	- **Private key** - use by dev to sign in
	- **Public key** - use by regular user to verify that the package is legit.

 **Difference ng app sa repo vs apps na kailangan ng GPG keys**

**Apps already in Repo**
- These are apps na already included in distros official repo.
- Automatic trust, they are presigned ng distro

**App that needs GPG key**
- Not part of the default distro
- Comes from third pary-repo

##### **RYSNC**
*Synchornization tool, not just copying*

>[!Note] Usage
>```Bash
>rsync [options] sources destination
>
>#example
>rsync -av file.txt user@server:/source/  /destination/
>```

>[!Question] **QUESTIONS**
>**What does it do?**
>- Compare sources vs destination
>- Transfer only differences
>	- meaning first sync is full transfer, the next time sync is only the changes.
>- Can resume interrupted transfers
>- Can preserve permissions, timestamps, ownership, etc.
>
>**Can it detects changes in slight modification in file and syncs it?**
>*Yes, it checks metadata, like modification time. It skips the size same, mtime same*
>
>**Can it be use for compressed files or dir?**
>*It does not sync whats inside the compressed file. It treats the compressed file as a single file. Technically, the answer is **yesnt** *

**The difference of sync to Copy**

| Tool  | Behavior           |
| :---- | ------------------ |
| `cp`  | Blindly copies     |
| scp   | secure remote copy |
| rsync | intelligent sync   |

**Commands**

| Commands                             | Desc                                                                   |                                                                                                                                                                                                                               |
| :----------------------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rsync -av sources/ dest/`           | `a` - archive mode <br>`v` - verbose mode, it displays whats happening | commonly use                                                                                                                                                                                                                  |
| `-c`                                 | -checksum                                                              |                                                                                                                                                                                                                               |
| `--delete`                           | delets files in destination that doesnt exist in source                |                                                                                                                                                                                                                               |
|                                      |                                                                        |                                                                                                                                                                                                                               |
| ***RSYNC OVER SSH***                 | ***RSYNC OVER SSH***                                                   | ***RSYNC OVER SSH***                                                                                                                                                                                                          |
| `rsync -av source/ user@ip:/path/`   | copy `source/` to remote machine via ssh                               | The source is client                                                                                                                                                                                                          |
| `rsync -av user@ip:/path/ localDest` | the source destination is from server. Copy to client machine          | The source is server                                                                                                                                                                                                          |
|                                      |                                                                        |                                                                                                                                                                                                                               |
| ***COMMON FLAGS***                   | ***COMMON FLAGS***                                                     | ***COMMON FLAGS***                                                                                                                                                                                                            |
| `-a`                                 | archive mode.                                                          | the baseline. Its like a bundle of flags<br><br>`-r` recursive<br>`-l` preserve symlinks<br>`-p` preserve permissions<br>`-t` preserve timestamp<br>`-g` preserve group<br>`-o` preserver owner<br>`-D` preserve device files |
| `-v`                                 | verbose. Shows whats happenig                                          | useful for bebugging<br>useless in automation                                                                                                                                                                                 |
| `--progress`                         | shows per-file transfer progress                                       | useful for large file<br>slows output slightly                                                                                                                                                                                |
| `-z`                                 | compression. Compress data during transfer.                            | FOR NETWORK/SSH ONLY<br>useful for slow internet<br>useless for local copy.<br><br>TRADEOFF??<br>save bandwith<br>uses CPU                                                                                                    |
| `--delete`                           | makes destination mirror the source exactly                            | if file is removed in source. It also removed in dest.                                                                                                                                                                        |
| `-c`                                 | checksum mode. Compares actual file content(not just metadata)         | very accurate, but super slow.<br>use only when timestamp is unreliable and high integrity is must                                                                                                                            |
|                                      |                                                                        |                                                                                                                                                                                                                               |



##### **The `xargs`** 
Short term for `arguments`. 
- An additional piece of information to commands to modify a behavior. 
- Common use in pipe for beginners.
- Basically *"Take input from stdin and turn it into an argument for the next command*

Many uses the `exec` instead.....SEARCH THIS

>[!Example] Example
>```Bash
>find /var/log -name "*.log" | xargs grep -i "error"
>
>mental model: 
>find -> outputs filenames as text -> xargs converts to arguments -> greps open each file
>```

>[!Note] about not using xargs with grep
>without `xargs` the output of a `find` will be just a text. the grep will reads those texts as stdin. 
>
>```Bash
>find /var/log -name "*.log" | xargs grep -i "error"
>
>find -> outputs filenames as text -> grep reads those as stdin
>```

---

##### **What is Base64?**
Think of it like a ***translation system***.

>Computers sometimes need to send binary data (images, files, encrypted stuff) through systems that **only understand text** — like email or the web.
>Base64 converts that binary into a **safe text format** using only these characters:

```
A-Z, a-z, 0-9, +, /
```

>[!Example] Simple analogy
>
> *Imagine you need to ship a liquid overseas, but the courier only accepts **solid packages.** So you freeze it first, ship it, then the receiver melts it back.*
>
>- **Freeze** = encode to Base64
>- **Melt** = decode from Base64
>
>- The liquid is still the same — just temporarily in a different form.

**What it looks like:**
```
original:  Hello World
base64:    SGVsbG8gV29ybGQ=
```
The `=` at the end is a **padding character** — a telltale sign something is Base64 encoded.

**TO DECODE**
```Bash
cat data.txt | base64 -d
```
`-d`  means decode — translate it back to readable text.

**TO ENCODE**
```Bash
echo "password123" | base64
#output : 
cGFzc3dvcmQxMjM=

#FOR IMAGE OR OTHER FILES
base64 [targetFile] > [encode_location.txt]
```

|  Flags   | Desc                                         |                                     |
| :------: | -------------------------------------------- | ----------------------------------- |
|          |                                              |                                     |
| ENCODING | ENCODING                                     | ENCODING                            |
|  `-W 0`  | No line wrapping (default wraps at 76 chars) | use when it needs to be continuous  |
|  `-w N`  | Wraps output at N characters                 | `base64 -w 120 file.txt`            |
|          |                                              |                                     |
| DECODING | DECODING                                     | DECODING                            |
|   `-d`   | decopde mode                                 |                                     |
|   `-i`   | ignore non-alphabets characters              | useful for dirty input              |


*It is related to Layer 7 of OSI Model. Read more — [[OSI_Model#Layer 7-6 Spotlight — Encoding & Base64]]*


>[!Note]- A Base64 increases the size of data by roughly 33%
>Base64 works by taking 3 bytes (24bits) of binary data and representing them as 4 base64 characters
> 
> **How**
> *Example:*
> ```
> #24 bits divide into 3 bytes
> 10101010 | 10101010 | 10101010 
> ```
>
>**Step2:** Split it into 6 groups
>*A Base64 doesnt think in bytes. It thinks in **6-bit chunks***
>```
>101010 | 101010 | 101010 | 101010 
>   42         42      42       42
>```
> 
> **Step3**: Convert each 6 bit value to a Base64 character
> *Each 6-bit group can represent a number from:*
> ```
> 000000 = 0   —  111111 = 63
> #Thats a 64 possible values
> ```
> Hence the name: *Base64*
> 
> Each value maps to a character from the Base64 alphabet:
> ```
>A-Z  -> 0-25
>a-z  -> 26-51
>0-9  -> 52-61
>+    -> 62
>/    -> 63
> ```
> 
> So each group of 6bits becomes
> ```
> q q q q
> ```
> 


---

#### **What is hexdump?**
*May 05, 2026*
*Overview: showing bytes of a file but in [[#**What is hexadecimal?**|hexadecimal]] format instead of long 1's and zero's*

**Why use hexdump**
*Because many files are not a text like : images, zip files, executables, compressed files.*

This is useful in: 
- Debugging
- Reverse engineering
- Malware analysis
- Digital Forensics
- File Recovery

ASCII range:
```
41-5A = A-Z
61-7A = a-z
30-39 = 0-9

#Example: 
48 54 54 50  -> HTTP 
```

> *For reference how pros use hex dump read here : [[RANDOM NOTES FOR NOW#*How professionals use hexdump*]]*

---

>**COMMANDS**

```Bash
xxd [options] [inputfile]  # for printing in the terminal
xxd [options] [input_file] [output_file]  # for saving the dump in a file.
```

|  Commands or Flags   | Desc                                                                                                               |                                                                                                           |
| :------------------: | ------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- |
| `xxd targetfile.txt` | Print a hex dump of any file — shows offset, hex ytes, and ASCII side by side                                      |                                                                                                           |
|      `-l <len>`      | Show the first `len` bytes of a file.                                                                              | Useful to identify the file type (magic number)                                                           |
|     `-s <0x100>`     | start the dump from specific offset (at 0x100 at this case)<br>- 0x10, 0x20, 0x30.......(hexadecimal displacement) | Mostly for spot debugging, to skip there instead of scrolling                                             |
|     `-c <8>`<br>     | Display 8 bytes per row. <br>can be 4, 8, 16,                                                                      | 16 bytes per row is conventional. But changing it sometime makes the pattern easier to see                |
|        `g 1`         | Change the grouping to 1<br>the default wihtout the flag is 2                                                      | use when inspecting byte by byte like reading a file header or network packets                            |
|         `-p`         | plain text only — no spacing, no ASCII column                                                                      | use for scripting and can be pipe to another tools or compare easily                                      |
|         `-b`         | display to binary/bits                                                                                             | rarely needed, but useful for inspecting perimssions, protocol header where individual bits have meaning. |
|         `-u`         | Uppercase.                                                                                                         | Purely preference. No functional difference                                                               |
|         `-d`         | show offset in decimal instead of hex                                                                              | by default the hexadecimal placement on left is hex. With this. it display it into decimal                |
|                      |                                                                                                                    |                                                                                                           |
|     FOR DECODING     | FOR DECODING                                                                                                       | FOR DECODING                                                                                              |
|         `-r`         | Reverse (hex -  binary)                                                                                            |                                                                                                           |
|       `-r -p`        | paired flags,                                                                                                      | when decoding a hex dump that used the flag `-p` .                                                        |

>**Patterns**
You're almost always combining just **two or three** of these:
> "Show me **this much** (`-l`), starting **here** (`-s`), formatted **this way** (`-g 1` or `-p`)"
That covers 90% of real-world use.



>[!Question]- Questions in my mind
>*What if you use hexdump and pipe it to [[#**What is Base64?**|Base64]] recursively?*
>Example:
>```bash
>xxd file.txt | base64 | xxd | base64 ........
>```
>I find it possible but, *What is this use for?*
>
>Answer: 
>*There is no-real world use for it other than **obfuscation (not security)***. 
>**Obfuscation** — mostly in CTF challenge do this to make things confusing as a puzzle. 



---

#### **What is hexadecimal?**
*May 05, 2026*

Reference: https://learn.sparkfun.com/tutorials/hexadecimal/all

- *Also known as **hex** or **base16*** — uses set of **16 unique digits**. 
- Uses the standard 0-9 but also incorporate letters A-F.
```
 	              : 0  1  2  3  4  5  6  7  8   9  A   B   C   D   E   F	
	 	                                           10  11  12  13  14  15
```

**Hexadecimal is an interpretation of bytes from binary**
Instead of humans reading each byte(8bit) of data  individually — a hex represent them as two-digit number.
```
11000000 = 192
11000000 = C0 = 192
```
it can be done mentally by dividing an 8bits into 2nibble(4bits) and each nibble into hex digit
```Bash
11000000

1100 = 12 = C
0000      = 0

#And to convert it back to decimal. Read the two nibble as an 1byte(8bit)
1100+0000 = 11000000 = 192
```

***Why not just use binary ?***
- Too long
- Too hard to visually group
- Impossible to scan quickly
Example: 
```
11000000 | 10101000 00000001 00000001

vs

C0 A8 01 01
```

---

#### **What is the binary (base2)?**
*May 05, 2026*
**Definition:** a numbering system that uses only two digits: 0's and 1's. 

**Key idea:** All data in a computer is ultimately stored as binary, but at the physical level, it is not “numbers” — it is electrical states (voltage ON/OFF).  
- Binary is a human interpretation of those physical states.
- This is important to understand first in order to understand what is the use of [[#**What is hexadecimal?**]]


---


