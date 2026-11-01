Up: [[00 Index]]

# Data Design
---

## Should i include to log other data like the `current weather`? If yes how it should be done?
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
## At what interval should i log?
---
**Answer**: Record more frequently that once per day.

One measurement per day loses a huge amount of information. A good starting point is every `5 minutes`.
Then it can be later aggregate it into:
- hourly avg
- daily avg
- daily minimum
- monthly avg

---
## How much storage (approximately) will this consume throughout the year?
---
At every `5 minutes`:
```
12 readings/hour
× 24 hours
× 365 days
= 105,120 readings/year
```
The original estimate was for a bare temp/RH CSV record (~50–80 bytes, so ~5–8 MB/year). The row is bigger now (temp, RH, CO2, three PM fields, uptime, firmware, status), so a rough, updated guess for SQLite is about `100–150 bytes` per row:

> **~10–16 MB/year** at 5-minute logging. Still tiny. *(Rough estimate, to be checked against real data during the soak test.)*

Even at every minute (525,600 readings/year), it is only a few tens of MB.

>The bigger problem is actually **data quality and reliability**.

---
## At what interval should i perform backup of the data?
---
**Answer**: ~~The sweet spot i was thinking is `Every 15 days`~~ Or so i thought.

According to AI, every 15 days is too far — if the interval logging is every 5 minutes, for 15 days that's about 4,300 readings at risk of losing.

> Recommended about 3 days max. Since storage cost is basically negligible (a few MB/year), even better to just automate the git commit **daily** via cron — no real downside to committing more often.

Tools: [[Tools#**Cron**|Cron]] and [[Git Workflow & Commands|Git]].

**Open question** *(added during review)*: committing the binary `.db` file to git daily makes the repo history grow. Alternatives are committing a daily CSV export, or committing a `sqlite3 .backup` copy. Tracked in [[10 Decisions]].

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

**Open question** *(added during review)*: how are events recorded? Manually, or with some automation? Tracked in [[10 Decisions]].

---
## What to do with the data collected?
---
**Answer**: The setting is local, so the dataset primarily describes this room rather than every home. Its value comes from comparisons and context: indoor-versus-outdoor temperature/RH deltas, seasonal patterns, rain and wind, AC/window/fan events, CO2 as a ventilation/occupancy proxy, and PM events such as dust or smoke. It can support observations about how this particular room responds to weather and room operation; it is not a general air-safety certification.
