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