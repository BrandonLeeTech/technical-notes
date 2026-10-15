# 自動化測試筆記：Appium、API 測試、Docker 與 CI/CD

這個 repository 收錄 Appium 自動化測試、API 測試、測試報告、Android ADB 疑難排解、Python 與 Docker 環境，以及 CI/CD 維護筆記。

## 瀏覽筆記

前往 [自動化測試筆記網站](https://brandonleetech.github.io/technical-notes/) 閱讀已發布的內容。

## 本機預覽

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
mkdocs serve
```

啟動後，在瀏覽器開啟 `http://127.0.0.1:8000`。若要在本機建置網站，請執行：

```bash
mkdocs build --strict
```

## 部署

建立 Pull Request 或推送至 `main` 時，GitHub Actions 會執行建置檢查。若要發布網站，請在 **Settings → Pages → Build and deployment → Source** 選擇 **GitHub Actions**，並在 **Settings → Secrets and variables → Actions → Variables** 新增 Repository Variable：`PUBLISH_PAGES=true`。未設定此變數時，工作流程只會建置，不會發布網站。

## 專案結構

- `docs/`：Markdown 筆記與圖片
- `mkdocs.yml`：MkDocs 主題與導覽設定
- `.github/workflows/pages.yml`：GitHub Actions 建置與部署工作流程
- `requirements.txt`：MkDocs 建置依賴
