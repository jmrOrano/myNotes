
Using the library: [[Libraries#SCD41| Sensirion_I2C_SCD4x]] by sensirion

### Setup
Uses Esp32 but with arduino framework
```c++
#include <SensirionI2cScd4x.h>
#include <Wire.h>

//object creation
SensirionI2cScd4x name_here;

void setup() {
  Serial.begin(115200);
  
  co2_sensor.begin(Wire, SCD41_I2C_ADDR_62);
  co2_sensor.startPeriodicMeasurement();
}
```

### Basic Reading
```c
#include <Arduino.h>
#include <Wire.h>
#include <SensirionI2cScd4x.h>

//object creation
SensirionI2cScd4x co2_sensor;

void setup(){
  Serial.begin(115200);
  Wire.begin();
  co2_sensor.begin(Wire, SCD41_I2C_ADDR_62);
  co2_sensor.startPeriodicMeasurement();
}
  
void loop(){
  //storage
  bool dataready = false;

  //check if new data is ready
  co2_sensor.getDataReadyStatus(dataready);
  if (dataready){
    uint16_t co2_concentration = 0;
    float temperature = 0.0;
    float relative_humidity = 0.0;
//reads
  co2_sensor.readMeasurement(co2_concentration, temperature, relative_humidity);
  Serial.print("Co2: ");
  Serial.println(co2_concentration);
  Serial.print("Temp: ");
  Serial.println(temperature);
  Serial.print("Rh: ");
  Serial.println(relative_humidity);
  }
  delay(5000);
}
```

### Basic Reading  + OLED
```c
#include <Arduino.h>
#include <Wire.h>
#include <SensirionI2cScd4x.h>
#include <U8g2lib.h>
  

//object creation
SensirionI2cScd4x co2_sensor;
U8G2_SH1106_128X64_NONAME_F_HW_I2C oled(U8G2_R0);


//FUNCTIONS
  //int to string
char* int_to_str(int int_value){
  static char int_buffer_array_size[8];
  sprintf(int_buffer_array_size, "%d", int_value);
  return int_buffer_array_size;
}
  //float to string
char* float_to_str(float float_value){
  static char float_buffer_size_array[8];
  dtostrf(float_value, 1, 2, float_buffer_size_array);
  return float_buffer_size_array;
}
  //center a string on X-axis
int x_axis_value(char* text){
  int textWidth = oled.getStrWidth(text);
  int x_axis = (128 - textWidth) / 2;
  return x_axis;
}


void setup(){
  Serial.begin(115200);
  Wire.begin(21, 22); //initialized shared I2c bus
  co2_sensor.begin(Wire, SCD41_I2C_ADDR_62);
  co2_sensor.startPeriodicMeasurement();
  oled.begin();
}
  
void loop(){
  oled.clearBuffer();
  bool dataready = false;
  //check if new data is ready
  co2_sensor.getDataReadyStatus(dataready);
  if (dataready){
    uint16_t co2_concentration = 0;
    float temperature = 0.0;
    float relative_humidity = 0.0;
//reads
    co2_sensor.readMeasurement(co2_concentration, temperature, relative_humidity);
  
    //label
    oled.setFont(u8g2_font_profont10_tr);
    oled.drawStr(26, 9, "Current Reading");
    
    //TEMPERATURE
      //label
    oled.setFont(u8g2_font_5x8_tr);
    oled.drawStr(9, 19, "Temp");
      //value
    oled.setFont(u8g2_font_profont15_tr);
    char* str_temp = float_to_str(temperature);
    oled.drawStr(9, 32, str_temp);
  
    //RELATIVE HUMIDITY
      //label
    oled.setFont(u8g2_font_5x8_tr);
    oled.drawStr(87, 19, "Rh");
      //value
    oled.setFont(u8g2_font_profont15_tr);
    char* str_relativeHumidity = float_to_str(relative_humidity);
    oled.drawStr(87, 32, str_relativeHumidity);


    //CO2 CONCENTRATION
      //label
    oled.setFont(u8g2_font_5x8_tr);
    oled.drawStr(57, 44, "CO2");
      //value
    oled.setFont(u8g2_font_profont15_tr);
    char* str_co2_concentration = int_to_str(co2_concentration);
    oled.drawStr(x_axis_value(str_co2_concentration), 60, str_co2_concentration);

    //Serial OUTPUT
    Serial.print("Co2: ");
    Serial.println(co2_concentration);
    Serial.print("Temp: ");
    Serial.println(temperature);
    Serial.print("Rh: ");
    Serial.println(relative_humidity);
  }
  oled.sendBuffer();
  delay(5000);
}
```