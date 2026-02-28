[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# digiOBSCURA - самодельный датчик изображения и цифровая камера 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#hardware-firmware-architecture-current)
[![Contributions](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#contributing)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> Полный открытый DIY-стек для обработки изображений: пользовательская плата датчика, плата MCU, прошивка, просмотрщик в стиле веб-камеры и печатные элементы корпуса.

## 📚 Содержание

- [Обзор](#overview)
- [Демонстрация](#demo)
- [Возможности](#features)
- [Структура проекта](#project-structure)
- [Аппаратная и прошивочная архитектура (текущая)](#hardwarefirmware-architecture-current)
- [Требования](#prerequisites)
- [Установка](#installation)
- [Использование](#usage)
- [Заметки по настройке](#configuration-notes)
- [Примеры](#examples)
- [Замечания по разработке](#development-notes)
- [Устранение неполадок](#troubleshooting)
- [План развития и технические заметки](#roadmap-and-technical-notes)
- [Вклад в проект](#contributing)
- [Поддержка](#%E2%9D%A4%EF%B8%8F-support)
- [Лицензия](#license)

## Overview 🛰️

`digiOBSCURA` — это DIY-система визуализации `32x32`, построенная вокруг пользовательской матрицы фототранзисторов и контроллера на базе SAMD21.

В этом репозитории содержится:
- Прошивка для автономного съёмки с сохранением на SD-карту.
- Прошивка + скетч Processing для просмотра вживую в стиле веб-камеры по последовательному порту.
- Полные исходники KiCad для обеих плат (`image_sensor` и `mcu`) и производственные Gerber-файлы.
- Механические ресурсы (`.f3d`, `.stl`) для корпуса, линзы и деталей штатива.
- Заметки по проектированию и документы по поэтапному роадмапу в [`docs/`](./docs/).

## Quick Snapshot 📌

| Пункт | Детали |
| --- | --- |
| Размер проекта | Камера + прошивка + просмотрщик + механика |
| Матрица датчика | `32x32` (`1024`) пользовательская матрица фототранзисторов |
| Основной контроллер | `SAMD21G18A` |
| Вывод изображения | BMP на SD-карту / сериализация кадров в реальном времени |
| Целевые сценарии | Автономная съёмка • Просмотр в стиле веб-камеры |

## Demo 🎬

Смотрите видео:

[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

BOM для обеих плат находится здесь: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Features ✨

- Пользовательская матрица фототранзисторного изображения `32x32` (`1024` пикселя).
- Двойное адресирование ADG732 для выбора пикселя по строке и столбцу.
- Автономный режим камеры:
  - Интерфейс состояния на OLED.
  - Вход затвора.
  - Вывод в SD-карту (`DGO_####.bmp`, автоинкремент имени).
- Режим веб-камеры:
  - Прошивка Arduino отвечает на запросы пикселей по последовательному порту на скорости `115200`.
  - Приложение Processing отрисовывает живое серое 32x32 изображение.
- Источники open hardware:
  - Схемы/PCBs в KiCad.
  - Производственные артефакты и BOM.
  - Файлы механики для печати.

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

## Hardware/Firmware Architecture (Current) 🧰

На основе [`docs/overview.md`](./docs/overview.md):

| Подсистема | Детали |
|---|---|
| Матрица сенсора | `1024 x ALS-PT19` фототранзисторов |
| Адресация | `2 x ADG732` аналоговых мультиплексоров (строка + столбец) |
| Основная плата | `SAMD21G18A` |
| Питание | `18650` аккумулятор, зарядное устройство `MCP73831`, стабилизация `3.3 V` |
| Сигнальный путь | `OUTCOL` подается высоким уровнем с GPIO MCU; выбранный маршрут датчика считывается через ADC с `OUTROW` |
| Поведение съёмки | Прошивка камеры выполняет полное сканирование кадра и записывает файлы BMP 24-bit |

## Prerequisites 🧰

### Hardware

- Собранные платы `image_sensor` и `mcu` (или эквивалентное подключение).
- Целевая плата с SAMD21 с совпадающими назначениями выводов, используемыми в прошивке.
- MicroSD-карта (режим камеры).
- OLED `64x48` SSD1306 (режим камеры, текущий скетч использует 4-wire software SPI).
- Кнопка/переключатель затвора.

### Software

- Arduino IDE (или Arduino CLI) с пакетом плат SAMD21.
- Необходимые библиотеки Arduino для прошивки камеры:
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/runtime для [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/).

## Installation 🧩

1. Клонируйте этот репозиторий:

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. Откройте один из скетчей прошивки в Arduino IDE:
- Режим камеры: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Режим веб-камеры: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Выберите правильную плату SAMD21 и порт, затем загрузите прошивку.

4. Для режима веб-камеры откройте скетч Processing:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Usage 🚀

### Camera Mode (SD snapshot workflow) 📸

1. Прошейте `digiOBSCURA_camera.ino`.
2. Вставьте отформатированную под FAT/FAT32 SD-карту.
3. Включите/перезагрузите плату; прошивка найдет следующее свободное имя `DGO_####.bmp`.
4. Запустите съёмку с помощью входа затвора.
5. Устройство делает снимок, записывает BMP на SD и показывает миниатюру на OLED.

### Webcam Mode (host-rendered live view) 🖥️

1. Прошейте `digiOBSCURA_Webcam_Arduino.ino`.
2. Подключитесь по USB serial на скорости `115200`.
3. В скетче Processing установите `String portName` в ваш фактический последовательный порт (в шаблоне используется `COM32`).
4. Запустите скетч Processing; он опрашивает 32x32 пикселей и отрисовывает живой градационно-серый кадр.

## Configuration Notes 🛠️

Текущие значения по умолчанию на уровне прошивки из исходного кода:

| Настройка | Текущее значение / поведение |
|---|---|
| Разрешение | Фиксированное `32x32` |
| Разрешение ADC | `analogReadResolution(12)` |
| Усреднение в режиме камеры | `avgCount = 10` (более высокое качество, меньшая скорость кадров) |
| SD CS pin (скетч камеры) | `cardPin = 21` |
| Baud rate (webcam) | `115200` |
| I/O mapping | Жёстко прописанные массивы/переменные (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW` и т.д.) |

Если схема подключений отличается, обновите определения пинов в начале каждого файла `.ino` перед прошивкой.

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

- В репозитории нет единой системы сборки (`Makefile`, `platformio.ini`, `package.json` и т.д.).
- На текущий момент разработка затрагивает Arduino + Processing + KiCad + файлы механического CAD.
- `PCB/image_sensor/README.md` и `PCB/mcu/README.md` сейчас содержат шаблонный/заглушечный контент и не являются авторитетным источником.
- В `i18n/` лежат переводы README; они могут быть неполными и отставать от английской версии.

## Troubleshooting 🧯

| Симптом | Что проверить |
|---|---|
| SD-карта не определяется в режиме камеры | Проверьте проводку и `cardPin` (`21` в текущем скетче). Переформатируйте SD-карту (FAT/FAT32) и повторите попытку. |
| Пустые/шумные данные изображения | Проверьте проводку `OUTCOL`/`OUTROW` и управляющие пины мультиплексора. Проверьте целостность питания и подключения платы сенсора. |
| Просмотрщик Processing не показывает данные | Убедитесь в скорости `115200` как в Arduino, так и в Processing. Измените `portName` с `COM32` на ваш фактический порт. |
| Очень медленная съёмка | Ожидаемо для полного сканирования 32x32 с усреднением (`avgCount=10` по умолчанию). |

## Roadmap and Technical Notes 🗺️

Каталог `docs/` отслеживает планируемые и исследовательские технические направления:

| Документ | Направление |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | Дифференцирование по времени только в прошивке / события |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | Обнаружение событий с поддержкой компаратора |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | Улучшения аналогового фронт-энда и смещения |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | Долгосрочные варианты редизайна event-датчика |

## Contributing 🤝

Вклады приветствуются. Рекомендуемые направления для участия:

- Заметки по запуску аппаратной части и подтверждённые карты пинов.
- Проверка toolchain/версий (ядро Arduino, библиотеки, версия Processing).
- Обновление документации, особенно схем и рабочего процесса калибровки.

- README переводы в `i18n/` после утверждения целевых языков.

Для изменений, затрагивающих поведение аппаратной части или прошивки, добавляйте:
- Детали тестового стенда.
- Контекст ревизии платы.
- Наблюдения до/после изменений.

## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |

## License

- `LICENSE` в этом репозитории указывает, что проект распространяется под GPL-3.0 на уровне репозитория.
- Заголовки исходников прошивки также ссылаются на `CC BY-NC-SA 3.0` для части содержимого.

При распространении бинарных файлов, файлов аппаратных артефактов или производных работ проверяйте точные условия лицензирования в изменяемых файлах и гарантируйте совместимость с вашим сценарном применения.
