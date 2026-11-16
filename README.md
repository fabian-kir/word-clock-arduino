# Word Clock (Arduino, WS2812B)

A German word clock built as a personal gift: instead of digits it lights up words on a 12×12 letter grid,
e.g. **„ES IST ZEHN VOR HALB ACHT“**. It also greets the time of day („GUTEN MORGEN / MITTAG / TAG / ABEND“)
and shows the recipient's name with a heart.

Hardware, firmware, wiring and soldering are all my own. A photo will follow with version 2, which is already planned.

## Hardware

| Part | Details |
|---|---|
| Microcontroller | Arduino Uno |
| LEDs | WS2812B strip, 144 LEDs, cut and laid out as a 12×12 matrix |
| Real-time clock | DS1302 module (keeps time without power) |
| Input | 2 push buttons (mode / action) |
| Enclosure | Picture frame (Woolworth) |
| Front plate | Chipboard with the letters cut out on a laser cutter, spray-painted black |
| Wiring | Hand-soldered connections between strip segments, RTC and buttons |

**Pinout**

| Signal | Uno pin |
|---|---|
| LED data | D5 |
| DS1302 CLK / DAT / RST | D6 / D7 / D8 |
| Mode button | D10 (INPUT_PULLUP) |
| Action button | D11 (INPUT_PULLUP) |

## Features

- Time in German words with 5-minute resolution, including "fünf vor halb" and „ein Uhr“ vs. „eins“
- Greeting depending on the time of day
- 34 selectable colours and adjustable brightness, both stored in EEPROM so they survive power loss
- Automatic night dimming from 22:00 to 07:00
- Easter egg: the name row turns red at 11:11
- Settings menu via two buttons. The clock spells out the current mode on the grid itself:
  - **FARBE**: change colour
  - **HELL**: change brightness
  - **STUNDE / MINUTEN**: set the time
- LED self-test on startup

## Build & flash

1. Install the libraries **FastLED** and **Rtc by Makuna** via the Arduino Library Manager.
2. Open `word-clock-arduino.ino` in the Arduino IDE (the folder must be named `word-clock-arduino`, which it is after `git clone`), select *Arduino Uno*, upload.
3. On first boot (or if the RTC battery was empty) the clock takes the compile time. Fine-tune it with the STUNDE/MINUTEN modes.

## Debugging story

The first version had a bug that took me a while to track down: around 7 or 8 o'clock the clock jumped
into an undefined state, and adding unrelated lines of code changed whether it happened.
The cause was an off-by-one in the LED map: the word ACHT was defined as LEDs 140–**144**, but the
strip only has indices 0–143. Every time "acht" was shown (from about 7:23 to 8:22 and 19:23 to 20:22),
the firmware wrote past the end of the LED buffer and overwrote the variable holding the clock's state.
It's a classic example of undefined behaviour: the symptoms showed up somewhere completely different
from the bug.

I found and fixed it shortly before the clock had to be finished, but the fix only ever made it onto
the clock itself, not into this repository. The code here has since been brought back in line.

A second fix: the RTC was reset to a hard-coded date on every boot, so the time was lost after each
power cut. It is now set only when the RTC reports an invalid time.

## Version 2 (planned)

Version 2 will come with photos and build documentation.
