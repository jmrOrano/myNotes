

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
Summary/Note : Ang Shell ay mag rurun parin kahit GUI ang gamit mo dahil nagawa parin ito ng mga commands. 

>[!Important]
>**TERMINAL: is a tool**
>`Ex: GNOME Terminal, Konsole`
>
>**SHELL :  the middle to OS/Kernel**
>`Ex: Bash, Zsh`
			
>[!Note] 
**Note: Ang Terminal command as we know tulad ng GNOME Terminal, Console ay mga ***Terminal Emulator lang din*** na nag eemulate ng **[[Feynman-Technique#TTY: Teletype Writer|Teletype Writer]]

___

## **TTY Teletype Writer**
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


---


---
---



---

## **WHAT DOES GIT COLLABORATION WITHOUT GITHUB BACK THEN?**
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

