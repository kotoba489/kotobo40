# ⌨️ kgrid40

40%（4行×10列）の一体型Gridレイアウト向け、ZMK Firmwareベースの自作キーボード用ファームウェアです。SuperMini nRF52840（nice!nano互換）を搭載し、Bluetooth・USB・ZMK Studioに対応しています。

> 2026-03-16時点で、マトリクス配線、Bluetooth接続、レイヤー切替を実機で確認済みです。

## 概要

| 項目 | 内容 |
| --- | --- |
| 配列 | 4行 × 10列、40キーのGridレイアウト |
| ファームウェア | [ZMK Firmware](https://zmk.dev/)（stable） |
| MCU | SuperMini nRF52840（nice!nano互換、board: `nice_nano`） |
| ダイオード方向 | COL2ROW |
| リポジトリ | [kotoba489/kotobo40](https://github.com/kotoba489/kotobo40) |

## プロジェクトの経緯

キーマップエディタを使ってオーソリニア配列のキーボードを試したことをきっかけに、40%サイズのGridレイアウトを自作しました。複数のキーボードを使い分けるのではなく、1台を育てながら自分に合う配列とファームウェアを継続的に調整する方針です。

## ハードウェア仕様

### マトリクス配線

- 4行 × 10列（40キー）
- 行はアクティブHigh、プルダウン
- ダイオード方向はCOL2ROW

| 種別 | 基板の穴番号 | GPIO |
| --- | --- | --- |
| Row 0 | 5 | P0.17 |
| Row 1 | 6 | P0.20 |
| Row 2 | 7 | P0.22 |
| Row 3 | 8 | P0.24 |
| Col 0 | 9 | P1.00 |
| Col 1 | 10 | P0.11 |
| Col 2 | 11 | P1.04 |
| Col 3 | 12 | P1.06 |
| Col 4 | 17 | P0.29 |
| Col 5 | 18 | P0.02 |
| Col 6 | 19 | P1.15 |
| Col 7 | 20 | P1.13 |
| Col 8 | 21 | P1.11 |
| Col 9 | 22 | P0.10 |

## ファームウェア構成

主要な設定は `config/` 以下にまとめています。

```text
kotobo40/
├── .github/workflows/build.yml
├── config/
│   ├── west.yml
│   ├── kgrid40.json
│   ├── kgrid40.keymap
│   ├── kgrid40.conf
│   ├── settings_reset.conf
│   └── boards/shields/kgrid40/
│       ├── Kconfig.defconfig
│       ├── Kconfig.shield
│       ├── kgrid40.conf
│       ├── kgrid40.dtsi
│       └── kgrid40.overlay
├── build.yaml
├── LICENSE
└── README.md
```

- `config/kgrid40.keymap`：Dvorakベースのキーマップ
- `config/kgrid40.conf`：Bluetooth、USB、バッテリー、Studioなどの共通設定
- `config/boards/shields/kgrid40/`：kgrid40固有のマトリクスおよびシールド定義
- `build.yaml`：通常書き込み用（`kgrid40.uf2`）と設定初期化用（`settings_reset.uf2`）を並行ビルド

## Bluetooth・USB設定

Bluetooth、USB、有線／無線の両利用、バッテリー残量表示を有効にしています。スリープは開発・調整しやすいよう無効化しています。

```conf
CONFIG_ZMK_KEYBOARD_NAME="kgrid40"
CONFIG_BT_CTLR_TX_PWR_PLUS_8=y
CONFIG_ZMK_BLE=y
CONFIG_ZMK_USB=y
CONFIG_ZMK_BATTERY_REPORTING=y
CONFIG_BT_BAS=y
CONFIG_ZMK_STUDIO=y
CONFIG_ZMK_STUDIO_LOCKING=n
CONFIG_ZMK_SLEEP=n
```

Bluetoothの出力強度は +8 dBm に設定しています。Bluetoothで接続したままUSBケーブルを挿すと、USBモードを明示的に選ぶまでUSB入力が有効にならない場合があります。必要に応じてキーマップ上の `OUT_USB` / `OUT_BLE` を使って出力先を切り替えてください。

## キーマップ編集とZMK Studio

- キーマップは [ZMK Keymap Editor](https://nickcoutsos.github.io/keymap-editor/) で編集できます。
- ブラウザ上で変更を反映した後、GitHub Actionsのビルド完了を待ち、生成されたUF2をダウンロードします。
- `CONFIG_ZMK_STUDIO=y` を有効化しているため、対応環境ではZMK Studioでの確認・調整も可能です。

## ビルドと書き込み

1. `config/kgrid40.keymap` や設定ファイルを変更して、mainブランチにコミット・プッシュします。
2. GitHub Actionsのビルドが完了したら、Artifactsから `kgrid40.uf2` を取得します。
3. 基板のリセットボタンを素早く2回押し、USBストレージとして認識させます。
4. `kgrid40.uf2` をそのドライブへコピーすると、ファームウェアが書き込まれます。

Bluetoothのペアリング情報を消して再登録したいときは、同様の手順で `settings_reset.uf2` を書き込んでから通常ファームウェアを書き込みます。書き込む前に、接続先のPCやスマートフォン側で古いペアリングを削除してください。

## 動作状況

- ✅ マトリクス配線・全キー入力
- ✅ Bluetooth接続
- ✅ USB接続
- ✅ レイヤー切替
- ✅ ZMK Studio有効化
- ✅ 通常ファームウェア／設定初期化用UF2の並行ビルド

## 変更履歴

- 2026-03-16：実機でマトリクス、Bluetooth、レイヤー切替を確認
- 2026-03-16：READMEを構成・Bluetooth設定・書き込み手順を含む形に整理

## ライセンス

[MIT License](LICENSE)
