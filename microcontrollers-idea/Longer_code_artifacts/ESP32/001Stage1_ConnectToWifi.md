


#### ==🟢Connecting ESP32 to Wifi==
- [[Libraries#==🟡Wifi.begin()==]]
- [[Libraries#==🟡Wifi.status()==]]

```C++

#include <Arduino.h>
#include <WiFi.h>

const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";

void setup() {
    Serial.begin(115200);
    WiFi.begin(ssid, password);
    Serial.print("Connecting to Wi-Fi");
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
    }

    Serial.println();
    Serial.println("Connected!");

    Serial.print("IP address: ");
    Serial.println(WiFi.localIP());
}

void loop() {
}
```

---

