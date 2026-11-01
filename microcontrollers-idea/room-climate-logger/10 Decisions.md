Up: [[00 Index]]

# Decisions
---
Single source of truth for what is decided and what is still open. Update this note first when something changes.

## Locked
---
- Official sensor stack: SHT31/SHT41 (temp/RH) + SCD41 (CO2) + SPS30 (PM). See [[05 Sensor Stack]]
- BME688 is optional and experimental only, never a replacement for SCD41 or SPS30
- SQLite is the authoritative storage; CSV is for exports
- Laptop (24/7) is the data endpoint; the ESP32 is sender-only
- OLED display
- Breadboard-first, no soldering for now
- Daily cron-based git backup
- Build through Phase 5 first, official logging starts only after the Official Start Gate. See [[02 Method of Execution]]
- Missing readings are `NULL`, never zero; schema is frozen at the official start

## Open
---
- Transport: HTTP vs MQTT
- Temperature/RH sensor: SHT31 vs SHT41
- Sensor placement: pending the 1-3 day test ([[placement-test]])
- CSV export format to finalize
- Metadata fields to finalize: timestamps, temp, RH, CO2, PM fields, uptime, firmware version, sensor status, and placement/configuration notes
- Event log: how are events recorded (manual or automated)? *(added during review)*
- Git backup format: binary `.db` vs daily CSV export vs `sqlite3 .backup` copy *(added during review)*
- SCD41 calibration approach *(added during review)*
