[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# digiOBSCURA - Un Sensor de Imagen DIY y Cámara Digital 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](../LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#resumen-general)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#características)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#arquitectura-hardwarefirmware-estado-actual)

> Una pila de imagen DIY totalmente abierta: PCB de sensor personalizada, placa MCU, firmware, visor tipo webcam y piezas de carcasa imprimibles.

## Resumen General
`digiOBSCURA` es un sistema de imagen DIY de 32x32 construido alrededor de una matriz personalizada de fototransistores y un controlador basado en SAMD21.

Este repositorio contiene:
- Firmware para captura en modo cámara autónoma con guardado en tarjeta SD.
- Firmware + sketch de Processing para visualización en vivo por serial estilo webcam.
- Archivos fuente completos de KiCad para ambas placas (`image_sensor` y `mcu`) además de Gerbers de producción.
- Recursos mecánicos (`.f3d`, `.stl`) para componentes de carcasa/lente/trípode.
- Notas de diseño y documentos de hoja de ruta por etapas en [`docs/`](../docs/).

### Vista Rápida
| Área | Implementación actual |
|---|---|
| Matriz de imagen | Matriz personalizada de fototransistores `32x32` (`1024` píxeles) |
| Multiplexación | `2 x ADG732` (selección de fila + columna) |
| Controlador | `SAMD21G18A` |
| Salida de cámara | Archivos BMP en SD (`DGO_0000.bmp`, autoincremental) |
| Modo en vivo | Serial (`115200`) + renderizado en host con Processing |
| Recursos de diseño | KiCad + Gerbers + artefactos BOM + archivos `F3D`/`STL` |

## Demo (Contenido Canónico Existente) 🎬
Mira el video:  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

Encuentra la BOM de ambas placas aquí: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Características
- Matriz de imagen personalizada de fototransistores 32x32 (1024 píxeles).
- Direccionamiento dual con ADG732 para selección de píxel por fila/columna.
- Flujo de trabajo de cámara autónoma:
  - Interfaz OLED.
  - Entrada de disparador (shutter).
  - Salida a tarjeta SD (`DGO_0000.bmp`, autoincremental).
- Flujo de trabajo webcam:
  - El firmware de Arduino responde solicitudes seriales de píxeles a `115200`.
  - La app de Processing renderiza una vista en vivo en escala de grises de 32x32.
- Hardware de código abierto:
  - Esquemáticos/PCB en KiCad.
  - Salidas de producción y artefactos BOM.
  - Archivos mecánicos imprimibles.

## Estructura del Proyecto

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

## Arquitectura Hardware/Firmware (Estado Actual)
Basado en [`docs/overview.md`](../docs/overview.md):

| Subsistema | Detalles |
|---|---|
| Matriz de sensor | `1024 x ALS-PT19` fototransistores |
| Direccionamiento | `2 x ADG732` multiplexores analógicos (fila + columna) |
| Placa principal | `SAMD21G18A` |
| Ruta de energía | Batería `18650`, cargador `MCP73831`, regulación a `3.3 V` |
| Ruta de señal | `OUTCOL` se lleva a nivel alto desde GPIO del MCU; la ruta de sensor seleccionada se lee en `OUTROW` vía ADC |
| Comportamiento de captura | El firmware de cámara realiza escaneos de cuadro completo y escribe archivos BMP de 24 bits |

## Requisitos Previos

### Hardware
- Placas `image_sensor` y `mcu` ensambladas (o cableado equivalente).
- Placa objetivo compatible con SAMD21 que coincida con la asignación de pines usada en el firmware.
- Tarjeta MicroSD (modo cámara).
- OLED SSD1306 de 64x48 (modo cámara, SPI por software de 4 hilos en el sketch actual).
- Botón/interruptor de disparo (shutter).

### Software
- Arduino IDE (o Arduino CLI) con paquete de placa SAMD21.
- Librerías de Arduino requeridas para el firmware de cámara:
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/runtime para [`Firmware/digiOBSCURA_Webcam_Processing`](../Firmware/digiOBSCURA_Webcam_Processing/).

## Instalación

1. Clona este repositorio:

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. Abre uno de los sketches de firmware en Arduino IDE:
- Modo cámara: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](../Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Modo webcam: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](../Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Selecciona la placa/puerto SAMD21 correctos y sube el firmware.

4. Para el modo webcam, abre el sketch de Processing:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](../Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Uso

### Modo Cámara (flujo de captura SD) 📸
1. Flashea `digiOBSCURA_camera.ino`.
2. Inserta la tarjeta SD.
3. Al arrancar, el firmware comprueba nombres de archivo y encuentra el siguiente `DGO_####.bmp` libre.
4. Pulsa el disparador.
5. El dispositivo captura, guarda el BMP en SD y muestra una vista previa miniatura en OLED.

### Modo Webcam (vista en vivo renderizada en host) 🖥️
1. Flashea `digiOBSCURA_Webcam_Arduino.ino`.
2. Conecta por serial USB (`115200`).
3. En el sketch de Processing, configura el puerto serial correcto (el archivo actualmente tiene `String portName = "COM32"`).
4. Ejecuta el sketch de Processing para consultar valores de píxel y dibujar el cuadro 32x32.

## Notas de Configuración
Valores por defecto actuales en firmware según el código fuente:

| Ajuste | Valor / comportamiento actual |
|---|---|
| Resolución | Fija en `32x32` |
| Resolución ADC | `analogReadResolution(12)` |
| Promediado en modo cámara | `avgCount = 10` (mejora calidad, reduce velocidad de cuadro) |
| Pin CS de SD (sketch de cámara) | `cardPin = 21` |
| Baud serial (ruta webcam) | `115200` |
| Mapeo de I/O | Arrays/variables codificados (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, etc.) |

Si el cableado de tu hardware difiere, actualiza las definiciones de pines cerca de la parte superior de cada archivo `.ino` antes de flashear.

## Ejemplos

### Abrir y editar el firmware de cámara
```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Abrir y editar el visor webcam de Processing
```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### Abrir fuentes PCB en KiCad
- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## Notas de Desarrollo
- No existe un sistema de build unificado (`Makefile`, `platformio.ini`, `package.json`, etc.) en este repositorio.
- El desarrollo actualmente abarca Arduino + Processing + KiCad + archivos CAD mecánicos.
- `PCB/image_sensor/README.md` y `PCB/mcu/README.md` parecen desactualizados/plantilla y actualmente no son documentación autoritativa del proyecto.
- `i18n/` existe y actualmente está vacío; las traducciones están planificadas pero aún no se han enviado.

## Documentación y Hoja de Ruta
El directorio `docs/` contiene notas de evolución técnica por etapas:

| Documento | Enfoque |
|---|---|
| [`docs/01-firmware-differencing.md`](../docs/01-firmware-differencing.md) | Diferenciación temporal/eventos solo en firmware |
| [`docs/02-comparator-event-mode.md`](../docs/02-comparator-event-mode.md) | Detección de eventos asistida por comparador |
| [`docs/03-bias-and-buffer-upgrade.md`](../docs/03-bias-and-buffer-upgrade.md) | Mejoras de bias y front-end analógico |
| [`docs/04-full-event-sensor-redesign.md`](../docs/04-full-event-sensor-redesign.md) | Opciones de rediseño de sensor de eventos a largo plazo |

## Solución de Problemas
| Síntoma | Verificaciones |
|---|---|
| La SD no se detecta en modo cámara | Confirma el cableado y `cardPin` (`21` en el sketch actual). Reformatea la SD (FAT/FAT32) y vuelve a probar. |
| Datos de imagen en blanco/con ruido | Verifica cableado de `OUTCOL`/`OUTROW` y pines de control de mux. Revisa integridad de alimentación y conexiones de la placa de sensores. |
| El visor de Processing no muestra datos | Confirma baud serial `115200` tanto en Arduino como en Processing. Cambia `portName` de `COM32` a tu puerto real. |
| Captura muy lenta | Es esperable con escaneo completo 32x32 y promediado (`avgCount=10` por defecto). |

## Soporte / Patrocinio
De los encabezados del firmware:

- Creador: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

Si construyes sobre este proyecto, considera apoyar al creador original.

## Contribuciones
Las contribuciones son bienvenidas. Áreas sugeridas:

- Notas de puesta en marcha de hardware y mapas de pines verificados.
- Validación de toolchain/versiones (Arduino core, librerías, versión de Processing).
- Actualizaciones de documentación, especialmente diagramas de cableado y flujo de calibración.
- Traducciones de README en `i18n/` una vez se finalicen los idiomas objetivo.

Para cambios que afecten comportamiento de hardware o firmware, incluye:
- Detalles del entorno de pruebas.
- Contexto de revisión de placa.
- Notas de comportamiento antes/después.

## Licencia
- Archivo de licencia del repositorio: [`LICENSE`](../LICENSE) (texto GPL-3.0 presente en el repo).
- Nota de encabezado de firmware: los encabezados actuales de `.ino` indican `cc-by-sa-nc-3.0`.

Nota importante: estas dos señales de licencia son diferentes. Hasta que los mantenedores lo aclaren, trata la licencia como un caso que requiere confirmación del propietario del proyecto para redistribución/uso comercial.

## Agradecimientos
- Sean Hodgins / trabajo del proyecto digiOBSCURA y documentación/video públicos.
- Recursos de comunidades maker y de hardware abierto que hacen posibles los experimentos DIY con sensores.
