

##### Things to do after fresh install ng linux
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
	`check muna : free -h`
	`if low ram then : sudo apt install zram-tools`

5. Disable unecessary startup apps

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

##### Get Fullscreen in Vbox linux
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

##### Fixing sluggish and slow response trackpad (NOT RESOLVE)
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

##### CHANGES IN GRUB FILE
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
