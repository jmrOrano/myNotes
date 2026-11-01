---
type: idea
tags:
  - microcontrollers
  - homelab
  - sensors
status: exploring
Date Generated: August 15, 2026
---
# **Room Microclimate and Indoor-Environment Logger**
---
A year-long indoor-environment dataset for one room: temperature, relative humidity, CO2, and particulate matter (PM1/PM2.5/PM10), compared against outdoor weather and room events (AC, window, fan).

> Hypothesis: `August has unusually high indoor relative humidity because of the Southwest Monsoon.`

## **Map**
---
| # | Note | What's inside |
|---|---|---|
| 01 | [[01 Idea & Hypothesis]] | Why this project exists |
| 02 | [[02 Method of Execution]] | Build first, log officially later + **Official Start Gate** |
| 03 | [[03 Phases]] | Phase 1 to Phase 5 |
| 04 | [[04 Architecture]] | Tools and the abstract architecture |
| 05 | [[05 Sensor Stack]] | SHT31/SHT41, SCD41, SPS30, BME688 policy |
| 06 | [[06 Hardware & BOM]] | Display, breadboard-first, parts list |
| 07 | [[07 Data Design]] | Logging interval, storage, backup, weather context, event log |
| 08 | [[08 Placement]] | Where the sensor goes |
| 09 | [[09 Risks & Problems]] | Power cuts, laptop sleep, sensor gotchas |
| 10 | [[10 Decisions]] | Locked vs open decisions (single source of truth) |

Test results go in `Dev Log/`: [[placement-test]], [[soak-test]].
