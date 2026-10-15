[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner — apps and four buddies](https://lazying.art/media/brand/lazyingart-intro.gif)](https://lazying.art)

# digiOBSCURA - DIY画像センサーとデジタルカメラ 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#hardware-firmware-architecture-current)
[![Contributions](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#contributing)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> 完全にオープンなDIYイメージングスタック：カスタムセンサ基板、MCUボード、ファームウェア、ウェブカム風ビューア、印刷可能な筐体部品で構成されています。

## 📚 目次

- [概要](#概要)
- [デモ](#デモ)
- [特徴](#特徴)
- [プロジェクト構成](#プロジェクト構成)
- [ハードウェア/ファームウェア構成（現行）](#ハードウェアファームウェア構成現行)
- [前提条件](#前提条件)
- [インストール](#インストール)
- [使い方](#使い方)
- [設定ノート](#設定ノート)
- [例](#例)
- [開発ノート](#開発ノート)
- [トラブルシューティング](#トラブルシューティング)
- [ロードマップと技術ノート](#ロードマップと技術ノート)
- [コントリビュート](#コントリビュート)
- [サポート](#%E2%9D%A4%EF%B8%8F-support)
- [ライセンス](#ライセンス)

## 概要 🛰️

`digiOBSCURA` は、カスタムフォトトランジスタアレイとSAMD21ベースのコントローラーを中心に構成されたDIY 32x32イメージングシステムです。

このリポジトリには以下が含まれます。
- SDカードへの単体撮影向けファームウェア。
- シリアル経由でライブ表示するためのファームウェア + Processingスケッチ。
- 両ボード（`image_sensor` と `mcu`）のKiCad完全ソースと製造用ガーバーデータ。
- エンクロージャ、レンズ、三脚部品の機械設計データ（`.f3d`, `.stl`）。
- `docs/` にある設計ノートおよび段階的アップグレードのロードマップ。

## クイックスナップショット 📌

| 項目 | 詳細 |
| --- | --- |
| プロジェクト構成 | カメラ + ファームウェア + ビューア + 機械設計 |
| センサーアレイ | `32x32`（`1024`）のカスタムフォトトランジスタマトリクス |
| 中核コントローラー | `SAMD21G18A` |
| 画像出力 | SDカードBMP / シリアルライブフレーム |
| 対象ワークフロー | 単体撮影 • ウェブカム風プレビュー |

## デモ 🎬

動画をチェック：

[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

両方の基板のBOMはここにあります: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## 特徴 ✨

- 32x32（1024ピクセル）のカスタムフォトトランジスタイメージングアレイ。
- 行・列ピクセル選択のための2つのADG732マルチプレクサアドレッシング。
- 単体カメラワークフロー:
  - OLEDステータスUI。
  - シャッター入力。
  - SDカード出力（`DGO_####.bmp`、自動インクリメント）。
- ウェブカムワークフロー:
  - Arduinoファームウェアが `115200` のシリアルピクセル要求に応答。
  - Processingアプリが32x32グレースケールライブビューをレンダリング。
- オープンハードウェアソース:
  - KiCadの回路図/PCB。
  - 製造成果物とBOMアーティファクト。
  - 印刷可能な機械設計データ。

## プロジェクト構成 🧱

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

## ハードウェア/ファームウェア構成（現行） 🧰

[`docs/overview.md`](./docs/overview.md) を基にしています。

| サブシステム | 詳細 |
|---|---|
| センサーマトリクス | `1024 x ALS-PT19` フォトトランジスタ |
| アドレッシング | `2 x ADG732` アナログマルチプレクサ（行 + 列） |
| 中核ボード | `SAMD21G18A` |
| 電源系統 | `18650` バッテリー、`MCP73831` チャージャ、`3.3 V` レギュレーション |
| 信号系統 | `OUTCOL` はMCU GPIOでHIGH駆動され、選択されたセンサーパスは `OUTROW` でADC経由で読み取られます |
| キャプチャ動作 | カメラファームウェアが全フレームを走査し、24bit BMPファイルを書き込みます |

## 前提条件 🧰

### ハードウェア

- 組み立て済みの `image_sensor` と `mcu` ボード（または同等の配線）。
- ファームウェアで想定されるピン配置に一致するSAMD21互換ターゲットボード。
- MicroSDカード（カメラモード）。
- `64x48` SSD1306 OLED（カメラモード、現在のスケッチでは4線ソフトSPI）。
- シャッターボタン/スイッチ入力。

### ソフトウェア

- Arduino IDE（またはArduino CLI）とSAMD21ボードパッケージ。
- カメラファームウェアに必要なArduinoライブラリ：
  - `U8g2`
  - `SPI`
  - `SD`
- Processing IDE/ランタイム（[`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/)）。

## インストール 🧩

1. このリポジトリをクローンします。

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. Arduino IDEで1つのファームウェアスケッチを開きます。
- カメラモード: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- ウェブカムモード: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. 正しいSAMD21ボードとポートを選択し、アップロードします。

4. ウェブカムモードではProcessingスケッチを開きます。
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## 使い方 🚀

### カメラモード（SDスナップショット） 📸

1. `digiOBSCURA_camera.ino` を書き込みます。
2. FAT/FAT32でフォーマットしたSDカードを挿入します。
3. 電源投入/リセットします。ファームウェアが次に使える `DGO_####.bmp` ファイル名を探します。
4. シャッター入力で撮影をトリガーします。
5. デバイスがキャプチャしてBMPをSDへ書き込み、OLEDでサムネイルを表示します。

### ウェブカムモード（ホスト描画ライブビュー） 🖥️

1. `digiOBSCURA_Webcam_Arduino.ino` を書き込みます。
2. USBシリアルを `115200` で接続します。
3. Processingスケッチで `String portName` を実際のシリアルポートに設定します（テンプレートは `COM32` を使用）。
4. Processingスケッチを実行します。`32x32` ピクセルをポーリングして、グレースケールのライブフレームをレンダリングします。

## 設定ノート 🛠️

現在のファームウェア既定値は次の通りです。

| 設定 | 現在の値 / 挙動 |
|---|---|
| 解像度 | 固定 `32x32` |
| ADC分解能 | `analogReadResolution(12)` |
| カメラモード平均化 | `avgCount = 10`（品質重視、フレーム速度低下） |
| SD CSピン（カメラスケッチ） | `cardPin = 21` |
| シリアルボーレート（ウェブカム経路） | `115200` |
| I/Oマッピング | 固定配列/変数（`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW` など） |

配線が異なる場合は、各 `.ino` ファイル先頭付近のピン定義を更新してから書き込みを行ってください。

## 例 🧪

### カメラファームウェアを開いて編集する

```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Processingウェブカムビューアを開いて編集する

```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### KiCadでPCBソースを開く

- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## 開発ノート 📘

- このリポジトリには統合ビルドシステム（`Makefile`, `platformio.ini`, `package.json` など）はありません。
- 開発は現在、Arduino + Processing + KiCad + 機械CADファイルが中心です。
- `PCB/image_sensor/README.md` と `PCB/mcu/README.md` は現在、プレースホルダー/テンプレートのような内容で、権威ある情報源ではありません。
- `i18n/` にはREADMEの翻訳版が入っていますが、未完成な箇所があり、英語版より遅れがある場合があります。

## トラブルシューティング 🧯

| 症状 | チェック |
|---|---|
| カメラモードでSDカードが認識されない | 配線と `cardPin`（現在のスケッチでは `21`）を確認してください。SDカードを再フォーマット（FAT/FAT32）して再試行します。 |
| 画像データが空／ノイズが多い | `OUTCOL` / `OUTROW` 配線とマルチプレクサ制御ピンを確認します。電源インテグリティとセンサーボード接続をチェックします。 |
| Processingビューアにデータが表示されない | ArduinoとProcessingの両方でシリアルボーレート `115200` を確認します。`portName` を `COM32` から実際のポートに更新します。 |
| キャプチャが非常に遅い | 32x32フルスキャンと平均化（デフォルトの `avgCount=10`）の結果として予想されます。 |

## ロードマップと技術ノート 🗺️

[`docs/`](./docs/) ディレクトリには、計画中および検討中の技術方針が記録されています。

| ドキュメント | 焦点 |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | ファームウェアのみの時間差分 / イベント |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | コンパレータ支援のイベント検出 |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | アナログフロントエンドとバイアス改善 |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | 長期的なイベントセンサー再設計の選択肢 |

## コントリビュート 🤝

コントリビューションは歓迎します。推奨分野:

- ハードウェア立ち上げノートと検証済みのピンマップ。
- ツールチェーン/バージョン検証（Arduinoコア、ライブラリ、Processingのバージョン）。
- 文書更新（特に配線図とキャリブレーションのワークフロー）。

- 言語対象が確定した後の `i18n/` README 翻訳。

ハードウェアまたはファームウェア挙動に変更がある場合は、以下を含めます。
- テスト構成の詳細。
- ボードリビジョンのコンテキスト。
- 変更前後の挙動メモ。

## ライセンス

- このリポジトリの `LICENSE` は、リポジトリ全体として現時点ではGPL-3.0で配布されていることを示しています。
- ファームウェアヘッダコメントは、いくつかのソースヘッダーで `CC BY-NC-SA 3.0` を参照しています。

バイナリ、ハードウェア成果物、派生物を再配布する場合は、対象ファイルの正確なライセンス条件を確認し、想定用途と互換性があることを確認してください。


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
