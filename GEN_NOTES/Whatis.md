	***use H5*** 


---


---

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

#### GPG Key
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
- 