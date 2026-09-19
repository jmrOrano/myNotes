| Protocol | Basic idea                           | Typical pins         |
| -------- | ------------------------------------ | -------------------- |
| **I²C**  | Shared bus, address-based            | SDA + SCL            |
| **SPI**  | Fast serial communication            | MOSI, MISO, SCK, CS  |
| **UART** | Serial communication between devices | TX + RX              |
| **GPIO** | Direct HIGH/LOW signals              | Usually 1+ GPIO pins |

### ==I^2C (Inter-Integrated Circuit)==
---
I²C is a ***communication protocol*** that allows devices to communicate using only two communication lines: 

- ==`**SDA** — Serial Data Line`== → carries data sequentially
- ==`**SCL** — Serial Clock Line`== → provides timing/clock

You **can have multiple I²C devices** connected to the same SDA/SCL lines:

```
             ┌── OLED
SDA ─────────┼── Sensor
             └── LCD

SCL ─────────┼── OLED
             ├── Sensor
             └── LCD
```
- In uno r3 board the A4 is connected to SDA dedicated header pin
- A5 is connected to SCL dedicated header pin
- Multiple devices can share SDA/SCL as long as they support **I²C** and are properly configured on the bus.
- A non-I²C component should **not normally be connected to an SDA/SCL line**, because the line is already being used for I²C communication.


==**Serial Data Line in I^2C**==
- In some communication protocol like SPI—that uses `MOSI` and `MISO` as data directions for SDA.
- But in I2C communication protocol the data path is: 
- `Shared bidirectional path` — one shared data path can be used in both directions, one at a time.
- - `Sequential transmission` — data bits are transmitted one after another rather than simultaneously.
```
TIME 1:
Arduino ─────→ OLED
     DATA

TIME 2:
Arduino ←───── OLED
     DATA
```

**==Serial Clock Line==**
`Timing/synchronization signal` — provides a shared timing reference that tells the communicating devices when to read/sample each bit of data on SDA.
SCL solves the ambiguity of continuous data by providing the timing points where the receiver knows, “ito ang isang bit.”

>Imagine a continous stream of data in SDA but with no SCL:
```
0 1 0 1 1 0—————>
? ? ? ? ? ?
```
The receiver can see the `0`s and `1`s, but it does not have a timing reference  
to know exactly **when to read each bit**.

>SCL provides those timing points:
```
SCL:   ↑   ↑   ↑   ↑   ↑   ↑
       │   │   │   │   │   │
SDA:   0   1   0   1   1   0
       │   │   │   │   │   │
     read read read read read read
```

TLDR: It answers/solve the problem *"Where/When the bit begins to be read?"*


> **I^2C Backpack**
> An I²C backpack acts as a **translator/interface** between the microcontroller and a device that normally requires many pins.

Example is : 16x2 LCD
Without I²C: 
- LCD requires multiple data/control connections (D0–D7, RS, RW, E, etc.) 
- This one by default uses a **parallel interface (HD44780)**, which can operate in 8bit mode or 4bit mode
- Many Arduino pins are consumed

With I²C backpack:
```
Arduino 
    │ 
    ├── SDA 
    └── SCL 
↓ I²C Backpack 
↓ 16×2 LCD
```

