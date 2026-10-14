# ADB Troubleshooting

> device unauthorized 問題解決

---

## USB Debugging Authorization Dialog Does Not Appear

如果你的 Android 設備沒有彈出「**允許 USB 調試？**」對話框（如下圖），那麼可能是 `adb` 金鑰 (`adbkey`) 沒有正確生成。

![image.png](../images/ADB 問題_1.png)

### Possible Causes

1. **`adbkey` 生成失敗**
    
    當 `adb` 連線時，會在 `~/.android/` 目錄下生成 `adbkey` 和 `adbkey.pub` 檔案，這是 ADB 使用的 RSA 公私鑰對。
    
    - 如果這些檔案遺失、損壞，或權限錯誤，則設備無法識別授權請求，導致 `unauthorized` 問題。
2. **USB 連線異常**
    - 嘗試更換 USB 線，或插入不同的 USB 端口。
    - 檢查 Android 設備的 **USB 運行模式**（應設為「**僅充電**」或「**文件傳輸**」）。
    - 在開發者選項中 **關閉並重新開啟 USB 調試**，然後重新插拔手機。
3. **舊的 `adb` 進程干擾**
    - 使用 `adb kill-server` 關閉舊的 adb 進程，然後 `adb start-server` 重新啟動。
    - 確保 `adb` 版本是最新的，執行 `adb version` 檢查。

### Manually Reset the `adbkey`

如果 `adbkey` 沒有自動生成，你可以手動修復：

可以成功被 adb 授權的電腦內拉取 `adbkey` 和 `adbkey.pub` 檔到你需要的電腦內即可，放置路徑通常為

`sudo nautilus ~/.android/` 可以用系統管理員權限開啟

1. `/home/your-username/.android/` 目錄內，或
2. `/root/.android/`

![image.png](../images/ADB 問題_2.png)

`adbkey.pub` 應該是一串 `RSA PUBLIC KEY`

![image.png](../images/ADB 問題_3.png)

![image.png](../images/ADB 問題_4.png)



## Resolve the `adb offline` State

`adb kill-server
 adb usb
 adb reconnect
 adb devices`

授權一直失敗可能是有多個 .android 文件導致抓不到正確的 adbkey

可以嘗試刪掉所有 .android 位置後重新建目錄，在重啟 servse

![image.png](../images/ADB 問題_5.png)



## `adb` Cannot Access the USB Device

可能是主機 (Ubuntu) 上的 `adb server` 已經在使用該設備

![image.png](../images/ADB 問題_6.png)

1. 可在主機上關閉 `adb-server`

`adb kill-server
 sudo pkill adb
 sudo lsof -i :5037`   # 確保沒有 adb 進程佔用該埠

![image.png](../images/ADB 問題_7.png)

1. 在容器內重新啟動 `adb-server`

`adb start-server
 adb devices`

![image.png](../images/ADB 問題_8.png)
