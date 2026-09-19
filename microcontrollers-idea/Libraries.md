
#### **==U8g2==**
---
A library for many oled displays
Check the [[Code_Artifacts#==Simple OLED Display==|code artifacts]] for example usage


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


---

#### ==**dht sensor library**==
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