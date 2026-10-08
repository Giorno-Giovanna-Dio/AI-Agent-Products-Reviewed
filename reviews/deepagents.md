# Deep Agents

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-deepagents-cell-9e0b/reviews/deepagents.md)
>
> Cell ID：[https://github.com/langchain-ai/deepagents](https://github.com/langchain-ai/deepagents)
>
> Status：`untried`
>
> Category：Agent harness（長程任務 runtime）
>
> Last updated：2026-10-08

## 產品介紹

Deep Agents 是 LangChain 的開源 **agent harness**：把「一個能做長任務的
agent」預設該有的辦公桌打包好，用 `create_deep_agent` 就能跑。它包含虛擬
檔案系統、子 agent、上下文壓縮、跨回合記憶、skills，以及工具執行前的人工
核准。模型只要支援 tool calling 就能接上。底層是 LangGraph，所以有串流、
checkpoint 與中斷後續跑。

同一個 repository 裡還有終端機產品 **Deep Agents Code**（`dcode`），定位
接近 Claude Code：在終端機裡改檔、跑指令、委派子任務，但模型可換。官方說
靈感來自 Claude Code，想把「通用 agent 為什麼能把長工作做完」收成可替換的
零件。Python SDK 是這個 Cell 的主體；JavaScript 版在另一個 repository
[`deepagentsjs`](https://github.com/langchain-ai/deepagentsjs)。尚未實際
安裝或執行。

LangGraph 是圖執行期，LangChain 的 `create_agent` 是較薄的一層。Deep
Agents 在那之上加上檔案、委派、上下文與 skills，當成預設辦公套件。任何
LangGraph 圖也可以被塞進來當子 agent。

## 主要 Features

### 虛擬檔案系統當成辦公桌

Agent 用同一組檔案工具工作：列出、讀取、寫入、修改、刪除、搜尋。後端可換：
這次對話的記憶體、本機磁碟、可跨對話的 store、或會多一項 `execute` 的
sandbox。常見做法是用複合路由，把專案檔放在真實目錄，把大型工具輸出
（`/large_tool_results/`）和對話歷史（`/conversation_history/`）留在暫時
抽屜，避免和專案檔混在一起。路徑權限可以用規則限制內建檔案工具能讀寫哪裡。

### 子 agent：乾淨的上下文，以及可盯著的背景工作

預設的 `task` 工具會派出**短暫的子 agent**。每次委派都是新的上下文，子
agent 自己做完，再交回**一份**結果。重的搜尋、測試或審查留在側桌，主
agent 的對話不會被中間過程塞滿。也可以自訂專家：各自的工具、skills、模型
與權限。

長任務可以改走非同步子 agent：立刻拿到任務編號，之後查狀態、補指示或取消，
主 agent 不必乾等。串流可以按每個委派任務分開看。

### 記憶、skills，以及把上下文卸下來

長期記憶是檔案，通常是 `AGENTS.md`，開場就載入 system prompt，agent 也能
改寫。存放範圍可以是「這個 agent 全體共用」或「每個使用者一份」。Skills
遵循 Agent Skills 規格：啟動時只看名稱與簡短說明，任務對上才讀全文，所以
能力手冊不會一開始占滿桌子。

對話變長時，harness 會摘要舊訊息，並把過大的工具結果寫進檔案。官方也描述
可另外跑一個整理用 agent，在對話之間把重點併回記憶；那是部署時的選配流程，
尚未確認預設是否會自動發生。

### 人的介入：核准、待辦，以及做到什麼算完成

敏感工具可以在執行前暫停，讓人核准、改參數或拒絕；這需要 checkpointer 才能
停住再繼續。檔案路徑規則除了允許／拒絕，也可以改成「碰到就暫停等人」。
從 v0.7 起，待辦清單是選配的：接上之後，agent 用 `write_todos` 維護
`pending`／`in_progress`／`completed`，狀態留在 agent state，介面可以跟著
串流更新。

Deep Agents Code 再加一層完成定義。`/goal` 先讓 agent 把目標寫成驗收條件，
條件會跨回合留著，直到暫停、完成、受阻或清除。`/rubric` 則是你已經寫好的
評分標準，可只套下一回合，或一直生效。無人值守時改用命令列傳入 rubric。

### 終端機裡的現成員工

`dcode` 把上述 harness 做成可安裝的 coding agent。全域記憶在
`~/.deepagents/`，專案記憶在該 git 根目錄的 `.deepagents/AGENTS.md`。
互動模式預設對寫檔、shell、網路與派出子 agent 等人點頭；唯讀檔案工具直接
執行。遠端 sandbox、MCP、LangSmith tracing 都是這條產品線的一部分。核准
模式還有實驗性的自動通過，以及需先確認風險的 YOLO；無人值守模式對 shell
與 MCP 採較嚴的預設。細節以官方文件為準，此處尚未實測。

## 主打賣點

- **長任務的預設辦公套件**：檔案、委派、上下文卸載、記憶與 skills 一次到
  齊。想要更薄的迴圈就退回 `create_agent`；迴圈形狀本身不對時再自己畫
  LangGraph。
- **上下文是一套系統**：摘要、把大輸出卸到檔案、把重活關進子 agent 的新
  視窗、再把要記得的事寫成檔案。這比「對話視窗變長就截斷」更適合多步驟工作。
- **模型可換，零件可換**：前沿 API、開放權重與本機模型都能接。middleware、
  後端、子 agent 都可以覆寫，官方不想讓你為了改一塊就 fork。
- **完成標準可以留在牆上**：Deep Agents Code 的 goal／rubric 讓「做到什麼
  算完成」跨回合存在，並能被人改過再繼續。這和只在聊天裡重講一次需求不同。

虛擬檔案工具、MCP、待辦清單，在 Claude Code 一類產品裡已經常見。Deep
Agents 的差異是把它們收成可程式化的 harness，並把「誰的上下文、哪一層記憶、
何時必須等人」變成明確設定。LangSmith 的追蹤與部署是同一生態系的觀測與
上線配套，本身是儀表板。

和本 repo 其他 Cell 的關係：它比 [Pi](reviews/pi.md) 更有主見、零件更滿；
比 [Sandcastle](reviews/sandcastle.md) 更像「員工怎麼想與怎麼用檔案」，
Sandcastle 則管 git worktree 與容器生命週期；比 [Paperclip](reviews/paperclip.md)
更像單一員工的 runtime，Paperclip 管的是整家公司的目標、預算與組織。

## 使用情境

### 做一個會做長研究或長流程的應用 agent

- 適合誰：要用 Python 把 agent 嵌進產品、又不想從零設計檔案、記憶與委派的團隊。
- 在什麼情況使用：`create_deep_agent` 接上自己的工具與 MCP，必要時加上
  子 agent 與 `/memories/` 路由。
- 帶來的價值：長任務有地方放中間結果與跨回合偏好，主對話保持可讀。

### 換模型的終端機 coding agent

- 適合誰：想要 Claude Code 那類終端機流程，但要自己選模型或接本機模型的人。
- 在什麼情況使用：安裝 `dcode`，用專案 `AGENTS.md` 與 skills 帶入慣例，
  寫檔與 shell 走核准，或改到遠端 sandbox。
- 帶來的價值：員工的行為規格（記憶、技能、驗收條件）跟模型供應商分開。

### 主 agent 派專家，人只看交回來的結果

- 適合誰：需要研究、實作、審查分開跑，又怕單一上下文被中間 log 淹沒的人。
- 在什麼情況使用：同步子 agent 做完交一份報告；耗時的工作改非同步，中途
  補指示或取消。
- 帶來的價值：委派有邊界。重活留在側桌，主桌只收到結論與可追的任務狀態。

## 我們可以學什麼

- **值得借鑑的 product idea**：把 agent 的工作現場定義成**一張桌子加幾種
  抽屜**。專案檔、長期記憶、skills、過大的工具輸出、對話歷史各有位置。
  子 agent 是暫時多出來的側桌，做完把一張紙滑回來。goal／rubric 是牆上的
  完成標準，跨回合留著，人可以改了再讓工作繼續。
- **值得借鑑的 interaction / workflow**：同步委派是「等人把報告送回」；
  非同步委派是「房間還亮著，可以進去補一句、或請他停」。核准是工具執行前
  的暫停，人可以改參數再放行。記憶開場就在桌上，skills 要對上任務才從架上
  拿下來。待辦只有選配時才出現，不能假設每個員工都有白板。
- **在 2D workspace 裡會變成什麼**：平面辦公室裡，主 agent 是一張固定辦公
  桌。`task` 派出時，旁邊亮起一張側桌，視窗裡是該子 agent 自己的串流；
  同步側桌在交回報告後收起。非同步側桌保持亮著，地板上有狀態、以及「補
  指示／取消」兩個動作。牆上一塊白板顯示待辦（有接上規劃時）。檔案櫃分成
  記憶、skills、大型輸出三層抽屜。碰到需要核准的寫入或指令，桌燈轉成等待
  人走過去點頭。Sandbox 是隔開的工坊隔間：shell 在裡面跑，路徑規則留在
  外面的檔案櫃，兩套規則要在平面上分開標示。
- **在 3D workspace 裡會變成什麼**：你走進主 agent 的辦公室。同步子 agent
  的房間沿走廊暫時出現，玻璃上能看見它自己的工具與訊息；門只在交報告時
  打開一次，然後房間消失。非同步子 agent 的房間一直有人，你可以走進去遞
  一張新指示，或請他收工。記憶櫃如果綁在 agent 上，就是這間辦公室的共用
  檔案櫃；如果綁在使用者上，就是你自己的置物櫃，別人的偏好不會出現在這裡。
  Skills 是架上的活頁，任務對上才取下。goal 是牆上一直留著的驗收板，暫停
  時翻過去，完成或受阻時留下狀態。LangSmith 的 trace 是走廊監視器的錄影，
  用來事後回放，那是觀測畫面，辦公室本身仍是這些人或房間。
- **不值得照搬或需要重新設計的地方**：官方安全模型是「信任模型，邊界放在
  工具與 sandbox」。路徑權限只管內建檔案工具，管不到 sandbox 裡的 shell，
  也管不到自訂工具與 MCP。Workspace 若只畫一把「這個資料夾鎖住了」的鎖，
  會讓人以為 shell 也被鎖住。子 agent 若只顯示成聊天裡的一顆工具標籤，
  就失去「側桌／側房」的空間；應讓派出、交報告、取消都對應到一個看得到的
  位置。待辦從 v0.7 起預設不在，不能把規劃白板當成每個 Deep Agent 都有的
  傢俱。這個產品是員工 runtime，[Conductor](reviews/conductor.md) 那類
  ADE 控制台與 [Paperclip](reviews/paperclip.md) 的公司編排是另外的層。

## 初步看法

- 最有價值的部分：上下文隔離、檔案型記憶，以及可暫停的工具呼叫。這三件事
  都能直接變成辦公室裡的側房、檔案櫃與門口核准，而且不依賴某一家模型。
- 最大限制或疑問：它沒有自己的空間介面；權限與 sandbox 的邊界容易在 UI 裡
  被畫錯。JavaScript 版是另一個 repository。Deep Agents Code 的核准模式與
  goal 生命週期尚未親測，整理記憶的背景 agent 是否為預設行為也尚未確認。
- 是否值得進一步研究或親自體驗：值得當「單一員工 runtime」的架構參考。
  若之後要驗證側房與核准手感，再在 `/workspace-labs/deepagents` 跑最小的
  `create_deep_agent` 或 `dcode` 即可，不必先把所有 sandbox 供應商測完。

## 後續補充（選填）

Not tried yet。官方文件已足夠回答 workspace 設計需要的問題：任務如何被
委派、記憶與 skills 如何分開、人在哪裡介入、sandbox 與路徑權限各自管什麼。

## Sources

- [Deep Agents repository](https://github.com/langchain-ai/deepagents)
- [Deep Agents overview](https://docs.langchain.com/oss/python/deepagents/overview)
- [Subagents](https://docs.langchain.com/oss/python/deepagents/subagents)
- [Async subagents](https://docs.langchain.com/oss/python/deepagents/async-subagents)
- [Memory](https://docs.langchain.com/oss/python/deepagents/memory)
- [Backends](https://docs.langchain.com/oss/python/deepagents/backends)
- [Sandboxes](https://docs.langchain.com/oss/python/deepagents/sandboxes)
- [Permissions](https://docs.langchain.com/oss/python/deepagents/permissions)
- [Human-in-the-loop](https://docs.langchain.com/oss/python/deepagents/human-in-the-loop)
- [Deep Agents Code](https://docs.langchain.com/oss/deepagents/code/overview)
- [Goals and rubrics](https://docs.langchain.com/oss/deepagents/code/goals-and-rubrics)
- [deepagents.js](https://github.com/langchain-ai/deepagentsjs)
