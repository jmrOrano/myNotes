#### ESP32 Wifi
The `.h` files for esp32 board packages located at:
==🔴`C:\Users\<user>\AppData\Local\Arduino15\packages\esp32\hardware\esp32`==

Learning this is crucial and can be done in stages
==🟢__Stage 1:Wi-Fi Connection==
- *Connecting to router to know connection status and Ip Address*
- Uses the [[Libraries#==🟡Wifi.begin()==|Wifi.begin()]] and [[Libraries#==🟡Wifi.status()==|Wifi.status()]] method

==🟡__Stage 2: Internet Access==
- *Testing HTTP Request to know how the ESP32 communicate in a server*
- Can do the ff:
	- Get data from the internet
	- Send sensor reading to a server
	- Send a notification
	- Become a webclient or webserver

==🔴__Stage 3: Local Web server==
- *An option to make ESP32 as a server to access it using simple webpage*

==🟣__Stage 4: Data Transmission==
- *Learns how to deliver sensor rading to other device or server*
- Critical at Phase 05 at [[03 Phases| Room Climate logger]]

