---
type: idea
tags:
  - microcontrollers
  - homelab
  - sensors
status: exploring
Date Generated: August 15, 2026
---
# **The idea**
---
To build a year-long indoor microclimate dataset and investigate the relationship between my local room conditions and seasonal/weather patterns in my country. 

---
## **Source of Idea**
---
It was august. I was listening to a local weather new. There it was mentioned that *habagat*—southwest monsoon  brings a much more humid air and more rain compared to *amihan*—northeast monsoon. I have my arduino running with dht22 sensor and 16x2 lcd display and observed that it consistently display a 100%relative humidty throughout the week. So i decided to conceptualize an idea to gather my own dataset to prove a hypothesis of:
```
August has unusually high indoor relative humidity because of the Southwest Monsoon.
```

---

## **How to do: The Initial Plan**
---
The initial plan  is use the ff tools, and technologies:
- [[Tools#**Cron**|Cron]] – to perform git
- [[Git Workflow & Commands|Git]] – to serve as a backup or snapshot
- **Esp32** — a the main controller
- **Temp and Humidity Sensor** — Options are: 
	- `DHT22`, - for starter only sensor 
	- `SHT31`, 
	- `SHT35`, 
	- `BME280`, – adds atmospheric pressure
	- `BME680/BME688` – adds gas/air-quality-related measurements
- **CSV file or SQLite** – for the storage file of logs 
- A laptop that runs 24/7 — An endpoint for the data. 

---
## **Potential Questions**
---
### Should i include to log other data like the `current weather`? If yes how it should be done?
---
**Answer**: Yes, but dont make the ESP32 responsible for everything. 

**Local Sensor measures the:**
- timestamp
- temperature
- relateive humidity

**External Weather data like a dedicated server periodically  retrieves the ff:**
- timestamp
- outside temperature
- outside humidity
- rainfall
- wind speed
- weather condition
- pressure

**Then join them together using the timestamp.** 
Conceptually
```
LOCAL ROOM
ESP32
  │
  │
  ├── 28.7°C
  └── 82% RH
          │
          │ timestamp
          ▼
      DATABASE
          ▲
          │
          │ timestamp
  WEATHER API
  ├── 29.8°C
  ├── 84% RH
  ├── rain
  └── wind
```
Though this schema architecture will be at `Phase 5` for the question of [[#Can this project scale to progressively harder?]]]


---
### At what interval should i log?
---
**Answer**: Record more frequently that once per day.

One measurement per day loses a huge amount of information. A good starting point is every `5 minutes`. 
Then it can be later aggregate it into:
- hourly avg
- daily avg
- daily minimum
- monthly avg

---

### At what interval should i perform backup of the data?
---
**Answer**: ~~The sweet spot i was thinking is `Every 15 days`~~ Or so i thought.

According to AI, every 15 days is too far — if the interval logging is every 5 minutes, for 15 days that's about 4,300 readings at risk of losing.

> Recommended about 3 days max. Since storage cost is basically negligible (a few MB/year), even better to just automate the git commit **daily** via cron — no real downside to committing more often.

---

### How much storage (approximately) will this consume throughout the year?
---
**Answer**: According to AI

Suppose I record **every 5 minutes**:
```
12 readings/hour
× 24 hours
× 365 days
= 105,120 readings/year
```
If one CSV record averages ~50–80 bytes:
>**~5–8 MB/year.**

Even recording every minute:
```
60 × 24 × 365
= 525,600 readings/year
```
would probably only be around **30–50 MB** depending on formatting.
So storage is basically a non-issue.

>The bigger problem is actually **data quality and reliability**.


### What to do with the data collected?
---
**Answer**: For now as of writing this `August 15, 2026` — The dataset will be generate will be useful only to me (i guess) since the setting done locally inside my room. I can share to others as a proof and strengthen an observation at which point of year has the highest humidity. 

---
### Where is the best location and placement for the sensor to accurately represent my room's ambient temperature and humidity?
---
Listed below are what i think the important variables for this question.
`Room dimension` — 320cm x 280cm and 11ft height 
`Appliances & Furniture` :
	Portable AC (placed near the window at height of 27in)
	Electric Fan (height at 45inch placed next to window pointing towards inside)
	Laptop,
	 UPS
	Light Bulb (installed exactly in the middle of the room)
	Bed   – with dimension of 56in x 80in with height of 15in
`Windows` — Yes, only 1 at a dimension of 37in x 61in
`Others` — Me, sometimes in the room, sometimes not in the room

**Answer**: Best place is `1.2 –1.5m` above the floor somwhere near the middle, but not directly in the path of airflow. 
Well, its easier to say but i dont know how to place it in the middle of my room. 

I added this as part of the problem [[#Potential Problems]]

### Can this project scale to progressively harder?
---
**Answer**: Yes. It can be done phase by phase

> **Phase 1 — Sensor**
	ESP32 + DHT22

Learn:
- GPIO
- Sensor Communication
- ESP32 Programming
- Sampling

================================

>**Phase 2 — Data logging**
>	ESP32 ——> HTTP/MQTT ——> Linux Server

Learn:
- Networking
- API's
- MQTT
- Systemd/cron
- File Handling

================================

>**Phase 3 — Database**
>	ESP32 ——> Python ——> SQLite

Learn:
- SQL
- Schema design
- timestamps
- querying

================================

>**Phase 4 — Visualization**
>	SQLite ——> pandas ——> matplotlib

Generates:
- Temp over time
- Humidty over time
- Temp vs Humidity
- Daily Cycle
- Monthly Trends

================================

>**Phase 5 — External Weather**
>	Weather API ——> Local Sensor + Outdoor Weather

This is where the actual data correlation comes into play.

---


## Potential Problems
---
>There is a frequent power interruption where i live. So things could be disrupted during that time. I could buy  a high capacity powerbank, but honestly i could potentially just use that for other personal use instead of it being a dedicated power source.

**Potential Solution** — to accept the gap, As long as it is being recorded rather than fabricating data.

**Addition**: log a `last_boot`/`uptime` metadata field alongside the readings, so during analysis it's clear which gaps are due to power loss vs. other issues (network down, sensor failure, etc). Helps distinguish data quality issues later. 

======================================

>Sensor placement. The  easiest option i have right now is to place it near the wall by sticking it and powering it by powerbank. But this could affect the entire data quality.
>I can look for longer cables to hang the sensor below the ceiling while the microcontroller is  attach and sticking against the wall but im not sure if a cable with length like 1m for this project even exist.

**Update**: Confirmed — longer wires (e.g. extended DuPont/jumper wires, or extending the DHT22 leads) do exist and are commonly used for this exact purpose (DIY humidors, incubators, etc). This makes ceiling/mid-room hanging feasible while keeping the ESP32 itself mounted on the wall.

**Plan**: Will test sensor placement for about 1-3 days to determine the best spot before committing to a permanent position.

---

**Addition**: track any physical setup changes (e.g. "moved sensor away from AC vent on [date]") in a log/metadata field — these can skew interpretation later if the context isn't recorded.

---


# Abstract Architecture
---

```
=====================================================
  ABSTRACT ARCHITECTURE — Room Microclimate Logger
=====================================================

[Physical Layer]

  DHT22 Sensor
      │
      │ (data pin, extended wire — TBD length after 1-3 day placement test)
      │
      ▼
  ESP32 (mounted on wall, powered via USB)
      │
      │  reads temp + humidity every 5 min
      │  attaches: timestamp, last_boot/uptime
      │
      ▼

-----------------------------------------------------
[Transport Layer]  — HOW does ESP32 send data?
-----------------------------------------------------

  ESP32
      │
      │  Option A: HTTP POST (simple, request/response)
      │  Option B: MQTT (lightweight, pub/sub, better for
      │             intermittent connectivity / retries)
      │
      ▼
  Laptop / Server (running 24/7 on local network)
      │
      │  CONFIRMED: laptop = the endpoint.
      | ESP32 does not
      │  store data long-term, it's sender-only (reads +
      │  sends). All real work (receive, validate, store,
      │  commit) happens on the laptop.
      │
      │  RISK: 24/7 uptime dependency — if laptop sleeps/
      │  hibernates (accidentally or via Windows update/
      │  power settings), no readings get received even if
      │  ESP32 is still running = gap on the receiving end.
      │  Consider: configure power settings to prevent
      │  sleep, and/or apply a last_boot/uptime style flag
      │  on the laptop/server side too (not just ESP32) to
      │  identify this as a separate gap source.


-----------------------------------------------------
[Ingestion Layer]  — receives + writes data
-----------------------------------------------------

  Listener script (Python)
      │
      │  validates incoming reading
      │  handles missing/malformed data
      │  writes to storage (CSV or SQLite — TBD)
      │
      ▼
  data/2026.csv   (or)   data/sensor.db


-----------------------------------------------------
[Backup Layer]
-----------------------------------------------------

  Cron job (daily)
      │
      │  git add + commit + push
      │
      ▼
  Git repo (local/remote snapshot)


-----------------------------------------------------
[External Context Layer]  — Phase 5, not yet active
-----------------------------------------------------

  Weather API (outdoor temp, humidity, rain, wind, pressure)
      │
      │  joined with local sensor data via timestamp
      ▼
  Combined dataset


-----------------------------------------------------
[Analysis Layer]  — Phase 4
-----------------------------------------------------

  Stored data
      │
      │  pandas (aggregation: hourly/daily/monthly avg)
      │  matplotlib (visualization)
      │
      ▼
  Charts / Summary reports (daily.csv, monthly.csv, yearly.md)


=====================================================
  OPEN DECISIONS (not yet locked in)
=====================================================
  - Transport: HTTP vs MQTT
  - Storage: CSV vs SQLite
  - Sensor: DHT22 (starter) vs upgrade (SHT31/BME280)
  - Sensor placement: pending 1-3 day test
  - Metadata fields to finalize: timestamp, temp, humidity,
    last_boot/uptime, placement_note (optional, only on change)
=====================================================
```

---


# Parts
---

## Sensor Architecture Decision
---
**Decision**: Do NOT implement air quality in Phase 1. Keep Phase 1 focused on Temp + Humidity only — matches the original hypothesis (indoor RH vs Southwest Monsoon) directly, without adding extra dimensions (pressure, gas, VOC, CO2, PM2.5) that turn the project into a general environmental station instead of a focused microclimate experiment.

**Design principle**: treat each measurement as an independent variable (timestamp → temp, humidity, pressure*, air_quality*, *=future) rather than requiring every environmental variable from day one. This makes the system "capable of progressively collecting measurements" instead of needing everything upfront — compatible with the Phase 1→5 plan.

**Adding vs Replacing**: when upgrading sensor (e.g. DHT22 → BME680), don't have to fully replace — can run both simultaneously for overlapping measurements (comparison/experimentation). But this raises a new question: **which sensor is the "official" source of truth** for temp/humidity continuity in the dataset if both are logging? Needs to be explicit in metadata/schema if this route is taken — otherwise ambiguous during analysis. → Add as an Open Decision if sensor overlap is ever attempted.

**Schema handling for missing older data**: when a new measurement (e.g. air_quality) is added later, older records simply won't have that column — treat as missing/NULL, not zero. Not a blocker, just needs correct handling in analysis (pandas).

## Display
---
**Decision**: OLED (SSD1306, 0.96" or 1.3") — top pick.

|                   Option                    | Notes                                                                                                            |     |
| :-----------------------------------------: | ---------------------------------------------------------------------------------------------------------------- | --- |
|                  16x2 LCD                   | Familiar (same as old Arduino setup), cheap, simple — but limited info, more wiring, less flexible for future UI |     |
|                  20x4 LCD                   | Bigger version, fits more fields (temp/hum/pressure/log status) — nice for monitoring station look               |     |
|            **OLED** \| Preferred            | same I2C bus as BME280/680 (no extra wiring complexity), flexible layout, can evolve UI as sensors are added     |     |
| TFT (color) \| Unnecessary for this project | adds UI/software complexity without contributing to the actual research                                          |     |
**Use case**: gives a local human-facing interface (everyday room monitor) while the laptop remains the actual data endpoint — display isn't just decoration.

**UI evolution concept**:
```
Phase 1:          Later (w/ pressure):     Later (w/ air quality):
┌──────────────┐  ┌──────────────┐        ┌──────────────┐
│ 28.7°C 82%RH │  │ 28.7°C 82%RH │        │ 28.7°C 82%RH │
│ LOG: OK      │  │ 1011 hPa     │        │ Air: GOOD    │
└──────────────┘  └──────────────┘        └──────────────┘
```

## Prototyping Platform
---
**Decision**: Breadboard for Phase 1. NOT perfboard, NOT custom PCB, NOT soldering yet.

Progression:
```
Breadboard → Prototype works → Test placement → Maybe permanent install → Perfboard/PCB (soldering)
```

Soldering iron, solder, flux, perfboard — only needed once circuit is stable and ready to become a permanent device. Not needed for first prototype. If/when that stage comes, can figure out the right flux/solder type then — no need to buy now.

## Initial BOM (Phase 1 — breadboard prototype)
---
**Core**
- ESP32 development board
- DHT22 (sensor — see Sensor Architecture Decision above)
- Breadboard
- Jumper wires — male-to-male
- Jumper wires — male-to-female
- USB **data** cable for ESP32 (not charging-only — common mistake)
- USB power adapter (separate item from the cable — easy to forget)

**Display** (choose one, OLED preferred — see Display section above)
- 16x2 LCD + I2C backpack, or
- 20x4 LCD + I2C backpack, or
- Small I2C OLED (SSD1306)

**Sensor placement**
- Longer cable/wire for DHT22 (extended DuPont/jumper wires, or extend the DHT22 leads directly)
- Basic mounting material
- Eventually: a ventilated enclosure

**Not needed yet**: soldering iron, solder, flux, perfboard, custom PCB — see Prototyping Platform decision above.
