# PCR 執行與排查索引

此頁提供 PCR 與 sanity 自動化的入口和排查順序；專案的測試清單、環境設定和參數會變動，請以各 repository 的目前設定為準，不在此複製完整案例清單或測試資料。

## 執行入口

在目標專案根目錄先確認虛擬環境與 pytest 設定，再指定平台或案例執行。常見測試目錄如下：

| 專案／平台 | 測試入口 | 專案內維護資料 |
|---|---|---|
| HKTVmall Android | `tests/hk_sanity_android/` | `tests/hk_sanity_android/support/`、`android_Page.yaml` |
| HKTVmall iOS | `tests/hk_sanity_iOS/` | `tests/hk_sanity_iOS/support/`、`iOS_Page.yaml` |
| HKTVmall Web | `tests/hk_sanity_web/` | `tests/hk_sanity_web/support/`、`web_Page.yaml` |
| PCR | `tests/Regular_Sanity/`、`tests/Check_PCR_Result/`、`tests/Precheck_EXPRESS/` | `tool/`、`docs/`、專案 pytest 設定 |

跨平台 pytest 範例與裝置池選項見 [Testing Execution Examples](https://hongkongtv.atlassian.net/wiki/spaces/~7120203cfc8772b87c4d138da3f1cfdd350f38/pages/6113788031/Testing+Execution+Examples+for+Android+iOS+and+Web+Platforms) 和 [Android Device Pool](https://hongkongtv.atlassian.net/wiki/spaces/~7120203cfc8772b87c4d138da3f1cfdd350f38/pages/5729124372/Android+Device+Pool)。執行前檢查 repository 的 `pytest.ini`、`conftest.py` 和 README，因為命令列選項和 fixture 會依專案而異。

## 排查順序

1. **先確認執行目標**：記錄 repository、branch、環境、案例路徑、pytest marker、worker 數量及裝置選擇。
2. **看失敗階段**：區分 collection/import、fixture/setup、driver/session 建立、測試步驟、teardown 或報告發布錯誤。
3. **讀專案 log 和附件**：檢視 pytest 輸出、Allure 附件、截圖與 page source；依失敗案例進入對應平台的 helper 和 locator YAML。
4. **檢查共享資源**：平行執行時確認裝置 lease、Appium session、共用帳號及測試資料是否互相影響。
5. **先縮小範圍重跑**：單案例、單 worker 重現後，再恢復原本的 suite 或平行設定。

## 專案工具入口

- `pcr/tool/`：PCR 報表、資料同步、SKU 與其他維運工具。執行前先看腳本說明及其設定來源。
- `qa-test-platform-infra/scripts/`：Grid 選擇和執行前置條件檢查。
- `hybris-sanity-for-hk/device_status_api/`：裝置、測試執行狀態與 lease service API。
- `hermes-custom-skills/`：Jenkins 觸發、Allure 報告檢查和日誌開啟工具。

## Confluence 參考

- [PCR Regular Sanity](https://hongkongtv.atlassian.net/wiki/spaces/~7120203cfc8772b87c4d138da3f1cfdd350f38/pages/5921702044/PCR+Regular+Sanity)：案例分類與執行背景。
- [Optimizing PCR Test Parameter](https://hongkongtv.atlassian.net/wiki/spaces/~7120203cfc8772b87c4d138da3f1cfdd350f38/pages/5677219919/Optimizing+PCR+Test+Parameter)：pytest 初始化和 fixture 管理的演進記錄；採用前請核對現有程式碼。
- [Conventional Commits](https://hongkongtv.atlassian.net/wiki/spaces/~7120203cfc8772b87c4d138da3f1cfdd350f38/pages/4688052366/Conventional+Commits)：提交訊息格式參考。

不要把 Confluence 的舊測試數量、帳號、SKU、內部 endpoint 或付款資料複製到此索引；這些資訊應以各專案目前的設定和資料來源為準。
