# Growlink Terralink Substrate Sensor — ESPHome Integration via M5Stack Atom

## Overview
This guide documents how to connect and operate the **Growlink Terralink** substrate sensor directly on a **M5Stack Atom (ESP32)** running **ESPHome**.  
No external converters or breakout hardware are required. The sensor communicates over a single half-duplex SDI-12 line handled entirely in software.

---

## Hardware Summary

| Component | Purpose |
|------------|----------|
| M5Stack Atom (ESP32) | Controller running ESPHome firmware |
| Growlink Terralink Sensor | Measures volumetric water content (VWC), bulk EC, and temperature |
| 5 V USB power | Supplies both Atom and sensor |
| Shared Ground | Reference for signal and power |

**Sensor electrical characteristics**
- Protocol: SDI-12 v1.3  
- Supply voltage: 3.6 – 16 V DC (typical 12 V, stable 5 V tested OK)  
- Measurement current: ≈ 10 mA for 150 ms

---

## Wiring

| Terralink Wire | Connect to Atom Pin | Function |
|----------------|---------------------|-----------|
| **Red** | 5 V | Power input |
| **White** | GPIO 26 | SDI-12 data line (TX/RX half-duplex) |
| **Bare** | GND | Common ground |

Notes:
- The SDI-12 bus uses one bidirectional data wire.
- UART on GPIO 26 is configured for 1200 baud, 7-E-1, half-duplex, inverted logic.
- Use shielded cable if the environment is electrically noisy or cables exceed 10 m.

---

## Software

The working ESPHome configuration for this setup is maintained at:  
**<https://github.com/JakeTheRabbit/Grownlink-Terralink-Substrate-Sensor-for-ESP-Home/blob/main/terralink_coco.yaml>**

That YAML file contains:
- Wi-Fi and OTA setup  
- SDI-12 component definitions using the `ssieb/esphome_components` fork  
- UART half-duplex configuration on GPIO 26  
- Derived template sensors for:
  - Apparent permittivity (εb)
  - Bulk EC (σb)
  - Temperature (°C)
  - Computed VWC (raw and aligned)
  - Hilhorst pore-water EC (σp)

---

## Deployment Steps
1. Clone or download the repository containing the YAML configuration.  
2. Flash the Atom via USB using:

3. Connect the device to Wi-Fi and verify registration in Home Assistant.  
4. View live SDI-12 readings in the ESPHome logs to confirm communication.  
5. Calibrate the **VWC (aligned)** scaling coefficient if comparing against a reference probe (e.g. TEROS-12).

---

## Operational Notes
- The SDI-12 bus supports multiple sensors (addresses 0–9) daisy-chained on the same data line.  
- Keep total cable length under 10 m for reliable timing.  
- Readings update every 30 seconds by default.  
- Temperature-normalized EC and pore-water EC follow the METER Group Hilhorst model.

---

## Maintenance
- Inspect sensor cable insulation and GND continuity monthly in high-humidity environments.  
- Clean the sensor with deionized water between crop cycles.  
- Use dielectric grease on connectors to prevent corrosion.
