Up: [[00 Index]]

# Sensor Stack
---

**Decision**: The official dataset will use three dedicated sensors, but they will be implemented one at a time during development. The year-long dataset does not start after Phase 1. It starts only when the complete stack, local logging, weather context, and event logging are integrated and stable. See [[02 Method of Execution]].

**Official stack**:
- `SHT31` or `SHT41` — the official temperature and relative-humidity source
- `SCD41` — direct NDIR CO2 measurement; a ventilation/occupancy indicator
- `SPS30` — PM1, PM2.5, and PM10 measurement

**Why dedicated sensors**: temperature/RH, CO2, and particulate matter are different physical measurements. A single generic "air-quality" number would make the final interpretation less clear. The project measures named variables, not a universal good/bad-room score.

**BME688 policy**: BME688 can be added later as an optional experimental VOC/IAQ-trend channel. Its baseline-adaptive output is not the official temperature/RH source, does not measure actual CO2, does not identify every gas in the room, and is not a safety alarm. It must not replace the SCD41 or SPS30.

**Schema rule**: the official schema is frozen at the start of the official run. Missing readings are `NULL`, never zero. A future experimental sensor receives separate fields and explicit configuration metadata rather than silently changing the meaning of the official series.

## Phase mapping
---
| Sensor | Added in |
|---|---|
| SHT31/SHT41 | Phase 1 |
| SCD41 | Phase 2 |
| SPS30 | Phase 3 |

Details in [[03 Phases]]. Parts and cables are in [[06 Hardware & BOM]].
