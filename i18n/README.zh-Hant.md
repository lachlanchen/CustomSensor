[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner — apps and four buddies](https://lazying.art/media/brand/lazyingart-intro.gif)](https://lazying.art)

# digiOBSCURA - 一套可自行打造的影像感測器與數位相機 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#hardware-firmware-architecture-current)
[![Contributions](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#contributing)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> 一套完全開放的 DIY 成像方案：自訂感測器 PCB、MCU 板、韌體、Webcam 檢視器，以及可列印的外殼零件。

## 📚 目錄

- [總覽](#總覽)
- [示範](#示範)
- [特性](#特性)
- [專案結構](#專案結構)
- [硬體/韌體架構（目前）](#硬體韌體架構目前)
- [先決條件](#先決條件)
- [安裝](#安裝)
- [使用方式](#使用方式)
- [設定說明](#設定說明)
- [範例](#範例)
- [開發備註](#開發備註)
- [故障排除](#故障排除)
- [路線圖與技術筆記](#路線圖與技術筆記)
- [貢獻](#貢獻)
- [Support](#%E2%9D%A4%EF%B8%8F-support)
- [授權](#授權)

## 總覽 🛰️

`digiOBSCURA` 是一個以自訂光電晶體陣列與 SAMD21 控制器為核心的 DIY 32x32 成像系統。

本專案包含：
- 用於單機拍攝並輸出到 SD 卡的韌體。
- 用於串列視訊、即時 webcam 風格預覽的韌體 + Processing 草圖。
- 兩塊板子（`image_sensor` 與 `mcu`）的完整 KiCad 原始檔，以及量產 Gerber 檔。
- 外殼、鏡頭、腳架等機構元件的機械資源（`.f3d`、`.stl`）。
- 設計筆記與分階段升級路線文件，位於 [`docs/`](./docs/)。

## 快速摘要 📌

| 項目 | 說明 |
| --- | --- |
| 專案規模 | 相機 + 韌體 + 檢視器 + 機構設計 |
| 感測器陣列 | `32x32`（`1024`）自訂光電晶體矩陣 |
| 核心控制器 | `SAMD21G18A` |
| 影像輸出 | SD 卡 BMP / 串列即時影格串流 |
| 目標工作流程 | 單機擷取 • Webcam 風格預覽 |

## 示範 🎬

查看影片：

[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

於此查看兩塊板子的 BOM： https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## 特性 ✨

- 32x32（1024 像素）自訂光電晶體成像陣列。
- 使用雙 ADG732 多工器進行列/欄像素選取。
- 單機相機流程：
  - OLED 狀態介面。
  - 快門輸入。
  - SD 卡輸出（`DGO_####.bmp`，自動遞增）。
- Webcam 流程：
  - Arduino 韌體會在 `115200` 回應像素串列讀取請求。
  - Processing 應用程式渲染 32x32 即時灰階影像。
- 開放硬體原始資料：
  - KiCad 電路圖/PCB。
  - 量產輸出與 BOM 成果。
  - 可列印機構檔案。

## 專案結構 🧱

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

## 硬體/韌體架構（目前） 🧰

基於 [`docs/overview.md`](./docs/overview.md)：

| 子系統 | 說明 |
|---|---|
| 感測器矩陣 | `1024 x ALS-PT19` 光電晶體 |
| 位址設定 | `2 x ADG732` 類比多工器（列 + 欄） |
| 核心板 | `SAMD21G18A` |
| 電源路徑 | `18650` 電池、`MCP73831` 充電器、`3.3 V` 穩壓 |
| 訊號路徑 | MCU GPIO 將 `OUTCOL` 拉高；所選感測通道透過 ADC 在 `OUTROW` 讀取 |
| 擷取行為 | 相機韌體執行全畫面掃描並寫入 24-bit BMP 檔 |

## 先決條件 🧰

### 硬體

- 已組裝完成的 `image_sensor` 與 `mcu` 板（或等效接線）。
- 與韌體使用相同腳位配置的 SAMD21 相容目標板。
- MicroSD 卡（相機模式）。
- `64x48` SSD1306 OLED（相機模式，當前草稿採用 4 線軟體 SPI）。
- 快門按鈕/開關輸入。

### 軟體

- 安裝有 SAMD21 板卡套件的 Arduino IDE（或 Arduino CLI）。
- 相機韌體需要的 Arduino 函式庫：
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/執行環境（用於 [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/)。

## 安裝 🧩

1. 複製本專案：

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. 在 Arduino IDE 開啟其中一個韌體草稿：
- 相機模式：[`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Webcam 模式：[`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. 選擇正確的 SAMD21 板卡與埠並上傳。

4. Webcam 模式下，開啟 Processing 草稿：
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## 使用方式 🚀

### 相機模式（SD 快照流程） 📸

1. 上傳 `digiOBSCURA_camera.ino`。
2. 插入已格式化為 FAT/FAT32 的 SD 卡。
3. 上電/重置板卡；韌體會搜尋下一個可用的 `DGO_####.bmp` 檔名。
4. 以快門輸入觸發拍攝。
5. 裝置拍攝後將 BMP 寫入 SD，並在 OLED 顯示縮圖預覽。

### Webcam 模式（主機端即時檢視） 🖥️

1. 上傳 `digiOBSCURA_Webcam_Arduino.ino`。
2. 以 `115200` 連上 USB 串列。
3. 在 Processing 草稿中將 `String portName` 設為實際序列埠（範例使用 `COM32`）。
4. 執行 Processing 草稿，它會輪詢 32x32 像素並渲染即時灰階影格。

## 設定說明 🛠️

目前韌體層級預設值如下：

| 設定 | 目前值 / 行為 |
|---|---|
| 解析度 | 固定 `32x32` |
| ADC 解析度 | `analogReadResolution(12)` |
| 相機模式平均值 | `avgCount = 10`（高品質、較低影格速度） |
| SD CS 腳位（相機草稿） | `cardPin = 21` |
| 串列鮑率（webcam 路徑） | `115200` |
| I/O 映射 | 硬編碼陣列/變數（`SCOL`、`SROW`、`WR`、`CS`、`EN`、`OUTCOL`、`OUTROW` 等） |

如果你的接線不同，請在每個 `.ino` 檔案頂端更新腳位定義後再進行上傳。

## 範例 🧪

### 開啟並編輯相機韌體

```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### 開啟並編輯 Processing webcam 檢視器

```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### 在 KiCad 開啟 PCB 原始檔

- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## 開發備註 📘

- 本專案目前沒有統一建置系統（`Makefile`、`platformio.ini`、`package.json` 等）。
- 開發涵蓋 Arduino + Processing + KiCad + 機構 CAD 檔。
- `PCB/image_sensor/README.md` 與 `PCB/mcu/README.md` 目前是樣板或占位內容，並非權威依據。
- `i18n/` 包含 README 的翻譯版本；部分內容未完整，可能落後英文版。

## 故障排除 🧯

| 現象 | 檢查 |
|---|---|
| 相機模式無法偵測到 SD 卡 | 確認接線與 `cardPin`（目前草稿為 `21`）是否正確。重新格式化 SD 卡（FAT/FAT32）後再試。 |
| 影像空白/噪點偏多 | 確認 `OUTCOL`/`OUTROW` 接線與多工控制腳位。檢查供電穩定性與感測板連接。 |
| Processing 檢視器無資料 | 確認 Arduino 與 Processing 的 `115200` 串列鮑率一致。將 `portName` 從 `COM32` 改為實際埠號。 |
| 擷取速度非常慢 | 完整 32x32 掃描與平均運算（預設 `avgCount=10`）屬正常行為。 |

## 路線圖與技術筆記 🗺️

`docs/` 目錄追蹤規劃中的技術方向與探索方向：

| 文件 | 重點 |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | 僅韌體的時間差分 / 事件 |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | 比較器輔助事件偵測 |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | 類比前端與偏壓改進 |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | 中長期事件感測器重設計選項 |

## 貢獻 🤝

歡迎提出貢獻，建議方向如下：

- 硬體上電檢查與已驗證的腳位對照表。
- 工具鏈／版本驗證（Arduino core、函式庫、Processing 版本）。
- 文件更新，尤其是接線圖與校正流程。
- 待語言目標確定後，補齊 `i18n/` 的 README 翻譯。

如果修改涉及硬體或韌體行為，請附上：
- 測試設定明細。
- 板卡版本資訊。
- 前後行為差異紀錄。

## 授權

- 本專案倉儲中的 `LICENSE` 目前指出整體專案採用 GPL-3.0 發佈。
- 固件標頭註解亦提及部分原始碼頭部內容採用 `CC BY-NC-SA 3.0`。

當你重新散佈二進位、硬體成果或衍生作品時，請先確認所變更檔案的授權條件，並確認與你的用途是否兼容。


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
