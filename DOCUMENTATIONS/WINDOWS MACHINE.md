


## **Excel Notes**
---
August 28, 2026

##### How auto-numbering even with filtering enabled
---
Use the formula: SUBTOTAL
```
=SUBTOTAL(103,$column$rowNum:column+rowNum)
=SUBTOTAL(103,$B$2:B2)
```
Note: the `103` is fixed.


## **Some "How To" Section**
---
### INSALL LENOVO LEGION TOOL KIT
---
*Overview: Mainly for lenovo only*
Date: `*****` 
*Purpose: Replace the Lenovo vantage *

### Mount Fat32 drive in WSL
---
Create a mount dir
```
sudo mkdir /mnt/<driveLetter>
```

Mount the drive by specifying `drvfs` or `vfat`
```
sudo mount -t drvfs F: /mnt/<driveletter>
```
*(Replace `E:` and `/mnt/e` with your specific drive letter).*

---

### Bypass the Win 11 sign in/ sign up
---
During the setup, Hit `Shift+F10` then type
```
ncpa.cpl
```
A control panel windows will appear. 
>Disable the wifi adapter

Back to cmd and type:
```
oobe\bypassnro
```

---

### UNDERVOLTING MY LAPTOP
---
*Overview: Philippines is a hot country. Imagine a laptop running at max load in a room with NO AC and with ambient temperature of 32C during summer.*
Date: May 01 2026
Purpose: To cooldown CPU temperature 

Overview2
Machine : Lenovo Ideapad Gaming 3 15CH6
Ryzen 5 5600H
RTX 3050TI
16GB RAM (Dual channel 8+ 8)

Ryzen 5 5600H Specs: 
Zen 3 Architecture
6C 12T
3.3Ghz Base and 4.2Ghz Boost
L1 Cache 384Kb, L2 Cache 3Mb, L3 Chache 16Mb, IGPU Vega 7 1900Mhz
Default TDP at 45W, OEM Configurable  35-54W
TJMAX (thermal ceiling) : 100C
Memory Support at DDR4-3200

**Install the AMD Universal Utility Tool**
- Link : https://amdaputuningutility.com/

**Perform an idle test and stress test log using the hwinfo**
- Test 1 : No Tweaking, RAW Power.  A 10Min Minecraft session
	- Avg Temp : 91C
	- Avg Clock Speed : 3881 Mhz
	- Avg Package Power : 26.2 W 
	- Time: Above 90C 85% of the session.
- Test 2 : Cinebench R23. 10Min Test. RAW power
	- Avg Temp : 97C Peaking at 98C
	- Avg Clock Speed : 3876 Mhz
	- Avg Package Power : 57.6 W Peaking at 67.4W
	- Time: Above 95C at 98% of the session.

**Open the Universal Utility Tool and Tweak with the settings**
Settings:
```
#Under APU Power Tuning
	- STAPM Power Limit : 45W
	- Slow Power Limit: 45W
	- Slow Boost Durataion: 60s
	- Fast Power Limit: 52W
	- Fast Boost Duraation 15s
	  
#Uder APU VRM Tuning
	- CPU TDC Limit : 33A
	- CPU EDC Limit : 72A
```

**Perform a second test**
- Test: Minecraft
	- Avg Temp : 88C
	- Avg Clock speed: 3979Mhz about 301Mhz higher than Initial Test
	- Avg Package Power: 26.0W same as baseline
	- Time: Above 90C aat 10% of the session. Only brief spikes now.

- Test: Cinebench R23
	- Avg Temp : 89.8C
	- Avg Clock speed: 3670Mhz lower than the baseline. 
	- Avg Package Power: 43.5W
	- Time: Above 95C at 0% of the time. Never hitting 95C now


---

### Enable/Disable "Aggresive Mode" For AMD CPU
---
*Date : April 17, 2026*
*Purpose: To enable toggle for performance flexibility use under the power management*

1. Open Regedit(Registry editor)
2. Navigate to `Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\54533251-82be-4824-96c1-47b60b740d00\be337238-0d82-4146-a960-4f3749d470c7`
3. Find the `attributes` and set the value to 2
4. Go advance power settting management in control panel
5. Find the processor power management and the `Processor Performance Boost Mode` should be visible now.  
6. Tweak it


---

### Timelapse recording
---
#### Setup: 
```
Machine & OS: Wuindows 11
Tools: OBS, ffmpeg
```
*This setup assumes there is already OBS Installed*

--1. ==Download the ffpmeg== 
Link: https://ffmpeg.org/download.html#build-windows  
	- Two options are: `ffmpeg-git-essentials` or `ffmpeg-git-full`

--2. ==Extract the downloaded== 
Place the extracted folder somwhere easy to access.
- Inside the folder, there is a `bin` folder which contains:
	- `ffmpeg.exe`
	- `ffplay.exe`
	- `ffprobe.exe`

--3. ==Add the folder dir to the PATH== 
- Search in windows: `Environment Variables`
- Inside the `User Variable for <youruser>`, click the `Path`
- Inside the window of listed `path`, click `new`
- Paste the `location` where the 3 exe is located : `\C:Users\<youruser>\ffmpeg\bin`

--==4. Verify==
```
ffmpeg -version
```

#### Usage

--1. ==Naviagte to where the recording is==
```
cd C:\Users\<youruser>\Vidoes
```

--2. ==Record normally in OBS==
No special OBS settings needed. Record at normal fps (e.g. 30fps), stop whenever done. This produces a full-length `.mkv` file — not a frame-interval capture.

--3. ==Run the ffmpeg speed-up command==
```
ffmpeg -i input.mkv -filter:v "setpts=SPEED_FACTOR*PTS" -an output_timelapse.mp4
```
- `input.mkv` = actual OBS output filename
- `-an` = strips audio (not needed for timelapse, and hard to sync at high speed anyway)
- `SPEED_FACTOR` = computed below

#### ===Method used: Post-process speed-up (not frame-interval capture)===
This is the approach actually implemented. OBS records the full session normally; ffmpeg speeds it up afterward. Simpler, no capture-side scripting/overhead, but full-length video sits on disk while recording (vs. saving only individual frames).

**Formula:**
```
speed_factor = target_duration ÷ original_duration_in_seconds
```

**Real example used (48min 13sec recording → 10sec output):**
```
48 min × 60 = 2880 sec
2880 + 13 = 2893 sec

speed_factor = 10 ÷ 2893 = 0.003456
```
==Command:==
```
ffmpeg -i input.mkv -filter:v "setpts=0.003456*PTS" -an output_timelapse.mp4
```

Reference table (speed factor ÷2893, adjust denominator per actual recording length):

| Target output | Speed factor |
| ------------- | ------------ |
| 10 sec        | 0.003456     |
| 15 sec        | 0.005184     |
| 20 sec        | 0.006912     |
| 30 sec        | 0.010368     |

---
#### Reference only: Frame-interval formula (true timelapse capture — NOT the method used)

This is for the *actual* "1 frame every N seconds" capture approach (OBS/ffmpeg grabbing individual frames at intervals, then encoding the image sequence) — different from the speed-up method above. Kept here in case this gets implemented later (more overhead, more setup, but produces literal interval-based frames instead of a sped-up full recording).

```
TotalFrames = recording_duration_in_sec ÷ interval_capture_per_sec
Duration = TotalFrames ÷ desired_fps
```

Example (interval known first):
```
1hr (3600s) ÷ 12s per frame = 300 total frames
300 frames ÷ 30fps = 10 sec output
```

Or, if duration is decided first, solve backwards for the interval:
```
TotalFrames = desired_duration × fps
Interval = recording_duration ÷ TotalFrames
```

Example:
```
10s × 30fps = 300 frames
3600s (1hr) ÷ 300 frames = 12 sec per frame
```

**Next step (not yet implemented):** wrap the speed-up method into a `.bat` file for one-click conversion instead of manual command typing.


---
### install pdftoppm
A CMD Tool to convert pdf to png

```
winget install oschwartz10612.Poppler
```

```
pdftoppm -v
```

==Usage:==
```
pdftoppm -r 300 sample.pdf page -png
```


### Swap all windows between two monitors (AutoHotkey)
---
*Date: October 08, 2026*
*Purpose: One hotkey to swap everything between Monitor 1 and Monitor 2 (side-by-side). E.g. Obsidian on Monitor 1 and browsers on Monitor 2 become browsers on Monitor 1 and Obsidian on Monitor 2.*

```
Monitor 1        Monitor 2
Obsidian         Chrome, Firefox
      ---- HOTKEY ----
Chrome, Firefox  Obsidian
```

#### ==🔵Setup
```
Machine & OS: Windows 11
Tools: git, AutoHotkey v1.1 (NOT v2, the syntax is different)
```

--1. ==Install AutoHotkey v1==
```
winget install AutoHotkey.AutoHotkey
```
If it installs v2, the script will not run. Get v1.1 from the AHK site.

--2. ==Get the original source (gist is a git repo)==
```
git clone https://gist.github.com/62630392c02420fab9c9f009aa765b39.git monswap
```
Original author: szkrd (based on MonSwap by Alan Henager, AutoHotkey forum thread t=68311).
Gist: https://gist.github.com/szkrd/62630392c02420fab9c9f009aa765b39

#### ==🔵Why the original did not work for me==
The original `monswap-top-bottom.ahk` is for **vertically stacked** monitors. For side-by-side:
- It only moves the Y-axis. `NewX` is never set, so X stays unchanged.
- Monitor detection uses the window's Y center, which is the same range on both monitors when side-by-side.
- Maximized windows are not restored before moving, so they often do not move.
- Windows are not resized when the monitors have different resolutions.

#### ==🔵Fixed version: `monswap-side-by-side.ahk`==
Key changes: use X for monitor detection, scale X/Y/width/height between work areas, restore maximized windows before moving then maximize again.
```ahk
#NoEnv
SetWinDelay, 0

; Hotkey: Ctrl + Win + Z
^#z::
    DetectHiddenWindows, Off
    WinGet, WinArray, List
    Loop, %WinArray% {
        WinID := WinArray%A_Index%
        WinGetClass, cls, ahk_id %WinID%
        if (cls = "Shell_TrayWnd" or cls = "Shell_SecondaryTrayWnd" or cls = "Progman" or cls = "WorkerW")
            continue
        WinGetTitle, title, ahk_id %WinID%
        if (title = "")
            continue

        WinGet, state, MinMax, ahk_id %WinID%
        if (state != 0)            ; minimized (-1) or maximized (1)
            WinRestore, ahk_id %WinID%

        SwapMon(WinID)

        if (state = 1)
            WinMaximize, ahk_id %WinID%
        else if (state = -1)
            WinMinimize, ahk_id %WinID%
    }
return

SwapMon(WinID) {
    SysGet, M1, MonitorWorkArea, 1
    SysGet, M2, MonitorWorkArea, 2
    M1W := M1Right - M1Left
    M1H := M1Bottom - M1Top
    M2W := M2Right - M2Left
    M2H := M2Bottom - M2Top

    WinGetPos, X, Y, W, H, ahk_id %WinID%
    cx := X + W / 2

    if (cx >= M1Left and cx < M1Right) {        ; Monitor 1 -> Monitor 2
        nx := M2Left + (X - M1Left) * M2W / M1W
        ny := M2Top  + (Y - M1Top)  * M2H / M1H
        nw := W * M2W / M1W
        nh := H * M2H / M1H
    } else {                                    ; Monitor 2 -> Monitor 1
        nx := M1Left + (X - M2Left) * M1W / M2W
        ny := M1Top  + (Y - M2Top)  * M1H / M2H
        nw := W * M1W / M2W
        nh := H * M1H / M2H
    }
    WinMove, ahk_id %WinID%, , %nx%, %ny%, %nw%, %nh%
}
```

#### ==🔵Usage
- Double-click the `.ahk` file (green H icon appears in the tray).
- Press `Ctrl+Win+Z` to swap.
- To change the hotkey, edit `^#z` (e.g. `^!m` = Ctrl+Alt+M).

#### ==🔵Notes / limitations
- Swaps **all** visible windows, not only specific apps. To limit it to certain apps, filter by `ahk_exe` (e.g. `obsidian.exe`, `chrome.exe`, `firefox.exe`).
- Assumes exactly 2 monitors. With a 3rd monitor, windows not on Monitor 1 go to Monitor 1.
- Mixed DPI scaling can cause small size/position errors.

**Next step (not yet implemented):** auto-start on boot and move the repo from the flash drive to the home directory on C:.


## **Troubleshooting Section**
---
### Windows Sudden Crashed and Rebooted
---
July 07, 2026

Lenovo Ideapad Gaming 3 15ACH6, 
Windows 11, version 25H2

Overview: Laptop suddenly crashed, display below text about `Page File` , Its too fast i cant read it all. 

As soon as the machine reboots. I directly went to Event Viewer. Under:
```
Winddows Logs-->System--->Filter Current Log ---> 
Logged: Last Hour
Event Level: Critial , Error
```
Found the Event ID 41, Kernel-Power. A plain report not useful.

```
The system has rebooted without cleanly shutting down first. This error could be caused if the system stopped responding, crashed, or lost power unexpectedly.
```
Note: *For faster search, filter to search ff ID  41, 6008, 1001, 27, 29, 7000, 7001, 7011*

Found the Event 1001, A BugCheck Error. Which tells where the dump log is located
```
The computer has rebooted from a bugcheck.  The bugcheck was: 0x00000050 (0xffffec8584cf1b30, 0x0000000000000000, 0xfffff80066cadc36, 0x0000000000000002). A dump was saved in: C:\WINDOWS\Minidump\070726-18453-01.dmp. Report Id: 1653a7c8-7867-402e-a924-d72e09b23de9.
```

I dont know how to read hex. So i directly install WnDbg— a tool for analyzing the dump at `C:\WINDOWS\Minidump\`.
Open WinDbg as an Admin
```
Open the dump file ---> Click analyze.
```
i couldnt understand the output, i had to rely to AI. 

---
### Fixing boot error in Windows
---
*June 21,2026*
*Machine: Windows 11*.
```
Case: A machine that is force shutdown multiple times by holding the power button.
	- When powering on—Its not booting properly and stuck on Choose a Keyboard Layout
	- It keeps launching the WinRE( Windows Recovery Environment)
	- Choosing the Startup Repair didnt fix it
	- The chkdsk C: /f /r didt fix it
```

>**PROCEDURAL FIX DONE**

>**Verify disk state**
```
diskpart
list disk
select disk 0
details disk
```
If it shows :
```
Read-only: Yes
Offline: Yes
```
Do:
```
attributes disk clear readonly
```

>**Check the actual partition**
```
list volumes
```
The EFI partition should be visible

>Assign a letter to the EFI. Then verify where the Windows installation is
```
select volume <number of EFI volume>
assign letter =S
dir C:\Windows
``` 

>Suppose the Windows Installation in on C:
```
bcdboot C:\Windows /s /S: /f UEFI
```
*Code Explanation*:
```
bcdboot = rebuilds the boot environment
C:\Windows = the bcdboot uses this path a a source of boot files to generate a new boot configuration files.
/S: /f UEFI = this is the destination path of the broken boot config. ANd this is where it will rebuild a new bootconfig file.
```

>**CONCLUSION**

```
Doing the bcdboot fixes it.

What makes it broken is due to force shutdown:
	- **Files currently being written**
	- **Filesystem metadata** (NTFS journals, directory entries, allocation tables)
	- **Registry hives**
	- **Boot-related files and configuration**
	- **Windows Update operations in progress**
```
****

****




