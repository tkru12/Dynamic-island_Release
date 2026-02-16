# 🏝️ Dynamic Island v1.0.0

iPhone14 Pro以降に搭載されている、Dynamic Islandを再現しています。
外部プログラムとのプロセス連携により、OSレベルでのオーディオダッキング（他アプリの減衰）を実現しています。

---

## 🚀 主な機能

- **ポモドーロタイマー**: デスクトップの最前面に常駐し、集中/休憩を視覚的に管理。
- **ミュージック**: OS上で流れているミュージックを操作。
- **スマート・ロック**: 通常のドラッグを禁止し、`Ctrl + ドラッグ`時のみ移動を許可。
- **設定の永続化**: スライダーやチェックボックスの状態を次回起動時も保持。

---

## 主なDynamic-islandの状態
![閉じているとき](/images/close.png "閉じているとき。主な状態")

![開いているとき](/images/open.png "開いたとき。主な状態")

![タイマー時](/images/timer.png "ポモドーロタイマー時。右クリック時")

## 🛠️ 技術仕様

### 1. 外部プロセスによる音量制御
`NAudio`を組み込んだ外部EXEに対し、インバリアントカルチャを用いた文字列引数を渡して実行します。

```
// Doubleを"0.0"形式のStringに変換して引数に指定
string volumeArg = (DuckingVolumeSlider.Value / 100.0).ToString("0.0", System.Globalization.CultureInfo.InvariantCulture);

Process.Start(new ProcessStartInfo
{
    FileName = @"VolumeController\VolumeController.exe",
    Arguments = volumeArg,
    CreateNoWindow = true,
    UseShellExecute = false
});
```

### 2. オーディオ再生の排他制御
SoundPlayerの仕様（単一スレッドでの同時再生制限）を考慮し、アラーム再生前にチクタク音を明示的に停止させるロジックを組んでいます。

```
if (_pomodoroRemainingSec <= 0)
{
    _tickPlayer.Stop(); // チクタクを止めてリソースを解放
    _alarm1Player.Play(); // 終了アラームを優先再生
}
```
### 3. 設定の保存 (Casting & Persistence)
double型のスライダー値を、設定ファイルに適したint型へキャストして保存します。

```
// doubleからintへの明示的キャスト
Properties.Settings.Default.DuckingVolume = (int)DuckingVolumeSlider.Value;
Properties.Settings.Default.Save();
```

## 📂 ディレクトリ構造
```
📂/
|-- DynamicIsland.exe         # アプリ本体
|-- music_info/               # ミュージック情報取得用exeアプリ 
|-- media_controller/         # メディアコントローラーexeアプリ
|-- VolumeController/         # ボリューム設定exeアプリ
|-- resource/
|   |-- ~~~~.png              # 画像ファイル
|   |__ ~~~~.wav              # 効果音など
|__ README.md                 # 本ドキュメント
----------(以下は、自動で作成＆編集＆修復されるファイルです)----------
|-- artwork.png               # アートワークの画像ファイル
|__ media_info.json           # ミュージックの詳細を保存しているファイル
```
## ⚠️ 注意事項
artwork.pngやmedia_info.json以外のファイルとの位置関係がズレてしまうとプログラムが正常に起動しない、かつ、破損する可能性がございますのでご注意ください！
artwork.png、media_info.jsonは、内容が書き換わってしまったり、削除されたりしてしまっても、動作に支障はございません。

#### 島を移動させたい場合は、Ctrlキーを押しながらドラッグしてください。

## このリポジトリの詳細
このリポジトリには、ソースコードは含まれていません。
このアプリを**逆コンパイルなど**を**禁止**いたします。また、**再配布**も**禁止**です。

