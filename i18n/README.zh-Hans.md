[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# digiOBSCURA - 一个 DIY 图像传感器与数字相机 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#hardware-firmware-architecture-current)
[![Contributions](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#contributing)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> 一个完整开源的 DIY 成像方案：自研传感器 PCB、MCU 板、固件、网络摄像头风格取景器，以及可打印外壳组件。

## 📚 目录

- [概述](#概述)
- [演示](#演示)
- [特性](#特性)
- [项目结构](#项目结构)
- [硬件/固件架构（当前）](#硬件固件架构当前)
- [先决条件](#先决条件)
- [安装](#安装)
- [使用方法](#使用方法)
- [配置说明](#配置说明)
- [示例](#示例)
- [开发说明](#开发说明)
- [故障排查](#故障排查)
- [路线图与技术说明](#路线图与技术说明)
- [贡献](#贡献)
- [支持](#%E2%9D%A4%EF%B8%8F-support)
- [许可证](#许可证)

## 概述 🛰️

`digiOBSCURA` 是一套基于自研光电晶体阵列和 SAMD21 控制器构建的 DIY 32x32 成像系统。

本仓库包含：
- 用于独立拍照到 SD 卡的固件。
- 用于串口网页摄像风格实时预览的固件 + Processing 草图。
- 两块主板（`image_sensor` 与 `mcu`）的完整 KiCad 源文件，以及生产用 Gerber 文件。
- 外壳/镜头/三脚架零件的机械文件（`.f3d`, `.stl`）。
- 设计说明和分阶段升级路线文档，位于 [`docs/`](./docs/)。

## 快速摘要 📌

| 项目 | 说明 |
| --- | --- |
| 项目规模 | 相机 + 固件 + 预览程序 + 机械设计 |
| 传感器阵列 | `32x32`（`1024`）自研光电晶体矩阵 |
| 核心控制器 | `SAMD21G18A` |
| 图像输出 | SD 卡 BMP / 串口实时帧流 |
| 目标工作流 | 独立拍摄 • 网页摄像头风格预览 |

## 演示 🎬

查看视频：

[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

两块主板的物料清单见： https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## 特性 ✨

- 32x32（1024 像素）自研光电晶体成像阵列。
- 通过两个 ADG732 复用器进行行列像素选择。
- 独立相机工作流：
  - OLED 状态界面。
  - 快门输入。
  - SD 卡输出（`DGO_####.bmp`，自动递增）。
- Webcam 工作流：
  - Arduino 固件在 `115200` 上响应串口像素请求。
  - Processing 应用渲染 32x32 的实时灰度画面。
- 开源硬件源文件：
  - KiCad 原理图/PCB。
  - 生产输出与 BOM 产物。
  - 可打印机械文件。

## 项目结构 🧱

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

## 硬件/固件架构（当前） 🧰

基于 [`docs/overview.md`](./docs/overview.md)：

| 子系统 | 说明 |
|---|---|
| 传感器矩阵 | `1024 x ALS-PT19` 光电晶体 |
| 寻址方式 | `2 x ADG732` 模拟复用器（行 + 列） |
| 主控板 | `SAMD21G18A` |
| 电源路径 | `18650` 电池、`MCP73831` 充电管理、`3.3 V` 稳压 |
| 信号路径 | MCU GPIO 将 `OUTCOL` 拉高，所选传感路径通过 ADC 在 `OUTROW` 上读取 |
| 拍摄行为 | 相机固件执行全帧扫描并写入 24 位 BMP 文件 |

## 先决条件 🧰

### 硬件

- 已焊接好的 `image_sensor` 和 `mcu` 主板（或等效接线）。
- 与固件中引脚分配一致的 SAMD21 兼容目标板。
- MicroSD 卡（相机模式）。
- `64x48` SSD1306 OLED（相机模式，当前草图使用 4 线软件 SPI）。
- 快门按钮/开关输入。

### 软件

- 已安装 SAMD21 板卡支持包的 Arduino IDE（或 Arduino CLI）。
- 相机固件所需的 Arduino 库：
  - `U8g2`
  - `SPI`
  - `SD`
- 用于 [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/) 的 Processing IDE/运行环境。

## 安装 🧩

1. 克隆本仓库：

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. 在 Arduino IDE 中打开任意一个固件草图：
- 相机模式：[`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Webcam 模式：[`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. 选择正确的 SAMD21 板卡与串口并上传。

4. Webcam 模式下，打开 Processing 草图：
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## 使用方法 🚀

### 相机模式（SD 快照工作流） 📸

1. 刷写 `digiOBSCURA_camera.ino`。
2. 插入已格式化为 FAT/FAT32 的 SD 卡。
3. 上电/复位板卡，固件会扫描下一个可用的 `DGO_####.bmp` 文件名。
4. 用快门输入触发拍摄。
5. 设备拍摄并将 BMP 写入 SD，同时在 OLED 显示缩略图预览。

### Webcam 模式（主机渲染实时预览） 🖥️

1. 刷写 `digiOBSCURA_Webcam_Arduino.ino`。
2. 用 `115200` 连接 USB 串口。
3. 在 Processing 草图中将 `String portName` 改为实际串口（模板中使用 `COM32`）。
4. 运行 Processing 草图，它会轮询 32x32 像素并渲染灰度实时帧。

## 配置说明 🛠️

当前固件源码中的默认项如下：

| 设置 | 当前值 / 行为 |
|---|---|
| 分辨率 | 固定 `32x32` |
| ADC 分辨率 | `analogReadResolution(12)` |
| 相机模式平均值 | `avgCount = 10`（高质量，较低帧率） |
| SD CS 引脚（相机草图） | `cardPin = 21` |
| 串口波特率（webcam 路径） | `115200` |
| I/O 映射 | 硬编码数组/变量（`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW` 等） |

如果你的接线不同，请在每个 `.ino` 文件顶部更新引脚定义后再刷写。

## 示例 🧪

### 打开并编辑相机固件

```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### 打开并编辑 Processing webcam 查看器

```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### 在 KiCad 中打开 PCB 源文件

- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## 开发说明 📘

- 本仓库没有统一的构建系统（`Makefile`, `platformio.ini`, `package.json` 等）。
- 当前开发覆盖 Arduino + Processing + KiCad + 机械 CAD 文件。
- `PCB/image_sensor/README.md` 和 `PCB/mcu/README.md` 目前包含占位/模板性质内容，不能作为权威来源。
- `i18n/` 包含 README 的翻译版本，部分内容不完整，可能落后于英文版本。

## 故障排查 🧯

| 现象 | 检查 |
|---|---|
| 相机模式下识别不到 SD 卡 | 检查接线和 `cardPin`（当前草图中的值为 `21`）。重新格式化 SD 卡（FAT/FAT32）后重试。 |
| 图像为空/噪声过大 | 检查 `OUTCOL`/`OUTROW` 接线及复用控制引脚。检查供电完整性和传感板连接。 |
| Processing 查看器无数据显示 | 确认 Arduino 和 Processing 两端均使用 `115200` 串口波特率。将 `portName` 从 `COM32` 改为实际端口。 |
| 捕获非常慢 | 由于全 32x32 扫描与平均处理（默认 `avgCount=10`）是预期行为。 |

## 路线图与技术说明 🗺️

`docs/` 目录记录了计划与探索中的技术方向：

| 文档 | 关注点 |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | 仅固件的时间差分/事件 |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | 比较器辅助的事件检测 |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | 模拟前端与偏置优化 |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | 更长期的事件传感器重构方案 |

## 贡献 🤝

欢迎贡献，建议从以下方向着手：

- 硬件启动说明和已验证的引脚映射。
- 工具链/版本验证（Arduino 核心库、库文件、Processing 版本）。
- 文档更新，尤其是接线图和标定流程。

- 语言目标确认后补齐 `i18n/README` 翻译。

涉及硬件或固件行为变更的修改，请附带：
- 测试设置细节。
- 板卡版本信息。
- 前后行为差异说明。

## 许可证

- 本仓库当前的 `LICENSE` 表明整体项目采用 GPL-3.0 发布。
- 固件头注释中也有对部分源码头部内容使用 `CC BY-NC-SA 3.0` 的说明。

重新分发二进制文件、硬件产物或衍生作品时，请先确认所涉及文件的准确许可条件，并确保与你的用途兼容。


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
