[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner — apps and four buddies](https://github.com/lachlanchen/LazyingArtWebsite/raw/main/media/brand/lazyingart-intro.gif)](https://lazying.art)

# digiOBSCURA - Un sensor de imagen y una cámara digital DIY 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](../LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#hardware-firmware-architecture-current)
[![Contributions](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#contributing)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> Una solución de imagen DIY totalmente abierta: PCB de sensor personalizada, placa MCU, firmware, visor estilo webcam y piezas de carcasa imprimibles.

## 📚 Table of Contents

- [Overview](#overview)
- [Demo](#demo)
- [Features](#features)
- [Project Structure](#project-structure)
- [Hardware/Firmware Architecture (Current)](#hardware-firmware-architecture-current)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration Notes](#configuration-notes)
- [Examples](#examples)
- [Development Notes](#development-notes)
- [Troubleshooting](#troubleshooting)
- [Roadmap and Technical Notes](#roadmap-and-technical-notes)
- [Contributing](#contributing)
- [Support](#%E2%9D%A4%EF%B8%8F-support)
- [License](#license)

## Overview 🛰️

`digiOBSCURA` es un sistema de imagen DIY de 32x32 construido alrededor de una matriz de fototransistores personalizada y un controlador basado en SAMD21.

Este repositorio contiene:
- Firmware para captura de cámara autónoma en tarjeta SD.
- Firmware + sketch de Processing para vista previa en vivo estilo webcam.
- Archivos fuente completos de KiCad para ambas placas (`image_sensor` y `mcu`) más los gerbers de producción.
- Archivos mecánicos (`.f3d`, `.stl`) para carcasa, lente y componentes de trípode.
- Notas de diseño y documentos de hoja de ruta por etapas en [`docs/`](../docs/).

## Quick Snapshot 📌

| Elemento | Detalles |
| --- | --- |
| Tamaño del proyecto | Cámara + firmware + visor + diseño mecánico |
| Matriz del sensor | `32x32` (`1024`) matriz personalizada de fototransistores |
| Controlador principal | `SAMD21G18A` |
| Salida de imagen | BMP en tarjeta SD / stream de fotogramas en serie |
| Flujos objetivo | Captura autónoma • Previsualización tipo webcam |

## Demo 🎬

Consulta el vídeo:

[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

Encuentra la BOM de ambas placas aquí: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Features ✨

- Matriz de imagen personalizada de 32x32 (`1024` píxeles).
- Selección dual con ADG732 para direccionar filas/columnas de píxeles.
- Flujo de trabajo de cámara autónoma:
  - Interfaz de estado OLED.
  - Entrada de obturador.
  - Salida en tarjeta SD (`DGO_####.bmp`) con incremento automático.
- Flujo de trabajo webcam:
  - El firmware de Arduino responde a solicitudes de píxeles por serie en `115200`.
  - La app de Processing renderiza una vista en vivo en escala de grises de 32x32.
- Hardware abierto:
  - Esquemáticos/PCBs de KiCad.
  - Archivos de producción y artefactos de BOM.
  - Archivos mecánicos imprimibles.

## Project Structure 🧱

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
|  `- fp-lib-table
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

## Hardware/Firmware Architecture (Current) 🧰

Basado en [`docs/overview.md`](../docs/overview.md):

| Subsistema | Detalles |
|---|---|
| Matriz del sensor | `1024 x ALS-PT19` fototransistores |
| Direccionamiento | `2 x ADG732` multiplexores analógicos (fila + columna) |
| Placa base | `SAMD21G18A` |
| Ruta de alimentación | Batería `18650`, cargador `MCP73831`, regulación a `3.3 V` |
| Ruta de señal | `OUTCOL` se pone en alto por GPIO del MCU; el camino seleccionado del sensor se lee en `OUTROW` vía ADC |
| Comportamiento de captura | El firmware de cámara realiza escaneos de fotograma completo y escribe archivos BMP de 24 bits |

## Prerequisites 🧰

### Hardware

- Placas ensambladas `image_sensor` y `mcu` (o cableado equivalente).
- Placa objetivo compatible con SAMD21 con asignación de pines igual a la usada en el firmware.
- Tarjeta MicroSD (modo cámara).
- Pantalla OLED SSD1306 `64x48` (modo cámara, software SPI de 4 hilos en el sketch actual).
- Entrada de botón/interruptor de obturación.

### Software

- Arduino IDE (o Arduino CLI) con paquete para placa SAMD21.
- Librerías requeridas para el firmware de cámara:
  - `U8g2`
  - `SPI`
  - `SD`
- IDE/runtime de Processing para [`Firmware/digiOBSCURA_Webcam_Processing`](../Firmware/digiOBSCURA_Webcam_Processing/).

## Installation 🧩

1. Clona este repositorio:

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. Abre uno de los sketches de firmware en Arduino IDE:
- Modo cámara: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](../Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Modo webcam: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](../Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Selecciona la placa SAMD21 y el puerto correctos, luego carga el firmware.

4. Para modo webcam, abre el sketch de Processing:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](../Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Usage 🚀

### Camera Mode (SD snapshot workflow) 📸

1. Flashea `digiOBSCURA_camera.ino`.
2. Inserta una tarjeta SD formateada en FAT/FAT32.
3. Enciende o reinicia la placa; el firmware busca el siguiente nombre libre `DGO_####.bmp`.
4. Activa la captura con la entrada de obturador.
5. El dispositivo captura y escribe BMP en la SD y muestra una vista previa en miniatura en el OLED.

### Webcam Mode (host-rendered live view) 🖥️

1. Flashea `digiOBSCURA_Webcam_Arduino.ino`.
2. Conéctate por USB serial a `115200`.
3. En el sketch de Processing, asigna `String portName` al puerto serial real (la plantilla usa `COM32`).
4. Ejecuta el sketch de Processing; consulta 32x32 píxeles y renderiza un fotograma en vivo en escala de grises.

## Configuration Notes 🛠️

Valores actuales a nivel firmware desde el código fuente:

| Ajuste | Valor / comportamiento actual |
|---|---|
| Resolución | Fija `32x32` |
| Resolución ADC | `analogReadResolution(12)` |
| Promedio en modo cámara | `avgCount = 10` (mayor calidad, menor velocidad de fotograma) |
| Pin CS SD (sketch de cámara) | `cardPin = 21` |
| Baud serie (ruta webcam) | `115200` |
| Mapeo I/O | Arrays/variables hardcodeados (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, etc.) |

Si tu cableado es distinto, actualiza las definiciones de pines cerca de la parte superior de cada archivo `.ino` antes de cargar el firmware.

## Examples 🧪

### Open and edit camera firmware

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

## Development Notes 📘

- En este repositorio no hay un sistema de build unificado (`Makefile`, `platformio.ini`, `package.json`, etc.).
- El desarrollo actual combina Arduino + Processing + KiCad + archivos CAD mecánicos.
- `PCB/image_sensor/README.md` y `PCB/mcu/README.md` contienen contenido tipo plantilla y no son autorizantes.
- `i18n/` contiene variantes traducidas del README; están incompletas en algunas partes y pueden ir por detrás del contenido en inglés.

## Troubleshooting 🧯

| Síntoma | Comprobaciones |
|---|---|
| La tarjeta SD no se detecta en modo cámara | Verifica el cableado y `cardPin` (`21` en el sketch actual). Reformatea la SD (FAT/FAT32) y reintenta. |
| Datos de imagen en blanco o ruidosos | Verifica el cableado de `OUTCOL`/`OUTROW` y los pines de control del mux. Comprueba la integridad de alimentación y conexiones de la placa de sensor. |
| El visor de Processing no muestra datos | Verifica el baud `115200` en Arduino y en Processing. Cambia `portName` de `COM32` al puerto real que uses. |
| Captura muy lenta | Es esperable con el barrido completo 32x32 y el promediado (`avgCount=10`) por defecto. |

## Roadmap and Technical Notes 🗺️

El directorio `docs/` recoge direcciones técnicas planificadas y exploratorias:

| Documento | Enfoque |
|---|---|
| [`docs/01-firmware-differencing.md`](../docs/01-firmware-differencing.md) | Diferenciación temporal/ eventos solo en firmware |
| [`docs/02-comparator-event-mode.md`](../docs/02-comparator-event-mode.md) | Detección de eventos asistida por comparador |
| [`docs/03-bias-and-buffer-upgrade.md`](../docs/03-bias-and-buffer-upgrade.md) | Mejoras de front-end analógico y de polarización |
| [`docs/04-full-event-sensor-redesign.md`](../docs/04-full-event-sensor-redesign.md) | Opciones de rediseño del sensor de eventos a más largo plazo |

## Contributing 🤝

Las contribuciones son bienvenidas. Áreas sugeridas:

- Notas de puesta en marcha de hardware y mapas de pines verificados.
- Validación de toolchain/versiones (núcleo de Arduino, librerías, versión de Processing).
- Actualizaciones de documentación, especialmente diagramas de cableado y flujo de calibración.
- Traducciones del README en `i18n/` cuando se finalicen los objetivos de idioma.

Para cambios que afecten el comportamiento de hardware o firmware, incluye:
- Detalles de configuración de pruebas.
- Contexto de revisión de la placa.
- Notas de comportamiento antes y después.

## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |

## License

- En este repositorio, `LICENSE` indica que el proyecto se distribuye bajo GPL-3.0 a nivel general.
- Los comentarios del encabezado del firmware también hacen referencia a `CC BY-NC-SA 3.0` para parte del contenido de cabecera del código.

Al redistribuir binarios, artefactos de hardware o derivados, verifica las condiciones exactas de licencia en los archivos que modifiques y asegúrate de que sean compatibles con el uso que planeas.
