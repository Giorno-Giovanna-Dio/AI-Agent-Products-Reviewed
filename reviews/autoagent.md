# AutoAgent

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autoagent-cell-ca43/reviews/autoagent.md)
>
> Cell ID：[https://github.com/HKUDS/AutoAgent](https://github.com/HKUDS/AutoAgent)
>
> Status：`untried`
>
> Category：Python 零程式碼多 agent 框架（CLI）
>
> Last updated：2026-10-10

## 產品介紹

AutoAgent 是香港大學 HKUDS 的開源 Python 框架，前稱 MetaChain。使用者在終端機用自然語言組出 agent、工具和工作流程，也可以直接把題目交給一組現成的研究小隊。官方把它說成一套「agent 作業系統」：內建網頁、程式、檔案三種專員，中間有一個會分派的分診員，另外再加向量資料庫，以及用對話改寫自己程式的客製流程。專案頁在 [autoagent-ai.github.io](https://autoagent-ai.github.io)，論文是 [arXiv:2502.05957](https://arxiv.org/abs/2502.05957)。

進入點是 CLI 指令 `auto`。`auto main` 先開一個選單，三個模式是 user mode、agent editor、workflow editor。`auto deep-research` 只開比較輕的 user mode。文件站多數指南頁還是空的，這次以 README、論文和 `main` 的程式結構為準，沒有安裝。

## 主要 Features

### 現成小隊：分診員和三個專員

user mode 的起點是 System Triage Agent。它看目前進度，寫下一張子任務說明，再把對話交給 File Surfer、Web Surfer 或 Coding Agent。專員做完用 `transfer_back_to_triage_agent` 交回一段任務狀態，分診員再決定下一棒，直到它呼叫結案。人可以在提示列用 `@` 點名某一位，或用 `@Upload_files` 把檔案放進 workplace 的 `files` 目錄。結案若以 Case resolved 開頭，CLI 會試著抽出 `<solution>` 裡的文字。

三位專員的房間不同。Web Surfer 用搜尋、開啟網頁、點擊、翻頁，瀏覽器環境接 BrowserGym；下載的檔在 `downloads`，它自己不打開，要交回分診員再轉給 File Surfer。File Surfer 把 pdf、簡報、試算表、聲音和一般文字轉成可翻頁的 Markdown，圖片另走視覺問答；打不開就交回去，讓 Coding Agent 用程式處理。Coding Agent 負責建檔、跑 Python、下指令；終端機輸出太長就上下翻頁，避免一整段塞進模型上下文。

### 用對話雇人：agent editor

agent editor 是一條固定的雇用線，三個後台角色輪流上場。Agent Former 把需求收成 XML 員工表，解析失敗會把錯誤丟回去重寫，最多三次。接著人可以對工具提意見，或直接按 Enter。Tool Editor 必須把表上的新工具寫出來，並用 `run_tool` 跑過，才能蓋 Case resolved；失敗同樣最多再試三次，然後詢問人的建議。最後人可再給建議，並可選填一道考題；留空就讓系統自己出題。Agent Creator 依表建立 agent。超過一位時，它必須再做一個 orchestrator 把大家接起來，然後用 `run_agent` 跑那道考題。

`auto main` 預設會把 AutoAgent 自己 clone 成鏡像（預設分支名 `autoagent_mirror`），再依任務開一條分支。新的工具和 agent 是寫進這份鏡像的程式，不是只停在對話裡。README 寫這一步需要自己的 GitHub token。

### 事件流程：workflow editor

workflow editor 給的是「誰聽誰做完才開始」。Workflow Former 同樣先出 XML，解析若違反限制（例如 `on_start` 的規則）就把錯誤退回重寫。Workflow Creator 若表上有標成 new 的 agent 就先建人；README 寫這個模式暫時不能新建工具，程式也是沒寫工具時用空清單。接著 `create_workflow`、`run_workflow`，成功才 Case resolved。

流程引擎把每個 agent 的工作當成一個事件。事件用 `listen_group` 訂閱上游，預設要全部到齊（`all`）才觸發，也可以改成任一（`any`）。回傳可以往下派、跳到別的事件、中止，或停下來等人輸入。倉庫裡的例子 `majority_voting`：`on_start` 同時叫醒三個解題員（程式裡寫死 gpt-4o、Claude 3.5、DeepSeek），三份答案都進共享 context 之後，`aggregate_solutions` 才做多數決。

### 兩種記得事情的方式

這一輪怎麼做的，留在訊息串裡：工具呼叫和觀察結果一路累積，論文把它比成記憶體。另一櫃是 Chroma 向量庫，程式分成一般記憶、工具說明、程式、論文文字。論文描述的自管檔案系統會把上傳的文字收進使用者指定的 collection，再用查詢工具回答。Agentic RAG 是一條評測腳本，文件頁多半是示意。它是否勝過 LangChain，是官方說法，尚未實測。

### 執行環境和模型

文件要求用 Docker 包住 agent 能碰到的環境。`auto main` 預設容器名 `auto_agent`、連接埠 `12347`；`auto deep-research` 預設容器名 `deepresearch`、連接埠 `12346`。映像依 CPU 架構選 `tjbtech1/metachain`。容器以 root 啟動，並把本機 workplace 目錄掛進去，裡面跑一支 TCP 服務。CLI 另有 `local_env` 開關，可以不開 Docker。兩種環境差在哪、預設映像是否仍可用，尚未確認。

模型經 LiteLLM，環境變數 `COMPLETION_MODEL`，README 預設 `claude-3-5-sonnet-20241022`。部分模型名稱會關掉函式呼叫，改走論文說的結構化文字。函式呼叫和這條退路實際差多少，尚未確認。

## 主打賣點

- **它想被記住的是：用一句話雇人，系統自己寫程式再考一次。** 員工表、工具、agent、流程都從自然語言長出來，人只在每段結束時看結果、給建議、出考題。
- **現成小隊是分診加專員。** 工作以一張子任務說明交出去，以一段狀態交回來。README 寫這組三人設計受益於 Magentic-one。和 CrewAI 那種先寫好 role、goal、task 再跑一輪不同，這裡的常駐員工是寫死的三種專員，新員工是事後生成的程式。
- **流程是聽事件，不是先畫一張死圖。** 倉庫裡的數學例子是三人同時做、等齊了再投票。
- 零程式碼是對外說法。底層仍是在 Git 鏡像裡生成並改 Python。Web GUI、E2B、Composio、更多評測都在 README 待辦。論文寫已支援類似 E2B 的第三方沙盒，和待辦清單還沒對上，尚未確認。

## 使用情境

### 把一個題目交給現成小隊

- 適合誰：想先檢索、讀檔、必要時寫程式，再拿一份答案的人。
- 在什麼情況使用：選 user mode 或 `auto deep-research`，題目打在提示列，檔案用 `@Upload_files` 放進去。
- 帶來的價值：分診員決定這一棒是上網、讀檔還是寫程式，人可以用 `@` 改點某一張桌子。

### 用一句話雇一組新員工並立刻考他們

- 適合誰：知道要什麼角色，但不想先手寫 agent 程式的人。
- 在什麼情況使用：agent editor。先看 XML 員工表，再看工具有沒有跑過，最後給一道考題或讓系統自己出題。
- 帶來的價值：多個角色會多出一位 orchestrator。工具沒跑過就不能結案，失敗會停下來問人的建議。

### 同一題分給幾個人，等齊再表決

- 適合誰：步驟大致固定、希望並行再彙總的人。
- 在什麼情況使用：workflow editor，或直接跑已註冊的 `majority_voting`。
- 帶來的價值：彙總的人預設要等所有上游事件，而不是誰先做完就往下。這個模式暫時不新建工具。

## 我們可以學什麼

- 值得借鑑的 product idea：辦公室有兩種編制。常駐的是分診櫃檯加網頁、檔案、程式三張桌子。另一種是雇用線：需求先變成員工表，工具要試過，新桌子才出現；人多就最後再擺一張調度桌。流程是「聽誰做完」，共享 context 是大家都能翻的同一本工作筆記。
- 值得借鑑的 interaction / workflow：子任務是一張寫清楚的紙條，交回的是任務狀態。人用 `@` 走到某一張桌子，用上傳把檔案放進文件盤。雇用線每過一關都問人有沒有建議，工具和員工都要實際跑過才能蓋章。事件的 `all` 是等人到齊，`any` 是誰先回來就開始，`input` 是停下來走向人。結案要把答案放進 `<solution>`，旁邊留下 Case resolved 或 Case not resolved。
- 在 2D workspace 裡會變成什麼：一張俯視樓層。大廳有三扇門：研究小隊、雇用工坊、流程室。研究小隊是分診櫃檯面對三張固定工位。紙條從櫃檯送到工位，做完送回。網頁工位旁是瀏覽器，下載籃要有人拿去檔案工位才打得開。檔案工位是一台可翻頁的讀稿機。程式工位在一間標出界線的房間裡，終端機輸出以頁碼攤在桌上。雇用工坊是一條直線：寫名牌、試驗工具、新工位落地；失敗就停在試驗桌等你的一句建議。流程室裡，三人同時低頭做同一題，投票的人要等三份答案都回到桌上才站起來。向量庫是貼了標籤的櫃子（文件、工具、程式、論文），這一輪的對話則是工位上還沒歸檔的紙。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你進門就看見分診員站在櫃檯，網頁、檔案、程式三位在自己的位子上。分診員把資料夾走到某一張桌子，對方做完再走回來。你也可以自己走到某個人面前說話，或把檔案放上文件盤。程式員在一間你看得到裡面的房間工作，很長的輸出是桌上的一疊分頁。雇用進行時，你看見名牌先寫好，工具在工作台試跑，通過之後新同事才出現在新座位；若是一組人，調度員最後才站到房間中間。流程室裡三個人同時開工，投票的人坐著，直到三個人都把答案放回共享桌才起身比較。重點是看見誰在場、誰正拿著那份資料夾、誰在等誰。
- 需要重新設計的地方：現在的進度是終端機彩色輸出和 log 檔，空間辦公室要另做「誰在哪一桌、紙條走到哪」。新員工被寫進框架自己的 Git 鏡像，等於員工可以改辦公室的建築圖；空間裡應把「這次雇用的副本」和本體分開，並讓人看見那條分支。容器以 root 跑、workplace 整目錄掛進去，房間的牆比上鎖抽屜寬，界線要畫出來。文件站指南幾乎是空的。GAIA 名次在文件站（開源第一）和論文 4.1（第二）說法不同，排行榜本次沒核對。`setup.cfg` 版本是 0.1.0，README 新聞寫 2025-02-17 的 v0.2.0，哪個才是目前發行，尚未確認。E2B 在論文和待辦裡互相矛盾，先不要做進空間介面。

## 初步看法

- 最有價值的部分：分診紙條、雇用線上的人為關卡，以及「等人到齊再匯合」的事件。這三件事都能直接變成工位和走動。
- 最大限制或疑問：它是 CLI 裡輪流說話的角色，不是各自長開的座位。文件站還沒寫完，版本號和沙盒說法還沒對齊。生成出來的員工品質要跑過才知道。
- 是否值得進一步研究或親自體驗：值得，尤其是 `@` 點名、工具必須跑過才放行、以及並行再投票，在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Project page：https://autoagent-ai.github.io
- Documentation：https://autoagent-ai.github.io/docs （指南頁多為空殼；本次實質內容在 README、論文與 source）
- Repository：https://github.com/HKUDS/AutoAgent
- Paper：https://arxiv.org/abs/2502.05957
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright © 2023，未寫著作權人姓名）
- README（`main`）：user mode、agent editor、workflow editor、Docker、`auto main`／`auto deep-research`
- 已讀程式：`autoagent/cli.py`、system triage 與三種專員、agent／workflow editor、`flow` 事件引擎、`majority_voting` 範例、Docker 環境、Chroma memory
- `setup.cfg`：套件版本 `0.1.0`，Python `>=3.10`，指令 `auto`
- `main` 最新 commit（2025-10-16）：`16c12b052ef2330a198063c62a07a7f9723031e3`（只改 Communication.md）
