[English](README.md) · [العربية](i18n/README.ar.md) · [Español](i18n/README.es.md) · [Français](i18n/README.fr.md) · [日本語](i18n/README.ja.md) · [한국어](i18n/README.ko.md) · [Tiếng Việt](i18n/README.vi.md) · [中文 (简体)](i18n/README.zh-Hans.md) · [中文（繁體）](i18n/README.zh-Hant.md) · [Deutsch](i18n/README.de.md) · [Русский](i18n/README.ru.md)


# digiOBSCURA - A DIY Image Sensor and Digital Camera 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#hardwarefirmware-architecture-current)

> A fully open DIY imaging stack: custom sensor PCB, MCU board, firmware, webcam viewer, and printable enclosure parts.

## Overview
`digiOBSCURA` is a DIY 32x32 imaging system built around a custom phototransistor array and a SAMD21-based controller.

This repository contains:
- Firmware for standalone camera capture to SD card.
- Firmware + Processing sketch for serial webcam-style live viewing.
- Full KiCad source files for both boards (`image_sensor` and `mcu`) plus production Gerbers.
- Mechanical assets (`.f3d`, `.stl`) for enclosure/lens/tripod components.
- Design notes and staged upgrade roadmap documents in [`docs/`](./docs/).

### At a Glance
| Area | Current implementation |
|---|---|
| Imaging array | `32x32` custom phototransistor matrix (`1024` pixels) |
| Multiplexing | `2 x ADG732` (row + column selection) |
| Controller | `SAMD21G18A` |
| Camera output | SD card BMP files (`DGO_0000.bmp`, auto-incremented) |
| Live mode | Serial (`115200`) + Processing host renderer |
| Design assets | KiCad + Gerbers + BOM artifacts + `F3D`/`STL` files |

## Demo (Canonical Existing Content) 🎬
Check out the video:  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

Find the BOM for both boards here: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Features
- 32x32 (1024-pixel) custom phototransistor imaging array.
- Dual ADG732 mux addressing for row/column pixel selection.
- Standalone camera workflow:
  - OLED UI.
  - Shutter input.
  - SD card output (`DGO_0000.bmp`, auto-incremented).
- Webcam workflow:
  - Arduino firmware responds to serial pixel requests at `115200`.
  - Processing app renders a live grayscale 32x32 view.
- Open hardware source:
  - KiCad schematics/PCBs.
  - Production outputs and BOM artifacts.
  - Printable mechanical files.

## Project Structure

```text
CustomSensor/
|- README.md
|- LICENSE
|- Firmware/
|  |- digiOBSCURA_camera/digiOBSCURA_camera.ino
|  |- digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino
|  `- digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde
|- docs/
|  |- overview.md
|  |- 01-firmware-differencing.md
|  |- 02-comparator-event-mode.md
|  |- 03-bias-and-buffer-upgrade.md
|  `- 04-full-event-sensor-redesign.md
|- PCB/
|  |- image_sensor/
|  `- mcu/
|- PCB-Production/
|- F3D/
|- STL/
|- logo/
`- i18n/
```

## Hardware/Firmware Architecture (Current)
Based on [`docs/overview.md`](./docs/overview.md):

| Subsystem | Details |
|---|---|
| Sensor matrix | `1024 x ALS-PT19` phototransistors |
| Addressing | `2 x ADG732` analog muxes (row + column) |
| Core board | `SAMD21G18A` |
| Power path | `18650` battery, `MCP73831` charger, `3.3 V` regulation |
| Signal path | `OUTCOL` is driven high by MCU GPIO; selected sensor path is read on `OUTROW` via ADC |
| Capture behavior | Camera firmware performs full-frame scans and writes 24-bit BMP files |

## Prerequisites

### Hardware
- Assembled `image_sensor` and `mcu` boards (or equivalent wiring).
- SAMD21-compatible target board matching the pin assignments used in firmware.
- MicroSD card (camera mode).
- 64x48 SSD1306 OLED (camera mode, 4-wire software SPI in current sketch).
- Shutter button/switch input.

### Software
- Arduino IDE (or Arduino CLI) with a SAMD21 board package.
- Required Arduino libraries for camera firmware:
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/runtime for [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/).

## Installation

1. Clone this repository:

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. Open one firmware sketch in Arduino IDE:
- Camera mode: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Webcam mode: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Select the correct SAMD21 board/port and upload.

4. For webcam mode, open the Processing sketch:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Usage

### Camera Mode (SD snapshot workflow) 📸
1. Flash `digiOBSCURA_camera.ino`.
2. Insert SD card.
3. On boot, firmware checks filenames and finds the next free `DGO_####.bmp`.
4. Press shutter input.
5. Device captures, writes BMP to SD, and shows a thumbnail preview on OLED.

### Webcam Mode (host-rendered live view) 🖥️
1. Flash `digiOBSCURA_Webcam_Arduino.ino`.
2. Connect over USB serial (`115200`).
3. In Processing sketch, set the correct serial port (the file currently has `String portName = "COM32"`).
4. Run the Processing sketch to poll pixel values and draw the 32x32 frame.

## Configuration Notes
Current firmware-level defaults from source:

| Setting | Current value / behavior |
|---|---|
| Resolution | Fixed `32x32` |
| ADC resolution | `analogReadResolution(12)` |
| Camera-mode averaging | `avgCount = 10` (increases quality, lowers frame speed) |
| SD CS pin (camera sketch) | `cardPin = 21` |
| Serial baud (webcam path) | `115200` |
| I/O mapping | Hardcoded arrays/variables (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, etc.) |

If your hardware wiring differs, update pin definitions near the top of each `.ino` file before flashing.

## Examples

### Open and edit the camera firmware
```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Open and edit the Processing webcam viewer
```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### Open PCB sources in KiCad
- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## Development Notes
- There is no unified build system (`Makefile`, `platformio.ini`, `package.json`, etc.) in this repository.
- Development currently spans Arduino + Processing + KiCad + mechanical CAD files.
- `PCB/image_sensor/README.md` and `PCB/mcu/README.md` appear stale/template-like and are not currently authoritative project docs.
- `i18n/` exists and is currently empty; translations are planned but not yet committed.

## Documentation and Roadmap
The `docs/` directory contains staged technical evolution notes:

| Document | Focus |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | Firmware-only temporal differencing/events |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | Comparator-assisted event detection |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | Analog front-end/bias improvements |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | Longer-term event-sensor redesign options |

## Troubleshooting
| Symptom | Checks |
|---|---|
| SD card not detected in camera mode | Confirm wiring and `cardPin` (`21` in current sketch). Reformat SD card (FAT/FAT32) and retry. |
| Blank/noisy image data | Verify `OUTCOL`/`OUTROW` wiring and mux control pins. Check power integrity and sensor board connections. |
| Processing viewer shows no data | Confirm serial baud `115200` in both Arduino and Processing. Update `portName` from `COM32` to your actual port. |
| Very slow capture | Expected with full 32x32 scan and averaging (`avgCount=10` by default). |

## Support / Sponsor
From firmware headers:

- Creator: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

If you build on this project, consider supporting the original creator.

## Contributing
Contributions are welcome. Suggested contribution areas:

- Hardware bring-up notes and verified pin maps.
- Toolchain/version validation (Arduino core, libraries, Processing version).
- Documentation updates, especially wiring diagrams and calibration workflow.
- `i18n/` README translations once language targets are finalized.

For changes touching hardware or firmware behavior, include:
- Test setup details.
- Board revision context.
- Before/after behavior notes.

## License
- Repository license file: [`LICENSE`](./LICENSE) (GPL-3.0 text present in repo).
- Firmware header note: current `.ino` headers state `cc-by-sa-nc-3.0`.

Important note: these two license signals are different. Until clarified by maintainers, treat licensing as requiring project-owner confirmation for redistribution/commercial use.

## Acknowledgements
- Sean Hodgins / digiOBSCURA project work and public documentation/video.
- Open hardware and maker community resources that make DIY sensor experiments possible.
