[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# digiOBSCURA - DIY イメージセンサー & デジタルカメラ 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#概要)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#機能)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#ハードウェアファームウェア-アーキテクチャ現行)

> 完全にオープンな DIY イメージングスタック: カスタムセンサー PCB、MCU ボード、ファームウェア、ウェブカムビューア、3D プリント可能な筐体パーツ。

## 概要
`digiOBSCURA` は、カスタムのフォトトランジスタアレイと SAMD21 ベースのコントローラで構成された DIY 32x32 イメージングシステムです。

このリポジトリには以下が含まれます:
- SD カードへの単体カメラ撮影用ファームウェア。
- シリアル経由のウェブカム風ライブ表示用ファームウェア + Processing スケッチ。
- 2 つのボード（`image_sensor` と `mcu`）向け KiCad 完全ソースと製造用 Gerber。
- 筐体・レンズ・三脚コンポーネント用の機構アセット（`.f3d`, `.stl`）。
- [`docs/`](./docs/) にある設計ノートと段階的アップグレードのロードマップ文書。

### 早見表
| 領域 | 現在の実装 |
|---|---|
| イメージングアレイ | `32x32` カスタムフォトトランジスタマトリクス（`1024` ピクセル） |
| 多重化 | `2 x ADG732`（行 + 列選択） |
| コントローラ | `SAMD21G18A` |
| カメラ出力 | SD カード BMP ファイル（`DGO_0000.bmp`、自動インクリメント） |
| ライブモード | シリアル（`115200`）+ Processing ホストレンダラ |
| 設計アセット | KiCad + Gerber + BOM 成果物 + `F3D`/`STL` ファイル |

## デモ（既存の正準コンテンツ） 🎬
動画はこちら:  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

両ボードの BOM: https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## 機能
- 32x32（1024 ピクセル）カスタムフォトトランジスタ・イメージングアレイ。
- 行/列ピクセル選択向けデュアル ADG732 MUX アドレッシング。
- 単体カメラワークフロー:
  - OLED UI。
  - シャッター入力。
  - SD カード出力（`DGO_0000.bmp`、自動インクリメント）。
- ウェブカムワークフロー:
  - Arduino ファームウェアが `115200` でシリアルピクセル要求に応答。
  - Processing アプリが 32x32 グレースケールのライブビューを描画。
- オープンハードウェアソース:
  - KiCad 回路図/PCB。
  - 製造出力と BOM 成果物。
  - 印刷可能な機構ファイル。

## プロジェクト構成

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

## ハードウェア/ファームウェア アーキテクチャ（現行）
[`docs/overview.md`](./docs/overview.md) に基づく:

| サブシステム | 詳細 |
|---|---|
| センサーマトリクス | `1024 x ALS-PT19` フォトトランジスタ |
| アドレッシング | `2 x ADG732` アナログ MUX（行 + 列） |
| コアボード | `SAMD21G18A` |
| 電源系 | `18650` バッテリー、`MCP73831` 充電器、`3.3 V` レギュレーション |
| 信号経路 | `OUTCOL` は MCU GPIO により High 駆動。選択されたセンサーパスを `OUTROW` で ADC 読み取り |
| 撮影動作 | カメラファームウェアはフルフレーム走査を行い、24-bit BMP を書き込み |

## 前提条件

### ハードウェア
- 組み立て済みの `image_sensor` および `mcu` ボード（または同等配線）。
- ファームウェアで使用されるピン割り当てに一致する SAMD21 互換ターゲットボード。
- MicroSD カード（カメラモード）。
- 64x48 SSD1306 OLED（カメラモード、現行スケッチでは 4 線式ソフトウェア SPI）。
- シャッターボタン/スイッチ入力。

### ソフトウェア
- SAMD21 ボードパッケージを導入した Arduino IDE（または Arduino CLI）。
- カメラファームウェアに必要な Arduino ライブラリ:
  - `U8g2`
  - `SPI`
  - `SD`
- [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/) 用の Processing IDE/runtime。

## インストール

1. このリポジトリをクローン:

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. Arduino IDE でいずれかのファームウェアスケッチを開く:
- カメラモード: [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- ウェブカムモード: [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. 正しい SAMD21 ボード/ポートを選択して書き込み。

4. ウェブカムモードでは Processing スケッチを開く:
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## 使い方

### カメラモード（SD スナップショットワークフロー） 📸
1. `digiOBSCURA_camera.ino` を書き込む。
2. SD カードを挿入。
3. 起動時にファームウェアがファイル名を確認し、次に利用可能な `DGO_####.bmp` を探す。
4. シャッター入力を押す。
5. デバイスが撮影し、BMP を SD に保存して OLED にサムネイルプレビューを表示。

### ウェブカムモード（ホストレンダリングのライブビュー） 🖥️
1. `digiOBSCURA_Webcam_Arduino.ino` を書き込む。
2. USB シリアル（`115200`）で接続。
3. Processing スケッチで正しいシリアルポートを設定（現在のファイルでは `String portName = "COM32"`）。
4. Processing スケッチを実行し、ピクセル値をポーリングして 32x32 フレームを描画。

## 設定メモ
ソースコード上の現行ファームウェア既定値:

| 設定 | 現在の値 / 動作 |
|---|---|
| 解像度 | 固定 `32x32` |
| ADC 分解能 | `analogReadResolution(12)` |
| カメラモード平均化 | `avgCount = 10`（画質向上、フレーム速度低下） |
| SD CS ピン（カメラスケッチ） | `cardPin = 21` |
| シリアルボーレート（ウェブカム経路） | `115200` |
| I/O マッピング | ハードコードされた配列/変数（`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW` など） |

ハードウェア配線が異なる場合は、書き込み前に各 `.ino` ファイル冒頭付近のピン定義を更新してください。

## 例

### カメラファームウェアを開いて編集
```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Processing のウェブカムビューアを開いて編集
```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### KiCad で PCB ソースを開く
- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## 開発ノート
- このリポジトリには統一ビルドシステム（`Makefile`, `platformio.ini`, `package.json` など）はありません。
- 現在の開発は Arduino + Processing + KiCad + 機械 CAD ファイルにまたがっています。
- `PCB/image_sensor/README.md` と `PCB/mcu/README.md` は古い/テンプレート的な内容に見え、現時点では一次情報ドキュメントではありません。
- `i18n/` は存在しますが現在は空で、翻訳は計画中で未コミットです。

## ドキュメントとロードマップ
`docs/` ディレクトリには段階的な技術進化メモが含まれます:

| ドキュメント | 焦点 |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | ファームウェアのみの時間差分/イベント |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | コンパレータ支援イベント検出 |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | アナログ・フロントエンド/バイアス改善 |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | 長期的なイベントセンサー再設計オプション |

## トラブルシューティング
| 症状 | 確認事項 |
|---|---|
| カメラモードで SD カードが検出されない | 配線と `cardPin`（現行スケッチでは `21`）を確認。SD カードを（FAT/FAT32 で）再フォーマットして再試行。 |
| 画像データが空白/ノイズだらけ | `OUTCOL`/`OUTROW` 配線と MUX 制御ピンを確認。電源品質とセンサーボード接続を確認。 |
| Processing ビューアにデータが表示されない | Arduino 側・Processing 側ともにシリアルボーレート `115200` を確認。`portName` を `COM32` から実際のポートへ更新。 |
| 撮影が非常に遅い | フル 32x32 走査と平均化（既定 `avgCount=10`）のため想定内。 |

## サポート / スポンサー
ファームウェアヘッダーより:

- Creator: Sean Hodgins
- YouTube: http://youtube.com/seanhodginsfilms
- Patreon: https://patreon.com/seanhodgins

このプロジェクトをベースに製作する場合は、原作者への支援を検討してください。

## コントリビュート
コントリビューション歓迎です。推奨分野:

- ハードウェア立ち上げメモと検証済みピンマップ。
- ツールチェーン/バージョン検証（Arduino core、ライブラリ、Processing バージョン）。
- ドキュメント更新（特に配線図とキャリブレーション手順）。
- 言語ターゲット確定後の `i18n/` README 翻訳。

ハードウェアまたはファームウェア動作に関わる変更には、以下を含めてください:
- テストセットアップ詳細。
- ボードリビジョン情報。
- 変更前/変更後の挙動メモ。

## ライセンス
- リポジトリのライセンスファイル: [`LICENSE`](./LICENSE)（リポジトリ内に GPL-3.0 テキストあり）。
- ファームウェアヘッダーノート: 現在の `.ino` ヘッダーは `cc-by-sa-nc-3.0` を記載。

重要: この 2 つのライセンス表示は異なります。メンテナにより明確化されるまで、再配布/商用利用にはプロジェクトオーナー確認が必要なライセンスとして扱ってください。

## 謝辞
- Sean Hodgins / digiOBSCURA プロジェクトの成果物、および公開ドキュメント/動画。
- DIY センサー実験を可能にするオープンハードウェアとメイカーコミュニティの各種リソース。
