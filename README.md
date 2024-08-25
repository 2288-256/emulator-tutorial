# emulator-tutorial
Tutorial to be able to play the game
> [!TIP]
> 日本語版(原文)は[こちら](https://github.com/2288-256/emulator-tutorial/tree/main/ja)にあります。
> 
> The Japanese version (original text) can be found [here](https://github.com/2288-256/emulator-tutorial/tree/main/ja).

# Required Items
- LDPlayer
- VPN service (if downloading from outside Japan)
- git (optional)
- SQL Server Express 2022
- SSMS (SQL Server Management Studio 20)
- Microsoft Visual Studio 2022
- Python
- mitmproxy
- WireGuard

> [!TIP]  
> Instructions for items below SQL Server Express 2022 are explained [here](https://github.com/2288-256/emulator-tutorial/blob/main/README.md#installation-of-various-software). 

# Settings for LDPlayer
## 1. Installing Blue Archive
### If installing from Japan
Download Blue Archive from Google Play.

### If installing from outside Japan
Download the APK or XAPK file from [APKPure](https://apkpure.com/) or [Aurora Store](https://f-droid.org/ja/packages/com.aurora.store/) (not recommended). Drag and drop the downloaded file into the LDPlayer window to install.

For APKPure:
https://apkpure.com/blue-archive/com.YostarJP.BlueArchive

For Aurora Store:
Search for "Yostar" or "Blue Archive".

> [!TIP]  
> If downloading from APKPure, it is recommended to install via the [APKPure app](https://apkpure.com/apkpure/com.apkpure.aegon).
>
> The Japanese version of the app typically receives updates about once a month, so using the [APKPure app](https://apkpure.com/apkpure/com.apkpure.aegon) will make updating easier.

## 2. Complete the Blue Archive Tutorial

Launch the installed app and complete the tutorial. The end of the tutorial is defined as drawing a free gacha and finishing the Momo Talk tutorial.

> [!CAUTION]  
> **There are many important notes. Please read everything before starting the game.**

---

> [!CAUTION]  
> If launching the game from outside Japan, it is recommended to use a VPN to prevent unexpected errors.

> [!WARNING]  
> If you do not turn off "Root Permission" in LDPlayer settings, a popup will appear saying "An unknown app has been detected. The app will now exit," preventing the game from launching. Make sure to turn off this setting.  
>  
> <img src="https://github.com/2288-256/emulator-tutorial/blob/main/img/en/root-setting.png?raw=true" width="400px">

After launching, choose "GUEST" for the login method.

> [!TIP]  
> Linking a Yostar ID is not required.  
>  
> You can skip this step by clicking the X at the top right.

> [!CAUTION]  
> To avoid unexpected errors, please do not enter anything in the window that appears after entering the account name and press OK.

> [!WARNING]  
> For the data download, select the option that is approximately 6000MB and press OK.

## 3. Installing Magisk
Since you need to launch the app with Root enabled, you will need to install Magisk.

> [!TIP]  
> This section is currently under development.  
>  
> Please refer to the following video for installation instructions until this section is completed:  
>  
> https://www.youtube.com/watch?v=h9gJtS7mLCE

After installation, go to settings and select "Hide the Magisk app" to configure it. Press OK without changing the app name.

---

# Installation of Various Software

Please install the following software:

## SQL Server Express 2022
Search for SQL Server Express 2022 on Google or other search engines, and install SQL Server Express **2022**.  
When installing, select "BASIC" as the installation type. No other changes are necessary.

## SSMS (SQL Server Management Studio 20)
Search for SQL Server Express 2022 on Google or other search engines, and install it.  
Simply press "Install SSMS" without making any additional changes.

## Microsoft Visual Studio 2022
Search for Microsoft Visual Studio 2022 on Google or other search engines and install it.

> [!TIP]  
> Since I couldn't provide the English version, it might be a bit difficult to follow, but please check the checkboxes based on the following images:
> 
> <img src="https://2288-256.github.io/emulator-tutorial/img/ja/Visual_Studio_1.png" width="400px">
> 
> <img src="https://2288-256.github.io/emulator-tutorial/img/ja/Visual_Studio_2.png" width="400px">


## Installing mitmproxy and CA Certificate

### Installing mitmproxy

Obtain the installer from [mitmproxy.org](https://mitmproxy.org) and install it.

> [!TIP]  
> There are no specific settings that need to be changed during the installation.

### Installing the CA Certificate

Open PowerShell and run the following commands:

```powershell
cd ~/.mitmproxy/
$hashed_name = & openssl x509 -inform PEM -subject_hash_old -in mitmproxy-ca-cert.cer | Select-Object -First 1
Copy-Item mitmproxy-ca-cert.cer "$hashed_name.0"
```

Copy the generated `[8-character alphanumeric].0` file to the folder where LDPlayer's `adb.exe` is located.

> [!TIP]  
> The default path is `C:\LDPlayer\LDPlayer9\`.

Open a command prompt in the folder where `adb.exe` is located and enter the following commands:

```shell
adb root
adb remount
adb push [8-character alphanumeric].0 /system/etc/security/cacerts/
adb shell chmod 664 /system/etc/security/cacerts/[8-character alphanumeric].0
```

If executed successfully, **manually** restart LDPlayer.

After that, go to Settings -> Security & Location -> Advanced -> Encryption & credentials -> Trusted credentials and ensure that mitmproxy is listed there.

## Installing and Configuring WireGuard

Install WireGuard from Google Play or a trusted APK source, and launch WireGuard.

Open the command prompt on your PC and execute the following commands:

```
mitmweb -m wireguard --no-http2 --set termlog_verbosity=warn
```

When you execute the commands, your browser will automatically open. In WireGuard, press the + button in the bottom right corner, select "Scan QR code," and scan the QR code.

> [!TIP]  
> For easier reading, select "Capture Screen" for camera access permissions.

The tunnel name can be anything you like.

# Building and Initial Configuration of the GameServer

## Building the GameServer

Download the source code from a trusted repository and unzip it.

Navigate to the folder ending with GameServer within the extracted folder, confirm that there is a .sln file, and open a command prompt in this directory. Then, enter the following commands:

```
dotnet publish -a x64 --use-current-runtime --self-contained false -p:InvariantGlobalization=false
```

Once the execution is complete, you should find a file ending with `GameServer.exe` in the `bin\Release\net8.0\win-x64\` directory.

## Initial Configuration

Navigate to the Root of the repository, go to the Scripts folder, and open the `redirect_server_mitmproxy` folder. 

Open `redirect_server.py` and replace the following line:

```
SERVER_HOST = 'YOUR_SERVER_HERE'
```

with your PC's private IP address.

# Startup Procedure

## Launching SSMS

Press the Windows key, search for SSMS, and run "SQL Server Management Studio 20".

Ensure that the "Trust server certificate" checkbox is checked under Connection Security, and then press Connect.

## Launch LDPlayer

If LDPlayer is already open, either close it or restart it, and follow the steps below for configuration.

After launching, without starting any other apps, open Magisk (settings), go to Settings -> Configure SuList, and uncheck all options.

## Launch the GameServer

Navigate to the Root of the repository, go to the GameServer folder, and then to `bin\Release\net8.0\win-x64\`. Start the file ending with `GameServer.exe` in a new command prompt.

If the log displays:

```
Now listening on: http://0.0.0.0:80
Application started. Press Ctrl+C to shut down.
```

then the process is successful.

> [!TIP]  
> If you encounter other errors, please refer to the FAQ.

## Launch mitmproxy

Navigate to the Root of the repository, go to the Scripts folder, and open the `redirect_server_mitmproxy` folder. Open a command prompt in this directory and enter the following command:

```
mitmweb -m wireguard --no-http2 -s redirect_server.py --set termlog_verbosity=warn --ignore [Your local IP address]
```

> [!TIP]  
> Special thanks to [DennouNeko](https://github.com/DennouNeko) for discovering how to enable club chat.

A browser window will automatically open. Once it opens, start WireGuard and turn on the toggle button next to the created tunnel. The QR code displayed in the browser should disappear.

> [!CAUTION]  
> Do not close WireGuard; keep it running in the background at the top of the screen.

## Launching the Game

Start the game.

When you hear the title call, check the command prompt where you launched GameServer. If you see:

```
HTTP POST /app/getCode responded 200 in ***ms
HTTP POST /app/getSettings responded 200 in ***ms
```

then it is successful.

> [!WARNING]  
> If these messages are not displayed, mitmproxy may not be functioning correctly. Please try again.

### Initial Launch

During the initial launch, you will need to create an account again. Follow these steps:

1. Click on the screen to start the game.
2. Press OK below:
   ```
   UID_OR_TOKEN_INVALIDCERT
   (Verification failed)
   ```
3. Click again.

4. Check both checkboxes and press OK.

5. Press OK under:
   ```
   初めまして！
   新しいアカウントを作成しましょう。
   ```

6. Enter your preferred name and press OK.

7. In the window that appears after pressing OK, do not enter anything and simply press OK.
> [!WARNING]
> Entering something and pressing OK may cause an error.

8. In the window that appears after pressing OK, press OK.

Congratulations! The setup is complete.

For subsequent launches, you should be able to follow the [startup procedure](https://github.com/2288-256/emulator-tutorial/blob/main/README.md#startup-procedure) to begin the game.

# Additional Information

FAQ (In Progress)  
Circle Command List (In Progress)

<br><br>
This translation was created using OpenAI's ChatGPT 4o mini.
