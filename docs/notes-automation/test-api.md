# API 測試與 Socket 資料管理

---

> 本文示範如何使用本機 Socket Server 管理 API 測試流程中的共用資料，例如 Token。
> 

測試時至少需要一個 Server，並可由多個 Client 同時連線。

1. 在一個終端中啟動 Server。
2. 在其他終端執行測試腳本（Client），發送 API 請求並與 Server 交換資料。

## 專案目錄結構

---

Project/api
├─ employer/          商家端 API
├─ labor/             打工端 API
├─ test/              多支 API 的串接測試
├─ tools/             共用工具，例如 Socket 資料傳遞與 Header 產生
├─ env.py             API 環境變數
├─ start_server.py    本機模擬資料 Server
└─ test_data.py       測試資料，例如註冊電話

## 執行測試

---

1. 開啟終端並啟動 Server：`python -u "c:\Worky_UITest\api\start_server.py”`。

![image.png](../images/API 測試_1.png)

2. 確認測試資料的管理方式：

若主測試流程會串接多支 API，可直接在測試函數中準備所需資料。

若要單獨測試某支 API，則可將測試資料放在獨立檔案中管理，例如 `test_data.py`。

![image.png](../images/API 測試_2.png)

![image.png](../images/API 測試_3.png)

3. 執行測試後，結果會輸出至終端。若需要排查問題，可以查看後端 API Log。

![image.png](../images/API 測試_4.png)

Server 也會記錄資料的寫入與讀取操作，方便確認 Client 和 Server 之間的資料交換是否如預期進行。

![image.png](../images/API 測試_5.png)

## Socket 伺服器設計

---

### 1. socket_data_manager

<aside>
💡

1. 管理伺服器的 IP 與連接埠。
2. 封裝資料讀取與寫入的處理邏輯。
</aside>

Server 啟動時會綁定指定的 IP（`localhost`）和連接埠（`5000`），並提供多個 Client 同時連線的基礎架構。`SocketDataManager` 負責管理共用資料，讓 Client 可以透過 Socket 存取資料。

![image.png](../images/API 測試_6.png)

- **`set_data` 函數**：由 Client 呼叫，將 `SET_DATA` 指令、標籤（`tag`）和資料內容（`data`）傳送至 Server。
- **`handle_set_data` 函數**：由 Server 呼叫，處理收到的 `SET_DATA` 指令，並將資料存入對應標籤的儲存空間。

![image.png](../images/API 測試_7.png)

例如，Client 可以將存取憑證存入 `new_access_token`，並以 `labor_token` 作為標籤，供後續測試查詢。


在 `server_task` 中，程式會依序完成以下工作：

- 使用 `parse_command` 將指令拆解為操作類型（`action`）、標籤（`tag`）和資料（`data`）。
- 依照操作類型執行資料讀取或寫入（例如 `SET_DATA`、`GET_DATA`），再將結果回傳給 Client。

![image.png](../images/API 測試_9.png)

![image.png](../images/API 測試_10.png)

### 2. **start_server**

<aside>
💡

1. 負責啟動 Server 並處理連線。
</aside>

`server_task` 是 Server 的主要處理函數。它會使用 `SocketDataManager` 設定的 IP 和連接埠啟動服務，並處理 Client 連線與資料傳輸。此處使用 TCP（`SOCK_STREAM`）進行通訊。

![image.png](../images/API 測試_11.png)

`start` 函數會在子執行緒中啟動 `server_task`，避免 Server 阻塞主程式。主程式因此仍可執行其他工作，例如等待使用者輸入或執行測試腳本。

![image.png](../images/API 測試_12.png)
