[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)



# digiOBSCURA - Ein DIY-Bildsensor und eine Digitalkamera 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#überblick)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#funktionen)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#hardwarefirmware-architektur-aktuell)

> Ein vollständig offener DIY-Imaging-Stack: benutzerdefinierte Sensor-PCB, MCU-Board, Firmware, Webcam-Viewer und druckbare Gehäuseteile.

## Überblick
`digiOBSCURA` ist ein DIY-32x32-Bildgebungssystem, das auf einem benutzerdefinierten Phototransistor-Array und einem SAMD21-basierten Controller aufbaut.

Dieses Repository enthält:
- Firmware für eigenständige Kameraaufnahmen auf SD-Karte.
- Firmware + Processing-Sketch für serielle Webcam-ähnliche Live-Ansicht.
- Vollständige KiCad-Quelldateien für beide Boards (`image_sensor` und `mcu`) sowie Produktions-Gerber.
- Mechanische Assets (`.f3d`, `.stl`) für Gehäuse-/Linsen-/Stativkomponenten.
- Designnotizen und stufenweise Upgrade-Roadmap-Dokumente in [`docs/`](./docs/).

### Auf einen Blick
| Bereich | Aktuelle Implementierung |
|---|---|
| Bildarray | `32x32` benutzerdefinierte Phototransistor-Matrix (`1024` Pixel) |
| Multiplexing | `2 x ADG732` (Zeilen- + Spaltenauswahl) |
| Controller | `SAMD21G18A` |
| Kameraausgabe | SD-Karten-BMP-Dateien (`DGO_0000.bmp`, auto-inkrementiert) |
| Live-Modus | Seriell (`115200`) + Processing-Host-Renderer |
| Design-Assets | KiCad + Gerber + BOM-Artefakte + `F3D`/`STL`-Dateien |

## Demo (Kanonischer vorhandener Inhalt) 🎬
Schau dir das Video an:  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

Die BOM für beide Boards findest du hier: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Funktionen
- 32x32 (1024-Pixel) benutzerdefiniertes Phototransistor-Bildarray.
- Duales ADG732-MUX-Addressing für Zeilen-/Spalten-Pixelauswahl.
- Eigenständiger Kamera-Workflow:
  - OLED-UI.
  - Auslöser-Eingang.
  - SD-Karten-Ausgabe (`DGO_0000.bmp`, auto-inkrementiert).
- Webcam-Workflow:
  - Arduino-Firmware beantwortet serielle Pixel-Anfragen mit `115200`.
  - Processing-App rendert eine Live-Graustufenansicht in 32x32.
- Open-Source-Hardware:
  - KiCad-Schaltpläne/PCBs.
  - Produktionsausgaben und BOM-Artefakte.
  - Druckbare mechanische Dateien.

## Projektstruktur

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

## Hardware/Firmware-Architektur (Aktuell)
Basierend auf [`docs/overview.md`](./docs/overview.md):

| Subsystem | Details |
|---|---|
| Sensormatrix | `1024 x ALS-PT19` Phototransistoren |
| Adressierung | `2 x ADG732` analoge MUXe (Zeile + Spalte) |
| Kernboard | `SAMD21G18A` |
| Strompfad | `18650`-Akku, `MCP73831`-Ladegerät, `3.3 V`-Regelung |
| Signalpfad | `OUTCOL` wird durch MCU-GPIO auf High gesetzt; der ausgewählte Sensorpfad wird über `OUTROW` per ADC eingelesen |
| Aufnahmeverhalten | Kamera-Firmware führt vollständige Frame-Scans aus und schreibt 24-Bit-BMP-Dateien |

## Voraussetzungen

### Hardware
- Zusammengebaute `image_sensor`- und `mcu`-Boards (oder gleichwertige Verdrahtung).
- SAMD21-kompatibles Zielboard passend zu den in der Firmware verwendeten Pin-Zuweisungen.
- MicroSD-Karte (Kameramodus).
- 64x48 SSD1306 OLED (Kameramodus, 4-Draht-Software-SPI im aktuellen Sketch).
- Auslöser-Taster/-Schalter-Eingang.

### Software
- Arduino IDE (oder Arduino CLI) mit einem SAMD21-Boardpaket.
- Erforderliche Arduino-Bibliotheken für die Kamera-Firmware:
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/Laufzeit für [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/).

## Installation

1. Dieses Repository klonen:

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. Einen Firmware-Sketch in der Arduino IDE öffnen:
- Kameramodus: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Webcam-Modus: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Korrektes SAMD21-Board/Port auswählen und hochladen.

4. Für den Webcam-Modus den Processing-Sketch öffnen:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Verwendung

### Kameramodus (SD-Snapshot-Workflow) 📸
1. `digiOBSCURA_camera.ino` flashen.
2. SD-Karte einlegen.
3. Beim Start prüft die Firmware Dateinamen und findet die nächste freie `DGO_####.bmp`.
4. Auslöser-Eingang betätigen.
5. Das Gerät nimmt auf, schreibt BMP auf die SD-Karte und zeigt eine Thumbnail-Vorschau auf dem OLED.

### Webcam-Modus (host-gerenderte Live-Ansicht) 🖥️
1. `digiOBSCURA_Webcam_Arduino.ino` flashen.
2. Über USB-Serial verbinden (`115200`).
3. Im Processing-Sketch den korrekten seriellen Port setzen (die Datei enthält aktuell `String portName = "COM32"`).
4. Processing-Sketch ausführen, um Pixelwerte abzufragen und den 32x32-Frame zu zeichnen.

## Konfigurationshinweise
Aktuelle Standardwerte auf Firmware-Ebene aus dem Quellcode:

| Einstellung | Aktueller Wert / Verhalten |
|---|---|
| Auflösung | Fest `32x32` |
| ADC-Auflösung | `analogReadResolution(12)` |
| Mittelung im Kameramodus | `avgCount = 10` (erhöht Qualität, verringert Bildrate) |
| SD-CS-Pin (Kamera-Sketch) | `cardPin = 21` |
| Serial-Baudrate (Webcam-Pfad) | `115200` |
| I/O-Mapping | Fest codierte Arrays/Variablen (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, usw.) |

Wenn deine Hardware-Verdrahtung abweicht, aktualisiere die Pin-Definitionen nahe dem Anfang jeder `.ino`-Datei vor dem Flashen.

## Beispiele

### Kamera-Firmware öffnen und bearbeiten
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

## Entwicklungshinweise
- Es gibt in diesem Repository kein einheitliches Build-System (`Makefile`, `platformio.ini`, `package.json`, usw.).
- Die Entwicklung umfasst derzeit Arduino + Processing + KiCad + mechanische CAD-Dateien.
- `PCB/image_sensor/README.md` und `PCB/mcu/README.md` wirken veraltet/template-artig und sind derzeit keine maßgeblichen Projektdokumente.
- `i18n/` existiert und ist derzeit nicht vollständig; Übersetzungen sind geplant bzw. werden schrittweise ergänzt.

## Dokumentation und Roadmap
Das Verzeichnis `docs/` enthält stufenweise technische Evolutionsnotizen:

| Dokument | Fokus |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | Nur-Firmware zeitliches Differencing/Ereignisse |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | Comparator-gestützte Ereigniserkennung |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | Analog-Front-End-/Bias-Verbesserungen |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | Längerfristige Optionen für Event-Sensor-Neudesign |

## Fehlerbehebung
| Symptom | Prüfungen |
|---|---|
| SD-Karte wird im Kameramodus nicht erkannt | Verdrahtung und `cardPin` prüfen (`21` im aktuellen Sketch). SD-Karte (FAT/FAT32) neu formatieren und erneut versuchen. |
| Leere/rauschige Bilddaten | `OUTCOL`/`OUTROW`-Verdrahtung und MUX-Steuerpins prüfen. Stromversorgung und Sensorboard-Verbindungen kontrollieren. |
| Processing-Viewer zeigt keine Daten | Serielle Baudrate `115200` sowohl in Arduino als auch in Processing prüfen. `portName` von `COM32` auf den tatsächlichen Port ändern. |
| Sehr langsame Aufnahme | Erwartbar bei vollständigem 32x32-Scan und Mittelung (`avgCount=10` standardmäßig). |

## Support / Sponsor
Aus den Firmware-Headern:

- Creator: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

Wenn du auf diesem Projekt aufbaust, erwäge, den ursprünglichen Creator zu unterstützen.

## Mitwirken
Beiträge sind willkommen. Vorgeschlagene Beitragsbereiche:

- Hardware-Inbetriebnahme-Notizen und verifizierte Pin-Maps.
- Toolchain-/Versionsvalidierung (Arduino-Core, Bibliotheken, Processing-Version).
- Dokumentations-Updates, insbesondere Verdrahtungsdiagramme und Kalibrierungs-Workflow.
- `i18n/`-README-Übersetzungen, sobald Sprachziele finalisiert sind.

Für Änderungen, die Hardware- oder Firmware-Verhalten betreffen, bitte einschließen:
- Details zum Testaufbau.
- Kontext zur Board-Revision.
- Vorher-/Nachher-Notizen zum Verhalten.

## Lizenz
- Repository-Lizenzdatei: [`LICENSE`](./LICENSE) (GPL-3.0-Text im Repository vorhanden).
- Firmware-Header-Hinweis: aktuelle `.ino`-Header nennen `cc-by-sa-nc-3.0`.

Wichtiger Hinweis: Diese beiden Lizenzsignale unterscheiden sich. Bis zur Klärung durch die Maintainer die Lizenzierung als zustimmungspflichtig durch die Projektverantwortlichen für Weiterverteilung/gewerbliche Nutzung behandeln.

## Danksagung
- Sean Hodgins / digiOBSCURA-Projektarbeit und öffentliche Dokumentation/Video.
- Open-Hardware- und Maker-Community-Ressourcen, die DIY-Sensorexperimente ermöglichen.
