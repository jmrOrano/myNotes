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
How it works: https://youtu.be/SqONrFwa4PA?si=tQrYqQdCHJKJELDs

Code Artifacts: [[Code_Artifacts#==MQ-2 Setup==]]

==Core specs to know about:==
- Has 4 pins: VCC, GND, DO, AO
- 5v operation
- Sensor focused on combustible/flammable gases and smoke—LPG, butane, propane, methane, alcohol, hydrogen, and smoke.
- Has `Analog Output` — for continuous reading. Uses [[Study&Learn_Section#**==Analog To Digital (ADC)==**|ADC]] and [[Study&Learn_Section#==analogRead()==|AnalogRead()]]
- Has `Digital Output` — usually uses the onboard potentiometer (LM393 comparator). Uses `digitalRead()`
==Note==: 
- *Not a lab calibrated sensor-grade sensor* 
- The `AO` and `DO` pins can be use altogether [[Study&Learn_Section#==Components with Analog Output and Digital Output==]]



---

### ==KY-037: Sound Sensor==


Diagram - https://sensorkit.joy-it.net/en/sensors/ky-037
Datasheet - https://fabacademy.org/2024/labs/fabc/students/charlotte-vandenbulcke/files/SEN-17-037-Datasheet.pdf


==Specs==
- Has 4 pins: VCC, GND, DO, AO 
	- Read more about it : [[Study&Learn_Section#**==Components with Analog Output and Digital Output==**]]
- 5v operation
- Has `Analog Output`  — Uses [[Study&Learn_Section#**==Analog To Digital (ADC)==**]]
- Has `Digital Ouput` — for setting logic for threshold