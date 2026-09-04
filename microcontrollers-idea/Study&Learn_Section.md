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
>  [[#Random Modules & Microcontroller Fundmantals]]

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


>--05. **Uploading codes**

Navigate on Left side of VScode
`platformIO` —> Project Tasks —> `<Your environment>` —> General —> Upload.



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
###### **LED**
---
In standard LED:
- `anode` — The `long leg` . The positive
- `cathode` — The `short leg`. The negative

###### **Analog Multimeter**
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

### Mistakes
---

##### Mistake number  
aug 31, 2026

while measuring current using analog meter

i accidentally made a short circuit by putting the negative probe of the multimeter to GND source instead of making a series connection with the components.

the circuit should be in series :
```
5v --- meter --- component --- resistor --- gnd
```

```
3v-----(+)[led](—)---(+)multimeter(—)---[resistor]----GND
```
### Random Modules & Microcontroller Fundmantals
---

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
##### variables
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
****

==**millis()**==
- good replacement for `delay`
- assigned as `long` data type at setup : `long previousMillis = 0;`
- assigned as `unsigned long` data type inside the loop function: `unsigned long CurrentMillis = millis()`
- `unsigned long` — means no negative value