Up: [[00 Index]]

# Architecture
---

## **Tools and technologies**
---
- [[Tools#**Cron**|Cron]] – to perform git
- [[Git Workflow & Commands|Git]] – to serve as a backup or snapshot
- **Esp32** — a the main controller
- **Official sensor stack** — see [[05 Sensor Stack]]
- **SQLite** – authoritative storage for readings, weather context, and event logs; CSV exports are for sharing/analysis
- A laptop that runs 24/7 — An endpoint for the data.

## **Abstract Architecture**
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
      │  ESP32 does not store data long-term, it's
      │  sender-only (reads + sends). All real work
      │  (receive, validate, store, commit) happens
      │  on the laptop.
      │
      │  RISK: 24/7 uptime dependency. See [[09 Risks & Problems]].


-----------------------------------------------------
[Ingestion Layer]  — receives + writes data
-----------------------------------------------------

  Listener script (Python)
      │
      │  validates incoming reading
      │  handles missing/malformed data
      │  writes to SQLite
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
[Analysis Layer]  — Phase 5
-----------------------------------------------------

  Stored data
      │
      │  pandas (aggregation: hourly/daily/monthly avg)
      │  matplotlib (visualization)
      │
      ▼
  Charts / Summary reports (daily.csv, monthly.csv, yearly.md)

=====================================================
```

Open and locked decisions live in [[10 Decisions]]. Data details are in [[07 Data Design]].
