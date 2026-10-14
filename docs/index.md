# 首頁

> 💡 計畫 → 開發 → 持續整合 CI → 測試 → 部署 CD → 監控 → 反饋

## 目錄

- 測試自動化
    - [框架介紹](notes-automation/auto-framework.md)  
    - [腳本架構](notes-automation/auto-scripts-structure.md)  
    - [Log & Report 查看](notes-automation/test-log-report.md)  
    - [ADB 問題](notes-automation/test-adb.md)  
    - [API 測試](notes-automation/test-api.md)
    - [Streamlit + PyInstaller](notes-automation/streamlit_pyinstaller_tutorial.md)

- 環境與部署
    - [Docker 設定](notes-env/docker-guide.md)  
    - [Python 環境設定](notes-env/python-env-setup.md)  
    - [VM 使用](notes-env/vm-setup.md)

- DevOps
    - [建構 GitLab Runner](notes-devops/gitlab-runner.md)（既有 GitLab 環境筆記）
    - [CI/CD](notes-devops/ci-cd.md)

---

## 🛠 工具鏈總覽

| 類別 | 工具 |
|------|------|
| 版本控制 | GitHub |
| 腳本編寫 | Python、Appium |
| CI/CD | GitHub Actions、Jenkins、GitLab CI（既有環境） |
| 容器化與編排 | Docker、Kubernetes |
| 基礎設施自動化 | Terraform、Ansible |
| 監控與告警 | Prometheus、Grafana |
| 虛擬化工具 | VirtualBox、VM |


## ❗ 主要難題與解法

<details>
<summary>❓ Q：可交互元素缺少 `id`</summary>

**A**：使用 `xpath` 或 `class` 定位，導致穩定性降低
</details>

<details>
<summary>❓ Q：自動化環境與測試環境互相影響</summary>

**A**：新增判斷條件、降低環境變動對測試穩定性的影響
</details>


## 🚀 快速上手指南

1. ⚙️ 依各篇教學安裝 Python、Appium 與相關工具。
2. 📚 從上方導覽選擇主題，依照步驟設定環境。
3. 🔗 範例測試程式目前仍位於舊的內部 Git 伺服器；請先向維護者確認可用的新連結。
