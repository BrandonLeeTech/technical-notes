# 使用 VirtualBox 建立 Ubuntu 虛擬機

> 若測試需要在 Linux 核心下執行 Docker，常見方式有兩種：安裝 Windows 和 Linux 雙系統，或使用 Linux 虛擬機。雙系統會占用較多磁碟空間，切換也較不方便，因此本文說明如何建立 VirtualBox VM。
> 

---


## 安裝 VirtualBox

---

> [VirtualBox](https://www.virtualbox.org/) 是 Oracle 開發的免費、開源虛擬化軟體。
> 

### 1. 下載 VirtualBox

從 [Oracle VirtualBox 舊版下載頁](https://www.virtualbox.org/wiki/Download_Old_Builds_6_1) 下載 VirtualBox 6.1，並完成安裝。

![image.png](../images/VM_1.png)

下載時選擇 **Windows hosts**。

這份筆記使用的預建 Linux 映像與 VirtualBox 7 有相容性問題，因此沿用 VirtualBox 6.1。

### 2. 下載 Ubuntu VDI 映像

從 [OSBoxes Ubuntu 虛擬機映像頁面](https://www.osboxes.org/ubuntu/#google_vignette) 下載 Ubuntu 20.04 LTS 映像。

![image.png](../images/VM_2.png)

OSBoxes 網站列出的預設登入資訊如下。這是下載映像的共用預設值，不是你的個人密碼；首次登入後仍應立即更換。

![OSBoxes 公開下載頁的預設帳號資訊](../images/VM_19.png)

下載的 VM 映像可能帶有預設登入資訊。首次登入後請立即更換密碼；不要以預設密碼連接公司或公開網路，也不要在虛擬機內放置正式環境憑證。

解壓縮下載檔案，取得預先建置好的 `.VDI` 虛擬磁碟。

### 3. 建立虛擬機

啟動 VirtualBox 並建立新虛擬機。建立後，虛擬機會出現在管理員視窗的清單中。

![image.png](../images/VM_3.png)

虛擬機名稱和儲存位置可依需求設定。

作業系統類型選擇 **Linux**，版本選擇 **Ubuntu (64-bit)**。

![image.png](../images/VM_4.png)

記憶體建議配置 8 GB，至少配置 4 GB。

![image.png](../images/VM_5.png)

建立硬碟時，選擇剛才解壓縮取得的 VDI 檔案。

![image.png](../images/VM_6.png)

### 4. 設定虛擬機

選取剛建立的 VM，開啟設定。

前往 **系統 → 處理器**，啟用 **Nested VT-x/AMD-V**。此功能需要 VirtualBox 6.1.36 或更新版本。

Nested VT-x/AMD-V 是嵌套虛擬化功能，可讓 VM 內再執行其他使用虛擬化技術的工具，例如 Docker。

![image.png](../images/VM_7.png)

<aside>
💡

**FAQ：Nested VT-x/AMD-V 的常見問題**

</aside>

> Q1：為什麼 Nested VT-x/AMD-V 選項是灰色，無法啟用？
> 

> A：檢查宿主機是否有其他虛擬化工具正在執行，例如 **Hyper-V** 或 VMware。
> 

> Q2：啟用嵌套虛擬化後，VM 效能變差怎麼辦？
> 

> A：可增加分配給 VM 的 CPU 和記憶體，或關閉宿主機上不必要的背景程式。
> 

如果宿主機使用 WSL 2，Hyper-V Hypervisor 可能會持續啟用。即使關閉 WSL，系統仍可能保留 Hyper-V，因此其他虛擬化工具可能無法使用 Nested VT-x。可依照下方步驟檢查目前的 Hypervisor 狀態。

![螢幕擷取畫面 2024-12-18 091859.png](../images/VM_8.png)

![螢幕擷取畫面 2024-12-18 092009.png](../images/VM_9.png)

以系統管理員身分開啟命令提示字元並執行 `systeminfo`，檢查輸出中是否顯示已偵測到 Hypervisor。

![image.png](../images/VM_10.png)

若要停用 Hyper-V，可執行 `bcdedit /set hypervisorlaunchtype off`。此變更會影響需要 Hyper-V 的功能。

![螢幕擷取畫面 2024-12-18 091912.png](../images/VM_11.png)

重新啟動電腦，讓設定生效。

重新開機後再次執行 `systeminfo`，確認 Hypervisor 狀態已更新，再回到 VirtualBox 檢查 Nested VT-x 選項。

![image.png](../images/VM_12.png)

確認宿主機設定後，即可在 VM 設定中啟用 Nested VT-x。

![image.png](../images/VM_13.png)

#### 其他設定

- 以下設定可依需求調整：
    
    
    1. **一般**：
        
        設定共用剪貼簿的方向與功能。
        
    2. **系統**：
        1. 作業系統存放在 VDI 虛擬磁碟中，並透過 SATA 控制器掛載。請確認硬碟的啟動順序正確。
        2. 記憶體配置過少可能導致 VM 無法啟動。
        3. CPU 核心數可依 VM 的工作負載調整。
        
        CPU 和記憶體配置過高會影響宿主機效能，請依可用資源調整。
        
    3. **存放資訊**：
        1. IDE 可用來掛載 ISO 光碟映像，例如 Guest Additions ISO；Guest Additions 可提供共用剪貼簿等功能。
        2. SATA 適用於現代作業系統的虛擬磁碟，例如 Windows 或 Ubuntu。
        
        建議將透過 SATA 控制器掛載的 VDI 檔案存放在 SSD，以提升讀寫效能。
        
    4. **網路介面卡**：
        1. **NAT 模式**：VM 透過宿主機連線到網路，但區域網內的其他設備通常無法直接連線到 VM。
        2. **橋接模式**：VM 直接連接實體網路並取得自己的 IP，適合需要與區域網設備通訊的情境；是否可用仍受網路政策限制。
        
        若公司網路限制橋接連線，或不允許 VM 取得 IP，請改用 NAT 模式。
        
        本環境使用預設的 NAT 網路，並透過 USB 直通將裝置連接到 VM。
        
    5. **USB**：
        
        使用 USB 3.0 前，需安裝 VirtualBox Extension Pack。USB 直通時，一個裝置同一時間只能由一台機器使用；測試時需在 Host 和 Guest 之間切換裝置控制權。
        
    6. **共用資料夾**：
        
        設定 Host 與 Guest 共用的資料夾路徑，並啟用自動掛載。
        
        建議為 VM 指定獨立的共用資料夾，方便管理測試資料，也能減少 Host 與 Guest 的檔案互相干擾。
        
    7. 結論：
    UI 自動化測試通常依序操作裝置，同一台裝置同一時間只能執行一個操作流程。請依測試負載配置 VM 的 CPU，並透過 USB 直通連接裝置。
    
    ![image.png](../images/VM_14.png)
    
    ![image.png](../images/VM_15.png)
    
    ![image.png](../images/VM_16.png)
    
    ![image.png](../images/VM_17.png)
    
    ![image.png](../images/VM_18.png)
    

### 5. 新增使用者

新增使用者前，請先確認已更換映像檔的預設密碼。

![image.png](../images/VM_20.png)

![image.png](../images/VM_21.png)

進入桌面後，開啟 **Settings → Users → Unlock**，解鎖使用者設定。

![image.png](../images/VM_22.png)

![image.png](../images/VM_23.png)

點選右上方的 **Add User**，建立個人帳號。

![image.png](../images/VM_24.png)

![image.png](../images/VM_25.png)

建立完成後，點選右上方的電源按鈕，切換至新帳號。

![image.png](../images/VM_26.png)

<aside>
💡

**FAQ：啟動 VM** 錯誤

</aside>

> Q1：為什麼 VM 啟動後會進入 `initramfs` 提示符？
> 

> A：可能是 VDI 路徑錯誤，或 VDI 的 UUID 重複。請確認虛擬磁碟掛載位置；若 UUID 重複，重新產生 UUID 後再掛載。
> 

![image.png](../images/VM_27.png)

### 6. 安裝 Guest Additions 與 VirtualBox Extension Pack

> Guest Additions 可支援調整解析度、共用剪貼簿和共用資料夾等功能。
> 

在 VirtualBox 視窗上方選擇 **Devices → Insert Guest Additions CD Image**。

![image.png](../images/VM_28.png)

依照畫面提示完成安裝。安裝完成後，虛擬機中會出現 Guest Additions 光碟機。

![image.png](../images/VM_29.png)

![image.png](../images/VM_30.png)

之後可透過 **View → Virtual Screen** 調整解析度。

![image.png](../images/VM_31.png)

如果系統找不到光碟機，Guest Additions 安裝程式就無法啟動。

請先在 VirtualBox 管理員中開啟 VM 設定，於儲存裝置新增虛擬光碟機，再重新執行上述安裝步驟。

![image.png](../images/VM_32.png)

### 7. 設定共用資料夾

若要在 Host 和 Guest 之間傳輸檔案，請設定共用資料夾。

先在 Guest 中開啟終端機，將目前使用者加入 `vboxsf` 群組：

```bash
sudo usermod -aG vboxsf $USER
```

登出並重新登入後，執行 `groups`，確認輸出包含 `vboxsf` 群組。

![image.png](../images/VM_33.png)

![image.png](../images/VM_34.png)

接著在 VM 的共用資料夾設定中，加入測試腳本的專案根目錄。

勾選 **Auto-mount** 和 **Make Permanent**，讓共用資料夾在 VM 啟動時自動掛載。

![image.png](../images/VM_35.png)

![image.png](../images/VM_36.png)

完成後，可在檔案管理員中查看新增的共用資料夾。

![image.png](../images/VM_37.png)

### 8. 設定自動登入與 `sudo` 權限

可在圖形介面中啟用 **Automatic Login**。

![image.png](../images/VM_38.png)

也可以透過命令列編輯 GDM3 設定檔：

`sudo nano /etc/gdm3/custom.conf`

將 `AutomaticLogin` 設為要自動登入的使用者名稱。

![image.png](../images/VM_39.png)

不要設定 `%sudo ALL=(ALL) NOPASSWD: ALL`。這會讓 sudo 群組成員執行任何管理命令時都不必輸入密碼，增加帳號遭入侵後的影響範圍。保留系統預設的 sudo 密碼驗證；若自動登入，只限隔離的個人測試 VM，且不要存放正式環境憑證。

以下截圖是舊設定中的不安全範例，請勿照圖加入 `NOPASSWD: ALL`：

![不安全的 sudoers 設定範例，請勿照做](../images/VM_40.png)

![image.png](../images/VM_41.png)
