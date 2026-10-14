# QA Automation Notes: Appium, API Testing, Docker & CI/CD

> 💡 計畫 → 開發 → 持續整合 CI → 測試 → 部署 CD → 監控 → 反饋

## Contents

- Test Automation
    - [Appium Test Automation](notes-automation/auto-framework.md)
    - [Test Automation Architecture](notes-automation/auto-scripts-structure.md)
    - [Test Reporting](notes-automation/test-log-report.md)
    - [ADB Troubleshooting](notes-automation/test-adb.md)
    - [API Testing](notes-automation/test-api.md)
    - [Streamlit + PyInstaller](notes-automation/streamlit_pyinstaller_tutorial.md)

- Environment & Deployment
    - [Docker](notes-env/docker-guide.md)
    - [Python Environment](notes-env/python-env-setup.md)
    - [VirtualBox VM](notes-env/vm-setup.md)

- DevOps
    - [GitLab Runner](notes-devops/gitlab-runner.md)
    - [CI/CD Pipeline](notes-devops/ci-cd.md)

---

## Toolchain Overview

| 類別 | 工具 |
|------|------|
| 版本控制 | GitHub |
| 腳本編寫 | Python、Appium |
| CI/CD | GitHub Actions、Jenkins、GitLab CI（既有環境） |
| 容器化與編排 | Docker、Kubernetes |
| 基礎設施自動化 | Terraform、Ansible |
| 監控與告警 | Prometheus、Grafana |
| 虛擬化工具 | VirtualBox、VM |


## Common Challenges and Solutions

<details>
<summary>❓ Q：可交互元素缺少 `id`</summary>

**A**：使用 `xpath` 或 `class` 定位，導致穩定性降低
</details>

<details>
<summary>❓ Q：自動化環境與測試環境互相影響</summary>

**A**：新增判斷條件、降低環境變動對測試穩定性的影響
</details>


## Quick Start Guide

1. ⚙️ 依各篇教學安裝 Python、Appium 與相關工具。
2. 📚 從上方導覽選擇主題，依照步驟設定環境。
3. 🔗 範例測試程式目前仍位於舊的內部 Git 伺服器；請先向維護者確認可用的新連結。
