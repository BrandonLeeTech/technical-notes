# 使用 Pytest 平行執行 UI 測試

> 平行執行 UI 測試前，先處理裝置分配與測試資料隔離，避免多個 Worker 同時操作同一台裝置或同一組帳號資料。

## 使用 pytest-xdist 啟動 Worker

安裝 `pytest-xdist` 後，可用 `-n` 指定並行 Worker 數量：

```bash
pip install pytest-xdist
pytest tests/ui -n 3
```

`pytest-xdist` 會把測試分配給多個 Worker。對 UI 自動化而言，每個同時執行的 Worker 都需要獨立的裝置或模擬器；Worker 數量不應高於可用裝置數量。若裝置數不足，增加 Worker 只會造成等待、逾時或資源爭用。

## 用 Device Pool 管理裝置租用

Device Pool 會在測試開始前分配一台可用裝置，並在測試結束後釋出。不同專案的 Pool Plugin 與命令列選項各不相同，請依所用 Plugin 的文件設定；`pytest-xdist` 本身不會自動管理實體裝置。

多個 CI Job 共用裝置時，Pool 需要用跨程序或跨主機的鎖定機制避免重複分配。等待裝置的逾時時間應依排程情境設定，並與單一測試的逾時區分開來。

## 避免共用測試資料互相污染

裝置隔離和測試資料隔離是兩個不同問題。多個 Worker 即使使用不同裝置，若同時操作同一個測試帳號，仍可能互相修改購物車、登入狀態或其他共用資料。

可依測試資料來源分組：

1. 共用同一個帳號或資料集的測試，安排在同一組並依序執行。
2. 使用不同帳號或獨立資料集的測試，可分配到不同 Worker 平行執行。
3. 測試結束後清理或重設狀態，避免後續測試依賴執行順序。

使用 `pytest-xdist` 時，可透過 `xdist_group` 和 `--dist=loadgroup` 將同組測試交給同一個 Worker：

```python
import pytest


@pytest.mark.xdist_group("account_a")
def test_account_a_login():
    ...


@pytest.mark.xdist_group("account_a")
def test_account_a_checkout():
    ...
```

```bash
pytest tests/ui -n 3 --dist=loadgroup
```

這能避免同一組測試同時操作共用資料，但不會自動重設帳號狀態；測試仍應自行準備資料並在結束後清理。

## 排查常見問題

| 現象 | 常見原因 | 排查方向 |
|------|----------|----------|
| 裝置已被租用 | 其他 Worker 或 CI Job 正在使用 | 確認 Pool 鎖定範圍及釋放流程 |
| Appium Session 已存在 | 前一個測試未正常關閉 Session | 在 Fixture 清理 Driver，並檢查逾時或中斷流程 |
| 沒有可用裝置 | 裝置離線、忙碌或與 Pool 條件不符 | 檢查裝置狀態、Pool 類型和候選清單 |
| 平行測試結果不穩定 | Worker 數量過多或共用資料互相影響 | 降低 Worker 數量，分組共用帳號的測試 |
