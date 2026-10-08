# CrewAI

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-crewai-cell-90bd/reviews/crewai.md)
>
> Cell ID：[https://github.com/crewAIInc/crewAI](https://github.com/crewAIInc/crewAI)
>
> Status：`untried`
>
> Category：Python multi-agent framework（Crew / Flow）
>
> Last updated：2026-10-08

## 產品介紹

CrewAI 是開源的 Python 框架，用來編排一組會扮演角色的自主 agent。開發者用程式或 JSON 寫下 Agent（角色、目標、背景）、Task，再收成一個 Crew，然後用 CLI 跑完這一次協作。它服務的是要寫自動化的工程師，而不是坐在桌面 ADE 裡看終端機的人。官方網站是 [crewai.com](https://crewai.com)，文件在 [docs.crewai.com](https://docs.crewai.com)。本次只讀 repo 的 README、LICENSE 與現行文件，沒有安裝。

現行介紹把產品拆成兩層。**Crew** 是有角色的小隊：自己決定怎麼用工具、彼此委派、把任務做完。**Flow** 是包在外面的事件流程：握住狀態、分支，以及何時把一段需要判斷的工作交給某個 Crew。README 另把商業套件 **CrewAI AMP**（部署、觀測、治理）和開源框架分開。AMP 有控制平面可以試用，那是企業營運層，不是這個 repo 的辦公室畫面。

## 主要 Features

### Agent、Task、Crew

Agent 的核心是 role、goal、backstory，再掛上 LLM、工具、知識來源。Task 要寫清 description 和 expected_output，並可指定負責的 agent、要接上哪些上游產出（context）、輸出檔，或固定成 Pydantic／JSON。Crew 把這兩份名單放在一起，並選定 process。新專案預設是 JSON：每個角色一個 `agents/*.jsonc`，任務與流程在 `crew.jsonc`；舊的 Python／YAML 骨架仍可用 `crewai create crew --classic`。這樣分工變成可讀的資料，換題目時改 input，而不必重寫一整段 prompt。

### Process：順序或階層

Processes 概念頁目前只寫兩種。**Sequential** 是預設：任務照清單順序執行，上一棒的輸出可透過 context 交給下一棒。**Hierarchical** 要另外給 `manager_llm` 或 `manager_agent`；文件說這位管理者負責規劃、依角色能力分派，並檢查結果。Tasks 頁仍允許每個 task 自己寫死 agent。階層模式下，寫死的負責人會不會被 manager 改派，尚未確認。文件首頁的卡片提到 hybrid process，但 Processes 頁沒有第三種 process，hybrid 尚未確認。Crew 還可開 `planning`：每輪開始前由規劃者把步驟寫進各 task 的說明。

### 委派、記憶與知識

`allow_delegation` 打開後，文件說 agent 會多出兩種協作：把工作派給隊友，或向隊友提問。Crew 可開 memory。文件描述的現行做法是統一的 `Memory`：任務結束後抽出事實存起來，下一棒開始前再召回相關內容，注入 prompt。記憶用 scope 樹分開，例如研究員有自己的子樹，寫手讀 crew 的共用範圍。同一頁寫明這套 API 取代舊的 short-term、long-term、entity、external 記憶類型。Knowledge 是另一條線：事先給的參考資料（檔案、網頁等），預設進 ChromaDB，也可換成 Qdrant，可掛在單一 agent 或整個 crew。記憶是跑出來的事實，知識是上工前放上書架的資料。召回品質尚未實測。

### Flow、人的關卡與產出

Flow 用 `@start`、`@listen`、`@router` 串步驟，狀態可以是 dict 或結構化 model。某一步裡面可以呼叫一整個 Crew，再依結果往下走。`@persist` 會把 flow 狀態存下來，之後可以恢復，或 fork 成另一條狀態線。人的介入有兩種，文件都還在。Task 的 `human_input`：agent 在交出最終答案前先問人，用來補上下文或請人確認。Flow 的 `@human_feedback`（文件寫需要 CrewAI 1.8.0 以上）：暫停這條流程，收集評語，並可把自由文字收成 approved、rejected 這類結果再分支。

Task 可把結果寫成檔案（README 示例是 markdown 報告），或收成固定結構。Guardrail 在結果交給下一棒之前檢查。Checkpoint 在任務完成等事件寫下執行快照（文件示例目錄是 `./.checkpoints/`），中斷後可 resume，也可 fork，並改已完成任務的輸出再重跑下游。Agent 若允許執行程式，文件把 `safe` 寫成走 Docker、`unsafe` 寫成直接執行。這是框架裡的開關，尚未實測。

### AMP（官方說明，尚未使用）

README 把 **CrewAI AMP Suite** 寫成開源框架外的商業控制平面：追蹤、統一管理、整合、安全、支援，以及雲端或自架部署，並指向可試用的 Crew Control Plane（`app.crewai.com`）。Agents／Tasks 文件各自提到 AMP 裡的視覺化編輯器。文件索引還有部署、Gmail／Slack 等觸發器、團隊權限。這些是官方對企業層的描述。哪些已經在免費控制平面裡可用，尚未確認。

## 主打賣點

- **它加上的是角色、任務、流程這組資料模型。** role、goal、backstory、task 的期望產出與 context、以及 sequential／hierarchical process，是 CrewAI 讓人記住的核心。Flow 再在外面加上事件、狀態與分支。這全部住在 Python 專案裡。
- **和 Paperclip、OpenRig 相同的想法** 是多角色編制、工作交接、有人管的階層、人在關卡點介入、以及共用與私人脈絡。Paperclip 是組織控制平面，把 Claude Code、Codex 這類外部 agent 雇成員工。OpenRig 是跨 Claude Code、Codex、Pi 的持久團隊，seat 活在 tmux。CrewAI 的 crew 是同一次 Python 執行裡的角色扮演小隊。
- **自主與精確控制拆開。** 介紹頁建議正式應用先用 Flow 管結構與狀態，只在需要協作判斷的那一步放進 Crew。這和「一家一直醒著的公司」或「一組一直開著的 terminal」是不同的執行方式。
- AMP、視覺化編輯器、大量現成工具，是編排能力的企業包裝與周邊。開源核心仍是 Crew 與 Flow。

## 使用情境

### 用角色接力做一份研究報告

- 適合誰：想用程式定義研究員和撰寫者的開發者。
- 在什麼情況使用：題目固定，要先研究再寫成報告，並把結果寫進檔案。
- 帶來的價值：角色、任務與 context 交接留在 `crew.jsonc`，下次換題只改 input。

### 業務流程中間包一小隊

- 適合誰：前後步驟已經是確定的 Python，只想把中間一段交給多角色判斷的人。
- 在什麼情況使用：Flow 負責抓資料、分支、寫回；某一步 kickoff 一個 Crew 做研究或起草。
- 帶來的價值：狀態留在 Flow，自主協作只發生在需要的那一段；交出前可用 task 的 `human_input` 或 flow 的 `@human_feedback` 請人看過。

### 由管理者分派而不是人肉排程

- 適合誰：任務多、希望依角色能力分派的進階使用者。
- 在什麼情況使用：process 設成 hierarchical，並提供 manager LLM 或 manager agent。
- 帶來的價值：文件描述的是經理規劃、分派、再檢查。已指定的 agent 會不會被改派，尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是 role、goal、backstory；工作是「期望產出 + 上游 context」；合作方式是 process。Flow 是這天的日程與狀態，用來決定哪一組 crew 上場。
- 值得借鑑的 interaction / workflow：順序流程是產出從這一桌傳到下一桌；階層流程是經理收件、派工、驗收；delegation 是員工起身去問同事；`human_input` 與 `@human_feedback` 是人在場才能放行；checkpoint 讓人回到某一棒、改產出、再 fork。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。每個 Agent 是固定工位，名牌寫 role 和 goal。Sequential crew 是一排工位，任務紙沿著桌子傳，context 夾在紙上。Hierarchical crew 多一張經理桌，工作先到經理再分出去。Flow 是牆上的日程板，決定下一間 crew 房間開不開工。人要蓋章時走到該工位。記憶的共用櫃和私人抽屜分開放，知識是架上的參考資料，報告與 checkpoint 放在工位旁。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。研究員和寫手在自己的位子上。順序流程裡，寫手要等研究資料夾送到才開始；階層流程裡，經理在房間中間派工，做完再走過去看結果。`allow_delegation` 是一個員工走到另一張桌子提問。Knowledge 是書架，Memory 是抽屜裡累積的筆記。Flow 的狀態留在白板，隔天還在，所以可以 resume。Checkpoint 是某一刻的快照，人可以走回那個時刻、改一份已完成的文件，再另開一條分支。重點是看見誰在場、誰在哪裡做事。
- 需要重新設計的地方：CrewAI 的執行軌跡是 log、檔案與 checkpoint，空間辦公室要另做「誰在哪一桌」的呈現。它的 agent 是同進程裡的 LLM 角色；若辦公室同時要放 Paperclip 雇來的外部員工，或 OpenRig 那種持久 terminal seat，座位種類要分開。hybrid process、階層是否覆寫已指定 agent、以及 AMP 免費控制平面的實際範圍都尚未確認，先不要做進空間介面。文件索引標成 v1.15.25，README 安裝示例印出的 CLI 是 `crewai v0.102.0`，兩套版本號是否同一發行，尚未確認。

## 初步看法

- 最有價值的部分：role、task、process 這組資料，加上 Flow 把自主小隊放進可恢復、可等人的流程。
- 最大限制或疑問：它是給開發者呼叫的框架。階層分派和統一記憶是否如文件所寫，要跑過才知道。AMP 與開源 CLI 的版本敘事也尚未對齊。
- 是否值得進一步研究或親自體驗：值得，尤其是 context 交接、經理驗收、human feedback 在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://crewai.com
- Repository：https://github.com/crewAIInc/crewAI
- Documentation：https://docs.crewai.com （索引 https://docs.crewai.com/llms.txt ；本次概念頁為文件站 v1.15.25）
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright 2025 crewAI, Inc.）
- README（`main`）：Crews、Flows、JSON crew、sequential／hierarchical、AMP
- 已讀概念頁：Introduction、Agents、Tasks、Processes、Collaboration、Memory、Knowledge、Flows、Planning、Checkpointing、Human Input on Execution
