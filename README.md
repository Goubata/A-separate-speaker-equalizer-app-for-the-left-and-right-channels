# Earpiece EQ

**English** | [日本語](#日本語)

A system-wide equalizer for Android that can apply a **separate EQ to the earpiece speaker and the bottom speaker at the same time**. It attaches an audio effect to other apps' audio sessions, and it is built around phones that use the earpiece as the second stereo speaker.

> **Closed source.** This repository does not grant any right to use, copy, modify or redistribute the source code. See [License](#license).

## Features

- **Per-speaker EQ** – independent EQ for the earpiece and for the bottom speaker. Turn either one on, or both at once.
- **Up to ±50 dB per band**, 1–32 sliders (default 10), preamp ±12 dB.
- **Smooth interpolation** – frequencies between sliders are filled in automatically (monotone cubic interpolation, no overshoot) onto a fixed set of fine internal bands.
- **Screen-rotation aware** – the earpiece/bottom assignment follows the screen orientation, so reverse landscape and upside-down portrait are handled.
- **Two attach modes**
  - *Normal*: attaches to each app's audio session (detected via the audio-effect broadcast and `dumpsys media.audio_policy`).
  - *Legacy*: attaches to the global output mix (session 0). No `dumpsys` / DUMP permission needed.
- **Automatic bypass for headphones** – when wired, USB or Bluetooth audio output is connected the EQ is released; it is re-applied when you are back on the built-in speakers.
- **Auto-start after reboot** (and after an app update), with a shortcut to the battery-optimization exemption.
- **Presets** – save/load named EQ curves for whichever speaker you are editing.
- **Combined presets** – save the earpiece EQ and the bottom-speaker EQ (plus their on/off state) as one preset and apply both with one tap.
- **Backup / transfer** – export/import settings and presets as JSON (file or clipboard). Old `eq.xml` files can be imported too.
- **Quick Settings tile**, test tones (per speaker, with or without EQ) and an on-screen diagnostic log.
- **English / Japanese UI** – Japanese if the device language is Japanese, English otherwise.
- No ads, no analytics, and the app does not request the `INTERNET` permission.

## Requirements

- Android 10 (API 29) or later.
- A device whose earpiece is driven as one of the stereo channels (the app assumes earpiece = left channel in portrait).
- For **Normal mode**, the app needs to read `dumpsys`. Either:
  - grant the DUMP permission once from a PC:
    ```
    adb shell pm grant rakkashin.earpieceeq android.permission.DUMP
    ```
  - or use a rooted device (the app can grant it to itself via `su`).
- **Legacy mode** needs neither, but some devices/ROMs refuse effects on session 0.

## Setup

1. Install the APK and open the app once (Android does not deliver boot events to apps that were never opened).
2. Allow notifications (the equalizer runs as a foreground service).
3. Turn on **Enabled**.
4. Choose where to apply EQ: *Apply EQ to earpiece* and/or *Apply EQ to bottom speaker*.
5. Use **EQ to edit** to switch which speaker's sliders you are changing.
6. Tap **Exempt from battery optimization** so auto-start after reboot is reliable.
7. If nothing happens in Normal mode, grant the DUMP permission (above) or switch on **Legacy mode**.

## Usage tips

- Use the test tones to check that the sound comes from the speaker you expect for the current orientation.
- The `Enabled` switch and the Quick Settings tile start/stop the service.
- If the EQ does not seem to work, tap **Write dumpsys session lines to log** / **Copy full log** and check the log lines starting with `attach OK ... readback`.
- Do not run it together with another system-wide equalizer.

## Known limitations

- The earpiece cannot reproduce very low or very high frequencies, so those sliders may be inaudible there regardless of the setting.
- Channel mapping (earpiece = left in portrait, swapped at 180° / 270°) is based on tests on one device family; other devices may differ.
- A connected Bluetooth/USB/wired output is treated as "headphones" and disables the EQ, even if audio is not actually routed there.
- Large gains (up to ±50 dB) can be very loud or distort. Lower the volume before adjusting.
- Provided as is, without warranty.

## Permissions

| Permission | Why |
|---|---|
| `MODIFY_AUDIO_SETTINGS` | Attach audio effects |
| `FOREGROUND_SERVICE`, `FOREGROUND_SERVICE_SPECIAL_USE` | Keep the equalizer alive |
| `POST_NOTIFICATIONS` | Show the foreground-service notification |
| `RECEIVE_BOOT_COMPLETED` | Auto-start after reboot |
| `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` | Open the battery-optimization exemption dialog |
| `DUMP` (optional, granted via adb/root) | Detect audio sessions with `dumpsys` |

## Build

APKs are produced by the maintainer with GitHub Actions (`.github/workflows/build-earpiece-eq.yml`). Updates are signed with the same key, so a new build installs over the previous one and keeps your settings.

## License

Copyright © Rakkashin. **All rights reserved.** The source code is closed; no license is granted to use, copy, modify, distribute or reverse engineer it. The compiled app is provided for personal use as is, without any warranty.


---

# 日本語

**[English](#earpiece-eq)** | 日本語

**イヤーピース側スピーカーと下スピーカーに、別々のEQを同時に適用できる**Android用のシステム全体イコライザーです。他アプリの音声セッションにオーディオエフェクトを張る方式で、イヤーピースをステレオの片側として使う端末向けに作っています。

> **クローズドソースです。** このリポジトリは、ソースコードの利用・複製・改変・再配布の権利を一切許諾しません。[ライセンス](#ライセンス)を参照してください。

## 機能

- **スピーカー別EQ** – イヤーピースと下スピーカーで独立したEQ。片方だけでも、両方同時でもONにできます。
- **各バンド最大±50 dB**、スライダー数は1〜32本(初期10本)、プリアンプは±12 dB。
- **自動の滑らかな補間** – スライダーの間の周波数は、固定の細かい内部バンドに単調3次補間(行き過ぎなし)で自動的に埋められます。
- **画面の向きに追従** – イヤーピース/下スピーカーの割り当てが向きに合わせて入れ替わるので、反転横や逆さ縦でも狙い通りに掛かります。
- **2つの適用モード**
  - *通常*: アプリごとの音声セッションに張ります(エフェクト通知のブロードキャストと `dumpsys media.audio_policy` で検出)。
  - *レガシー*: グローバル出力ミックス(セッション0)に張ります。`dumpsys` もDUMP権限も不要です。
- **イヤホン接続時は自動で無効化** – 有線・USB・Bluetoothの出力が接続されるとEQを解除し、本体スピーカーに戻ると自動で再適用します。
- **再起動後(とアプリ更新後)の自動開始**。バッテリー最適化の除外へのショートカット付き。
- **プリセット** – 編集中のスピーカーのEQに名前を付けて保存/読込。
- **まとめてプリセット** – イヤーピースと下スピーカーのEQ(と適用のON/OFF)を1つにまとめて保存し、ワンタップで両方に適用。
- **バックアップ・引き継ぎ** – 設定とプリセットをJSONで書き出し/読み込み(ファイル/クリップボード)。旧版の `eq.xml` も読み込めます。
- **クイック設定タイル**、スピーカー別のテスト音(EQ込み/なし)、画面上の診断ログ。
- **日本語/英語UI** – 端末の言語が日本語なら日本語、それ以外は英語で表示します。
- 広告・解析なし。`INTERNET` 権限も要求しません。

## 動作要件

- Android 10(API 29)以上。
- イヤーピースがステレオの片チャンネルとして鳴る端末(縦向きでイヤーピース=左チャンネルを想定)。
- **通常モード**では `dumpsys` を読む必要があります。次のどちらかを行ってください。
  - PCから一度だけDUMP権限を付与:
    ```
    adb shell pm grant rakkashin.earpieceeq android.permission.DUMP
    ```
  - rootの端末を使う(アプリが `su` で自分に付与できます)。
- **レガシーモード**はどちらも不要ですが、セッション0へのエフェクトを拒否する端末/ROMもあります。

## セットアップ

1. APKをインストールして、一度アプリを開きます(一度も開いていないアプリには、起動完了の通知が届かないため)。
2. 通知を許可します(イコライザーはフォアグラウンドサービスで動きます)。
3. **有効** をONにします。
4. 「イヤーピース側にEQを適用」「下スピーカー側にEQを適用」で、EQを掛ける側を選びます(両方も可)。
5. 「編集するEQ」で、いまスライダーを操作する側を切り替えます。
6. 「バッテリー最適化を除外」を押して、再起動後の自動開始を安定させます。
7. 通常モードで反応しない場合は、DUMP権限を付与するか、**レガシーモード**をONにしてください。

## 使い方のヒント

- テスト音で、いまの向きで狙ったスピーカーから鳴るか確認できます。
- 「有効」スイッチとクイック設定タイルで、サービスの開始/停止ができます。
- EQが効かないときは、「dumpsysのsession行をログへ」「ログを全文コピー」で、`attach OK ... readback` で始まるログ行を確認してください。
- 他のシステム全体イコライザーとは併用しないでください。

## 既知の制限

- イヤーピースは非常に低い/高い周波数を再生できないため、その帯域のスライダーは設定に関わらず聞こえないことがあります。
- チャンネルの対応(縦向きでイヤーピース=左、180°/270°で入れ替え)は、特定の端末での検証に基づいています。他の端末では異なる場合があります。
- 接続中のBluetooth/USB/有線出力は、実際には音が流れていなくても「イヤホン」として扱われ、EQが無効になります。
- 大きなゲイン(最大±50 dB)は非常に大きな音や歪みの原因になります。調整前に音量を下げてください。
- 現状有姿で提供し、いかなる保証もしません。

## 権限

| 権限 | 用途 |
|---|---|
| `MODIFY_AUDIO_SETTINGS` | オーディオエフェクトを張る |
| `FOREGROUND_SERVICE`, `FOREGROUND_SERVICE_SPECIAL_USE` | イコライザーの常駐 |
| `POST_NOTIFICATIONS` | フォアグラウンドサービスの通知表示 |
| `RECEIVE_BOOT_COMPLETED` | 再起動後の自動開始 |
| `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` | バッテリー最適化の除外ダイアログを開く |
| `DUMP`(任意。adb/rootで付与) | `dumpsys` で音声セッションを検出 |

## ビルド

APKは、メンテナーがGitHub Actions(`.github/workflows/build-earpiece-eq.yml`)で作成します。更新版は同じ鍵で署名されるため、前のビルドに上書きインストールでき、設定も残ります。

## ライセンス

Copyright © Rakkashin. **All rights reserved.** ソースコードは非公開で、利用・複製・改変・再配布・リバースエンジニアリングのいかなる権利も許諾しません。ビルド済みアプリは個人利用に限り、現状有姿で提供し、いかなる保証もしません。

