[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://lazying.art)

# digiOBSCURA - حساس صورة وكاميرا رقمية DIY 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#hardware-firmware-architecture-current)
[![Contributions](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#contributing)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> حزمة تصوير DIY مفتوحة بالكامل: PCB حساس مخصص، لوحة MCU، برنامج ثابت، عارض ويب كام، وقطع هيكل قابلة للطباعة.

## 📚 جدول المحتويات

- [نظرة عامة](#overview)
- [عرض](#demo)
- [الميزات](#features)
- [بنية المشروع](#project-structure)
- [معمارية العتاد/البرنامج الثابت (الحالية)](#hardware-firmware-architecture-current)
- [المتطلبات](#prerequisites)
- [التثبيت](#installation)
- [الاستخدام](#usage)
- [ملاحظات الضبط](#configuration-notes)
- [الأمثلة](#examples)
- [ملاحظات التطوير](#development-notes)
- [استكشاف الأخطاء](#troubleshooting)
- [خطة الطريق والملاحظات التقنية](#roadmap-and-technical-notes)
- [المساهمة](#contributing)
- [الدعم](#support)
- [الترخيص](#license)

<a id="overview"></a>
## نظرة عامة 🛰️

`digiOBSCURA` هو نظام تصوير 32x32 منزلي مبني حول مصفوفة فوتوترانزستور مخصصة ومتـحكم SAMD21.

يتضمن هذا المستودع:
- برنامج ثابت لالتقاط الصور إلى بطاقة SD بشكل مستقل.
- برنامج ثابت + Sketch في Processing لعرض مباشر بنمط الكاميرا عبر منفذ تسلسلي.
- ملفات KiCad كاملة لكلا اللوحين (`image_sensor` و`mcu`) بالإضافة إلى ملفات الإنتاج Gerber.
- ملفات ميكانيكية (`.f3d`، `.stl`) لقطع الهيكل/العدسة/الحامل.
- ملاحظات تصميم ووثائق خارطة التحديث المرحلية في [`docs/`](./docs/).

<a id="overview-snapshot"></a>
## لمحة سريعة 📌

| البند | التفاصيل |
| --- | --- |
| حجم المشروع | كاميرا + برنامج ثابت + عارض + تصميم ميكانيكي |
| مصفوفة الحساس | مصفوفة فوتوترانزستور مخصصة `32x32` (`1024`) |
| المتحكم الأساسي | `SAMD21G18A` |
| ناتج الصورة | BMP على SD / مجرى إطارات حي عبر التسلسل |
| سير العمل المستهدف | التقاط مستقل • معاينة بنمط الويب كام |

<a id="demo"></a>
## عرض 🎬

شاهد الفيديو:

[![فيديو digiOBSCURA](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

توجد قائمة BOM لكلا اللوحين هنا: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

<a id="features"></a>
## المزايا ✨

- مصفوفة تصوير فوتوترانزستور مخصصة 32x32 (1024 بكسل).
- العنوان المزدوج باستخدام ADG732 لاختيار البيكسل حسب الصف/العمود.
- سير عمل الكاميرا المستقلة:
  - واجهة حالة OLED.
  - إدخال زر الغالق.
  - مخرج SD (`DGO_####.bmp`، يزيد تلقائيًا).
- سير عمل الويب كام:
  - استجابة برنامج Arduino لطلبات البكسلات التسلسلية على `115200`.
  - يعرض تطبيق Processing عرضًا مباشرًا أحادي اللون بدقة 32x32.
- مصدر مفتوح للأجهزة:
  - مخططات ومجسّمات KiCad.
  - مخرجات الإنتاج وملفات BOM.
  - ملفات ميكانيكية قابلة للطباعة.

<a id="project-structure"></a>
## بنية المشروع 🧱

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

<a id="hardware-firmware-architecture-current"></a>
## معمارية العتاد/البرنامج الثابت (الحالية) 🧰

بناءً على [`docs/overview.md`](./docs/overview.md):

| النطاق الفرعي | التفاصيل |
|---|---|
| مصفوفة الحساس | `1024 x ALS-PT19` فوتوترانزستورات |
| العنوان | `2 x ADG732` مفاتيح تماثلية (صف + عمود) |
| لوحة القلب | `SAMD21G18A` |
| مسار الطاقة | بطارية `18650`، شاحن `MCP73831`، تنظيم `3.3 V` |
| مسار الإشارة | يرفع متحكم MCU إشارة `OUTCOL` إلى HIGH؛ وتُقرأ المسار المختار على `OUTROW` عبر ADC |
| سلوك الالتقاط | يقوم برنامج الكاميرا بإجراء مسوح إطارية كاملة وكتابة ملفات BMP بتنسيق 24-بت |

<a id="prerequisites"></a>
## المتطلبات 🧰

### العتاد

- لوحات `image_sensor` و`mcu` مركبة (أو توصيل مكافئ).
- لوحة SAMD21 متوافقة تتطابق مع توزيع الأطراف المستخدم في البرنامج الثابت.
- بطاقة MicroSD (وضع الكاميرا).
- شاشة OLED `64x48` SSD1306 (وضع الكاميرا، SPI برمجي بأربعة أسلاك في الـ sketch الحالي).
- مدخل زر/مفتاح الغالق.

### البرمجيات

- Arduino IDE (أو Arduino CLI) مع حزمة لوحات SAMD21.
- مكتبات Arduino المطلوبة لبرنامج الكاميرا:
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/runtime لـ [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/).

<a id="installation"></a>
## التثبيت 🧩

1. استنساخ هذا المستودع:

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. افتح أحد Sketches البرنامج الثابت في Arduino IDE:
- وضع الكاميرا: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- وضع الويب كام: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. اختر لوحة SAMD21 والمنفذ الصحيحين، ثم ارفع البرنامج.

4. لوضع الويب كام، افتح sketch في Processing:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

<a id="usage"></a>
## الاستخدام 🚀

### وضع الكاميرا (تدفق لقطات SD) 📸

1. ارفع `digiOBSCURA_camera.ino`.
2. أدخل بطاقة SD مهيأة بنظام FAT/FAT32.
3. فعّل/أعد تشغيل اللوحة؛ يبحث البرنامج الثابت عن اسم ملف `DGO_####.bmp` التالي المتاح.
4. فعّل الالتقاط عبر إدخال الغالق.
5. يلتقط الجهاز الصورة ويكتب BMP على SD ويعرض معاينة مصغّرة على OLED.

### وضع الويب كام (عرض حي معالج بواسطة المضيف) 🖥️

1. ارفع `digiOBSCURA_Webcam_Arduino.ino`.
2. اتصل عبر USB بالتوالي بسرعة `115200`.
3. في sketch Processing، حدّد `String portName` لمنفذك التسلسلي الفعلي (القالب يستخدم `COM32`).
4. شغّل sketch Processing؛ يستعلم من 32x32 بكسل ويعرض إطارًا حيًا بالدرجات الرمادية.

<a id="configuration-notes"></a>
## ملاحظات الضبط 🛠️

القيم الافتراضية الحالية على مستوى البرنامج الثابت من المصدر:

| الإعداد | القيمة/السلوك الحالي |
|---|---|
| الدقة | ثابتة `32x32` |
| دقة ADC | `analogReadResolution(12)` |
| متوسط وضع الكاميرا | `avgCount = 10` (جودة أعلى، سرعة إطار أقل) |
| دبوس SD CS (sketch الكاميرا) | `cardPin = 21` |
| سرعة البود التسلسلي (مسار الويب كام) | `115200` |
| خريطة الإدخال/الإخراج | مصفوفات/متغيرات ثابتة (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`، وغيرها) |

إذا اختلف التوصيل لديك، حدّث تعاريف الأطراف قرب أعلى كل ملف `.ino` قبل الرفع.

<a id="examples"></a>
## الأمثلة 🧪

### فتح وتحرير برنامج الكاميرا

```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### فتح وتحرير عارض الويب كام في Processing

```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### فتح مصادر PCB في KiCad

- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

<a id="development-notes"></a>
## ملاحظات التطوير 📘

- لا يوجد نظام بناء موحّد (`Makefile`، `platformio.ini`، `package.json`، إلخ) في هذا المستودع.
- التطوير حاليًا يمتد عبر Arduino + Processing + KiCad + ملفات CAD ميكانيكية.
- يحتوي `PCB/image_sensor/README.md` و`PCB/mcu/README.md` حاليًا محتوى يشبه القالب/نموذج وليس توثيقًا مرجعيًا.
- يحتوي `i18n/` على نسخ README مترجمة؛ قد تكون غير مكتملة في بعض المواضع وقد تتأخر عن المحتوى الإنجليزي.

<a id="troubleshooting"></a>
## استكشاف الأخطاء 🧯

| العرض | الفحوص |
|---|---|
| بطاقة SD غير مكتشفة في وضع الكاميرا | تأكد من التوصيلات و`cardPin` (`21` في sketch الحالي). أعد تهيئة بطاقة SD بصيغة FAT/FAT32 وأعد المحاولة. |
| بيانات الصورة فارغة/مشوشة | تحقق من توصيلات `OUTCOL`/`OUTROW` وأطراف تحكم الـ mux. افحص سلامة التغذية واتصالات لوحة الحساس. |
| عارض Processing لا يظهر أي بيانات | تأكد من سرعة البود `115200` في Arduino وProcessing. حدّث `portName` من `COM32` إلى المنفذ الفعلي. |
| الالتقاط بطيء جدًا | هذا متوقع مع مسح كامل 32x32 واستخدام المتوسط (`avgCount=10`) افتراضيًا. |

<a id="roadmap-and-technical-notes"></a>
## خطة الطريق والملاحظات التقنية 🗺️

يدعم مجلد `docs/` المسارات التقنية المخطط لها والاستكشافية:

| المستند | التركيز |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | الفروقات الزمنية / الأحداث في البرنامج الثابت فقط |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | اكتشاف الأحداث بمساعدة المقارن |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | تحسينات الواجهة التناظرية والـ bias |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | خيارات إعادة تصميم مستشعر حدث طويل الأجل |

<a id="contributing"></a>
## المساهمة 🤝

المساهمات مرحّبة. مجالات المساهمة المقترحة:

- ملاحظات إقلاع العتاد وخرائط التوصيل المؤكدة.
- التحقق من سلسلة الأدوات/الإصدارات (نواة Arduino، المكتبات، إصدار Processing).
- تحديثات التوثيق، خاصةً مخططات التوصيل وسير عمل المعايرة.
- ترجمة `i18n/README` عند اعتماد لغات الهدف.

أما التغييرات التي تمس العتاد أو سلوك البرنامج الثابت، فقم بإضافة:
- تفاصيل إعداد الاختبار.
- سياق نسخة اللوحة.
- ملاحظات السلوك قبل/بعد.

<a id="support"></a>
## الترخيص

- `LICENSE` في هذا المستودع يشير حاليًا إلى توزيع المشروع تحت GPL-3.0 على مستوى المستودع.
- تعليقات ترويسات البرنامج الثابت أيضًا تشير إلى `CC BY-NC-SA 3.0` لبعض محتوى ترويسات المصدر.

عند إعادة توزيع الثنائيات أو القطع العتادية أو المشتقات، تحقق من شروط الترخيص الدقيقة في الملفات التي تُعدّلها وتأكد من توافقها مع الاستخدام المقصود.


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
