Up: [[00 Index]]

# Hardware & BOM
---

## Display
---
**Decision**: OLED (SSD1306, 0.96" or 1.3") — top pick.

| Option | Notes |
|:--|---|
| 16x2 LCD | Familiar (same as old Arduino setup), cheap, simple — but limited info, more wiring, less flexible for future UI |
| 20x4 LCD | Bigger version, fits more fields (temp/hum/CO2/log status) — nice for monitoring station look |
| **OLED (preferred)** | Shares I2C with the SHT and SCD41 modules, flexible layout, can evolve UI as sensors are integrated |
| TFT (color) | Unnecessary for this project — adds UI/software complexity without contributing to the actual research |

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

**Official sensor stack** (see [[05 Sensor Stack]])
- SHT31 or SHT41 breakout/module — temperature + relative humidity
- SCD41 breakout/module — NDIR CO2
- SPS30 module — PM1/PM2.5/PM10; include the correct cable/connector for the module

**Display** (choose one, OLED preferred — see Display section above)
- 16x2 LCD + I2C backpack, or
- 20x4 LCD + I2C backpack, or
- Small I2C OLED (SSD1306)

**Sensor placement** (see [[08 Placement]])
- Longer, appropriate sensor cable only after the 1-3 day placement test
- Basic mounting material
- Eventually: a ventilated enclosure

**Not needed yet**: soldering iron, solder, flux, perfboard, custom PCB, or BME688. Add the BME688 only as a separately labelled future experiment, never as a replacement for the official CO2 or PM sensors.

## To verify before buying *(added during review)*
---
- SPS30 is, as far as I recall, powered from 5V rather than 3.3V. Confirm in the datasheet, and check that the ESP32 board can supply it.
- Check the SPS30 connector type so the right cable or breakout adapter is bought.
