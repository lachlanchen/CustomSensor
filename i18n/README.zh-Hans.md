[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)



# digiOBSCURA - DIY 图像传感器与数码相机 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#hardwarefirmware-architecture-current)

> 一个完全开放的 DIY 成像技术栈：自定义传感器 PCB、MCU 板卡、固件、网络摄像头查看器，以及可打印外壳部件。

## 概览
`digiOBSCURA` 是一个基于自定义光电晶体管阵列与 SAMD21 控制器构建的 DIY `32x32` 成像系统。

本仓库包含：
- 用于独立相机拍摄并保存到 SD 卡的固件。
- 用于串口网络摄像头实时查看的固件 + Processing 草图。
- 两块板（`image_sensor` 与 `mcu`）的完整 KiCad 源文件，以及生产用 Gerber。
- 外壳/镜头/三脚架组件的机械资源（`.f3d`、`.stl`）。
- 位于 [`docs/`](./docs/) 的设计说明与分阶段升级路线文档。

### 快速一览
| 区域 | 当前实现 |
|---|---|
| 成像阵列 | `32x32` 自定义光电晶体管矩阵（`1024` 像素） |
| 复用方式 | `2 x ADG732`（行 + 列选择） |
| 控制器 | `SAMD21G18A` |
| 相机输出 | SD 卡 BMP 文件（`DGO_0000.bmp`，自动递增） |
| 实时模式 | 串口（`115200`）+ Processing 主机渲染 |
| 设计资源 | KiCad + Gerber + BOM 工件 + `F3D`/`STL` 文件 |

## 演示（规范保留内容） 🎬
查看视频：  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

两块板的 BOM 在这里： https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## 功能特性
- `32x32`（1024 像素）自定义光电晶体管成像阵列。
- 双 ADG732 复用器寻址，用于行/列像素选择。
- 独立相机工作流：
  - OLED UI。
  - 快门输入。
  - SD 卡输出（`DGO_0000.bmp`，自动递增）。
- 网络摄像头工作流：
  - Arduino 固件以 `115200` 响应串口像素请求。
  - Processing 应用渲染实时 `32x32` 灰度画面。
- 开源硬件资源：
  - KiCad 原理图/PCB。
  - 生产输出与 BOM 工件。
  - 可打印机械文件。

## 项目结构

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

## 硬件/固件架构（当前）
基于 [`docs/overview.md`](./docs/overview.md)：

| 子系统 | 细节 |
|---|---|
| 传感器矩阵 | `1024 x ALS-PT19` 光电晶体管 |
| 寻址 | `2 x ADG732` 模拟复用器（行 + 列） |
| 核心板 | `SAMD21G18A` |
| 电源路径 | `18650` 电池、`MCP73831` 充电器、`3.3 V` 稳压 |
| 信号路径 | `OUTCOL` 由 MCU GPIO 拉高；选中的传感器路径通过 ADC 在 `OUTROW` 读取 |
| 拍摄行为 | 相机固件执行整帧扫描并写入 24 位 BMP 文件 |

## 前置要求

### 硬件
- 已组装的 `image_sensor` 与 `mcu` 板（或等效连线）。
- 与固件引脚分配匹配的 SAMD21 兼容目标板。
- MicroSD 卡（相机模式）。
- 64x48 SSD1306 OLED（相机模式，当前草图使用 4 线软件 SPI）。
- 快门按钮/开关输入。

### 软件
- Arduino IDE（或 Arduino CLI），并安装 SAMD21 板卡包。
- 相机固件所需 Arduino 库：
  - `U8g2`
  - `SPI`
  - `SD`
- 用于 [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/) 的 Processing IDE/运行时。

## 安装

1. 克隆本仓库：

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. 在 Arduino IDE 中打开一个固件草图：
- 相机模式： [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- 网络摄像头模式： [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. 选择正确的 SAMD21 板卡/端口并上传。

4. 对于网络摄像头模式，打开 Processing 草图：
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## 使用

### 相机模式（SD 快照工作流） 📸
1. 烧录 `digiOBSCURA_camera.ino`。
2. 插入 SD 卡。
3. 启动时，固件会检查文件名并找到下一个可用的 `DGO_####.bmp`。
4. 按下快门输入。
5. 设备拍摄后将 BMP 写入 SD 卡，并在 OLED 上显示缩略图预览。

### 网络摄像头模式（主机渲染实时画面） 🖥️
1. 烧录 `digiOBSCURA_Webcam_Arduino.ino`。
2. 通过 USB 串口连接（`115200`）。
3. 在 Processing 草图中设置正确串口（文件当前为 `String portName = "COM32"`）。
4. 运行 Processing 草图以轮询像素值并绘制 `32x32` 帧。

## 配置说明
当前源码中的固件默认值：

| 设置 | 当前值 / 行为 |
|---|---|
| 分辨率 | 固定 `32x32` |
| ADC 分辨率 | `analogReadResolution(12)` |
| 相机模式平均 | `avgCount = 10`（提高画质，降低帧速度） |
| SD CS 引脚（相机草图） | `cardPin = 21` |
| 串口波特率（网络摄像头路径） | `115200` |
| I/O 映射 | 硬编码数组/变量（`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW` 等） |

如果你的硬件连线不同，请在烧录前修改每个 `.ino` 文件顶部附近的引脚定义。

## 示例

### 打开并编辑相机固件
```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### 打开并编辑 Processing 网络摄像头查看器
```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### 在 KiCad 中打开 PCB 源文件
- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## 开发说明
- 本仓库没有统一的构建系统（`Makefile`、`platformio.ini`、`package.json` 等）。
- 当前开发流程横跨 Arduino + Processing + KiCad + 机械 CAD 文件。
- `PCB/image_sensor/README.md` 与 `PCB/mcu/README.md` 看起来较陈旧/偏模板化，目前不是权威项目文档。
- `i18n/` 目录已存在，当前正在逐步提交各语言翻译。

## 文档与路线图
`docs/` 目录包含分阶段技术演进说明：

| 文档 | 重点 |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | 仅固件的时域差分/事件 |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | 借助比较器的事件检测 |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | 模拟前端/偏置改进 |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | 更长期的事件传感器重构选项 |

## 故障排查
| 症状 | 检查项 |
|---|---|
| 相机模式下检测不到 SD 卡 | 确认连线与 `cardPin`（当前草图为 `21`）。将 SD 卡格式化为 FAT/FAT32 后重试。 |
| 图像数据空白/噪声大 | 检查 `OUTCOL`/`OUTROW` 连线与复用器控制引脚。确认供电完整性与传感器板连接。 |
| Processing 查看器无数据 | 确认 Arduino 与 Processing 两端串口波特率均为 `115200`。将 `portName` 从 `COM32` 改为实际端口。 |
| 拍摄非常慢 | 这是全 `32x32` 扫描加平均（默认 `avgCount=10`）下的预期表现。 |

## 支持 / 赞助
来自固件头部信息：

- Creator: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

如果你基于此项目继续开发，欢迎支持原始作者。

## 贡献
欢迎贡献。建议的贡献方向：

- 硬件点亮记录与已验证引脚映射。
- 工具链/版本验证（Arduino core、库、Processing 版本）。
- 文档更新，尤其是连线图与标定流程。
- 语言目标确定后，补充 `i18n/` README 翻译。

对于涉及硬件或固件行为的改动，请附带：
- 测试环境细节。
- 板卡版本背景。
- 变更前/后的行为说明。

## 许可证
- 仓库许可证文件：[`LICENSE`](./LICENSE)（仓库内包含 GPL-3.0 文本）。
- 固件头部说明：当前 `.ino` 头部声明为 `cc-by-sa-nc-3.0`。

重要说明：这两个许可证信号并不一致。在维护者澄清前，请将再分发/商用视为需要项目所有者确认。

## 致谢
- Sean Hodgins / digiOBSCURA 项目工作及其公开文档与视频。
- 使 DIY 传感器实验成为可能的开源硬件与创客社区资源。
