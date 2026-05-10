use h3 for date and h4 for topics

## FEB 2026

### |Feb 21
#### Ano ang difference ng Terminal vs Shell? 
	Isipin mo na ang Terminal ay isang application na similar sa mga GUI apps. Pero, sa halip na Graphical User Interface, ang Terminal ay Text Base Interface.
	
		Halimbawa. : Sa pag hanap ng IP Address
		GUI      -> Setting -> Wifi Setting -> WifiNameInformation
		Terminal -> ipconfig or ifconfig

	Ang SHELL naman ang command interpreter. Ito ang gumagawa ng mga iutos mo bilang user.
	Ito ang middle man para makausap mo ang core OS/kernel.
	Summary/Note : Ang Shell ay mag rurun parin kahit GUI ang gamit mo dahil nagawa parin ito ng mga commands. 
		TERMINAL: is a tool
			Ex: GNOME Terminal, Konsole
		SHELL :  the middle to OS/Kernel
			Ex: Bash, Zsh
**Note: Ang Terminal command as we know tulad ng GNOME Terminal, Konsole ay mga ***Terminal Emulator lang din*** na nag eemulate ng **[[Feynman-Technique#TTY: Teletype Writer|Teletype Writer]]

___

### |Feb 22
#### TTY Teletype Writer
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

### FEB 25
#### ANO ANG SYMBOLIC LINKS? 
`Shortcut(.Ink file)`
***Symbolic link*** ay isang shortcut file pero mas low level 

Madalas gamitin sa folders or files.
##### Core Idea
Kapag gumawa ka ng symbolic link:
- Hindi nito kinokopya ang file
- Hindi ito duplicate
- Isa lang itong “pointer” papunta sa original file
###### Example
Original File:
***`/home/userName/Documents/report.txt`***

Creating symbolic link:
***`ln -s /home/orano/Documents/report.txt ~/Desktop/report-link.txt`***

###### Visual Example
`ls -l` Outputs:
**`report-link.txt -> /home/orano/Documents/report.txt`**
Makikita sa arrow (*`->`*) kung nasan yung shortcut 

---
---


## MARCH 2026

### MARCH 07
#### WHAT DOES GIT COLLABORATION WITHOUT GITHUB BACK THEN?
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
	`git clone user@server:/path/to/repo.git`
	`git push`
	`git pull`

---

### March 08
#### Regex-Regular Expression
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

### March 14

#### The ZRAM technology
--Isang kernel feature sa linux
-- Can be installed like `apt install zram-tools`

**it compresses the inactive process from RAM and store it again sa RAM.**
* gumagawa ito ng **zram devices** *check it via `lblk`*

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

---
#### Using sudo vs logging in as root
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

