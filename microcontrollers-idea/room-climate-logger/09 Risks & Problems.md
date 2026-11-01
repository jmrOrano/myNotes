Up: [[00 Index]]

# Risks & Problems
---

## Power interruption
---
>There is a frequent power interruption where i live. So things could be disrupted during that time. I could buy  a high capacity powerbank, but honestly i could potentially just use that for other personal use instead of it being a dedicated power source.

**Potential Solution** — to accept the gap, As long as it is being recorded rather than fabricating data.

**Addition**: log a `last_boot`/`uptime` metadata field alongside the readings, so during analysis it's clear which gaps are due to power loss vs. other issues (network down, sensor failure, etc). Helps distinguish data quality issues later.

## Laptop uptime (the endpoint)
---
If the laptop sleeps or hibernates (accidentally or via Windows update/power settings), no readings get received even if the ESP32 is still running. That is a gap on the receiving end.

Consider: configure power settings to prevent sleep, and/or apply a last_boot/uptime style flag on the laptop/server side too (not just the ESP32) to identify this as a separate gap source.

## Sensor placement
---
See [[08 Placement]].

## Added during review (verify with datasheets)
---
- **SCD41 self-calibration:** its automatic calibration, as far as I recall, assumes the sensor sees fresh-air CO2 levels (~400 ppm) now and then. In a room that is often closed with the AC on, that could skew calibration. Decide how to handle calibration.
- **Heat and airflow:** SPS30's fan, the SCD41, and the ESP32 can warm or disturb the air near the SHT sensor. See [[08 Placement]].
- **Git and SQLite:** daily commits of a binary `.db` file grow the repo. See [[07 Data Design]].
- **Event log method:** it is required by the Official Start Gate in [[02 Method of Execution]], but how events get recorded is not decided yet.
