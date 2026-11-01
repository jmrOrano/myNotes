Up: [[00 Index]]

# Phases
---
### Can this project scale to progressively harder?
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

---
Only after all five development phases are stable does the official year-long dataset begin. See the **Official Start Gate** in [[02 Method of Execution]].
