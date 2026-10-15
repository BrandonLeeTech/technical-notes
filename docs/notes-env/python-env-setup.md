# Python 環境設定

---

使用 `pip` 安裝 `requirements.txt` 中的套件時，若安裝位置在 `C:/Program Files`，可能會因權限不足而失敗。

此時 Python 可能會改在使用者目錄下建立 `site-packages`，並將套件安裝在該處。

![image.png](../images/Python 環境設定_1.png)

如果需要將套件安裝到系統目錄，可以先移除使用者目錄中的舊套件，再以系統管理員身分開啟命令提示字元執行安裝。

![image.png](../images/Python 環境設定_2.png)

`pip` 預設會快取套件，以加快後續安裝。若要排除快取造成的問題，可以清除快取後重新安裝：

![image.png](../images/Python 環境設定_3.png)

```bash
pip cache purge
pip install --no-cache-dir -r requirements.txt
```

若要額外加入模組搜尋路徑，可以在 `site-packages` 目錄中新增 `.pth` 檔案。

![image.png](../images/Python 環境設定_4.png)
