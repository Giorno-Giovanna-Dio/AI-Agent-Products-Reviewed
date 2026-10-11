# OpenAI Agents SDK

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openai-agents-python-cell-9381/reviews/openai-agents-python.md)
>
> Cell ID：[https://github.com/openai/openai-agents-python](https://github.com/openai/openai-agents-python)
>
> Status：`untried`
>
> Category：Python agent SDK（Runner / Handoff）
>
> Last updated：2026-10-10

## 產品介紹

OpenAI Agents SDK 是 OpenAI 官方的 Python 函式庫，PyPI 套件名是 `openai-agents`，原始碼在 [openai-agents-python](https://github.com/openai/openai-agents-python)。文件首頁說它是實驗專案 Swarm 的正式版：用很少的基本件組出多 agent 流程。基本件是 Agent（帶指示與工具的模型）、把工作交給別的 agent，以及 guardrail。它服務的是要把這段流程寫進自己應用的開發者。它沒有一間可以走進的辦公室，也不是桌面 ADE。另有 JavaScript／TypeScript 版 repo；這份筆記只談 Python。

使用者在程式裡寫好 agent，呼叫 `Runner` 跑一輪。一輪裡模型可以回答、呼叫工具，或把對話交給另一位 agent。官方說預設走 OpenAI Responses API，也可以換 Chat Completions 與其他模型；換供應商之後行為是否相同，尚未確認。README 還列出即時語音與語音管線，這次不展開。本次只讀 README、LICENSE 與 `main` 文件，沒有安裝。查到的發行清單與 PyPI 都是 `0.23.1`。文件要求 Python 3.10 以上。

## 主要 Features

### 一次 Runner：現在是誰在做事

Agent 要有名字，通常再寫 instructions，並掛上工具、可選的 handoff、guardrail、結構化輸出。`Runner.run`（或同步、串流版本）從一位起始 agent 進入迴圈：叫一次模型；若產出的是想要的最終答案且沒有工具呼叫，這輪結束；若是 handoff，換成下一位 agent 再跑；若是工具呼叫，執行後把結果接回去再跑。超過 `max_turns` 會丟出例外。文件把這一次 `Runner` 當成聊天裡的一個使用者回合：裡面可以換好幾個 agent、叫很多次模型。結束時 `last_agent` 是下一句話通常該找的人，`final_output` 是最後那位的答案。另外有一個 context 物件，從 `Runner.run` 傳進每個 agent、工具與 handoff，用來放依賴與這次執行的狀態。它是依賴注入，不是對話逐字稿。

### 兩種交辦，加上程式自己排程

文件把模型自己決定的合作收成兩種，可以混用。

**Handoff**：分流員把對話交給專家。專家變成這輪的現任 agent，並看到先前的對話，除非用 `input_filter` 改掉再送過去。對模型來說，handoff 是一把名為 `transfer_to_<agent>` 的工具。交接留在同一次 run 裡。`nest_handoff_history` 會把可摘要的歷史收成片段，文件標成預設關閉的 beta。

**Agents as tools**：經理留在對使用者的對話裡，用 `Agent.as_tool()` 把專家當成工具叫來。專家做完交回結果，不接管訪客。文件建議：專家只做一段有邊界的子任務時用這個；路由本身就是流程、而且要專家直接對使用者說話時，用 handoff。

第三種是程式排程，不讓模型決定下一棒。文件列了常見寫法：用結構化輸出分類再選下一個 agent、把上一棒輸出接成下一棒輸入、寫手與評審迴圈直到通過、以及 `asyncio.gather` 平行跑。這些是寫在 Python 裡的流程。

### Guardrail 擋在不同的點

Guardrail 是檢查，觸發 tripwire 就停掉這輪。文件把檢查點拆開，不能理解成每人桌前都有一個相同的門衛：

- **Input guardrail** 只跑在這條鏈的第一位 agent，看使用者一開始的輸入。預設與 agent 平行跑，擋下時模型可能已經花了 token、呼叫了工具。改成阻擋模式，則檢查沒過之前 agent 不會開始。
- **Output guardrail** 只跑在寫出最終答案的那位 agent。
- **Tool guardrail** 包在函式工具上，每次呼叫前後都檢查。文件寫明它不套到 handoff，也不套到網頁搜尋這類 hosted 工具。

Output tripwire 拒絕最終答案時，文件說已完成的工具呼叫可以留下可重播的紀錄，但被拒的工具輸出會換成佔位文字，不進 session。這是文件描述，尚未實測。

### 批准把同一輪凍住

人的介入主要是工具批准，不是另開一場聊天。工具設 `needs_approval` 後，模型發出該呼叫時，這輪暫停。`interruptions` 裡有待批項目，帶著 agent 名稱、工具名稱與參數。程式把結果收成 `RunState`，核准或拒絕，再用原本的頂層 agent 與這份 state 續跑。巢狀的 `Agent.as_tool()` 裡若再要批准，文件說仍出現在外層這一次 run，要在外層 state 上決定，再恢復外層。`always_approve` 會讓同一個工具在這輪剩下的呼叫沿用決定，並能跟 state 一起存下來。

`RunState` 可以序列化，稍後在別的行程恢復。文件同時警告：序列化內容含執行狀態、待批呼叫與參數；SDK 還原時不驗證這包資料是誰交的。給瀏覽器或手機批核時，完整快照應留在伺服器，審核者只看被允許看到的工具細節。本機 shell 與改檔工具的批准是選擇加入，預設不暫停。文件另列 Temporal、Restate、DBOS、Dapr，用來把久等、重試、行程重啟做成耐久流程。那些整合是否真的把暫停跑跨行程恢復，尚未確認。

### 對話紀錄、工作間、追蹤儀表

跨回合要記得對話，文件給四種，並要一條對話選一種。自己拿 `to_input_list()` 再送下一輪；或把 `session` 交給 Runner，跑之前讀歷史、跑之後寫回。`SQLiteSession` 是內建的輕量做法。也可以把歷史放在 OpenAI 的 `conversation_id` 或 `previous_response_id`，下一輪只送新的使用者輸入。同一輪不能把 session 和這兩種伺服器續接疊在一起。`OpenAIResponsesCompactionSession` 是包在別的 session 上的壓縮。`AdvancedSQLiteSession` 文件寫了對話分支。沙盒裡的 `Memory()` 是另一件事：把先前工作間學到的筆記放進沙盒檔案（預設 `memories/`），不是對話逐字稿。

**Sandbox agent** 仍是 Agent，仍走同一個 Runner，但多一間會改檔、跑指令的工作間。`Manifest` 決定新工作間一開始有哪些檔案或 repo；`SandboxRunConfig` 決定這輪用哪種沙盒 client、要不要接回上次的工作間。文件把「沙盒 session」定義成這間活的執行環境，並寫明它和對話用的 Session 是兩個詞。`UnixLocalSandboxClient` 給本機開發：文件寫 Linux 上不另加作業系統隔離，macOS 有檔案限制但沒有網路隔離；不受信任的指令應改 Docker 或託管沙盒。尚未實測。

**Tracing** 預設開啟。一次 workflow 是一條 trace，裡面的 span 蓋住 agent、模型呼叫、工具、handoff、guardrail。官方儀表是 [Traces dashboard](https://platform.openai.com/traces)。`group_id` 用來把同一段對話的多次 run 串在一起。這是觀測儀表。`draw_graph` 用 Graphviz 把 agent、工具、handoff 畫成線路圖，是結構圖，不是樓層。文件另寫：採用 Zero Data Retention 的組織無法使用這套 tracing。

## 主打賣點

- **它加上的是一輪執行，以及「誰握住訪客」。** Agent、Runner 迴圈、handoff、把 agent 當工具、guardrail，是文件要人記住的核心。Python 負責把這些接起來。你若想自己管迴圈與工具分派，文件建議直接用 Responses API。
- **和 CrewAI 相比，資料模型不同。** CrewAI 把角色、任務、順序或階層收成一份 crew。這個 SDK 沒有那份任務清單；交辦是「專家接手整段對話」或「經理留下、專家只回結果」，流程多半寫在 Python 裡。這是我們的對照，不是官方比較。
- **和 Paperclip、OpenRig 相比，它不是一直醒著的編制。** Paperclip 雇外部 agent 當員工，OpenRig 的 seat 活在 tmux。這裡的 agent 是同一次 Python 呼叫裡的設定。對話要跨回合，得另接 session；人要隔很久才批，文件指向 RunState 或那些耐久整合。座位不會自己留在樓層上。
- 追蹤儀表、Graphviz、大量 hosted 工具，是執行過程的觀測與動作包裝。開源核心仍是 Runner 這一輪。

## 使用情境

### 分流後由專家接手整段對話

- 適合誰：要做客服或分診，而且希望專家直接回答使用者的開發者。
- 在什麼情況使用：起始 agent 的 handoffs 連到訂位、退款等專家；模型決定把對話交出去。
- 帶來的價值：交接後現任 agent 換人，`last_agent` 指出下一句該找誰。歷史預設跟著走，必要時用 filter 拿掉工具紀錄。

### 對外只有一位經理

- 適合誰：最終答案要由同一人整理，專家只負責一段子任務的人。
- 在什麼情況使用：經理的 tools 裡放 `booking_agent.as_tool()` 這類專家工具，必要時再在程式裡把研究、大綱、改寫串成固定順序。
- 帶來的價值：訪客一直停在經理這輪。專家的輸出回到經理，由經理決定怎麼講。

### 敏感動作先凍住，人批完再繼續

- 適合誰：退款、寄信、改檔、跑 shell 不能直接做的應用。
- 在什麼情況使用：工具標 `needs_approval`；暫停後把 `RunState` 留下，人核准或拒絕再恢復同一個頂層 run。
- 帶來的價值：批准是這輪的中斷，不是新的對話。文件要求完整快照留在伺服器。拒絕原因可以送回模型，讓它改走別的做法。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是名字、指示、工具，以及他能把對話交給誰。一次工作是 Runner 的一輪，輪內有一位現任 agent。交辦有兩種產品含義：handoff 是換一位現任；當成工具是經理不下座。人的關卡是把這一輪凍成 `RunState`。對話櫃、工作間、工作間筆記是三層記憶。
- 值得借鑑的 interaction / workflow：訪客單進門後停在某一桌。Handoff 時單子連同對話夾走到下一桌。經理模式是側桌回條，前台仍對訪客說話。Input 檢查在第一位接待之前或同時；output 檢查在寫出最終答案之後；工具鎖在每次拉抽屜時。批准時員工停在半空，人看工具名稱與參數後蓋章或退回，同一張單從原位繼續。`group_id` 把這位訪客每次進門的軌跡串成同一組，軌跡本身顯示在儀表上。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層。每位 Agent 一張固定工位，名牌寫 name，桌上貼著 instructions。一張訪客單代表這一次使用者回合，停在哪一桌，那一桌就是現任。Handoff 是單子沿著過道走到退款桌，對話夾跟著走；若有 input filter，過桌時夾裡的工具頁被抽掉。Agents as tools 是經理不下座，把紙條送到旁邊隔間，隔間的人不接待訪客。入口閘只服務第一位接待者，出口章只蓋在交出最終答案的那一桌。要批的工具在該工位亮起，整輪收進抽屜（RunState），人來了再從同一格抽出。對話 session 是按編號的檔案櫃，下一輪先把紀錄鋪上桌。沙盒是樓層旁的工作間，Manifest 是進門時桌上該有的檔案；`memories/` 是工作間裡的筆記。追蹤是牆上的儀表，Graphviz 是佈告欄上的線路圖。人在樓層裡工作，看儀表是另外一個動作。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你進門看見誰坐在接待桌、訪客停在誰面前。Handoff 時，你看見訪客被帶到另一張桌子，那位專家開始對他說話；`last_agent` 是你離開後、下一句還該找的那個人。經理模式裡，經理始終坐在前台，專家在側桌低頭做事，訪客聽不到側桌，結果用紙條送回。批准時員工停在動作中間，你走過去看工具與參數，核准或拒絕後看同一個人繼續；隔天可以走回同一個凍結時刻。入口的人只攔第一位接待前的訪客，出口的人只退回最後那封信。工作間在走廊盡頭，你走進去看見檔案與終端，和對話座位分開。追蹤仍是牆面螢幕。重點是看見誰在場、誰正握著這位訪客。
- 需要重新設計的地方：SDK 沒有「人在哪裡」。`current_agent` 與 `last_agent` 只是結果欄位，空間介面要自己畫座位。兩種交辦必須長得不同，否則每個人都會以為專家正在跟訪客說話。Input 只在鏈上第一位、output 只在最後一位；每張桌子都站一個警衛，會把文件的規則畫錯。對話 Session、沙盒 session、沙盒 `Memory()` 不能畫成同一層抽屜。追蹤儀表與 Graphviz 留在牆和佈告欄。文件要求批准用的完整 RunState 留在伺服器。`nest_handoff_history` 預設關閉。Linux 上的 `UnixLocalSandboxClient` 文件寫明沒有作業系統隔離，那間工作間不能畫成上鎖的房間，除非 client 是 Docker 或託管沙盒。文件的 release 頁寫明 `0.Y.Z` 的 minor 可以包含破壞性變更。以上行為都尚未跑過。

## 初步看法

- 最有價值的部分：誰握住訪客，以及同一輪可以凍住等人。這兩件事直接決定 2D／3D 辦公室裡單子怎麼移動、人要走到哪一桌。
- 最大限制或疑問：它是函式庫，跑完就結束，座位不會自己留著。沙盒隔不隔離取決於 client。耐久整合與非 OpenAI 模型的實際行為都尚未確認。
- 是否值得進一步研究或親自體驗：值得，尤其是 handoff 與 as_tool 在同一層樓裡如何被一眼分開，以及批准凍結後如何走回原位。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official docs：https://openai.github.io/openai-agents-python/
- Repository：https://github.com/openai/openai-agents-python
- Package：https://pypi.org/project/openai-agents/ （查詢版本 0.23.1）
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright (c) 2025 OpenAI）
- 讀取的 `main`：`125efa029b4bfd84238bd2c4fd69c3406f802663`（2026-10-08）。`.release-please-manifest.json` 為 0.23.1。沒有安裝。
- README：Agents、handoffs、guardrails、sessions、tracing、sandbox、四種跑法
- 已讀文件：index、agents、handoffs、multi_agent、running_agents、guardrails、human_in_the_loop、sessions、tracing、results、tools（分類與 agents as tools）、sandbox_agents、sandbox/guide、sandbox/memory、visualization、release、advanced SQLite session
