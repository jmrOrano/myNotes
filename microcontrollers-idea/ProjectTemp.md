---
type: idea
tags:
  - microcontrollers
  - homelab
  - sensors
status: exploring
Date Generated: August 15, 2026
---
# **The idea — Room Microclimate and Indoor-Environment Logger**
---
To build a year-long indoor-environment dataset for one room: temperature, relative humidity, CO2, and particulate matter (PM1/PM2.5/PM10). The goal is to investigate how those local conditions relate to room operation and seasonal/outdoor-weather patterns in my country.

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
- **Official sensor stack** — dedicated modules with distinct roles:
	- `SHT31` or `SHT41` — temperature and relative humidity
	- `SCD41` — NDIR carbon-dioxide (CO2) measurement
	- `SPS30` — PM1, PM2.5, and PM10 particulate-matter measurement
	- `BME688` — optional future experimental VOC/IAQ trend sensor; not an official CO2, PM, named-gas, or safety sensor
- **SQLite** – authoritative storage for readings, weather context, and event logs; CSV exports are for sharing/analysis
- A laptop that runs 24/7 — An endpoint for the data. 

---
## **Potential Questions**
---
### Should i include to log other data like the `current weather`? If yes how it should be done?
---
**Answer**: Yes, but dont make the ESP32 responsible for everything. 

**The local sensor stack measures:**
- timestamp
- temperature
- relative humidity
- CO2 (ppm)
- PM1, PM2.5, and PM10 (ug/m3)
- device uptime and sensor/read-status metadata

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
The external-weather integration is built during the final development phase, then becomes part of the official dataset from its first day.


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
**Answer**: The setting is local, so the dataset primarily describes this room rather than every home. Its value comes from comparisons and context: indoor-versus-outdoor temperature/RH deltas, seasonal patterns, rain and wind, AC/window/fan events, CO2 as a ventilation/occupancy proxy, and PM events such as dust or smoke. It can support observations about how this particular room responds to weather and room operation; it is not a general air-safety certification.

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

> **Phase 1 — Temperature/RH foundation**
> ESP32 + SHT31/SHT41 + OLED

Learn and verify:
- I2C, ESP32 programming, sampling, display, and temperature/RH sanity checks

================================

> **Phase 2 — CO2 integration**
> Add SCD41

Learn and verify:
- NDIR CO2 readings, warm-up/field-calibration requirements, ventilation response

================================

> **Phase 3 — Particulate-matter integration**
> Add SPS30

Learn and verify:
- PM1/PM2.5/PM10 fields, I2C/UART interface, startup/fan behavior, particle-response testing

================================

> **Phase 4 — Reliable local logging**
> Integrated sensor stack ——> Python listener ——> SQLite

Learn and verify:
- common timestamps, validation, missing values, status fields, power/restart behavior, schema design, and querying

================================

> **Phase 5 — Network, weather context, and analysis**
> ESP32 ——> HTTP/MQTT ——> local server ——> SQLite + Weather API ——> pandas/matplotlib

Learn and verify:
- transport/retries, daily backup, timestamp joins, event-log joins, charts, and indoor-to-outdoor differential analysis

Only after all five development phases are stable does the official year-long dataset begin.


---


## Potential Problems
---
>There is a frequent power interruption where i live. So things could be disrupted during that time. I could buy  a high capacity powerbank, but honestly i could potentially just use that for other personal use instead of it being a dedicated power source.

**Potential Solution** — to accept the gap, As long as it is being recorded rather than fabricating data.

**Addition**: log a `last_boot`/`uptime` metadata field alongside the readings, so during analysis it's clear which gaps are due to power loss vs. other issues (network down, sensor failure, etc). Helps distinguish data quality issues later. 

======================================

>Sensor placement. The  easiest option i have right now is to place it near the wall by sticking it and powering it by powerbank. But this could affect the entire data quality.
>I can look for longer cables to hang the sensor below the ceiling while the microcontroller is  attach and sticking against the wall but im not sure if a cable with length like 1m for this project even exist.

**Update**: Longer, suitable sensor cables do exist. The final choice must be compatible with each module, especially the SPS30 connector, and should be tested during development. This makes representative mid-room placement feasible while keeping the ESP32 itself mounted on the wall.

**Plan**: Will test sensor placement for about 1-3 days to determine the best spot before committing to a permanent position.

---

**Addition**: track any physical setup changes (e.g. "moved sensor away from AC vent on [date]") in a log/metadata field — these can skew interpretation later if the context isn't recorded.

---

## Operating-condition event log
---
The room is intentionally not treated as a perfectly controlled laboratory. AC use, an open window, the fan, weather, and occupancy are part of the room's real microclimate. The data is useful when those contexts are recorded rather than ignored.

Record meaningful changes in a small event log:
- AC on/off and optional set temperature
- window open/closed
- fan on/off
- notable occupancy or activity such as sleeping, cleaning, or a nearby smoke/dust event
- power loss, server restart, network loss, sensor fault, maintenance, and firmware/configuration changes

Use `timestamp_start`, optional `timestamp_end`, `event_type`, `event_value`, and a short note. The goal is consistent context, not perfect minute-by-minute surveillance.

---


# Abstract Architecture
---

```
=====================================================
  ABSTRACT ARCHITECTURE — Room Microclimate Logger
=====================================================

[Physical Layer]

  SHT31/SHT41 + SCD41 + SPS30
      │
      │ (I2C/UART; final cable lengths and placement follow the development test)
      │
      ▼
  ESP32 + OLED (mounted away from sensing location heat, powered via USB)
      │
      │  reads temp/RH, CO2, and PM on their validated schedules
      │  attaches: timestamp, uptime, firmware version, and sensor status
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
  sensor.db (SQLite; CSV exports when needed)


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
  - Storage: SQLite as the official store; CSV export format to finalize
  - Temperature/RH sensor: SHT31 vs SHT41
  - Sensor stack: SHT31/SHT41 + SCD41 + SPS30
  - Sensor placement: pending 1-3 day test
  - Metadata fields to finalize: timestamps, temp, RH, CO2, PM fields,
    uptime, firmware version, sensor status, and placement/configuration notes
=====================================================
```

---


# Parts
---

## Sensor Architecture Decision
---
**Decision**: The official dataset will use three dedicated sensors, but they will be implemented one at a time during development. The year-long dataset does not start after Phase 1. It starts only when the complete stack, local logging, weather context, and event logging are integrated and stable.

**Official stack**:
- `SHT31` or `SHT41` — the official temperature and relative-humidity source
- `SCD41` — direct NDIR CO2 measurement; a ventilation/occupancy indicator
- `SPS30` — PM1, PM2.5, and PM10 measurement

**Why dedicated sensors**: temperature/RH, CO2, and particulate matter are different physical measurements. A single generic "air-quality" number would make the final interpretation less clear. The project measures named variables, not a universal good/bad-room score.

**BME688 policy**: BME688 can be added later as an optional experimental VOC/IAQ-trend channel. Its baseline-adaptive output is not the official temperature/RH source, does not measure actual CO2, does not identify every gas in the room, and is not a safety alarm. It must not replace the SCD41 or SPS30.

**Schema rule**: the official schema is frozen at the start of the official run. Missing readings are `NULL`, never zero. A future experimental sensor receives separate fields and explicit configuration metadata rather than silently changing the meaning of the official series.

## Display
---
**Decision**: OLED (SSD1306, 0.96" or 1.3") — top pick.

|                   Option                    | Notes                                                                                                            |     |
| :-----------------------------------------: | ---------------------------------------------------------------------------------------------------------------- | --- |
|                  16x2 LCD                   | Familiar (same as old Arduino setup), cheap, simple — but limited info, more wiring, less flexible for future UI |     |
|                  20x4 LCD                   | Bigger version, fits more fields (temp/hum/pressure/log status) — nice for monitoring station look               |     |
|            **OLED** \| Preferred            | shares I2C with the SHT and SCD41 modules, flexible layout, can evolve UI as sensors are integrated              |     |
| TFT (color) \| Unnecessary for this project | adds UI/software complexity without contributing to the actual research                                          |     |
**Use case**: gives a local human-facing interface (everyday room monitor) while the laptop remains the actual data endpoint — display isn't just decoration.

**UI evolution concept**:
```
Phase 1:          After CO2 phase:         After PM phase:
┌──────────────┐  ┌──────────────┐        ┌──────────────┐
│ 28.7°C 82%RH │  │ 28.7°C 82%RH │        │ 28.7°C 82%RH │
│ LOG: OK      │  │ CO2: 850 ppm │        │ PM2.5: 8     │
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

## Initial BOM (development prototype, leading to the official stack)
---
**Core controller and prototype hardware**
- ESP32 development board
- Breadboard
- Jumper wires — male-to-male
- Jumper wires — male-to-female
- Jumper wires — female-to-female
- USB **data** cable for ESP32 (not charging-only — common mistake)
- USB power adapter (separate item from the cable — easy to forget)

**Official sensor stack**
- SHT31 or SHT41 breakout/module — temperature + relative humidity
- SCD41 breakout/module — NDIR CO2
- SPS30 module — PM1/PM2.5/PM10; include the correct cable/connector for the module

**Display** (choose one, OLED preferred — see Display section above)
- 16x2 LCD + I2C backpack, or
- 20x4 LCD + I2C backpack, or
- Small I2C OLED (SSD1306)

**Sensor placement**
- Longer, appropriate sensor cable only after the 1-3 day placement test
- Basic mounting material
- Eventually: a ventilated enclosure

**Not needed yet**: soldering iron, solder, flux, perfboard, custom PCB, or BME688. Add the BME688 only as a separately labelled future experiment, never as a replacement for the official CO2 or PM sensors.

---

# Method of Execution
---

## Core idea: build through Phase 5 first, then start official logging
---
The plan is to treat Phase 1 to Phase 5 [[#Can this project scale to progressively harder?||(Read this) ]]as a build-and-learn stage, not as the real data collection. This means every reading gathered while wiring up the sensor, testing the display, testing the transport method, and testing sensor placement is just practice data — not the "official" dataset. The official, year-long logging only begins once all 5 phases are working together and stable.

For this plan, "all phases" means the full official stack is present: SHT31/SHT41 for temperature and RH, SCD41 for CO2, SPS30 for PM, the local endpoint and SQLite storage, weather context, and an event log. The sensors are developed progressively; they are frozen together only at the official start.

**Why do it this way:**

- Every time something changes in the system — a new sensor, a new field being logged, a different way of storing data — it creates a break or inconsistency in the dataset if logging already "officially" started. Fixing or improving the system while also trying to keep a clean permanent record at the same time becomes messy fast.
- If logging starts too early (like right after Phase 1), the dataset would carry all the growing pains of the project: gaps from testing, inconsistent formats, missing weather data for the early months since that only gets added in Phase 5.
- By keeping the build stage completely separate from the official run, the messy part (failing, fixing, changing components) never touches the "real" data. Once Phase 5 works, the architecture is already proven, and the data collected from that point on can be trusted as consistent from day one to the end of the year.
- In short: better to be unstable while nobody is counting on the data yet, than to be unstable while trying to maintain a serious dataset at the same time.

## Reminder: perfect is the enemy of started
---
The risk of this approach is getting stuck trying to make Phase 5 "perfect" before ever really starting. There will always be one more thing that feels worth improving — better sensor, better wiring, better code — and if that mindset isn't kept in check, the official logging might never actually begin.

**Why this matters:**

- The goal of Phase 1-5 is not to build something flawless, it's to build something stable and working. Stable is enough to start. Waiting for perfect usually just means waiting forever, since there's always something more to improve.
- A simple way to avoid this trap is to set a clear, minimum checklist of what "done enough" looks like before moving to official logging (e.g. sensor placement finalized, transport working reliably, storage format decided, weather API connected). Once that checklist is met, start — even if the system isn't the most optimized version it could be.
- The minimum checklist also requires a 1-2 week integrated soak test, correct restart/gap logging, and a working event log for AC, window, fan, and configuration changes.
- Improvements can still happen later, but they happen as intentional, tracked changes to an already-running project, not as an excuse to keep delaying the actual start.
