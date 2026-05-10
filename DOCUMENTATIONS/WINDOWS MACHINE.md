

### Enable/Disable "Aggresive Mode" For AMD CPU
*Date : April 17, 2026*
*Purpose: To enable toggle for performance flexibility use under the power management*

1. Open Regedit(Registry editor)
2. Navigate to `Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\54533251-82be-4824-96c1-47b60b740d00\be337238-0d82-4146-a960-4f3749d470c7`
3. Find the `attributes` and set the value to 2
4. Go advance power settting management in control panel
5. Find the processor power management and the `Processor Performance Boost Mode` should be visible now.  
6. Tweak it

### INSALL LENOVO LEGION TOOL KIT
*Overview: Mainly for lenovo only*
Date: `*****` 
*Purpose: Replace the Lenovo vantage *


### UNDERVOLTING MY LAPTOP
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
