# Earpiece EQ

Androidの左右チャンネルを個別に調整するオーディオアプリです。イヤーピース側と下スピーカー側に別々の設定を使い、両側にかける共通処理も重ねられます。

[最新版をダウンロード](https://github.com/Goubata/A-separate-speaker-equalizer-app-for-the-left-and-right-channels/releases/latest) · [English](#english)

## できること

| メニュー | 主な機能 |
|---|---|
| 個別EQ・共通EQ | 1〜32バンド、各バンド±50 dB、プリアンプ±12 dB、滑らかな周波数補間 |
| 個別低音・共通低音 | 仮想低音、低音の厚み、処理帯域調整、曲に合わせた自動調整、低域の上限設定、処理量メーター |
| 個別FX・共通FX | 音量均等化、明瞭度、真空管・アナログ質感、高域拡張、最大10バンドのダイナミックEQ、IRS/WAVコンボルバー、低域モノラル、サラウンド、3帯域ステレオ幅、リバーブ |
| 処理品質 | 非線形処理の内部オーバーサンプリング。元音源にない録音情報を復元する機能ではありません |
| プリセット | 個別・共通・まとめて保存に加え、動作設定も含む全設定プリセット、機器名付き外部用プリセット（個別／全件ファイル入出力） |
| バックアップ | JSONファイル／クリップボードで書き出し・読込、旧eq.xmlの取込、IRSデータのバックアップ |
| 出力別設定 | 本体と外部機器の設定を切り替え、再接続時に最後の設定を復帰 |

個別段と共通段は同時に使えます。サラウンド・ステレオ幅・低域モノラルは左右を参照する処理です。設定は各側と共通段で分かれていますが、音声信号そのものが完全に独立する処理ではありません。

- 本体スピーカーでは画面の向きに合わせて適用チャンネルを切り替えます。外部出力の左右は固定です。
- 処理の有効／無効は専用モジュールの音声処理にも連動します。出力監視サービスだけが動作を続ける場合があります。
- ダークモード、大きな文字、クイック設定タイル、テスト音、診断ログ、導入済みモジュールのバージョン表示に対応しています。
- 端末の言語が日本語なら日本語、それ以外は英語で表示します。
- 録音・画面キャプチャによる音声取得は使いません。

## 必要なもの

| 機能 | 条件 |
|---|---|
| アプリ | Android 10以降。音声エフェクトの対応状況は端末・ROM・プレーヤーに依存 |
| 従来の個別EQ | Android標準のDynamicsProcessing。レガシーモードではセッション0を使用し、DUMP権限は不要。通常モードの補助スキャンにはDUMP権限またはrootが必要 |
| 共通EQ・低音・追加FX | rootと専用AIDLモジュール。最新の同梱版を推奨。音量均等化はモジュールv0.21以降が必要 |
| 専用モジュール | **Android 16・arm64・AIDL effect HAL v3向け**。全端末への互換性を保証するものではありません |
| 外部機器ごとの自動切替 | メディア出力先を取得できるAndroid 13以降。識別不能・複数出力時は処理と編集を停止 |
| プリセット・バックアップ・UI | 機能自体にrootは不要。音声への適用条件は上記の通り |

root用処理を使うために、レガシーモードをONにする必要はありません。

## 導入・更新

1. [Releases](https://github.com/Goubata/A-separate-speaker-equalizer-app-for-the-left-and-right-channels/releases/latest)からAPKをダウンロードし、インストールします。同じ署名の既存アプリには上書きできます。
2. 一度アプリを開き、通知を許可します。自動開始を使う場合は、バッテリー最適化の除外も設定します。
3. root用機能を使う場合は、リリースの専用モジュールZIPをMagiskで導入・更新して再起動します。アプリから同梱ZIPを書き出すこともできます。
4. rootを許可し、処理を有効にします。適用する側と編集する側を選び、必要な機能をONにします。
5. 調整後は「プリセット」メニューで保存できます。

既にモジュールv0.21を使っている場合、**v0.21.1は説明文の更新のみ**です。音声処理のためのモジュール再更新は不要です。v0.21.1のZIPは、モジュールの説明とインストール案内も更新しています。

通常モードのセッション検出にDUMP権限を付与する場合:

```sh
adb shell pm grant rakkashin.earpieceeq android.permission.DUMP
```

## 全設定プリセットと外部出力

「全設定プリセット」は、個別・共通の音声設定に加え、処理ON/OFF、レガシーモード、回転追従、自動開始・切替、本体と保存済み外部機器の設定を保存・復元します。実際の接続先、Androidの権限、インストール済みモジュール、プリセット一覧そのものは変更しません。バックアップには保存済みプリセットも含まれますが、取り込むだけで全設定プリセットが適用されることはありません。

外部出力でも処理したい場合は、本体出力に戻って「イヤホン接続中は処理を停止する」をOFFにします。外部出力中はEQ・低音・追加FXなどの音声調整と処理ON/OFF・レガシーモードを利用し、本体用の動作設定は操作できません。新しい機器は処理OFF・フラットで始まります。固有情報のない機器や同名機器、受動型の有線イヤホンは、設定を共有する場合があります。

「プリセット」画面先頭の外部デバイス用プリセットは、過去に保存された機器も選択できます。一覧に対象機器名と短い識別IDを表示します。適用はその機器の設定だけを復元し、未接続の場合は次回接続時に使われます。取り込むだけでは音声設定を適用しません。ファイルには保存済みのIRSも含められます。

## 音量と動作について

dBFSはデジタル信号の値で、スピーカーの音圧や振動量ではありません。低音の上限や音量均等化は、すべての曲を完全に同じ聴感音量にしたり、元音源の歪みを修復したりするものではありません。低い音量から調整してください。スピーカーが再生できる帯域にも限界があります。

音量均等化は有効な段ごとに約5ms、IRS畳み込みは512サンプルの遅延を加えます。音量均等化の強さ0は素通しです。効果が分からない場合は、対象出力・適用側・各機能のON/OFF・ステータス・モジュールのバージョンを確認してください。

## 配布とライセンス

このリポジトリは説明とビルド済み成果物の配布用です。アプリのソースコードは公開していません。

Copyright © Rakkashin. All rights reserved. ビルド済みアプリは個人利用向けに現状有姿で提供し、保証はありません。同梱の第三者コンポーネントについては、各ライセンスとNOTICEを参照してください。

---

## English

Earpiece EQ is an Android audio app with separate settings for the earpiece and bottom speaker, plus common processing that can be stacked with the individual stages.

[Download the latest release](https://github.com/Goubata/A-separate-speaker-equalizer-app-for-the-left-and-right-channels/releases/latest)

### Features

- Individual and common EQ: 1–32 bands, ±50 dB per band, ±12 dB preamp and smooth frequency interpolation.
- Individual and common bass: virtual bass, bass body, adjustable processing bands, automatic bass adjustment, low-band ceilings and processing meters.
- Individual and common FX: full-band volume leveling, clarity, tube/analog character, spectrum extension, up to 10-band dynamic EQ, IRS/WAV convolution, bass mono, surround, three-band stereo width and reverb.
- Internal oversampling for nonlinear processing. It cannot recover missing recorded information or turn the hardware output into true hi-res.
- Individual, common, combined and full settings presets; external device presets with device names and individual/all JSON file export/import; legacy eq.xml import and backups with IRS data.
- Saved settings per external output, speaker assignment following screen rotation, dark mode, large text, Quick Settings tile, test tones, diagnostics and installed module version.
- Japanese UI for Japanese devices; English for every other language. No recording or screen capture is used to obtain audio.

Individual and common stages work together. Spatial effects, stereo width and bass mono reference both channels, while retaining separate controls per output side and common stage. The master switch also bypasses the companion module's audio processing; a route monitor may remain running.

### Requirements and setup

| Feature | Requirements |
|---|---|
| App | Android 10+. Audio-effect support depends on the device, ROM and player |
| Original individual EQ | Android DynamicsProcessing. Legacy mode uses session 0 without DUMP permission. Normal-mode supplemental scanning needs DUMP permission or root |
| Common EQ, bass and advanced FX | Root and the companion AIDL module. Volume leveling needs module v0.21+ |
| Companion module | **Targets Android 16, arm64, AIDL effect HAL v3**; not a universal module |
| Automatic external output profiles | Android 13+ media-route identification. Unknown or multiple routes pause processing and editing |
| Presets, backup and UI | No root needed for these features themselves |

Install the APK from Releases over the existing app, open it and allow notifications. For root DSP, install/update the companion ZIP in Magisk and reboot, grant root access, then enable processing and the desired features. You can also export the bundled ZIP from the app. Legacy mode is optional for root DSP. Existing settings are preserved when updating with the same app ID and signature.

If you already use module v0.21, **v0.21.1 only changes descriptions**: a module update is optional for audio processing. Its ZIP updates the module description and installer text as well.

For normal-mode supplemental session scanning, DUMP permission can be granted from a PC:

```sh
adb shell pm grant rakkashin.earpieceeq android.permission.DUMP
```

### Full settings and external outputs

Full settings presets restore audio and operation settings for built-in and saved external outputs, including processing ON/OFF, Legacy mode, rotation and automatic startup/switching. They keep the actual connection and preset libraries unchanged, and do not restore Android permissions or installed modules. Backup import carries saved full presets without applying them automatically.

To process external outputs, return to built-in output and turn off “Pause processing for earphones.” Audio controls and processing/Legacy switches are available externally; built-in operation settings remain locked. New devices start with processing OFF and flat settings. Devices without unique metadata, passive wired headphones or identically named devices may share a profile. External left/right assignments stay fixed when the screen rotates.

The External device presets section lists preset names, target device names and short identifiers. You can manage previously saved devices while disconnected. Apply restores only the target device; import only adds presets to the library. Individual/all JSON export includes available referenced IRS data.

### Levels and latency

Digital dBFS is not acoustic sound pressure or speaker excursion. Bass ceilings and volume leveling do not guarantee identical perceived loudness or repair existing clipping. Start at a low listening volume. Physical speakers also have limited frequency response.

Each active volume-leveling stage adds about 5ms; IRS convolution adds 512 samples. Leveler strength 0 is bypass. Check the current output, selected side, feature switches, status and module version if an effect seems absent.

### Distribution

This repository distributes documentation and compiled releases. Application source is not published.

Copyright © Rakkashin. All rights reserved. Compiled application releases are provided for personal use as is, without warranty. Third-party components retain their respective licenses and notices.

## USBレート追従（実験） / Experimental USB rate follow

v0.24「処理品質」に初期OFFで追加。root・Android 14以降・対応USB出力・同じ出力上の有効なAIDL DSPが必要です。単一PCMステレオ音源にDEFAULT mixerを合わせ、EQ/低音/FXとの併用を試みます。排他/BIT_PERFECTではありません。非対応・複数音源・既存優先設定は変更せず、要求後にレート/DSPを確認できなければ解除。全体OFFにも連動します。**USB DAC実機と実際のSRC回避は未検証です。** 元ファイルやDAC内SRCは判定できず、プレーヤー側変換は取り消せません。切替時の音切れ、異常終了後の解除保留があり得ます。保留時はアプリ再起動かUSB再接続を行ってください。

Initially OFF under Processing quality. Requires root, Android 14+, compatible USB output and active AIDL DSP on the same output. Requests DEFAULT mixer attributes matching one PCM stereo media track; intended to retain EQ/bass/FX. No exclusive/BIT_PERFECT. Unknown/multiple/unsupported streams and existing preferences are skipped. Missing rate/DSP confirmation triggers rollback; master OFF releases the request. **Physical USB DAC behavior and actual SRC avoidance are untested.** Android-boundary observations cannot prove file/DAC-internal SRC bypass or undo player-side conversion. Switching may interrupt playback; reopen the app or reconnect USB if cleanup remains pending.
