# Appium 元素與 XML 排查

當 Appium 測試找不到元素時，先確認目前畫面與 UI hierarchy，再調整 locator。這份流程適用 Android Appium 測試；若失敗發生在 WebView，另確認 context 與 frame 狀態。

## 擷取目前畫面 hierarchy

測試執行中可透過 Appium driver 取得目前 session 的 source：

```python
from pathlib import Path

source = driver.page_source
Path("/tmp/appium-page-source.xml").write_text(source, encoding="utf-8")
```

先確認 `driver` 指向失敗測試使用的 session，並在失敗步驟附近擷取 source。若 pytest 已將 page source 附加到失敗報告，優先檢視該附件，避免重新執行造成畫面狀態不同。

## 檢查 XML 與 locator

在 XML 中找到目標節點，檢查它是否存在、是否在目前畫面，以及以下屬性是否穩定：

- `resource-id`：優先用於 App 內有明確 ID 的控制項。
- `text`、`content-desc`：確認文字和無障礙描述符合目前語系及畫面狀態。
- `class`、`clickable`、`enabled`、`bounds`：判斷節點類型、互動狀態與位置。

若節點完全不存在，先排查前一個畫面轉換、登入狀態、彈窗、載入中狀態或 WebView context。若節點存在但無法點擊，確認是否被遮罩、尚未 enabled，或需要先捲動到畫面中。

## 修正與驗證順序

1. 以 `resource-id` 或穩定的 accessibility ID 定位；文字 locator 僅在文案及語系穩定時使用。
2. 沿用平台 `Elements.py` 的 locator wrapper 和顯式等待，不以固定長時間 sleep 掩蓋錯誤畫面或 locator 問題。
3. XPath 僅在沒有穩定 ID 或描述時使用，並以必要屬性縮小匹配範圍；避免依賴節點序號或易變的 bounds。
4. 更新頁面 YAML 或 locator 後，單獨執行失敗測試，確認正常畫面與預期錯誤畫面都能被辨識。

此專案 Android locator 集中在 `tests/hk_sanity_android/android_Page.yaml`，通用定位和等待封裝在 `tests/hk_sanity_android/support/Elements.py`。失敗時也可參考 `reports/logs/test.log`、Allure 附件與截圖，確認是 locator、等待、畫面流程還是 session 問題。

## 敏感資料

Page source 可能包含使用者資料、訂單資訊或頁面 token。檢視及分享 XML 前先移除個人資料和敏感欄位；不要將原始 XML 或截圖提交到公開筆記或程式庫。
