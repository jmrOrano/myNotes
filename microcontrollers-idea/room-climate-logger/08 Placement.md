Up: [[00 Index]]

# Placement
---

## Where is the best location and placement for the sensor to accurately represent my room's ambient temperature and humidity?
---
Listed below are what i think the important variables for this question.
`Room dimension` — 320cm x 280cm and 11ft height
`Appliances & Furniture` :
	Portable AC (placed near the window at height of 27in)
	Electric Fan (height at 45inch placed next to window pointing towards inside)
	Laptop,
	 UPS
	Light Bulb (installed exactly in the middle of the room)
	Bed   – with dimension of 56in x 80in with height of 15in
`Windows` — Yes, only 1 at a dimension of 37in x 61in
`Others` — Me, sometimes in the room, sometimes not in the room

**Answer**: Best place is `1.2 –1.5m` above the floor somwhere near the middle, but not directly in the path of airflow.
Well, its easier to say but i dont know how to place it in the middle of my room.

## Placement problem
---
>Sensor placement. The  easiest option i have right now is to place it near the wall by sticking it and powering it by powerbank. But this could affect the entire data quality.
>I can look for longer cables to hang the sensor below the ceiling while the microcontroller is  attach and sticking against the wall but im not sure if a cable with length like 1m for this project even exist.

**Update**: Longer, suitable sensor cables do exist. The final choice must be compatible with each module, especially the SPS30 connector, and should be tested during development. This makes representative mid-room placement feasible while keeping the ESP32 itself mounted on the wall.

**Plan**: Will test sensor placement for about 1-3 days to determine the best spot before committing to a permanent position. Results go in [[placement-test]].

**Addition**: track any physical setup changes (e.g. "moved sensor away from AC vent on [date]") in a log/metadata field — these can skew interpretation later if the context isn't recorded. This goes in the event log, see [[07 Data Design]].

## To consider *(added during review)*
---
- Keep the SHT sensor physically separated from the ESP32, SCD41, and SPS30. Their heat and fan airflow could bias the temperature/RH reading.
