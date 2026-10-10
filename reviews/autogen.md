# AutoGen

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autogen-cell-9381/reviews/autogen.md)
>
> Cell ID：[https://github.com/microsoft/autogen](https://github.com/microsoft/autogen)
>
> Status：`untried`
>
> Category：多代理框架（AgentChat／Core；維護模式）
>
> Last updated：2026-10-10

## 產品介紹

AutoGen 是 Microsoft Research 做出來的開源框架，讓開發者用程式組一群會自己做事、也能和人一起做事的 agent。現行 README 把這個 repo 標成**維護模式**：不再加新功能，之後由社群維護。新專案它請人改去 [Microsoft Agent Framework](https://github.com/microsoft/agent-framework)；舊使用者則走 [AutoGen 遷移指南](https://learn.microsoft.com/en-us/agent-framework/migration-guide/from-autogen/)。這個 Cell 的家仍是 `https://github.com/microsoft/autogen`。Agent Framework 是另一個 repo，是後繼者。

現在還留在這個 repo 裡的，是分層的程式框架，主要給要寫多代理應用的工程師。高階從 AgentChat 組 agent 和 team，低階用 Core 自己送訊息。另外有擴充套件（模型客戶端、程式執行、MCP）、免寫程式的 AutoGen Studio，以及評測用的 Bench。文件站在 [microsoft.github.io/autogen](https://microsoft.github.io/autogen/stable/)。本次只讀 `main` 的 README、`LICENSE`、`LICENSE-CODE` 與現行 stable 文件，沒有安裝。Python 最新發行標籤是 `python-v0.7.5`（2025-09-30）。README 要求 Python 3.10 以上。

## 主要 Features

### Core、AgentChat、Extensions

README 仍把框架分成三層。**Core** 負責訊息、事件驅動的 agent，以及本地或分散的 runtime，並寫明 Python 與 .NET 都能走這層。**AgentChat** 蓋在 Core 上，是有預設行為的 API：現成的 agent，以及幾種現成的 team。它最接近 v0.2 使用者熟悉的寫法，現行文件仍用它教雙人對話和群組對話。**Extensions** 放具體能力：OpenAI 等模型客戶端、程式執行、MCP、Magentic-One 的專職 agent，以及 gRPC runtime。AgentChat 的 `AssistantAgent` 是應用程式自己建立的；要放進 Core runtime，文件要求再包一層 `RoutedAgent`，由 runtime 管生命週期。

### 對話團隊：誰下一句說話

現行 AgentChat 文件仍把「多人看同一段對話」教成 team。v0.2 的 `GroupChat` 類別出現在 0.2 到 0.4 的遷移指南裡；下面這些是 stable 文件現在教的形狀。

**RoundRobinGroupChat**：參與者共用同一段上下文，依名單輪流發言，每一句廣播給所有人。文件用它示範寫手與評論者：評論者說出約定字（示例是 `APPROVE`）就停。

**SelectorGroupChat**：每一句之後，由模型看對話、名字和 description 選下一位；也可以換成自己的選擇函式，或先用候選函式縮小名單。預設不連續點同一個，除非只剩一人。

**Swarm**：沒有中央選人。每個 agent 用工具呼叫交出 `HandoffMessage`，下一位就是這張交接單的對象。上下文仍是同一段對話。文件寫這需要模型會呼叫工具。

**MagenticOneGroupChat**：一位 Orchestrator 先建任務帳（計畫、事實、猜測），每一步再寫進度帳，把子任務派給一個人，停滯太久就改計畫。專職座位含 WebSurfer（瀏覽器）、FileSurfer（本地檔案）、Coder、ComputerTerminal。`MagenticOne` 輔助類把這組綁在一起，並可在執行程式前呼叫 `approval_func` 問人。

**GraphFlow**：用 `DiGraph` 規定誰可以接著做，支援順序、平行、條件與迴圈。文件標成實驗功能，API 可能再變。

README 的多代理示例還有另一條：用 `AgentTool` 把專家包成工具，由一個助理決定何時呼叫。那是「一個人叫專家」，和上面「大家坐在同一場對話裡」是兩種編排。

### 人的介入、停輪與狀態

人進場有兩條，文件都還在。**進行中**：把 `UserProxyAgent` 放進 team。輪到它時，控制權交給應用程式，團隊停住等回答。RoundRobin 照名單輪到它；Selector 由選人規則決定何時問人。文件寫明這會堵住執行，這段狀態不穩，不能存、也不能恢復，只適合短的立刻回覆，例如按核准。

**這一輪結束後再回**：`max_turns`、`TextMentionTermination`、`HandoffTermination`，或從外面呼叫 `ExternalTermination`。`HandoffTermination` 在 agent 交出指向 user 的 `HandoffMessage` 時停輪，狀態可以存下來，人晚點把下一句話送進下一次 `run`。Swarm 要恢復時，文件要求下一則 task 是指向下一個 agent 的 `HandoffMessage`。`max_turns` 目前文件寫只支援 RoundRobin、Selector、Swarm。

Team 和 agent 有 `save_state`／`load_state`。Team 的狀態含每位 agent 的模型上下文，以及輪到誰。`reset` 會清掉這段對話。相關的下一題可以不 reset，直接再跑。

### Runtime、工具與程式執行

本地開發用嵌入的 `SingleThreadedAgentRuntime`：先 `register` 一個 agent 類型和工廠，runtime 在第一次把訊息送到某個 `AgentId` 時才建立實例。分散 runtime 文件標成實驗功能：一個 `GrpcWorkerAgentRuntimeHost` 加多個 worker，用主題發布訊息；跨語言時訊息要走共用的 protobuf。安裝額外依賴是 `autogen-ext[grpc]`。

模型、MCP、程式執行住在 Extensions。README 的 MCP 示例把 `McpWorkbench` 掛在助理上，並提醒只連接信任的伺服器。Magentic-One 文件要求把會碰網頁和檔案的任務放進容器，並可在跑程式前等人核准。`python-v0.7.5` 的發行說明寫加入安全警告，並把預設改向 Docker 指令執行器。尚未對照原始碼，也尚未實測。

記憶是另一條線。`Memory` 在下一步之前把查到的條目寫進模型上下文。文件給的 `ListMemory` 依時間把事實附上去。向量庫等要自己實作這個協定。這是塞進下一次 prompt 的備忘，和 team 那條共用對話是分開的。召回品質尚未實測。

### Studio 與 Bench

**AutoGen Studio** 是建在 AgentChat 上的低程式介面：Team Builder 用 JSON 或拖放組 team，Playground 串流訊息、畫控制轉移圖、可用 UserProxy 互動並暫停，Gallery 匯入別人做的元件，Deployment 把 team 輸出成 Python、端點或 Docker。文件和 README 都寫它是研究用原型，用來快速試流程，要上線應自己用框架做驗證與權限。Playground 是儀表板。**AutoGen Bench** 是評測 agent 表現的套件。Studio 與 `python-v0.7.5` 是否同一套相容版本，尚未確認。README 的 .NET 欄指向 Core 文件；.NET 是否有和 Python 相同的 AgentChat team，尚未確認。

## 主打賣點

- **它讓人記住的是「同一段對話」加上「下一句誰說」的幾種規則。** 輪流、模型選人、本人交接、指揮的任務帳／進度帳、以及實驗性的有向圖，都還在現行 AgentChat 文件裡。Core 則把同一件事降下到訊息、`AgentId` 和 runtime。
- **和 CrewAI 相同的想法** 是多角色一起做完一件事、產出沿著上下文傳、人可以在關卡點介入。CrewAI 用 role、task、process 寫在 Python 專案裡。AutoGen 的現行單位是 agent、共用 message thread，以及 team 的發言規則。
- **維護模式是現在的產品事實。** README 要新專案去 Microsoft Agent Framework，並把那裡寫成有長期支援、多模型、以及 A2A 與 MCP 的後繼者。那些是 README 對另一個 repo 的說法。這個 Cell 記錄的是 autogen 這個 repo 仍在教的分層與 team。
- Studio、Bench、現成的 Magentic-One 小隊，是同一套編排的原型介面、評測與範例團隊。開源核心仍是 Core 與 AgentChat。

## 使用情境

### 寫手與評論者輪流改到核准

- 適合誰：想用程式定義兩個角色、並看完整對話的開發者。
- 在什麼情況使用：一件短任務要先做再改，停的條件是評論者說出約定字。
- 帶來的價值：兩人共用同一段上下文，輪流廣播；`save_state` 可以把這段對話和輪到誰留到下一次。

### 專家自己把工作交出去，必要時交回給人

- 適合誰：不想要中央選人、希望每個角色自己決定下一棒的人。
- 在什麼情況使用：Swarm 裡每個助理帶著可交接的對象；做不完時交出指向 user 的 `HandoffMessage`，團隊停下來等人。
- 帶來的價值：交接單決定下一張桌子；人晚點回覆時可以先把狀態存起來再恢復。Swarm 恢復時要交回 `HandoffMessage`，細節以文件為準，尚未實測。

### 開放題目交給指揮與專職座位

- 適合誰：任務要上網、讀檔、寫程式，而且希望有人看過再執行的人。
- 在什麼情況使用：`MagenticOneGroupChat` 或 `MagenticOne`，指揮維護任務帳與進度帳，WebSurfer、FileSurfer、Coder、終端機各做一段。
- 帶來的價值：文件描述指揮會在停滯時改計畫；跑程式前可用 `approval_func` 問人。文件同時要求容器、限制權限、看 log。這些風險控管尚未實測。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是帶名字、系統提示、工具與交接名單的 agent。一件工作是 team 裡的一段共用對話。合作方式是「下一句誰說」的規則：輪流、選人、交接、指揮的兩本帳、或地板上的路線。Core runtime 是這些人收發訊息的機房，和 AgentChat 裡直接 new 出來的角色要分成兩種座位。
- 值得借鑑的 interaction / workflow：RoundRobin、Selector、Swarm 把每一句廣播到共用白板。輪流是發言牌沿桌子傳。Selector 是有人看完整對話再點名。Swarm 是員工把交接單送到指定桌子。Magentic-One 是指揮先改牆上的任務帳，再在進度夾寫這一小步，然後走到某一張專職桌。GraphFlow 是允許走的路線，含分岔與會合。人可以坐進隊伍（`UserProxyAgent`，房間停住等他），或等這一輪收工再回（handoff、`max_turns`），後者可以把狀態封存。終端機執行前先等人點頭。`reset` 清桌；下一題相關就留著桌上的紙繼續。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層。每張桌子是一個 agent，名牌寫 name，職務是 system message 和 description。桌子中間的白板是這一輪的 message thread。RoundRobin 的發言牌順時針走。Selector 多一張選人桌。Swarm 沒有選人桌，交接單從這一桌送到那一桌。Magentic-One 的指揮桌貼著任務帳和進度帳，四周是瀏覽器桌、檔案櫃、寫程式桌和終端機。GraphFlow 把下一棒畫成地板上的路線，平行時兩張桌子同時有人，再在會合桌碰上。人的訪客椅在輪到時整層停住；若是交接停輪，這一層先打烊，狀態收進抽屜，人回來再打開。Memory 是下一句之前塞進提示的備忘條。同進程 runtime 是這一層樓；實驗性的 gRPC host 是總機，把訊息送到別的進程裡的另一層。Studio 的 Team Builder 和 Playground 掛在牆外的控制台。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。推門就看見誰在場、工作發生在哪一張桌子。寫手和評論者隔著一張桌子，草稿放在桌上，評論者說出核准這輪才停。選人的人站在房間中間，看過大家剛說的話再指向下一個人。Swarm 裡有人拿著資料夾走到另一張桌子。指揮來回於瀏覽器工位、檔案櫃、程式桌和終端機，牆上的任務帳與手上的進度夾會改。人要批准程式時站在終端機前。`save_state` 是把這一刻的紙留在桌上，明天推門還在，輪到誰也還在。`UserProxyAgent` 被叫到時，房間裡的人停下等他說話。重點是看見員工在場，以及工作在哪裡發生。
- 需要重新設計的地方：AutoGen 看得到的軌跡是 console 訊息，Studio 則是訊息流和轉移圖。空間辦公室要另做「誰在哪一桌」。AgentChat 的角色是應用程式建立的；Core 的員工要先註冊，才由 runtime 在 `AgentId` 上建立。兩種座位進同一間辦公室時，生命週期要分開畫。這個 repo 已是維護模式，新功能在 Microsoft Agent Framework；辦公室學的是這裡仍在教的對話、交接和 runtime，兩個 repo 是兩間公司。GraphFlow、分散 runtime 標成實驗，Studio 與 `python-v0.7.5` 是否對齊、.NET 有沒有同一組 team，都尚未確認，先不要做進空間介面。`UserProxyAgent` 堵住的那一段文件說不能存；人可以離開再回來的動線，要用停輪後 `save_state` 的那一條。

## 初步看法

- 最有價值的部分：同一段對話上的幾種下一發言規則，加上可以封存的停輪，讓人晚點再回到同一張桌子。
- 最大限制或疑問：README 已宣布維護模式，後繼者是另一個 repo。UserProxy 的當下介入不能存狀態。GraphFlow 和分散 runtime 仍是實驗。這些行為都還沒跑過。
- 是否值得進一步研究或親自體驗：值得，尤其是交接、指揮的兩本帳、以及人回到同一段對話時，在 2D 樓層和 3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Repository：https://github.com/microsoft/autogen
- Documentation：https://microsoft.github.io/autogen/stable/ （本次為 stable 用戶指南）
- Successor（另一個 repo，不是這個 Cell）：https://github.com/microsoft/agent-framework
- Migration guide：https://learn.microsoft.com/en-us/agent-framework/migration-guide/from-autogen/
- License：`LICENSE` 為 CC BY 4.0（文件與其他內容）；`LICENSE-CODE` 為 MIT（程式碼，Copyright Microsoft Corporation）
- README（`main`）：維護模式、AgentChat／Core／Extensions、Studio、Bench、Magentic-One、從 v0.2 升級的遷移連結
- 已讀概念頁：AgentChat 首頁、Teams、Selector Group Chat、Swarm、Magentic-One、GraphFlow、Memory、Human-in-the-Loop、Managing State、Agent and Agent Runtime、Distributed Agent Runtime、AutoGen Studio 首頁
- Release：`python-v0.7.5`（2025-09-30）
