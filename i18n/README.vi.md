[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)



# digiOBSCURA - Cảm Biến Ảnh và Máy Ảnh Kỹ Thuật Số DIY 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](../LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#tong-quan)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#tinh-nang)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#kien-truc-phan-cungfirmware-hien-tai)

> Một bộ imaging stack DIY mở hoàn toàn: PCB cảm biến tự thiết kế, bo MCU, firmware, trình xem webcam và các phần vỏ có thể in 3D.

## Tổng Quan
`digiOBSCURA` là hệ thống chụp ảnh DIY 32x32 được xây dựng quanh mảng phototransistor tự thiết kế và bộ điều khiển nền tảng SAMD21.

Kho này bao gồm:
- Firmware cho chế độ chụp độc lập và lưu vào thẻ SD.
- Firmware + sketch Processing cho chế độ xem trực tiếp kiểu webcam qua serial.
- Bộ mã nguồn KiCad đầy đủ cho cả hai bo (`image_sensor` và `mcu`) cùng Gerber sản xuất.
- Tài nguyên cơ khí (`.f3d`, `.stl`) cho vỏ/lens/chân tripod.
- Ghi chú thiết kế và tài liệu lộ trình nâng cấp theo giai đoạn trong [`docs/`](../docs/).

### Tóm Tắt Nhanh
| Khu vực | Triển khai hiện tại |
|---|---|
| Mảng ảnh | Ma trận phototransistor tự thiết kế `32x32` (`1024` pixel) |
| Ghép kênh | `2 x ADG732` (chọn hàng + cột) |
| Bộ điều khiển | `SAMD21G18A` |
| Đầu ra camera | File BMP trên thẻ SD (`DGO_0000.bmp`, tự tăng) |
| Chế độ live | Serial (`115200`) + renderer host bằng Processing |
| Tài nguyên thiết kế | KiCad + Gerber + BOM artifacts + file `F3D`/`STL` |

## Demo (Nội Dung Gốc Chuẩn) 🎬
Xem video tại đây:  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

Danh sách BOM cho cả hai bo: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Tính Năng
- Mảng ảnh phototransistor tự thiết kế 32x32 (1024 pixel).
- Định địa chỉ hàng/cột bằng dual ADG732 mux.
- Quy trình camera độc lập:
  - OLED UI.
  - Ngõ vào shutter.
  - Xuất ra thẻ SD (`DGO_0000.bmp`, tự tăng).
- Quy trình webcam:
  - Firmware Arduino phản hồi yêu cầu pixel qua serial ở `115200`.
  - Ứng dụng Processing render hình xám 32x32 theo thời gian thực.
- Mã nguồn phần cứng mở:
  - Schematic/PCB KiCad.
  - Output phục vụ sản xuất và BOM artifacts.
  - File cơ khí có thể in.

## Cấu Trúc Dự Án

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

## Kiến Trúc Phần Cứng/Firmware (Hiện Tại)
Dựa trên [`docs/overview.md`](../docs/overview.md):

| Phân hệ | Chi tiết |
|---|---|
| Ma trận cảm biến | `1024 x ALS-PT19` phototransistor |
| Định địa chỉ | `2 x ADG732` analog mux (hàng + cột) |
| Bo lõi | `SAMD21G18A` |
| Đường nguồn | Pin `18650`, sạc `MCP73831`, ổn áp `3.3 V` |
| Đường tín hiệu | `OUTCOL` được kéo mức cao bởi GPIO MCU; đường cảm biến được chọn sẽ đọc trên `OUTROW` qua ADC |
| Hành vi chụp | Firmware camera quét toàn khung và ghi file BMP 24-bit |

## Điều Kiện Tiên Quyết

### Phần cứng
- Bo `image_sensor` và `mcu` đã lắp ráp (hoặc dây nối tương đương).
- Bo mục tiêu tương thích SAMD21 khớp với mapping chân trong firmware.
- Thẻ MicroSD (chế độ camera).
- OLED SSD1306 64x48 (chế độ camera, software SPI 4 dây trong sketch hiện tại).
- Nút/công tắc shutter input.

### Phần mềm
- Arduino IDE (hoặc Arduino CLI) với board package SAMD21.
- Thư viện Arduino cần cho firmware camera:
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/runtime cho [`Firmware/digiOBSCURA_Webcam_Processing`](../Firmware/digiOBSCURA_Webcam_Processing/).

## Cài Đặt

1. Clone kho này:

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. Mở một sketch firmware trong Arduino IDE:
- Chế độ camera: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](../Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Chế độ webcam: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](../Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Chọn đúng board/port SAMD21 rồi nạp chương trình.

4. Với chế độ webcam, mở sketch Processing:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](../Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Cách Sử Dụng

### Chế Độ Camera (quy trình snapshot SD) 📸
1. Nạp `digiOBSCURA_camera.ino`.
2. Gắn thẻ SD.
3. Khi khởi động, firmware kiểm tra tên file và tìm `DGO_####.bmp` còn trống tiếp theo.
4. Nhấn shutter input.
5. Thiết bị chụp, ghi BMP vào SD, rồi hiển thị thumbnail preview trên OLED.

### Chế Độ Webcam (live view render phía host) 🖥️
1. Nạp `digiOBSCURA_Webcam_Arduino.ino`.
2. Kết nối USB serial (`115200`).
3. Trong sketch Processing, đặt đúng cổng serial (file hiện có `String portName = "COM32"`).
4. Chạy sketch Processing để lấy giá trị pixel và vẽ khung 32x32.

## Ghi Chú Cấu Hình
Giá trị mặc định ở cấp firmware từ mã nguồn hiện tại:

| Thiết lập | Giá trị / hành vi hiện tại |
|---|---|
| Độ phân giải | Cố định `32x32` |
| Độ phân giải ADC | `analogReadResolution(12)` |
| Lấy trung bình ở camera mode | `avgCount = 10` (tăng chất lượng, giảm tốc độ khung hình) |
| Chân SD CS (camera sketch) | `cardPin = 21` |
| Baud serial (nhánh webcam) | `115200` |
| Mapping I/O | Mảng/biến hardcoded (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, v.v.) |

Nếu phần cứng của bạn đi dây khác, hãy cập nhật định nghĩa chân ở gần đầu mỗi file `.ino` trước khi nạp.

## Ví Dụ

### Mở và chỉnh sửa firmware camera
```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Mở và chỉnh sửa trình xem webcam bằng Processing
```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### Mở mã nguồn PCB trong KiCad
- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## Ghi Chú Phát Triển
- Kho này chưa có build system thống nhất (`Makefile`, `platformio.ini`, `package.json`, v.v.).
- Quy trình phát triển hiện trải trên Arduino + Processing + KiCad + file CAD cơ khí.
- `PCB/image_sensor/README.md` và `PCB/mcu/README.md` có vẻ cũ/dạng template và hiện chưa phải tài liệu tham chiếu chính thức.
- `i18n/` đã tồn tại và hiện trống; các bản dịch đang được lên kế hoạch nhưng chưa commit.

## Tài Liệu và Lộ Trình
Thư mục `docs/` chứa các ghi chú tiến hóa kỹ thuật theo từng giai đoạn:

| Tài liệu | Trọng tâm |
|---|---|
| [`docs/01-firmware-differencing.md`](../docs/01-firmware-differencing.md) | Temporal differencing/events chỉ bằng firmware |
| [`docs/02-comparator-event-mode.md`](../docs/02-comparator-event-mode.md) | Phát hiện sự kiện có hỗ trợ comparator |
| [`docs/03-bias-and-buffer-upgrade.md`](../docs/03-bias-and-buffer-upgrade.md) | Cải tiến analog front-end/bias |
| [`docs/04-full-event-sensor-redesign.md`](../docs/04-full-event-sensor-redesign.md) | Tùy chọn tái thiết kế event-sensor dài hạn |

## Khắc Phục Sự Cố
| Triệu chứng | Kiểm tra |
|---|---|
| Không nhận thẻ SD ở camera mode | Xác nhận dây nối và `cardPin` (`21` trong sketch hiện tại). Format lại thẻ SD (FAT/FAT32) và thử lại. |
| Dữ liệu ảnh trống/nhiễu | Kiểm tra dây `OUTCOL`/`OUTROW` và chân điều khiển mux. Kiểm tra độ ổn định nguồn và kết nối bo cảm biến. |
| Processing viewer không có dữ liệu | Xác nhận baud serial `115200` ở cả Arduino và Processing. Đổi `portName` từ `COM32` sang cổng thực tế của bạn. |
| Chụp rất chậm | Đây là hành vi dự kiến với quét đầy đủ 32x32 và lấy trung bình (`avgCount=10` mặc định). |

## Hỗ Trợ / Tài Trợ
Từ header firmware:

- Creator: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

Nếu bạn phát triển tiếp dự án này, hãy cân nhắc hỗ trợ tác giả gốc.

## Đóng Góp
Hoan nghênh mọi đóng góp. Một số hướng đóng góp đề xuất:

- Ghi chú bring-up phần cứng và pin map đã được xác thực.
- Xác thực toolchain/phiên bản (Arduino core, thư viện, phiên bản Processing).
- Cập nhật tài liệu, đặc biệt là sơ đồ đi dây và quy trình hiệu chuẩn.
- Bản dịch README trong `i18n/` khi danh sách ngôn ngữ mục tiêu được chốt.

Với thay đổi tác động đến hành vi phần cứng hoặc firmware, vui lòng kèm theo:
- Chi tiết môi trường kiểm thử.
- Bối cảnh phiên bản bo mạch.
- Ghi chú hành vi trước/sau.

## Giấy Phép
- File giấy phép kho: [`LICENSE`](../LICENSE) (văn bản GPL-3.0 có trong repo).
- Ghi chú header firmware: header `.ino` hiện ghi `cc-by-sa-nc-3.0`.

Lưu ý quan trọng: hai tín hiệu giấy phép này khác nhau. Cho đến khi maintainer làm rõ, hãy coi việc cấp phép cần xác nhận từ chủ dự án trước khi phân phối lại/sử dụng thương mại.

## Lời Cảm Ơn
- Sean Hodgins / công trình dự án digiOBSCURA và tài liệu/video công khai.
- Các nguồn lực cộng đồng open hardware và maker giúp các thử nghiệm cảm biến DIY trở nên khả thi.
