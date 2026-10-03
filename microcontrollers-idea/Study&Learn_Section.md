---
tags:
  - study
  - general-notes
  - setup
  - howto
  - microcontrollers
Date Generated: August 29, 2026
---
>[! Content]- Content
>[[#Use VsCode as IDE for microcontrollers]]
>
>[[#Random Electricity Fundamentals]]
>- [[#**Ohms Law**]]
>- [[#**LED**]]
>- [[#**Analog Multimeter**]]
>
>  
>  [[#Random Modules & Microcontroller Fundamentals]]
>-  [[#==**PWM** Pulse Width Modulation==]]
>- [[#==**Analog Pins vs Digital Pins**==]]
>- [[#**==Input Pins==**]]
>- [[#**==Pull-up and Pull-down resistor==**]]
>- [[#**==Floating Input==**]]
>- [[#**==Potentiometer==**]]
>- [[#**==Analog To Digital (ADC)==**]]
>- [[#==**Voltage vs Signals**==]]
>- 
>
>[[#Random Syntax Fundamentals]]
>- [[#variables]]
>- [[#==arrays and arrays indexing==]]
>- [[#==Modulo==]]
>- [[#==**millis()**==]]
>- [[#==functions==]]
>- [[#==analogRead()==]]
>- [[#==Convert float into string==]]
>- [[#==Convert int value into string==]]
>- 

### Use VsCode as IDE for microcontrollers
---

>--01. **Install PlatformIO**

	In VS Code:
	Extensions (`Ctrl+Shift+X`) —> search —> `Platform IDE` —> Install
	
	`PlatformIO's` Official VS Code Integration already includes PlatformIO Core, so no need to seperately install the PlatformIO CLI.
```
	VS Code
	 └── PlatformIO IDE
	    ├── Arduino framework
	    ├── ESP32 platform
	    ├── AVR/Arduino platform
	    ├── Library management
	    ├── Build system
	    ├── Upload
	    ├── Serial Monitor
	    └── Debugging
```

>--02. **Restart VScode and Create Projects
	On the left panel —> click the alien icon —> Create Projects


>--03. **Understanding Project Structure

Each time a project is created, it is a folder.

Example structure:
```
Microcontrollers_Parent_Folder/
  |——esp32_folder/
	  |——study_Project/
  |
  |——arduino/
	  |——study_project_folder/
		  |—— .pio/
		  |——.vscode/
		  |——include/
		  |——lib/
		  |——src/
		  |——test/
		  |——.gitignore
		  |——platformnio.ini
```
- `.pio`  — build artifacts: *any machine-generated file or output produced during the software compilation and packaging process*. DO NOT COMMIT. DO NOT TOUCH.

- `.vscode/` — stores all the config of vscode itself.

- `include/` — default path for projects public header
  
- `src/` — this is where the code will be. 
	  The ff:
	  -`#include` and other library declarations.
	  -`pin declaration`
	  -`varialbes`
	  -`setup()` and `loop()`
	  -`conditional and sensor logic`
	  
- `platform.ini` — a config file that sets development environment, things like what `board`, `framework`, `libraries` etc.

- `lib/` — intended for self-made library
- `test/` — for advance users. Test Driven development
- `.gitignore` — contains list of files or dir to be ignore during commits


>--04. **Library Installations**

For beginners there are already tons of ready-made libraries for modules.

To install and Use:
On left side panel click the`Alien Icon` ——> In PIO Home click the `Libaries` —> Search ——> Check the installation and how to use. 

Or you can directly click the `Add to Project` button 


>--05. ** Build and Uploading codes**

Navigate on Left side of VScode
`platformIO` —> Project Tasks —> `<Your environment>` —> General —> Upload.
`platformIO` —> Project Tasks —> `<Your environment>` —> General —> Build.

`====OR===`

You can setup the terminal and use it.
--1: Add the path `C:\Users\<username>\.platformio\penv\Scripts`
in the `path` environment variables.
--2: restart the vscode
--3: test:
```
pio --version
pio run -e uno   # BUILD. uses the <uno> set at the `[env:uno]`  
pio run -e uno -t upload #BUILD AND UPLOAD
```


----
#### Multiple cpp files
---
`main.cpp` — the main program; usually contains the `setup()` and the `void loop()`. 
`Additional cpp files` — can exist in a single dir project but serves a diff purpose.
- To store related functions 
- normally do not have their own `setup()` and `void loop()`.
- All cpp file are compiled into one firmware
Examples
```
src/ 
├── main.cpp     → setup(), loop(), overall logic 
├── sensor.cpp   → sensor-related functions 
└── display.cpp  → display-related functions
```
Concept:
```
main.cpp 
├── readTemperature()     → sensor.cpp 
└── displayTemperature()  → display.cpp
```



----

### Random Electricity Fundamentals
---
August 29, 2026
##### **Series and Parallel**
---
==**Series Circuit**==
- This is where components are connected `continuously` in a single path.
- Their voltage requirements add up
```
----(—)source(+)-----(+)led(—)---(+)led(—)----(—)source---....
```

**==Parallel Circuit==**
where components are connected in separate branches.
- Each branch sees the supply voltage
- **Current Divides** between each branches
- The total current is the sum of all branch currents
```
Positive rail ─────────────────────────────
                 │              │
              long leg       long leg
                 │              │
                LED            LED
                 │              │
              short leg      short leg
                 │              │
Negative rail ─────────────────────────────
```
**Mental model:**
>More paths → more current can flow → higher total current

More paths also **lower the equivalent resistance** of the circuit 
```
       R₁ × R₂
Rₜ = ───────────
       R₁ + R₂
```

##### **Ohms Law**
---
Ohm's Law describes the relationship between **Voltage (V)**, **Current (I)**, and **Resistance (R)**.

Formula:
```Formula
      V
     ───
    I | R
```
- `Voltage = Current × Resistance` → `V = I × R`
- `Current = Voltage / Resistance` → `I = V / R`
- `Resistance = Voltage / Current` → `R = V / I`


==**Voltage Across Resistor**==
- When a resistor is connected in series with another component, the resistor may **not receive the full source voltage**.
- The `voltage across the resistor` —  is the **remaining voltage** after the voltage drop of the other component.

>Example:
```
3V Battery ──[ Resistor ]──[ LED ]── GND 
                             ~2V
```
`Voltage across resistor = Vsource - Vforward`
`Voltage across resistor = 3v - 2v`

Therefore, when calculating the resistor for an LED:
Formula:
```Formula
R = (Vsource - Vforward) / Idesired
```

Example:
- Vsource = 3V 
- Vforward = 2V 
- Idesired = 10mA = 0.01A
```
R = (3V - 2V) / 0.01A R = 100Ω
```
The only difference is that `V` must be the **voltage across the resistor**(1v), not necessarily the entire source voltage (3v).

==**Component Ratings**==
Components have specified operating conditions and **absolute maximum ratings**.

Before connecting an unknown component, check its datasheet from source like online for:
- Operating voltage
- Forward voltage (`Vf`), when applicable
- Recommended/rated current
- Maximum current
- Other relevant maximum ratings

The **desired operating current** should normally be chosen within the component's recommended operating range, rather than simply using its absolute maximum current.


---
##### **LED**
---
In standard LED:
- `anode` — The `long leg` . The po
- sitive
- `cathode` — The `short leg`. The negative

##### **Analog Multimeter**
---
In analog multimeter there is the black and red probe
`Red` — positive
`Black` — negative

==**In resistance mode meter**==
But in `resistance mode` , the multimeter uses its internal battery to inject small current into the components being tested. And it  wired such that:
`Black` — positive 
`Red`— negative 

In tldr: 
-  `Normal voltage measurement` — Red probe read positive from outler/source.
- `Resistenace measurement` — the multimeter outputs current from the battery to black probe

==**REMINDER REMINDER REMINDER**==
To should not use the `resistance mode` in a powered circuit. 
Because the `resistance mode` assumes that it is the one giving voltage/current to the components.

==**When measuring current using multimeter**==
You essential will let the current flow through the multimeter so its better to disconnect the ground of the circuit
Connect the probe in series:
```
3v-----(+)[led](—)---(+)multimeter(—)---[resistor]----GND
```
Reading the analog:
```
setting_num / 50lines  then multiply by which line the needle points. 
```

----
#####  **==Reference Voltage==**
`Vref` — In abstract term. It is a  voltage that the [[#**==Analog To Digital (ADC)==**|ADC]] uses as a baseline to know how high the input voltage is from the components that produces varying voltage.

==*Tldr*==
- Its like a ruler but for voltage
- Vref tells the ADC what voltage range its "ruler" should cover.
```
      REFERENCE
0v——1v——2v——3v——4v——5v

0v——1v——2v——3v——4v——5v
             ^
            The input, for example
```

==*Note*: ==
- Vref is a property of a [[#**==Analog To Digital (ADC)==**|ADC]] which is inside the microcontroller
- Vref is not a component's required supply voltage.
- In Arduino uno, the default Vref is 5volts — but can be change via `analogReference()` or some external AREF pin

==*Formula*:==
To convert voltage to ADC:
```
digital_value = (input_voltage / reference_voltage) × 1023
```
To convert ADC to voltage:
```
analog_value = analogRead(A0) * (5.0 / 1023.0);
```


----

### Random Modules & Microcontroller Fundamentals
---

##### ==**PWM** Pulse Width Modulation==
---
In arduino uno—pins with symbol `~` means its capable of PWM.

==`Pulse Width Modulation`==
- `digitalWrite()` — it only do `LOW`  or `HGIH` OR `0volts to 5v`.  IT CANT be in between.
- `ANALOG Voltage` — *continuously variable* ouputs between 0v to 5v. BUT **arduino uno DOESNT HAVE** analog output.
- `analogWrite() PWM` — it mimics `ANALOG Voltage` by rapidly switching between `0v` and `5v`
- *Reference video:* [Science Buddies](https://youtu.be/QCZISwDi_Qo?si=OayO10M9m5-RcJsX&t=481)

Example:
```c
digitalWrite(9, HIGH) // REGULAR OUTPUT PIN
analogWrite(9, 25)   // PMW-cable PIN
```
- it uses  `analogWrite` with the value can be set from `0-255`
- the `value` tells the `ON-ness` of the pin. 
- `25` — means ON `9.8%` of the time and OFF `90.2%` of the time.

>*For another example:* [[Code_Artifacts#Millis with PMW]]


---
##### ==**Analog Pins vs Digital Pins**==
---
**Reminder:** Components/modules may need a **power supply** to operate.  
They can then send a **signal** to the Arduino.

- **Power** → supplies the component so it can operate
- **Signal** → carries information from the component to the Arduino
- The signal can be read through:
    - ==**Analog pins** ==
	    - read/measure a analog voltage from component such as a potentiometer, sensors
	    - but it can still be used as digital pins, including digital outputs
    - ==**Digital pins**== 
	    - can read voltage but only HIGH or LOW. no in between

---
##### ==**Digital Sensor vs Analog Sensor**==

==`Analog Sensors` ==
- provides a **varying voltage** that represents a physical condition or measurement.
- uses [[#**==Analog To Digital (ADC)==**|ADC]] to convert the Voltages values into digital numbers

```
Physical condition changes 
          ↓
 Sensor output voltage changes 
          ↓ 
      Analog Pin 
          ↓
        ADC 
         ↓ 
   Digital number 
         ↓ 
        Code 
         ↓ 
   Useful value

Example
Sensor output → 0–3.3V
ADC → 0–675-ish (if Vref = 5V)
```
Read: [[#**==Reference Voltage==**|Reference voltage]] for how it works

==`Digital Sensors`==
- **internally measures and processes** the physical condition, then sends the result as **digital data** through its communication pin.

```
🌡️ Physical condition changes
        ↓
Sensor internally measures/processes
        ↓
   Digital data
        ↓
   Bits (1s and 0s)
        ↓
  Communication Pin
        ↓
      Arduino
        ↓
Decode according to
sensor's protocol
        ↓
 Useful value
```
The voltage is still present on the communication pin, but it is used to represent **digital bits** rather than directly representing the measurement.

##### **==Input Pins==**
---
Doesnt require high current to read the voltages

```
5v —————— [10k ohms]———————————button——————— GROUND
                        |
                    Input Pin()
```

---
##### **==Pull-up and Pull-down resistor==**
---
>**==Pull up Resistor==**
```
5v —————— [10k ohms]———————————/  ———button——————— GROUND
                        |
                    Input Pin
```
- The input pin reads the 5v when the button is not pressed

```
5v —————— [10k ohms]——————————————button——————— GROUND
                        |
                    Input Pin
```
- The input pin now reads 0 volts when button is pressed
- Because, the input node is now directly connected to GND through pressed button

>**==Pull Down Resistor==**
```
5v————/  ——button————————————————[10k ohms]——————GND
                     |
                 Input pin
```
- When the button is not pressed, the Input pins reads zero volts

```
5v————————button————————————————[10k ohms]——————GND
                       |
                   Input pin
```
- Then input pin now reads 5volts when button is pressed.
- Because the resistor limits the current flowing from 5v to the GND path

==Why set the appropriate value for pull down or pull resistor==
- There is nothing wrong, the logic will still apply.
	- But super low Resistance Value can be such a waste current for a basic purpose such as pressing button
	- But super high Resistance value can cause noise interference 

---
##### **==Floating Input==**
---
In [[#**==Input Pins==**|input pins]] it usually reads  state—can be `HIGH` or `LOW`. 
But ==LOW and disconnected are not the same==
- `LOW` — means connected to the GND/0volts
- `Disconnected/Floating` — no definite connection to `HIGH` or `LOW`

A floating input can produce unpredictable reading, that is why [[#**==Pull-up and Pull-down resistor==**|Pull-up / Pull-down resistor]] is used to give the input pins a default state.
To demonstrate, the best component to test is [[#**==Potentiometer==**|Potentiometer]]. Read its value but let the vcc disconnected—result will be, instead of value `0-1023`,it iwll be random single digit numbers.

---
##### **==Potentiometer==**
---
Gives variable voltage to the microcontroller.
Uses the  [[#**==Input Pins==**|input pins]] to read those variable voltage
- `Middle pin` — the output/wiper, for input pins
- `Two outer pin` — interchangeable as `vcc` and `gnd`

>Two outer pins can be switched, though the knob direction will reverse as well
```
5v———>>clockwise>>———gnd  == low resistance to far right knob 
gnd———<<counterCL<<————5v    == low resistance to far left knob
```

Think of potentiometer having two resistor:
```
5V ──[ 1kΩ ]──●──[ 9kΩ ]── GND
              │
              A0
```
- it adjusts the resistance between two nodes rather than directly controlling the current flow
- `Leftside` — path from 5v towards the input
- `rightside` — path from input towards the gnd

---

##### **==Analog To Digital (ADC)==**
---
A hardware component in a microcontroller that converts analog(continuous voltage, e.g. 0V-5V) into a digital number that can be use in coding.
	`0 - 1023` - for 10bit  adc like arduino uno
	`0 - 4095` - for 12 bit adc like esp32


**==Why ADC?==**
- Those analog voltages usually comes from components like (sensors, or potentiometers)
-  Microcontroller processes information digitally, using binary values not. 
- The formula is at [[#**==Reference Voltage==**]]

---
##### ==**Voltage vs Signals**==

Steady voltage source 
- like raw electrical quantity, no inherent meaning, it only gives power.
Signal
- A voltage but with variation/pattern 
- `Analog Signal` — continuous range(e.g. Potentiometer output) uses [[#==analogRead()==|analogRead()]]
- `Digital Signal` — discrete state only either true or false

---
##### **==Components with Analog Output and Digital Output==**

A component with `AO` and `DO` pins can be used together.

Example: 
- `Analog Ouput` — focused on reading 
- `Digital Ouput` — Focused on reading the set threshold

Example are:
[[Components and Modules#==MQ-2 Gas Sensor==]]
[[Components and Modules#==KY-037 Sound Sensor==]]

```
                 MQ-2 sensing
                      │
             gas concentration
                      ↓
             sensor response
                      │
                ┌─────┴─────┐
                ↓           ↓
               AO           DO
           Analog Output  Digital Output
                │           │
                ↓           ↓
    "How is the read?"  "Did it exceed??"
                │           │
                ↓           ↓
          analogRead()    digitalRead()
                │           │
             0–1023       HIGH / LOW
```
With this, its possible to setup a different logic and resposnse.

==Most components with these setup typically has `LM393 comparator` and [[#**==Potentiometer==**|Potentiometer]]==
Reference: https://youtu.be/SqONrFwa4PA?si=tAhR_df4aBzEzhDb&t=297

- The way this works basically is the voltage from analog  went to the `LM393 comparator` which then compares to the value set at the potentiometer.
- If the threshold set at potentiometer reached— outputs Low digital signal and output HIGH digital signal when


### Random Syntax Fundamentals
---

the first line of the `main.cpp` file: 
==`#include <Arduino.h>`==
```d
#include <Arduino.h>

// put function declarations here:
int myFunction(int, int);

void setup() {
  // put your setup code here, to run once:
  int result = myFunction(2, 3);
}

void loop() {
  // put your main code here, to run repeatedly:
}

// put function definitions here:
int myFunction(int x, int y) {
  return x + y;
}
```
Is basically there to include Arduino framework definitions/declarations to enable to use Arduino functions and features.

---
##### ==variables==
---
Directly using pin
```
pinMode(2,OUPUT);
```

storing it in a variable:
```
int ledPin = 2;

pinMode(ledPin,OUTPUT);
```
This is recommended for larger chunks of code. 
- Easy to read
- Easy to change


---
##### ==arrays and arrays indexing==

For reference how its used: [[Code_Artifacts#==OLED + bitmap array==]]

==Creating array for example:==
```c
int numbers[] = {1,2,3,4,5};
int numbers[5] = {1,2,3,4,5}; //explicitly telling the array size
```

==Indexing an array:==
```c
//suppose you have an array:
int numbers[] = {1,2,3,4,5};

//calling
numbers[0] //—— 1
numbers[3] //—— 4 
//...........
```

Function + array indexing:
Used at : [[Code_Artifacts#==oled + basic animation==]] 
```c
delay(bitmap_anim_delays[frame]);
```
- `delay()` — the function
- `bitmap_anim_delays` — an array, contains value `e.i {60, 120 ,400, 30..........}`
- `[frame]` — a variable containing an `int` to tell which index in the array to access `e.i: [0[, [1], [2]......`
****
##### ==Modulo==
Reference usage: [[Code_Artifacts#==OLED + DHT22==]]

Uses the symbol : `%` 
An operator to take the remainder after a division

Example:
```c++
1 % 3 = 1
```
Can be read as: *How many group of 3 can you take from numerator(1). Answer is zero and the remaining is still 1.*

---

#### ==**millis()**==
- good replacement for `delay`
- assigned as `long` data type at setup : `long previousMillis = 0;`
- assigned as `unsigned long` data type inside the loop function: `unsigned long CurrentMillis = millis()`
- `unsigned long` — means no negative value
Example:
```c
long previousMillis = 0;
void loop(){
  //calls out the millis function and store it in currentMillis variable 
  unsigned long currentMillis = millis(); 
  if (currentMillis - previousMillis >= 1000){
    Serial.print("1Sec passed")}
    //updates the previousMillis
    previousMillis = currentMillis
}
```

>For another example that uses [[#**PWM** Pulse Width Modulation|PMW pin]] : [[Code_Artifacts#Millis with PMW|millis with PWM]]

---

#### ==functions==
---
**Warning:** Since the microcontroller repeatedly executes the code inside `void loop()`, be aware of two common types of functions:

- functions that only need to run **once**  per event
- functions that are **acceptable to run many times**

>Example of a function that only needs to run once per event
```
sendMessage() {// send message }
```
If used like this:
```
if (temperature >= 40) { sendMessage(); }
```
- the function will **not automatically run only once**.
- As long as the condition remains `true`, `sendMessage()` will be called repeatedly on every `loop()` iteration.
- An `if` condition does not mean "run once when this becomes true." It means "run whenever this condition is true."
- This can be a problem for functions where repeated execution is undesirable, such as:
	- `sendMessage()` — could send the same message repeatedly
	- `saveData()` — could repeatedly write same data
	- `activateMotor()` — could repeatedly trigger an action

>In contrast, functions that are acceptable to run many times such as:
```
digitalWrite(LED, HIGH);
```
- may be perfectly acceptable to execute repeatedly if the desired result is simply to keep the LED HIGH.


----

#### ==analogRead()==
---
Checks how much voltage is present in the pin, then gives the program a number representing it

Other components can provide a *varying voltage* to the analog pin. The [[#**==Analog To Digital (ADC)==**|ADC]] then converts that analog voltage into a digital number, which `analogRead()` gives to the program.

The `Varying voltage` can change according to changes in the external environment or physical conditions.

*Example*:
```
🌡️ Temperature changes
        ↓
Sensor output voltage changes
        ↓
     Analog Pin
        ↓
       ADC
        ↓
   analogRead()
        ↓
   Digital number
```

That number ranges from `0-1023` because its [[#**==Analog To Digital (ADC)==**|ADC ]] has 10-bit resolution
	0 - lowest measurable voltage
	1023 - highest measureable voltage within the ADC's reference range

---

#### ==Convert int value into string==

This is mainly done to display the value using the [[Libraries#==drawStr()==|drawStr()]] function in U8g2 library 
- View the [[Code_Artifacts#OLED + LED + Poteniometer + Button]] for pattern 
Using the 
```c++
sprintf()

 sprintf(buf,"%d",value);
```

Suppose you there is a number `1000`  and want to convert it to `"1000"` 

==Example==
```c
//suppose
int value = 1000;

//convert
char buf[5];
sprintf(buf, "%d", value);
```
- `char buf[5]`  — setting a buffer max of 5 places, the extra 1 is for null-terminator
- `value` — the variable containing the int value
- `"%d"` — specificier decimal
- `buf` — where the string value will get stored.

---


#### ==Convert float into string==

Using the 
```c++
dtostfr()

dtostrf(value, width, precision, buffer);
```

Suppose there is a float with value of  : `30.3095` and want to turn it into `30.31`.

==Example==
```c++
//suppose
float numbers = 30.30956;

//converting
char buf[7];
dtostrf(numbers, 1, 2, buf);
```
- `numbers` — a float variable containing float 
- `1`—minimum width
- `2` — Decimal places
- `buf` — variable where to store the converted float into string