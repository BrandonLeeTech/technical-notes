# Docker 測試環境建置

---

> 在 Windows 上使用 WSL 2 執行 Docker 時，容器存取 USB 裝置可能受到限制。WSL 2 的 Linux 環境與主機硬體隔離；其 NAT 網路也讓主機與 Linux 環境之間的連線設定較不直接。
> 

若測試需要直接連接 USB 裝置，可以改在 Linux 環境中執行 Docker。常見做法有兩種：

1. 在電腦上安裝 Windows 和 Linux，並以雙系統方式啟動。
2. 建立使用 Linux 核心的虛擬機。


## 安裝 Docker

---

> `Dockerfile` 用來定義映像所需的環境與依賴；Docker Compose 則透過 YAML 設定多個容器服務及其協作方式。
> 

由於 Docker 容器不一定能直接控制主機的 USB 連接埠，本文採用 Linux 虛擬機執行 Docker，以便標準化測試環境。

<aside>
💡

可使用前一篇設定的共用剪貼簿，在虛擬機內貼上安裝指令。

</aside>

### 1. 更新系統套件

開啟終端機並更新系統套件。執行 `sudo` 指令時，系統會要求輸入使用者密碼。

```bash
sudo apt update 
sudo apt upgrade -y
```

![image.png](../images/Docker_1.png)

### 2. 安裝必要套件

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```

![image.png](../images/Docker_2.png)

### 3. 新增 Docker GPG 金鑰與套件來源

執行以下指令，加入 Docker 的 GPG 金鑰與套件來源：

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

```

![image.png](../images/Docker_3.png)

### 4. 安裝 Docker Engine

更新套件列表，然後安裝 Docker Engine（Community Edition）：

```bash
sudo apt update
sudo apt install docker-ce -y
```

![image.png](../images/Docker_4.png)

### 5. 啟動 Docker 並設定開機自動啟動

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

![image.png](../images/Docker_5.png)

### 6. 確認 Docker 安裝結果

```bash
docker --version
sudo docker run hello-world
```

若輸出中出現 `Unable to find image`，代表本機尚未有該映像，Docker 會嘗試從 Docker Hub 下載。下載並執行 `hello-world` 後若看到成功訊息，即表示 Docker Engine 已可正常執行。

![image.png](../images/Docker_6.png)

### 7. 授權一般使用者執行 Docker

預設情況下，執行 Docker 指令需要 `sudo`。若要以一般使用者執行，可將帳號加入 `docker` 群組：

```bash
sudo usermod -aG docker $USER
```

登出後重新登入，或重新啟動系統，讓群組設定生效。

再執行 `groups`，確認輸出中包含 `docker` 群組。

![image.png](../images/Docker_7.png)

### 8. 安裝 Docker Compose

Docker Compose 用來定義及管理多個容器服務。以下指令會下載並安裝 Compose：

```bash
sudo curl -L "[https://github.com/docker/compose/releases/download/v2.25.0/docker-compose-$](https://github.com/docker/compose/releases/download/v2.25.0/docker-compose-$)(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

安裝完成後，執行以下指令確認 Compose 版本：

```bash
docker-compose --version

```

![image.png](../images/Docker_8.png)



## 使用 Docker Compose 啟動測試容器

---

> 此測試環境使用簡易的容器架構。相關範例可參考 [`/SQATP/vm.git`](http://122.147.190.35/SQATP/vm.git)。
> 

架構中的檔案分工如下：

1. `Dockerfile`：安裝必要工具並建立容器映像。
2. `docker-compose.yml`：定義要啟動的服務及其設定。
3. `start.sh`：由 Compose 啟動，負責初始化 Appium 測試流程。

<aside>
⚠️

同一個 USB 裝置同一時間只能由一個環境控制。執行 `start.sh` 前，請先將裝置連接到虛擬機。

</aside>

裝置顯示 USB 偵錯授權視窗時，請勾選「一律允許」並確認授權。

虛擬機的共用資料夾通常會掛載在 `/media` 下。

![image.png](../images/Docker_9.png)

以下範例先切換到共用資料夾中的專案根目錄。此例的共用資料夾名稱是 `ubuntu`，專案資料夾名稱是 `VM_worky`：

```bash
cd /media/sf_ubuntu/VM_worky
```

![image.png](../images/Docker_10.png)

使用 `--no-cache` 重新建置映像，並將映像命名為 `worky-test-image`：

```bash
docker build --no-cache -t worky-test-image:latest .
```

![image.png](../images/Docker_11.png)

建置完成後，執行 `docker images` 確認映像已建立。由於映像包含完整測試環境，建議先確認映像可正常啟動，再執行測試腳本中的檢查流程。

![image.png](../images/Docker_12.png)

![image.png](../images/Docker_13.png)

此架構會在容器內啟動 ADB Server 和 Appium Server，讓測試服務與主機分開執行。啟動容器前，請確認主機上沒有其他 ADB 或 Appium 程序占用相關連接埠，例如 `5037` 或 `4723`。

<aside>
⚠️

若容器中的 ADB 無法存取 USB 裝置，可依目前環境的權限需求調整裝置權限。以下是原筆記使用的指令：

```bash
sudo chmod -R 777 /dev/bus/usb
```

</aside>

`docker-compose.yml` 已指定映像名稱，因此建置時請使用相同的名稱與 Tag，例如 `worky-test-image:latest`。

![image.png](../images/Docker_14.png)

映像建置完成後，執行以下指令啟動容器：

```bash
docker-compose up
```

Compose 會依照 `docker-compose.yml` 啟動容器，並執行 `start.sh` 中的初始化流程：

1. 啟動 `adb` 服務
2. 連接設備並檢查狀態
3. 啟動 `Appium`
4. 運行測試腳本
5. 測試完成後關閉 `Appium`

![image.png](../images/Docker_15.png)

![image.png](../images/Docker_16.png)

![image.png](../images/Docker_17.png)
