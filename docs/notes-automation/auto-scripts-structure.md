# 腳本架構

> 介紹自動化測試腳本的架構

---

1. Data-driven testing
    - 將測試需要的數據用 XLSX 檔導入，例如: HTC 測試、電話號碼等
    - 將元素資訊用 XLSX 檔導入，例如: "開始”按鈕的 id 值
2. 操作模組化
    - 將 App 的頁面操作及邏輯操作模組化，例如: 點擊、生成隨機身分證
3. 頁面模組化
    - 以頁面區分腳本，在主測試流程時調用參與 UI 測試的頁面


```markdown
### 📁 腳本架構（API 測試）

| 資料夾 / 檔案         | 說明 |
|----------------------|------|
| `api/employer/`      | 商家端 API |
| `api/labor/`         | 打工端 API |
| `api/test/`          | API 測試互動邏輯 |
| `tools/`             | 工具類（Socket 傳遞、Header 處理） |
| `env.py`             | API 環境變數 |
| `start_server.py`    | 模擬資料伺服器 |
| `test_data.py`       | 測試資料（如註冊電話） |
```



## 範本

---

### step0. 根目錄

下載源代碼到 Windows 本機端 C 槽

我的專案命名為 Worky_UITest 

![images/腳本架構_1.png](../images/腳本架構_1.png)

### step1. 數據驅動

根目錄下方的 Scripts.xlsx 負責記錄測試按鈕的元素值 (這個檔案下的定位方式僅為記錄用)

例如：我將 打工端 onboarding 頁面的下一頁記錄在 Scripts.xlsx 第4列

Name 欄位命名為“l1_下一頁” 對應的 wait_id 欄位為 “tw.com.worky.labor.and:id/iv_next”

![images/腳本架構_1.png](../images/腳本架構_2.png)

![images/腳本架構_1.png](../images/腳本架構_3.png)

我在執行 onboarding 頁面測試時如果要點擊這個按鈕 

1. 先將 Scripts.xlsx 整個檔案導入頁面測試腳本 
2. 調用定義好的點擊方法，將腳本的 Name 欄位的值當作變數加入 click_by_locating 函數中

![images/腳本架構_1.png](../images/腳本架構_4.png)


<aside>
💡
FAQ：欄位的命名格式 

</aside>

> Q1: 為甚麼 Name 的欄位要這樣設計?
> 

> A: 因為腳本讀取的是 `Name` 欄位，所以必須設計為唯一值，可以將哪個 page 調用當成前綴
> 

> Q2: 其他的欄位怎麼設計?
> 

> A: 除了 `Name` 跟 `wait_id` 受格式規範，其他皆為自定義，功能僅為方便查詢，可用頁面模組名稱來命名
> 

> Q3: 黃色的資料格是甚麼?
> 

> A: 黃色的值是整個 `wait_id` 行被重新定義的值。目前認為，採用以頁面來分割元素值的方法，能夠實現欄位的一對一對應，其效益高於直接以元素分割。這種做法的維護成本較低，並且在擴展欄位時，其增長複雜度為 O(n)，不會導致程式明顯的運行遲緩
> 


Test_Config.xlsx 下第一張資料表 Device&App_Config 設計為 Appium Driver 連線需要配置的Capability 可參考相關的 Appium 文件添加

![images/腳本架構_1.png](../images/腳本架構_5.png)

![images/腳本架構_1.png](../images/腳本架構_6.png)

除了第一張資料表 (Device&App_Config) ，其他資料表負責添加測試數據，調用方式與上方雷同

紅字的部分為錯誤數據可以用來測試數據不合法的情況

![images/腳本架構_1.png](../images/腳本架構_7.png)

![images/腳本架構_1.png](../images/腳本架構_8.png)

資料表有鎖定以保護勿編輯

![images/腳本架構_1.png](../images/腳本架構_9.png)

### step2. 操作模組化

根目錄下方 scripts 子資料夾，存放與 app 直接操作(連線)相關的模組

![images/腳本架構_1.png](../images/腳本架構_10.png)

對 UI 測試來說，主要操作包含 

1. 連線裝置
2. 定位
3. 定位到後的操作 點擊,輸入,滑動

分別對應到五個模組，下方僅說明常用的 Function

#### `setup_driver`

> Appium Manager to handle device and app configuration and start / stop the driver
> 

![images/腳本架構_1.png](../images/腳本架構_11.png)

一般在使用時僅需注意 no_reset 變數即可

#### `location_elements.py`

> 顯示等待跟定位相關
> 

positioning_method_dict 用來管理使用的 “定位方法”

conditions_dict 用來管理使用的 ”判斷元素狀態” 

![images/腳本架構_1.png](../images/腳本架構_12.png)

1. wait_for_locate 可以根據 XML 檔內元素的資訊來定位元素：

“wait_for_locate“ Function 最少要放兩個變數 1.定位方法 跟 2.元素資訊

condition 變數預設為判斷元素是否可見

timeout 變數預設為10秒內要定位到

示範函數例如 :

![](../images/external/df68cfba0192.png)

#### `click_action.py`

> 點擊相關
> 

![images/腳本架構_1.png](../images/腳本架構_13.png)

1. click_by_locating 是根據定位元素點擊：

“click_by_locating” Function 最少要放三個變數 1.定位方法 2.元素資訊 3.按鈕名稱(自定義)

condition 變數預設為判斷元素是否可點擊

示範函數 :

![](../images/external/3b29e39d870b.png)

1. click_by_percentage 是根據座標百分比點擊：

“click_by_percentage” Function 最少要放三個變數 1. x 軸百分比 2. y 軸百分比 3.按鈕名稱(自定義)

click_times 變數預設為點擊一次

示範函數 :

![images/腳本架構_1.png](../images/腳本架構_14.png)

#### `input_action.py`

> 輸入相關
> 

![images/腳本架構_1.png](../images/腳本架構_15.png)

<aside>
⚠️

因為這是 UI 的自動化測試，所以在編寫腳本時不能放會被前端擋的值

</aside>

1. input_by_send_key 是根據定位元素用 send_key 方法輸入（常用於輸入字串）：

“input_by_send_key” Function 最少要放三個變數 1.定位方法 2.元素資訊 3.輸入值(自定義)

示範函數 :

![images/腳本架構_1.png](../images/腳本架構_16.png)

1. input_with_keycode 是根據定位元素用 keycode 方法輸入（常用於輸入數字）：

“input_with_keycode” Function 最少要放三個變數 1.定位方法 2.元素資訊 3.輸入值(自定義)

示範函數 :

![images/腳本架構_1.png](../images/腳本架構_17.png)

#### `swipe_action.py`

> 頁面滑動相關
> 

![images/腳本架構_1.png](../images/腳本架構_18.png)

1. swipe_by_percentage 是根據頁面 x,y 百分比做滑動：

“swipe_by_percentage” Function 最少要放四個變數 1.左上 x 百分比 2.左上 y 百分比 3.右下 x 百分比 4.右下 y 百分比

示範函數 :

![images/腳本架構_1.png](../images/腳本架構_19.png)

### step3. 頁面模組化

根目錄下方 pages 子資料夾區分商家端跟打工端，有各自 App 對應的頁面測試，並封包成模組

![images/腳本架構_1.png](../images/腳本架構_20.png)

![images/腳本架構_1.png](../images/腳本架構_21.png)

例如 pages/employer/e3_login_page 代表商家端登入頁面的操作

![images/腳本架構_1.png](../images/腳本架構_22.png)

1. 導入相關的頁面操作 
2. 導入數據驅動並設定要調用哪一組數據
3. 對頁面編寫測試

## 虛擬環境

---

在根目錄下方建構 python 的虛擬環境

1. 創建虛擬環境 : `python -m venv venv`
2. 啟動虛擬環境
    
    **Windows** : `.\venv\Scripts\activate`
    
    **MacOS / Linux** : `source venv/bin/activate`
    
3. 安裝依賴 : `pip install -r requirements.txt`

## 執行測試

---

根目錄下方 tests 子資料，存放主測試腳本，調用測試流程經過的頁面進行測試

[`1.py`](http://1.py)是一個簡短測試腳本可以拿來調適腳本架構有沒有問題

![images/腳本架構_1.png](../images/腳本架構_23.png)

例如 tests/test_e1_reset 代表商家端啟動流程測試，包含

1. 刪除快取重新啟動商家端 app
2. 檢查 onboarding 頁面
3. 檢查 規則功能

![images/腳本架構_1.png](../images/腳本架構_24.png)

腳本架構為

1. 設置 log 等級
2. 設置使用的測試數據
3. 管理啟動 / 關閉 driver
4. 測試頁面

可直接用 python 執行腳本 (示範執行 1.py)

![images/腳本架構_1.png](../images/腳本架構_25.png)

- 可下載 “[Vysor](https://www.vysor.io/)” 查看執行畫面
    
    ![](../images/external/c80cc1bb11af.png)
    

## Coding Style

---

安裝 VSCode 的 Python 插件 pylint，檢查代碼符合規範（建議分數在 8 分以上）。

執行 c:/Worky_UITest/utils/pylint_report.py 

TARGET_DIRECTORIES 為要分析的資料夾

![images/腳本架構_1.png](../images/腳本架構_26.png)

結果存放於 `reports/pylint_report` 目錄：

1. 條形圖（png 檔案），顯示各文件 pylint 分數。
2. 詳細報告（txt 檔案），記錄未達標的問題，例如：單行過長、缺少文檔說明等

![images/腳本架構_1.png](../images/腳本架構_27.png)

![images/腳本架構_1.png](../images/腳本架構_28.png)