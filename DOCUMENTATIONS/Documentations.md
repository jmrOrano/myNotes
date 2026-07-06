*Overview: This note taking began on March 28, 2026 with the intention of documenting problems and fixes also potential recreating a small setups and workflow *


>[!Contents]-
>[[#Things to do after fresh install ng linux]]
>[[#Get Fullscreen in Vbox linux]]
>[[#Fixing sluggish and slow response trackpad (NOT RESOLVE)]]
>


# **HOW TO/HOW I**
## Things to do after fresh install ng linux
*03/28/2026*
*Linux mint cinnamon 22.3  Zena*
*Purpose : for easy future set up*

1. Update everything (non-negotiable)
	`sudo apt update && sudo apt upgrade -y`
	Optional: `sudo apt autoremove`
	
2. Enable Firewall    
	
3. Check Hardware 
	`run : inxi -Fxxx`
4. Setup swap/Zram  (Performance boost)
	check muna : `free -h`
	if low ram then : `sudo apt install zram-tools`
>[!Info] Configuring ZRAM size
>1. Check if its on: `swapon --show`
>2. Change config by: `sudo nano /etc/default/zramswap`
>3. set the size to: `SIZE=1024` 
>
 >[!Info] Future use. Configure Swappines
 >
 >*Overview: It controls how aggressively Linux moves data from RAM to the SWAP space*
 >*Value Range 0 to 100 : `0` (avoids as much as possible) -> `100` (swap vey aggresively)*
 >- At `60`, the kernel is fairly balanced: it will start swapping out less‑used memory pages even if RAM is still available.
 >- This helps keep more RAM free for disk cache, but can cause noticeable slowdowns if swap is on a slow disk.
 >  - At `10`, the kernel prefers to keep processes in RAM and will only swap when memory pressure is high.
 >- This is good if you have plenty of RAM and want apps to stay responsive.
 >  - Downsides: less RAM is available for caching files, so disk reads might be slower in some workloads.
 >
 >>[!Info] How to: CHANGE SWAPPINESS
 >>`echo "vm.swappiness-10" | sudo tee -a /etc/sysctl.conf` 
 >>Reload it by : `sudo systctl -p`
 >    
    
    
5. Disable unecessary startup app
    
6. Power Management(Laptop users)
	`sudo apt install tlp`
	`sudo systemctl enable tlp`
	for more info `sudo tlp-stat`
	
7. .Enable TRIM(for SSD only)
	check first : `systemctl status fstrim.timer`
	if disabled then : `sudo systemctl enable fstrim.timer`
	
8. Flatpak + Software Sources
	Mint already supports flatpak
	Enable flathub : `flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

9. Security
	Disable root login(already default in Mint)
	Avoid random `.deb` install

10. Optional 
		Terminal : switch to `zsh`
		Install plugins (auto suggestions, syntax highlights)

11. Install monitoring system 
	`sudo apt install htop`
	or 
	`sudo apt install btop`

12. DONTS
	- Mag tweak already before update
	- Walang backup
	- Ignore drivers (especially Nvidia)
	- Over-installing apps
	- No firewall
---

## Get Fullscreen in Vbox linux
*03/28/2026*
*Mainly for Ubuntu*
*Purpose : avoid repetition at googling*

1. Get dependencies
*run `sudo apt-get install build-essentials gcc perl dkms`*

2. Mount guest edition image
Click *Devices > insert Guest Additions CD Image*

3. Navigate to the mount directory
Run *`lsblk`*
	`cd /the-path-where/Vbox_GAs_7.0.16 is`
Run the file named `VboxLinuxAdditions.run`
	`sudo bash TheFileName`
	`reboot`
4. Done
Just click `view` then `Fullscreen Mode`

---


## CHANGES IN GRUB FILE
*03/29/2026*
Purpose : *To see the grub menu and doesnt need to press shift or esc button every time system boots. Mainly for changing kernel version*

- `cd /etc/default`
- `sudo nano grub`

The default values are:
`GRUB DEFAULT=0`
`GRUB_TIMEOUT_STYLE=hidden`
`GRUB_TIMEOUT=0`

My changes are:
`GRUB DEFAULT=0`
`GRUB_TIMEOUT_STYLE=menu`
`GRUB_TIMEOUT=5`

## Know battery health via terminal
*June11-2026*
##### **Via Upower**
Find battery's device path by running
```Bash
upower -e | grep battery
```
Copy the full path and run:
```Bash
grep -i / [pastehere]
```
Or you can use the [[Whatis#**The `xargs`**|xargs]] for seamlessnes:
```Bash
upower -e | grep -i | xargs upower -i
```

## Eject a flashdrive

>Use the [[Linux_File_System__Major_Only_#The *`lsblk`* command|lsblk]] command
```Bash
lsblk

#OUTPUT
sdc       8:32   1   3.8G  0 disk 
└─sdc1    8:33   1   3.7G  0 part /media/linuxuser/F4FD-93EE
```

|                               | Desc                           |
| :---------------------------: | ------------------------------ |
|   `sdc` 8:32 1 3.8G 0 disk`   | - Ito yung mismong USB Drive   |
| \|-`sdc1` 8:833 1 3.7G 0 part | - ito yung partition           |
|      `/media/linuxuser/`      | -Dito naka mount ang partition |
|          `F4FD-93EE`          | -Ito yung name ng USB          |

>**unmount muna ang partition**
```bash
sudo umount /dev/sdc1
```

>**then power off the device**
```Bash
sudo udisksctl power-off -b /dev/sdc
```


---

## Partition a second disk
*June13, 2026*
```
Machine: Virtual Machine | Linux Mint Zena Ubuntu base

Exercise Flow: Partition -> Format -> Mount -> Persist
```
>[!Note] Recommended read
>- Read about the Linux Filesystem first before proceeding: [[Linux_File_System__Major_Only_#The Anatomy of Disk|The anatomy of Disk]]
>- About the Disk naming as well: [[Linux_File_System__Major_Only_#Disk Naming|Disk Naming]]
>- And if curious about the commands use, it can be search here under the **`Linux File System Exploration`** : [[Commands#^4ccc34]]
>- For tools used : [[Tools#FOR DISK-RELATED TOOLs]]

### First: Identify disk
```Bash
lsblk
```
Look for something like :
```
sdb  -> extra disk   
sda  -> the main OS disk 
```

### Step1: Create a Partition Table

>Use the [[Tools#parted|parted]] command
```Bash
sudo parted /dev/sdb
```

>Inside the parted, use `mkpart` to create partition
```Bash
#LABEL THE PARATITION TABLE AS GPT
mklabel gpt  

# CREATE A PARTITION STARTING AT 1MB ends at 100%. USING PERCENT
mkpart primary ext4 1MiB 100% 

# OR YOU CAN USE THE EXACT SIZE
mkpart primary ext4 1MiB 1GiB

print #PRINT THE PARATITION
quit
```

### Step2: Format partition (make filesystem)

>Using the `mkfs` command
```Bash
#TURNING A RAW SPACE INTO USEABLE FILESYSTEM
sudo mkfs.ext4 /dev/sdb1
```

>The sequence is: [[Linux_File_System__Major_Only_#Block|block]] , [[Linux_File_System__Major_Only_#^becce8|superblock]], [[Linux_File_System__Major_Only_#Inodes|Inode table]]
```
mkfs.ext4 /dev/sdb1 
	│ 
	├── Divides entire partition into equal 4KB blocks 
	├── Builds the superblock (metadata about the filesystem) 
	├── Builds the inode table (empty — no files yet) 
		└── Marks ALL blocks as FREE ↓ Filesystem is now "empty" but fully structured
```

### Step3: Create a mount point & Mount It

>Mount point = is an empty folder at `/mnt/` where disk will be attach.
```Bash
sudo mkdir -p /mnt/disk2
```

>Mount it
```Bash
#CHECK FIRST the absolute path of the partition
lsblk -p   

#MOUNTING IT
sudo mount /dev/sdb1 /mnt/disk2

# CHECK IT AGAIN
df -h
lsblk -p
```

### Step4: Test Write

```Bash
echo "Hello Disk" | sudo tee /mnt/disk2/Hello.txt
```

### Step5 : Make it persist

>Get the partition [[Linux_File_System__Major_Only_#Why UUID is Preferred|UUID]] 
```Bash 
lsblk -f
# OR
sudo blkid /dev/sdb1
```

>Edit the config for static mount at [[Linux_File_System__Major_Only_#/etc|/etc]] for specific at [[Linux_File_System__Major_Only_#^27d1c1|/etc/]]
```Bash
sudo nano /etc/fstab

#ADD THE LINE
UUID=YOUR-UUID-GOES-HERE /mnt/disk2 ext4 defaults  0 2
```
- `UUID`               - the Device Identifier — use `lsblk -f` or `blkid` to see ID
- `/mnt/disk2`    - The mount point directory — the `disk2` is custom made dir.
- `ext4`               - The type of filesystem —  view it here : [[Linux_File_System__Major_Only_#Common File System Types|filesystem types]]
- `defaults`        - Mount options  — includes `relatime`, `errors=remount-ro`. Refer to `man mount`.
- `0`                     - Dump field — 1 means dump, 0 means dont.
- `2`                     - File system check — 0 means check, 1 means check first(reserved for root `/`), 2 means check after root.

# Making a swap partition
*June 16,2026*

## METHOD FOR USING THE ENTIRE DISK
---
### Step1: Make swap command
```Bash
sudo mkswap /dev/sdb
```
### Step2: Enable it via swapon command
```Bash
swapon mkswap /dev/sdb
```

### Step3: Persistence 
Add an entry at `/etc/fstab` 
```Nano
UUID=your-ID-goes-here none swap sw 0 0
```

## METHOD FOR USING PARTITION OF A DISK
---
### Step1: Make a partition for the swap in parted
```Bash
#THIS ASSUMES THAT THE FIRST 50% IS ALREADY OCCUPIED
mkpart primary linux-swap 50% 100% 
```

### Step2: Initialize swap & Enable It
Outside of parted, do:
```Bash
sudo mkswap /dev/sdb2

#Enable
swapon /dev/sdb2
```

### Step3: Persistence 
Add an entry at `/etc/fstab` 
```Nano
UUID=your-ID-goes-here none swap sw 0 0
```


>[!Note] Making swap partition doesnt involve mounting point.
>Because swap is **not a filesystem**.
>The kernel uses it directly. And treats it as pages of memory storage
>
>There are:
>- No files
>- No directories
>- No permissions
>- no pathname hierarchy

---
# PROBLEMS ENCOUNTERED

## Cant established a connection to server from different network across internet.
---
*Overview: I Setup an [[SSH_Setup|SSH Server]]  and a [[SETUP MINECRAFT SERVER IN DOCKER|Minecraft Server]]. Both are port forwarded.*
- Machine : Lenovo thinkpad L480,
- OS: Linux Mint Zena Ubuntu base
- Network architecture overview: 1ISP router, 1 third party router (this is where the machine is cable connected)
	- The 2nd router is not in different subnet. 
	- host isolation is disabled
	- Firewall is properly configured
	- Using IPV4 
	- Not lock behind in CGNAT

### The anomaly: 
---
- **When accessing the server from different network across the internet, its not reaching the destination**
```
Pinging 11x.2x.x.xxx with 32 bytes of data:
Reply from 11x.x.x.x: Destination host unreachable.
Reply from 11x.x.x.x: Destination host unreachable.
Reply from 11x.x.x.x: Destination host unreachable.
Reply from 11x.x.x.x: Destination host unreachable.
```

Performing tracert:
```
C:\Users\orano>tracert 112.2xx.xxx.xxx

Tracing route to 112.2xx.xxx.xxx.pldt.net [112.209.208.207]
over a maximum of 30 hops:

  1     1 ms     1 ms     1 ms  192.168.1.1
  2  11x.xxx.xxx.xxx.pldt.net [112.2xx.xxx.xxx]  reports: Destination host unreachable.
```

- **But, its reaching the destination when using a VPN or specifically the cloudflare warp app.**
Ping request with cloudflare enabled: 
```
C:\Users\orano>ping 11x.2xx.xxx.xxx

Pinging 11x.2xx.xxx.xxx with 32 bytes of data:
Reply from 11x.xxx.xxx.xxx: bytes=32 time=72ms TTL=55
Reply from 11x.xxx.xxx.xxx: bytes=32 time=72ms TTL=55
Reply from 11x.xxx.xxx.xxx: bytes=32 time=67ms TTL=55
Reply from 1xx.xxx.xxx.xxx: bytes=32 time=69ms TTL=55

Ping statistics for 1xx.xxx.xxx.xxx:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 67ms, Maximum = 72ms, Average = 70ms

C:\Users\orano>
```

- After weeks of trying to figure out. I found out that other clients (friends) are able to established a connection to the server.
- The anomaly is different network to network. Some able to connect other network cant. 


### THE CONCLUSION (most likely)
---
**Most likely the internal routing issue with my ISP**
- With the use of cloudflare warp, the request gets redirected to the routing of the cloudflare. 
- After eliminating other potential problem, it narrows it down to ISP backbone routing.
- With inconsistent results from testing to different network, others can connect other cannot. The routing broken to other networks.

---

## WARPINATOR CANT CONNECT
*06/06/2026*
*Overview*: the warpinator app in linux is not connecting to other devices. 

*Linux Mint*
*In VM oracle virtual box*
*Connected to same subnet.*
*The machines can see each other but cant established a connection*

Trying manual connection didnt fix it.

After a bit of searching. It turns out the `ufw` from linux is preventing the connection. 

Disabling it ables to connect.
```
sudo ufw disable
```

Now for perma fix, adding the port that the warpinator uses in ufw. 
```
sudo ufw allow 42000/tcp
sudo ufw allow 42000/udp
sudo ufw reload
```

---

## Fixing sluggish and slow response trackpad (NOT RESOLVE)
*03/28/2026*
*Machine : Thinkpad L490 ***
*Kernel : 6.8.0-106 -generic*
*Distro : Mint 22.3 Zena Ubuntu base 24.04*

Didnt work : Disabling touchpad power saving
Reverted it to deafult.
	`sudo nano /etc/tlp.`
	`USB_AUTOSUSPEND=0`
	`sudo tlp start`

Didnt work : disabling i2c HID power management quirks
	:`sudo nano /etc/default/grub`
	`GRUB_CMDLINE_LINUX_DEFAULT="quiet splash i2c_hid.mousepoll=0"`
	`sudo update-grub`
	`reboot`

Didnt work (worst attemp): Disabling libinput by creating config
	`sudo mkdir -p /etc/libinput`
	`sudo nano /etc/libinput/local-overrides.quirks`
	Adding this inside : 
	`[Elan Touchpad Override]`
	`MatchName=Elan Touchpad`
	`AttrPressureRange=10:8``
	AttrPalmPressureThreshold=90`
	`AttrThumbPressureThreshold=85`
	
	= this causes weird messages during boot up. DO NOT RECREATE

Didnt work : reloading psmouse module
	`sudo modprobe -r psmouse && sudo modprobe psmouse`

---

## Fix if the colors in the bash shell is gone.
*June 28,2026*
*Overview: I did a practice using reverse shell using netcat, Used the named FIFO method and the VM served as an outbound endpoint for connection. But, somehow i dont what happened after i exited the session that the colors for the terminal is gone.



>**I did**
```
echo $0
echo $SHELL
ps -p $$ -o pid,ppid,cmd
```
And confirmed that im using /bin/bash
```
linuxuser@linuxuser:~$ echo $0 
bash 
linuxuser@linuxuser:~$ ps -p $$ -o pid,ppid,cmd 
  PID PPID CMD 
  3883 3718 bash
```
I check the content of the `~/.bashrc`. Found out its empty—which should not be
```
nano ~/.bashrc
```

**THE FIX**

>Reinstall the bash defaaults
```
sudo apt install --reinstall bash
``` 
>Restore colors 
```
export PS1="\[\e[1;32m\]\u@\h:\w\$ \[\e[0m\]"
```

## Using terminator
*June 30, 2026*
*Machine: Linux Mint, Cinnamon, Ubuntu Noble Base 24.0*
*Purpose: For basic but useful functionality that the regular terminal doesnt have — mainly the side by side terminal in a single window.*

```Note
Other option can be is tmux, but i personally think its for advance customization — Ill opt to that if necessary.
```

Install:
```bash
sudo apt intall terminator
```

Setting a shortcut key for opening:
```
1. Open menu(press the super key or the windows key for windows keyboard)
2. Search for 'keyboard' and open it.
3. In the `Shortcuts` tab go to `Custom Shortcuts`
4. Press `Add custom shortcuts` 
   Name: `Launch Terminal` (or whatever you want)
   Command: `terminator` (withtout the quotes)
   Then click `Add`.
5. Under the `Keybord Bindings`, double click an `unassigned`, then perform your desired shortcut. In regular terminal — its ctrl+alt+E, so for easier just do ctrl+alt+y
   
```

Common shortcuts:  *For reference, visit : [Full Shortcuts](https://github.com/igniteflow/terminator-keyboard-shortcuts)*
```
ctrl + shift + E   =   Split vertically
ctrl + shift + O   =   split horizontally
ctrl + tab         =   Move to next terminal within the same window
ctrl + shift + W   =   Close the current terminal
```

### Setting it for Autostart
```
cd ~/.config/autostart
sudo nano terminator.desktop

[Desktop Entry]
Type=Application
Name= Your terminator
Exec=terminator
X-GNOME-Autostart-enabled=true
```
