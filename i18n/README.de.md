[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner — apps and four buddies](https://lazying.art/media/brand/lazyingart-intro.gif)](https://lazying.art)

# digiOBSCURA - Eine DIY-Bildsensor- und Digitalkamera-Lösung 📷

[![Firmware-Lizenz: CC BY-NC-SA 3.0 (Firmware-Kopfzeile)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repository-Lizenz](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](../LICENSE)
[![Projektart](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#ueberblick)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#funktionen)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#hardwarefirmware-architektur-aktuell)
[![Mitwirkung](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#mitwirken)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> Ein vollständig offenes DIY-Bildsystem: benutzerdefinierte Sensorplatine, MCU-Board, Firmware, Webcam-Viewer und druckbare Gehäuseteile.

## 📚 Inhaltsverzeichnis

- [Ueberblick](#ueberblick)
- [Demo](#demo)
- [Funktionen](#funktionen)
- [Projektstruktur](#projektstruktur)
- [Hardware/Firmware-Architektur (Aktuell)](#hardwarefirmware-architektur-aktuell)
- [Voraussetzungen](#voraussetzungen)
- [Installation](#installation)
- [Benutzung](#benutzung)
- [Konfigurationsnotizen](#konfigurationsnotizen)
- [Beispiele](#beispiele)
- [Entwicklungsnotizen](#entwicklungnotizen)
- [Fehlerbehebung](#fehlerbehebung)
- [Fahrplan und technische Notizen](#fahrplan-und-technische-notizen)
- [Mitwirken](#mitwirken)
- [Support](#%E2%9D%A4%EF%B8%8F-support)
- [Lizenz](#lizenz)

## Ueberblick 🛰️

`digiOBSCURA` ist ein DIY-32x32-Bildsystem, aufgebaut auf einem eigens entwickelten Phototransistor-Array und einem SAMD21-basierten Controller.

Dieses Repository enthält:
- Firmware für Standalone-Kameraaufnahmen auf SD-Karte.
- Firmware + Processing-Sketch für Live-Ansicht im Webcam-Stil über Serial.
- Vollständige KiCad-Quelldateien für beide Boards (`image_sensor` und `mcu`) sowie Produktionsdaten (Gerber).
- Mechanische Dateien (`.f3d`, `.stl`) für Gehäuse, Objektiv und Stativkomponenten.
- Konstruktionsnotizen und ein gestuftes Upgrade-Roadmap-Dokument in [`docs/`](./docs/).

## Schnellueberblick 📌

| Punkt | Details |
| --- | --- |
| Projektumfang | Kamera + Firmware + Viewer + Mechanikdesign |
| Sensorarray | Benutzerspezifische Phototransistor-Matrix `32x32` (`1024`) |
| Hauptcontroller | `SAMD21G18A` |
| Bildausgabe | SD-Karten-BMP / Serielle Live-Frame-Übertragung |
| Ziel-Workflows | Standalone-Aufnahme • Webcam-Style-Vorschaumodus |

## Demo 🎬

Schau dir das Video an:

[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

Die Stückliste für beide Boards findest du hier: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Funktionen ✨

- 32x32 (1024-Pixel) benutzerdefiniertes Phototransistor-Bildarray.
- Doppelte ADG732-Mux-Adressen für Reihen-/Spaltenpixel-Auswahl.
- Standalone-Kamera-Workflow:
  - OLED-Status-UI.
  - Auslöse-Eingang.
  - SD-Karten-Ausgabe (`DGO_####.bmp`, automatisch hochgezählt).
- Webcam-Workflow:
  - Arduino-Firmware beantwortet serielle Pixelabfragen bei `115200`.
  - Processing-App rendert eine Live-Graustufen-Ansicht mit 32x32 Pixeln.
- Open-Hardware-Quellen:
  - KiCad-Schematics/PCBs.
  - Produktionsdaten und BOM-Artefakte.
  - Druckbare CAD- bzw. Gehäusedateien.

## Projektstruktur 🧱

```text
CustomSensor/
|- README.md
|- LICENSE
|- Firmware/
|  |- digiOBSCURA_camera/
|  |  |- digiOBSCURA_camera.ino
|  |- digiOBSCURA_Webcam_Arduino/
|  |  |- digiOBSCURA_Webcam_Arduino.ino
|  `- digiOBSCURA_Webcam_Processing/
|     `- digiOBSCURA_Webcam_Processing.pde
|- docs/
|  |- overview.md
|  |- 01-firmware-differencing.md
|  |- 02-comparator-event-mode.md
|  |- 03-bias-and-buffer-upgrade.md
|  `- 04-full-event-sensor-redesign.md
|- PCB/
|  |- image_sensor/
|  |  |- digiobscura.kicad_pcb
|  |  |- digiobscura.sch
|  |  |- sym-lib-table
|  |  `- fp-lib-table
|  `- mcu/
|     |- digiobscura_mcu.kicad_pcb
|     |- digiobscura_mcu.sch
|     |- sym-lib-table
|     `- fp-lib-table
|- PCB-Production/
|- F3D/
|- STL/
|- logo/
|- i18n/
|- .auto-readme-work/
|  |- pipeline-context.md
|  `- repo-structure-analysis.md
`- .git
```

## Hardware/Firmware-Architektur (Aktuell) 🧰

Basierend auf [`docs/overview.md`](./docs/overview.md):

| Teilsystem | Details |
|---|---|
| Sensormatrix | `1024 x ALS-PT19` Phototransistoren |
| Adressierung | `2 x ADG732` analoge Muxes (Reihe + Spalte) |
| Kernboard | `SAMD21G18A` |
| Stromversorgung | `18650`-Akku, `MCP73831`-Ladecontroller, `3.3 V`-Regelung |
| Signalpfad | `OUTCOL` wird per MCU GPIO auf High gesetzt; der gewählte Sensorkanal wird über `OUTROW` mit ADC eingelesen |
| Aufnahmeverhalten | Kamera-Firmware führt Vollbild-Scans durch und schreibt 24-Bit-BMP-Dateien |

## Voraussetzungen 🧰

### Hardware

- Fertige `image_sensor`- und `mcu`-Platinen (oder gleichwertige Verdrahtung).
- SAMD21-kompatibles Zielboard mit passenden Pin-Zuordnungen wie in der Firmware verwendet.
- MicroSD-Karte (Kamera-Modus).
- `64x48` SSD1306 OLED (Kamera-Modus, 4-wire Software-SPI im aktuellen Sketch).
- Taster/Schalter für Auslösung.

### Software

- Arduino IDE (oder Arduino CLI) mit SAMD21-Board-Unterstützung.
- Erforderliche Arduino-Bibliotheken für die Kamera-Firmware:
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/Runtime für [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/).

## Installation 🧩

1. Repository klonen:

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. Einen Firmware-Sketch in der Arduino IDE öffnen:
- Kameramodus: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Webcam-Modus: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Das passende SAMD21-Board und den richtigen Port wählen, dann hochladen.

4. Für den Webcam-Modus den Processing-Sketch öffnen:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Benutzung 🚀

### Kamera-Modus (SD-Schnappschuss-Workflow) 📸

1. `digiOBSCURA_camera.ino` flashen.
2. Eine FAT/FAT32-formatierte SD-Karte einlegen.
3. Board einschalten/neu starten; die Firmware sucht den nächsten freien Dateinamen `DGO_####.bmp`.
4. Aufnahme mit dem Auslöser starten.
5. Das Gerät nimmt auf, schreibt die BMP-Datei auf die SD-Karte und zeigt eine Miniaturvorschau auf dem OLED.

### Webcam-Modus (Host-gerenderte Live-Ansicht) 🖥️

1. `digiOBSCURA_Webcam_Arduino.ino` flashen.
2. Über USB-Serial mit `115200` verbinden.
3. In Processing den `String portName` auf deinen echten seriellen Port setzen (das Template nutzt `COM32`).
4. Processing-Sketch starten; die 32x32-Pixel werden abgefragt und ein graustufiges Live-Frame wird angezeigt.

## Konfigurationsnotizen 🛠️

Aktuelle Firmware-Standardeinstellungen aus dem Quellcode:

| Einstellung | Aktueller Wert / Verhalten |
|---|---|
| Auflösung | Fix auf `32x32` |
| ADC-Auflösung | `analogReadResolution(12)` |
| Mittelung im Kameramodus | `avgCount = 10` (höhere Qualität, niedrigere Bildrate) |
| SD-CS-Pin (Kamera-Sketch) | `cardPin = 21` |
| Serielle Baudrate (Webcam-Pfad) | `115200` |
| I/O-Zuordnung | Fest codierte Arrays/Variablen (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW` usw.) |

Wenn deine Verdrahtung abweicht, aktualisiere die Pin-Definitionen am Anfang jeder `.ino`-Datei vor dem Flashen.

## Beispiele 🧪

### Kamerafirmware öffnen und bearbeiten

```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Processing-Webcam-Viewer öffnen und bearbeiten

```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### PCB-Quellen in KiCad öffnen

- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## Entwicklungsnotizen 📘

- Dieses Repository hat kein einheitliches Build-System (`Makefile`, `platformio.ini`, `package.json`, etc.).
- Die Entwicklung umfasst aktuell Arduino + Processing + KiCad + mechanische CAD-Dateien.
- `PCB/image_sensor/README.md` und `PCB/mcu/README.md` enthalten derzeit eher Platzhalter-/Vorlage-Inhalte und sind nicht maßgeblich.
- `i18n/` enthält Übersetzungs-README-Dateien; einige sind unvollständig und können dem englischen Stand hinterherhinken.

## Fehlerbehebung 🧯

| Symptom | Überpruefungen |
|---|---|
| SD-Karte wird im Kameramodus nicht erkannt | Verdrahtung und `cardPin` (`21` im aktuellen Sketch) prüfen. SD-Karte neu formatieren (FAT/FAT32) und erneut versuchen. |
| Leere/rauschhafte Bilddaten | `OUTCOL`/`OUTROW`-Verdrahtung und Mux-Steuerpins prüfen. Stromversorgung und Sensorboard-Verbindung kontrollieren. |
| Processing-Viewer zeigt keine Daten | Sicherstellen, dass die serielle Baudrate `115200` in Arduino und Processing identisch ist. `portName` von `COM32` auf den tatsächlichen Port anpassen. |
| Sehr langsame Aufnahme | Erwartet bei vollständigem 32x32-Scan und Mittelung (`avgCount=10` standardmässig). |

## Fahrplan und technische Notizen 🗺️

Im Ordner `docs/` sind geplante und explorative technische Richtungen dokumentiert:

| Dokument | Fokus |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | Zeitliche Differenzbildung / Ereignisverarbeitung nur in der Firmware |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | Ereigniserkennung mit Hilfestellung durch Komparator |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | Verbesserungen an Analog-Frontend und Bias |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | Langfristige Optionen für den Event-Sensor-Umbau |

## Mitwirken 🤝

Beiträge sind willkommen. Empfohlene Mitwirkungsbereiche:

- Dokumentation zu Hardware-Setup und verifizierten Pin-Maps.
- Toolchain-/Versionsvalidierung (Arduino-Core, Bibliotheken, Processing-Version).
- Dokumentations-Updates, vor allem Schaltpläne und Kalibrierungsablauf.

- `i18n/`-README-Übersetzungen, sobald Sprachversionen finalisiert sind.

Bei Änderungen, die Hardware oder Firmware-Verhalten betreffen, bitte mitliefern:
- Testaufbau-Details.
- Board-Revisionskontext.
- Vorher/Nachher-Verhaltensnotizen.

## Lizenz

- Die `LICENSE` in diesem Repository verweist auf eine Verteilung unter GPL-3.0 auf Repository-Ebene.
- In den Firmware-Kopfzeilen wird zusätzlich `CC BY-NC-SA 3.0` für bestimmte Quelltexte erwähnt.

Beim Weiterverteilen von Binärdateien, Hardware-Artefakten oder Ableitungen prüfe bitte immer die jeweiligen Lizenzbedingungen der betroffenen Dateien und stelle die Kompatibilität mit der geplanten Verwendung sicher.


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
