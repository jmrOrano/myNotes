
#### Some useful command RANDOM

|                                               Commands                                               | Desc                                                                                                                                                                                                                  |                                                                     |
| :--------------------------------------------------------------------------------------------------: | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
|                                         the **`inxi`** tool                                          | *to show complete info ng system in readable format*                                                                                                                                                                  |                                                                     |
|                                            **`inxi -F`**                                             | Display Full system info                                                                                                                                                                                              |                                                                     |
|                                 **`inxi -Fxz`**<br>**`inxi -Fxxxz`**                                 | Full + extra details<br>`-F` - *full output*<br>`-x` - *extra details*<br>`-z` - *hide sensitive info (IP, MAC Address*                                                                                               |                                                                     |
|                                            **`inxi -G`**                                             | Display:<br>`-GPU Model`<br>`-Driver in use`<br>`-Res`<br>`-Refresh Rate`                                                                                                                                             |                                                                     |
|                                                `lshw`                                                | list hardware                                                                                                                                                                                                         |                                                                     |
|                                     `dpkg -l \| grep <appname>`                                      | check if app is installed                                                                                                                                                                                             |                                                                     |
|                             or  `apt list --installed \| grep <appname>`                             |                                                                                                                                                                                                                       |                                                                     |
|                                           **APT COMMANDS**                                           | **APT COMMANDS**                                                                                                                                                                                                      |                                                                     |
| `apt search <appname>`<br>`apt search ^appname$`<br>`apt-cache search appname`<br>`apt show appname` | - to search if an app is available in repo<br>-search for exact package name using [[Whatis#Regex-Regular Expression\|Regex]]<br>-faster but idk how it works compare to others.<br>-display info if the app is found | OUTPUT LABEL<br>`v` - virtual<br>`i` - installed<br>`p` - available |
|                                          `which <appname>`                                           | check if the executable is in path. Mostly useful for checking path configuration.                                                                                                                                    |                                                                     |
|                                          CLEANING UP SPACES                                          | CLEAN UP SPACES                                                                                                                                                                                                       |                                                                     |
|                                        `sudo apt autoremove`                                         | cleanups unused dependencie                                                                                                                                                                                           |                                                                     |
|                                     `sudo apt purge <pkg_name>`                                      | removes pkg and its config files in the `/etc/`                                                                                                                                                                       | aggressive way, but daijoubu                                        |
|                                   `sudo apt remove <package-name>`                                   | removes binaries, but keeps the config files                                                                                                                                                                          |                                                                     |
|                                           `sudo apt clean`                                           | clean apt cache                                                                                                                                                                                                       |                                                                     |
|                                        `du -h --max-depth=1`                                         | find large/unecessary files.                                                                                                                                                                                          |                                                                     |
|                                          `rm -rf ~/.cache`                                           | cleans the cache at home dir for user                                                                                                                                                                                 | reminder :some apps will be slower as they will re-build cache      |

|    **NETWORKING COMMANDS**     |                                                                                                                                    |
| :----------------------------: | ---------------------------------------------------------------------------------------------------------------------------------- |
|              `ss`              | Socket statistics - a faster and more detailed replacement for netstat. Shows active connection and listening ports                |
|            `ss -ol`            | Display `established` and `listening`                                                                                              |
|        `mtr google.com`        | combination of `ping` and `traceroute` that provides continuous, real time and report of network response and connectivity to host |
|        `ip route show`         | Display the kernel IP routing table.                                                                                               |
|        `dig google.com`        | query DNS name servers and retrieving detaled information about DNS record                                                         |
|         `lshw -C net`          | list hardware                                                                                                                      |
|            ifconfig            | display ip, mac addr info etc                                                                                                      |
|  `iwconfig yourinterfacename`  | display info about sa networ interface.                                                                                            |
| `iw dev [interfacename] link`  | (for wireless) updated version ng `iwconfig`                                                                                       |
|             `ip a`             | display the `Ip addr, Mac address , Interface state, interface name`                                                               |
|   `ethtool [interfacename]`    | (for ehternet port only) check the status ng interface                                                                             |
|          **FOR ARP**           | **FOR ARP**                                                                                                                        |
| `ip neigh`f<br>`ip neigh show` | neighbor command -  the standard for managing and viewing the ARP table                                                            |
|      `cat /proc/net/arp`       | everything is a file in linux, this is for raw Kernel ARP table                                                                    |

	
use H4 for titles 
#### The *`lsblk`*  command
Sample output: 
`*sdc      8:32   1   3.8G  0 disk `*
*`└─sdc1   8:33   1   3.7G  0 part /media/linuxuser/F4FD-93EE`*

|                             | Desc                           |
| :-------------------------: | ------------------------------ |
|   sdc 8:32 1 3.8G 0 disk    | - Ito yung mismong USB Drive   |
| \|-sdc1 8:833 1 3.7G 0 part | - ito yung partition           |
|      /media/linuxuser/      | -Dito naka mount ang partition |
|          F4FD-93EE          | -Ito yung name ng USB          |

**PANO SAFELY EJECT AND USB DRIVE? **

*unmount muna ang partition*
**`sudo umount /dev/sdc1`**

*then power off the device*
**`sudo udisksctl power-off -b /dev/sdc`**

|                                            Flags / Options                                             | Desc                                         |
| :----------------------------------------------------------------------------------------------------: | -------------------------------------------- |
|                                                   -b                                                   | -- block-device : ang ibibigay mong argument |
| sa linux, lahat ng hardware devices ay ***nirerepresent bilang files*** sa loob ng directory na : /dev |                                              |
|                                                                                                        |                                              |


----




---

#### About the *`cp and mv`* command

>[!Reminder]
>
>- **`mv`** and **`cp`** works both for files and directory.
>  - if same path, it just `renames` or `updates`. No dups or extra space consumed.

>[!Example]
>
>**You're in** `/home/username/Desktop`
>
>`mv bigFolder [space] /home/username/Desktop`
		
- **if the path is diff file system. It behaves recursively .**
- **in using *`cp`* to copy dir to another. using the `-r --recursively` is a must**
>[!Example]
>**You're in** `/home/username/Desktop`
>
`cp -r movethisDir/ [space] tothisDir/`
>
or you can set the destination/target first 
`cp -r -t tothisDir/ [space] movethisDir/`


Running the `--help` is good, but can be overwhelming. So, i'll list some commonly use flags

- `-v --verbose` : ipapakita sa terminal kung ano ginagawa ng mv
- `-i --interactive` : for yes or no option kung may something come up
- `-n -- no-clobber` : cancel ang mv kung may exisiting folder sa dest. 
- `-t --setTheDESTDir first` : 

---


#### Tips in *`cut`* command
The **`cut`** command outputs in new line if the text has multiple lines.

*What if you want to put each delimiter in new line. But the text is only a single-line?*

--Example : in normal method
**`echo $ PATH | -d":" -f1`**
will only output a single entry dahil ang **`echo $PATH`** is a single line lang.

--**Answer** : use ***`tr`*** command
**`echo $PATH | tr ':' '\n'

--**Explanation**
with `translate tr` command. It basically just replace the colon ***`:`*** with the new line ***`\n`***
TR USAGE : **`tr [space] ["replaceThis"] [space] ["withThis"]

---

#### The `tr` command
**Basic structure :** 
```Bash
tr 'find this' [space] 'replace with this'
```
It replaces character by character — not word by word

**Using `the range tax`**:
Instead of typing of every letter manually, you can use ranges:
```bash

tr 'a-z' 'A-Z'    # Converts lower to UPPER
tr 'A-Z' 'a-z'    # Converts UPPER to lower
tr '0-9' 'a-j'    # replaces each number with letter

tr 'a-Za-z'       # Combining two ranges in one parameter
```

**Useful flags**
```Bash
tr -d ':'    # DELETE the character instead of replacing
tr -s ' '    # SQUEEZ multiple spaces into one
```

**Example problem at bandit overthewire dot org.**  
*Level 11 -> 12*
*The password for the next level is stored in the file **data.txt**, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions*

```Bash
#-------THIS IS WRONG ---------
bandit11@bandit:~$ cat data.txt | tr 'A-Za-z' 'N-Zn-zA-Ma-m'
The CaFFJBEd iF 7K16WNeuvi5YkvhWFfsvDBBgAUTLj9Q4

#------THIS IS THE CORRECT------
bandit11@bandit:~$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4

```
Rot13: It's just the alphabet **shifted by 13 positions.**

**The rule i impose ?** Pattern recognition.
```
First parameter:   UPPER range first, LOWER range second
Second parameter:  UPPER range first, LOWER range second
```
---
### RELATED TO LEVEL 12 IN bandit overthewire
*The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!)*


---



|                      Flags                       | desc                                                                                                                                      |
| :----------------------------------------------: | ----------------------------------------------------------------------------------------------------------------------------------------- |
|                     `ps aux`                     | --a -shows the process from all users<br>--u -user-oriented format (may cpu, MEM,etc)<br>--x -include processes without terminal(daemons) |
| `ps axjf`<br>--*this shows in hierrarchy format* | --x include processes that do not have controlling terminal(TTY)<br>--f -full format listing                                              |
|      `ps aux --sort=-%cpu`<br>`--sort=pid`       |                                                                                                                                           |
|                    `-p 1234`                     | - check specific id                                                                                                                       |
|                      `-eL`                       | - shows threads                                                                                                                           |
|                  pstree -p 1234                  |                                                                                                                                           |
|               `pstree -linuxuser`                |                                                                                                                                           |


## LINUX

>[!Note]- User Management
>[[User Management]]
>- [[User Management#**USER ADD**|useradd]]
>- [[User Management#**ADDUSER**|adduser]]
>- [[User Management#**USER DEL**|userdel]]
>- [[User Management#**USERMOD**|usermod]]
>- [[User Management#**passwd**|passwd]]
>
>[[User Management#GROUPS|Groups]]
>- [[User Management#**groupadd**|groupadd]]
>- [[User Management#**groupdel**|groupdel]]
>- [[User Management#**gpasswd**|gpasswd]]
>- [[User Management#**usermod -aG**|usermod -aG]]
>
>[[User Management#PERMISSIONS|Permissions]]
>- [[User Management#chmod|chmod]]
>- [[User Management#**chown**|Chown]]
>
> [[User Management#SPECIAL PERMISSIONS|Special Permissions]]
> - [[User Management#**SUID**|suid]]
> - [[User Management#**SGID**|sgid]]
> - [[User Management#**STICKY BIT**|sticky bit]]

>[!Note]- Linux File System Exploration
>
>**For navigation**
> [[Linux_File_System__Major_Only_#ls|ls command]]
> [[Linux_File_System__Major_Only_#tree|tree command]]
>
>**For reading files**
> [[Linux_File_System__Major_Only_#cat|cat command]]
> [[Linux_File_System__Major_Only_#less|less command]]
> [[Linux_File_System__Major_Only_#head|head command]]
> [[Linux_File_System__Major_Only_#tail|tail command]]
>
>**For Searching**
> [[Linux_File_System__Major_Only_#grep|grep command]]
> [[Linux_File_System__Major_Only_#find|find command]]
>
>**For System awareness**
> [[Linux_File_System__Major_Only_#df|df command]]
>[[Linux_File_System__Major_Only_#du|du command]]
>[[Linux_File_System__Major_Only_#ps|ps command]]
>[[Linux_File_System__Major_Only_#top|top command]]
>
>
>



[[DOCKER SETUP  2#**DOCKER COMMON COMMANDS**|Docker Common Commands]]

>[!Note]- Security
>[[Whatis#**What is hexdump?**|hexdump]]
>