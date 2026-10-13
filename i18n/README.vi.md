[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://lazying.art)

# digiOBSCURA - Cảm biến ảnh và máy ảnh kỹ thuật số DIY 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](../LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#tổng-quan)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#tính-năng)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#kiến-trúc-phần-cứngfirmware-hiện-tại)
[![Contributions](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#đóng-góp-)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> Một hệ sinh thái chụp ảnh DIY hoàn toàn mở: PCB cảm biến tự thiết kế, bo mạch MCU, firmware, công cụ xem webcam và các bộ phận vỏ có thể in 3D.

## 📚 Mục lục

- [Tổng quan](#tổng-quan)
- [Demo](#demo)
- [Tính năng](#tính-năng)
- [Cấu trúc dự án](#cấu-trúc-dự-án)
- [Kiến trúc phần cứng/Firmware (Hiện tại)](#kiến-trúc-phần-cứngfirmware-hiện-tại)
- [Yêu cầu](#yêu-cầu)
- [Cài đặt](#cài-đặt)
- [Sử dụng](#sử-dụng)
- [Ghi chú cấu hình](#ghi-chú-cấu-hình)
- [Ví dụ](#ví-dụ)
- [Ghi chú phát triển](#ghi-chú-phát-triển)
- [Khắc phục sự cố](#khắc-phục-sự-cố)
- [Lộ trình và ghi chú kỹ thuật](#lộ-trình-và-ghi-chú-kỹ-thuật)
- [Đóng góp](#đóng-góp)
- [Hỗ trợ](#%E2%9D%A4%EF%B8%8F-support)
- [Giấy phép](#giấy-phép)

## Tổng quan 🛰️

`digiOBSCURA` là hệ thống cảm biến hình ảnh DIY 32x32 được xây dựng quanh một mảng phototransistor tùy chỉnh và bộ điều khiển dựa trên SAMD21.

Kho này chứa:
- Firmware cho chế độ chụp ảnh độc lập ghi trực tiếp ra thẻ SD.
- Firmware + sketch Processing cho xem trực tiếp kiểu webcam qua serial.
- Toàn bộ file nguồn KiCad cho cả hai bo mạch (`image_sensor` và `mcu`) cùng các file Gerber sản xuất.
- Tài nguyên cơ khí (`.f3d`, `.stl`) cho phần vỏ, ống kính, chân tripod.
- Ghi chú thiết kế và tài liệu lộ trình nâng cấp theo giai đoạn trong [`docs/`](../docs/).

## Tóm tắt nhanh 📌

| Hạng mục | Chi tiết |
| --- | --- |
| Quy mô dự án | Camera + firmware + viewer + thiết kế cơ khí |
| Mảng cảm biến | `32x32` (`1024`) ma trận phototransistor tự thiết kế |
| Bộ điều khiển lõi | `SAMD21G18A` |
| Đầu ra hình ảnh | BMP trên thẻ SD / luồng khung ảnh trực tiếp qua serial |
| Quy trình mục tiêu | Chụp độc lập • Xem trước kiểu webcam |

## Demo 🎬

Xem video:

[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

Xem BOM cho cả hai bo tại: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Tính năng ✨

- Mảng cảm biến ảnh phototransistor tùy chỉnh 32x32 (1024 pixel).
- Đánh địa chỉ hàng/cột bằng dual ADG732 mux.
- Quy trình camera độc lập:
  - Giao diện trạng thái OLED.
  - Đầu vào shutter.
  - Xuất ra thẻ SD (`DGO_####.bmp`, tự tăng dần).
- Quy trình webcam:
  - Firmware Arduino phản hồi yêu cầu pixel qua serial ở `115200`.
  - Ứng dụng Processing hiển thị ảnh xám 32x32 trực tiếp.
- Mã nguồn phần cứng mở:
  - Sơ đồ mạch/PCB KiCad.
  - Kết quả sản xuất và BOM artifacts.
  - File cơ khí có thể in.

## Cấu trúc dự án 🧱

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

## Kiến trúc phần cứng/Firmware (Hiện tại) 🧰

Dựa trên [`docs/overview.md`](../docs/overview.md):

| Tiểu hệ thống | Chi tiết |
|---|---|
| Mảng cảm biến | `1024 x ALS-PT19` phototransistors |
| Cách đánh địa chỉ | `2 x ADG732` analog mux (hàng + cột) |
| Bo lõi | `SAMD21G18A` |
| Đường dẫn nguồn | Pin `18650`, sạc `MCP73831`, điều áp `3.3 V` |
| Đường tín hiệu | `OUTCOL` được kéo mức cao bởi GPIO MCU; đường cảm biến đã chọn được đọc trên `OUTROW` qua ADC |
| Hành vi chụp | Firmware camera quét toàn bộ khung và ghi file BMP 24-bit |

## Yêu cầu 🧰

### Phần cứng

- Bo `image_sensor` và `mcu` đã lắp ráp (hoặc dây nối tương đương).
- Bo mục tiêu tương thích SAMD21 có thể thay thế với đúng assignment chân theo firmware.
- Thẻ MicroSD (chế độ camera).
- OLED `64x48` SSD1306 (chế độ camera, SPI phần mềm 4 dây trong sketch hiện tại).
- Nút/công tắc đầu vào shutter.

### Phần mềm

- Arduino IDE (hoặc Arduino CLI) với board package SAMD21.
- Thư viện Arduino bắt buộc cho firmware camera:
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/runtime cho [`Firmware/digiOBSCURA_Webcam_Processing`](../Firmware/digiOBSCURA_Webcam_Processing/).

## Cài đặt 🧩

1. Clone repository:

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. Mở một sketch firmware trong Arduino IDE:
- Chế độ camera: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](../Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Chế độ webcam: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](../Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Chọn board SAMD21 và cổng đúng, rồi upload.

4. Với chế độ webcam, mở sketch Processing:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](../Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Sử dụng 🚀

### Chế độ Camera (luồng snapshot SD) 📸

1. Flash `digiOBSCURA_camera.ino`.
2. Gắn thẻ SD đã định dạng FAT/FAT32.
3. Cấp nguồn/reset board; firmware sẽ tìm tên file `DGO_####.bmp` chưa dùng tiếp theo.
4. Kích hoạt chụp bằng đầu vào shutter.
5. Thiết bị chụp và ghi BMP lên thẻ SD, đồng thời hiển thị ảnh thu nhỏ trên OLED.

### Chế độ Webcam (xem trực tiếp do host render) 🖥️

1. Flash `digiOBSCURA_Webcam_Arduino.ino`.
2. Kết nối qua USB serial ở `115200`.
3. Trong sketch Processing, đặt `String portName` đúng với cổng serial thực tế của bạn (mẫu mặc định là `COM32`).
4. Chạy sketch Processing; nó sẽ polling 32x32 pixel và render khung ảnh xám theo thời gian thực.

## Ghi chú cấu hình 🛠️

Các mặc định hiện tại ở mức firmware theo source:

| Thiết lập | Giá trị / hành vi hiện tại |
|---|---|
| Độ phân giải | Cố định `32x32` |
| Độ phân giải ADC | `analogReadResolution(12)` |
| Trung bình ở chế độ camera | `avgCount = 10` (chất lượng cao hơn, tốc độ khung thấp hơn) |
| Chân SD CS (camera sketch) | `cardPin = 21` |
| Baud serial (đường webcam) | `115200` |
| Mapping I/O | Các mảng/biến hardcoded (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, ...) |

Nếu dây nối của bạn khác, cập nhật phần định nghĩa chân gần đầu mỗi file `.ino` trước khi flash.

## Ví dụ 🧪

### Mở và chỉnh sửa firmware camera

```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Mở và chỉnh sửa webcam viewer bằng Processing

```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### Mở nguồn PCB trong KiCad

- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## Ghi chú phát triển 📘

- Hiện tại repository chưa có hệ thống build thống nhất (`Makefile`, `platformio.ini`, `package.json`, ...).
- Dòng phát triển hiện tại trải qua Arduino + Processing + KiCad + file CAD cơ khí.
- `PCB/image_sensor/README.md` và `PCB/mcu/README.md` hiện mới chỉ mang tính placeholder/template, chưa phải nguồn chính thức.
- `i18n/` chứa các bản README đã dịch; một số phần có thể chưa cập nhật đầy đủ so với nội dung tiếng Anh.

## Khắc phục sự cố 🧯

| Triệu chứng | Kiểm tra |
|---|---|
| Không phát hiện được thẻ SD trong chế độ camera | Kiểm tra wiring và `cardPin` (`21` trong sketch hiện tại). Định dạng lại thẻ SD (FAT/FAT32) rồi thử lại. |
| Dữ liệu hình ảnh trống/nhiễu | Kiểm tra wiring `OUTCOL`/`OUTROW` và các chân điều khiển mux. Kiểm tra độ ổn định nguồn và kết nối board cảm biến. |
| Processing viewer không hiển thị dữ liệu | Xác nhận serial baud `115200` trong cả Arduino và Processing. Đặt `portName` từ `COM32` sang đúng cổng của bạn. |
| Chụp rất chậm | Đây là hành vi mong đợi với quét toàn cảnh 32x32 và trung bình (`avgCount=10` theo mặc định). |

## Lộ trình và ghi chú kỹ thuật 🗺️

Thư mục `docs/` theo dõi các hướng kỹ thuật dự kiến và khảo sát:

| Tài liệu | Tập trung |
|---|---|
| [`docs/01-firmware-differencing.md`](../docs/01-firmware-differencing.md) | Temporal differencing / events chỉ qua firmware |
| [`docs/02-comparator-event-mode.md`](../docs/02-comparator-event-mode.md) | Phát hiện events với comparator hỗ trợ |
| [`docs/03-bias-and-buffer-upgrade.md`](../docs/03-bias-and-buffer-upgrade.md) | Cải thiện analog front-end và bias |
| [`docs/04-full-event-sensor-redesign.md`](../docs/04-full-event-sensor-redesign.md) | Lựa chọn tái thiết kế event-sensor dài hạn |

## Đóng góp 🤝

Đóng góp đều được chào đón. Một vài hướng đóng góp đề xuất:

- Ghi chú bring-up phần cứng và bản đồ chân đã được xác thực.
- Kiểm chứng toolchain/version (Arduino core, thư viện, phiên bản Processing).
- Cập nhật tài liệu, đặc biệt là sơ đồ nối dây và quy trình hiệu chuẩn.
- Dịch `README` trong `i18n/` sau khi hoàn thiện ngôn ngữ mục tiêu.

Cho các thay đổi ảnh hưởng hành vi phần cứng hoặc firmware, hãy kèm:
- Chi tiết thiết lập test.
- Ngữ cảnh revision bo mạch.
- Mô tả hành vi trước/sau.

## Giấy phép

- `LICENSE` của repository hiện chỉ rõ dự án dùng giấy phép GPL-3.0 ở cấp repository.
- Các comment phần đầu firmware cũng tham chiếu `CC BY-NC-SA 3.0` cho một phần nội dung.

Khi phân phối lại binary, tài sản hardware hoặc bản phái sinh, hãy kiểm tra lại điều khoản cấp phép chính xác trong các file bạn chỉnh sửa để đảm bảo tương thích với mục đích sử dụng.


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
