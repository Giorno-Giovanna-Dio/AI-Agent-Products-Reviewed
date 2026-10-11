# HelloAgents

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-helloagents-cell-ca43/reviews/helloagents.md)
>
> Cell ID：[https://github.com/jjyaoao/HelloAgents](https://github.com/jjyaoao/HelloAgents)
>
> Status：`untried`
>
> Category：Python 元件化智能體框架（Function Calling）
>
> Last updated：2026-10-10

## 產品介紹

HelloAgents 是給 Python 開發者組裝的智能體元件庫。套件名是 `hello-agents`，`pyproject.toml` 與 `hello_agents/version.py` 都寫 1.0.1，需要 Python 3.12 或 3.13。開發者在程式裡建立一個 Agent、掛上工具註冊表，然後跑一輪「模型提出工具請求 → 程式執行 → 回執回到下一輪」。預設不開技能、子代理、待辦、決策日誌、會話存檔和軌跡；要哪些，再用設定或 `AgentComponents` 接上。它沒有桌面畫面，也沒有給人走進去的辦公室。本次只讀 GitHub 上的 README、LICENSE 與 `docs/`，沒有安裝。

它和 Datawhale 的教材 [hello-agents](https://github.com/datawhalechina/hello-agents)（線上閱讀 [hello-agents.datawhale.cc](https://hello-agents.datawhale.cc)）有關，但是另一個 Cell。教材是一本從原理寫到案例的中文教程，第七章要讀者自己做框架，並指向這個倉庫。本倉庫 README 把 `learn_version` 分支寫成跟教材正文對應的學習版，把 `main` 的 v1.0.1 寫成按現行源碼維護的工程版；歷史 release v0.1.1 到 v0.2.9 則對應教材章節。本次沒有逐章比對 `learn_version` 和教材是否仍然一致，對應程度尚未確認。README 還提到 AtomGit 鏡像，以及社群的 Go、TypeScript 重寫；鏡像是否同步、重寫是否跟上 v1.0.1，尚未確認，也不併進這個 Cell。

授權檔是 CC BY-NC-SA 4.0：使用要署名、改作要相同方式共享、不得用於商業目的。GitHub API 把這份授權標成 Other／NOASSERTION，以倉庫裡的 LICENSE 與 `pyproject.toml` 的 `CC-BY-NC-SA-4.0` 為準。README 寫商業使用要聯絡維護者，授權條件尚未確認。

## 主要 Features

### 同一套循環上的四種做法

`SimpleAgent`、`ReActAgent`、`ReflectionAgent`、`PlanSolveAgent` 共用 `core/runtime.py`。Agent 決定提示詞和階段，執行器負責叫模型、跑工具、回填、判斷結束。一個 Agent 實例一次只處理一件事；要並行就再建一個實例。

- **Simple**：依目前訊息叫模型，執行請求，把回執放回，再繼續。
- **ReAct**：多了 `Thought` 和 `Finish`。含思考或結束的那一批按順序處理；同一回應裡的一般工具可以有限並行，會改同一份資源時應把並行數設成 1。
- **Reflection**：先做、再評、再按需要改。評語是公開文字。類別裡另有一份只活在這次嘗試的軌跡（上一輪產出、評審意見），和下面的 SQLite 記憶不是同一件事。
- **Plan-and-Solve**：規劃者必須用 `generate_plan` 交出步驟清單，再逐步執行。計畫輸出被截斷就不會執行半份計畫。文件寫明它不會自動證明每步正確，也沒有額外的外部驗收階段。

`run`、`arun`、同步串流、非同步串流四個入口都會執行工具。`last_run` 有 `completed`、`max_iterations`、`output_limit`、`failed`、`cancelled`。`completed` 只表示模型交出回答或 ReAct 用 Finish 結束，文件寫明這不代表外面的任務已經驗收。

### 三種記得事情的地方

這三份儲存可以一起用，職責分開。框架不會自動把整段對話抽成事實。

- **會話（SessionStore）** 存這一輪發生過的訊息、工具請求和回執，以及設定摘要、工具 Schema 雜湊。恢復是把紀錄攤開，不會重放工具。已記錄請求但沒有回執的，補上 `execution_status="unknown"`：無法從紀錄確認結果。取消或失敗仍保留已經拿到的回執。強制中止處理程序不保證存檔完成。
- **任務記憶（MemoryStore）** 綁定宿主給的 `user_id` 和 `task_id`。一筆記錄有類別（偏好、事實陳述、事件、推斷、做法）、來源和狀態。`revise` 把舊記錄標成已被取代再寫新的；`retract` 讓它退出目前檢索，稽核還在。預設搜尋是關鍵詞重疊。換一個 `task_id` 就讀不到另一個任務的記錄。
- **畫像（ProfileStore）** 是同一使用者、同一應用範圍裡少量、有欄位型別的資料，例如步行上限。每次寫入要帶已讀過的版本；兩個會話同時改，只有一個成功，另一個收到 `ProfileConflict`。`forget` 是退出目前讀取，檔案、備份、歷史還在。每次叫模型前，Provider 會重讀有效畫像。畫像文字進上下文，文件要求不要把它提升成系統指令；工具仍要自己檢查實際操作是否符合限制。

上下文組裝把記憶和檢索放進任務前面的使用者參考訊息，帶來源，整包放不下就整包排除，並留下選了什麼、排除原因。必需資料超過預算會直接報錯，不會悄悄刪掉任務。歷史壓縮預設只統計輪數和則數；要留下的目標和約束，文件建議寫進記憶或另寫經過檢查的摘要。

**Skills** 是另一條線：`SKILL.md` 先露出名稱和用途，模型需要時再用工具把正文取回。正文以工具回執進入對話，不會自動變成系統指令，也不會執行裡面的腳本。使用者偏好進記憶，可檢索的大量資料進 RAG，可重用的步驟才放 Skill。

### 子任務：獨立記事本，共用工具

`TaskTool` 或 `run_as_subagent()` 把一段明確子任務交給另一次運行，對話紀錄分開，做完把摘要交回。類型可以是 simple、react、reflection、plan。`readonly` 是名稱白名單（讀檔、檢索、記憶查詢；可寫入的 MemoryTool 不在裡面），`full` 排除若干命令執行工具名，`none` 不篩。未知類型或過濾器會回參數錯誤，不會改成不受限制的執行。

預設子代理工廠依子設定重新組裝，不繼承父代理的 Task、Skill、TodoWrite、DevLog，預設也不允許再巢狀派工。子任務期間暫停父會話自動存檔，結束時把父代理的歷史、註冊表、步數限制放回來。文件寫得很直：工具物件和熔斷器仍然共用；名稱過濾管不到工具內部的檔案、網路或寫入。獨立上下文不是獨立處理程序，也不是權限沙箱。子任務的 `success` 只表示跑完，不表示事實已核對。

### 宿主閘門、檔案修改、後台工作

工具看不看得到，和允不允許執行，是兩件事。`ToolRegistry` 的策略在執行前檢查；拒絕或策略出錯就不執行，也不算進熔斷失敗。策略看到的是參數副本。人要核准時，文件要求宿主介面核對工具名、參數、身份和現況，再給這一次有限許可；模型說「已獲批准」不算。這個元件沒有持久的審批佇列。直接呼叫 `tool.run()` 不會經過策略。

檔案工具是 Read、Write、Edit、MultiEdit。Edit 要求原字串剛好出現一次；MultiEdit 先在記憶體裡依序檢查，全部通過才寫入。讀過之後檔案被改過會回 `CONFLICT`，要重讀再決定。覆寫前會留備份，但不會自動還原。`project_root` 是路徑起點，文件寫明它不是存取沙箱，絕對路徑和目錄外路徑要宿主另外限制。

後台 `JobQueue` 把任務放進 SQLite，`JobWorker` 執行宿主註冊的處理函式，記錄進度、結果和失敗。載荷是 JSON，不會從載荷還原程式，模型也不能指定要跑哪個函式。工人領取時拿到租約；處理程序中斷後，租約到期別人可以再領，並依已存進度繼續，不會從 Python 呼叫堆疊原點接著跑。相同冪等鍵加相同載荷會回到原任務。沒有 Cron、團隊協作或 worktree 生命週期。佇列只存任務，不自動開工。

`TodoWrite` 把目前清單存成完整快照，同時最多一項 `in_progress`。狀態由呼叫方填寫，工具不檢查成果。`DevLog` 按會話記下決策、進展、問題和辦法，不會自動全部注入下一輪。`TraceLogger` 把模型輸出、工具請求、回執、錯誤和結束寫成 JSONL 與 HTML。HTML 是這一輪的閱讀頁，文件寫明它不是長期監控後台。

RAG、GraphRAG、Qdrant、MCP（stdio／HTTP）和 FastAPI SSE 是選用附加。檢索評測（Recall、MRR、nDCG）評的是找資料的品質。這些都要額外依賴，本次沒有安裝。

## 主打賣點

- **它加上的是職責拆開的資料契約。** 會話記發生過什麼，任務記憶記目前仍有效、可修訂的陳述，畫像記每次都要遵守的少量欄位，Skill 記可重用步驟，軌跡記這輪呼叫，決策日誌記人能讀的階段說明。很多框架把這些收成一條聊天。
- **四種經典做法坐在同一條執行循環上。** ReAct、反思、計畫再執行是教材裡的範式。這個倉庫的工程差異是共用執行器、可替換元件，以及會話、記憶、權限、子任務的邊界寫進文件。
- **文件反覆標出「看起來完成」和「真的被允許」的差距。** 跑完不等於驗收；子任務換了記事本不等於換了房間鎖；看得到工具不等於執行得了；畫像進了上下文不等於路線工具會拒絕超距；撤回記憶不等於從日誌刪除；載入 Skill 不等於腳本已執行。
- MCP、RAG、GraphRAG、SSE、向量檢索是既有能力的選用包裝。開源核心仍是 Agent 循環和可注入的元件。商業授權路徑尚未確認。

## 使用情境

### 偏好會改，舊值還要留底

- 適合誰：要做會跨次使用的助手，例如旅行規劃，步行上限會被使用者更正。
- 在什麼情況使用：宿主把確認過的句子寫進 MemoryStore 或 ProfileStore；更正時修訂並帶版本，而不是再新增一條互相矛盾的偏好。
- 帶來的價值：下次呼叫讀到的是目前有效值，舊值留在修訂鏈上。另一個 `user_id` 用這個實例讀不到。模型不會自動把推測寫成使用者確認。

### 做到一半關掉，明天接著做

- 適合誰：長任務會被取消、逾時或關串流的開發者。
- 在什麼情況使用：先看 `last_run` 和工具回執，再 `save_session`；恢復後先核對標成 unknown 的外部狀態，再決定繼續、補查或重試。
- 帶來的價值：已成功的寫入不會因為恢復而再做一次（文件中的離線示例是這樣設計的）。有副作用的操作仍要宿主自己的冪等鍵。真實模型會不會再次要求寫入，尚未實測。

### 把核對丟進側間，主對話保持短

- 適合誰：主任務要維持自己的對話，只想把查資料、對條件交給一次獨立運行。
- 在什麼情況使用：用 `readonly` 過濾器派一個子代理，收回摘要。
- 帶來的價值：父代理的歷史不會被子任務的來回塞滿。過濾器只限制看得到哪些工具名；會改檔案的工具若仍共享，側間的寫入會出現在主位的檔案上。

## 我們可以學什麼

- 值得借鑑的 product idea：一位員工一次一件事。做法是四種工位模式（直接循環、想一下再交卷、做完公開評審、先釘計畫再走）。記得事情分成三個櫃子：桌上的畫像卡、這個任務的檔案櫃、可合上的會話活頁。步驟手冊在架子上，用時才抽出。
- 值得借鑑的 interaction / workflow：人委派的是「這一次、這些參數」，走到工具閘門蓋章。子任務帶一張摘要回來。計畫沒寫完就不釘上牆。待辦是整張換新的清單，同時只有一項在做，而且「完成」要等人或程式另外驗。後房的工人由宿主雇用，載荷裡不能變出新的可執行員工。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層。中間一張工位就是一個 Agent 實例，名牌寫目前的做法。Simple 是桌上的問答循環。ReAct 多一張思考紙條和一枚交卷章。Reflection 是同一張桌子先寫草稿，再轉到旁邊的評審席，評語寫在公開的紙上。Plan-and-Solve 把步驟釘在身後的板子上，人沿著步驟走；走到下一步只表示人移動了。畫像卡貼在桌上，每次開口前重讀，卡上有版本戳，兩次同時修改會撞在一起。任務檔案櫃按使用者和任務上鎖，被取代的卡片還在、蓋了章。會話活頁可以明天再攤開，工具不會重演，缺回執的那格標 unknown。Skill 是架上的手順，抽出來不會自己跑腳本。待辦是桌上那張清單。決策日誌是手寫本。JSONL 軌跡收在抽屜。工具台有閘門。檔案先讀再改，讀完被別人動過就退回重讀。後房板子上是 queued、running、succeeded，工人胸口有租約計時，進度是書籤。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走進去看見一位員工坐在自己的位子，看得出他在等模型、在用工具，或桌上立著結束牌（completed、max_iterations、failed、cancelled）。反思時他轉到旁邊的椅子把評語說出來，你聽得見。計畫釘在身後，他沿著步驟走動。子任務是他派一個人進側間：側間有自己的記事本，帶回來的是一張摘要；側間和主位可能共用同一組工具，所以你走過去會看到主位的檔案在側間工作時也被改了，門上的 readonly 名單沒有把房間鎖上。你要放行寫入時走到閘門，看這一次的工具名和參數。畫像卡的版本衝突發生在桌上。後房有人拿租約跑索引，人不見了，板上的工作還在，等租約結束別人從書籤繼續。RAG 和 GraphRAG 是他們走過去翻的資料架。軌跡 HTML 和 SSE 頁是事後可翻的這一輪紀錄。重點是看見誰在哪一張桌子、側間有沒有人、閘門前有沒有人在等蓋章。
- 需要重新設計的地方：HelloAgents 的執行軌跡是 log、JSON 和 HTML，空間辦公室要另做「誰在哪一桌」。一個實例不能同時服務兩個請求，並行就是多張桌子，共用的工具物件仍要自己處理搶寫。`project_root`、名稱白名單和提示詞都不是沙箱，辦公室若要鎖房間，鎖要做在宿主和工具裡。Profile 與 ContextProvider 的注入文件寫明目前接在 SimpleAgent；另外三種 Agent 能不能同樣每輪重讀畫像，尚未確認。`learn_version` 與教材的章節對應、AtomGit 是否同步、商業授權條件，都尚未確認，先不要寫進空間介面。

## 初步看法

- 最有價值的部分：會話、任務記憶、畫像、技能、待辦、決策日誌彼此不混，再加上執行前的宿主閘門。這些可以直接變成辦公室裡不同的櫃子、側間和蓋章點。
- 最大限制或疑問：它是給開發者呼叫的函式庫。子任務隔離、畫像衝突、中斷恢復是否如文件所寫，要跑過才知道。非商業授權會限制直接把這份實作放進產品；我們要學的是空間裡的分工。
- 是否值得進一步研究或親自體驗：值得，尤其是三種記憶在 2D／3D 辦公室裡怎麼被看見，以及閘門蓋章和側間共用工具要怎麼讓人一眼看懂。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Repository：https://github.com/jjyaoao/HelloAgents （`main`，套件 `hello-agents` 1.0.1；預設分支 `main`，最近推送 2026-10-06）
- License：倉庫 `LICENSE` 為 CC BY-NC-SA 4.0；`pyproject.toml` 的 `license` 同為 `CC-BY-NC-SA-4.0`
- README（`main`）：v1.0.1 與 `learn_version`、Datawhale 教材、歷史 release、元件一覽
- 相關教材（另一個 Cell，未併入）：https://github.com/datawhalechina/hello-agents 、https://hello-agents.datawhale.cc
- 已讀文件：runtime、function-calling、subagent、context、session、memory、profile、skills、tool-policy、file tools、todowrite、devlog、background jobs、observability、component composition
- 已讀源碼開頭：`simple_agent.py`、`react_agent.py`、`reflection_agent.py`、`plan_solve_agent.py`、`factory.py`、`version.py`
