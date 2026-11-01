---
tag: study
---
>[!Contents]-
>- [[#===Millis with PMW===]]
>- [[#==Adding a function to call==]]
>- [[#==simple button and LED==]]
>- [[#**==Simple potentiometer read==**]]
>- [[#==Potentiometer plus led brightness==]]
>- [[#==Potentiometer + LEDs + Button==]]
>- [[#==Simple OLED Display==]]
>- [[#==OLED + LED + Poteniometer + Button==]]
>- [[#==OLED + DHT22==]]
>- [[#OLED + bitmap array]]
>- [[#==OLED + bitarray in different file location==]]
>- [[#==oled + basic animation==]]
>- [[#==oled + two bit arrays + btn==]]
>- [[#==KY-037 Sound Sensor==]]

##### ===Millis with PMW===
---
- [[Study&Learn_Section#**PWM** Pulse Width Modulation]]
- [[Study&Learn_Section#==**millis()**==]]

==fade in fade out led==
```c
#include <Arduino.h>

int ledPin = 3;
unsigned long previousMillis = 0;
int brightness = 0;
int step = 3;

void setup(){
    pinMode(ledPin, OUTPUT);
}

void loop(){
    unsigned long currentMillis = millis();
    if (currentMillis - previousMillis >= 30){
        previousMillis = currentMillis;
        analogWrite(ledPin, brightness);
        brightness += step;  //if value is nagative. Its 255+ -3
    }
    if (brightness >= 255){
        brightness = 255;
        step = -3;  //set the value to negative
    }
    if (brightness <= 0){
        brightness = 0;
        step = 3;  //reset the value to 3 
    }
}
```

---


##### ==Adding a function to call==
---
A fade in fade out led with added logic for a secondLED
With parameters and arguments included:
```c
#include <Arduino.h>
int ledPin = 3;
int secondLedPin = 8;
unsigned long previousMillis = 0;
int brightness = 0;
int step = 3;

//int pin and state is the paramter that acts as placeholder
void secondLed(int pin, int state){  
    digitalWrite(pin, state);
}

void setup(){
    pinMode(ledPin, OUTPUT);
    pinMode(secondLedPin, OUTPUT);
}

void loop(){
    unsigned long currentMillis = millis();
    if (currentMillis - previousMillis >= 30){
        previousMillis = currentMillis;
        analogWrite(ledPin, brightness);
        brightness += step;
    }
    if (brightness >= 255){
        brightness = 255;
        
        //inside the () is the arguements to pass
        secondLed(secondLedPin, HIGH);  
        step = -3;
    }
    if (brightness <= 0){
        brightness = 0;
        //inside the () is the arguements to pass
        secondLed(secondLedPin, LOW);
        step = 3;
    }
}
```

---

##### ==simple button and LED==
Logic is: if btn pressed change the led state
But this one is not optimized, it lacks the what i called `loop state manipulation`
```c
#include <Arduino.h>

int inputPin = A0;
int ledPin = 7;

//booleans to set led state
int led_state = false;

void setup(){
    pinMode(inputPin, INPUT);
    pinMode(ledPin, OUTPUT);
}

void loop(){
    int status = digitalRead(inputPin);
    if (status ==  HIGH){
        led_state = !led_state;    
        digitalWrite(ledPin, led_state);
    }
}
```

2nd code:
This one is better:
```c
#include <Arduino.h>

int inputPin = A0;
int ledPin = 7;

//booleans
int led_state = false;
int btn_state = false;
  
void setup(){
    pinMode(inputPin, INPUT);
    pinMode(ledPin, OUTPUT);
}

void loop(){
    int status = digitalRead(inputPin);
    if (status ==  HIGH && !btn_state){
        led_state = !led_state;    
        digitalWrite(ledPin, led_state);
        btn_state = true;
    }

    if(status == LOW){
        btn_state = false;
    }
}
```
- `btn_state = !btn_state` — It inverts the current value. If `btn_state` is `false`, it becomes `true`. On the next loop, `status == HIGH && !btn_state` becomes `true && !true`, so the `if` condition will not go through.
- `btn_state = false;` — Handles the unpressed state. It resets `btn_state` to `false`, so when the button is pressed again, `status == HIGH && !btn_state` becomes `true && !false`, allowing the `if` condition to go through.

---

##### **==Simple potentiometer read==**
```c
#include <Arduino.h>
int input_pin = A0;
unsigned long previousMillis = 0;

void setup(){
    pinMode(input_pin, INPUT);
    Serial.begin(9600);
}

void loop(){
    int read_value = analogRead(input_pin);
    unsigned long currentMillis = millis();
    if (currentMillis - previousMillis >= 300){
        Serial.println(read_value);
        previousMillis = currentMillis;
    }
}
```

---
##### ==Potentiometer plus led brightness==
- Uses the value of from potentiometer 
- then feed to the led [[Study&Learn_Section#==**PWM** Pulse Width Modulation==|PWM]] pin as brightness:
```c
#include <Arduino.h>

//PINS
int pot = A0;
int green_led = 6; //uses PMW 

void setup(){
    pinMode(green_led, OUTPUT);
    pinMode(pot, INPUT);
}

void loop(){
    //read the potentiometer value
    int value = analogRead(pot);
    int brightness = value / 4;

    //adjust the led according to potentiometer value
    analogWrite(green_led, brightness);
}
```
In this code the `int brightness` is important.
- because the `value` from potentiometer ranges from `0-1023`. 
- But, a PMW pins only has max value of `0-255`
- Hence to map it correctly from potentiometer to PMW pin, the `1024 / 4 = 256`  maps accordingly

==2nd Iteration:==
- The 2nd iteration uses only digitalWrite but uses the value of the potentiometer to set conditions
- It measures the range not the threshhold
```c
  
#include <Arduino.h>

//OUTPUT PINS
int green_led = 3;
int yellow_led = 5;
int red_led = 6;

//INPUT PINS
int pot = A0;

void setup(){
    pinMode(green_led, OUTPUT);
    pinMode(yellow_led, OUTPUT);
    pinMode(red_led, OUTPUT);
    pinMode(pot, INPUT);

}

void loop(){
    //read the potentiometer
    int value = analogRead(pot);
    //if conditional that sets the led to HIGH depending on the value
    // 1024 /  3 is 341
    
    //1st condition to turn LED green only range at 0-342
    if (value <= 341){
        digitalWrite(green_led, HIGH);
    } else {
        digitalWrite(green_led, LOW);
    }
	//2nd condition is to turn yellow led only at range 342 to 682
    if (value <= 682 && value >=342){
        digitalWrite(yellow_led, HIGH);    
    }else{
        digitalWrite(yellow_led,LOW);
    }
    
    //third condition is to turn red led only at range 683 to 1023
    if (value <= 1023 && value >= 683){
        digitalWrite(red_led, HIGH);
    }else{
        digitalWrite(red_led,LOW);
    }

}
```

==3rd iteration==
- This one checks the threshhold/limit rather than range:
```c
#include <Arduino.h>
//OUTPUT PINS
int green_led = 3;
int yellow_led = 5;
int red_led = 6;

  
//INPUT PINS
int pot = A0;
  
void setup(){
    pinMode(green_led, OUTPUT);
    pinMode(yellow_led, OUTPUT);
    pinMode(red_led, OUTPUT);
    pinMode(pot, INPUT);
}

void loop(){
    //read the potentiometer
    int value = analogRead(pot);
    
    //if conditional that sets the led to HIGH depending on the value
    // 1024 /  3 is 341
    if (value >= 341) {
        digitalWrite(green_led, HIGH);
    } else {
        digitalWrite(green_led, LOW);
    }
    
    if (value >= 682) {
        digitalWrite(yellow_led, HIGH);
    } else {
        digitalWrite(yellow_led, LOW);
    }
  

    if (value >= 1023) {

        digitalWrite(red_led, HIGH);

    } else {

        digitalWrite(red_led, LOW);

    }

  

}
```

---

##### ==Potentiometer + LEDs + Button==


---
Simulating a danger level wherein you cannot turn the led as long as the value of the potentiometer is not low enough:
```c
#include <Arduino.h>

//OUTPUT PINS
int green_led = 3;
int yellow_led = 5;
int red_led = 6;


//INPUT PINS
int pot = A0;
int btn = A1;

  
//booleans
int in_danger = false;
int led_g = false;
int led_y = false;
int led_r = false;

  
void setup(){
    pinMode(green_led, OUTPUT);
    pinMode(yellow_led, OUTPUT);
    pinMode(red_led, OUTPUT);
    pinMode(pot, INPUT);
    pinMode(btn, INPUT);

}

void loop(){
    //read the potentiometer
    int value = analogRead(pot);
    
    //if conditional that sets the led to HIGH depending on the value
    // 1024 /  3 is 341
  
    if (!in_danger){  //starts with normal
        if (value >= 341) {
        digitalWrite(green_led, HIGH);
        } else {
            digitalWrite(green_led, !LOW);
        }

        if (value >= 682) {
            digitalWrite(yellow_led, HIGH);
        } else {
            digitalWrite(yellow_led, LOW);
        }

        if (value >= 1023) {
            digitalWrite(red_led, HIGH);
        } else {
            digitalWrite(red_led, LOW);
        }
    }

    int btn_state = digitalRead(A1);
    
    if(btn_state == HIGH && !in_danger){
    //turn led off potentiometer is high enough.
        if (value >= 1023){         
            digitalWrite(green_led, LOW);
            digitalWrite(yellow_led, LOW);
            digitalWrite(red_led, LOW);
            in_danger = true;  
            //set the danger to true 
            //so that the first conditional statement will be read as  "false, hence no going inside the check
        }
    }

    if(btn_state == HIGH && value <= 682 && in_danger){  //will only go through if the value of "in_danger" is true
        in_danger = false;
        //revert back to false
        //with this——the first block of the "if conditional" will now read as true
    }
}
```


---

##### ==Simple OLED Display==
- Using 1.3inch 128x64 OLED display
- [[Communication Protocols#==I 2C (Inter-Integrated Circuit)==|I2C communication]]
- Using U8g2 library by oliver
```c
#include <Arduino.h>
#include <U8g2lib.h>

U8G2_SH1106_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

void setup(){
    oled.begin();    //initialize the hardware
    oled.clearBuffer();
    oled.setFont(u8g2_font_ncenB08_tr);
    oled.drawStr(0, 15, "Hello OLED!");
    oled.sendBuffer();
}

void loop(){

}
```
==Object creation breakdown==
- `Type`  :  U8G2_SH1106_128X64_NONAME_F_HW_I2C
	- `SH1106` — the controller
	- `128X64` — the display resolution
	- `NONAME` — the display variant/profile
	- `F` — the buffer type
		- `Full frame buffer` —  "keeps the full display framebuffer in RAM" . Requires more RAM (128x64=8192 bits = 1024kb) 
		- Read more about [[Libraries#==Choosing Correct Frame Buffer==|Choosing the proper frame buffer]]
	- `HW_I2C`— communication method
		- `HW` — means the hardware will handle the communication
		- `SW` — if its SW, means the software will handle the communication
- `Variable Name` : oled
- `Constructor` : U8G2_R0
	- `R2` — Rotation and 0 degrees

The way this works in abstract level is:
```c
#include <U8g2lib.h>                               // 1. import
U8G2_SH1106_128X64_NONAME_F_HW_I2C oled(U8G2_R0);  // 2. object

void setup() {
    oled.begin();                                   // 3. initialize

    oled.clearBuffer();                             // 4. use methods
    oled.setFont(...);
    oled.drawStr(0, 15, "Hello");
    oled.sendBuffer();
}
```
> `Import library` ——> `Create/configure object` ——> `Initialize object` ——> `Use its common methods` ——> `Update/read as needed` 

----

##### ==Simple OLED Display but not using full frame buffer==

Why not use full frame buffer sometimes:
- [[dht22-u8g2-conflict-debugging]]
- [[Libraries#==Framebuffer==]]
- [[Libraries#==Choose the correct framebuffer==]]

```c++
#include <Arduino.h>
#include <U8g2lib.h>

U8G2_SH1106_128X64_NONAME_1_HW_I2C oled(U8G2_R0);  //uses _1_ instead of _F_

void setup(){
  oled.begin();
}

void loop(){
  //oled  page buffer
  oled.firstPage();
  do{
    oled.setFont(u8g2_font_4x6_tr);
    oled.drawStr(6, 16, "Hello world");
  }while (oled.nextPage());
}
```

---
##### ==OLED + LED + Poteniometer + Button==
A application of [[#==Potentiometer + LEDs + Button==]] but with OLED

Used functions :
- [[Libraries#==setFont()==]]
- [[Libraries#==drawStr()==]]
- [[Study&Learn_Section#==Convert float into string==]]

```c
#include <U8g2lib.h>
  
int green = 3;
int yellow = 5;
int red = 6;
int pot = A0;
int btn = A1;

int scl = A5;
int sda = A4;

U8G2_SSD1306_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

//BOOL
bool danger_state = false;

//helper function to convert number to string
char* intoStr(int value){
  static char buf[5];
  sprintf(buf,"%d",value);
  return buf;
}

//helper  function to get x value to center a text:
int getX(char* str){
  int textWidth = oled.getStrWidth(str);
  int x_value = (128 - textWidth) / 2;
  return x_value;
}

void setup() {
  // put your setup code here, to run once:
  pinMode(green, OUTPUT);
  pinMode(yellow, OUTPUT);
  pinMode(red, OUTPUT);
  pinMode(pot, INPUT);
  pinMode(btn, INPUT);

  //initialize the oled
  oled.begin();
  oled.clearBuffer();
  
  //init the serial
  Serial.begin(9600);

}

void loop() {

  //adding clearbuffer
  oled.clearBuffer();

  //read potentiometer
  int value = analogRead(pot) / 4;

  //start in normal state
  if(!danger_state){
    //adding a threshhold logic at 85 value each
    if(value >= 0){
      digitalWrite(green, HIGH);
    }else{
      digitalWrite(green, LOW);
    }
    if(value >= 85){
      digitalWrite(yellow, HIGH);
    }else{
      digitalWrite(yellow, LOW);
    }
    if(value >=170){
      digitalWrite(red, HIGH);
    }else{
      digitalWrite(red, LOW);
    }
}

  //logic for lock the LED state
  if(digitalRead(btn) == HIGH && value >= 255){
    digitalWrite(green, LOW);
    digitalWrite(yellow, LOW);
    digitalWrite(red, LOW);
    danger_state = true;
  }
  //logic for unlock state
  if(digitalRead(btn) == HIGH && value <= 170){
    danger_state = false;
  }

  //display the text reading value
  oled.setFont(u8g2_font_6x10_tr);
  oled.drawStr(2, 8, "Reading Value: ");

  //displaying the value of the potentiometer
  oled.setFont(u8g2_font_t0_16_tr);
  char* value_text = intoStr(value);
  int x_value = getX(value_text);
  oled.drawStr(x_value, 30, value_text);

  //display DANGER
  if(value <= 85){
    oled.setFont(u8g2_font_t0_22b_tr);
    char* safe_text = "SAFE";
    int x_value = getX(safe_text);
    oled.drawStr(x_value, 52, safe_text);
  }else if (value >= 86 && value <= 170){
    oled.setFont(u8g2_font_t0_22b_tr);
    char* warning_text = "WARNING";
    int x_value = getX(warning_text);
    oled.drawStr(x_value, 52, warning_text);
  }else{
    oled.setFont(u8g2_font_t0_22b_tr);
    char* danger_text = "DANGER";
    int x_value = getX(danger_text);
    oled.drawStr(x_value, 52, danger_text);
  }

  oled.sendBuffer();
}

```


##### ==OLED + DHT22==

Read about:
- [[Libraries#**==U8g2==**]]
- [[Libraries#==Choosing Correct Frame Buffer==]]
- [[Libraries#==How sending data works when not using the full frame buffer==]]
- [[Libraries#==**dht sensor library**==]]
- [[Study&Learn_Section#==Convert float into string==]]

```c++
#include <Arduino.h>
#include <DHT.h>
#include <U8g2lib.h>


#define DHTPIN 3
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

U8G2_SH1106_128X64_NONAME_2_HW_I2C oled(U8G2_R0);

//helper function to convert
char* float_to_str(float value){
  static char buf[8];
  dtostrf(value, 1, 2, buf);
  return buf;
}

void setup(){
  dht.begin();
  oled.begin();
  pinMode(DHTPIN, INPUT);
}
void loop(){
  float temp = dht.readTemperature();
  float humidity = dht.readHumidity();
  float heat_index = dht.computeHeatIndex(temp, humidity, false);
  //oled  page buffer
  oled.firstPage();
  do{
    oled.setFont(u8g2_font_t0_22b_tr);
    oled.drawStr(5, 22, float_to_str(temp));
    oled.drawStr(4, 59, float_to_str(humidity));
    oled.drawStr(67, 40, float_to_str(heat_index));
  }while (oled.nextPage());
  delay(1000);
}
```

==Iteration 2==
The display is too small, so in this iteration, the font is bigger and the each information is displayed at an interval:
```c++
#include <Arduino.h>
#include <DHT.h>
#include <U8g2lib.h>
  
//variables for swtitching
int currentDisplay = 0;
unsigned long lastSwitch = 0;

#define DHTPIN 3
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

U8G2_SH1106_128X64_NONAME_2_HW_I2C oled(U8G2_R0);

//helper functions to convert
char* float_to_str(float value){
  static char buf[8];
  dtostrf(value, 1, 2, buf);
  return buf;
}
//helper function to center a text on x axis
int x_value(char* text){
  int textWidth = oled.getStrWidth(text);
  int x = (128 - textWidth) /2;
  return x;
}

void setup(){
  dht.begin();
  oled.begin();
  pinMode(DHTPIN, INPUT);
}

void loop(){
  float temp = dht.readTemperature();
  float humidity = dht.readHumidity();
  float heat_index = dht.computeHeatIndex(temp, humidity, false);
  
  //timing interval
  if(millis() - lastSwitch >= 2000){
    currentDisplay = (currentDisplay + 1) % 3; //uses modulo to swtich value [0%3=0, 1%3=1, 2%3=2, 3%3=0]
    lastSwitch = millis();
  }

  //oled  page buffer
  oled.firstPage();
  do{
    oled.setFont(u8g2_font_fub30_tr);
    //swtiching
    if(currentDisplay ==  0){
      char* temp_string = float_to_str(temp);
      int x = x_value(temp_string);
      oled.drawStr(x, 57, temp_string);
    } else if (currentDisplay == 1){ 
      char* humidity_string = float_to_str(humidity);
      int x = x_value(humidity_string);
      oled.drawStr(x, 59, humidity_string);
    } else {
      oled.setFont(u8g2_font_timR24_tr);
      char* heatIndex_string = float_to_str(heat_index);
      int x = x_value(heatIndex_string);
      oled.drawStr(x, 40, heatIndex_string);
    }
  }while (oled.nextPage());
}
```

---
##### ==OLED  + bitmap array==
- read about [[Libraries#==drawXBMP();==]]

Display a qrcode
```c
#include <Arduino.h>
#include <U8g2lib.h>

U8G2_SH1106_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

const unsigned char qrBitmap[] PROGMEM = { 0xff,0x3f,0xfc,0x33,0xf3,0xff,0x03,0xff,0x3f,0xfc,0x33,0xf3,0xff,0x03,0x03,0x30,0xcf,0x3c,0x33,0x00,0x03,0x03,0x30,0xcf,0x3c,0x33,0x00,0x03,0xf3,0x33,0x33,0xf3,0x33,0x3f,0x03,0xf3,0x33,0x33,0xf3,0x33,0x3f,0x03,0xf3,0x33,0xc3,0xcf,0x33,0x3f,0x03,0xf3,0x33,0xc3,0xcf,0x33,0x3f,0x03,0xf3,0x33,0x0c,0x0c,0x33,0x3f,0x03,0xf3,0x33,0x0c,0x0c,0x33,0x3f,0x03,0x03,0x30,0xf0,0x0c,0x33,0x00,0x03,0x03,0x30,0xf0,0x0c,0x33,0x00,0x03,0xff,0x3f,0x33,0x33,0xf3,0xff,0x03,0xff,0x3f,0x33,0x33,0xf3,0xff,0x03,0x00,0x00,0xc3,0xc3,0x03,0x00,0x00,0x00,0x00,0xc3,0xc3,0x03,0x00,0x00,0x03,0x30,0xff,0x3c,0x3c,0xfc,0x00,0x03,0x30,0xff,0x3c,0x3c,0xfc,0x00,0xcc,0x0f,0x0f,0xff,0xcc,0xff,0x00,0xcc,0x0f,0x0f,0xff,0xcc,0xff,0x00,0xfc,0x3f,0x3c,0xf3,0xff,0xcc,0x03,0xfc,0x3f,0x3c,0xf3,0xff,0xcc,0x03,0xc0,0x00,0x00,0xf3,0x30,0x0f,0x03,0xc0,0x00,0x00,0xf3,0x30,0x0f,0x03,0x3f,0xf0,0x00,0xc3,0xf3,0x00,0x03,0x3f,0xf0,0x00,0xc3,0xf3,0x00,0x03,0x0f,0x0c,0x30,0xf3,0xc3,0xc0,0x00,0x0f,0x0c,0x30,0xf3,0xc3,0xc0,0x00,0xc3,0x3f,0x33,0xcc,0xc0,0xcf,0x03,0xc3,0x3f,0x33,0xcc,0xc0,0xcf,0x03,0x33,0xc3,0xf0,0xf0,0xf0,0x33,0x03,0x33,0xc3,0xf0,0xf0,0xf0,0x33,0x03,0x03,0x3f,0xfc,0x33,0xff,0x33,0x00,0x03,0x3f,0xfc,0x33,0xff,0x33,0x00,0x00,0x00,0xc3,0x0c,0x03,0x33,0x00,0x00,0x00,0xc3,0x0c,0x03,0x33,0x00,0xff,0x3f,0xcc,0x03,0x33,0x0f,0x03,0xff,0x3f,0xcc,0x03,0x33,0x0f,0x03,0x03,0x30,0xf0,0x30,0x03,0x03,0x00,0x03,0x30,0xf0,0x30,0x03,0x03,0x00,0xf3,0x33,0xc0,0xff,0xff,0x3f,0x00,0xf3,0x33,0xc0,0xff,0xff,0x3f,0x00,0xf3,0x33,0xcc,0x00,0x3c,0xcc,0x03,0xf3,0x33,0xcc,0x00,0x3c,0xcc,0x03,0xf3,0x33,0x30,0xcc,0xcc,0x30,0x03,0xf3,0x33,0x30,0xcc,0xcc,0x30,0x03,0x03,0x30,0x0c,0x03,0xff,0x03,0x03,0x03,0x30,0x0c,0x03,0xff,0x03,0x03,0xff,0x3f,0xcf,0x03,0x33,0x0c,0x03,0xff,0x3f,0xcf,0x03,0x33,0x0c,0x03};
  
void setup(){
  oled.begin();
}

void loop(){
  oled.clearBuffer();
  oled.drawXBMP(40, 6, 50, 50, qrBitmap);
  oled.sendBuffer();
}
```

----
##### ==OLED + bitarray in different file location==
In PIO, you can store the bit array in different `.h` file so you can call it in `main.cpp` file. For cleaner code.

- read about [[Libraries#==drawXBMP();==]]

**Structure:**
```
study-practice/
│
├── include/
│   └── qrcocde.h
│
├── src/
│   └── main.cpp
│
└── platformio.ini
```

qrcode.h 
```c
//include guard
#ifndef QRCODE_H
#define QRCODE_H
#include <Arduino.h>

const unsigned char qrBitmap[] PROGMEM = {
	//arrays
	}
#endif
```
- `#ifndef QRCODE_H` and `define QRCODE_H` — called an `include guard` to prevent contents from being included more than once in same compilation

main.cpp
```c
//setup
#include <Arduino.h>
#include <U8g2lib.h>
#include <qrcode.h>

U8G2_SSD1306_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

void setup(){
  oled.begin();
}
void loop(){
  
    oled.clearBuffer();
    oled.drawXBMP(40,6,BITMAP_ANIM_WIDTH, BITMAP_ANIM_HEIGHT, qrBitmap)
    oled.sendBuffer();
}
```

##### ==oled + basic animation==

The workflow:
--1. Find a gif at [tenor](https://tenor.com/). Download it
--2. Convert the gif using the tools at: [ezgif](https://ezgif.com/gif-to-bitmap)
	-- if the original gif is too large—resize or crop using the same tool located at "video tools"
--3. You can copy the active code  and/or  directly download the `.h` file itslef

==main.cpp==
```c
#include <Arduino.h>
#include <U8g2lib.h>
#include <bitmap_anim.h>

U8G2_SSD1306_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

void setup(){
  oled.begin();
}

void loop(){
  for (int frame = 0; frame < BITMAP_ANIM_FRAME_COUNT; frame++){
    oled.clearBuffer();
    oled.drawXBMP(0,0,BITMAP_ANIM_WIDTH, BITMAP_ANIM_HEIGHT, (const uint8_t*)pgm_read_ptr(&(bitmap_anim_frames[frame])));
    oled.sendBuffer();
    delay(bitmap_anim_delays[frame]); //array indexing to the value of delay inside the bitmap_anim.h 
  }

}
```

==bitmap_anim.h==
>[!bitmap.anim_h]- bitmap.anim.h
>```c
// ==========================================================================
// Bitmap animation generated by ezgif.com/gif-to-bitmap
// Dimensions: 128 x 64 px | Frames: 8 | Total Size: 8 KB (8192 bytes)
// Format: MONO (XBM)
>
// ==========================================================================
#ifndef BITMAP_ANIM_H
#define BITMAP_ANIM_H
#include <avr/pgmspace.h> // Arduino AVR Flash memory support
#define BITMAP_ANIM_WIDTH  128
#define BITMAP_ANIM_HEIGHT 64
#define BITMAP_ANIM_FRAME_COUNT 8
#define BITMAP_ANIM_BYTES_PER_FRAME 1024
// Frame delays in milliseconds
const uint16_t bitmap_anim_delays[BITMAP_ANIM_FRAME_COUNT] = {
    60, 60, 60, 60, 60, 60, 60, 60
};
// Frame 0 (delay: 60 ms)
const unsigned char bitmap_anim_frame_0[] PROGMEM = {
>//arrays
};
>.............
// Frame 7 (delay: 60 ms)
const unsigned char bitmap_anim_frame_7[] PROGMEM = {
>//arrays
};
// Array of frame pointers
const unsigned char* const bitmap_anim_frames[BITMAP_ANIM_FRAME_COUNT] PROGMEM = {
    bitmap_anim_frame_0,
    bitmap_anim_frame_1,
    bitmap_anim_frame_2,
    bitmap_anim_frame_3,
    bitmap_anim_frame_4,
    bitmap_anim_frame_5,
    bitmap_anim_frame_6,
    bitmap_anim_frame_7
};
#endif // BITMAP_ANIM_H
>```

---
##### ==oled + two bit arrays + btn==
- [[Study&Learn_Section#==**millis()**==]]
- [[Study&Learn_Section#==arrays and arrays indexing==]] to understand how the value of delay is get
- [[Libraries#==drawXBMP();==]]
- [[#==OLED + bitmap array==]] to display qrcode


```c
#include <Arduino.h>
#include <U8g2lib.h>
#include <bitmap_anim.h>
#include <qrcode.h>

unsigned long previousMillis = 0;
int current_frame = 0;

//bnt read
int btn_pin = 4;
bool btn_condition = false;

//oled display
U8G2_SSD1306_128X64_NONAME_F_HW_I2C oled(U8G2_R0);
unsigned long current_display = 0;

void setup(){
  pinMode(btn_pin, INPUT);
  oled.begin();
}
void loop(){
  //reading btn input
  int btn_status = digitalRead(btn_pin);

  //change state when pressed
  if (btn_status == HIGH && !btn_condition){
    current_display = (current_display + 1) % 2;
    btn_condition = true;
  }

  if(current_display == 0){
    unsigned long currentMillis = millis();
    if (currentMillis - previousMillis >= bitmap_anim_delays[current_frame]){
      previousMillis = currentMillis;
      oled.clearBuffer();
      oled.drawXBMP(0,0,BITMAP_ANIM_WIDTH, BITMAP_ANIM_HEIGHT, (const uint8_t*)pgm_read_ptr(&(bitmap_anim_frames[current_frame])));
      oled.sendBuffer();
      current_frame++;
      if (current_frame >= BITMAP_ANIM_FRAME_COUNT){
        current_frame = 0;
      }
    }
    }
  if(current_display == 1){
      //current display 1
      oled.clearBuffer();
      oled.drawXBMP(38, 7, 50, 50, qrBitmap);
      oled.sendBuffer();
  }

  if (btn_status == LOW){
    btn_condition = false;
  }
}
```

---


##### ==KY-037 Sound Sensor==
A basic reading first:  
```c
#include <Arduino.h>

int soundPin = A0;
unsigned long previousMillis = 0;


void setup(){
  Serial.begin(9600);
  pinMode(soundPin,INPUT);
}
void loop(){
  float soundValue = analogRead(soundPin);
  unsigned long currentMillis = millis();
  if (currentMillis - previousMillis >= 500){
     Serial.println(soundValue);
     previousMillis = currentMillis;
  }
}
```
With this setup:
- can observe the value of analogRead
- can compute the voltage using the formula at [[Study&Learn_Section#**==Reference Voltage==**]]

Small exercise or project to do:
Components: LED, KY-037, 2X buttons, LED 
Visualization: O
LED is for displaying the `analogRead` from `KY-037` , the buttons are used to change the threshold of the `ky-037` , the LED is there for lighting -ofcourse


---

##### ==Using a dedicated "h file" to store functions==
Read: [[Study&Learn_Section#==function in a different file location==]]

A `modularization` method.

Include/button.h
```c++
#include<Arduino.h>
#ifndef BUTTONS_H
#define BUTTONS_H
  
//using 'extern' syntax to use the variables that is in  the main.cpp
extern int btn1;
extern int btn2;
extern bool btn1State;
extern bool btn2State;
extern int btn1Reading;
extern int btn2Reading;
extern unsigned int threshold;

void handleButtons(){
    //btn1 for decrements
    if(btn1Reading == HIGH && !btn1State){
        btn1State = true;
        threshold = threshold - 1;
        Serial.println(threshold);
    }
    //btn2 for increase
    if(btn2Reading == HIGH && !btn2State){
        btn2State = true;
        threshold = threshold + 1;
        Serial.println(threshold);
    }

    //handles unpressed btn 1 and 2
    if(btn1Reading == LOW){
        btn1State = false;
    }
    if(btn2Reading == LOW){
        btn2State = false;
    }
}
#endif  
```

main.cpp:
```c++
#include <Arduino.h>
#include <buttons.h> 

//global var for functions to use
int btn1 = 7;
int btn2 = 6;
int btn1Reading;
int btn2Reading;
bool btn1State = false;
bool btn2State = false;
unsigned int threshold = 511;
  
void setup(){
  Serial.begin(9600);
  pinMode(btn1, INPUT);
  pinMode(btn2, INPUT);
}

void loop(){
  //readings
  btn1Reading = digitalRead(btn1);
  btn2Reading = digitalRead(btn2);

  //the function for handling the buttons
  //located at include/button.h
  handleButtons();


}
```