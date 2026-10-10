# BeeAI Framework

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-beeai-framework-cell-ad04/reviews/beeai-framework.md)
>
> Cell ID：[https://github.com/i-am-bee/beeai-framework](https://github.com/i-am-bee/beeai-framework)
>
> Status：`untried`
>
> Category：Python／TypeScript multi-agent framework（Requirement Agent）
>
> Last updated：2026-10-10

## 產品介紹

BeeAI Framework 是開源的 agent 函式庫，用來在 Python 或 TypeScript 裡寫會推理、呼叫工具、互相交接的 agent。開發者用 `pip install beeai-framework` 或 `npm install beeai-framework` 裝進自己的程式，再在程式裡組 agent、規則、工具和流程。它服務的是要寫自動化的工程師，不是坐在桌面 ADE 裡看終端機的人。文件在 [framework.beeai.dev](https://framework.beeai.dev)。本次只讀 repo 的 README、LICENSE 與現行文件，沒有安裝。

這個 Cell 是使用者點名的 framework repo。Canonical URL 仍是 `https://github.com/i-am-bee/beeai-framework`，GitHub API 顯示未封存，預設分支 `main`，授權 SPDX 為 Apache-2.0，首頁指向文件站。沒有看到 repo 轉到別的組織。README 寫這是 BeeAI 專案貢獻者開發、屬於 Linux Foundation AI & Data 計畫；同一頁的法律聲明寫程式由 IBM 以開源專案提供（不是 IBM 產品），IBM 不保證品質或安全，也不會繼續維護。GitHub 顯示這個 repo 在 2026-10-10 仍有 push。誰在日常維護，尚未確認。

先前有一份簡介把這個 repo 收成「observability + logging」。那兩樣都在，但不是產品本身。README 的功能表把 Observability 寫成用事件、日誌和錯誤處理來看 agent 行為；文件站另有獨立的 Observability 頁（OpenInference／OpenTelemetry，目前只寫 Python）和 Logger 頁。主體是 agent、requirement 規則、工具、記憶、workflow，以及把 agent 用協定送出去。

根 README 沒有把 BeeAI Platform 寫進來。文件站有一篇 [Agent Stack](https://framework.beeai.dev/integrations/agent-stack) 整合：一個用來發現、執行、組合各框架 agent 的平台，BeeAI Framework 可以當客戶端去叫它，也可以把自己的 agent 掛上去。TypeScript 範例註解仍寫只支援 BeeAI platform v0.2.xx。公開文章 [Introducing Agent Stack](https://beeai.dev/blog/introducing-agent-stack) 說舊名 BeeAI Platform 已改名 Agent Stack，repo 指向 `i-am-bee/agentstack`。那是另一個產品。本次沒有打開 Agent Stack repo，也沒有跑它。

## 主要 Features

### Requirement Agent：用規則卡住工具順序

文件把 Requirement Agent 寫成目前建議的 agent。開發者宣告 `ConditionalRequirement`：第一步必須先想、某個工具至少叫一次、必須在另一個工具之後、不能連續呼叫、叫滿次數就不能再用。每一輪呼叫模型之前，框架把這些規則收成「這個工具現在準不準、要不要藏起來、是不是強制、能不能停」。衝突時，禁止蓋過其他規則；多條強制規則以較高 priority 為準。`ToolCallChecker` 用來擋重複的工具呼叫循環。最終回答也被當成一種工具，所以「還沒做完不準停」可以寫成規則。

同一頁還有 `AskPermissionRequirement`：昂貴或會破壞資料的工具，預設在終端機問人 yes／no，也可以換成自己的 handler。可以記住選擇，或把拒絕的工具永久關掉。TypeScript 那段註明尚未實作。Agents 總頁寫 Requirement Agent「目前只有 Python」；Requirement 專頁卻有 TypeScript 範例。哪一頁代表現況，尚未確認。

ReAct Agent 與 Tool Calling Agent 仍在，Agents 頁寫它們之後不會再積極維護。Lite Agent 只有 Python，預設沒有框架自己的 system prompt，給想從空白模型往上蓋的人。

### 交接、Workflow，以及人給的期望產出

多個專家不是一張組織圖，而是工具。`HandoffTool` 把另一個 Requirement Agent 包成主 agent 可呼叫的工具，附上名字和說明。README 的例子是主 agent 把一般知識交給 Knowledge Specialist、把天氣交給 Weather Specialist。TypeScript 的 handoff 範例在 Agents 頁標成 COMING SOON。

Workflow 是另一條線：狀態是一份型別化的資料，步驟讀這份狀態，再回傳下一步的名字、`NEXT`、`SELF`（再做一次）或 `END`。步驟可以嵌另一個 workflow。`AgentWorkflow` 則是具名的研究員、天氣、彙整者，每人有 role、instructions 和工具；一次 run 送進多段 input，後段可以帶 `expected_output`。Workflows 頁的說明把這種組合稱作平行處理不同面向。程式是不是真的同時跑，尚未確認。Agents 頁另寫 workflows 仍在改建，並指向一份 V2 提案。歡迎頁還寫可以用 YAML 做宣告式編排，本次讀到的 Workflows 頁是程式裡的 `add_step`，沒有 YAML 範例。YAML 是否仍是現行做法，尚未確認。

單次 `run` 還可以給 `expected_output`（文字、Pydantic 或 JSON schema）、`backstory`，以及重試與迭代上限。這是這一次執行的契約，不是長期目標系統。

### 記憶是對話策略，不是共用事實庫

Memory 頁寫的是對話訊息：角色是 user、assistant、system。四種策略：`UnconstrainedMemory` 全留；`SlidingMemory` 只留最近幾則；`TokenMemory` 用 token 預算擠掉舊訊息；`SummarizeMemory` 把整段對話壓成一則摘要。Agent 和 workflow 的狀態都可以帶同一份 memory。ReAct 文件寫中間步驟內部用 `TokenMemory`。可以做成唯讀複本，也可以 `reset`。這是這一輪對話怎麼塞進 context，不是跨專案的組織記憶。召回品質尚未實測。

### 軌跡、日誌、可觀測性

Middleware 掛在一次執行的中間，聽元件發出的事件（開始、成功、錯誤，或自訂名字），用來記日誌、過濾或做安全檢查，不用改 agent 本體。文件特別說這是程式裡的攔截器，不是市面上那種「agent 平台」。`GlobalTrajectoryMiddleware` 把巢狀呼叫印到主控台，可以用縮排看呼叫堆疊，也可以只記某幾類工具。

Logger 是包在 logging 上的記錄器，Python 與 TypeScript 都有，多一個 TRACE 等級，環境變數 `BEEAI_LOG_LEVEL` 調等級。記 agent 對話時會加上人和機器人圖示。這是開發者看的文字日誌。

Observability 頁是另一件事：另裝 `openinference-instrumentation-beeai`，用 OpenTelemetry 把 agent、工具、模型、embedding、workflow 步驟送出去。頁面寫目前只有 Python。後端例子是 Arize Phoenix、Langfuse、LangSmith，或任何吃 OpenTelemetry／OpenInference 的系統。歡迎頁寫「native OpenTelemetry」；實際要先裝這個 instrumentor，並在匯入框架元件之前呼叫設定。沒有裝過，送得出去的欄位尚未確認。

### 工具、沙箱、權限、送出與快照

工具頁列出思考、交接、天氣、搜尋、Wikipedia、向量檢索、MCP、OpenAPI，以及一組寫程式用的原語。`PythonTool` 與 `SandboxTool` 要另外的 `beeai-code-interpreter`，預設打到本機 `http://127.0.0.1:50081`。文件寫程式在隔離環境跑；`PythonTool` 才能碰到檔案。`ShellTool`、讀檔、改檔的預設後端是本機子程序和本機檔案系統，不是那個 code interpreter。後端可以換成沙箱或遠端；Glob 和 Grep 寫成不可替換。隔離是否真的擋住本機，尚未實測。

Serve 把工具、agent、chat model 或 runnable 掛到伺服器。安裝額外套件後可走 A2A、MCP、Agent Stack、Zed 的 ACP；watsonx Orchestrate 與 OpenAI 相容 API 寫在主套件裡。這是把程式裡的 agent 送到別的協定，不是辦公室畫面。

Serialization 用快照保存 memory、agent、工具，之後再還原。該頁的 Python 例子多處寫 Example coming soon，TypeScript 有記憶體序列化範例。跨程序還原是否完整，尚未確認。

## 主打賣點

- **它加上的是「工具必須照規則走」。** role 和 instructions 仍在，但文件最想讓人記住的是 Requirement：不同模型也得先想、再查、叫滿次數才能停。這和只在 prompt 裡拜託模型「請記得用工具」不同。規則是程式，不是另一層聊天。
- **和 CrewAI 相同的是多角色與交接，執行方式不同。** CrewAI 的 crew 是 role、task、process。BeeAI 的專家是 `HandoffTool` 裡的另一個 agent，或 `AgentWorkflow` 裡的一個具名步驟。兩邊都是函式庫，都不是一間一直開著的辦公室。
- **可觀測性是執行軌跡的出口，不是這個框架的身份。** 事件、Logger、OpenTelemetry 讓人在主控台或外部後端看見呼叫。歡迎頁的「可插拔可觀測性」是這組能力的包裝。把它當成產品本體，會漏掉 requirement、交接和權限詢問。
- **雙語言對等是歡迎頁的宣稱，模組頁沒有對齊。** 歡迎頁寫 Python 與 TypeScript 功能對等。Agents 頁寫 Requirement 與 Lite 只有 Python；Requirement 專頁又有 TypeScript；`AskPermissionRequirement` 與 handoff 的 TypeScript 標成尚未完成；Observability 只寫 Python。以哪一邊為準，尚未確認。

## 使用情境

### 逼小模型先想再查，答完才準停

- 適合誰：想用本機小模型，又怕它跳過工具的開發者。
- 在什麼情況使用：Requirement Agent 強制第一步 `Think`，天氣至少查一次，搜尋必須發生在天氣之後，並限制呼叫次數。
- 帶來的價值：順序寫在規則裡，換模型時不必重寫一整段「請記得依序」的 prompt。規則是否真的壓住小模型，尚未實測。

### 一個主 agent 把問題交給專家

- 適合誰：想把知識和天氣拆開、又希望對外只有一個入口的人。
- 在什麼情況使用：兩個 Requirement Agent 各帶自己的工具，主 agent 用 `HandoffTool` 決定問誰。
- 帶來的價值：交接是一次工具呼叫，軌跡 middleware 可以把這次呼叫印出來。專家是否真的被叫到，要看規則有沒有強制，文件沒有保證模型一定會交接。

### 破壞性工具先問人

- 適合誰：agent 能改資料或呼叫昂貴工具，需要人點頭的人。
- 在什麼情況使用：Python 的 `AskPermissionRequirement` 包住刪除或讀取，預設在終端機問；也可以換成自己的審核函式。
- 帶來的價值：人的關卡是規則的一種，不是事後看 log。TypeScript 尚未提供同一個 requirement。Shell 與本機檔案工具的預設後端不在 code interpreter 裡，權限邊界要自己接。

## 我們可以學什麼

- 值得借鑑的 product idea：員工不只是 role 和 instructions。崗位上還貼著規則：先想、某工具至少做一次、做完才能下班、危險動作要等人蓋章。目標是這一次 `run` 的 `expected_output`，不是公司的年度目標。專家是可以被呼叫的同事，不是另一套聊天視窗。
- 值得借鑑的 interaction / workflow：順序是規則點亮的步驟，不是人肉排程。交接是主桌把資料夾送到專家桌。Workflow 的狀態是桌上那份會改寫的文件，`SELF` 是同一張桌子再做一輪。記憶策略決定抽屜怎麼清：全留、只留最近幾張、超過 token 就撕舊頁、或改寫成一頁摘要。軌跡是紙帶，OpenTelemetry 是把紙帶送到外面的監控牆。人的介入是許可對話，不是事後審查。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。每個 Requirement Agent 是固定工位，名牌寫 name、role。規則刻在工位旁的牌子上，沒滿足的步驟亮著，不能把完成章蓋下去。`HandoffTool` 是工位之間的傳票，專家在自己的桌子上處理再送回。`AskPermissionRequirement` 是人必須走到那張桌子按的章。Memory 是工位抽屜，滑動視窗會把舊紙條推出抽屜。Workflow 是地板上的箭頭，狀態紙跟著箭頭走。Code interpreter 是玻璃隔間；Shell 和本機讀寫的預設後端是辦公室外的真實機器，平面圖上要畫成不同的門。Logger 和 OpenTelemetry 是牆上的記錄帶，不是這間辦公室本身。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。主 agent 在房間中間，知識專家和天氣專家在側間，你看得到誰在場、誰在哪一桌。規則沒滿足時，這個人不能離開該步驟。交接發生時，主 agent 把問題帶到專家桌，或把資料夾送過去，做完再走回來。人要批准刪除時，站在那張桌子前面，不按章工具不會動。對話記憶是桌上的筆記本；摘要記憶是只留一頁的改寫。Workflow 是你跟著狀態資料夾走過的房間。隔離的 code interpreter 是上鎖的實驗室；預設的本機 Shell 是直接通向辦公室外面的門。重點是看見約束和人在哪裡介入，不是把 trace 做成懸浮的立體圖。
- 需要重新設計的地方：這個框架的呈現是 log、事件和外部 trace 後端。空間辦公室要另做「誰在哪一桌、哪條規則擋住他」。不要把 OpenTelemetry 儀表板當成 2D／3D workspace。Python／TypeScript 對等、Requirement 是否已有 TypeScript、workflow 是否已進入 V2、YAML 編排是否還在，都尚未確認，先不要做進空間介面。ReAct 與 Tool Calling 被標成不再積極維護，座位種類不要照舊型 agent 來設計。Shell 預設打到本機，不能畫成和 code interpreter 同一間實驗室。Agent Stack 是部署與發現平台，不是這個 framework repo 的辦公室。

## 初步看法

- 最有價值的部分：把工具順序和「能不能停」寫成 requirement，再加上 handoff 與詢問許可。這三件事都能在辦公室裡變成看得見的約束。
- 最大限制或疑問：它是給開發者呼叫的函式庫。文件對雙語言支援互相矛盾。可觀測性要另裝 Python instrumentor。IBM 聲明不再維護，但 repo 在本次研究當天仍有 push，實際維護者尚未確認。
- 是否值得進一步研究或親自體驗：值得，尤其是規則如何在畫面上擋住一個員工、交接時人怎麼看見專家被叫到、以及許可章和本機 Shell 的門要怎麼分開。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://framework.beeai.dev
- Repository：https://github.com/i-am-bee/beeai-framework
- Documentation：https://framework.beeai.dev/introduction/welcome （索引 https://framework.beeai.dev/llms.txt）
- License：repo `main` 的 `LICENSE` 為 Apache License 2.0（GitHub API 的 SPDX 為 Apache-2.0）
- README（`main`）：Python／TypeScript、Requirement Agent、Workflows、Memory、Observability、Serve、IBM 法律聲明、Linux Foundation AI & Data
- 已讀文件頁：Welcome、Agents、Requirement Agent、Workflows、Memory、Middleware、Observability、Logger、Tools、Serve、Serialization、Agent Stack 整合
- 命名區隔（未執行）：https://beeai.dev/blog/introducing-agent-stack
- GitHub API（2026-10-10）：`i-am-bee/beeai-framework` 未封存，`pushed_at` 為 2026-10-10T08:58:28Z
