# Microsoft Agent Framework

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-microsoft-agent-framework-cell-9381/reviews/microsoft-agent-framework.md)
>
> Cell ID：[https://github.com/microsoft/agent-framework](https://github.com/microsoft/agent-framework)
>
> Status：`untried`
>
> Category：Python 與 .NET 的 agent／workflow 框架（AutoGen 與 Semantic Kernel 的後繼）
>
> Last updated：2026-10-10

## 產品介紹

Microsoft Agent Framework（官方也簡稱 MAF）是開源框架，給要把 agent 從試作推進到可運行系統的開發者。它住在程式裡：你寫一個會呼叫模型和工具的 agent，或把多個 agent 與普通函式串成一條 workflow。使用者是寫 Python 或 .NET 的工程師，不是坐在桌面 ADE 裡看終端機的人。官方文件站在 [learn.microsoft.com/agent-framework](https://learn.microsoft.com/en-us/agent-framework/)。本次只讀 repo 的 README、LICENSE、Transparency FAQ、samples 目錄，以及 Learn 上的總覽與兩份遷移指南，沒有安裝、也沒有執行。

官方的遷移故事寫得很直。Learn 總覽說，它由 AutoGen 與 Semantic Kernel 同一批團隊做，是兩者的下一代：留下 AutoGen 那種比較好上手的單一與多 agent 抽象，接上 Semantic Kernel 的企業向能力（session 狀態、型別、middleware、telemetry），再補上圖形式 workflow，讓多 agent 的路徑可以寫死、暫停、再恢復。Python 與 .NET 在這個 repo 裡都是正式實作，samples 與五種編排型別兩邊都有。Go SDK 另開 [microsoft/agent-framework-go](https://github.com/microsoft/agent-framework-go/)，Learn 標成 public preview，並寫明 declarative agents、RAG、CodeAct、函式式 workflow 還沒有。

## 主要 Features

### Agent 與 workflow 分開

Learn 總覽把用法拆開。開放、要自己用工具和規劃的工作用 **agent**。步驟已經清楚、要控制順序、或多個 agent 與函式必須配合時，用 **workflow**。能寫成普通函式的，就不要硬做成 agent。AutoGen 遷移指南說，Agent Framework 的 agent 預設不把對話留在自己身上；要延續上一句，得把歷史放進 **AgentSession**。Workflow 才是「這一輪工作怎麼從這一棒交到下一棒」。

### 官方遷移：Semantic Kernel 與 AutoGen

Semantic Kernel 遷移指南把差異收成開發者會碰到的幾件事：不再先做一個 Kernel 再把 plugin 塞進去，工具可以直接掛在 agent 上；呼叫端不必自己挑 thread 型別，改由 agent 建立 session；非串流呼叫從 `Invoke` 收成一次 `Run`。這是 API 收斂，不是另一套產品故事。

AutoGen 遷移指南寫，單 agent 仍是模型客戶端、指示、工具。變的是多人怎麼合作。AutoGen 有低階的事件 runtime，也有高階的 `Team`；實驗性的 GraphFlow 是控制流，訊息廣播給參與者，邊代表條件轉移。Agent Framework 收成一種 **Workflow**：資料沿有型別的邊走，節點可以是 agent、函式或子 workflow。同一份指南說，AutoGen 有嵌入式與實驗性的分散式 runtime，Agent Framework 目前聚焦單一 process，分散式執行還在計畫中。Transparency FAQ 卻寫 runtime 同時支援 in-process 與 distributed。Durable Task 與 Azure Functions 已從這個 repo 移到 [agent-framework-durable-extension](https://github.com/microsoft/agent-framework-durable-extension)。這三份說法沒有對齊，分散式是否已可用，尚未確認。

Learn 的 AutoGen 遷移指南仍把 Swarm（交接）和 SelectorGroupChat 寫成開發中。同一份 repo 裡，Python 的 `agent-framework-orchestrations`（套件狀態表標成 released）與 .NET 的 `Microsoft.Agents.AI.Workflows` 都已有 Sequential、Concurrent、Handoff、GroupChat、Magentic 這幾種 builder。Transparency FAQ 也寫 GroupChat、Sequential、Concurrent 在 Python 與 .NET 都可用。遷移指南和 repo 現況不同步，實際行為尚未實測。

### 五種編排

高階編排是 workflow 上的現成房間規則，兩邊原始碼都看得到：

- **Sequential**：參與者接力，後一棒看得到前面的對話。
- **Concurrent**：同一份輸入同時發給多人，再彙整。
- **Handoff**：分診把工作交給專家。Samples 還有自主模式，專家自己做到要交接為止。
- **Group chat**：有人決定下一個誰說話，可以是函式，也可以是一位 manager agent。
- **Magentic**：一位經理先出計畫再派工，可限制輪數、卡住次數與重置次數。Samples 讓人先審計畫，卡住時也可以把人叫進來。

一條 workflow 可以再包成一個 agent，放進更大的流程。Python samples 另有一種寫法：流程就是一般 async function，用 `if` 和 `asyncio.gather` 分支與並行，不必先畫邊。這套函式式寫法在 .NET 是否同等完整，尚未確認。

### Checkpoint、人的關卡、time-travel

Workflow 可以在 superstep 邊界寫 **checkpoint**。遷移指南說快照包含各 executor 的本地狀態、跨 executor 的共用狀態、還沒送出的訊息，以及做到哪一步。儲存示例有本機目錄，也有 Azure Cosmos DB。之後可以列出 checkpoint，從選中的那一點恢復。人的介入用 request/response：executor 送出請求後這條流程暫停，呼叫端把人的回答送回去才繼續。從含有未完成請求的 checkpoint 恢復時，那些請求會再送出來。Magentic 可以把「審計畫」和「卡住時問人」接進同一套暫停。工具核准是另一條線：ADR 0006 決定不要用同步 callback 硬問人，因為遠端 agent 可能人不在場；改成這次 run 先結束，同一條 session 再帶著核准結果進來。

README 與 Transparency FAQ 把 **time-travel** 和 checkpoint、human-in-the-loop 放在一起。讀到的操作說明是從某個 superstep 恢復，沒有另看到獨立的時間旅行畫面。改寫已完成步驟再分出一條新歷史，尚未確認。

### Python 與 .NET 都是這個 repo 的一等實作

README 寫 Python 與 C#／.NET 都有完整框架與對齊的 API，samples 從 agent、workflow 到 hosting 兩邊都有。Transparency FAQ 寫框架有跨 .NET 與 Python 的 conformance testing，並把平台需求寫成 Python 3.10+，以及 .NET 8.0、9.0、10.0 等。Python changelog 記載 `agent-framework` **1.21.0**（2026-10-08）。本次讀到的 commit 是 2026-10-09 的 `fd52de7`，沒有執行。套件狀態表把核心、workflow 所在的 core、orchestrations、Foundry、declarative 標成 released；DevUI、許多連接器、hosting 仍是 beta 或 alpha。Go 不在這個 repo，也不在同一完整度。每一支 API 是否真的對等，尚未確認。

### 觀測、Harness、掛載，以及 DevUI

OpenTelemetry 可包住 agent、workflow 與工具呼叫。Middleware 可以擋在一次 run 的前後。模型與代管服務的官方名單包含 Microsoft Foundry、Azure OpenAI、OpenAI，以及 GitHub Copilot SDK。README 說 Foundry hosted agents 多兩行程式就能部署到代管基礎設施，尚未使用。

**Harness** 是官方 overview 列的第四塊：一個帶待辦、context 壓縮、檔案記憶、技能、以及「不要再問一次」工具核准的預設員工，給較長的多步任務。Python 的工廠會發出 experimental 警告。這是單一員工的執行配備。YAML 也可以描述 agent 或 workflow，方便放進版本管理。

**DevUI** 是 Python 的 sample app（套件分類為 beta）：本機開一個頁面，用來跑目錄裡的 agent 與 workflow、看事件。官方 README 寫明它用來起步，不是 production 介面，正式環境應自己做 UI。這是開發者的除錯頁，留在瀏覽器分頁。

## 主打賣點

- **它要被記住的是後繼，不是第三套無關框架。** AutoGen 的群聊與事件 runtime、Semantic Kernel 的企業向 session 與工具，收成同一套 agent 加 workflow。單 agent 的呼叫方式變簡單；多人合作從「廣播與事件」改成「資料沿邊走」。
- **workflow 才是新加上的控制面。** 順序、並行、交接、會議、經理派工，都是這張圖上的規則。暫停等人、checkpoint 再恢復，是 AutoGen 遷移指南說 `Team` 本身沒有、要在框架外自己做的部分。
- **和 CrewAI 的 Crew／Flow、Maestro 的 Group Chat 是同一類編排想法**，但執行單位是同一次程式裡的 agent 與函式，沒有辦公室畫面，也沒有現成的桌面控制平面。
- DevUI、Foundry 代管、YAML、大量 provider 套件，是開發與部署周邊。開源核心仍是 agent 與 workflow。Go、分散式 runtime、以及 time-travel 能不能改歷史再分叉，都還不能當成已驗證能力。

## 使用情境

### 把舊的 AutoGen 小隊收成可暫停的流程

- 適合誰：已經用 GroupChat 或 GraphFlow 做過多 agent 實驗、想收成可重跑程式的開發者。
- 在什麼情況使用：接力、並行或群聊的形狀還在，但希望每一棒的資料沿邊走，並能在人要看的地方停下來。
- 帶來的價值：遷移指南把這條路寫成從 Team／GraphFlow 對到 Workflow。人的回答與 checkpoint 留在流程上，而不是另寫一套暫停邏輯。對應是否順，尚未實測。

### 確定的步驟中間夾一段判斷

- 適合誰：前後已經是普通程式，只想把分類、起草或審查交給模型的人。
- 在什麼情況使用：workflow 的節點混著函式與 agent；需要判斷的那一棒才叫模型，其餘沿邊傳遞。
- 帶來的價值：總覽建議能寫成函式就不要用 agent。狀態在 workflow，對話歷史在 session，兩邊不混成同一袋。

### 計畫先給人看，再讓經理派工

- 適合誰：任務長、不希望經理自己改計畫就開工的人。
- 在什麼情況使用：Magentic 打開計畫審核；卡住時再把人叫進來。敏感工具另走核准，這次 run 先停，帶著決定再繼續。
- 帶來的價值：人看的是計畫與待核准的動作，不是事後翻 log。Samples 與 ADR 如此描述，尚未實測。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是 agent，身上是名字與指示，對話夾在 session，不長在人身上。這一天的工作是 workflow：誰把哪一種東西交給誰。五種編排是房間規則。順序是接力，並行是同時做再交回，交接是前台分診，群聊是有人點名，Magentic 是經理先把計畫寫出來。Harness 是單一員工的待辦、筆記與核准習慣，不是整層樓的編制。
- 值得借鑑的 interaction / workflow：資料沿走道傳，每一棒只收到邊上送來的那一包。人要介入時，流程在那張桌子停住，請求放在桌上；人寫完回答，同一條線繼續。Checkpoint 是某一 superstep 的快照，隔天可以走回那一步，還沒蓋章的請求還在。工具核准是員工舉手，人點頭才按下去。經理的計畫先上白板，人可以改字，點頭後才派工；卡住時經理出來找人。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層。每個 agent 是固定工位，名牌寫名字和指示。Workflow 是地板上的走道，文件夾沿著走道從這一桌送到下一桌，夾子上標著這一包的型別。Sequential 是一排工位。Concurrent 是同一份文件同時放到幾張桌子，再回到彙整桌。Handoff 是門口的前台，把來客送到專科座位，做完走回前台。Group chat 是會議室，主持人決定誰站起來。Magentic 的經理站在房間中間，計畫貼在白板，人走到白板前改完才放行。Checkpoint 是地板上的時間標記，人可以站回某一個標記。DevUI 留在樓層外的瀏覽器分頁。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。研究員、寫手、審查者各自坐在自己的位子上。順序流程裡，寫手要等文件夾送到才開始。並行時幾個人同時低頭做事，彙整的人站在中間收資料夾。Handoff 是前台把人帶到專科座位，專科做完走回前台，把結果交給站在那裡的人。Group chat 時大家進同一間會議室，主持人點名。Magentic 的經理站在白板前寫計畫，人走過去改字、點頭，員工才回座位開工；卡住時經理走到門口等人。Checkpoint 是某一刻整間辦公室的狀態，人可以走回那個時刻，還沒蓋章的請求還在桌上。重點是看見誰在場、誰在哪裡做事、流程停在誰的桌子。
- 需要重新設計的地方：執行軌跡是事件與 checkpoint，空間辦公室要另做「誰在哪一桌」。這裡的 agent 是程式裡的模型角色；若同時要放 Paperclip 雇來的外部員工，或 OpenRig 那種持久 terminal seat，座位種類要分開。Graphviz 那種流程圖是邊的示意，不要把它當成可走的樓層。DevUI 是除錯頁。Learn 遷移指南仍把交接與選擇發言人寫成未完成，repo 裡已經有對應 builder，先不要把「尚未實作」寫進空間介面。分散式 runtime，以及從 checkpoint 改寫歷史再分叉，都尚未確認。

## 初步看法

- 最有價值的部分：agent 與 workflow 拆開，再加上暫停等人、從 superstep checkpoint 恢復。這直接對應辦公室裡的工位、走道、蓋章和「走回剛才那一步」。
- 最大限制或疑問：它是給開發者呼叫的框架，沒有空間介面。遷移指南、Transparency FAQ 與 repo 對分散式和「哪些編排還在開發」的說法不一致。time-travel 讀起來是恢復 checkpoint，改歷史再分叉尚未確認。
- 是否值得進一步研究或親自體驗：值得，尤其是 handoff 的前台、Magentic 的白板審計畫、以及人蓋章後從同一條 checkpoint 繼續。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official documentation：https://learn.microsoft.com/en-us/agent-framework/overview/agent-framework-overview
- Repository：https://github.com/microsoft/agent-framework
- Migration from Semantic Kernel：https://learn.microsoft.com/en-us/agent-framework/migration-guide/from-semantic-kernel
- Migration from AutoGen：https://learn.microsoft.com/en-us/agent-framework/migration-guide/from-autogen
- License：repo 的 `LICENSE` 為 MIT（Copyright (c) Microsoft Corporation.）
- 本次讀到的 commit：`fd52de71579162a888fb2dd7510c57c6014dcdfc`（2026-10-09，未執行）
- README：Python 與 .NET、workflow、checkpoint、human-in-the-loop、time-travel、DevUI、Go 另倉
- Python changelog：`agent-framework` 1.21.0（2026-10-08）
- `python/PACKAGE_STATUS.md`、`python/samples/03-workflows/`、`dotnet/samples/03-workflows/`、`docs/decisions/0006-userapproval.md`、`TRANSPARENCY_FAQ.md`、`docs/features/durable-agents/README.md`
- .NET 套件的單一發行號，尚未確認
