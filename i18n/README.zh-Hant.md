[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)



# digiOBSCURA - DIY 影像感測器與數位相機 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](../LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#hardwarefirmware-architecture-current)

> 完整開放的 DIY 影像系統：自製感測器 PCB、MCU 板、韌體、webcam 檢視器，以及可列印外殼零件。

<a id="overview"></a>
## Overview
`digiOBSCURA` 是一套 DIY 的 32x32 影像系統，核心由自製光電晶體陣列與 SAMD21 控制器組成。

此儲存庫包含：
- 可獨立運作並儲存至 SD 卡的相機韌體。
- 搭配 Processing 草圖的韌體，用於序列埠 webcam 風格即時檢視。
- 兩塊板卡（`image_sensor` 與 `mcu`）的完整 KiCad 原始檔與量產 Gerber。
- 外殼／鏡頭／腳架元件的機構檔（`.f3d`、`.stl`）。
- 位於 [`docs/`](../docs/) 的設計筆記與分階段升級路線圖文件。

### At a Glance
| 區域 | 目前實作 |
|---|---|
| 影像陣列 | `32x32` 自製光電晶體矩陣（`1024` 像素） |
| 多工切換 | `2 x ADG732`（列 + 行選擇） |
| 控制器 | `SAMD21G18A` |
| 相機輸出 | SD 卡 BMP 檔（`DGO_0000.bmp`，自動遞增） |
| 即時模式 | 序列埠（`115200`）+ Processing 主機端渲染 |
| 設計資產 | KiCad + Gerbers + BOM 成果 + `F3D`/`STL` 檔案 |

## Demo (Canonical Existing Content) 🎬
請看影片：  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

兩塊板卡的 BOM 在此： https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

<a id="features"></a>
## Features
- 32x32（1024 像素）自製光電晶體影像陣列。
- 使用雙 ADG732 mux 進行行／列像素選址。
- 獨立相機工作流程：
  - OLED UI。
  - 快門輸入。
  - SD 卡輸出（`DGO_0000.bmp`，自動遞增）。
- Webcam 工作流程：
  - Arduino 韌體以 `115200` 回應序列埠像素請求。
  - Processing 應用程式渲染 32x32 灰階即時畫面。
- 開放硬體原始資料：
  - KiCad 原理圖／PCB。
  - 量產輸出與 BOM 成果。
  - 可列印機構檔案。

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

<a id="hardwarefirmware-architecture-current"></a>
## Hardware/Firmware Architecture (Current)
根據 [`docs/overview.md`](../docs/overview.md)：

| 子系統 | 細節 |
|---|---|
| 感測器矩陣 | `1024 x ALS-PT19` 光電晶體 |
| 定址 | `2 x ADG732` 類比 mux（列 + 行） |
| 核心板 | `SAMD21G18A` |
| 電源路徑 | `18650` 電池、`MCP73831` 充電器、`3.3 V` 穩壓 |
| 訊號路徑 | MCU GPIO 將 `OUTCOL` 拉高；被選中的感測器路徑由 ADC 在 `OUTROW` 讀取 |
| 擷取行為 | 相機韌體執行全畫面掃描並寫入 24-bit BMP 檔 |

## Prerequisites

### Hardware
- 已組裝的 `image_sensor` 與 `mcu` 板（或等效配線）。
- 與韌體腳位配置相符的 SAMD21 相容目標板。
- MicroSD 卡（相機模式）。
- 64x48 SSD1306 OLED（相機模式，現行草圖為 4 線軟體 SPI）。
- 快門按鈕／開關輸入。

### Software
- Arduino IDE（或 Arduino CLI）與 SAMD21 板套件。
- 相機韌體所需 Arduino 函式庫：
  - `U8g2`
  - `SPI`
  - `SD`
- [`Firmware/digiOBSCURA_Webcam_Processing`](../Firmware/digiOBSCURA_Webcam_Processing/) 的 Processing IDE/runtime。

## Installation

1. 複製此儲存庫：

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. 在 Arduino IDE 開啟其中一個韌體草圖：
- 相機模式：[`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](../Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Webcam 模式：[`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](../Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. 選擇正確的 SAMD21 板子與連接埠並上傳。

4. 若為 webcam 模式，開啟 Processing 草圖：
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](../Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Usage

### Camera Mode (SD snapshot workflow) 📸
1. 燒錄 `digiOBSCURA_camera.ino`。
2. 插入 SD 卡。
3. 開機時，韌體會檢查檔名並找到下一個可用的 `DGO_####.bmp`。
4. 按下快門輸入。
5. 裝置擷取影像、寫入 BMP 到 SD，並在 OLED 顯示縮圖預覽。

### Webcam Mode (host-rendered live view) 🖥️
1. 燒錄 `digiOBSCURA_Webcam_Arduino.ino`。
2. 透過 USB 序列埠連線（`115200`）。
3. 在 Processing 草圖中設定正確序列埠（目前檔案為 `String portName = "COM32"`）。
4. 執行 Processing 草圖以輪詢像素值並繪製 32x32 畫面。

## Configuration Notes
目前原始碼中的韌體預設值：

| 設定 | 目前值／行為 |
|---|---|
| 解析度 | 固定 `32x32` |
| ADC 解析度 | `analogReadResolution(12)` |
| 相機模式平均 | `avgCount = 10`（提升畫質、降低幀速） |
| SD CS 腳位（camera 草圖） | `cardPin = 21` |
| 序列傳輸率（webcam 路徑） | `115200` |
| I/O 對應 | 硬編碼陣列／變數（`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW` 等） |

如果你的硬體配線不同，燒錄前請先更新每個 `.ino` 檔案開頭附近的腳位定義。

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
- 此儲存庫目前沒有統一建置系統（`Makefile`、`platformio.ini`、`package.json` 等）。
- 目前開發橫跨 Arduino + Processing + KiCad + 機構 CAD 檔案。
- `PCB/image_sensor/README.md` 與 `PCB/mcu/README.md` 看起來較舊／偏模板化，目前不是權威文件。
- `i18n/` 已存在，翻譯文件正在逐步加入。

## Documentation and Roadmap
`docs/` 目錄包含分階段的技術演進筆記：

| 文件 | 重點 |
|---|---|
| [`docs/01-firmware-differencing.md`](../docs/01-firmware-differencing.md) | 僅韌體的時間差分／事件偵測 |
| [`docs/02-comparator-event-mode.md`](../docs/02-comparator-event-mode.md) | 以比較器輔助的事件偵測 |
| [`docs/03-bias-and-buffer-upgrade.md`](../docs/03-bias-and-buffer-upgrade.md) | 類比前端／偏壓改善 |
| [`docs/04-full-event-sensor-redesign.md`](../docs/04-full-event-sensor-redesign.md) | 長期事件感測器重設計選項 |

## Troubleshooting
| 症狀 | 檢查項目 |
|---|---|
| 相機模式偵測不到 SD 卡 | 確認配線與 `cardPin`（現行草圖為 `21`）。將 SD 卡重格式化為 FAT/FAT32 後重試。 |
| 影像資料空白／雜訊大 | 檢查 `OUTCOL`/`OUTROW` 配線與 mux 控制腳位。確認供電完整性與感測器板連接。 |
| Processing 檢視器沒有資料 | 確認 Arduino 與 Processing 兩端皆為 `115200`。把 `portName` 從 `COM32` 改成你的實際連接埠。 |
| 擷取速度很慢 | 屬於預期行為：完整 32x32 掃描加上平均（預設 `avgCount=10`）。 |

## Support / Sponsor
來自韌體標頭：

- Creator: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

如果你基於此專案延伸開發，可考慮支持原作者。

## Contributing
歡迎貢獻。建議貢獻方向：

- 硬體 bring-up 筆記與已驗證腳位圖。
- 工具鏈／版本驗證（Arduino core、函式庫、Processing 版本）。
- 文件更新，特別是配線圖與校正流程。
- `i18n/` README 翻譯（語言目標定案後）。

若變更涉及硬體或韌體行為，請附上：
- 測試環境細節。
- 板卡版本資訊。
- 變更前後行為說明。

## License
- 儲存庫授權檔：[`LICENSE`](../LICENSE)（儲存庫內有 GPL-3.0 文字）。
- 韌體標頭註記：目前 `.ino` 標頭標示 `cc-by-sa-nc-3.0`。

重要說明：這兩個授權訊號彼此不同。維護者尚未釐清前，請視為需要專案擁有者確認後再進行再散布／商業使用。

## Acknowledgements
- Sean Hodgins / digiOBSCURA 專案成果與公開文件／影片。
- 讓 DIY 感測器實驗成為可能的開放硬體與 maker 社群資源。
