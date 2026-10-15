# 使用 Pytest Fixture 管理測試初始化

> 將測試環境初始化放進 Pytest Fixture，可避免模組載入時執行外部操作，並讓測試只在需要時才建立相依資源。

## 避免在模組載入時初始化

Pytest 收集測試時會匯入測試模組與 `conftest.py`。如果在模組頂層呼叫 API、取得 Token 或啟動服務，這些操作會在測試正式執行前發生。即使只執行其中一部分測試，也可能因不相關的初始化失敗而中止測試收集。

將設定讀取與外部連線放進 Fixture，能讓初始化時機更明確，也能將錯誤定位在使用該 Fixture 的測試上。

```python
import pytest


@pytest.fixture(scope="session")
def api_client():
    settings = load_settings()
    return ApiClient(base_url=settings.api_url)


@pytest.fixture
def access_token(api_client):
    return api_client.create_test_token()


def test_profile_endpoint(api_client, access_token):
    response = api_client.get_profile(access_token)

    assert response.status_code == 200
```

此範例在測試需要 `api_client` 時才讀取設定並建立 Client；只有要求 `access_token` 的測試才會建立測試 Token。

## 選擇 Fixture 範圍

Fixture 的 `scope` 決定資源建立與清理的頻率。選擇時要同時考量初始化成本和測試隔離：

| 範圍 | 生命週期 | 適合的資源 |
|------|----------|------------|
| `function`（預設） | 每個測試函數建立一次 | 需要獨立狀態的測試資料、交易或臨時檔案 |
| `class` | 每個測試類別建立一次 | 同一類別共用且不會互相污染的資源 |
| `module` | 每個測試模組建立一次 | 同一模組共用的固定設定 |
| `session` | 整次 Pytest 執行建立一次 | 可安全共用的設定、唯讀 Client 或昂貴資源 |

會改變狀態的資源通常應使用較短的範圍，並在 Fixture 的 `yield` 後清理。只有確定可安全共用時，才使用 `session` 範圍。

## 謹慎使用 `autouse`

`autouse=True` 會讓符合範圍的測試自動使用 Fixture，即使測試函數沒有明確宣告它。這適合套用在每個測試都必須遵守的輕量設定；若 Fixture 會呼叫外部服務、建立帳號或改變資料，應讓測試明確依賴它，避免隱藏成本和副作用。

## 初始化失敗時的排查方向

- 若測試在收集階段失敗，檢查模組頂層與 `conftest.py` 是否執行了外部操作。
- 若只有部分測試失敗，確認它們使用的 Fixture、設定與外部服務狀態。
- 若測試之間互相影響，縮短 Fixture 範圍，並在 `yield` 後清理資料。
- 若初始化很耗時，確認是否能安全共用唯讀資源，或將昂貴操作延後到實際需要的 Fixture。
