Up: [[00 Index]]

# Method of Execution
---

## Core idea: build through Phase 5 first, then start official logging
---
The plan is to treat Phase 1 to Phase 5 ([[03 Phases]]) as a build-and-learn stage, not as the real data collection. This means every reading gathered while wiring up the sensor, testing the display, testing the transport method, and testing sensor placement is just practice data — not the "official" dataset. The official, year-long logging only begins once all 5 phases are working together and stable.

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
- A clear minimum checklist of what "done enough" looks like keeps this in check. That is the **Official Start Gate** below. Once it is met, start — even if the system isn't the most optimized version it could be.
- Improvements can still happen later, but they happen as intentional, tracked changes to an already-running project, not as an excuse to keep delaying the actual start.

## Official Start Gate
---
The official year-long dataset begins only when all of these are true:

- [ ] Sensor placement finalized (see [[08 Placement]] and [[placement-test]])
- [ ] SHT31/SHT41, SCD41, and SPS30 all integrated and reading
- [ ] Transport (HTTP or MQTT) working reliably
- [ ] SQLite storage decided and schema frozen
- [ ] Weather API connected and joined by timestamp
- [ ] Event log working for AC, window, fan, and configuration changes
- [ ] Restart and gap logging behaves correctly
- [ ] 1-2 week integrated soak test passed ([[soak-test]])

Open items that still block this gate are tracked in [[10 Decisions]].
