
#### **==🟢U8g2==**
---
A library for many oled displays
Check the [[Code_Artifacts#==Simple OLED Display==|code artifacts]] for example usage

- [[#==Choosing Correct Frame Buffer==]]
- [[#==setFont()==]]
- [[#==drawStr()==]]
- [[#==setCursor()==]]
- [[#==drawXBMP();==]]

##### ==Choosing Correct Frame Buffer==
Reference : [[Code_Artifacts#==Simple OLED Display==]]

Understand first the ff 
 - [[#Physical OLED]]
- [[#Frame Buffer]]
- [[#Choose the correct framebuffer]]
- [[#==How sending data works when not using the full frame buffer==]]

###### ==Physical OLED==
Example : a 128x64 OLED display that uses SH1106 

- Think of it that it has a total of 8,192 individual small LED pixels (128x64 = 8192)
- Each of that either ON or OFF (its monochrome, no grayscale, each pixel is has only one state.)
- In order for a shape to display (like letter "A"), the OLED chip needs to know where exactly on which 8192 pixels to turn on and which one is to turn off
- To describe the display at a single moment, all 8,192 bits need to know whether they are ON or OFF.
- If the Arduino wants to tell the OLED which pixels should be ON or OFF, where is that information kept? [[#==Framebuffer==]]

###### ==Framebuffer==
A dedicated section of memory that stores pixel data for a complete image frame before it is shown in a display screen.

- It is **not practical** to send the each state of pixel directly to a display screen. 
- This is why one role of the framebuffer is to send all the state of pixel in a single "dump" once the `sendBuffer()` is called.
- ==`OLEDs GDDRAM`== —Once it is send, it is not yet displayed in display but rather stores the data in OLED own memory
- The `GDDRAM` exists because refreshing the display is not instant, therefore the data is staged inside the GDDRAM before it gets displayed.
```
┌─────────────────--┐          ┌─────────────────-─┐         ┌─────────────┐
│  Arduino SRAM     │  I2C/    │  OLED chip's own  │  Direct │  Physical   │
│  (framebuffer sa  │ ─wire─→  │  internal memory  │ ─wire─→ │  LED pixels │
│   RAM ng Arduino) │  send    │  (GDDRAM)         │  drive  │  (ON/OFF)   │
└─────────────────--┘          └──────────────────-┘         └─────────────┘
       |                                |
  This is where computation        This is where the             
  happens                          entire image parked
                                   waiting for refresh 
                                   before sending
```

###### ==Choose the correct framebuffer==

In using the u8g2 library and during the object creation. [[Code_Artifacts#==Simple OLED Display==]]
It typically includes the framebuffer variant to use :;
```
U8G2_SH1106_126X64_NONAME_F_HW_I2C
```

In u8g2's internal design.
A typical 128x64 (8,192pixel) display has about 8 pages if divided horizontally where each page is 8pixel height:
```
┌────────────────────────────────┐  ← y=0
│         Page 0 (8px tall)       │  ← rows 0-7
├────────────────────────────────┤
│         Page 1 (8px tall)       │  ← rows 8-15
├────────────────────────────────┤
│         Page 2 (8px tall)       │  ← rows 16-23
├────────────────────────────────┤
│              ...                │
├────────────────────────────────┤
│         Page 7 (8px tall)       │  ← rows 56-63
└────────────────────────────────┘  ← y=64
```

==The **core concept**==
- An Arduino uno has about 2kb (2048bytes) of SRAM
- All of the 8pages is not necessarily needed to be stored in arduino's SRAM like how the`_F_` frame buffer variant does.
- It can be done page by page.
  `_1_` — stores and send 1 page at a time from SRAM to OLEDs GDDRAM
  `_2_` — stores and send 2 pages at a time from SRAM to OLEDs  GDDRAM

| Variant | Pages at a time                | Bits       | Bytes     | Compared to `_F_` |
| :-----: | ------------------------------ | ---------- | --------- | ----------------- |
|   `1`   | 1 page (8pixel row)            | 1,024 bits | 128bytes  | 8x smaller        |
|   `2`   | 2pages at a time (16pixel row) | 2,048 bits | 256 bytes | 4x smaller        |
|   `F`   | Full 8 pages                   | 8,192 bits | 1024byes  | baseline          |
If a microcontroller like uno has only like 2048bytes of SRAM and the u8g2 uses 1024bytes as framebuffer variant. It is now requiring 50% which eventually leaves no headroom for other components. 
See the [[dht22-u8g2-conflict-debugging]] for this particular issue.

###### ==How sending data works when not using the full frame buffer==
- Unlike using `_F_`, that uses `sendBuffer()` and `clearBuffer()` and sends the whole dump of data
- using the `_1_` or  `_2_` uses different syntax: [[Code_Artifacts#==Simple OLED Display but not using full frame buffer==|Read here ]]

Supposed :
```c++
void loop(){
  //oled  page buffer
  oled.firstPage();
  do{
    oled.setFont(u8g2_font_4x6_tr);
    oled.drawStr(6, 16, "Hello world");
  }while (oled.nextPage());
}
```
- The u8g2 lib checks what page does the text scopes into based on `y coordinates`
	- If the text is outside of rows 10-18(for example), and the current page in memory is Page 0(0-7pixel/row)— it ==`auto-skips`== it
	- In next loop (Page 1, row/pixel 8-15)— an overlap will appear and portion of the text will be written according to its coordinates.
	- It does this to all 8pages one page at a time

##### ==setFont()==
> Official font list page:https://github.com/olikraus/u8g2/wiki/fntlistallpic

Pattern is : 
```
(u8g2_font_<name>_<purpose><charset>)
```


```c
oled.setFont(u8g2_font_ncenB08_tr);
```
- `ncen` — the font family name
- `B` — bold
- `08` — 8px height
- `t` — transparent
- `r` — ASCII range 32-127

==suffixes==

|     |                                                                                                  |
| --- | ------------------------------------------------------------------------------------------------ |
| `f` | The font includes up to 256 glyphs.                                                              |
| `r` | Only glyphs on the range of the ASCII codes 32 to 127 are included in the font.                  |
| `u` | Only glyphs on the range of the ASCII codes 32 to 95 (uppercase chars) are included in the font. |
| `n` | Only numbers and extra glyphs for writing date and time strings are included in the font.        |
| ... | Other custom character list.                                                                     |

```
Screen : 128 x 64 pixels (entire) canvas)

┌─────────────────────────────┐  ← y=0
│                             │
│   "23.5°C"  (font 16px)     │  ←  ~16px tall each line
│                             │
│   "Humidity: 65%"           │  ← you can still fit 2-3 lines of 16px 
│                             │
└─────────────────────────────┘  ← y=64
```


---
##### ==drawStr()==

To display strings.
- refer to [[#==setCursor()==]] to how to print integers
- In order to display `int` or `float`. Convert it first to string
	- [[Study&Learn_Section#==Convert int value into string==]]
	- [[Study&Learn_Section#==Convert float into string==]]


 ```c
 drawStr(x, y text)
 ``` 
- `y` — think of it as a platform where the text will sit
- `x` — think of it as a line that pushes the text to the right
```c
oled.drawStr(0, 16, "text");
```

==To center the text relative to y axis==
- for a 16px height text
- the display total height is 64pixels
```c
oled.drawStr(0, 40, "A");
```
- The math stars with `64/2=` 32 as the baseline where the text sits
- Then get the half value of the text height `16/2` = 8
- `32 + 8` = 40 


==To center a text relative to x axis==
Suppose the total width of the display is 128pixel

--1. Get the text width using the function
==`getStrWidth(text)`==
```c
const char* text = "ABCDEFGH";
int textWidth = oled.getStrWidth(text);
```

--2. Calculate the remaining space 
then divide it by 2
```c
int x_value = (128 - textWidth) / 2;
```

--3. Set it as the value for the x:
```c
oled.drawStr(x_value, 44, text);
```

---

##### ==setCursor()==
`u8x8-compatibility style` — much simpler not U8g2 style
- Explicitily state where to start writing
- Combined with ==`print()`== to display integers
- Because a regular  [[#==drawStr()==|`drawStr()`]]  only display strings.
```c
int number = 1000;
oled.setCursor(x, y);
print(number);
```

---
##### ==drawXBMP();==

Reference code: [[Code_Artifacts#OLED + bitmap array]]

```c
oled.drawXBMP(x, y, width, height, bitmap_name);
```
- doesnt use [[#==setFont()==]] because it already uses the bitmap array for pixel placement. 
- doesnt use [[#==drawStr()==]]
- commonly used for basic animation

Memory usage:
- Uno R3 has about 32kb of flash memory
- And in display each pixel is about 1bit of memory. If the display is 128x64, its about 1024byte(1kb) or memory each frame

---

#### ==🟢**dht sensor library**==
*Note*: not the universal library
A library for dht 11, 22, etc  by Adafruit

Basic setup:
```c++
#include <Arduino.h>
#include <DHT.h>

// DHT22
int dhtpin = 3;
#define DHTTYPE DHT22
DHT dht(dhtpin, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
}

void loop() {
  float temp = dht.readTemperature();
  float humid = dht.readHumidity();
  float heatIndex = dht.computeHeatIndex(temp, humid);

  Serial.print("Temperature: ");
  Serial.print(temp);
  Serial.print(" C");

  
  Serial.print(" | Humidity: ");
  Serial.print(humid);
  Serial.print(" %");


  Serial.print(" | Heat Index: ");
  Serial.print(heatIndex);
  Serial.println(" °C");

  Serial.println("____________");

  delay(2000);
}
```

---
#### ==🟢**MQUnifiedSensor**==
A library by  Miguel Califa

[[Code_Artifacts#==MQ-2 Warm Up monitoring==]]
[[Code_Artifacts#==MQ-2 Setup==]]

##### ==🟢**getVoltage()**==
Sample use: [[MQ-2#==MQ-2 Setup Voltage Reading==]]

Used to get the output voltage of a sensor in volts. 
Its like from [[Study&Learn_Section#**==Analog To Digital (ADC)==**|ADC]] back to analog Voltage.

##### ==🟢**calibrate()**==
Sample use: [[MQ-2#==MQ-2 Setup Calibration==]]

Used to estimate the resistance of a sensor in `R0`
- `Rs` — resistance of sensor in current conditon 
- `R0` — reference or will serve as a ruler
Example:
```c++
#define RatioMQ2CleanAir 9.83
float lecture = gas_sensor.calibrate(RatioMQ2CleanAir);
```
The value is reads in ``kΩ``

**==🔴The purpose==** is to have reference point to interpret the next sensor reading.
Example:
```
- Calibrated `R0` = 2.8 kΩ
- Current `Rs` = 1.4 kΩ
- Therefore, `Rs / R0 = 0.5`
```
The ratio `0.5` means — the current sensor resistance reached half of the sensor reference resistance.
```
Reference(R0)
0------------------------------2.8k
               |
            1.4k(Current resistance)
```

==🔴**Overall process:**==
`Voltage → Rs → R0 through calibration → Rs / R0 → Estimated gas concentration`
**Key idea:** `R0` is not the gas reading itself. It is the reference value used to help interpret future readings.

##### ==🟢**Rs/R0: The purpose**==

In some sample codes at [[MQ-2]]— the `RatioMQ2CleanAir = 9.83` is  an assumption that the ratio of the sensor resistance to clean air is `9.83` 

==🔴Ratio (Rs/R0)    General Interpretation==
**Important:** The interpretation of `Rs/R0` depends on the target gas and its calibration curve.
 - `Rs/R0` represents the sensor's current resistance relative to its reference resistance.
 - `A lower ratio ` — may indicate a higher concentration of a particular target gas.
 - `A higher ratio ` — may indicate a lower concentration of that target gas.

 - The relationship and conversion to ppm depend on the target gas and its calibration model.

==**Note:** ==The MQ-2 responds to multiple gases and cannot identify a specific gas from the ratio alone.

---

##### **==🟢setRegressionMethod() & setA() and setB()==**

Used to set a mathematical model that the library used to convert the `Rs/R0)` raation into an estimated PPM.
```c++
void setup() {
  //Set math model to calculate the PPM concentration and the value of constants
  MQ2.setRegressionMethod(1); //_PPM =  a*ratio^b
  MQ2.setA(574.25); MQ2.setB(-2.222); // Configure the equation to to calculate LPG concentration
  /*
    Exponential regression:
    Gas    | a      | b
    H2     | 987.99 | -2.162
    LPG    | 574.25 | -2.222
    CO     | 36974  | -3.109
    Alcohol| 3616.1 | -2.675
    Propane| 658.71 | -2.168
  */
```

Equation: 
```
PPM = a(Rs / R0 ) ^b
```


---


#### ==🟢**SCD41** ==
Learn about SCD41 sensor: [[Components and Modules#==🟡SCD41 Co2 Sensor==]]
See the initial setup: [[SCD41]]
##### ==startPeriodicMeasurement()==

A function tell to sensor to start to measure continuously
- Only called **once**
```c
void setup(){
  Serial.begin(115200);
  Wire.begin();
  co2_sensor.begin(Wire, SCD41_I2C_ADDR_62);
  co2_sensor.startPeriodicMeasurement();
}
```

##### ==getDataReadyStatus()==

A method to check if there is new measurement data that can be read
##### ==readMeasurement()==

A method to get the latest CO2 reaading, temperature and humidity reading.

#### **==🟢ESP32—Wifi.h==**
A Class that contains lots of method/functions provided in ESP32 Arduino framework
Located typically at : 
==🟣`C:\Users\<user>\AppData\Local\Arduino15\packages\esp32\hardware\esp32`==

##### ==🟡Wifi.begin()==
- Refer to [[001Stage1_ConnectToWifi#Connecting ESP32 to Wifi]] to how to use it
- An already existing global object that the wifi.h library as part of Arduino framework
- This is the method that starts the connection process 

##### ==🟡Wifi.status()==
- A method that returns the current status of Wifi-Connection
- It returns the status 
	- `WL_CONNECTED` — means connected to Wifi
	- `WL_DISCONNECTED` — Disconnected
	- `WL_CONNECT_FAILED` — Failed to connect

#### **==🟢ESP32—WiFiClientSecure.h==**
A Class that contains lots of method/functions provided in ESP32 Arduino framework
Located typically at : 
==🟣`C:\Users\<user>\AppData\Local\Arduino15\packages\esp32\hardware\esp32`==
- Look at [[002Stage2_InternetAccess]] on how to set it up
##### ==WiFiClientSecure `<name>`==
A `Class` that serves as a blueprint for secure network connection using `TLS/SSL`
- A secure connection handler for HTTPS request of the ESP32.
- **Call it once** is enough
- Read example at : [[001Stage1_ConnectToWifi#==ESP32 Internet Access==]]
```c++
WiFiCientSecure nameOfObject;
```

##### ==setInsecure()==
A method inside the `WiFiClienSecure` class. 
- Very insecure, only do for testing
- Because, it doesnt check the certificate given by the server. Read about CA at: [[Chain_of_Trust_(Certificate_Authority)]]

##### ==useBuiltinCACertBundle()==
A method inside the `WiFiClienSecure` class. 
- No need to find CA certificates for each Public API 
- **Note**: It still depends on the availability, meaning—not all CA are listed in the bundle 

#### ==🟢**ESP32—HTTPClient.h**==
- A Class that contains lots of method/functions provided in ESP32 Arduino framework
- Located typically at : 
==🟣`C:\Users\<user>\AppData\Local\Arduino15\packages\esp32\hardware\esp32`==

- Look at : [[002Stage2_InternetAccess]] on how to set it up

##### ==HTTPClient `<name>`==
A class manage HTTP request and response
```c
HTTPClient nameOfObject;
```

##### ==.begin()==
A method to initialize
- It uses the existing object of the [[#==WiFiClientSecure `<name>`==]] as the first argument and accepts a website name for a second argument
- It is Necessary to call this method again if want to get info at interval rate. Use [[Study&Learn_Section#==**millis()**==|millis()]] for that approach
```c
http.begin(client, "https://example.com")
```
Read the basic example at: [[002Stage2_InternetAccess]]

##### ==.GET()==
A method of `HTTPClient` that sends `HTTP GET` request
- A method that returns error code in an `int` data type
	- `HTTPClient error` — `-1` to `-11` 
	- `HTTP response status code` — like `302`, `200`, `404`
Read example at : [[002Stage2_InternetAccess]]

##### ==getString()==
A method of `HTTPClient` 
- Returns `string` containing the response body from the server
- For example, read at : [[002Stage2_InternetAccess]]

>Note: 
>*The server decides on what type of string content it will return to the client.*
>*Request to a typical website returns a typical html body.But, Request to dedicated API endpoint may return other types of data like JSON*