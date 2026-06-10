*Overview: This note taking began on March 28, 2026 with the intention of documenting problems and fixes also potential recreating a small setups and workflow *


>[!Contents]-
>[[#Things to do after fresh install ng linux]]
>[[#Get Fullscreen in Vbox linux]]
>[[#Fixing sluggish and slow response trackpad (NOT RESOLVE)]]
>

##### **Things to do after fresh install ng linux**
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

##### **Get Fullscreen in Vbox linux**
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

##### **Fixing sluggish and slow response trackpad (NOT RESOLVE)**
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

##### **CHANGES IN GRUB FILE**
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

---

#### **WARPINATOR CANT CONNECT**
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
### Cant established a connection to server from different network across internet.
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

#### The anomaly: 
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


#### THE CONCLUSION (most likely)
---
**Most likely the internal routing issue with my ISP**
- With the use of cloudflare warp, the request gets redirected to the routing of the cloudflare. 
- After eliminating other potential problem, it narrows it down to ISP backbone routing.
- With inconsistent results from testing to different network, others can connect other cannot. The routing broken to other networks.