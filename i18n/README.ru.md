[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)



# digiOBSCURA - DIY сенсор изображения и цифровая камера 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#обзор)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#возможности)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#архитектура-hardwarefirmware-текущее-состояние)

> Полностью открытый DIY-стек для получения изображений: кастомная плата сенсора, плата MCU, прошивка, просмотрщик в режиме веб-камеры и печатаемые детали корпуса.

## Обзор
`digiOBSCURA` - это DIY-система формирования изображения 32x32, построенная вокруг кастомного массива фототранзисторов и контроллера на базе SAMD21.

Этот репозиторий содержит:
- Прошивку для автономной съемки с сохранением на SD-карту.
- Прошивку + скетч Processing для просмотра в реальном времени в стиле веб-камеры по последовательному порту.
- Полные исходники KiCad для обеих плат (`image_sensor` и `mcu`) плюс производственные Gerber-файлы.
- Механические материалы (`.f3d`, `.stl`) для компонентов корпуса/линзы/штатива.
- Заметки по проектированию и документы по поэтапной дорожной карте улучшений в [`docs/`](./docs/).

### Кратко
| Область | Текущая реализация |
|---|---|
| Массив изображения | Кастомная матрица фототранзисторов `32x32` (`1024` пикселя) |
| Мультиплексирование | `2 x ADG732` (выбор строки + столбца) |
| Контроллер | `SAMD21G18A` |
| Выход камеры | BMP-файлы на SD-карте (`DGO_0000.bmp`, автоинкремент) |
| Live-режим | Serial (`115200`) + рендеринг на хосте через Processing |
| Дизайн-артефакты | KiCad + Gerber + BOM-артефакты + файлы `F3D`/`STL` |

## Демонстрация (каноническое существующее содержимое) 🎬
Посмотрите видео:  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

BOM для обеих плат: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Возможности
- Кастомный массив фототранзисторов 32x32 (1024 пикселя).
- Двойная адресация через ADG732 mux для выбора пикселей по строкам/столбцам.
- Автономный режим камеры:
  - OLED UI.
  - Вход затвора.
  - Вывод на SD-карту (`DGO_0000.bmp`, автоинкремент).
- Режим веб-камеры:
  - Прошивка Arduino отвечает на запросы пикселей по serial при `115200`.
  - Приложение Processing рендерит live grayscale-вид 32x32.
- Open hardware исходники:
  - Схемы/PCB в KiCad.
  - Производственные выходные файлы и BOM-артефакты.
  - Печатные механические файлы.

## Структура проекта

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

## Архитектура Hardware/Firmware (текущее состояние)
На основе [`docs/overview.md`](./docs/overview.md):

| Подсистема | Детали |
|---|---|
| Матрица сенсора | `1024 x ALS-PT19` фототранзисторов |
| Адресация | `2 x ADG732` аналоговых mux (строка + столбец) |
| Основная плата | `SAMD21G18A` |
| Питание | Аккумулятор `18650`, зарядка `MCP73831`, стабилизация `3.3 V` |
| Сигнальный тракт | `OUTCOL` подается в high через GPIO MCU; выбранный путь сенсора считывается по `OUTROW` через ADC |
| Поведение захвата | Прошивка камеры выполняет полный скан кадра и записывает 24-битные BMP-файлы |

## Предварительные требования

### Аппаратная часть
- Собранные платы `image_sensor` и `mcu` (или эквивалентная разводка проводами).
- Совместимая с SAMD21 целевая плата с соответствием назначениям пинов в прошивке.
- MicroSD-карта (режим камеры).
- OLED 64x48 SSD1306 (режим камеры, 4-проводный software SPI в текущем скетче).
- Кнопка/переключатель затвора.

### Программное обеспечение
- Arduino IDE (или Arduino CLI) с пакетом плат SAMD21.
- Необходимые библиотеки Arduino для прошивки камеры:
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/runtime для [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/).

## Установка

1. Клонируйте репозиторий:

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. Откройте один скетч прошивки в Arduino IDE:
- Режим камеры: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Режим веб-камеры: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Выберите правильные плату/порт SAMD21 и загрузите прошивку.

4. Для режима веб-камеры откройте скетч Processing:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Использование

### Режим камеры (снимки на SD) 📸
1. Прошейте `digiOBSCURA_camera.ino`.
2. Вставьте SD-карту.
3. При загрузке прошивка проверяет имена файлов и находит следующий свободный `DGO_####.bmp`.
4. Нажмите вход затвора.
5. Устройство захватывает кадр, записывает BMP на SD и показывает миниатюру на OLED.

### Режим веб-камеры (live view с рендерингом на хосте) 🖥️
1. Прошейте `digiOBSCURA_Webcam_Arduino.ino`.
2. Подключитесь по USB serial (`115200`).
3. В скетче Processing укажите правильный serial-порт (сейчас в файле указано `String portName = "COM32"`).
4. Запустите скетч Processing, чтобы опрашивать значения пикселей и отрисовывать кадр 32x32.

## Примечания по конфигурации
Текущие значения по умолчанию на уровне прошивки из исходного кода:

| Настройка | Текущее значение / поведение |
|---|---|
| Разрешение | Фиксированное `32x32` |
| Разрешение ADC | `analogReadResolution(12)` |
| Усреднение в режиме камеры | `avgCount = 10` (повышает качество, снижает скорость кадров) |
| SD CS pin (скетч камеры) | `cardPin = 21` |
| Serial baud (путь веб-камеры) | `115200` |
| I/O mapping | Жестко заданные массивы/переменные (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, и т.д.) |

Если ваша разводка отличается, обновите определения пинов в начале каждого `.ino` файла перед прошивкой.

## Примеры

### Открыть и отредактировать прошивку камеры
```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Открыть и отредактировать Processing viewer для веб-камеры
```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### Открыть исходники PCB в KiCad
- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## Заметки по разработке
- В репозитории нет единой системы сборки (`Makefile`, `platformio.ini`, `package.json` и т.д.).
- Разработка сейчас охватывает Arduino + Processing + KiCad + файлы механического CAD.
- `PCB/image_sensor/README.md` и `PCB/mcu/README.md` выглядят устаревшими/шаблонными и сейчас не являются авторитетной проектной документацией.
- `i18n/` существует и пока не полностью заполнен; переводы планируются и добавляются поэтапно.

## Документация и дорожная карта
Каталог `docs/` содержит заметки по поэтапной технической эволюции:

| Документ | Фокус |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | Временное дифференцирование/события только на прошивке |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | Детектирование событий с помощью компаратора |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | Улучшения аналогового фронтенда/смещения |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | Долгосрочные варианты полного редизайна event-сенсора |

## Устранение неполадок
| Симптом | Проверки |
|---|---|
| SD-карта не определяется в режиме камеры | Проверьте разводку и `cardPin` (`21` в текущем скетче). Переформатируйте SD-карту (FAT/FAT32) и повторите. |
| Пустые/шумные данные изображения | Проверьте разводку `OUTCOL`/`OUTROW` и управляющие пины mux. Проверьте целостность питания и соединения платы сенсора. |
| Processing viewer не показывает данные | Убедитесь, что baud `115200` задан и в Arduino, и в Processing. Измените `portName` с `COM32` на ваш фактический порт. |
| Очень медленный захват | Это ожидаемо при полном скане 32x32 и усреднении (`avgCount=10` по умолчанию). |

## Поддержка / Спонсорство
Из заголовков прошивки:

- Creator: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

Если вы развиваете этот проект, рассмотрите поддержку оригинального автора.

## Участие в проекте
Вклад приветствуется. Рекомендуемые направления:

- Заметки по запуску hardware и проверенные pin map.
- Валидация toolchain/версий (Arduino core, библиотеки, версия Processing).
- Обновления документации, особенно схемы подключения и workflow калибровки.
- Переводы README в `i18n/`, когда набор целевых языков окончательно определен.

Для изменений, затрагивающих поведение hardware или firmware, приложите:
- Подробности тестового стенда.
- Контекст ревизии платы.
- Заметки о поведении до/после.

## Лицензия
- Файл лицензии репозитория: [`LICENSE`](./LICENSE) (текст GPL-3.0 присутствует в репозитории).
- Примечание по заголовкам прошивки: текущие заголовки `.ino` указывают `cc-by-sa-nc-3.0`.

Важное примечание: эти два лицензионных сигнала различаются. Пока maintainers не дадут уточнение, считайте, что для распространения/коммерческого использования требуется подтверждение от владельцев проекта.

## Благодарности
- Sean Hodgins / работа над проектом digiOBSCURA и публичная документация/видео.
- Ресурсы сообщества open hardware и maker, делающие возможными DIY-эксперименты с сенсорами.
