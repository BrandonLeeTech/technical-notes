# 自動化框架

---

> 介紹如何下載並設置必要的工具和軟體，確保編寫自動化測試腳本的環境配置完備
> 

## Appium 原理

<aside>
💡

Def :
Appium Server 透過 Webdriver 協議來驅動 Android 系統的 UI Automator 框架

Appium 啟動一個 Server (預設 port: 4723) 用來接口 Client 端的 rest 請求

</aside>

![](../images/external/aa541f24e887.png)

## 安裝項目

---

- Python： 適合編寫自動化測試腳本
- JDK： Appium 和 Android 模擬器需要 Java 環境來運行
- Node.js： Node.js 提供了 Appium 的執行環境，同時支持 npm 來管理和安裝
- Android SDK： 開發 Android 應用所需的工具 & 可以設置 Android 模擬器
- Appium Server： 用於運行自動化測試腳本，並與設備進行交互
- Appium Inspector： 幫助測試人員查看應用中的元素屬性的 GUI 工具
- Appium-Python-Client：用於讓 Python 和 Appium Server 進行通信。

## 安裝步驟

---

### VSCode

> 選一個文字編輯器或是 IDE 用來撰寫腳本
> 

#### 1. 下載 [VSCode](https://code.visualstudio.com/download)

照畫面指示步驟下載即可

下載完後進【延伸模組】安裝 Python

![](../images/external/d1e522d63d9e.png)

#### 2. 編輯第一個文件

點擊 File 裡的 New Text File 新增一個文本

![](../images/external/945ada180d5f.png)

新增完後先 Ctrl+S 儲存，並改附檔名為 .py

![](../images/external/5615cae5601c.png)

在第一行輸入在 Python 內導入 Appium 的語法:

`from appium import webdriver`

![](../images/external/aa84f50261d7.png)

此時程式應該會報錯，必須安裝 Python 相關軟體與套件

### Java (JDK)

> 為了運行 Appium 和 Android 模擬器，首先需要安裝 JDK。
> 

#### 1. 下載 [Java SDK](https://www.oracle.com/java/technologies/downloads/)

建議下載 JDK17 或 JDK21

每3年就會有一個長期支援（LTS；Long-term support）的版本釋出

![](../images/external/04782f108b35.png)

開啟執行檔後點擊下一步 並安裝在預設路徑

![](../images/external/4a2d07817e49.png)

預設為 C:Files(我之前安裝了兩個版本)

![](../images/external/367f8290434f.png)

#### 2. 設置環境變數

點擊 windows+R 開啟對話框並輸入 『sysdm.cpl』

![](../images/external/03bff3544f1b.png)

點擊進階 點擊環境變數

![](../images/external/36249b0e1100.png)

在系統變數裡新增環境變數

![](../images/external/53b67a0ce2d2.png)

變數名稱 輸入『JAVA_HOME』

變數值 輸入剛剛預設下載路徑 (e.g.C:Files)

![](../images/external/97c8560776f6.png)

編輯Path變數

新增剛剛的 JDK 路徑的下一層 bin 資料夾到 path 變數

C:Files
(我的window排版跑掉 這是Rapid Environment Editor的畫面)

![](../images/external/7b16ad28a2b7.png)

![](../images/external/f3a4d7d9e84c.png)

Path 變數設定完成，接下來點選『確定』就設定完成 Java 開發環境

### Node.js

> Appium 需要依賴 Node.js。
> 

#### 1. 下載與安裝 [Node js](https://nodejs.org/en/)

建議選擇最新的 LTS 版本安裝 如圖片中的 v20.17.0(LTS)

![](../images/external/fa2f25c14b74.png)

預設安裝路徑 勾選安裝必要的工具

Chocolatey：Windows 的套件管理工具，它簡化了 Python 和其他開發工具的安裝和管理。

![](../images/external/8f4b9eb51202.png)

![](../images/external/9bc5b4ebf6b5.png)

![](../images/external/2693f52935c4.png)

![](../images/external/054ef92d95b4.png)

#### 2. 設置環境變數

安裝完成會自動添加 node.js 及 npm 的路徑到環境

可以點擊 windows 輸入 『cmd』 叫出命令提示字元

輸入 `echo %PATH:;=&echo.%` 查看是否有 npm 跟 node

npm 是 node 的套件管理系統會預設安裝

![](../images/external/6362e72da959.png)

#### 3. 驗證是否可運行

在 cmd 中分別輸入 `node -v` 和 `npm -v` 確認版本

檢查是否可以正確運行

![](../images/external/8e36f0153c78.png)

如果 cmd 回傳 `'node'不是內部或外部...`之類的訊息

檢查環境變數是否正確依圖片步驟至 path 變數添加 node.js 及 npm 的路徑

或是可能是封包時資料遺漏可以至 step1 重新下載

![](../images/external/6f7c6fa20af9.png)

![](../images/external/514416e7016e.png)

![](../images/external/15b320e0d29f.png)

### Android SDK

> 開發和測試 Android 應用所需的工具和資源。
> 

#### 1. 下載與安裝 [Android Studio](https://developer.android.com/studio?hl=zh-tw)

建議安裝 如果習慣 cli 操作或希望保持輕量級環境可以略過

![](../images/external/684732ad65c7.png)

![](../images/external/f19cb9f77171.png)

預設安裝路徑 C:FilesStudio

如果需要大量開發則不建議安裝在系統槽( e.g.更改路徑為D槽 ) 易造成系統槽過載

![](../images/external/63ce3363c598.png)

![](../images/external/2db313bfbf11.png)

在安裝過程中，選擇安裝 Android SDK 和 SDK 管理工具（SDK Manager），確保這些工具與 Android Studio 一起安裝。

![](../images/external/17edc1dc397b.png)

如果有需要虛擬機，可以勾選

![](../images/external/2f20ea8ec586.png)

#### 2. 下載SDK Tool

下載完打開 Android studio

可以新增新的 project

![](../images/external/6e3efaefb25d.png)

頁面的右上跟左上可以找到 setting 或是 SDK Manager

![](../images/external/4eaad2ff5224.png)

配置需要的 Android OS 版本

![](../images/external/af563545a7ae.png)

配置 SDK Tool

* Android SDK Build-Tools：用於編譯 Android 應用的核心工具。

* Android SDK Platform-Tools：包含 adb 和 fastboot，這些是測試和管理 Android 設備的核心工具。

* Android Emulator（如果你需要使用模擬器）。

* Android SDK Tools：SDK 管理工具集，包含調試和開發的基本工具。

![](../images/external/35625905f6ec.png)

#### 3. 設置環境變數

新增 Android SDK 的安裝目錄到系統變量

創建一個名為 ANDROID_HOME 的變量，其值設置為 Android SDK 的安裝目錄。通常這個路徑類似於：

![](../images/external/63331b63e027.png)

![](../images/external/8adc42593a6f.png)

添加下載的 Tool 路徑到 path 變數裡

![](../images/external/0960bdea48e5.png)

#### 4. 驗證是否可運行

在任何命令提示字元( e.g.cmd )輸入 `adb devices`

回傳如圖表示adb工具已正確安裝

![](../images/external/0cd248b3bddd.png)

如果用傳輸線接上 Android 實體機 並打開發者模式的ADB除錯則會回傳
連線的 device name

![](../images/external/217710c6db37.png)

#### 5. 檢查

用 npm 安裝 appium-doctor

可以用appium-doctor指令快速檢查appium的環境設定問題

`npm install -g appium-doctor`

![](../images/external/a684a0351fda.png)

第一段necessary(必要的)不能有X

### Appium Server

> 介紹如何通過 npm 安裝 Appium。
> 

#### 1. 下載與安裝 [Appium Server GUI](https://github.com/appium/appium-desktop/releases/tag/v1.6.3)

如果不用 GUI 可以略過

![](../images/external/71a9a334ff9b.png)

![](../images/external/518f169f72f3.png)

![](../images/external/e35d155cc38d.png)

也可透過 npm 安裝 Appium

在任何命令提示字元( e.g.cmd )輸入 `npm install -g appium`

![](../images/external/31d7e4d16bdf.png)

#### 2. 驗證是否可運行

輸入 `appium -v` 確認版本

檢查是否可以正確運行

![](../images/external/cca12de86294.png)

#### 3-1. 配置端口資訊

Appium Desktop: 打開下載的 GUI 介面

![](../images/external/33dccfdac798.png)

可以在 Advanced 中設置伺服器詳細訊息、端口號、日誌級別、CORS設誌等

![](../images/external/33823a7666ff.png)

* - [ ] Server Address (伺服器地址)：

默認值是 0.0.0.0 相當於本機 IP 127.0.0.1 或 Localhost，維持預設即可。

* - [ ] Server Port (伺服器端口)：

默認值是 4723，你可以根據需要更改這個端口，目前維持預設即可。

* - [ ] Log Level (日誌級別)：

默認設置為 debug，用於在控制台中顯示詳細的日誌。你可以根據需要將其設置為 info、error 或 warn，以減少日誌輸出。

* - [X] Override Temp Path：

默認值 /wd/hub，這個設置定義了 Appium 伺服器的 WebDriver 端點。大多數情況下，一般情況保持默認值即可，如果遇到問題可以加上 /wd/hub。

* - [X] Relaxed Security：

勾選「Relaxed Security」允許 Appium 忽略一些安全限制，這通常在測試環境中使用，允許更多命令執行（如文件上傳等）。

* - [X] Allow CORS：

允許跨源資源共享（CORS），如果需要從不同的來源訪問 Appium 伺服器，這個選項應該保持勾選。

![](../images/external/b43ca361c976.png)

設置完點擊 “Save As Preset” 之後方便直接從 Presets 選單調用設置

#### 3-2. 配置端口資訊

cmd: 直接輸入`appium`

會啟動appium伺服器 並綁定到127.0.0.1:4723（本地主機）和默認端口 4723

![](../images/external/9d947d49cd40.png)

### Appium Inspector

> 介紹如何安裝 Appium Inspector 工具來檢測 UI 元素。                                                       Appium Desktop 升級到 1.22 版本後，Server 跟 Inspector 已經分開
> 

#### 1. 下載與安裝 [Appium Inspector](https://github.com/appium/appium-inspector/releases)

Windows 系統可下載最新版本的.exe檔

![](../images/external/c30cf8586674.png)

![](../images/external/a8595d37e127.png)

![](../images/external/d46cc2798b33.png)

#### 2. Appium Inspector 配置

![](../images/external/442a4f5744ab.png)

可以如圖片中配置 Capability 並保存到 preset

![](../images/external/de7a8bcc75c5.png)

上面四項 告訴Appium 測試在哪個平台和設備上運行

```
* platfromName
* deviceName
* platformVersion
* automationName (選填)
```

中間三項 是測試 apk 檔的路徑 跟 app 的相關包名

可以反編譯 apk 檔或是用 adb 指令查看

appPackage 跟 appActivity 在 Appium2.0 版本後為了提升命名的辨識度 必須在這兩項前面加上前綴 告訴 appium 這兩項是 Android-specific 的 capabilities

```
* app
* appPackage
* appActivity
```

下面兩項 是 Session Capabilities 告訴 Appium 在啟動應用或進行測試時的行為

例如最多等待秒數、啟動時 Reset 等

```
* newCommandTimeout
* noReset
```

### Appium_Python_Client

> Appium 與腳本互動時 必須調用 Client 端和 Appium Server 進行通信(e.g.Appium_Python_Client)  而 Appium_Python_Client 用 pip 下載最不容易出錯
> 

#### 1. 下載 [Python](https://www.python.org/downloads/)

![](../images/external/0aeb57c813a7.png)

![](../images/external/140d2551f6ea.png)

確認 pip 這格要勾選(通常下載 python 預設也會下載 pip)

![](../images/external/0f85be0fd6f5.png)

安裝資料夾路徑選擇預設，如果有更改要額外確認環境變數

![](../images/external/1c06993aa723.png)

這邊我是設置在 C:

![](../images/external/8235e7c4e889.png)

我的 pip 放在 C:裡，所以 path 變數要加上這個路徑

#### 2. 設置 VSCode 裡的直譯器

回到 VSCode

點擊 Ctrl+Shift+P 在命令面板輸入 python 選擇剛剛下載直譯器

![](../images/external/37add5823061.png)

這會影響腳本是用哪個 Python 版本做轉碼

python 是直譯式語言，編輯的每一行轉成電腦可理解的 Byte Code 去執行，這也會導致輸出結果較慢

#### 3. 下載 Appium-Python-Client

cmd 輸入 `pip -V`

![](../images/external/07510dc6afbd.png)

確認 pip 是否安裝成功

輸入 `pip install Appium-Python-Client`

除了安裝Appium-Python-Client-2.11.0版外，也一併安裝了Selenium-4.23.1版。

![](../images/external/124d34c0fe99.png)

![](../images/external/c4cee62cd16c.png)

#### 4. 檢查 VSCode 有無報錯

回到VSCode

點擊 Ctrl+Shift+P 在命令面板輸入 Reload

![](../images/external/3fe6372ed2f1.png)

重整後應該無報錯

![](../images/external/d6daf8f13b3c.png)