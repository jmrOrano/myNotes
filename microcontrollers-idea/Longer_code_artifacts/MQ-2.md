##### ==MQ-2 Warm Up monitoring==
---
Using an [[#==Simple OLED Display==]] to monitor the warm-up phase of [[Components and Modules#==MQ-2 Gas Sensor==|MQ-2]]  which requires 24-48 hrs.

With integration of:
- [[Code_Artifacts#==Simple OLED Display==]]
- [[Study&Learn_Section#==Convert int value into string==]]
```c++
#include <Arduino.h>
#include  <U8g2lib.h>
  
//hardware configuration
int gas_sensor = A3;
int serial_clock = A5;
int serial_data = A4;

//object creation for oled
U8G2_SH1106_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

//function to center the string in x axis
int x_value(char* value){
  int string_width = oled.getStrWidth(value);
  int x = (128 -string_width) /2 ;
  return x;
}

//function to convert int to string
char* int_to_str(int value){
  static char buffer[5];
  sprintf(buffer, "%d", value);
  return buffer;
}

void setup(){
  Serial.begin(9600);
  oled.begin();
  pinMode(gas_sensor, INPUT);
}

void loop(){
  oled.clearBuffer();
  int reading = analogRead(gas_sensor);

  //displaying the label
  oled.setFont(u8g2_font_profont12_tr);
  char* reading_label = "Current Reading";
  int x = x_value(reading_label);
  oled.drawStr(x, 22, "Current Reading");

  //displaying the reading value
  oled.setFont(u8g2_font_profont17_tr);
  char* str_reading = int_to_str(reading);
  int value_x_position = x_value(str_reading);
  oled.drawStr(value_x_position, 49, str_reading);

  oled.sendBuffer();
}
```

---
##### ==MQ-2 Setup: Voltage Reading==
With integration of:
- [[Code_Artifacts#==Simple OLED Display==]]
- [[Study&Learn_Section#==Convert float into string==]]

From : [[Study&Learn_Section#**==Analog To Digital (ADC)==**| ADC]] back to Voltage
This setup is used for the electrical calculation of the [[Libraries#==**MQUnifiedSensor**==|MQ-2 Library]]

```c++
#include <Arduino.h>
#include  <U8g2lib.h>
#include <MQUnifiedsensor.h>

//hardware configuration
int serial_clock = A5;
int serial_data = A4;

//hardware config for MQ2
#define Board "Arduino UNO"
#define pin A3
#define Voltage_Resolution 5
#define ACD_Bit_Resolution 10
#define Type "MQ-2"
  
//object Creataion for MQ-2
MQUnifiedsensor gas_sensor(Board, Voltage_Resolution, ACD_Bit_Resolution, pin, Type);

//object creation for oled
U8G2_SH1106_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

//function to center the string in x axis
int x_value(char* value){
  int string_width = oled.getStrWidth(value);
  int x = (128 -string_width) /2 ;
  return x;
}

//function to convert int to string
char* float_to_str(float value){
  static char buffer[5];
  dtostrf(value, 1, 2, buffer);
  return buffer;

}

////////////////////////////////////////
///////////////////////////////////////

void setup(){
  Serial.begin(9600);
  oled.begin();
  gas_sensor.init();

}

void loop(){
  gas_sensor.update();
  oled.clearBuffer();

  float reading = gas_sensor.getVoltage();
  //displaying the label
  oled.setFont(u8g2_font_profont12_tr);
  char* reading_label = "Current Reading";
  int x = x_value(reading_label);
  oled.drawStr(x, 22, "Current Reading");

  
  //displaying the reading value
  oled.setFont(u8g2_font_profont17_tr);
  char* str_reading = float_to_str(reading);
  int value_x_position = x_value(str_reading);
  oled.drawStr(value_x_position, 49, str_reading);

  oled.sendBuffer();

}
```


##### ==MQ-2 Setup==
---
Using the MQ-2 Unified Library
Read more about the component: [[Components and Modules#==MQ-2 Gas Sensor==]]
```c
#include <MQUnifiedsensor.h>

// Hardware configuration
#define Board "Arduino UNO"
#define Pin A0
#define Voltage_Resolution 5
#define ADC_Bit_Resolution 10
#define Type "MQ-2"

MQUnifiedsensor gas_sensor(
  Board,
  Voltage_Resolution,
  ADC_Bit_Resolution,
  Pin,
  Type
);


void setup() {
  Serial.begin(9600);
  // LPG regression model
  gas_sensor.setRegressionMethod(1);
  gas_sensor.setA(574.25);
  gas_sensor.setB(-2.222);
  gas_sensor.init();

  // Calibrate R0
  float calcR0 = 0;
  for (int i = 0; i < 10; i++) {
    gas_sensor.update();
    calcR0 += gas_sensor.calibrate(9.83);
  }
  
  gas_sensor.setR0(calcR0 / 10);
  Serial.println("Calibration done.");
} 
void loop() {
  gas_sensor.update();
  // Calculate LPG concentration
  gas_sensor.readSensor(false);
  Serial.print("LPG: ");
  Serial.println(gas_sensor.readSensor(false));
  delay(1000);
}
```


**==The Breakdown==**

==🟢**Object Creation**==
```c++
#include <MQUnifiedsensor.h>

// Hardware configuration
#define Board "Arduino UNO"
#define Pin A0
#define Voltage_Resolution 5
#define ADC_Bit_Resolution 10
#define Type "MQ-2"

MQUnifiedsensor gas_sensor(
  Board,            
  Voltage_Resolution,
  ADC_Bit_Resolution,
  Pin,
  Type
);
```
- `Voltage Resolution` —  is the [[Study&Learn_Section#**==Reference Voltage==**|Reference Voltage]]
- `ADC_Bit Resolution` — number of bits an ADC use to represent from analog value to digital value (varied from controller to controller)

---


##### ==MQ-2 Setup: Calibration==

Uses: 
- [[Code_Artifacts#==Simple OLED Display==]]
- [[Study&Learn_Section#==Convert float into string==]]
- [[#==MQ-2 Setup==]]

```c++
#include <Arduino.h>
#include  <U8g2lib.h>
#include <MQUnifiedsensor.h>

//hardware configuration
int serial_clock = A5;
int serial_data = A4;

//hardware config for MQ2
#define Board "Arduino UNO"
#define pin A3
#define Voltage_Resolution 5
#define ACD_Bit_Resolution 10
#define Type "MQ-2"

//object Creataion for MQ-2
MQUnifiedsensor gas_sensor(Board, Voltage_Resolution, ACD_Bit_Resolution, pin, Type);

//define for the calibration
#define RatioMQ2CleanAir 9.83

//object creation for oled
U8G2_SH1106_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

//function to center the string in x axis
int x_value(char* value){
  int string_width = oled.getStrWidth(value);
  int x = (128 -string_width) /2 ;
  return x;
}
//function to convert float to string
char* float_to_str(float value){
  static char buffer[10];
  dtostrf(value, 1, 2, buffer);
  return buffer;
}

////////////////////////////////////////
///////////////////////////////////////

void setup(){
  Serial.begin(9600);
  oled.begin();
  gas_sensor.init();
}

void loop(){
  gas_sensor.update();
  oled.clearBuffer();

  //reading
  float lecture =  gas_sensor.calibrate(RatioMQ2CleanAir);
  //float reading = gas_sensor.getVoltage();

  //displaying the label
  oled.setFont(u8g2_font_profont12_tr);
  char* reading_label = "Current Reading";
  int x = x_value(reading_label);
  oled.drawStr(x, 22, reading_label);

  //displaying the reading value
  oled.setFont(u8g2_font_profont17_tr);
  char* str_reading = float_to_str(lecture);
  int value_x_position = x_value(str_reading);
  oled.drawStr(value_x_position, 49, str_reading);

  oled.sendBuffer();
}
```

##### ==MQ-2: Getting the ratio==

```c++
#include <Arduino.h>
#include  <U8g2lib.h>
#include <MQUnifiedsensor.h>

//hardware configuration
int serial_clock = A5;
int serial_data = A4;

//hardware config for MQ2
#define Board "Arduino UNO"
#define pin A3
#define Voltage_Resolution 5
#define ACD_Bit_Resolution 10
#define Type "MQ-2"

//object Creataion for MQ-2
MQUnifiedsensor gas_sensor(Board, Voltage_Resolution, ACD_Bit_Resolution, pin, Type);

//define for the calibration
#define RatioMQ2CleanAir 9.83
float calibrated_r0 =0;
  
//object creation for oled
U8G2_SH1106_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

//function to center the string in x axis
int x_value(char* value){
  int string_width = oled.getStrWidth(value);
  int x = (128 - string_width) / 2 ;
  return x;
}

//function to convert float to string
char* float_to_str(float value){
  static char buffer[10];
  dtostrf(value, 1, 2, buffer);
  return buffer;
}

////////////////////////////////////////
///////////////////////////////////////

void setup(){
  Serial.begin(9600);
  oled.begin();
  gas_sensor.init();
  gas_sensor.update();
  calibrated_r0 =  gas_sensor.calibrate(RatioMQ2CleanAir);
}

void loop(){
  gas_sensor.update();
  oled.clearBuffer();

  //reading
  float current_rs = gas_sensor.getRS();
  float result = (current_rs / calibrated_r0);
  //float reading = gas_sensor.getVoltage();

  //displaying the label
  oled.setFont(u8g2_font_profont12_tr);
  char* reading_label = "Current Reading";
  int x = x_value(reading_label);
  oled.drawStr(x, 22, reading_label);

  //displaying the R0 value
  oled.setFont(u8g2_font_profont17_tr);
  char* str_reading = float_to_str(calibrated_r0);
  oled.drawStr(77, 36, str_reading);

  //displaying the current RS value
  oled.setFont(u8g2_font_profont17_tr);
  char* string_rs = float_to_str(current_rs);
  oled.drawStr(9, 36, string_rs);

  //displaying the MATH value of RS and R0
  char* str_result = float_to_str(result);
  int x_axis = x_value(str_result);
  oled.drawStr(x_axis, 57, str_result);
  oled.sendBuffer();
}
```

---
##### Displaying the PPM (the reading)
```c
#include <Arduino.h>
#include  <U8g2lib.h>
#include <MQUnifiedsensor.h>

//hardware configuration
int serial_clock = A5;
int serial_data = A4;

//hardware config for MQ2
#define Board "Arduino UNO"
#define pin A3
#define Voltage_Resolution 5
#define ACD_Bit_Resolution 10
#define Type "MQ-2"

//object Creataion for MQ-2
MQUnifiedsensor gas_sensor(Board, Voltage_Resolution, ACD_Bit_Resolution, pin, Type);

//define for the calibration
#define RatioMQ2CleanAir 9.83
float calibrated_r0 =0;

//object creation for oled
U8G2_SH1106_128X64_NONAME_F_HW_I2C oled(U8G2_R0);

//function to center the string in x axis
int x_value(char* value){
  int string_width = oled.getStrWidth(value);
  int x = (128 - string_width) / 2 ;
  return x;
}

//function to convert float to string
char* float_to_str(float value){
  static char buffer[10];
  dtostrf(value, 1, 2, buffer);
  return buffer;
}


////////////////////////////////////////
///////////////////////////////////////

void setup(){
  Serial.begin(9600);
  oled.begin();
  gas_sensor.setRegressionMethod(1);
  gas_sensor.setA(36974);
  gas_sensor.setB(-3.109);

  gas_sensor.init();
  gas_sensor.update();
  calibrated_r0 =  gas_sensor.calibrate(RatioMQ2CleanAir);
  gas_sensor.setR0(calibrated_r0);
}

void loop(){
  gas_sensor.update();
  oled.clearBuffer();

  //reading
  float current_rs = gas_sensor.getRS();
  float result = (current_rs / calibrated_r0);
  float ppm = gas_sensor.readSensor(false, 0);
  //float reading = gas_sensor.getVoltage();

  //displaying the label
  oled.setFont(u8g2_font_profont10_tr);
  char* reading_label = "Current Reading";
  int x = x_value(reading_label);
  oled.drawStr(x, 9, reading_label);

  //displaying the R0 value
  oled.setFont(u8g2_font_profont17_tr);
  char* str_reading = float_to_str(calibrated_r0);
  oled.drawStr(87, 32, str_reading);

  //displaying the current RS value
  oled.setFont(u8g2_font_profont15_tr);
  char* string_rs = float_to_str(current_rs);
  oled.drawStr(9, 32, string_rs);

  //displaying the MATH value of RS and R0
  char* str_result = float_to_str(result);
  oled.drawStr(8, 57, str_result);

  //displaying the ppm reading
  oled.setFont(u8g2_font_5x8_tr);
  oled.drawStr(62, 58, "ppm");

  oled.setFont(u8g2_font_profont17_tr);
  char* ppm_str = float_to_str(ppm);
  oled.drawStr(80, 59, ppm_str);

  oled.sendBuffer();
}
```