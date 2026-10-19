# 自動化測試筆記：Appium、API 測試、Docker 與 CI/CD

> 💡 計畫 → 開發 → 持續整合 CI → 測試 → 部署 CD → 監控 → 反饋

## 筆記目錄

- 測試自動化
    - [Appium 自動化測試環境](notes-automation/auto-framework.md)
    - [自動化測試腳本架構](notes-automation/auto-scripts-structure.md)
    - [Pytest Fixture 初始化](notes-automation/pytest-fixture-initialization.md)
    - [Pytest 平行 UI 測試](notes-automation/pytest-parallel-device-pool.md)
    - [測試紀錄與報告](notes-automation/test-log-report.md)
    - [ADB 疑難排解](notes-automation/test-adb.md)
    - [API 測試](notes-automation/test-api.md)
    - [Streamlit + PyInstaller 打包教學](notes-automation/streamlit_pyinstaller_tutorial.md)

- 環境與部署
    - [Docker 測試環境](notes-env/docker-guide.md)
    - [Python 環境設定](notes-env/python-env-setup.md)
    - [VirtualBox 虛擬機設定](notes-env/vm-setup.md)
    - [Xcode 27.1 iPhone Simulator 設定](notes-env/xcode27-iphone-simulator.md)

- DevOps
    - [GitLab Runner 安裝與維護](notes-devops/gitlab-runner.md)
    - [CI/CD 流程](notes-devops/ci-cd.md)

---

## 工具鏈總覽

| 類別 | 工具 |
|------|------|
| 版本控制 | GitHub |
| 腳本編寫 | Python、Appium |
| CI/CD | GitHub Actions、Jenkins、GitLab CI（既有環境） |
| 容器化與編排 | Docker、Kubernetes |
| 基礎設施自動化 | Terraform、Ansible |
| 監控與告警 | Prometheus、Grafana |
| 虛擬化工具 | VirtualBox、VM |


## 常見問題與處理方式

<details>
<summary>❓ Q：可交互元素缺少 `id`</summary>

**A**：使用 `xpath` 或 `class` 定位，導致穩定性降低
</details>

<details>
<summary>❓ Q：自動化環境與測試環境互相影響</summary>

**A**：新增判斷條件、降低環境變動對測試穩定性的影響
</details>


## 快速開始

1. ⚙️ 依各篇教學安裝 Python、Appium 與相關工具。
2. 📚 從上方導覽選擇主題，依照步驟設定環境。
3. 🔗 範例測試程式目前仍位於舊的內部 Git 伺服器；請先向維護者確認可用的新連結。
