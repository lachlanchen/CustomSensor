[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner — apps and four buddies](https://lazying.art/media/brand/lazyingart-intro.gif)](https://lazying.art)

# digiOBSCURA - A DIY 이미지 센서 및 디지털 카메라 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#hardware-firmware-architecture-current)
[![Contributions](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#contributing)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> 완전히 오픈된 DIY 이미징 스택: 맞춤형 센서 PCB, MCU 보드, 펌웨어, 웹캠 뷰어, 프린트 가능한 인클로저 부품까지 한 번에 제공합니다.

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

`digiOBSCURA`는 맞춤형 포토트랜지스터 배열과 SAMD21 기반 컨트롤러를 중심으로 만든 DIY 32x32 이미지 시스템입니다.

이 저장소에는 다음 항목이 포함됩니다:
- SD 카드로 독립 실행형 카메라 캡처를 수행하는 펌웨어.
- 직렬 기반 웹캠 스타일 실시간 미리보기를 위한 펌웨어 + Processing 스케치.
- 두 보드(`image_sensor`, `mcu`)의 전체 KiCad 소스 파일 및 생산용 gerber.
- 인클로저/렌즈/삼각대 부품의 기계식 자산(`.f3d`, `.stl`).
- [`docs/`](./docs/)의 설계 노트와 단계적 업그레이드 로드맵 문서.

## Quick Snapshot 📌

| Item | Details |
| --- | --- |
| Project size | 카메라 + 펌웨어 + 뷰어 + 기계 설계 |
| Sensor array | `32x32` (`1024`) 맞춤형 포토트랜지스터 매트릭스 |
| Core controller | `SAMD21G18A` |
| Image output | SD 카드 BMP / 직렬 실시간 프레임 스트림 |
| Target workflows | 독립형 캡처 • 웹캠 스타일 미리보기 |

## Demo 🎬

동영상을 확인하세요:

[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

양쪽 보드의 BOM은 다음에서 확인할 수 있습니다: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Features ✨

- 32x32 (1024 픽셀) 맞춤 포토트랜지스터 이미징 배열.
- 행/열 픽셀 선택을 위한 ADG732 듀얼 멀티플렉서 어드레싱.
- 독립형 카메라 워크플로우:
  - OLED 상태 UI.
  - 셔터 입력.
  - SD 카드 출력 (`DGO_####.bmp`, 자동 증가).
- 웹캠 워크플로우:
  - Arduino 펌웨어가 `115200` 속도에서 직렬 픽셀 요청에 응답.
  - Processing 앱이 실시간 흑백 32x32 뷰를 렌더링.
- 오픈 하드웨어 소스:
  - KiCad 회로도/PCB.
  - 생산 출력물 및 BOM 아티팩트.
  - 프린트 가능한 기계식 파일.

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

[`docs/overview.md`](./docs/overview.md)를 기반으로:

| Subsystem | Details |
|---|---|
| Sensor matrix | `1024 x ALS-PT19` 포토트랜지스터 |
| Addressing | `2 x ADG732` 아날로그 멀티플렉서 (행 + 열) |
| Core board | `SAMD21G18A` |
| Power path | `18650` 배터리, `MCP73831` 충전기, `3.3 V` 전압 레귤레이션 |
| Signal path | `OUTCOL`은 MCU GPIO에서 high로 구동되며, 선택된 센서 경로는 `OUTROW`에서 ADC로 읽습니다 |
| Capture behavior | 카메라 펌웨어가 전체 프레임을 스캔하고 24비트 BMP 파일을 기록 |

## Prerequisites 🧰

### Hardware

- 조립된 `image_sensor` 및 `mcu` 보드(또는 동등한 배선).
- 펌웨어에서 사용한 핀 할당과 일치하는 SAMD21 호환 타겟 보드.
- SD 카드 (카메라 모드).
- `64x48` SSD1306 OLED (카메라 모드, 현재 스케치에서 4-wire software SPI).
- 셔터 버튼/스위치 입력.

### Software

- Arduino IDE(또는 Arduino CLI)와 SAMD21 보드 패키지.
- 카메라 펌웨어에 필요한 Arduino 라이브러리:
  - `U8g2`
  - `SPI`
  - `SD`
- [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/)용 Processing IDE/런타임.

## Installation 🧩

1. 이 저장소를 복제합니다:

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. Arduino IDE에서 펌웨어 스케치 하나를 엽니다:
- 카메라 모드: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- 웹캠 모드: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. 적절한 SAMD21 보드와 포트를 선택한 뒤 업로드합니다.

4. 웹캠 모드의 경우 Processing 스케치를 엽니다:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Usage 🚀

### Camera Mode (SD snapshot workflow) 📸

1. `digiOBSCURA_camera.ino`를 업로드합니다.
2. FAT/FAT32로 포맷된 SD 카드를 삽입합니다.
3. 보드에 전원을 켜거나 리셋합니다. 펌웨어가 다음 사용 가능한 `DGO_####.bmp` 파일명을 찾아냅니다.
4. 셔터 입력으로 캡처를 트리거합니다.
5. 장치가 캡처를 수행해 SD에 BMP를 쓰고 OLED에 썸네일 미리보기를 표시합니다.

### Webcam Mode (host-rendered live view) 🖥️

1. `digiOBSCURA_Webcam_Arduino.ino`를 업로드합니다.
2. USB 직렬 연결을 `115200`으로 맞춥니다.
3. Processing 스케치에서 `String portName`을 실제 직렬 포트로 설정합니다(템플릿 기본값은 `COM32`).
4. Processing 스케치를 실행하면 32x32 픽셀을 폴링해 실시간 흑백 프레임을 렌더링합니다.

## Configuration Notes 🛠️

현재 소스 기준 펌웨어 기본값:

| Setting | Current value / behavior |
|---|---|
| Resolution | `32x32` 고정 |
| ADC resolution | `analogReadResolution(12)` |
| Camera-mode averaging | `avgCount = 10` (화질 향상, 프레임 속도 저하) |
| SD CS pin (camera sketch) | `cardPin = 21` |
| Serial baud (webcam path) | `115200` |
| I/O mapping | 하드코딩된 배열/변수 (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW` 등) |

배선이 다르다면 플래싱 전에 각 `.ino` 파일 상단의 핀 정의를 업데이트하세요.

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

- 이 저장소에는 통합 빌드 시스템(`Makefile`, `platformio.ini`, `package.json` 등)이 없습니다.
- 현재 개발은 Arduino + Processing + KiCad + 기계 CAD 파일로 진행됩니다.
- `PCB/image_sensor/README.md`와 `PCB/mcu/README.md`에는 현재 템플릿/플레이스홀더 같은 내용이 있어 최종 기준 문서로 보기 어렵습니다.
- `i18n/`에는 번역된 README가 들어 있습니다. 일부는 미완성일 수 있으며 영문 본문보다 뒤처져 있을 수 있습니다.

## Troubleshooting 🧯

| Symptom | Checks |
|---|---|
| SD card not detected in camera mode | 배선과 `cardPin`(`21`, 현재 스케치 기준)을 확인합니다. SD 카드를 FAT/FAT32로 포맷한 뒤 재시도하세요. |
| Blank/noisy image data | `OUTCOL`/`OUTROW` 배선과 멀티플렉서 제어 핀을 점검하세요. 전원 안정성과 센서 보드 연결 상태를 확인하세요. |
| Processing viewer shows no data | Arduino와 Processing 양쪽 모두 직렬 보레이트가 `115200`인지 확인하세요. `portName`을 `COM32`가 아닌 실제 포트로 바꾸세요. |
| Very slow capture | 전체 32x32 스캔과 평균 처리(`avgCount=10` 기본값) 때문에 기대되는 속도입니다. |

## Roadmap and Technical Notes 🗺️

`docs/` 디렉터리는 계획/탐색 중인 기술 방향을 추적합니다:

| Document | Focus |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | 펌웨어 전용 시간 차분 / 이벤트 |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | 비교기 보조 이벤트 검출 |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | 아날로그 프런트엔드와 바이어스 개선 |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | 장기 이벤트 센서 재설계 옵션 |

## Contributing 🤝

기여를 환영합니다. 제안 기여 영역:

- 하드웨어 기동 노트 및 검증된 핀 맵.
- 툴체인/버전 검증 (Arduino 코어, 라이브러리, Processing 버전).
- 문서 업데이트, 특히 배선도와 캘리브레이션 워크플로우.
- 언어 타깃이 확정된 후 `i18n/` README 번역.

하드웨어 또는 펌웨어 동작을 변경할 때는 다음을 포함하세요:
- 테스트 세트업 세부 사항.
- 보드 리비전 맥락.
- 변경 전/후 동작 노트.

## ❤️ Support

<!-- support:start -->
[![GitHub Sponsors](https://img.shields.io/badge/GitHub-Sponsors-EA4AAA?style=for-the-badge&logo=githubsponsors&logoColor=white)](https://github.com/sponsors/lachlanchen)
[![LazyingArt Support](https://img.shields.io/badge/LazyingArt-Support-0EA5E9?style=for-the-badge)](https://chat.lazying.art/donate)
[![PayPal Support](https://img.shields.io/badge/PayPal-Support-00457C?style=for-the-badge&logo=paypal&logoColor=white)](https://paypal.me/RongzhouChen)
[![Stripe Support](https://img.shields.io/badge/Stripe-Support-635BFF?style=for-the-badge&logo=stripe&logoColor=white)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400)
<!-- support:end -->

## License

- 이 저장소의 `LICENSE`에는 현재 프로젝트가 저장소 수준에서 GPL-3.0으로 배포됨이 명시되어 있습니다.
- 펌웨어 헤더 주석에서도 일부 소스 헤더 콘텐츠가 `CC BY-NC-SA 3.0`를 따릅니다.

바이너리, 하드웨어 산출물 또는 파생물을 재배포할 때는 수정한 파일의 정확한 라이선스 조건을 확인하고 의도한 사용 방식과의 호환성을 검토하세요.
