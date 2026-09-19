

##### ==Proper use of multimeter when measing current==
*aug 31, 2026*
while measuring current using analog meter

i accidentally made a short circuit by putting the negative probe of the multimeter to GND source instead of making a series connection with the components.

the circuit should be in series :
```
5v --- meter --- component --- resistor --- gnd
```

```
3v-----(+)[led](—)---(+)multimeter(—)---[resistor]----GND
```

---


==##### ADC Reference Voltage Mismatch (Potentiometer max reading capped low)==

Reference:
	- [[Code_Artifacts#OLED + LED + Poteniometer + Button]]
	- [[Study&Learn_Section#**==Analog To Digital (ADC)==**]]

*==Symptoms*: ==
The max reading of potentiometer connected in AnalogPins gets capped at value of 166 instead of 255

==*Root cause* : ==
	- The potentiometer VCC(power source) is connected to 3.3V pin of arduino uno, 
	- On the Arduino Uno, `analogRead()` normally uses AVCC (~5 V) as its ADC reference.

*Formula*
```
Formula: digital_value = (input_voltage / reference_voltage) × 1023
```

==*Fix*==
Connect the VCC of the component to a 5v source