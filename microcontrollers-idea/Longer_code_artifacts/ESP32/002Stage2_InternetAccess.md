#### ==🟡ESP32: Basic HTTP GET==

Methods  and Class used:
- [[Libraries#**==🟢ESP32—WiFiClientSecure.h==**]]
	- [[Libraries#==WiFiClientSecure `<name>`==]]
	- [[Libraries#==setInsecure()==]]
	- [[Libraries#==useBuiltinCACertBundle()==]]
- [[Libraries#==🟢**ESP32—HTTPClient.h**==]]
	- [[Libraries#==.begin()==]]


```c++
#include <Arduino.h>
#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <HTTPClient.h>


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

    Serial.println("\nWi-Fi connected!");
    Serial.println(WiFi.localIP());

    // HTTPS client for this test
    // HTTPS client for this test
    WiFiClientSecure client;
    client.setInsecure(); // Temporary test only; skips certificate verification
  
    HTTPClient http;
    Serial.println("\nSending HTTP GET request...");

    if (http.begin(client, "https://example.com")) {
        int httpCode = http.GET();
        Serial.print("HTTP status code: ");
        Serial.println(httpCode);

        if (httpCode > 0) {
            String response = http.getString();
            Serial.println("Server response:");
            Serial.println(response);
        } else {
            Serial.print("Request failed: ");
            Serial.println(http.errorToString(httpCode));
        }
        http.end();

    } else {
        Serial.println("Could not initialize HTTP client.");
    }
}

void loop() {
 //loop code goes here
}
```

Replace the ==`setInsecure`== with [[Libraries#==useBuiltinCACertBundle()==| useBuiltinCACertBundle]] if want to use a secure method 