# Growlink Terralink Substrate Sensor — ESPHome Integration via M5Stack Atom

# Pics

<img width="265" height="473" alt="image" src="https://github.com/user-attachments/assets/efe80180-a6d1-4ad8-b45e-2f1ecb08d310" />
<img width="636" height="348" alt="image" src="https://github.com/user-attachments/assets/ac388d9d-5bce-43cd-bb30-e25c32d05460" />
<img width="515" height="653" alt="image" src="https://github.com/user-attachments/assets/11ebe0a6-d889-43af-a328-31ce9532aeae" />


## Overview
This guide documents how to connect and operate the **Growlink Terralink** substrate sensor directly on a **M5Stack Atom (ESP32)** running **ESPHome**.  
No external converters or breakout hardware are required. The sensor communicates over a single half-duplex SDI-12 line handled entirely in software.
What this means is that you don't need the Growlink cloud based controller to use the sensor. Instead you can buy an M5Stack Atom for $7.50 and start using it. https://shop.m5stack.com/products/atom-lite-esp32-development-kit


## Hardware Summary

| Component | Purpose |
|----------|---------|
| M5Stack Atom (ESP32) | Controller running ESPHome firmware |
| Growlink Terralink Sensor | Measures VWC, bulk EC, and temperature |
| 5 V USB power | Powers Atom and sensor |
| Shared Ground | Reference for signal and power |

**Sensor electrical characteristics**
- Protocol: SDI-12 v1.3
- Supply voltage: 3.6–16 V DC (5 V tested OK in this setup)
- Measurement current: ~10 mA for ~150 ms

---

## Wiring

| Terralink Wire | Connect to Atom Pin | Function |
|----------------|---------------------|----------|
| Red            | 5 V                 | Sensor power |
| White          | GPIO 26             | SDI-12 data (TX/RX half-duplex) |
| Bare           | GND                 | Common ground |

Notes:
- SDI-12 uses one bidirectional data wire.
- UART on GPIO 26 must be 1200 baud, 7-E-1, half-duplex, inverted.
- Use shielded cable if runs are long or the environment is noisy.

---

## Software

Use the maintained ESPHome configuration here (do not paste YAML into this README):
- Config: https://github.com/JakeTheRabbit/Grownlink-Terralink-Substrate-Sensor-for-ESP-Home/blob/main/terralink_coco.yaml

That YAML includes:
- Wi-Fi and OTA
- `ssieb` SDI-12 external component + UART half-duplex on GPIO 26
- Sensors for εb, bulk EC (σb), temperature (°C)
- Template outputs for VWC (raw), VWC (aligned), pore-water EC (Hilhorst)

---

## Deployment Steps
1. Clone/download the repo with the YAML.
2. Flash the Atom via USB:

   $ esphome run terralink_coco.yaml

3. Join Wi-Fi; verify device appears in Home Assistant.
4. Watch ESPHome logs to confirm SDI-12 reads.
5. Calibrate VWC (raw/aligned) as needed (below).

---

## Operational Notes
- SDI-12 supports multiple sensors (addresses 0–9) on the same data line; daisy-chain if required.
- Keep total cable length conservative (≤10 m preferred) unless you’ve validated longer runs.
- Default update interval is ~30 s (adjust in YAML if needed).
- Pore-water EC is temperature-normalized (Hilhorst model) in the provided templates.

---

## Maintenance
- Monthly: inspect cable insulation, strain relief, and GND continuity.
- Between crop cycles: clean sensor with deionized water; avoid solvents.
- Use dielectric grease on connectors where corrosion risk is high.

---

## Easy Calibration Guide (For Normal Humans)

This explains calibration in plain English. Do this after you see readings in Home Assistant.

### 1. What Calibration Does
Makes the numbers on screen match what “dry” and “wet” actually mean in your media.

### 2. What You’ll Need
- Terralink sensor working in ESPHome
- Your media (rockwool, coco, soil, etc.)
- Two samples: one dry, one fully wet (soaked then drained)
- A cup/pot and water

### 3. Get Your “Dry” Reading
1. Use media that’s clearly dry (no recent watering).
2. Insert the sensor fully (rods buried).
3. Wait ~1 minute.
4. Note TL VWC (raw) in Home Assistant — this is your dry baseline.

### 4. Get Your “Wet” Reading
1. Soak the media until dripping, drain ~10 minutes.
2. Insert the same sensor.
3. Wait ~1 minute.
4. Note TL VWC (raw) — this is your wet baseline.

### 5. Sanity Check
- Dry should be low (often ~5–20% depending on media).
- Wet should be high (often ~60–90% depending on media).
If those look wrong, recheck insertion, drainage, and contact with media.

### 6. Optional Fine-Tuning (match a reference probe)
If you compare against a reference probe and want your numbers to match it:
- Put both sensors in the same media.
- If your VWC reads 60% and the reference says 48%, use a scale of 0.80 in your VWC (aligned) multiplier inside the YAML.

### 7. Done
You now have practical dry/wet anchors. Day-to-day readings should fall between them and make operational sense.

---

## Example: Quick Real-World Calibration Walkthrough (Coco)

### Step 1 — Dry Sample
- Insert sensor in dry coco (crumbly, light).
- Wait ~1 minute.
- Example: Dry coco = 11.5% VWC (your dry baseline).

### Step 2 — Wet Sample
- Soak coco to dripping, drain 10 minutes.
- Insert sensor, wait ~1 minute.
- Example: Wet coco = 78.4% VWC (your wet baseline).

### Step 3 — Confirm Range

| Condition      | VWC reading |
|----------------|-------------|
| Dry coco       | ~11 %       |
| Fully wet coco | ~78 %       |

### Step 4 — Optional Tuning
If a reference probe says 48% while yours reads 60%, set the VWC (aligned) scale to 0.80 in the YAML.

### Step 5 — Done
- 15–25% → getting dry
- 60–80% → well-watered
Numbers now map to reality for coco. Same procedure works for rockwool/soil with different dry/wet values.


## Maintenance
- Inspect sensor cable insulation and GND continuity monthly in high-humidity environments.  
- Clean the sensor with deionized water between crop cycles.  
- Use dielectric grease on connectors to prevent corrosion.
