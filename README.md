# Terralink SDI‑12 → ESP32 (M5Stack Atom) with ESPHome  
_Clean VWC, Temperature, and pore‑water EC for Home Assistant (Coco Coir calibration)_

This project shows how to read a **Growlink Terralink** soil moisture sensor over **SDI‑12** with an **ESP32 (M5Stack Atom)** using ESPHome, and expose **clean** Home Assistant entities:

- **VWC** (Volumetric Water Content) — `%`
- **Temperature** — `°C`
- **pwEC** (pore‑water electrical conductivity) — `dS/m`

It uses **single‑wire, half‑duplex on one GPIO** (no extra parts), matching the quick setup you already have.

---

## What you get

- Minimal wiring: **GND / 5 V / one data wire**
- Proper SDI‑12 framing (1200 baud, **7‑E‑1**, inverted, half‑duplex)
- Coco‑coir (soilless) calibration polynomial for **VWC**
- Hilhorst‑style **pore EC** calculation with temperature compensation to 25 °C
- Only three user‑facing sensors in Home Assistant; raw values are hidden

---

## Parts

- **ESP32**: M5Stack **Atom** (or similar ESP32 board)
- **Sensor**: Growlink **Terralink** SDI‑12 soil probe
- **USB‑C cable** for the Atom
- **Jumper/Grove pigtail** (to break out the sensor’s red/black/white leads)

> _Optional (recommended for production):_ a simple inverting level shifter or transistor stage to guarantee 3.3 V on the ESP32 pin if your SDI‑12 idle high is ~5 V. Many people run direct at 1200 baud without issues, but check with a meter if in doubt.

---

## Wiring (no extra parts)

We use the **Atom’s 5 V** for power and a single **GPIO26** for SDI‑12 data (TX/RX shared internally by the driver in half‑duplex mode).

