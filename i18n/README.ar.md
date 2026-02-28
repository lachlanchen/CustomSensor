[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# digiOBSCURA - مستشعر صور DIY وكاميرا رقمية 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#نظرة-عامة)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#الميزات)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#بنية-العتادالبرمجيات-الحالية)

> حزمة تصوير DIY مفتوحة بالكامل: لوحة مستشعر مخصصة، لوحة MCU، برمجيات ثابتة، عارض ويب كام، وأجزاء غلاف قابلة للطباعة.

## نظرة عامة
`digiOBSCURA` هو نظام تصوير DIY بدقة 32x32 مبني حول مصفوفة فوتوترانزستور مخصصة ومتحكم يعتمد على SAMD21.

يحتوي هذا المستودع على:
- برمجية ثابتة لالتقاط الصور ككاميرا مستقلة وحفظها على بطاقة SD.
- برمجية ثابتة + مخطط Processing لعرض مباشر بأسلوب webcam عبر المنفذ التسلسلي.
- ملفات المصدر الكاملة لـ KiCad لكلتا اللوحتين (`image_sensor` و`mcu`) بالإضافة إلى ملفات Gerber الخاصة بالإنتاج.
- أصول ميكانيكية (`.f3d`, `.stl`) لمكونات الغلاف/العدسة/الحامل الثلاثي.
- ملاحظات تصميم ووثائق خارطة طريق للترقيات المرحلية داخل [`docs/`](./docs/).

### لمحة سريعة
| المجال | التنفيذ الحالي |
|---|---|
| مصفوفة التصوير | مصفوفة فوتوترانزستور مخصصة `32x32` (`1024` بكسل) |
| التبديل المتعدد (Multiplexing) | `2 x ADG732` (اختيار الصف + العمود) |
| المتحكم | `SAMD21G18A` |
| خرج الكاميرا | ملفات BMP على بطاقة SD (`DGO_0000.bmp` مع زيادة تلقائية) |
| الوضع المباشر | منفذ تسلسلي (`115200`) + عارض Rendering على المضيف باستخدام Processing |
| أصول التصميم | KiCad + Gerbers + عناصر BOM + ملفات `F3D`/`STL` |

## العرض التوضيحي (المحتوى الأساسي الحالي) 🎬
شاهد الفيديو:
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

يمكنك العثور على BOM لكلتا اللوحتين هنا: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## الميزات
- مصفوفة تصوير فوتوترانزستور مخصصة 32x32 (بعدد 1024 بكسل).
- عنونة صف/عمود عبر ADG732 مزدوج.
- سير عمل الكاميرا المستقلة:
  - واجهة OLED.
  - إدخال زر الغالق (Shutter).
  - خرج بطاقة SD (`DGO_0000.bmp` مع زيادة تلقائية).
- سير عمل webcam:
  - برمجية Arduino تستجيب لطلبات البكسلات عبر المنفذ التسلسلي بسرعة `115200`.
  - تطبيق Processing يعرض مشهدًا حيًا بتدرج رمادي 32x32.
- عتاد مفتوح المصدر:
  - مخططات ولوحات KiCad.
  - مخرجات الإنتاج وعناصر BOM.
  - ملفات ميكانيكية قابلة للطباعة.

## هيكل المشروع

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

## بنية العتاد/البرمجيات (الحالية)
استنادًا إلى [`docs/overview.md`](./docs/overview.md):

| النظام الفرعي | التفاصيل |
|---|---|
| مصفوفة المستشعر | `1024 x ALS-PT19` فوتوترانزستور |
| العنونة | `2 x ADG732` مبدلات تناظرية (صف + عمود) |
| اللوحة الأساسية | `SAMD21G18A` |
| مسار الطاقة | بطارية `18650`، شاحن `MCP73831`، تنظيم `3.3 V` |
| مسار الإشارة | يتم دفع `OUTCOL` إلى الحالة العالية بواسطة GPIO في MCU؛ وتُقرأ إشارة المستشعر المحدد على `OUTROW` عبر ADC |
| سلوك الالتقاط | برمجية الكاميرا تجري مسحًا كاملًا للإطار وتكتب ملفات BMP بعمق 24-بت |

## المتطلبات المسبقة

### العتاد
- لوحات `image_sensor` و`mcu` مجمّعة (أو توصيل مكافئ).
- لوحة هدف متوافقة مع SAMD21 وتطابق تعيينات الأرجل المستخدمة في البرمجية.
- بطاقة MicroSD (وضع الكاميرا).
- شاشة OLED نوع SSD1306 بمقاس 64x48 (وضع الكاميرا، باستخدام software SPI بأسلاك 4 في المخطط الحالي).
- زر/مفتاح إدخال للغالق.

### البرمجيات
- Arduino IDE (أو Arduino CLI) مع حزمة لوحات SAMD21.
- مكتبات Arduino المطلوبة لبرمجية الكاميرا:
  - `U8g2`
  - `SPI`
  - `SD`
- بيئة Processing IDE/runtime للمجلد [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/).

## التثبيت

1. استنسخ هذا المستودع:

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. افتح أحد ملفات البرمجية الثابتة في Arduino IDE:
- وضع الكاميرا: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- وضع webcam: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. اختر لوحة/منفذ SAMD21 الصحيح ثم ارفع البرنامج.

4. لوضع webcam، افتح مخطط Processing:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## الاستخدام

### وضع الكاميرا (سير عمل لقطة SD) 📸
1. ارفع `digiOBSCURA_camera.ino`.
2. أدخل بطاقة SD.
3. عند الإقلاع، تتحقق البرمجية من الأسماء وتبحث عن أول اسم متاح بالصيغة `DGO_####.bmp`.
4. اضغط إدخال الغالق.
5. يلتقط الجهاز الصورة، يكتب ملف BMP على SD، ويعرض معاينة مصغرة على OLED.

### وضع webcam (عرض مباشر مع Rendering على المضيف) 🖥️
1. ارفع `digiOBSCURA_Webcam_Arduino.ino`.
2. اتصل عبر USB serial (`115200`).
3. في مخطط Processing، اضبط المنفذ التسلسلي الصحيح (الملف يحتوي حاليًا على `String portName = "COM32"`).
4. شغّل مخطط Processing لاستطلاع قيم البكسلات ورسم إطار 32x32.

## ملاحظات التهيئة
القيم الافتراضية الحالية على مستوى البرمجية من المصدر:

| الإعداد | القيمة/السلوك الحالي |
|---|---|
| الدقة | ثابتة عند `32x32` |
| دقة ADC | `analogReadResolution(12)` |
| المتوسط في وضع الكاميرا | `avgCount = 10` (يزيد الجودة ويخفض سرعة الإطار) |
| رجل SD CS (في مخطط الكاميرا) | `cardPin = 21` |
| سرعة المنفذ التسلسلي (مسار webcam) | `115200` |
| تعيينات I/O | مصفوفات/متغيرات ثابتة في الشيفرة (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, وغيرها) |

إذا كانت توصيلات العتاد لديك مختلفة، حدّث تعريفات الأرجل قرب أعلى كل ملف `.ino` قبل الرفع.

## أمثلة

### فتح وتعديل برمجية الكاميرا
```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### فتح وتعديل عارض webcam في Processing
```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### فتح مصادر PCB في KiCad
- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## ملاحظات التطوير
- لا يوجد نظام بناء موحد (`Makefile`, `platformio.ini`, `package.json`, إلخ) في هذا المستودع.
- التطوير حاليًا موزع بين Arduino + Processing + KiCad + ملفات CAD الميكانيكية.
- الملفان `PCB/image_sensor/README.md` و`PCB/mcu/README.md` يبدوان قديمين/شبيهان بالقوالب وليسا حاليًا توثيقًا مرجعيًا معتمدًا للمشروع.
- الدليل `i18n/` موجود، والترجمات كانت مخططة سابقًا لكنها لم تكن مُعتمدة بعد.

## التوثيق وخارطة الطريق
يحتوي مجلد `docs/` على ملاحظات تطور تقني مرحلية:

| المستند | التركيز |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | التفريق الزمني/الأحداث على مستوى البرمجية فقط |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | كشف أحداث بمساعدة Comparator |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | تحسينات الواجهة التناظرية الأمامية/الانحياز |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | خيارات إعادة تصميم مستشعر أحداث على المدى الأبعد |

## استكشاف الأخطاء وإصلاحها
| العَرَض | التحققات |
|---|---|
| عدم اكتشاف بطاقة SD في وضع الكاميرا | تأكد من التوصيلات ومن `cardPin` (القيمة `21` في المخطط الحالي). أعد تهيئة البطاقة (FAT/FAT32) وحاول مجددًا. |
| بيانات صورة فارغة/مشوشة | تحقق من توصيلات `OUTCOL`/`OUTROW` وأرجل التحكم بالمبدلات. افحص استقرار الطاقة واتصالات لوحة المستشعر. |
| عارض Processing لا يعرض بيانات | تأكد من `115200` في كل من Arduino وProcessing. حدّث `portName` من `COM32` إلى المنفذ الفعلي لديك. |
| التقاط بطيء جدًا | هذا متوقع مع المسح الكامل 32x32 والمتوسط (`avgCount=10` افتراضيًا). |

## الدعم / الرعاية
من ترويسات البرمجية الثابتة:

- المنشئ: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

إذا قمت بالبناء على هذا المشروع، ففكّر في دعم المنشئ الأصلي.

## المساهمة
المساهمات مرحّب بها. مجالات المساهمة المقترحة:

- ملاحظات تفعيل العتاد وخرائط الأرجل المتحقق منها.
- التحقق من أداة التطوير/الإصدارات (Arduino core، المكتبات، إصدار Processing).
- تحديثات التوثيق، خصوصًا مخططات التوصيل وسير عمل المعايرة.
- ترجمات README داخل `i18n/` بعد اعتماد اللغات المستهدفة.

بالنسبة للتغييرات التي تمس سلوك العتاد أو البرمجية، أرفق:
- تفاصيل إعداد الاختبار.
- سياق مراجعة اللوحة.
- ملاحظات السلوك قبل/بعد.

## الترخيص
- ملف ترخيص المستودع: [`LICENSE`](./LICENSE) (نص GPL-3.0 موجود في المستودع).
- ملاحظة ترويسة البرمجية الثابتة: ترويسات ملفات `.ino` الحالية تشير إلى `cc-by-sa-nc-3.0`.

ملاحظة مهمة: هاتان الإشارتان للترخيص مختلفتان. إلى أن يوضح المشرفون ذلك، تعامل مع الترخيص على أنه يتطلب تأكيدًا من مالك المشروع لإعادة التوزيع/الاستخدام التجاري.

## الشكر والتقدير
- Sean Hodgins / عمل مشروع digiOBSCURA وتوثيقه/فيديوهاته العامة.
- موارد مجتمع العتاد المفتوح وصناع المشاريع التي تجعل تجارب مستشعرات DIY ممكنة.
