# Octop Harness

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-harness-cell-7618/reviews/octop-harness.md)
>
> Cell ID：[https://github.com/TencentCloud/octop-harness](https://github.com/TencentCloud/octop-harness)
>
> Status：`untried`
>
> Category：Agent runtime library（deepagents production harness）
>
> Last updated：2026-10-08

## 產品介紹

Octop Harness（套件名 `octop-harness`，目前文件版本 1.0.1）是騰訊雲開源的
Python runtime 函式庫。它把 LangChain [deepagents](https://github.com/langchain-ai/deepagents)
的 `create_deep_agent` 包成可部署的 agent：一個 process 裡用
`HarnessAgentManager` 登記多個彼此隔離的 agent，每個 agent 自帶模型、
tools／skills、記憶後端，以及 LangGraph checkpoint。

它服務的是要自己做產品的 host，而不是終端使用者。官方把登入、排程、IM、
Web UI 明確留給上層，例如已收錄的 [Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/octop.md)。
可選 CLI（`octop-harness init` / `chat` / `agent`）只是設定與對話入口。
本筆記依 README、`AGENTS.md` 與 teams／workspace／security 原始碼整理，
尚未安裝或跑過。

## 主要 Features

### 程序內的 agent 名冊

`HarnessAgentManager` 在記憶體裡登記 agent：名稱、tags、metadata、
各自的 `workspace_dir`。呼叫端用 agent id 把 `ChatRequest` 送到指定
thread；同一 thread 延續 checkpoint，也可以中途 `cancel`。這是「同一間
辦公室裡有多位員工」，編排入口是程式，畫面要由 host 自己畫。

### 檔案櫃與執行間分開

Agent 看得到的內容（人設 Markdown、skills、產出）只走 `BackendWorkspace`
這一層。儲存可以是本機目錄、S3、騰訊 COS、阿里 OSS、華為 OBS 或
PostgreSQL。對話紀錄、checkpoint、memory sqlite 則留在 host 檔案系統，
不跟內容櫃混在同一條寫入路徑。

執行面另有 Docker sandbox 與 OpenSandbox（皆為選用 extra）。Docker
預設以 **agent** 為單位開容器，也可改成多個 expert 共用一個 **user**
sandbox，或指定固定 id。官方說明 host 目錄與容器裡的工作目錄**預設不
bind-mount**：agent 改的是容器裡那份樹。關掉 agent 不會自動刪容器，要
明確 `destroy` 才清掉。Linux 上若開 virtual path 且有 `bwrap`，shell
還可以再套一層目錄 jail。這些行為來自原始碼說明，尚未實機確認。

### 人設檔與分層記憶

每個 workspace 用 Markdown 當跨對話記憶：`USER.md` 記使用者偏好，
`MEMORY.md` 記環境與長期約定，`AGENTS.md` 記協作規矩；模板也提到用
`IDENTITY.md` 微調語氣。每輪開始會把存在的檔案注入系統提示，並要求
agent 先讀再改，避免整檔覆蓋。

另外接 [octop-memory](https://github.com/TencentCloud/octop-memory)：
每輪先留下 L0 原始事件，再非同步蒸餾成 L2 原子卡片與 L3 實體頁。召回
在使用者每一輪只凍結一次，附在模型看到的訊息上，讓 system prompt 保持
穩定；checkpoint 保留這次召回快照，聊天紀錄仍存使用者原文。這是官方
對記憶流動的設計，召回品質尚未驗證。

### 同事信箱（peer inbox）

Teams 讓同一 registry 裡的 agent 用 `agent_list` 與 `ask_agent` 互相
叫人。`peer_invoke_mode` 決定這次是同步等結果、丟進背景信箱，或兩者都
開放；單次請求只能把權限收緊，不能臨時打開背景派工。

背景信箱是全 process 一條佇列：同一位被呼叫者的工作串行，不同對象可以
並行。做完後先回到**來源 thread** 寫一段 follow-up，再交給 host 的
`TeamProcessor.on_reply`。函式庫本身不推 IM；誰把回覆送到飛書或網頁，
是 Octop 這類 host 的事。範例把一對一與群組房間走同一條 push，群組只
多寫一份房間紀錄。

### 人的介入點與安全政策

`ask_user_question` 用來問只需回答、不必改檔的決定；無人值守可關掉。
`SecurityPolicy` 把核准、路徑、個資、skill 掃描、shell 參數檢查收成
host 可存檔的設定，建 agent 時展開成 deepagents 的 interrupt 與權限。

預設會擋一組敏感路徑（例如 `.ssh`、`.env`）。工具核准（bash、execute、
寫檔、刪檔等）預設關閉；shell guard 與 skill 掃描預設是警告。個資處理
涵蓋供應商 API key，以及中國大陸手機號與 18 位身分證；其他國家證件
不在範圍內。這些預設來自 `security/models.py`，實際攔截手感尚未測。

### 對 IDE 與工具清單的接縫

ACP stdio server 讓外部 IDE 或終端 agent 驅動這裡的 `HarnessAgent`。
Skills 跟每位 agent 走，低頻工具可以先藏起參數 schema，模型搜到再載入，
載入後沿用同一條 thread。MCP、多供應商模型路由（含可編輯預設）與
選用的圖片／影片生成，都掛在同一層組裝上，產出放在 workspace 的
`generated/`。

## 主打賣點

- 它想被記住的是：**deepagents 負責 agent loop，Harness 負責生產環境的家**——模型可換、記憶可搬、檔案櫃可換、同事怎麼互叫、什麼要人點頭，都收成可替換的層，而不是再做一個聊天產品。
- 和 [Sandcastle](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/sandcastle.md) 相比，兩邊都叫 harness，但座標不同。Sandcastle 的單位是 git worktree、branch 與 coding CLI sandbox；Octop Harness 的單位是 process 內的 agent、Markdown 人設、儲存後端與 peer thread。
- 和 [Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/octop.md) 相比，Octop 是多使用者、IM、專家團隊的產品；本庫是它呼叫的 runtime。主持人排班、通道、登入不在這裡。
- 模型路由、MCP、skills、終端 chat 是常見包裝。真正值得看的是分層 I/O（內容只走 workspace facade）、信箱把回覆送回來源 thread，以及 sandbox 範圍（每人一間、多人共用、固定 id）跟 host 檔案分開。

## 使用情境

### 自架助理要長出多位專家

- 適合誰：在做 Octop 這類 host，需要每位專家有自己的檔案、記憶與模型。
- 在什麼情況使用：一個 manager 登記多個 agent，用 tags／metadata 區分角色，各自一份 `workspace_dir`。
- 帶來的價值：專家是可建立、可移除、可依 thread 續聊的 runtime 物件，產品層不必重寫 agent loop。

### Agent 互相派工，結果回到原對話

- 適合誰：希望研究同事、分析同事在背景做事，使用者仍留在原本那條對話。
- 在什麼情況使用：`ask_agent` 丟進 inbox，完成後 follow-up 寫回來源 thread，再由 host 推到 IM 或網頁。
- 帶來的價值：交接有 job 狀態（queued、running、replying、done、failed、cancelled），而且函式庫不綁某一家聊天頻道。

### 把執行關進容器，人設與 checkpoint 留在 host

- 適合誰：要讓 agent 跑 shell 或改檔，又不想預設把 host 目錄掛進容器。
- 在什麼情況使用：選 Docker sandbox，範圍設成單一 agent 或同一使用者的多個 expert 共用。
- 帶來的價值：檔案櫃、對話狀態、執行間三件事可以分開擺；人仍要決定何時銷毀容器。

## 我們可以學什麼

- 值得借鑑的 product idea：把「員工」定義成 registry 裡的一筆——名字、角色 metadata、自己的檔案櫃、自己的 thread。人設用短 Markdown 放在桌面上（使用者、記憶、規矩、語氣），系統檔（skills、session、sqlite）可以收到抽屜裡，避免桌面被 runtime 檔案淹沒。
- 值得借鑑的 interaction / workflow：同事協作是信箱，不是再新開一個無限層 chat。同步是站在對方桌邊等；非同步是放下紙條，回覆貼回**你原來的對話**。人的介入拆成兩種：只要回答的 `ask_user_question`，以及會改世界的工具核准（同意、拒絕、改過再跑、改用回應）。
- 在 2D workspace 裡會變成什麼：俯視平面辦公室，每一格是一位已登記的 agent，門牌是名稱與 tags。桌上攤著 `USER.md`、`MEMORY.md`、`AGENTS.md`。格子角落標檔案櫃種類（本機抽屜或雲端櫃），這只是標籤，地圖本身仍是這層樓。信箱是格與格之間的紙條，顏色表示 queued 到 done。Docker sandbox 是玻璃隔間：agent 範圍是單人隔間，user 範圍是幾位 expert 共用的實驗室。ACP 是隔間側門，IDE 從那裡進來指揮同一位員工。Context 用量是桌上的量表，compaction 是把舊對話收成摘要夾。
- 在 3D workspace 裡會變成什麼：可走進的辦公室。你走到某位員工的座位，看得到人設筆記與正在進行的 thread。一位員工叫另一位時，要麼留在對方座位等到答覆，要麼派一個信差離開，稍後把結果送回自己座位上的原對話。需要核准 shell 或寫檔時，員工停在門口拿著單子等你蓋章。密封的 sandbox 房間可以從窗外看執行，房間裡的檔案預設不是走廊上的 host 地板。這是「在場的員工、桌上的共用記憶、座位之間的信」，上層的 Octop 才負責接待櫃台與 IM；這裡不畫公司組織圖，也不做 ADE 側欄。
- 不值得照搬或需要重新設計的地方：`BackendWorkspace` 是儲存 facade，進到我們的 2D／3D 辦公室時要畫成座位與檔案櫃，不能把 S3 bucket 叫成 workspace。工具核准預設關閉、shell guard 預設只警告，對看得到副作用的辦公室太鬆，應改成座位上看得見的政策。Peer inbox 沒有目標、預算或匯報線，那些屬於 Octop 的 AgentTeams 或 Paperclip 那一層。個資規則偏中國大陸證件，可學的是「政策物件」，不是那兩條偵測器。容器預設不隨 agent 關閉而消失，辦公室裡要有明確的「這間實驗室還開著」狀態，避免人以為人走了房間就拆了。

## 初步看法

- 最有價值的部分：用一個 process 內的名冊，把員工、檔案櫃、checkpoint、同事信箱與核准政策拆開，而且刻意把產品 UI 留在 host。這很適合當未來辦公室的執行層。
- 最大限制或疑問：沒有自己的空間介面；sandbox 與 host 檔案是否真的隔離、信箱在成員忙碌或失敗時的體感，都還沒跑過。和 Octop 產品的邊界在文件裡寫得很清楚，實際 host 會不會把編排邏輯漏回這層，尚未確認。
- 是否值得進一步研究或親自體驗：值得當 runtime 參考。若要動手，放在 `/workspace-labs/octop-harness`，只驗證「兩位 agent、一條 async `ask_agent`、回覆回到來源 thread」以及 Docker sandbox 是否未 bind-mount。不必把 CLI 與全部儲存後端測完。

## 後續補充（選填）

Not tried yet.

## Sources

- Repository：https://github.com/TencentCloud/octop-harness
- PyPI：https://pypi.org/project/octop-harness/（文件版本 1.0.1，2026-10-03）
- 套件手冊：repo 根目錄 `AGENTS.md`（模組分層與「library, not application」邊界）
- 協作與儲存：`src/octop_harness/teams/`、`backends/workspace.py`、`backends/docker_sandbox.py`、`security/models.py`
- 相關產品 Cell：[Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/octop.md)
- 相關套件：https://github.com/TencentCloud/octop-memory · https://github.com/langchain-ai/deepagents
