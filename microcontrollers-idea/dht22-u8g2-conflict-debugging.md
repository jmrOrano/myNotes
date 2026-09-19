# DHT22 + U8g2 OLED Conflict — Debugging Log

**Board:** Arduino Uno (separate from the ESP32 microclimate logger — bench test / prototyping board, DHTPIN 3, I2C on A4/A5)
**Status:** RESOLVED
**Date:** 2026-09-17 → 2026-09-19

## TL;DR

Combining a DHT22 read with a U8g2 OLED on an Uno caused the OLED (and sometimes Serial output) to fail — symptoms included no display, gibberish pixels, and dropped Serial output. After ruling out timing/interrupt conflicts between the two libraries, **the actual cause was SRAM exhaustion**: `U8G2_..._F_...` (full framebuffer) constructors reserve a full 1024-byte screen buffer, which is half the Uno's 2048 bytes of SRAM on its own — leaving too little room for the DHT object, Serial buffers, and other runtime state. Switching to the `_1_` (page buffer) constructor variant fixed it completely.

**Fix:** Use `U8G2_SH1106_128X64_NONAME_1_HW_I2C` instead of the `_F_` variant, and switch the draw pattern from `clearBuffer()`/`sendBuffer()` to `firstPage()`/`nextPage()`.

## Background

Original code combined a DHT22 sensor and a U8G2_SH1106_128X64 OLED (hardware I2C, full framebuffer variant) on an Uno, using a helper function to convert float readings to strings for display. The OLED stopped displaying, and initial suspicion fell on the helper function and buffer reuse.

## Phase 1 — Ruling out the string-formatting code

Code review of the float-to-string helper turned up two real (but ultimately unrelated) issues:

1. **Undersized buffer.** `static char buf[7]` is exactly enough for `"100.00\0"` with zero headroom — bumped to `buf[10]`.
2. **Overlapping draw coordinates.** Temperature and humidity were both being drawn at the same `(86, 19)` coordinates, so humidity silently overwrote temperature on screen.

Neither explained the total display failure, so the investigation moved to isolating the actual cause.

## Phase 2 — Isolating to `dht.readTemperature()` (timing hypothesis)

Controlled tests systematically ruled out the helper function, `dtostrf()`, and `drawStr()` itself:

| Test                                                            | DHT library | U8g2 | Result    |
| --------------------------------------------------------------- | ----------- | ---- | --------- |
| OLED only                                                       | ❌           | ✅    | Works     |
| DHT only                                                        | ✅           | ❌    | Works     |
| DHT + U8g2, hardcoded string (no float conversion at all)       | ✅           | ✅    | **Fails** |
| DHT object created + `dht.begin()`, never read                  | ✅           | ✅    | Works     |
| Same, plus `dht.readTemperature()` called (return value unused) | ✅           | ✅    | **Fails** |

This narrowed the failure to the act of calling `dht.readTemperature()` itself, regardless of what was done with the result. Leading hypothesis at this point: DHT22's bit-banged single-wire protocol is timing-sensitive and might disable interrupts long enough to disrupt U8g2's I2C (`Wire`) communication.

**This hypothesis turned out to be a red herring** — see Phase 3.

## Phase 3 — Root cause: SRAM exhaustion, not timing

Switching to `DHT_Unified` (`DHT_U.h`) and adding Serial marker-print debugging (`Serial.println("A")`, `"B"`, etc. between each line) to localize exactly where execution stalled was the planned next step — but the more direct experiment was memory-focused:

- `U8G2_SH1106_128X64_NONAME_F_HW_I2C` uses the **`_F_` full-framebuffer** variant: 128×64 monochrome = 8192 bits = **1024 bytes**, reserved as one contiguous buffer.
- The Uno has **2048 bytes of SRAM total** — the OLED framebuffer alone consumes ~50% of it.
- Add the DHT object/state, Serial buffers, local variables, and stack, and the program runs out of headroom. This produces exactly the kind of erratic, seemingly-unrelated symptoms seen earlier (garbled pixels, dropped Serial output, hangs) — classic signs of SRAM corruption/collision rather than a clean logic bug.

**Fix tested:** switch to `U8G2_SH1106_128X64_NONAME_1_HW_I2C` — the `_1_` variant uses a much smaller page buffer instead of reserving the full screen at once.

This requires changing the draw pattern from:

```cpp
oled.clearBuffer();
// draw calls
oled.sendBuffer();
```

to the page-buffer pattern:

```cpp
oled.firstPage();
do {
    // all draw calls go here — this block re-runs per page
} while (oled.nextPage());
```

### Minimal rebuild used to confirm the fix

```cpp
#include <Arduino.h>
#include <DHT.h>
#include <U8g2lib.h>

#define DHTPIN 3
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

U8G2_SH1106_128X64_NONAME_1_HW_I2C oled(U8G2_R0);

void setup() {
  Serial.begin(9600);
  dht.begin();
  oled.begin();
}

void loop() {
  float temp = dht.readTemperature();

  Serial.print("Temperature: ");
  Serial.print(temp);
  Serial.println(" C");

  oled.firstPage();
  do {
    oled.setFont(u8g2_font_4x6_tr);
    oled.drawStr(6, 16, "Temperature");

    oled.setFont(u8g2_font_t0_12b_tr);
    oled.setCursor(86, 19);
    oled.print(temp);
  } while (oled.nextPage());

  delay(2000);
}
```

**Result: confirmed working.** Serial output printed reliably every loop, and the OLED displayed accurate values with no gibberish pixels.

Note: `oled.print(temp)` (u8x8-style) and `oled.drawStr()` (u8g2-native) can be mixed within the same `firstPage()`/`nextPage()` block — this isn't exclusive to one API style, which wasn't obvious going in.

## Conclusion

| Hypothesis | Verdict |
|---|---|
| `dtostrf()` / float-to-string formatting | Ruled out |
| Static buffer reuse | Ruled out (safe pattern, `drawStr()` copies immediately) |
| `drawStr()` itself | Ruled out |
| DHT/U8g2 interrupt-timing conflict | Ruled out — was a plausible-looking red herring |
| **SRAM exhaustion from `_F_` full framebuffer** | **Confirmed root cause** |

## Remaining open items

- Not yet studied in depth: *why* exactly the `_1_` page-buffer variant avoids the SRAM pressure at the implementation level (deferred — noted as a "later" topic, not urgent).
- Humidity, heat index, and the full multi-line layout still need to be re-added on top of the working page-buffer minimal version, one piece at a time.
- The float-to-string helper function (with the `buf[10]` fix) hasn't yet been reintroduced into the `_1_`-based version — was intentionally left out of the minimal test to isolate the page-buffer mechanism alone.
