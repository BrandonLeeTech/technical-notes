# 自動化測試教學筆記

使用 MkDocs Material 維護 QA 自動化測試、環境建置與 DevOps 筆記，並透過 GitHub Actions 部署至 GitHub Pages。

## 本機預覽

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
mkdocs serve
```

瀏覽器開啟 `http://127.0.0.1:8000`。正式建置與連結檢查：

```bash
mkdocs build --strict
```

## 部署

Pull request 與推送至 `main` 都會執行建置檢查。要發布網站，需在 **Settings → Pages → Build and deployment** 將 **Source** 設為 **GitHub Actions**，並在 **Settings → Secrets and variables → Actions → Variables** 新增 `PUBLISH_PAGES=true`。未設定這個變數時，工作流程只會建置，不會部署頁面。

Repository：[BrandonLeeTech/technical-notes](https://github.com/BrandonLeeTech/technical-notes)。啟用 Pages 後，部署網址會顯示在 **Settings → Pages** 與 Actions 工作流程結果中。

## 內容結構

- `docs/`: Markdown 筆記與圖片
- `mkdocs.yml`: 網站主題與導覽
- `.github/workflows/pages.yml`: GitHub Actions 建置與部署
- `requirements.txt`: MkDocs 建置依賴
