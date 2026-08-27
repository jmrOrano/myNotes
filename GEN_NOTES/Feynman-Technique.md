

## Ano ang difference ng Terminal vs Shell? 
*Feb 21, 2026*

>Isipin mo na ang **Terminal** ay isang application na similar sa mga GUI apps. Pero, sa halip na Graphical User Interface, ang Terminal ay Text Base Interface.
>Halimbawa. : Sa pag hanap ng IP Address
```
GUI      -> Setting -> Wifi Setting -> WifiNameInformation
Terminal -> ipconfig or ifconfig
```
	
>Ang SHELL naman ang command interpreter. Ito ang gumagawa ng mga iutos mo bilang user.

Ito ang middle man para makausap mo ang core OS/kernel.

>[!Important]
>**TERMINAL: is a tool**
>`Ex: GNOME Terminal, Konsole`
>
>**SHELL :  the middle to OS/Kernel**
>`Ex: Bash, Zsh`
			
>[!Note] 
**Note: Ang Terminal command as we know tulad ng GNOME Terminal, Console ay mga ***Terminal Emulator lang din*** na nag eemulate ng **[[Feynman-Technique#TTY: Teletype Writer|Teletype Writer]]

#### **Terminal**
---
Just a GUI based program that provides a text-based interface.
Examples:
```
- GNOME Terminal
- Konsole
- Terminator
```

---
#### **Shell**
---
A command interpreter running inside the Terminal
Examples:
```
-Bash
-Zhs
-Fish
```

Main job is to:
- Read the string input from terminal done by user
- Parse the command (breakdown to sequence of data)
- Execute 


>[!Note] GUI Apps do not normally use a shell to launch.
>
>**in GUI:**
>```
>Click an App GUI
>--> Desktop Env launches the app binary Directly
>```
>
>The shell does not run the Terminal when clicking it. 
>```
>Operating System
 >       |
 >       v
>Terminal emulator
>       |
>        v
>Shell (bash)
>        |
>        v
>Commands/programs
>```
>
>**In Terminal:**
>```
>Type <appname>
>--->The shell running in terminal reads it--->Run the applicaton
>```
>



___
## How do commands run?
---
*July 11, 2026*
A command is just a binary (executable command) that the shell launches.
- Not all commands are designed or behave the same.
Some programs :
- Run once, perform a task then exit.
- Accept Sub commands  `Good for running Scripts`
- Stays running and will have its own command prompt `Interactive Mode`
	- All `Interactive Mode` binaries have their own command parser loop. 

#### Interactive Mode Commands
---
The `sqlite3`, `parted` ,. and many more are just programs that gets executed by shell(bash).
But in their interactive mode(inside), they have their own command parser(like interpreter)  specific to their own.


They do not passed the command to the General Shell interpreter(Bash)
```c
Terminal
   |
   v
bash
   |
   v
sqlite3 process starts
   |
   v
sqlite3's own parser handles input


What happens:===============================
1. Bash sees `sqlite3` as a command.
2. Bash starts the `sqlite3` program.
3. Bash is now mostly waiting.
4. `sqlite3` reads your input and parses SQL itself.
```


## **TTY Teletype Writer**
---
*Feb 22, 2026*
----Isang Terminal base interface nag nagbibigay ng way para magamit mo ang comptuter ng hindi na dumadaan sa GUI ng Desktop

**Pano Gamitin?**
**`Ctrl + Alt F1....F6`**
`login: username
`password: <!-- ******* --> 
**`$ls`**
<!-- Display the list of contents -->

**Kailan ginagamit?**
- It manage servers without graphics
- Rescue system when GUI fails, since this one runs on system level
- Lightweight and fast (*Good for Admin, system recovery, scripting, and low resource env*)


----


## **WHAT DOES GIT COLLABORATION WITHOUT GITHUB BACK THEN?**
---
*March 07, 2026*
**Patches via email**
1. Ang mga contributor ay gumagawa ng changes sa kanilang local repo.
2. Gumagawea ng *patch file* gamit : 
		`git format-patch`
3. I-email ang patch sa *Linux kernel mailing list*
4. Ang maintainer (like si Linus) ang mag aapply using:
		`git am patchfile.patch`
5. Lahat ng collaboration ay reviewed manually sa email.

**Shared repository servers**
- May *central git repo* hosted sa server
- Ang maintainers at trusted contributor may push/pull directly sa repo via SSH
```bash
git clone user@server:/path/to/repo.git
git push
git pull
```


---

## **The ZRAM technology**
---
*March 14, 2026*
--Isang kernel feature sa linux
-- Can be installed like `apt install zram-tools`

**it compresses the inactive process from RAM and store it again sa RAM.**
* gumagawa ito ng **zram devices** *check it via [[Linux_File_System__Major_Only_#The *`lsblk`* command|lsblk]]*

**Typical flow**
1.  Program uses RAM
2. RAM starts filling up
3. Kernel moves the inactive memory
4. Compress it to zram
5. Store it inside compress RAM
**This is much fater than using disk swap technology and here is why**
*Cpu compression speed >> than Disk speed*
***Example***
- RAM access  ~ nanosec
- CPU compress ~ microsec
- SSD access ~ milisec 
- HDD access ~ even slower
*So kahit may compression overhead from CPU, mas mabilis parin kaysa disk swap*
-- Sa normal swap :  its *`RAM -> Disk`*
-- Sa zram swap     : *`RAM -> Compressed RAM`*
-- sa hybrid              : *`RAM -> ZRAM swap -> disk swap(last resort)*`

>[!Question] Why zram has no entry at `/etc/fstab` for persistence?
>If you read about the [[Linux_File_System__Major_Only_#Swap]]. It has an entry as the `etc` for persistence.
>
>The reason zram doesnt have one is becuase, zram is not mounted like a disk. 
>It is:
>- created by boot by systemd
>- managed by kernel modules or services
>- configured via system settings (not fstab)
> 

---
## Using sudo vs logging in as root
---
*March 14, 2026*
--both gives `admin privilages` pero magkaiba ang principles.

**Logging in as root** 
-- Lahat ng command ay automatically full permission agad.
-- one wrong command can cause system failure.
-- `walang logging kung sino ang gumawa`
-- ang prompt ay gumagamit ng `#` na  ending instead na `$` 
--*`sudo -i`* - start as root
-- *`sudo su`* - root but keeps the current directory
-- *`sudo -s`*  same

**Using `sudo` command**
-- Normal user but `temporary only` 

**Test if your user acc can use sudo**
`sudo test` if no message appears, then it can

>[!Note] 
>Scenario: *Opening a dir owned by root with no other permissions.*
>```Bash
>linuxuser@linuxuser:/var/lib$ ls -alh | grep -i docker 
>#Output
>drwx--x--- 12 root root 4.0K May 2 10:55 docker
>```
>
>Opening via `sudo cd ` is not applicable because `cd` is a `builtin` and `sudo` runs external commands only
>
>**Option1** : Open a root shell
>```Bash
>sudo -i
>```
>
>**Option 2: One shot command**
>```Bash
>sudo ls <path>
>#not really entering inside but rather just reading whats inside
>```
>
>**Option 3: Subshell**
>```Bash
>sudo bash
>```

---

## Linux Boot Process
---
*August 23, 2026*
Reference: https://youtu.be/XpFsMB6FoOs?si=p8kB_h3q8UtB-OuB
The linux boot process follows five main stages from power-on to login screen:

The process starts when a user press the power button of a computer, then followed by:
- [[#BIOS/UEFI]]
- [[#GRUB Boot Loader]]
- [[#Kernel & Initramfs]]
- [[#Init System (Systemd)]]
- [[#User Login]]

### BIOS/UEFI
---
A `BIOS(old)`and `UEFI(modern)` are low-level software on computers motherboard.

What it does in boot process are:
- `POST` — Post-On-Self-Test: The firmware tests core parts like the CPU, memory (RAM), and keyboard to make sure they work
- `Hardware Initialization`:  It wakes up devices like storage drives, graphics cards, and USB ports
- It finds and loads up the Bootloader

---
### GRUB Boot Loader
---
A `bootloader` is a separate software program stored in hard drive that the firmware launches next to load the actual operating system.

- For `BIOS`, the bootloader lives in the first little chunk of hard drive called (`Masterboot Record`).
- For `UEFI`, the bootloader is stored in a separate partition.

>The key jobs are:
- `Locate` the operating system kernel on the disk
- `Load` the kernel into the computers memory(`RAM`)
- `Starts` running the kernel code

---
### Kernel & Initramfs
---
*Read about kernel*: [[Whatis#**What is a kernel?**]]

Once the kernel is loaded into memory. The [[Whatis#**What is a kernel?**|kernel]] takes over the computers resources, and starts initiating the background processes and services.

What happens in this step are:
- `Decompress` — The kernel decompresses itself into memory
- `Checks` — The hardware
- `Loads` — Device drivers and other kernel modules

>The kernel by itself cannot access the real disk yet (it may be missing drivers, or the disk might be encrypted). So before the *real* root filesystem can be mounted:

- `Mount initramfs` — a temporary, minimal root filesystem stored in RAM. This is where the kernel gets the missing drivers/tools it needs (e.g. disk controller driver, decryption tools)

- `switch_root` — once the real disk is accessible, the kernel switches over to the REAL root filesystem (leaving the temporary initramfs behind)

---
### Init System (Systemd)
---
Once mounted the root filesystem. The kernel runs the first process— PID 1. 
The Process ID 1 is the — [[Whatis#systemd|Systemd]] and is the parent of all processes.

The responsibilities of Systemd are:
- `Checks` — for remaining hardware that needs drivers to be loaded.
- `Mounts up` — all different filesystem and disk so it can be accessible
- `Launch` — all the background services needed (networking, cron daemon, display managers etc.)


---
### User Login
---
Once all the services started. The user gets to the graphical interface and the desktop environment loads up.