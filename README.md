# USB-C Power Delivery (PD) Interface Board

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![KiCad](https://img.shields.io/badge/KiCad-8.0-brightgreen.svg)
![Status](https://img.shields.io/badge/status-v1.0-orange.svg)

A compact **USB Type-C Power Delivery sink board** designed in KiCad, built around the **HUSB238** PD controller IC. This board negotiates USB PD voltage profiles (5V / 9V / 12V / 15V / 20V) from any USB-C PD charger and outputs selectable DC power through a screw terminal.

---

## ✨ Features

- **USB-C PD Negotiation** — Supports USB PD 2.0/3.0, auto-negotiates up to 20V / 5A (100W)
- **HUSB238 Controller** — Standalone PD sink IC, no firmware required
- **Voltage Selection** — Selectable via I²C or hardware pins (VSEL0/VSEL1)
- **I²C Interface** — 4-pin header for external MCU control and voltage readback
- **Power LED Indicator** — Green LED shows active power output
- **Screw Terminal Output** — 5.08mm pitch terminal block for easy wiring
- **Compact Form Factor** — 40mm × 30mm, 2-layer PCB
- **M2.5 Mounting Holes** — 4 corner mounting holes for secure enclosure mounting

---

## 📐 Board Specifications

| Parameter | Value |
|---|---|
| **Board Size** | 40 mm × 30 mm |
| **Layers** | 2 (F.Cu + B.Cu) |
| **PCB Thickness** | 1.6 mm |
| **Copper Weight** | 1 oz (35 µm) |
| **Surface Finish** | HASL (lead-free recommended) |
| **Min Track Width** | 0.2 mm (power: 1.0 mm) |
| **Min Clearance** | 0.2 mm |
| **Min Via** | 0.8 mm / 0.4 mm drill |
| **Input Connector** | USB Type-C receptacle (GCT USB4105) |
| **Output Connector** | 2-pin screw terminal (5.08 mm pitch) |

---

## 🔧 Schematic Overview

```
                          ┌──────────────┐
   USB-C          CC1 ───►│              │
  Connector       CC2 ───►│   HUSB238    │──► VOUT_EN ──► Power Output
   (J1)          VBUS ───►│  PD Sink IC  │              ├── Screw Terminal (J2)
                  GND ───►│     (U1)     │──► INT       ├── LED Indicator (D1)
                          │              │              └── Output Cap (C3)
                          │   SDA ◄──────│──── R1 ──► I2C Header
                          │   SCL ◄──────│──── R2 ──► (J3)
                          └──────────────┘
                               │
                          C1 ──┤── C2
                         10µF  │  100nF
                              GND
```

---

## 📋 Bill of Materials (BOM)

| Ref | Component | Value | Package | Qty | Notes |
|-----|-----------|-------|---------|-----|-------|
| J1 | USB-C Receptacle | — | GCT USB4105-GF-A | 1 | 16-pin USB 2.0 type |
| U1 | PD Controller | HUSB238 | DFN-10 (3×3mm) | 1 | Hynetek USB PD Sink |
| C1 | Capacitor | 10 µF / 25V | 0805 | 1 | X5R MLCC, VBUS decoupling |
| C2 | Capacitor | 100 nF | 0402 | 1 | HF decoupling |
| C3 | Capacitor | 22 µF / 25V | 1206 | 1 | X5R MLCC, output bulk |
| R1 | Resistor | 4.7 kΩ | 0402 | 1 | I²C SDA pull-up |
| R2 | Resistor | 4.7 kΩ | 0402 | 1 | I²C SCL pull-up |
| R3 | Resistor | 1 kΩ | 0402 | 1 | LED current limiter |
| D1 | LED | Green | 0603 | 1 | Power indicator |
| J2 | Screw Terminal | 2-pin | 5.08 mm pitch | 1 | Power output |
| J3 | Pin Header | 1×4 | 2.54 mm pitch | 1 | I²C (VCC/SDA/SCL/GND) |
| H1–H4 | Mounting Hole | M2.5 | 2.7 mm drill | 4 | Corner mounting |

---

## 🛠️ Getting Started

### Prerequisites

- **[KiCad 8.0+](https://www.kicad.org/download/)** (free & open-source EDA)

### Opening the Project

1. Clone this repository:
   ```bash
   git clone https://github.com/ADITHYANWUU2/USB-C-PD-Board.git
   cd USB-C-PD-Board
   ```

2. Open in KiCad:
   - Launch KiCad
   - **File → Open Project** → select `USB-C-PD-Board.kicad_pro`

3. View the schematic:
   - Double-click `USB-C-PD-Board.kicad_sch` in the project tree

4. View the PCB layout:
   - Double-click `USB-C-PD-Board.kicad_pcb` in the project tree

---

## 📂 Project Structure

```
USB-C-PD-Board/
├── USB-C-PD-Board.kicad_pro      # KiCad project file
├── USB-C-PD-Board.kicad_sch      # Schematic
├── USB-C-PD-Board.kicad_pcb      # PCB layout
├── BOM.csv                        # Bill of Materials
├── gerber/                        # Gerber output files (for fabrication)
├── .gitignore                     # Git ignore rules
├── LICENSE                        # MIT License
└── README.md                     # This file
```

---

## ⚡ Voltage Selection

The HUSB238 supports voltage selection via hardware pins or I²C:

### Hardware Pin Selection (VSEL0 / VSEL1)

| VSEL1 | VSEL0 | Output Voltage |
|-------|-------|----------------|
| LOW | LOW | 5V |
| LOW | HIGH | 9V |
| HIGH | LOW | 12V |
| HIGH | HIGH | 20V |

### I²C Control (Address: 0x08)

Connect an MCU (Arduino, ESP32, STM32, etc.) to the I²C header (J3):

```c
#include <Wire.h>

#define HUSB238_ADDR 0x08

void setup() {
    Wire.begin();
    // Request 12V output
    Wire.beginTransmission(HUSB238_ADDR);
    Wire.write(0x01);  // Voltage register
    Wire.write(0x03);  // 12V setting
    Wire.endTransmission();
}
```

---

## 🏭 Manufacturing

### Generate Gerber Files

1. Open the PCB layout in KiCad
2. **File → Plot** (or **Fabrication → Plot**)
3. Select output directory: `gerber/`
4. Check layers: `F.Cu`, `B.Cu`, `F.SilkS`, `B.SilkS`, `F.Mask`, `B.Mask`, `Edge.Cuts`
5. Click **Plot**
6. Click **Generate Drill File** → **Generate**

### Recommended Fab Houses

- [JLCPCB](https://jlcpcb.com) — \$2 for 5 boards
- [PCBWay](https://www.pcbway.com) — Good quality, affordable
- [OSH Park](https://oshpark.com) — USA-based, purple boards
- [LCSC](https://www.lcsc.com) — Components sourcing

### Fab Settings

| Setting | Value |
|---------|-------|
| Layers | 2 |
| Thickness | 1.6 mm |
| Surface Finish | HASL (lead-free) |
| Copper Weight | 1 oz |
| Min Hole Size | 0.4 mm |
| Solder Mask | Green |
| Silkscreen | White |

---

## ⚠️ Design Notes

1. **VBUS trace width**: Use ≥1.0 mm traces for VBUS/power paths to handle up to 5A
2. **Decoupling caps**: Place C1 and C2 as close to the HUSB238 VBUS pin as possible
3. **Ground pour**: Use copper pour on both layers connected to GND for thermal relief
4. **ESD protection**: The USB4105 connector includes built-in shield grounding
5. **CC line routing**: Keep CC1/CC2 traces short and away from noisy signals
6. **Thermal pad**: Solder the HUSB238 exposed pad (EP) to the ground plane with thermal vias

---

## 📜 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -am 'Add new feature'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## 📚 References

- [HUSB238 Datasheet (PDF)](https://www.hynetek.com/uploadfiles/site/219/news/HUSB238-DS-RevE.01-20230331.pdf)
- [USB Type-C Specification](https://www.usb.org/document-library/usb-type-cr-cable-and-connector-specification-revision-21)
- [USB PD Specification](https://www.usb.org/document-library/usb-power-delivery)
- [GCT USB4105 Datasheet](https://gct.co/files/specs/usb4105-spec.pdf)
- [KiCad Documentation](https://docs.kicad.org/)
