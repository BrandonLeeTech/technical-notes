# 測試紀錄與報告

> 本文說明如何查看 Appium Server Log、保存測試截圖，以及整理測試報告，協助追蹤測試結果與排查問題。

測試產生的檔案會存放在專案根目錄的 `reports` 資料夾中，主要分為以下類型：

- **`logs/`**：測試與 Appium Server Log。
- **`screenshots/`**：測試流程中的畫面截圖。
- **`Test_Report.xlsx`**：彙整測試結果的報表。




## 查看 Appium Server Log

---

主測試流程會透過 `utils/logger_config` 模組產生 `get.log`。

![image.png](../images/Log 和 Report_1.png)

`logger_config` 模組中的 `LoggerConfig` 類別負責設定 Log 儲存位置，並檢查輸入的 Log Level。

![image.png](../images/Log 和 Report_2.png)

主測試流程結束後會產生 Log 檔案。

![image.png](../images/Log 和 Report_3.png)

![](../images/external/5540f4ce6c1b.png)

排查問題時，可以將 Log Level 設為 `DEBUG`，取得更詳細的執行資訊。

![](../images/external/6990d91c9030.png)

Appium 會透過 UiAutomator2 自動化操作 Android 應用。除測試程式自己的 Log 外，也可以查看 Appium Server Log，追蹤指令送出與執行的狀況。
    
![](../images/external/bc5d6caa6846.png)

以下 Log 顯示 Appium Client 依照 W3C WebDriver 協定發出尋找元素的請求：

```
[W3C (81a54518)] Responding to client with driver.findElement() result:
```

![](../images/external/809bbf6388fb.png)

若元素可見，請求會回傳 HTTP `200`：

```
GET /wd/hub/session/81a54518-8903-4e77-b54e-786cafaa78ed/element/00000000-0000-03d6-0000-002200000007/displayed 200
```

![](../images/external/54b01d0a9733.png)

Appium 也會記錄觸控操作。例如，下方 Log 顯示使用 `performTouch` 在螢幕上執行滑動：

操作從座標 `(800, 500)` 滑至 `(300, 500)`。

```
[W3C (81a54518)] Calling AppiumDriver.performTouch() with args: [[{"action":"press","options":{"x":800,"y":500}},{"action":"wait","options":{"ms":800}},{"action":"moveTo","options":{"x":300,"y":500}},{"action":"release","options":{}}],"81a54518-8903-4e77-b54e-786cafaa78ed"]
```

![](../images/external/8ef19112e28b.png)

測試結束時，Appium 會刪除 UiAutomator2 Session：

```
[UiAutomator2] Deleting UiAutomator2 session
```

![](../images/external/78690d5b079c.png)
    


## 保存測試截圖

---

頁面測試流程會透過 `utils/image_action` 模組保存截圖。若只需要特定頁面的畫面，可在對應的頁面流程中呼叫截圖功能。

例如，`e7_tutorial_page.py` 會替新手教學的各個頁面截圖。

![image.png](../images/Log 和 Report_4.png)

目前使用固定檔名，因此同一頁面的新截圖會覆蓋舊檔。

![image.png](../images/Log 和 Report_5.png)

可以直接到 `screenshots/` 資料夾查看截圖。`captcha/` 子資料夾則存放供驗證碼或打卡碼辨識使用的圖片。

![image.png](../images/Log 和 Report_6.png)



## 整理測試報表

---

`root/utils/excel_operations.py`：管理測試報表 `Test_Report.xlsx` 的欄位與匯入格式。

`root/utils/data_export.py`：負責將測試結果匯出至 `Test_Report.xlsx`。

`root/utils/data_import.py`：負責匯入資料驅動測試使用的 `Test_Config.xlsx` 和 `Scripts_Config.xlsx`。

![image.png](../images/Log 和 Report_7.png)

每次測試完成後，結果會附加在報表末尾，並自動產生流水號。每個頁面測試會列為一筆測項。

<aside>
⚠️

測試執行期間請勿開啟或編輯 XLSX 檔。若檔案正由其他程式使用，匯入或匯出可能會失敗。

</aside>

![image.png](../images/Log 和 Report_8.png)



## 定義錯誤代碼

---

目前會在動作模組中加入 Error Code，方便定位執行問題。代碼列表請參考 `reports/docs/Error_code.txt`。

![image.png](../images/Log 和 Report_9.png)

![image.png](../images/Log 和 Report_10.png)

![image.png](../images/Log 和 Report_11.png)



## 使用 Allure 產生測試報告

---

在專案根目錄執行以下指令，將測試結果輸出至 `allure-results`：

```bash
pytest --alluredir=allure-results
```

Pytest 會收集符合命名規則的測試檔並執行，Allure 則會讀取 `allure-results` 中的資料產生報告。

若需要依特定順序執行主測試腳本，請在檔名中加入流水號，讓執行順序一目了然。

![image.png](../images/Log 和 Report_12.png)

接著執行以下指令，在瀏覽器中檢視互動式報告：

```bash
allure serve allure-results
```

如果測試失敗，可以先檢視 Allure 報告中的 Log，再依照前述方式排查。

![image.png](../images/Log 和 Report_13.png)

例如，執行 `test_e7_1` 時發生錯誤：

Log 顯示錯誤發生在截圖階段，例外類型為 `CalledProcessError`。

![image.png](../images/Log 和 Report_14.png)

![image.png](../images/Log 和 Report_15.png)

檢查對應的頁面腳本後，發現檔名中的連字號 `-` 與程式預期的底線 `_` 不一致。修正名稱後，可以只重跑失敗的主測試腳本：


```bash
pytest tests/test_4E_account.py --alluredir=allure-results
```

![image.png](../images/Log 和 Report_16.png)

![image.png](../images/Log 和 Report_17.png)

重新檢視報告，確認該測試已通過。

![image.png](../images/Log 和 Report_18.png)
