# QA Automation Notes: Appium, API Testing, Docker & CI/CD

這個 repository 收錄 Appium 自動化測試、API 測試、測試報告、Android ADB 疑難排解、Python 與 Docker 環境，以及 CI/CD 維護筆記。

## View the Notes

Browse the published notes on [QA Automation Notes](https://brandonleetech.github.io/technical-notes/).

## Local Preview

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
mkdocs serve
```

Open `http://127.0.0.1:8000` in a browser. To build the site locally:

```bash
mkdocs build --strict
```

## Deployment

Pull requests and pushes to `main` run the build checks. To publish the site, set **Settings → Pages → Build and deployment → Source** to **GitHub Actions**, then add the Actions repository variable `PUBLISH_PAGES=true` under **Settings → Secrets and variables → Actions → Variables**. Without this variable, the workflow builds the site but does not publish it.

## Repository Structure

- `docs/`: Markdown notes and images
- `mkdocs.yml`: MkDocs theme and navigation
- `.github/workflows/pages.yml`: GitHub Actions build and deployment workflow
- `requirements.txt`: MkDocs build dependencies
