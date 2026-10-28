# 用 API 準備 UI 測試資料

UI 測試要驗證畫面和使用者流程；若前置資料可經 API 建立或重設，將資料準備交給 API workflow，通常更快也較容易排查。本文說明如何在 HKTVmall pytest 專案中尋找並使用既有 API workflow，不列出帳號、SKU、付款資料或環境憑證。

## 資料準備流程

```text
YAML / CI secret source
        ↓
API client → workflow helper → setup/assertions → UI flow → cleanup
```

1. 從現有測試資料與環境設定載入測試所需值，不要將帳密或 token 寫進測試程式或筆記。
2. 使用 `api/hktvmall_api_client.py` 的 client 方法處理單一 API 操作；使用 `api/workflows/` 封裝登入、購物車、地址簿或訂單等多步驟流程。
3. 檢查準備 API 的 HTTP 狀態與回應資料，再開始 UI 操作。若資料準備失敗，讓測試在 setup 階段失敗，避免把前置問題誤判成 UI 元素問題。
4. 測試結束時清理可重設的狀態，例如購物車或 wishlist；刪除帳號等不可逆操作只用於專用測試資料。

## 專案中的入口

| 需求 | 程式碼位置 |
|---|---|
| 登入、清空購物車、加入 SKU、切換付款 gateway | `api/workflows/cart_and_account_workflows.py` |
| 地址簿流程 | `api/workflows/address_book_workflow.py` |
| 完成訂單流程 | `api/workflows/order_completion_workflow.py` |
| 註冊測試帳號 | `api/register_account.py`；設定由專案 `data/` YAML 載入 |
| HAC 管理操作 | `api/hac_session.py`、`api/hac_impex_admin.py` |
| API 行為與合約測試 | `tests/api/`、`tests/api/README.md` |
| 可直接執行的資料準備範例 | `tests/hk_sanity_web/tool/` |

加入新的資料準備操作前，先搜尋 `api/` 與 `tests/*/tool/` 是否已有 helper。新增 API client 時沿用共用執行與回應驗證模式，避免在 UI 測試中複製 HTTP、登入或錯誤處理邏輯。

## 安全與平行執行

- 從 YAML、CI secret 或既有設定載入值；不要把秘密複製到 Markdown、測試輸出或錯誤訊息。
- 不同 worker 不應同時修改同一帳號的購物車、地址、付款設定或訂單。使用獨立測試資料，或將共用資料的測試分組依序執行。
- 準備資料的 helper 應清楚記錄修改內容，並回傳後續 UI 步驟需要的識別資料；不要在 helper 內隱藏無關副作用。

## 驗證

API helper 或 workflow 有變更時，先讀 `tests/api/README.md`，執行 schema 與 contract 測試。需要驗證環境連線時，再執行 API smoke 測試；UI 測試只用來確認畫面流程，不取代 API setup 的回應驗證。
