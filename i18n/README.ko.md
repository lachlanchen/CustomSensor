[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# digiOBSCURA - DIY 이미지 센서 및 디지털 카메라 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](../LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#hardwarefirmware-architecture-current)

> 완전한 오픈 DIY 이미징 스택: 커스텀 센서 PCB, MCU 보드, 펌웨어, 웹캠 뷰어, 그리고 출력 가능한 인클로저 파츠.

## Overview
`digiOBSCURA`는 커스텀 포토트랜지스터 배열과 SAMD21 기반 컨트롤러를 중심으로 구성된 DIY 32x32 이미징 시스템입니다.

이 저장소에는 다음이 포함됩니다:
- SD 카드로 독립 촬영이 가능한 카메라 펌웨어.
- 시리얼 기반 웹캠 스타일 실시간 뷰잉을 위한 펌웨어 + Processing 스케치.
- 두 보드(`image_sensor`, `mcu`)의 전체 KiCad 소스 파일과 생산용 Gerber.
- 인클로저/렌즈/삼각대 구성요소용 기구 자산(`.f3d`, `.stl`).
- [`docs/`](../docs/)의 설계 노트 및 단계별 업그레이드 로드맵 문서.

### At a Glance
| 영역 | 현재 구현 |
|---|---|
| 이미징 배열 | `32x32` 커스텀 포토트랜지스터 매트릭스 (`1024` 픽셀) |
| 멀티플렉싱 | `2 x ADG732` (행 + 열 선택) |
| 컨트롤러 | `SAMD21G18A` |
| 카메라 출력 | SD 카드 BMP 파일 (`DGO_0000.bmp`, 자동 증가) |
| 라이브 모드 | 시리얼 (`115200`) + Processing 호스트 렌더러 |
| 설계 자산 | KiCad + Gerbers + BOM 산출물 + `F3D`/`STL` 파일 |

## Demo (Canonical Existing Content) 🎬
영상 보기:  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

두 보드의 BOM은 여기에서 확인할 수 있습니다: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Features
- 32x32 (1024픽셀) 커스텀 포토트랜지스터 이미징 배열.
- 행/열 픽셀 선택을 위한 듀얼 ADG732 mux 주소 지정.
- 독립형 카메라 워크플로:
  - OLED UI.
  - 셔터 입력.
  - SD 카드 출력 (`DGO_0000.bmp`, 자동 증가).
- 웹캠 워크플로:
  - Arduino 펌웨어가 `115200`에서 시리얼 픽셀 요청에 응답.
  - Processing 앱이 32x32 그레이스케일 라이브 뷰를 렌더링.
- 오픈 하드웨어 소스:
  - KiCad 회로도/PCB.
  - 생산 출력물 및 BOM 산출물.
  - 출력 가능한 기구 파일.

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
[`docs/overview.md`](../docs/overview.md) 기준:

| 서브시스템 | 상세 |
|---|---|
| 센서 매트릭스 | `1024 x ALS-PT19` 포토트랜지스터 |
| 주소 지정 | `2 x ADG732` 아날로그 mux (행 + 열) |
| 코어 보드 | `SAMD21G18A` |
| 전원 경로 | `18650` 배터리, `MCP73831` 충전기, `3.3 V` 레귤레이션 |
| 신호 경로 | `OUTCOL`은 MCU GPIO로 High 구동되며, 선택된 센서 경로는 ADC를 통해 `OUTROW`에서 읽음 |
| 캡처 동작 | 카메라 펌웨어가 전체 프레임 스캔을 수행하고 24-bit BMP 파일로 저장 |

## Prerequisites

### Hardware
- 조립된 `image_sensor` 및 `mcu` 보드(또는 동등한 배선).
- 펌웨어의 핀 할당과 일치하는 SAMD21 호환 타깃 보드.
- MicroSD 카드(카메라 모드).
- 64x48 SSD1306 OLED(카메라 모드, 현재 스케치에서 4-wire 소프트웨어 SPI).
- 셔터 버튼/스위치 입력.

### Software
- SAMD21 보드 패키지가 설치된 Arduino IDE(또는 Arduino CLI).
- 카메라 펌웨어에 필요한 Arduino 라이브러리:
  - `U8g2`
  - `SPI`
  - `SD`
- [`Firmware/digiOBSCURA_Webcam_Processing`](../Firmware/digiOBSCURA_Webcam_Processing/)용 Processing IDE/런타임.

## Installation

1. 저장소를 클론합니다:

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. Arduino IDE에서 펌웨어 스케치 하나를 엽니다:
- 카메라 모드: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](../Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- 웹캠 모드: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](../Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. 올바른 SAMD21 보드/포트를 선택하고 업로드합니다.

4. 웹캠 모드에서는 Processing 스케치를 엽니다:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](../Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Usage

### Camera Mode (SD snapshot workflow) 📸
1. `digiOBSCURA_camera.ino`를 플래시합니다.
2. SD 카드를 삽입합니다.
3. 부팅 시 펌웨어가 파일명을 확인하고 다음 사용 가능한 `DGO_####.bmp`를 찾습니다.
4. 셔터 입력을 누릅니다.
5. 장치가 캡처를 수행하고 BMP를 SD에 저장한 뒤 OLED에 썸네일 프리뷰를 표시합니다.

### Webcam Mode (host-rendered live view) 🖥️
1. `digiOBSCURA_Webcam_Arduino.ino`를 플래시합니다.
2. USB 시리얼(`115200`)로 연결합니다.
3. Processing 스케치에서 올바른 시리얼 포트를 설정합니다(현재 파일에는 `String portName = "COM32"`가 들어 있음).
4. Processing 스케치를 실행해 픽셀 값을 폴링하고 32x32 프레임을 그립니다.

## Configuration Notes
소스 기준 현재 펌웨어 기본값:

| 설정 | 현재 값 / 동작 |
|---|---|
| 해상도 | 고정 `32x32` |
| ADC 해상도 | `analogReadResolution(12)` |
| 카메라 모드 평균화 | `avgCount = 10` (품질 향상, 프레임 속도 저하) |
| SD CS 핀 (카메라 스케치) | `cardPin = 21` |
| 시리얼 보드레이트 (웹캠 경로) | `115200` |
| I/O 매핑 | 하드코딩된 배열/변수 (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW` 등) |

하드웨어 배선이 다르면, 플래시 전에 각 `.ino` 파일 상단 근처의 핀 정의를 수정하세요.

## Examples

### 카메라 펌웨어 열기 및 수정
```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Processing 웹캠 뷰어 열기 및 수정
```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### KiCad에서 PCB 소스 열기
- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## Development Notes
- 이 저장소에는 통합 빌드 시스템(`Makefile`, `platformio.ini`, `package.json` 등)이 없습니다.
- 현재 개발은 Arduino + Processing + KiCad + 기구 CAD 파일에 걸쳐 진행됩니다.
- `PCB/image_sensor/README.md`와 `PCB/mcu/README.md`는 오래되었거나 템플릿성 내용으로 보이며, 현재 권위 있는 프로젝트 문서는 아닙니다.
- `i18n/`은 존재하며 현재 비어 있고, 번역은 계획되어 있지만 아직 커밋되지 않았습니다.

## Documentation and Roadmap
`docs/` 디렉터리에는 단계별 기술 발전 노트가 있습니다:

| 문서 | 초점 |
|---|---|
| [`docs/01-firmware-differencing.md`](../docs/01-firmware-differencing.md) | 펌웨어 전용 시간차 기반 디퍼런싱/이벤트 |
| [`docs/02-comparator-event-mode.md`](../docs/02-comparator-event-mode.md) | 비교기 보조 이벤트 검출 |
| [`docs/03-bias-and-buffer-upgrade.md`](../docs/03-bias-and-buffer-upgrade.md) | 아날로그 프런트엔드/바이어스 개선 |
| [`docs/04-full-event-sensor-redesign.md`](../docs/04-full-event-sensor-redesign.md) | 장기 이벤트 센서 재설계 옵션 |

## Troubleshooting
| 증상 | 점검 항목 |
|---|---|
| 카메라 모드에서 SD 카드가 인식되지 않음 | 배선과 `cardPin`(현재 스케치에서 `21`)을 확인하세요. SD 카드를 FAT/FAT32로 포맷 후 재시도하세요. |
| 이미지 데이터가 비어 있거나 노이즈가 많음 | `OUTCOL`/`OUTROW` 배선과 mux 제어 핀을 확인하세요. 전원 무결성과 센서 보드 연결 상태를 점검하세요. |
| Processing 뷰어에 데이터가 표시되지 않음 | Arduino와 Processing 모두에서 시리얼 보드레이트 `115200`을 확인하세요. `portName`을 `COM32`에서 실제 포트로 변경하세요. |
| 캡처가 매우 느림 | 전체 32x32 스캔 및 평균화(기본 `avgCount=10`)에서는 정상적인 동작입니다. |

## Support / Sponsor
펌웨어 헤더 기준:

- Creator: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

이 프로젝트를 기반으로 작업한다면 원 제작자를 후원해 주세요.

## Contributing
기여를 환영합니다. 권장 기여 영역:

- 하드웨어 bring-up 노트 및 검증된 핀 맵.
- 툴체인/버전 검증(Arduino 코어, 라이브러리, Processing 버전).
- 문서 업데이트, 특히 배선도와 캘리브레이션 워크플로.
- 언어 타깃 확정 후 `i18n/` README 번역.

하드웨어 또는 펌웨어 동작에 영향을 주는 변경에는 다음을 포함해 주세요:
- 테스트 셋업 상세.
- 보드 리비전 맥락.
- 변경 전/후 동작 노트.

## License
- 저장소 라이선스 파일: [`LICENSE`](../LICENSE) (저장소에 GPL-3.0 텍스트 존재).
- 펌웨어 헤더 표기: 현재 `.ino` 헤더는 `cc-by-sa-nc-3.0`으로 기재.

중요 참고: 이 두 라이선스 신호는 서로 다릅니다. 유지관리자가 명확히 할 때까지는 재배포/상업적 사용 시 프로젝트 소유자 확인이 필요하다고 간주하세요.

## Acknowledgements
- Sean Hodgins / digiOBSCURA 프로젝트 작업 및 공개 문서/영상.
- DIY 센서 실험을 가능하게 해 주는 오픈 하드웨어 및 메이커 커뮤니티 리소스.
