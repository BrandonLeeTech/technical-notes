# VM

> **在 Linux 內核下驅動 Docker 執行測試**，有兩種常見的做法
1. **Windows** + Linux 雙系統啟動
2. 虛擬機環境 (Linux 核心)
雙系統要占用較多磁碟空間，且切換系統較複雜，下方說明 VM 設置
> 

---


## VirtualBox (VM) 安裝

---

> **VirtualBox** 是由 **Oracle** 開發的 **免費**、**開源** 的虛擬機軟體。 [Oracle VirtualBox](https://www.virtualbox.org/)
> 

### **1. 下載** VirtualBox

下載 VirtualBox 6.11 並安裝：[Download_Old_Builds_6_1 – Oracle VirtualBox](https://www.virtualbox.org/wiki/Download_Old_Builds_6_1)

![image.png](../images/VM_1.png)

選擇 Windows hosts

 VirtualBox 7 跟現成 Linux OS Image 不相容

### 2. 下載 Ubuntu VDI 檔

下載 Ubuntu 20.04 LTS： [Ubuntu Virtual Machine Images for VirtualBox and VMware](https://www.osboxes.org/ubuntu/#google_vignette)

![image.png](../images/VM_2.png)

OSBoxes 網站列出的預設登入資訊如下。這是下載映像的共用預設值，不是你的個人密碼；首次登入後仍應立即更換。

![OSBoxes 公開下載頁的預設帳號資訊](../images/VM_19.png)

下載的 VM 映像可能帶有預設登入資訊。首次登入後請立即更換密碼；不要以預設密碼連接公司或公開網路，也不要在虛擬機內放置正式環境憑證。

解壓縮下載檔案得到建置好的 .VDI 檔

### 3. 創建 VM

啟動 VirtualBox 並新增機器 (新增完的機器會顯示在設置右邊，如下圖)

![image.png](../images/VM_3.png)

名稱跟資料夾可自由配置

類型跟版本需要選擇 Linux / Ubuntu (64bit)

![image.png](../images/VM_4.png)

記憶體空間可以設置為 8GB (最少要4GB)

![image.png](../images/VM_5.png)

硬碟選擇剛剛的 VDI 檔

![image.png](../images/VM_6.png)

### 4. 設定 VM

點擊創建的 VM 的設定

點擊系統>處理器>啟用 Nested VT-x/AMD-V (該功能需要 VirtualBox 版本需 **6.1.36 以上**)

“Nested VT-x/AMD-V” 是嵌套虛擬化技術，允許在虛擬機內再運行另一個虛擬化工具(e.g. Docker)

![image.png](../images/VM_7.png)

<aside>
💡

**FAQ：Nested VT-x/AMD-V 的常見問題**

</aside>

> Q1: 為甚麼 Nested VT-x/AMD-V 反灰無法點擊?
> 

> A: 確認宿主機內有無背景運行的虛擬化工具 (e.g. **Hyper-V**、VMware)
> 

> Q2: 虛擬機內啟用嵌套導致效能變差怎麼辦?
> 

> A: 增加虛擬機的 CPU 和 RAM 分配或減少背景運行程式
> 

如果 host 機內有裝類似 WSL 等來運行 Linux 指令就預設一定會啟動 Hyper-V 虛擬化，即使停用 WSL，Hyper-V 的輕量級 Hypervisor 仍可能留在系統中，這是由於 Hyper-V 核心必須支援 WSL2。而 Hyper-V 核心在運行時佔用了虛擬化功能會導致其他虛擬化工具無法啟用 **Nested VT-x**。

![螢幕擷取畫面 2024-12-18 091859.png](../images/VM_8.png)

![螢幕擷取畫面 2024-12-18 092009.png](../images/VM_9.png)

用 admin 啟動 cmd 輸入 `systeminfo` 可以在下方看到偵測到 Hypervisor 功能啟用的提示

![image.png](../images/VM_10.png)

輸入 `bcdedit /set hypervisorlaunchtype off` 來禁用 Hyper-V ，完全釋放虛擬化功能

![螢幕擷取畫面 2024-12-18 091912.png](../images/VM_11.png)

重啟電腦讓系統級指令生效

再次查看 `systeminfo` 可以在看到 Hyper-V 可在 VM 模式中擴充

![image.png](../images/VM_12.png)

可設定 VM 內 Nesr VT-x 功能

![image.png](../images/VM_13.png)

#### 其他設定

- (可需要時再做修改)
    
    
    1. **一般**：
        
        共用剪貼簿的位置跟功能。
        
    2. **系統**：
        1. 我們下載的 OS 是存放在 vdi 內，透過 SATA 控制器掛載。因此優先運行硬碟。
        2. RAM 分配過少可能導致虛擬機無法開機。
        3. CPU 核心數可以根據虛擬機的任務需求進行調整。
        
        過高的設置會消耗宿主機資源，建議在資源允許範圍內合理設置。
        
    3. **存放資訊**：
        1. IDE 通常用於掛載 ISO 光碟映像檔(軟體)，Guest Additions ISO 用來解決共用剪貼簿的問題。
        2. SATA 提供更好的效能，適合現代作業系統(Windows 7/10、Ubuntu)。
        
        透過 SATA 控制器掛載的 VDI 檔案，建議將其存放在固態硬碟（SSD）上，以提升虛擬機的效能和讀寫速度。
        
    4. **網路介面卡**：
        1. **NAT 模式**：虛擬機透過宿主機的網路進行連線，但無法與區域網內其他設備直接通訊。
        2. **橋接模式**：虛擬機直接連接到實體網路，能獲取獨立 IP 位址，適合需要與區域網內設備通訊的場景。但可能受網路管理限制。
        
        橋接模式碰到公司網路限制等問題，無法分享 IP 給虛擬機。
        
        因此這部份選擇預設 NAT 網路，並用 USB 直通技術將設備連接到虛擬機，繞過網路配置限制。
        
    5. **USB**：
        
        USB 3.0，你必須安裝 VirtualBox Extension Pack。
        USB 直通設定使得同一個裝置在同一時間只能被一台機器使用。因此測試時需要在 Host 機 和 Guest 虛擬機 之間手動切換控制權限。
        
    6. **共用資料夾**：
        
        添加 host 機與 guess 機內調用資料的路徑，選用自動掛載。
        
        共用資料夾建議分配一個乾淨的磁碟或資料夾給虛擬機使用，這樣可以有效隔離 Host 機與 Guest 機的測試資料，方便資源管理並避免測試時互相干擾。
        
    7. 結論：
    UI 自動化測試是順序性執行的操作，一台設備在同一時間只能處理單一操作序列。因此，虛擬機建議設置為單核 CPU 運行，減少資源競爭，並透過 USB 直通技術 提高測試的穩定性與準確度。
    
    ![image.png](../images/VM_14.png)
    
    ![image.png](../images/VM_15.png)
    
    ![image.png](../images/VM_16.png)
    
    ![image.png](../images/VM_17.png)
    
    ![image.png](../images/VM_18.png)
    

### 5. 新增使用者

新增使用者前，請確認已更換映像檔的預設密碼。

![image.png](../images/VM_20.png)

![image.png](../images/VM_21.png)

進到主畫面後選擇 Settings > Users > Unlock 來解鎖新增使用者

![image.png](../images/VM_22.png)

![image.png](../images/VM_23.png)

點擊右上方的 Add User，並新增自己的使用者帳密

![image.png](../images/VM_24.png)

![image.png](../images/VM_25.png)

設定完成後，點擊右上角的開機鍵，切換帳號

![image.png](../images/VM_26.png)

<aside>
💡

**FAQ：啟動 VM** 錯誤

</aside>

> Q1: 為甚麼啟動 VM 但是進到 initramfs 提示符?
> 

> A: 可能是 1. VDI 掛載硬碟路徑錯誤 或2. VDI UDID 名稱重複，重新生成 VDI 文件的 UDID 並重新掛載通常可以解決問題。
> 

![image.png](../images/VM_27.png)

### 6. **Guest Additions iso &**  VirtualBox Extension Pack

> 解決解析度調整、共用剪貼簿、共用資料夾等問題
> 

點擊上方的裝置 > 插入 Guest Additions CD 映像

![image.png](../images/VM_28.png)

會自動開始運行安裝，安裝結束會多出一個光碟機標示

![image.png](../images/VM_29.png)

![image.png](../images/VM_30.png)

這時應該可以透過 檢視 > 虛擬桌面 調整解析度

![image.png](../images/VM_31.png)

如果碰到找不到光碟機的問題會導致 Guest Additions 無法載入

這時需要去 VirtualBox 管理員頁面設定 VM，在存放位置新增一個虛擬光碟機後在重複上述操作

![image.png](../images/VM_32.png)

### 7. 共享資料夾

要讓資料能在 host 機與 guest 機能傳輸可以掛載共享資料夾功能

開啟一個 terminal 窗口，輸入

```bash
sudo usermod -aG vboxsf $USER
```

先登出使用者，重新登入後輸入 `groups`查看有無包含 vboxsf 群組

![image.png](../images/VM_33.png)

![image.png](../images/VM_34.png)

在機器資料夾內加入測試腳本的根目錄資料夾 

並打開 1.自動掛載 跟 2.設為永久 兩個選項

![image.png](../images/VM_35.png)

![image.png](../images/VM_36.png)

可以在 Files 內察看剛剛新增的資料夾

![image.png](../images/VM_37.png)

### 8. 設定自動登入與 sudo 權限

可以從 GUI 開啟 Automatic Login

![image.png](../images/VM_38.png)

或是透過 CLI 編輯 gdm3 設定檔

`sudo nano /etc/gdm3/custom.conf`

AutomaticLogin 改為登入的 “username”

![image.png](../images/VM_39.png)

不要設定 `%sudo ALL=(ALL) NOPASSWD: ALL`。這會讓 sudo 群組成員執行任何管理命令時都不必輸入密碼，增加帳號遭入侵後的影響範圍。保留系統預設的 sudo 密碼驗證；若自動登入，只限隔離的個人測試 VM，且不要存放正式環境憑證。

以下截圖是舊設定中的不安全範例，請勿照圖加入 `NOPASSWD: ALL`：

![不安全的 sudoers 設定範例，請勿照做](../images/VM_40.png)

![image.png](../images/VM_41.png)
