---
date: September 22, 2026
tags:
  - microcontrollers
  - study
  - sensors
---
---

### ==MQ-2 Gas Sensor==
---
For datasheet: https://www.pololu.com/file/0j309/mq2.pdf
==🔴How it works:== https://youtu.be/SqONrFwa4PA?si=tQrYqQdCHJKJELDs
- Also called `Chemiressitor` sensor
- Works by changing its electrical resistance when its heat
- Higher resistance `Rs` means lower concentration because air molecule from air sticks to the tin dioxide which hinders the electron flow.
- Lower resistance means higher concentration of target gas because gases reacts to the oxygen in the tin dioxide layer which then let elections flow easily


Code Artifacts: [[MQ-2]]

**==🔴Note:==**
The MQ-2 does not directly provide gas concentration. Its resistance changes depending on exposure to gases it responds to. The `Rs / R0` ratio is used with a gas-specific calibration curve or mathematical model to estimate gas concentration. [[Libraries#==🟢calibrate()==]]

==🔵Note==: 
- *Not a lab calibrated sensor-grade sensor* 
- The `AO` and `DO` pins can be use altogether [[Study&Learn_Section#==Components with Analog Output and Digital Output==]]
- `MQ-2` is a ==🟢*gas detection*== not gas identification. Meaaning—it does general reading of the different gasses in the environment.
- Requires 24-48 hours of preheat time for the calibration.

==🟠Core specs to know about:==
- Has 4 pins: VCC, GND, DO, AO
- 5v operation
- Sensor focused on combustible/flammable gases and smoke—LPG, butane, propane, methane, alcohol, hydrogen, and smoke.
- Has `Analog Output` — for continuous reading. Uses [[Study&Learn_Section#**==Analog To Digital (ADC)==**|ADC]] and [[Study&Learn_Section#==analogRead()==|AnalogRead()]]
- Has `Digital Output` — usually uses the onboard potentiometer (LM393 comparator). Uses `digitalRead()`
- `Detection Concentration range` — 300ppm to 10000 ppm 




---

### ==KY-037: Sound Sensor==

Diagram - https://sensorkit.joy-it.net/en/sensors/ky-037
Datasheet - https://fabacademy.org/2024/labs/fabc/students/charlotte-vandenbulcke/files/SEN-17-037-Datasheet.pdf
Code Artifacts - 
	- [[Code_Artifacts#==KY-037 Sound Sensor==]]
	- [[KY-037]]
- 
==Specs==
- Has 4 pins: VCC, GND, DO, AO 
	- Read more about it : [[Study&Learn_Section#**==Components with Analog Output and Digital Output==**]]
- 5v operation
- Has `Analog Output`  — Uses [[Study&Learn_Section#**==Analog To Digital (ADC)==**]]
- Has `Digital Ouput` — for setting logic for threshold


---

### ==🟡SCD41 : Co2 Sensor==
Datasheet: https://download.mikroe.com/documents/datasheets/SCD41%20Datasheet.pdf

==🔴How it works== : https://youtu.be/Bqu9s5eTiL8?si=nu0ODfvvaYgjfS5k
- Uses ***Photoacoustic sensing technology***
	- Meaning, it uses infrared light at specific wavelength to excite Co2 molecules in air 
	- The Co2 absorbs those light energy 



==🔴What does it do?==
- Measures carbon dioxide(C02) concentration in `ppm`. 
- Measures Temperature and Relative Humidity as well

==🔴Baseline==
- ==🟢Around 400-800 ppm ==— Commonly observed in outdoor air and well-ventilated indoor spaces, depending on conditions
- ==🟠Around 800-1000 ==— Worth observing alongside room occupancy and ventilation, though not automatically dangerous.

==🟡Specs==
- uses [[Communication Protocols#==I 2C (Inter-Integrated Circuit)==|I2c]] for communication protocol
