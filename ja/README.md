# エミュレータのチュートリアル (2024/08 作成 Var: 295969 まで有効)
ゲームをプレイするためのチュートリアル

# 必要なもの
- LDPlayer
- VPNサービス (日本以外からダウンロードする場合)
- git (任意)
- SQL Server Express 2022
- SSMS(SQL Server Management Studio 20)
- Microsoft Visual Studio 2022
- Python
- mitmproxy
- WireGuard

> [!TIP]
> SQL Server Express 2022より下は[ここ](https://github.com/2288-256/emulator-tutorial/tree/main/ja#%E5%90%84%E7%A8%AE%E3%82%BD%E3%83%95%E3%83%88%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)で手順を解説しています

  
---
# LDPlayerでの設定
## 1. BlueArchiveのインストール
### 日本からインストールする場合
GooglePlayからブルーアーカイブをダウンロードする

### 日本以外からインストールする場合
[APKPure](https://apkpure.com/)または[Aurora Store](https://f-droid.org/ja/packages/com.aurora.store/)(非推奨)からapkもしくはxapkをダウンロードしダウンロードしたファイルをLDPlayerのウィンドウにドラッグアンドドロップしインストールする

APKPureの場合
https://apkpure.com/blue-archive/com.YostarJP.BlueArchive

Aurora Storeの場合
「Yostar」もしくは「ブルーアーカイブ」で検索する

> [!TIP]
> APKPureからダウンロードする場合は[APKPureアプリ](https://apkpure.com/apkpure/com.apkpure.aegon)を経由してインストールすることをオススメします
> 
> 日本版は約1ヶ月に一度アプリの更新があるため、[APKPureアプリ](https://apkpure.com/apkpure/com.apkpure.aegon)からインストールすることで更新が楽になります。

## 2. BlueArchiveのチュートリアルを終わらせる

インストールしたアプリを起動し、チュートリアルを終わらせる
チュートリアルの終わりは無料でガチャを引き、モモトークのチュートリアルが終わったところまでを指します。


> [!CAUTION]
> **注意事項が多いです。すべて読んでからゲームを起動してください。**


---


> [!CAUTION]
> 日本以外から起動する場合は予期せぬエラーを防ぐためVPNを使用することをオススメします。

> [!WARNING]
> LDPlayerの設定で「Root権限」をオフにしない場合は「不明なアプリが検出されたため、終了します。」とポップアップが出て起動できないので必ずオフにすること
> 
> <img src="https://github.com/2288-256/emulator-tutorial/blob/main/img/ja/root-setting.png?raw=true" width="400px">

起動後、ログイン方法の選択では「GUEST」を選択してください。

> [!TIP]
> Yostar ID連携は必須ではありません。
> 
> 右上のXを押してスキップすることができます。

> [!CAUTION]
> 予期せぬエラーを防ぐためアカウント名入力後のウィンドウは何も入力せずにOKを押してください。

> [!WARNING]
> データのダウンロードは約6000MBの方を選択し、OKを押してください。

## 3. Magiskのインストール
この先、Rootを有効にした状態でアプリを起動しないといけないためMagiskをインストールします

> [!TIP]
> ここのセクションは現在制作中です
> 
> 完成するまでは以下の動画を見てインストールしてください
> 
> https://www.youtube.com/watch?v=h9gJtS7mLCE

インストール後は設定にある「Magiskアプリを隠す」を選択して設定してください。
アプリ名は変更せずにOKを押してください

---

# 各種ソフトのインストール

以下のソフトをインストールしてください

## SQL Server Express 2022
Google等でSQL Server Express 2022を検索し、SQL Server Express **2022**をインストールしてください。
インストールする際は「BASIC」を選択してください
その他は変更する必要はありません

## SSMS(SQL Server Management Studio 20)
Google等でSQL Server Express 2022を検索し、インストールしてください
特に何も変更せず、「SSMSをインストール」を押してください

## Microsoft Visual Studio 2022
Google等でMicrosoft Visual Studio 2022と検索し、インストールしてください。
> [!TIP]
> 以下のようにしてください
> 
> <img src="https://github.com/2288-256/emulator-tutorial/blob/main/img/ja/Visual_Studio_1.png?raw=true" width="400px">
> 
> <img src="https://github.com/2288-256/emulator-tutorial/blob/main/img/ja/Visual_Studio_2.png?raw=true" width="400px">

## mitmproxyのインストールとCA証明書のインストール

### mitmproxyのインストール

[mitmproxy.org](https://mitmproxy.org) からインストーラーを入手し、インストールしてください。

> [!TIP]
> インストール時に変更しないといけない箇所はありません。

### CA証明書のインストール

PowerShellを開き、以下のコマンドを実行してください

```
cd ~/.mitmproxy/
$hashed_name = & openssl x509 -inform PEM -subject_hash_old -in mitmproxy-ca-cert.cer | Select-Object -First 1
Copy-Item mitmproxy-ca-cert.cer "$hashed_name.0"
```

生成された`[8桁の英数字].0`をLDPlayerのadb.exeがあるフォルダにコピーしてください。

> [!TIP]
> デフォルトのパスは`C:\LDPlayer\LDPlayer9\`です

LDPlayerのadb.exeがあるフォルダでコマンドプロンプトを開き以下のコマンドを入力してください

```
adb root
adb remount
adb push [8桁の英数字].0 /system/etc/security/cacerts/
adb shell chmod 664 /system/etc/security/cacerts/[8桁の英数字].0
```

正常に実行されたら**手動で**LDPlayerを再起動してください。

その後、設定 -> セキュリティと現在地情報 -> 詳細設定 -> 暗号化と認証情報 -> 信頼できる認証情報 でmitmproxyがあることを確認してください。

## WireGuardのインストールと設定

WireGuardをGooglePlayまたは、信頼できるapkからインストールし、WireGuardを起動してください。

PCのコマンドプロンプトを起動し以下のコマンドを実行してください

```
mitmweb -m wireguard --no-http2 --set termlog_verbosity=warn
```
実行すると自動的にブラウザが起動するのでWireGuardの右下にある+ボタンを押し、QRコードをスキャンを選択し、スキャンしてください。

> [!TIP]
> カメラのアクセス権限は「一定間隔でキャプチャー」を選択すると読み込みやすいです

トンネル名は何でも構いません。

# GameServerのビルドと初期設定

## GameServerのビルド

信頼できるRepositoryからソースコードをダウンロードし、解凍してください

解凍したフォルダの中にあるGameServerで終わるフォルダに移動し、.slnのファイルがあることを確認し、このパスでコマンドプロンプトを開き以下のコマンドを入力してください。

```
dotnet publish -a x64 --use-current-runtime --self-contained false -p:InvariantGlobalization=false
```
実行が完了すると、`bin\Release\net8.0\win-x64\`にGameServer.exeで終わるファイルがあるはずです。

## 初期設定
RepositoryのRootに移動し、Scriptsフォルダに移動して、redirect_server_mitmproxyフォルダを開きます。
中にある、redirect_server.pyを開き、以下の

```
SERVER_HOST = 'YOUR_SERVER_HERE'
```
のYOUR_SERVER_HEREをPCのプライベートIPアドレスに置き換えます。

# 起動手順
## SSMSの起動
Windowsキーを押して、SSMSと検索し、「SQL Server Management Studio 20」を実行します。

Connection SecurityのTrust server certificateにチェックが付いていることを確認し、Connectを押します。

## LDPlayerを起動する
LDPlayerを開いていても一度終了するか再起動し、以下の手順に従って設定してください。

起動後、何も他のアプリを起動せずにMagisk(setting)を起動し、設定 -> Configure SuList を選択し、すべてのチェックを外してください。

## GameServerを起動する
RepositoryのRootに移動し、GameServerで終わるフォルダ、`bin\Release\net8.0\win-x64\`と移動し、GameServer.exeで終わるファイルを新たなコマンドプロンプトで起動します。

ログに

```
Now listening on: http://0.0.0.0:80
Application started. Press Ctrl+C to shut down.
```
と表示されれば成功です

> [!TIP]
> 他のエラーが発生した場合はFAQをご覧ください

## mitmproxyの起動
RepositoryのRootに移動し、Scriptsフォルダに移動して、redirect_server_mitmproxyフォルダを開きます。
このパスでコマンドプロンプトを開き以下のコマンドを入力してください
```
mitmweb -m wireguard --no-http2 -s redirect_server.py --set termlog_verbosity=warn --ignore [Your local IP address]
```

> [!TIP]
> サークルのチャットを利用可能にする方法を発見した [DennouNeko](https://github.com/DennouNeko) に感謝します。

自動的にブラウザが起動するので起動したらWireGuardを起動し、作成したトンネルの右にあるトグルボタンをオンにします。
ブラウザに戻ると表示されていたQRコードがなくなっているはずです。

>[!CAUTION]
> WireGuardは終了させずに上部に残すようにしてください。

## ゲームの起動
ゲームを起動してください。

タイトルコールが聞こえたらGameServerを起動したコマンドプロンプトを見て、
```
HTTP POST /app/getCode responded 200 in ***ms
HTTP POST /app/getSettings responded 200 in ***ms
```
が表示されていれば成功です。

> [!WARNING]
> 表示されていない場合はmitmproxyが正常に動作していない場合があります。
> 再度、やり直してください

---

### 初回起動の場合

初回起動時はアカウントの作成を再度行う必要があります。
以下の手順に従ってください。

画面をクリックしゲームを開始する
↓
> UID_OR_TOKEN_INVALIDCERT
> (Verification failed)
の下にあるOKを押す
↓
再度クリックする

両方のチェックボックスにチェックを入れ、OKを押す

初めまして！
新しいアカウントを作成しましょう。
の下にあるOKを押す

好きな名前を入れてOKを押す

OKを押した後のウィンドウは何も入力せずにOKを押す
> [!WARNING]
> 入力してOKを押すとエラーが発生する場合があります。

OKの後のウィンドウはOKを押す

お疲れ様でした。
これで、起動完了です。

次回以降の起動は起動手順から実行すると起動できるはずです。

# その他の情報

FAQ (制作中)
サークルのコマンド集 (制作中)
