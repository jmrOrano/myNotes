	***use H5*** 
##### The .Bashcr file in home dir
*Ito ang user setting config for the terminal.*

Location : *`~/.bashrc or /home/username/.bashrc)

Dito nilalalgay ang [[Whatis#The `env` (environment variables)| env variables]] for users
For customizing prompt
And also mga commands na want i-automate.

---
##### The ~/.profile  /  ~/.xprofile
*Dito nakalagay ang mga script that ***runs at log in*** 
- Any commands na ilagay ko dito ay executed automatically when logged in

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

##### The User User Management Files
For commands : [command ]

1.  The `sudoers` file
	- located at /etc/sudoers
	- it lists of the users and groups who can use sudo command
	- must be edited with the `visudo` command as root for safer method.
2. The `passwd` file
	- located at /etc/passwd
	- contains the mapping between usernames and UIDs
		- `root:x:0:0:root:/root:/bin/bash` 
		- `root` - the user
		- `x`        - indicate encrypted pass in the /etc/shadow
		- `*`         - if its asterisk, acc is locked and cannot be used for login
		- `0`          - the `UID` the root is always zero
		- `0`          - the iditentifier of user in a primary group
	- editing is possible but heavily discourage, causing typo can cause system locking.
	- more reliable to use `useradd, usermod, userdel` command util to manage.
3. the `shadow` file
	 - located at `/etc/shadow`
	 - it stores encrypted user password 
		 - `root:!:20495:0:99999:7:::`
			- each user is seperated by colon
4. the `group` file
	- located at `/etc/group` 
	- for managing permissions for multiple users
		- `root:x:0:`
		- each group is seperated by colon

---
##### File permissions
-- in linux ***everything is a file*** . 
-- This is fundamental for security and administration.

**Understanding Permission String**
*Example* : `ls -l Desktop` 
*Output* : 
`total 4`
`drwxrwxr-x 4 linuxuser linuxuser 4096 Mar 12 21:04 practiceEnv`

1. Unang Character : **`d`**
	- `d` - directory folder
	- `-` - regular file
	- `l` - symbolic link
	- `c` - character device, etc.
	
2.  Sunod na 9 Characters : **`rwxrwxr-x`** 
	- *lets divide it into 3 groups* : **`rwx | rwx | r-x |`** 

		1. **`rwx`** - permission para sa **owner**( the creator ng file or dir)
		2. **`rwx`** - permission para sa **group** (yung group na related sa file)
		3. **`r-x`** - permission para sa **others** 

3. Ano meaning ng **`r , w`** and **`x`**  ?
	- **`r`** - read
	- **`w`** - write
	- **`x`** - execute
	- **`-`** - no permission granted


**Modifying File Permissions**
-- we use  [[Commands#Chmod command|Chmod Command]] for this `change mode`.
-- It offers **two main methods** 
	1. **Symbolic**   - more readable
	2. **Numerical Mode** - more powerful and faster, can change all permissions of groups, user and others using only three digit number.

---



##### Special Permissions

There are `3 special permissions` :" **`Setuid, Setgid, Stick`**
	- similar sa regular permissions, they also have value
	- `4- setuid`
	- `2 - setgid`
	- `1 - sticky`

1. **Setuid**
	--Aay special permission na kapag naka-set sa isang **executable file**,
	--ang program ay **tatakbo gamit ang privileges ng owner ng file**, hindi ng user na nag-run.
	
	*Example*:
		`ls -l /usr/bin/passwd` 
	output the : 
		`-rwsr-xr-x 1 root root 68248 Mar 12 /usr/bin/passwd`
	
	-- ang `s` means setuid + executable. 
	-- This means ang `passwd` command ay nag rurun as root 
	
	**Without `setuid`:**
	- Hindi ka makakapag-change ng password
	- Kasi wala kang permission sa `/etc/shadow`
	
	**With `setuid`:**
	- Program acts as **trusted bridge**
	- Controlled way para makagawa ka ng privileged action

2. **Setgid**
	-- --Aay special permission na kapag naka-set sa isang **directories** (shared folders)
	
3. **Sticky**
	--  mostly sa directories (like /temp)

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