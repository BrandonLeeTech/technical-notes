# GitLab Runner 安裝與維護

> 本文整理自建 GitLab Runner 時的安裝方式與常見問題處理方法。


---

## 下載與安裝 GitLab Runner

---

![image.png](../images/gitlab-runner_1.png)

### Q1：Runner 狀態顯示警告

在 GitLab 的 **Settings → CI/CD → Runners** 頁面中，Runner 狀態有時會顯示警告。下圖為設定完成後的狀態。

![image.png](../images/gitlab-runner_2.png)

![image.png](../images/gitlab-runner_3.png)

執行以下指令，確認 Runner 是否通過驗證：

`sudo gitlab-runner verify`

![image.png](../images/gitlab-runner_4.png)

### Q2：JSON Schema 錯誤

錯誤訊息：`expected boolean, but got null`

![image.png](../images/gitlab-runner_5.png)

1. 開啟 GitLab Runner 設定檔：`sudo nano /etc/gitlab-runner/config.toml`。
2. 將 `custom_build_dir.enabled` 設為 `false`。

按 `Ctrl + X` 離開，再按 `Y` 儲存變更。

3. 重新啟動 GitLab Runner：`sudo gitlab-runner restart`。

### Q3：Job 執行卡住

如果 Job 卡住，可以在 Runner 主機上手動啟動 Runner：

```bash
sudo gitlab-runner run
```

也可以在 `config.toml` 中設定 `privileged`，讓 Docker 在虛擬機內執行。這樣可避免在 `.gitlab-ci.yml` 中設定 Docker-in-Docker，減少 CI 設定的複雜度。

若要在 CI 設定中呼叫主機上的 Docker，也可以在 `config.toml` 中掛載 `docker.socket`。


![image.png](../images/gitlab-runner_9.png)
