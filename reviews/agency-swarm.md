# Agency Swarm

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agency-swarm-cell-ad04/reviews/agency-swarm.md)
>
> Cell ID：[https://github.com/VRSEN/agency-swarm](https://github.com/VRSEN/agency-swarm)
>
> Status：`untried`
>
> Category：Python multi-agent framework（Agency／單向溝通）
>
> Last updated：2026-10-10

## 產品介紹

Agency Swarm 是開源的 Python 框架，用來組一支有職位的 agent 團隊。開發者寫每個角色的說明、工具和檔案，再把他們收成一個 Agency，並畫出誰可以主動找誰。它服務的是要寫自動化的工程師，不是坐在桌面 ADE 裡看終端機的人。官方文件在 [agency-swarm.ai](https://agency-swarm.ai)。現行版本建在 OpenAI Agents SDK 上。本次只讀 repo 的 README、LICENSE 與現行文件，沒有安裝。

作者 Arsenii Shatokhin（VRSEN）的說法是：把自動化想成一間真實的代理商，角色像 CEO、開發者、虛擬助理，比較好懂。使用者從入口 agent 開始說話。入口 agent 只能沿著你允許的方向把工作送出去，或把這個人交到另一張桌子。官方另有託管產品 Agencii，文件把它寫成框架外的部署與市集。那是產品層，不是這個 repo 的辦公室畫面。

## 主要 Features

### Agent 是職位，不是任務清單

每個 Agent 有 name、給其他 agent 看的 description、以及自己的 instructions。說明可以是字串或一份 markdown。工具、OpenAPI schema、檔案資料夾、MCP 伺服器都掛在這個角色上。README 建議一個角色一個資料夾：說明、tools、files、schemas 分開。description 是同事眼中的職稱，instructions 是這個人自己的工作手冊。框架不幫你寫好一套固定人設。

### Agency 是一張單向的溝通圖

Agency 把 agent 放在一起。放在前面的是入口：使用者可以直接傳訊息的人。`communication_flows` 是單向邊，左邊可以主動找右邊。文件寫兩種走法。**SendMessage**（預設）是派工：對方做完，控制權回到派工的人；文件說同一個人可以同時派給多個同事，各自一條 thread，再把結果收回來。**Handoff** 是交人：對方拿走整段對話，接著跟使用者說話，控制權不回來。CrewAI 的主單位是 Task 和 process（順序或階層）。Agency Swarm 沒有把任務清單當成編制本身；編制是誰被允許開口。

### 這一次的 context、對話與共用資料

Agency Context 是這一次執行裡的共用抽屜。工具把結構化資料放進去，下一個工具直接拿，不必把整包內容再寫進訊息。文件要求比較久的 session 狀態由呼叫端自己保管，每次用 `context_override` 帶進去，再從回應拿回來。舊的 `user_context` 仍可用，但標成即將移除。跨次對話的歷史要自己接 `load_threads_callback` 和 `save_threads_callback`。不接的時候歷史存在哪裡，尚未確認。`shared_instructions` 是全員工作手冊。`shared_files_folder` 是大家共用的向量庫。每個 agent 也可以有自己的 files 資料夾。

### 人怎麼進場、怎麼看進度

跑法文件寫三種。`get_response` 是程式呼叫，可以指定 `recipient_agent`，也可以加一段 `additional_instructions`。`copilot_demo` 開一個 CopilotKit 聊天畫面。終端機建議用 `npx @vrsen/agentswarm`。TUI 裡 `@AgentName` 把這句話直接送給某個入口 agent。`/agents` 在 Plan、Build、Run 之間切：Plan 和 Build 用 Agent Builder 改專案檔，不必先把 agency 跑起來；Run 才把話送給活著的 agency。`/cost` 看這次 session 的用量。追蹤可接到 OpenAI tracing、Langfuse 或 AgentOps。`visualize()` 產出一張可互動的 HTML，`get_agency_graph()` 給 ReactFlow 用的節點和邊。那是組織圖，不是人在場的工作空間。

### 檢查與外部工人

輸入 guardrail 在 agent 處理前擋訊息，輸出 guardrail 在交出前檢查，失敗可依 `validation_attempts` 重試。這是程式關卡，不是人坐在旁邊蓋章。文件沒有寫像固定審核關那樣、交出前一定先問人。人的介入是繼續聊、@ 某個人、指定收件者，或加上額外指示。是否另有審核 API，尚未確認。`OpenClawAgent` 讓編排留在 Agency Swarm，把一支工作交給外面的 OpenClaw。文件說它適合當工人，不適合當會再往下派工的人，而且普通的 Python 工具不會自動跟過去。

## 主打賣點

- **它加上的是一張單向溝通圖，以及兩種交接。** 誰可以主動找誰是資料。SendMessage 是派工後收回；Handoff 是把使用者交到下一張桌子，對話歷史跟著走。入口 agent 決定人從哪裡進門。
- **和 CrewAI 相同的是多角色編制。** 不同的是單位。CrewAI 用 role、goal、backstory 組小隊，用 Task 的期望產出和 context 接力，process 決定順序或由經理分派。Agency Swarm 的 CEO 只是你寫說明的一個 agent，沒有內建的 manager process。他能不能找開發者，只看邊上有沒有那一條箭頭。
- **官方比較頁把「沒有預寫 prompt、溝通方式統一、自動改錯」寫成只有自己做得到**，並把 CrewAI 寫成缺乏架構、建在 LangChain 上。那是他們的行銷比較，不是本次核對過的事實。CrewAI 現行文件描述的是另一套資料：角色、任務、順序或階層流程。Pydantic 工具、LiteLLM、視覺化組織圖、TUI 的 Plan／Build／Run，是這張圖的周邊。開源核心仍是 Agency 和 communication flows。
- Agencii 是文件裡的託管層：從 GitHub 部署、市集、Slack 等整合。同一頁把 Agent Builder、背景 agent、Workspaces 標成還沒做完。哪些已經能用，尚未確認。

## 使用情境

### 入口的人派工，做完再收回

- 適合誰：想用程式定義「客戶只跟一個人說話」的開發者。
- 在什麼情況使用：CEO 是入口，communication flows 允許他找開發者和助理，走 SendMessage。
- 帶來的價值：編制留在誰可以開口。文件描述的是派出去的工作分線進行，結果回到 CEO。是否真的平行、結果如何合併，尚未實測。

### 把使用者交到專員桌上

- 適合誰：分流之後，希望專員接著同一段對話的人。
- 在什麼情況使用：分流 agent 用 Handoff 把人交給帳務或技術專員。
- 帶來的價值：專員拿到先前的對話，人不用把事情重講一遍。分流的人不再握著這段對話。

### 先改編制，再對活著的團隊說話

- 適合誰：想在終端機裡調整角色和箭頭的開發者。
- 在什麼情況使用：TUI 的 Plan／Build 改檔案，Run 再用 `@` 把話送給某個入口。若某一支工作要交給 OpenClaw，就把那個工人接在同一張圖上。
- 帶來的價值：改的是辦公室的編制，不是重寫一整段 prompt。TUI 仍是終端機，不是可走進的辦公室。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是職位（description 給同事看，instructions 給自己）。公司是單向的允許圖，不是任務佇列。兩種交接要分開：派工收回，以及把訪客交出去。入口是接待處。全員手冊和共用檔案櫃是公司的，每個座位還有自己的抽屜。
- 值得借鑑的 interaction / workflow：人從入口進門，也可以直接走到某個入口座位（`@` 或指定 recipient）。SendMessage 是單據送出、人留在原位等結果；Handoff 是人被帶到下一張桌子，上一個人退出這段對話。Agency Context 是這一輪的共用托盤，session 狀態由人帶走再帶回。對話要歸檔才接 thread callback。Guardrail 是門禁，不是主管走過來簽名。用量在 TUI 的 `/cost` 或回應裡的 usage，追蹤則接到外面的觀測工具。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。每個 Agent 是固定工位，名牌寫 name 和 description。地板上的箭頭是 communication flow，而且只有一個方向。SendMessage 是單據沿著箭頭送出去，派工的人留在位子上，多張單可以同時在路上。Handoff 是訪客的棋子被移到下一張桌子，原來的人不再參與。入口工位朝向大門。中間是共用檔案櫃和全員手冊，各座位旁是自己的 tools 和 files。Agency Context 是這次放在桌上的托盤。`visualize()` 的 ReactFlow 圖貼在牆上當藍圖，人並不在那張圖裡工作。TUI 是接待櫃上的終端機。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你從入口 agent 的位子進來，看得到誰在場。SendMessage 時，CEO 留在座位，一份資料夾被送到開發者桌上，做完再送回；兩份資料夾可以同時在不同位子上。Handoff 時，你被帶到專員的房間，分流的人離開這段對話，專員桌上已經有先前的紀錄。OpenClaw 工人是接在同一張允許圖上、但人在另一棟的外包座位，普通工具不會自動跟過去。Plan／Build 是改建這層樓的房間，Run 才是營業中的辦公室。重點是看見誰在場、誰被允許走向誰、訪客現在站在哪一張桌子。
- 需要重新設計的地方：組織圖儀表板不是工作空間。箭頭必須看出「單據會回來」和「人被交出去」的差別，一般節點圖做不到。CrewAI 用任務順序決定下一棒；這裡的圖只規定誰可以先開口，何時開口由 agent 自己決定，空間裡要能看出這件事。跨次記憶不是內建抽屜，而是你自己接的 callback。沙箱沒有成為核心文件的獨立章節，權限主要寫在溝通方向，以及 OpenClaw 自己的工具清單；程式工具怎麼隔離，尚未確認。文件把預設模型寫成 `gpt-6-luna`，本次沒有呼叫，能不能用尚未確認。Agencii 頁面上的 Workspaces 仍是未完成項目，不要把它當成這間 2D／3D 辦公室。

## 初步看法

- 最有價值的部分：單向允許圖，加上派工收回和交人這兩種交接。辦公室的平面圖可以直接從這張圖長出來。
- 最大限制或疑問：它是給開發者呼叫的框架。平行 thread、不接 callback 時的對話存在哪、以及預設模型是否可用，都還沒跑過。官方比較頁對 CrewAI 的批評不能當成事實。
- 是否值得進一步研究或親自體驗：值得，尤其是 Handoff 時人被帶到哪一桌、SendMessage 時單據如何同時在路上。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://agency-swarm.ai
- Repository：https://github.com/VRSEN/agency-swarm
- Documentation：https://agency-swarm.ai （索引 https://agency-swarm.ai/llms.txt）
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright 2023–2025 Nick Bobrowski, Arsenii Shatokhin, Artemii Shatokhin）
- README（`main`）：Agent、Agency、單向 communication flows、thread callback、`copilot_demo`、`tui`
- 已讀概念頁：Agencies Overview、Communication Flows、Agents Overview、Running an Agency、Agent Swarm TUI、Agency Visualization、Agency Context、Guardrails Overview、Observability、Third-Party Agents／OpenClawAgent、Agency Swarm vs Other Frameworks、Agencii Platform Overview
