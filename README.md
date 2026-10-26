# Earpiece EQ

[日本語](#ja) · [English](#en) · [简体中文](#zh)

[Download / ダウンロード / 下载](https://github.com/Goubata/A-separate-speaker-equalizer-app-for-the-left-and-right-channels/releases/latest)

<a id="ja"></a>
## 日本語

Androidのイヤーピース側・下スピーカー側を別々に調整するオーディオアプリです。外部出力では左・右チャンネルを調整します。個別設定に加え、両側へ適用する共通EQ・共通低音・共通FX・共通HiFiを重ねて使えます。この説明は **v0.33-stability** を対象にしています。

### 1. 導入・必要条件

| 機能 | 必要なもの |
|---|---|
| アプリ・画面・設定保存 | Android 10以降。保存や画面操作自体にrootは不要 |
| 個別EQ | Android標準のDynamicsProcessing。専用モジュールは不要。音声への適用可否は端末・ROM・プレーヤー次第 |
| 共通EQ、個別/共通低音、メーター、追加FX、内部処理品質 | rootと専用AIDLモジュール |
| 音量均等化 / アップミックス / HiFi | rootとモジュールv0.21以上 / v0.22以上 / v0.25以上 |
| 強弱連動の振動 / 音声波形モード | rootとモジュールv0.27以上 / v0.29以上、および対応モーター・振動音声出力 |
| 外部出力の自動識別 | Android 13以降のメディア出力先情報 |
| USBレート追従 | root、Android 14以降、対応USB出力、同じ出力上で有効な専用DSP |

**同梱モジュールはAndroid 16・arm64・AIDL effect HAL v3向けです。** SoC名だけでは対応を判定できず、すべての端末で動く汎用モジュールではありません。現在の機能一式には同梱v0.30を使用してください。

1. ReleasesのAPKをインストールします。同じアプリID・署名での上書き更新は既存設定を引き継ぎます。
2. アプリを開き、通知を許可します。再起動後の自動開始を使う場合はバッテリー最適化の除外も設定します。
3. root用機能を使う場合は専用ZIPをMagisk/KernelSUで導入・更新し、再起動します。「設定」から同梱ZIPの書き出しもできます。
4. rootを許可し、全体の「音声処理を有効にする」と、使う機能をONにします。v0.29の音声波形モードにはAPK・モジュール両方の更新が必要です。

### 2. 処理ON/OFF・適用先・動作モード

- **全体スイッチ**：アプリと専用モジュールの音声処理・モーター出力を停止/再開します。外部機器を監視するサービスと通知だけが残る場合があります。他アプリのエフェクトをOFFにするスイッチではありません。
- **個別EQ・低音の適用側 / EQの編集側**：どちらに適用するかと、どちらのEQを編集するかは別です。両側の適用をONにすると別々の設定を同時に使えます。
- **共通処理**：個別処理と併用できます。個別FX→共通FX、個別低音→共通低音の順に重ねます。低音の上限は後段でまとめて適用します。共通段をONにしても個別設定は消えません。
- **レガシーモード**：個別EQをグローバル出力ミックス（セッション0）へ適用します。DUMP権限・セッション検出は不要です。未設定時はON、全体処理の初期値はOFF。保存済みOFF/ONは保持します。
- **通常モード**：個別EQをアプリごとの音声セッションへ適用します。セッション通知と補助スキャンを利用し、手動の再スキャン・セッションID指定にも対応します。専用root DSPのためにレガシーモードをONにする必要はありません。
- **回転追従**：本体では画面の向きに合わせて物理L/Rの割当を切り替え、イヤーピース側・下側の設定を追従させます。外部出力の左右は固定です。機種の配線に合うかテスト音で確認できます。

通常モードの補助スキャン用DUMP権限はPCから付与できます。rootが利用可能な場合の権限付与・`dumpsys`実行にも対応します。

```sh
adb shell pm grant rakkashin.earpieceeq android.permission.DUMP
```

### 3. 個別EQ・共通EQ

1〜32バンド、各バンド **−50〜+50 dB**、プリアンプ **−12〜+12 dB**。約31Hz〜20kHzの制御点の間を対数周波数上で滑らかに補間します。バンド数ごとにゲインを記憶し、バンド数を戻すとその調整値へ戻ります。フラットへのリセットも可能です。1バンドでは全体のゲインを調整します。

個別EQは編集対象の片側へ、共通EQは独立したスイッチと設定で両側へ適用します。共通EQだけのプリセットも保存できます。

### 4. 個別低音・共通低音

両メニューで同じ種類の調整ができます。個別は左右別、共通は両側に共通設定を追加する段です。

| 機能 | 効果・設定 |
|---|---|
| 仮想低音 | 倍音を生成し、小型スピーカーで出にくい低音を感じやすくします。強さ0〜100%、0%でOFF |
| 低音の厚み | 弱い低音の持ち上げと倍音追加で厚みを調整し、超低域の負担も抑えます。0〜100%、0%でOFF |
| 低音の帯域調整 | ONで仮想低音・厚み・自動調整の対象を変更。中心50〜180Hz、幅1.0〜2.5オクターブ。OFFで従来の帯域へ戻す |
| 曲に合わせた自動調整 | 弱い低音を持ち上げ、強い低音を最大12dB抑制。目標検出レベル−36〜−12dBFS、最大持ち上げ0〜12dB。静音の持ち上げを抑えるゲートあり |
| 低音のピーク上限 | 250Hz以下の処理バンドへ−36〜−3dBFSのデジタルピーク上限を設定。ON/OFF、0.1dB刻み。弱い低音を上限まで自動で揃える機能ではありません |
| メーター | 検出した低音の入力、実際の自動増減量、出力ピーク、上限で抑えた量を表示。メーターのみの利用にも対応 |

自動調整の「目標」は検出レベル、リミッターの「上限」は出力制限です。役割が異なり、両方を併用できます。

### 5. 個別FX・共通FX

各機能を個別にON/OFFし、左右別と共通段で調整できます。サラウンド・ステレオ幅・低域モノラルなどは相手側の音も参照するステレオ処理であり、音声信号まで完全に独立するわけではありません。各FXは独自実装です。

| 機能 | 効果・主な調整項目 |
|---|---|
| 音量均等化 | 全帯域の平均音量を目標へ近づけ、静かな音源を持ち上げ、大きい音源を抑制。強さ、目標平均レベル、最大増幅/抑制、抑える速さ、回復時間、静音ゲート、ピーク上限。強さ0は素通し |
| 立体音響アップミックス | 中央成分と左右差から横方向の広がりと拡散音を追加。強さ、横幅、拡散音、中央の音の保持。ステレオ内の処理で、多チャンネル音源の復号ではありません。強さ0は素通し |
| 明瞭度 | Natural / OZone+ style / XHiFi styleの3方式。プレゼンスや高域の倍音・細部感を調整。強さと対象周波数1〜10kHz |
| 真空管シミュレーター | 非対称の柔らかい飽和で偶数次倍音を追加。ドライブ、原音との混合量 |
| アナログ質感 | ソフトな飽和と高域の緩やかな減衰。ドライブ、混合量 |
| 高域拡張 | 高域から倍音を合成。倍音量、開始周波数2〜12kHz。失われた録音情報の復元ではありません |
| ダイナミックEQ | 最大10バンド。各検出レベルがしきい値を超えると指定ゲインへ変化。各バンドで周波数、最大動的ゲイン±12dB、しきい値、アタック、リリース、Qを調整 |
| コンボルバー / IRS | インパルス応答ファイルによる音色・空間特性の適用。ファイル選択/解除、混合量、IRSゲイン−24〜+12dB。未選択時は素通し |
| フィールドサラウンド | 拡散音による空間の広がり。強さ、拡散時間 |
| 差動サラウンド | 左右の時間差による広がり。遅延差、遅延音の混合量 |
| 3帯域ステレオイメージャー | 低/中/高域の幅を個別に0〜200%で調整。0%は中央寄り、100%は原音の幅、100%超は拡張。低/中・中/高の境界周波数も変更可能 |
| ヘッドホンサラウンド | 帯域制限した遅延クロスフィード。空間量、3段階の空間サイズ |
| リバーブ | 左右の残響ネットワーク。混合量、残響時間、高域減衰、部屋サイズ |
| 低域モノラル | 指定した低域を中央へ寄せる処理。対象40〜300Hz、混合量 |

音量均等化の目標は−36〜−9dBFS、最大持ち上げ0〜18dB、最大抑制0〜36dB。低音だけを調整する自動低音とは別機能です。「追加FXをリセット」は選んだ段のFXをOFF・初期値へ戻し、その段のHiFi設定は保持します。

IRSはモノラル/ステレオのPCM16/24/32・float32 RIFF/WAVE、または48kHzモノラルfloat32のraw IRSに対応します。8〜192kHz、最大65,536フレームで、再生レートへ変換した後も同じ上限です。個別側へステレオIRSを選んだ場合は対応する側のチャンネルを使用します。

### 6. HiFi

左右別・共通段を重ねて利用できます。各段のON/OFFと、その段だけの初期化に対応します。

| 設定 | 意味 |
|---|---|
| 入力ヘッドルーム | 既存の低音・FXより前に0〜24dBの減衰を加え、後続処理の余裕を作る |
| DC除去 | 直流成分を減らす。カットオフ2〜30Hz。超低域も減衰します |
| 極性反転 | 波形の符号を反転。片側だけ反転すると中央の音が弱まる場合があります |
| 最終ピーク上限 | DSP後のサンプルピークを−12〜−0.1dBFSへ制限。回復時間20〜1000ms。共通段は左右連動 |
| 出力形式の参考情報 | 選択中の機器が報告する対応レート・PCM形式/ビット深度を表示し、再取得可能。現在の実動作形式や元ファイル形式とは異なります |

ヘッドルームは前段、DC除去→極性→ピーク抑制は後段で個別→共通の順です。HiFiは音源情報を復元するモードではなく、ピーク上限もTrue Peakや実際の音圧を保証しません。

### 7. 処理品質・USBレート追従

- **標準 / 2倍 / 4倍**：真空管・アナログ質感・倍音生成の内部オーバーサンプリングで折返しノイズを低減します。左右と共通FXへ適用され、高品質ほどCPU負荷・処理遅延が増えます。
- 内部品質の変更は、端末出力を最大サンプリングレート/ビット深度へ変更する機能ではありません。失われた情報を作り直したり、元音源を本来のハイレゾ録音へ変えるものでもありません。
- **USBレート追従（実験・初期OFF）**：単一のPCMステレオ音源がAndroidへ渡す形式へ、USBのDEFAULT mixerを合わせる要求を試みます。同じ出力上の専用DSPが有効な場合のみ継続し、排他/BIT_PERFECTモードは使いません。
- 非対応・複数音源・既存の優先設定などは変更せず、レート/DSPを確認できない場合は要求を解除します。全体OFFや切断にも連動します。解除保留時はアプリ再起動/USB再接続を行ってください。
- **USB DAC実機での挙動と実際のSRC回避は未検証です。** プレーヤー内部やDAC内部の変換は判定・解除できず、システム全体の排他再生を保証する機能ではありません。

### 8. バイブレーション・モーター専用EQ

本体出力専用です。全体の処理とモーター出力をONにして利用します。機能の初期値はOFF、強さ15%。波形モードも初期OFFで、以前の設定を勝手に切り替えません。振動を理由に他の音声機能を無効化する処理はありません。

| モード/設定 | 動作 |
|---|---|
| 強弱連動 | DSPの低音包絡をモーターの強さへ変換。左右の強い方へ追従。検出上限60〜200Hz、強さ1〜100%。元の音程を再生する方式ではありません |
| 音声波形モード（試験機能） | DSPのPCMコピーを別セッションの振動用出力へ送る。オクターブ変換せず元の周波数を保持。左右を平均して1つのモーターへ送る |
| モーター専用EQ | 波形モードで40/80/160/320/640/1280Hzの6バンド、各±12dB・0.5dB刻み。スピーカーEQには影響しない。フラットへリセット可能 |
| 追加ブースト | 初期OFF。ONで追加強度スライダーを表示。元の強さに0〜200%を加算（最大3倍）。波形・強弱連動の両方式に適用。全設定プリセットとバックアップへ保存。端末側の上限を超えることはできない |
| 強さと保護 | 波形モードはDC除去・折返し抑制・デジタル駆動量制限を行う。強さとOS側のメディア/振動設定、端末の機械特性が実際の振動に影響 |

波形モード中は従来の検出上限を編集できず、強弱連動モード中は波形用EQを編集できません。全体OFF・機能OFF・外部出力・古い/停止したデータでは停止します。非対応なら状態に表示して停止し、別方式へ黙って切り替えません。必要な更新後は機能をOFF→ONにして再試行できます。

スピーカーは元の経路で全帯域を再生し、追加ストリームの音声L/Rは常に無音です。録音・音声モード切替・フォーカス要求・他アプリのエフェクトをOFFにする操作は行いません。**モーターの音色・音圧・物理的な音程の再現精度・完全な同時発音は保証できません。** 同期のためにスピーカーへ遅延を加える処理もありません。新モジュール導入後の実音楽の聴感・同期は、リリース時点では未確認です。

### 9. プリセット・バックアップ

| 種類 | 保存・適用する範囲 |
|---|---|
| 片側プリセット | 編集中の側のプリアンプ・バンド数/ゲイン・仮想低音・厚み・低音上限・帯域/自動調整/メーター設定 |
| 共通EQプリセット | 共通EQのON/OFF・プリアンプ・バンド数/ゲイン |
| まとめてプリセット | 現在の出力の両側EQ/低音・適用ON/OFF、共通EQ/低音、個別/共通FX（HiFiを含む）と内部処理品質 |
| 全設定プリセット | 本体と保存済みの全外部機器の音声/動作設定。処理ON/OFF、レガシー、回転、自動開始、USB追従、モーターのモード/強さ/EQなども含む |
| 外部デバイス用プリセット | 選択した機器専用の音声設定、処理ON/OFF、レガシー。対象機器名と短い識別IDを一覧表示。未接続機器も選べ、適用はその機器だけへ反映 |

名前を付けて保存・呼び出し/適用・削除できます。同じ名前での保存は上書きです。**すべての設定を戻したい場合は「全設定プリセット」を使用してください。** 全設定プリセットは現在の物理的な接続先・プリセット一覧・Androidの権限・導入済みモジュールを復元/変更するものではありません。

通常バックアップはJSONファイル/クリップボードへ書き出し・読み込みでき、旧`eq.xml`、保存済みプリセット、IRSデータの移行にも対応します。取り込みだけで処理ONや全設定プリセットの適用は行いません。外部プリセットは個別/全件のJSONファイル入出力に対応し、機器の関連付けと利用可能なIRSも含めます。外部用ファイルの取り込みだけでは音声設定を変更しません。モーター設定は全設定/通常バックアップへ含め、外部専用プリセットからは除外します。

### 10. 外部出力・出力別の自動保存

本体出力に戻って「イヤホン接続中は処理を停止する」をOFFにすると、外部でも音声処理を利用できます。接続先ごとにEQ・低音・FX・HiFi・品質・処理ON/OFF・レガシーを記憶し、本体へ戻ると本体設定、再接続するとその機器の最後の設定へ戻ります。

新しい機器は処理OFF・フラットで開始します。外部出力時は音声調整と処理/レガシーの操作を利用し、本体の回転・自動起動・モーターなどの動作設定は制限します。出力不明・複数出力では処理と関連編集を停止します。固有情報のない機器、同名機器、受動型有線イヤホンはプロファイルを共有する場合があります。

### 11. 画面・起動・診断

- EQ、共通EQ、低音、共通低音、FX、共通FX、HiFi、バイブレーション、品質、プリセット、設定、ステータスの12メニュー。
- Material風カード/タブ、システムバーの表示調整、ライト/ダーク、大きな文字、横画面に対応。アプリアイコンには黒い縁取りがあります。
- 端末が日本語なら日本語、中国語なら簡体字中国語、それ以外は英語。ユーザーが付けた機器名・プリセット名は翻訳しません。
- クイック設定タイルで全体ON/OFF。再起動/アプリ更新後の自動開始は、保存済み処理ONと自動開始設定に従います。通知とバッテリー最適化設定を確認できます。
- 1kHzの片側テスト音とEQ込みテスト音で、スピーカー割当・適用を確認可能。グローバル処理が有効なら通常のテスト音にも適用されます。
- ステータスでサービス、モード、出力先、処理セッション、権限、自動開始/電池設定、導入済みモジュール、稼働ドライバーの確認状況を表示。
- 手動セッション適用、再スキャン、`dumpsys`のセッション行取得、診断ログ表示・全文コピーに対応。

### 12. 音量・遅延・検証・配布

v0.33では、同じEQ種類に複数の実装が登録された端末で別実装が選ばれる問題に対応しました。この場合はroot補助でAndroid EQの実装IDを明示し、設定の読み戻し後に有効化します。通常の端末では従来の標準APIを使います。旧版v0.32では、再生中の全体OFF→ONに関わるPID起動判定、制御ファイルの世代一致、全設定反映後の有効化、素早い切替時の再初期化を修正しました。レガシー時は再生・出力・エンジンのイベント後に実際の順序を非同期で確認し、自分のEQが低音処理の後ろへ移った場合に並べ直します。確認はイベントごとの有限回で、常時ポーリングはしません。スピーカー音声のDSPアルゴリズムとモジュールはv0.30と同一です。実音楽の改善は更新後の確認が必要です。

dBFSはデジタル信号の値で、音圧dBや振動量ではありません。上限・自動調整・音量均等化は、すべての曲を完全に同じ聴感へ揃えたり、既に割れた音を修復したりする機能ではありません。小さい音量から調整してください。共通段を重ねると処理量も増えます。

低域上限の経路は約3ms、音量均等化は有効段ごとに約5msの先読み、IRSは有効な畳み込み段ごとに512サンプルの遅延があります。処理品質や空間エフェクトにも遅延があり、モーターと出力バッファにも遅延があります。ゼロ遅延・全端末/全プレーヤー/全他社エフェクトとの共存は保証できません。

v0.31の追加ブーストは独立したPCM/制御試験と画面操作で検証しました。反映にはAPK更新後の再起動が必要で、AIDLモジュールはv0.30と同一です。v0.30では、全設定復元の連続操作によるクラッシュを修正し、同一形式通知時のDSP継続と停止・再開時の内部状態を検証しました。Java/Android回帰20スイート、ネイティブ14スイート、独立AIDL/FMQ試験、14画面条件、461項目の全設定プリセット/バックアップ復元を確認しました。独立した振動用出力中に既存音楽DSPが有効であることはv0.29で確認しています。今回のv0.30は独立テストで検証し、使用中のAPK・モジュールは変更していません。これは試験範囲での結果であり、機械的な発音・物理同期・実USB DAC/SRC回避・任意環境の不具合ゼロの保証ではありません。

このリポジトリは説明とビルド済み成果物の配布用です。**アプリのソースコードは公開していません。** Copyright © Rakkashin. All rights reserved. ビルド済みアプリは個人利用向けに現状有姿で提供し、保証はありません。第三者コンポーネントには各ライセンス/NOTICEが適用されます。

[言語選択へ戻る](#earpiece-eq)

---

<a id="en"></a>
## English

Earpiece EQ adjusts the earpiece and bottom speaker independently. With external outputs, it adjusts left and right channels. Common EQ, bass, FX and HiFi stages can be stacked with individual processing. This manual describes **v0.33-stability**.

### 1. Installation and requirements

| Feature | Requirements |
|---|---|
| App, interface and settings storage | Android 10+. These operations themselves need no root |
| Individual EQ | Android DynamicsProcessing; no companion module. Audio support depends on the device, ROM and player |
| Common EQ, individual/common bass, meters, advanced FX and internal quality | Root and the companion AIDL module |
| Volume leveler / spatial upmix / HiFi | Root and module v0.21+ / v0.22+ / v0.25+ |
| Intensity-following vibration / waveform mode | Root and module v0.27+ / v0.29+, plus compatible motor/haptic audio output |
| Automatic external device identification | Android 13+ media routing information |
| USB rate follow | Root, Android 14+, compatible USB output and active companion DSP on that output |

**The bundled module targets Android 16, arm64 and AIDL effect HAL v3.** SoC branding alone cannot determine compatibility; this is not a universal module. Use bundled v0.30 for the current feature set.

1. Install the APK from Releases. Updating with the same app ID and signature preserves existing settings.
2. Open the app and allow notifications. For automatic startup, also review battery optimization exemptions.
3. For root features, install/update the companion ZIP with Magisk/KernelSU and reboot. The Settings menu can export the bundled ZIP.
4. Grant root, enable the master audio processing switch and enable the desired features. Waveform mode requires both the v0.29 APK and module update.

### 2. Master switch, targets and processing modes

- **Master switch:** stops/resumes this app's and the companion module's audio processing and motor output. A route-monitor service/notification may remain active. It does not turn other apps' effects off.
- **Individual EQ/bass apply targets versus the EQ edit target:** choosing which EQ to edit does not determine which sides receive individual EQ/bass. Enable both apply switches to use different settings simultaneously.
- **Common stages:** work alongside individual settings. Individual FX precedes Common FX; individual bass precedes Common bass, with bass ceilings applied afterwards. Enabling a common stage does not erase individual settings.
- **Legacy mode:** applies individual EQ to the global mix, session 0, without DUMP permission or session scanning. Unset Legacy defaults to ON; master processing initially defaults to OFF. Saved ON/OFF values are preserved.
- **Normal mode:** applies individual EQ per application audio session, using session broadcasts and supplemental scanning. Manual rescanning and session-ID attachment are available. Root companion DSP does not require Legacy mode to be ON.
- **Rotation following:** maps built-in physical L/R so settings follow the earpiece/bottom speaker when the screen rotates. External L/R stays fixed. Use the test tones to check your device's speaker mapping.

For supplemental normal-mode scanning, grant DUMP from a PC. Root-based permission granting/`dumpsys` fallback is also supported where available.

```sh
adb shell pm grant rakkashin.earpieceeq android.permission.DUMP
```

### 3. Individual and common EQ

Choose 1–32 bands, **−50 to +50 dB per band**, and **−12 to +12 dB preamp**. Control points span approximately 31Hz–20kHz, with smooth interpolation on a logarithmic frequency axis. Each band count remembers its own gains; switching back recalls them. Reset to flat is available. A single band adjusts the overall gain.

Individual EQ applies to the selected output side. Common EQ has its own switch and settings for both sides, plus dedicated common-EQ presets.

### 4. Individual and common bass

Both menus provide the following controls. Individual bass is per side; Common bass adds shared settings to both sides.

| Feature | Purpose and controls |
|---|---|
| Virtual bass | Generates harmonics to make bass more perceptible on small speakers. Amount 0–100%; 0% is OFF |
| Bass body | Lifts weak bass and adds harmonics for fullness while reducing deep-sub-bass load. 0–100%; 0% is OFF |
| Bass range | When enabled, changes the range used by virtual bass, body and automatic adjustment. Center 50–180Hz, width 1.0–2.5 octaves. OFF restores the original ranges |
| Automatic bass adjustment | Raises weak bass and reduces strong bass by up to 12dB. Detector target −36 to −12dBFS; maximum boost 0–12dB. A quiet-signal gate limits noise/silence boosting |
| Bass peak ceiling | Digital ceiling for the processing band below 250Hz: −36 to −3dBFS in 0.1dB steps, with ON/OFF. Does not automatically raise quiet bass to that ceiling |
| Meters | Show detected bass input, applied automatic gain, output peaks and ceiling reduction. Meter-only operation is supported |

The automatic target is a detector level; the limiter ceiling is an output constraint. They serve different purposes and can be used together.

### 5. Individual and common FX

Each feature has its own switch and settings per side and common stage. Surround, stereo width and bass mono reference the opposite channel; separate controls do not imply completely independent audio signals. Effects use independent implementations.

| Feature | Purpose and main controls |
|---|---|
| Volume leveler | Moves full-band average levels towards a target, raising quiet material and reducing loud material. Strength, average target, maximum boost/reduction, reduction attack, recovery, quiet gate and peak ceiling. Strength 0 is bypass |
| Spatial upmix | Adds lateral width and diffuse ambience using center/side information. Amount, lateral width, diffuse ambience and center focus. Stereo processing, not multichannel decoding. Amount 0 is bypass |
| Clarity | Natural / OZone+ style / XHiFi style. Adjusts presence or harmonic/detail character. Amount and presence frequency, 1–10kHz |
| Tube simulator | Asymmetric soft saturation adds even harmonics. Drive and wet mix |
| Analog character | Soft saturation and gentle high-frequency damping. Drive and wet mix |
| Spectrum extension | Synthesizes upper harmonics. Amount and starting frequency, 2–12kHz. Does not recover missing recording information |
| Dynamic EQ | Up to 10 bands, moving towards specified gain above each detector threshold. Per-band frequency, maximum dynamic gain ±12dB, threshold, attack, release and Q |
| Convolver / IRS | Applies an impulse response's tonal/spatial characteristics. Select/clear file, wet mix and IRS gain −24 to +12dB. No selected IRS means bypass |
| Field surround | Adds diffuse spatial presentation. Amount and diffusion time |
| Differential surround | Creates width using L/R timing differences. Delay and delayed-signal mix |
| Three-band stereo imager | Independent low/mid/high width, 0–200%: 0% narrows to center, 100% retains original width, above 100% widens. Adjustable low/mid and mid/high crossovers |
| Headphone surround | Delayed, band-limited crossfeed. Amount and three room-size choices |
| Reverberation | Left/right reverberation networks. Wet mix, decay time, high-frequency damping and room size |
| Bass mono | Moves selected low frequencies towards center. Cutoff 40–300Hz and wet mix |

Leveler targets range from −36 to −9dBFS, maximum boost 0–18dB, maximum reduction 0–36dB. It is separate from bass-only automatic adjustment. Resetting an FX stage turns its FX off and restores defaults while preserving that stage's HiFi settings.

IRS supports mono/stereo PCM16/24/32 or float32 RIFF/WAVE, and raw mono float32 IRS at 48kHz. Accepted rates are 8–192kHz, with a 65,536-frame limit also enforced after conversion to the playback rate. Selecting stereo IRS for an individual side uses the corresponding channel.

### 6. HiFi

Individual L/R and common stages can stack. Each stage has its own enable switch and reset.

| Setting | Purpose |
|---|---|
| Input headroom | Applies 0–24dB attenuation before bass/advanced FX to leave room for later processing |
| DC removal | Reduces DC offset; cutoff 2–30Hz. Also attenuates sub-bass |
| Polarity inversion | Reverses waveform sign. Inverting only one side may weaken centered sound |
| Final peak ceiling | Guards DSP output sample peaks at −12 to −0.1dBFS; recovery 20–1000ms. Common reduction links L/R |
| Reported output capabilities | Displays and refreshes the selected device's advertised rates and PCM formats/bit depths. These are not the actual running format or source-file format |

Headroom precedes bass/FX. DC removal, polarity and peak guarding run afterwards, individual then common. HiFi does not restore missing information; sample-peak limits are not True Peak or acoustic loudness guarantees.

### 7. Processing quality and USB rate follow

- **Standard / 2x / 4x:** internal oversampling reduces aliasing in tube, analog and harmonic processing across individual/common FX. Higher quality increases CPU work and processing latency.
- Quality does not force the device to its maximum output sampling rate/bit depth or turn a recording into genuine hi-res material.
- **USB rate follow, experimental and initially OFF:** attempts to match the USB DEFAULT mixer to the PCM stereo format one player supplies to Android. Continues only while active companion DSP is observed on the same output. Does not use exclusive/BIT_PERFECT mode.
- Unsupported/multiple sources and existing preferred settings are skipped. Missing rate/DSP confirmation triggers rollback; master OFF and disconnection also release the request. Reopen the app or reconnect USB if cleanup remains pending.
- **Physical USB DAC behavior and actual SRC avoidance are untested.** Player-internal and DAC-internal conversion cannot be identified/undone. This is not guaranteed system-wide exclusive playback.

### 8. Vibration and motor-only EQ

Built-in output only. Enable master processing and motor output. Initially OFF, with weak 15% strength. Waveform mode is also initially OFF, preserving previous behavior. Enabling vibration does not intentionally disable other audio features.

| Mode/setting | Behavior |
|---|---|
| Intensity following | Maps the DSP bass envelope to motor amplitude, following the stronger L/R envelope. Detection cutoff 60–200Hz, strength 1–100%. Does not reproduce source pitch |
| Waveform mode, experimental | Copies DSP PCM into a separate haptic output session. Retains source frequencies without octave shifting; averages L/R for one motor |
| Motor-only EQ | Waveform mode: six bands at 40/80/160/320/640/1280Hz, ±12dB in 0.5dB steps. Does not change speaker EQ. Reset to flat available |
| Extra boost | Initially OFF; enabling reveals an extra-strength slider. Adds 0–200% to the existing strength (up to 3×), in waveform and intensity modes. Included in full presets/backups; hardware limits still apply |
| Strength and protection | Waveform mode removes DC, suppresses aliasing and caps digital drive. Strength, OS media/haptic settings and mechanical response affect actual vibration |

The envelope cutoff is unavailable in waveform mode; waveform EQ is unavailable in intensity mode. Master OFF, feature OFF, external output and stale/missing data stop motor output. Unsupported output is reported and stopped without silently switching modes. After required updates, toggle the feature OFF then ON to retry.

Speaker audio retains its full-band route; the additional stream's audio L/R is always silent. No capture, audio-mode changes, focus requests or commands disabling other effects are used. **Identical timbre, sound pressure, mechanical pitch accuracy and exact simultaneity are not guaranteed.** Speaker audio is not delayed for synchronization. Real-music sound/synchronization with the newly installed module was unverified at release time.

### 9. Presets and backup

| Type | Stored/applied scope |
|---|---|
| Single-side preset | Edited side's preamp, band count/gains, virtual bass, body, bass ceiling and range/automatic adjustment/meter settings |
| Common EQ preset | Common EQ enable, preamp and band count/gains |
| Combined preset | Both sides' EQ/bass and apply switches for the current output; common EQ/bass, individual/common FX including HiFi, and internal quality |
| Full settings preset | Built-in and all saved external outputs' audio/operation settings, including master ON/OFF, Legacy, rotation, startup, USB follow and motor mode/strength/EQ |
| External device preset | Selected device's audio settings, processing ON/OFF and Legacy. Lists target name and short identifier. Disconnected devices can be selected; Apply affects only that device |

Named presets support save, load/apply and deletion; saving the same name overwrites it. **Use Full settings presets when you want every setting restored.** They do not change the physical connection, preset libraries, Android permissions or installed modules.

Normal backups support JSON files/clipboard, old `eq.xml`, saved preset libraries and IRS transfer. Import alone does not enable processing or apply a full preset. External presets support individual/all JSON file import/export with device associations and available IRS. Import alone does not apply audio settings. Motor settings are in full presets/normal backups, but excluded from external-only presets.

### 10. External outputs and automatic profiles

Return to built-in output and turn OFF “Pause processing for earphones” to allow external processing. EQ, bass, FX, HiFi, quality, master ON/OFF and Legacy are saved per device. Disconnection restores built-in settings; reconnection recalls that device's last settings.

New devices start with processing OFF and flat settings. External output allows audio adjustment and processing/Legacy switches while restricting built-in operation controls such as rotation, startup and motor settings. Unknown/multiple routes pause processing and related editing. Devices without unique metadata, identically named devices and passive wired headphones may share profiles.

### 11. Interface, startup and diagnostics

- Twelve menus: EQ, Common EQ, Bass, Common bass, FX, Common FX, HiFi, Vibration, Quality, Presets, Settings and Status.
- Material-style cards/tabs, system-bar appearance adjustments, light/dark modes, large text and landscape support. The app icon has a black outline.
- Japanese device language selects Japanese; all Chinese locales select Simplified Chinese; every other language selects English. User device/preset names remain unchanged.
- Quick Settings tile toggles master processing. Automatic startup after reboot/app update follows saved processing and startup settings. Notification and battery optimization controls are available.
- One-sided 1kHz regular/EQ-attached test tones help check speaker mapping and processing. Enabled global processing also affects the regular test tone.
- Status shows service, mode, output, sessions, permissions, startup/battery settings, installed module version and active-driver verification status.
- Manual session attachment, rescanning, `dumpsys` session-line collection, log display and full-log copying are available.

### 12. Levels, latency, verification and distribution

v0.33 explicitly selects the Android EQ implementation through root assistance when multiple implementations claim its effect type. It verifies settings before activation; unambiguous devices retain the public Android API. Previous v0.32 fixes PID readiness parsing, paired control-file generations, activation after all configuration/readback, and resets after rapid master toggles. Legacy mode performs a bounded burst of asynchronous checks after playback/output/engine events, repairing this app’s EQ if it follows bass processing. No perpetual polling. Speaker DSP algorithms and module remain identical to v0.30. Listening on the updated device remains necessary.

Digital dBFS is not acoustic sound pressure or motor displacement. Ceilings, automatic adjustment and leveling cannot guarantee identical perceived loudness or repair existing distortion. Start at low volume. Stacking stages increases processing.

The bass ceiling path has about 3ms latency; an active leveler adds about 5ms look-ahead per stage; IRS adds 512 samples per active convolution stage. Quality/spatial effects, motor response and output buffers also introduce latency. Zero latency and universal device/player/third-party effect compatibility are not guaranteed.

v0.31 adds vibration boost verified with isolated PCM/control tests and UI operations. Reboot after updating the APK to load the new root controller; the AIDL module remains identical to v0.30. v0.30 fixes repeated full-settings restoration crashes and verifies DSP continuity on identical configuration updates and clean STOP/START histories. Verification covered 20 Java/Android regression suites, 14 native suites, isolated AIDL/FMQ tests, 14 UI conditions and restoration of 461 typed settings through full presets/backups. The earlier v0.29 device check retained active music DSP during a separate haptic stream. v0.30 was tested in isolation without replacing the running APK/module. These results do not establish mechanical sound/synchronization, physical USB DAC/SRC bypass or bug-free behavior in every environment.

This repository distributes documentation and compiled releases. **Application source is not published.** Copyright © Rakkashin. All rights reserved. Compiled releases are for personal use, provided as is without warranty. Third-party components retain their respective licenses and NOTICE files.

[Back to language selection](#earpiece-eq)

---

<a id="zh"></a>
## 简体中文

Earpiece EQ 可分别调整 Android 设备的听筒侧与底部扬声器。使用外部输出时，可分别调整左、右声道。共用 EQ、低音、音效与 HiFi 级可以与独立处理叠加。本说明适用于 **v0.33-stability**。

### 1. 安装与使用条件

| 功能 | 条件 |
|---|---|
| 应用、界面和设置保存 | Android 10 以上；这些操作本身无需 root |
| 独立 EQ | Android DynamicsProcessing，无需专用模块；是否可应用取决于设备、ROM 和播放器 |
| 共用 EQ、独立/共用低音、处理表、高级音效、内部质量 | root 与专用 AIDL 模块 |
| 音量均衡 / 空间上混 / HiFi | root 与 v0.21 以上 / v0.22 以上 / v0.25 以上模块 |
| 强弱跟随振动 / 波形模式 | root 与 v0.27 以上 / v0.29 以上模块，以及兼容马达和触觉音频输出 |
| 外部设备自动识别 | Android 13 以上媒体路由信息 |
| USB 采样率跟随 | root、Android 14 以上、兼容 USB 输出，以及同一输出上的有效专用 DSP |

**附带模块面向 Android 16、arm64 和 AIDL effect HAL v3。** 不能仅按 SoC 品牌判断兼容性，也不是适用于所有设备的通用模块。当前完整功能请使用附带的 v0.30 模块。

1. 从 Releases 安装 APK。使用相同应用 ID 和签名覆盖更新，可保留已有设置。
2. 打开应用并允许通知。若使用自动启动，也请检查电池优化豁免。
3. 使用 root 功能时，通过 Magisk/KernelSU 安装或更新专用 ZIP，然后重启。也可从“设置”导出附带 ZIP。
4. 授予 root，开启整体音频处理及所需功能。v0.29 波形模式需要同时更新 APK 和模块。

### 2. 整体开关、应用对象与处理模式

- **整体开关**：停止/恢复本应用及专用模块的音频处理与马达输出。输出监视服务和通知可能继续运行。此开关不会关闭其他应用的音效。
- **独立 EQ/低音应用对象与 EQ 编辑对象**：选择编辑哪一侧 EQ，与启用哪一侧独立 EQ/低音是不同设置。两侧应用开关均开启时，可同时使用各自设置。
- **共用处理**：可与独立处理并用。先独立音效，再共用音效；先独立低音，再共用低音，最后应用低音上限。开启共用级不会删除独立设置。
- **传统模式**：独立 EQ 应用于全局混音，即会话 0，无需 DUMP 权限或会话扫描。未设置时默认开启，整体处理默认关闭；保留已保存的开关状态。
- **普通模式**：独立 EQ 按应用音频会话处理，使用会话广播与辅助扫描。支持手动重新扫描和输入会话 ID。专用 root DSP 不要求开启传统模式。
- **方向跟随**：机身输出会根据屏幕方向切换物理左右映射，使设置跟随听筒/底部扬声器。外部输出左右固定。可用测试音确认设备映射。

普通模式的辅助扫描可通过电脑授予 DUMP 权限。设备允许时，也支持通过 root 授权或执行 `dumpsys`。

```sh
adb shell pm grant rakkashin.earpieceeq android.permission.DUMP
```

### 3. 独立 EQ 与共用 EQ

支持 1–32 个频段，每段 **−50 至 +50 dB**，前级增益 **−12 至 +12 dB**。控制点约覆盖 31Hz–20kHz，在对数频率轴上平滑插值。每种频段数量分别记忆增益，切回时恢复对应设置。支持重置为平直。仅使用一个频段时调整整体增益。

独立 EQ 应用于所选输出侧；共用 EQ 使用独立开关与设置作用于两侧，并提供专用共用 EQ 预设。

### 4. 独立低音与共用低音

两个菜单具有相同种类的控制。独立低音分别作用于各侧，共用低音为两侧叠加共同设置。

| 功能 | 作用与设置 |
|---|---|
| 虚拟低音 | 生成谐波，让小扬声器上难以重现的低音更易感知。强度 0–100%，0% 为关闭 |
| 低音厚度 | 提升较弱低音并增加谐波，使声音更饱满，同时减轻超低频负担。0–100%，0% 为关闭 |
| 低音频段调整 | 开启后改变虚拟低音、厚度和自动调整的作用范围。中心 50–180Hz，宽度 1.0–2.5 个八度；关闭后恢复原始范围 |
| 自动低音调整 | 提升较弱低音，较强低音最多降低 12dB。检测目标 −36 至 −12dBFS，最大提升 0–12dB；静音门限用于抑制噪声和静音被放大 |
| 低音峰值上限 | 对 250Hz 以下处理带设置数字峰值上限：−36 至 −3dBFS，步进 0.1dB，可独立开关。不会自动把较弱低音提升至上限 |
| 处理表 | 显示检测到的低音输入、实际自动增益、输出峰值与上限衰减量。也支持仅启用处理表 |

自动调整的“目标”是检测电平，限制器的“上限”是输出约束，作用不同，可以同时使用。

### 5. 独立音效与共用音效

各功能均可独立开关，并分别设置左右侧与共用级。环绕、立体声宽度和低频单声道会参考另一声道，因此设置独立不代表音频信号完全隔离。各音效采用独立实现。

| 功能 | 作用与主要设置 |
|---|---|
| 音量均衡 | 使全频段平均电平向目标靠近，提升较小声音、降低较大声音。强度、平均目标、最大提升/衰减、衰减启动时间、恢复时间、静音门限与峰值上限；强度 0 为旁通 |
| 空间上混 | 利用中央信号与左右差异增加横向宽度和扩散氛围。强度、横向宽度、扩散氛围量、中央聚焦。属于立体声处理，并非多声道解码；强度 0 为旁通 |
| 清晰度 | Natural / OZone+ style / XHiFi style 三种方式，调整临场感或高频谐波与细节。可调强度及 1–10kHz 目标频率 |
| 电子管模拟 | 不对称柔和饱和增加偶次谐波。可调驱动与湿声混合量 |
| 模拟音色 | 柔和饱和与轻微高频衰减。可调驱动与湿声混合量 |
| 频谱扩展 | 合成高次谐波。可调谐波量与 2–12kHz 起始频率；不会恢复录音中缺失的信息 |
| 动态 EQ | 最多 10 个频段。检测电平超过各阈值后向指定增益靠近。各段可调频率、最大动态增益 ±12dB、阈值、启动时间、释放时间和 Q |
| 卷积器 / IRS | 应用脉冲响应的音色或空间特性。选择/清除文件、湿声混合量、IRS 增益 −24 至 +12dB；未选择文件时旁通 |
| 声场环绕 | 通过扩散声增加空间感。可调强度和扩散时间 |
| 差分环绕 | 利用左右时间差增加宽度。可调延迟差与延迟声混合量 |
| 三频段立体声声像 | 低/中/高频分别调整宽度，范围 0–200%；0% 收拢至中央，100% 保留原始宽度，大于 100% 扩宽。可调低/中及中/高分界频率 |
| 耳机环绕 | 使用带限延迟交叉馈送。可调空间量与三级空间大小 |
| 混响 | 左右混响网络。可调湿声混合量、衰减时间、高频衰减和空间大小 |
| 低频单声道 | 将指定低频收拢至中央。可调 40–300Hz 截止频率与混合量 |

音量均衡目标范围 −36 至 −9dBFS，最大提升 0–18dB，最大衰减 0–36dB，与仅作用于低音的自动调整不同。重置音效级会关闭该级音效并恢复默认值，同时保留该级 HiFi 设置。

IRS 支持单/双声道 PCM16/24/32、float32 RIFF/WAVE，以及按 48kHz 单声道 float32 读取的 raw IRS。支持 8–192kHz，最多 65,536 帧，转换至播放采样率后也不得超过上限。为独立侧选择双声道 IRS 时，使用对应侧的声道。

### 6. HiFi

左右独立级与共用级可叠加，各级均可开关和单独重置。

| 设置 | 含义 |
|---|---|
| 输入余量 | 在低音和高级音效之前衰减 0–24dB，为后续处理留出空间 |
| DC 除去 | 减少直流偏置，截止频率 2–30Hz；也会衰减超低频 |
| 极性反转 | 反转波形符号；仅反转一侧可能削弱中央声音 |
| 最终峰值上限 | 限制 DSP 输出采样峰值，范围 −12 至 −0.1dBFS，恢复时间 20–1000ms；共用级联动左右声道 |
| 输出能力参考 | 显示并刷新所选设备报告的支持采样率及 PCM 格式/位深；并非当前实际格式或原文件格式 |

余量衰减位于低音/音效之前。DC 除去→极性→峰值抑制位于处理后段，先独立、再共用。HiFi 不代表恢复缺失信息，采样峰值限制也不等于 True Peak 或实际声压保证。

### 7. 处理质量与 USB 采样率跟随

- **标准 / 2 倍 / 4 倍**：对独立与共用音效中的电子管、模拟音色和谐波处理进行内部过采样，减少混叠噪声。更高质量增加 CPU 负载与处理延迟。
- 内部质量不会强制设备以最大采样率/位深输出，也不会将录音变为真正的高解析度原始录音。
- **USB 采样率跟随（实验，默认关闭）**：尝试让 USB DEFAULT mixer 匹配单个播放器向 Android 提供的 PCM 立体声格式。仅在同一输出上检测到有效专用 DSP 时继续，不使用独占/BIT_PERFECT 模式。
- 对不支持、多音源或已有优先设置等情况不进行更改。无法确认采样率/DSP 时回退；整体关闭或断开连接时也释放请求。若清理仍待完成，请重新打开应用或重新连接 USB。
- **实际 USB DAC 行为与 SRC 绕过尚未验证。** 无法识别或撤销播放器内部、DAC 内部的转换，也不保证系统范围的独占播放。

### 8. 振动与马达专用 EQ

仅用于机身输出，需要开启整体处理和马达输出。功能默认关闭，初始强度 15%；波形模式也默认关闭，不会擅自改变已有行为。启用振动不会主动关闭其他音频功能。

| 模式/设置 | 行为 |
|---|---|
| 强弱跟随 | 将 DSP 低音包络转换为马达振幅，跟随左右较强的一侧。检测截止频率 60–200Hz，强度 1–100%；不重现原始音高 |
| 音频波形模式（试验） | 将 DSP PCM 复制到独立触觉输出会话。保留原始频率，不做八度变换；左右取平均后发送至单个马达 |
| 马达专用 EQ | 波形模式下提供 40/80/160/320/640/1280Hz 六段 EQ，每段 ±12dB、步进 0.5dB；不影响扬声器 EQ，可重置为平直 |
| 额外增强 | 默认关闭；开启后显示额外强度滑块。在原强度上增加 0–200%（最高 3 倍），适用于波形与强弱模式。包含在全设置预设和备份中；仍受硬件上限限制 |
| 强度与保护 | 波形模式去除 DC、抑制混叠并限制数字驱动量。强度、系统媒体/振动设置与设备机械特性共同决定实际振动 |

波形模式中不能编辑原有包络检测截止频率；强弱模式中不能编辑波形 EQ。整体关闭、功能关闭、外部输出或数据过期/中断时停止马达。不支持时显示状态并停止，不会擅自切换至另一方式。完成必要更新后，可关闭再开启功能以重试。

扬声器保持原有全频段路径，附加输出的音频左右声道始终为零。不录音、不切换音频模式、不申请音频焦点，也不关闭其他应用音效。**无法保证相同音色、声压、机械音高重现精度或完全同步。** 不会为同步而延迟扬声器音频。发布时尚未确认新模块安装后实际音乐的听感与同步。

### 9. 预设与备份

| 类型 | 保存/应用范围 |
|---|---|
| 单侧预设 | 正在编辑侧的前级增益、频段数/增益、虚拟低音、厚度、低音上限、频段/自动调整/处理表设置 |
| 共用 EQ 预设 | 共用 EQ 开关、前级增益与频段数/增益 |
| 组合预设 | 当前输出两侧的 EQ/低音及应用开关、共用 EQ/低音、独立/共用音效（含 HiFi）和内部质量 |
| 全设置预设 | 机身和所有已保存外部设备的音频/运行设置，包括整体开关、传统模式、方向、自动启动、USB 跟随、马达模式/强度/EQ |
| 外部设备预设 | 指定设备的音频设置、处理开关和传统模式；列表显示目标名称与短识别码，可选择未连接设备，应用时只影响目标设备 |

命名预设支持保存、加载/应用和删除，同名保存会覆盖。**若要恢复全部设置，请使用“全设置预设”。** 它不会更改实际连接、预设列表、Android 权限或已安装模块。

普通备份支持 JSON 文件/剪贴板、旧 `eq.xml`、已保存预设及 IRS 数据迁移。仅导入不会开启处理，也不会自动应用全设置预设。外部预设支持单个/全部 JSON 文件导入导出，保留设备关联和可用 IRS。仅导入外部预设不会应用音频设置。马达设置包含于全设置预设和普通备份，排除在外部专用预设之外。

### 10. 外部输出与自动保存

返回机身输出，关闭“连接耳机时暂停处理”即可允许外部处理。每台设备分别记忆 EQ、低音、音效、HiFi、质量、整体开关和传统模式；断开后恢复机身设置，重新连接后恢复该设备最后的设置。

新设备以处理关闭、平直设置开始。外部输出允许音频调整和处理/传统模式开关，机身方向、自动启动、马达等运行设置受到限制。输出不明或多输出时暂停处理及相关编辑。没有唯一元数据的设备、同名设备和被动有线耳机可能共用配置。

### 11. 界面、启动与诊断

- 共 12 个菜单：EQ、共用 EQ、低音、共用低音、音效、共用音效、HiFi、振动、质量、预设、设置、状态。
- Material 风格卡片/标签、系统栏显示调整、浅色/深色、大字体和横屏支持；应用图标带黑色边框。
- 日语设备显示日语；所有中文区域显示简体中文；其余语言显示英语。用户设备名和预设名不翻译。
- 快捷设置磁贴切换整体处理。重启/应用更新后的自动启动遵循已保存处理状态及自动启动设置；可检查通知和电池优化。
- 提供单侧 1kHz 普通测试音与附加 EQ 的测试音，帮助确认扬声器映射及处理。已启用的全局处理也会影响普通测试音。
- 状态显示服务、模式、输出、会话、权限、启动/电池设置、已安装模块版本和运行驱动验证状态。
- 支持手动会话应用、重新扫描、获取 `dumpsys` 会话行、显示诊断日志和复制完整日志。

### 12. 电平、延迟、验证与分发

v0.33 在同一 EQ 类型注册了多个实现的设备上，通过 root 辅助明确选择 Android EQ 实现，并在读回验证后启用。没有冲突的设备继续使用公开 Android API。之前的 v0.32 修复 PID 就绪解析、控制文件配对、完成全部设置及读回后再启用，以及快速切换整体开关后的重新初始化。传统模式在播放/输出/引擎事件后进行有限次数的异步检查；若本应用 EQ 位于低音处理之后，则重新排列本应用 EQ。不会持续轮询。扬声器 DSP 算法及模块与 v0.30 相同，更新后仍需实机听音确认。

dBFS 是数字信号电平，不是声压或马达位移。上限、自动调整和音量均衡不能保证所有曲目听感完全一致，也不能修复已有失真。请从较小音量开始；叠加更多级会增加处理量。

低音上限路径约有 3ms 延迟，每个有效音量均衡级约增加 5ms 前瞻，IRS 的每个有效卷积级增加 512 个采样。质量、空间音效、马达和输出缓冲也存在延迟。不保证零延迟，也不保证所有设备、播放器及其他应用音效兼容。

v0.31 新增振动增强，使用独立 PCM/控制测试及界面操作验证。更新 APK 后请重启以加载新版 root 控制器；AIDL 模块与 v0.30 相同。v0.30 修复重复恢复全部设置导致的崩溃，并验证相同格式通知下的 DSP 连续性与停止/启动时的状态清理。验证覆盖 20 套 Java/Android 回归、14 套原生测试、独立 AIDL/FMQ 测试、14 种界面条件，以及通过全设置预设/备份恢复 461 项带类型设置。此前 v0.29 的实机验证中，独立触觉输出运行时已有音乐 DSP 保持有效。本次 v0.30 使用独立测试，未替换正在运行的 APK 或模块。这些结果不证明机械发声/物理同步、实际 USB DAC/SRC 绕过或任意环境下没有缺陷。

此仓库用于说明和已编译版本分发，**不公开应用源代码。** Copyright © Rakkashin. All rights reserved. 编译应用仅供个人使用，按现状提供，不作保证。第三方组件适用各自的许可证与 NOTICE。

[返回语言选择](#earpiece-eq)
