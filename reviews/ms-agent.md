# MS-Agent

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ms-agent-cell-ad04/reviews/ms-agent.md)
>
> Cell ID：[https://github.com/modelscope/ms-agent](https://github.com/modelscope/ms-agent)
>
> Status：`untried`
>
> Category：Python agent harness（CLI／TUI／WebUI）
>
> Last updated：2026-10-10

## 產品介紹

MS-Agent 是 ModelScope 的開源 Python 框架，用來把模型、工具、skills 和子 agent 收成一個能跑長任務的助手。現行 `main` 的 README 把它寫成可調整的 harness：這層負責規劃、上下文、權限和執行回饋；專案記憶與排程讓工作隔天還能接著做。同一套 Python SDK 同時服務命令列、終端機 TUI 和瀏覽器 WebUI。使用者是要做研究、寫程式，或把 agent 嵌進自己應用的開發者。本次只讀 README、LICENSE、英文文件與部分原始碼，沒有安裝。

先前把它收成「輕量框架，只管任務執行和狀態」並不完整。授權確實是 Apache-2.0（`LICENSE` 開頭為 Copyright 2023-2025 Alibaba ModelScope）。架構比那句話寬：賣點是長任務的 harness，再加上幾條寫好的領域工作流。英文文件站的 Quick Start 仍停在較早的 LLMAgent 迴圈，和 `main` README 不是同一層敘事。GitHub 最新發行是 v1.6.0（2026-03-23）。README 寫明這個 PyPI 版沒有現行 TUI，WebUI 也是舊的；新介面要從原始碼安裝。`main` 在 2026-07 還加上 Agent Hub，比 v1.6.0 新。

## 主要 Features

### 一圈推理、工具，以及人可以插手的鉤子

基本員工是 LLMAgent。它讀 yaml 設定，接上模型與工具，必要時讀歷史訊息，壓縮上下文，呼叫模型，再依結果呼叫工具。停下來的條件是模型不再呼叫工具，或達到設定裡的回合上限。Callback 可以插在任務開始、生成前、工具前後和任務結束，用來擋下流程、記 log，或檢查結果。需要大改時，改繼承 LLMAgent，而不是把複雜邏輯塞進 callback。另一種員工是 CodeAgent，只有一段自己寫的 `run`，不走模型迴圈。

### 兩種編制：主責分派，或事先排好的流程

開放題目可以讓主 agent 把工作派給專責子 agent。階段固定時，用 yaml 寫流程。ChainWorkFlow 是一棒接一棒；下一步沒寫自己的設定，就繼承上一棒。DagWorkflow 在 FinResearch 裡實際使用：orchestrator 同時把工作交給 searcher 和 collector，最後在 aggregator 會合。英文 Workflow 文件目前只寫 ChainWorkFlow。DAG 的通用說明尚未在那一頁確認，但 FinResearch 的 `workflow.yaml` 標了 `type: DagWorkflow`。

幾條現成工作流把編制寫死：

- **Agentic Insight v2**：Researcher 指揮 Searcher 和 Reporter。計畫、證據和章節放在輸出目錄的檔案裡，報告的主張要綁回證據。`--load_cache` 可以從上次的輸出接著跑。現行 WebUI 指南寫明，新的瀏覽器介面不再提供舊的 Deep Research 選單，這條工作流要走 CLI。
- **CodeGenesis**：標準七段是使用者故事、架構、檔案設計、依賴順序、安裝、寫碼、修正；精簡四段把前面的設計收成一個 orchestrator。寫碼依依賴順序進行，並用 LSP 與執行結果再改。
- **FinResearch**：五個角色分頭做拆解、蒐集、量化、輿情和寫報告。結構化金融資料與網路上的公開資訊最後合成一份報告。
- **DocResearch** 把多份文件或網址收成帶圖報告。**Singularity Cinema** 從題目走到腳本、分鏡和短片。這些是框架上的範例工作，不是另一個產品。

### 三層脈絡，外加可換的長期記憶

README 把 session 歷史、當下 context、project memory 分開。完整紀錄留著；工具輸出可以剪掉，較舊的對話可以壓成摘要，下一輪再從專案記憶撈。設定文件把壓縮寫成 `context_compressor`、`refine_condenser`、`code_condenser`。原始碼的記憶對照表把這三種標成 deprecated，註解建議改走 session／strategies；同一張表還有 `unified_memory` 和較舊的 `default_memory`。`unified_memory` 可換後端（檔案、mem0 等）。同一工作目錄的 agent 共用一份 store，註解寫到檔案後端會碰到 `MEMORY.md`，路徑在輸出目錄下的 `.ms_agent/memory`。WebUI 把設定、session 和受管 skills 預設放在 `~/.ms_agent`，專案檔仍留在原本的資料夾。WebUI 預設接哪一個記憶後端，尚未確認。

知識搜尋是另一條線。Sirchmunk 的 `localsearch` 由模型在需要時呼叫，不是每回合自動塞進 prompt。

### Skills、權限與沙盒

Skills v2 把技能當成做事的手順，而不是另一條執行管線。系統提示只放名稱和一行說明；模型呼叫 `skill_view` 才展開全文，再用既有的程式執行、檔案或搜尋工具去做。來源可以是本機目錄、ModelScope repo 或 git。同名時，工作區蓋過使用者家目錄，再蓋過內建。Skill Evolution 用執行痕跡和分數改技能，候選版本要在驗證集上變好才換上。

權限是獨立模組。TUI 的 `--permission_mode` 列出 `auto`、`strict`、`restricted`、`interactive`，預設 `restricted`。程式把 `restricted` 當成 `interactive` 的別名；yaml 完全沒寫 permission 時，模式預設是 `auto`。黑名單不能被模式、白名單或使用者的「允許」蓋過。`curl`、`wget`、`ssh` 這類對外連線，註解寫成每種模式都要再確認，除非打開 `allow_network`。安全層另擋破壞性指令、敏感路徑，以及憑證檔的讀取。可寫範圍預設包含工作目錄和系統暫存目錄。這些規則在 TUI 或 WebUI 上怎麼問人，尚未實測。README 的 WebUI 示範提到改檔前會請人核准。

程式執行可以走 [ms-enclave](https://github.com/modelscope/ms-enclave)：Docker，或會保留 notebook 狀態的 `docker_notebook`。主機的 `output_dir` 掛進沙盒的 `/data`。也可以改在本機直跑。Skills 文件說技能本身不再另開子行程；沙盒是 `code_executor` 的事。任務開始前可開 `enable_snapshots`，用 git 留下工作區快照，預設關閉。

### 介面、排程，以及可搬走的員工檔案

`ms-agent run` 跑一次任務，`tui` 在終端機裡管理 session，`ui` 打開瀏覽器。WebUI 以專案為單位：開本機資料夾、多個 session、看串流與工具呼叫、瀏覽或編輯專案檔。它沒有內建登入，預設聽 `127.0.0.1`，文件寫的常見位址是 `http://127.0.0.1:8000`。這是瀏覽器裡的工作台，不是可走進的辦公室。

`ms-agent cron` 排重複或單次工作，並能看歷史輸出。Agent Hub（`ms-agent agent`）在本機與 ModelScope repo 之間上傳、下載、背景同步、轉換和備份。文件列出的框架名稱有 `qoder`、`qwenpaw`、`openclaw`、`hermes`、`nanobot`、`openhuman`、`ms-agent`。搬的是說明、skills 和記憶這類員工檔案，不是把那些 runtime 雇進同一間房間。ACP 與 A2A 讓它接上編輯器或其他 agent。文件站寫明，ModelScope 網站上的 [MCP Playground](https://modelscope.cn/mcp/playground) 後端就是這套框架。

## 主打賣點

- **它加上的是長任務的 harness。** 規劃、分層上下文、權限關卡、執行回饋、專案記憶和排程，是現在最想被記住的一組。領域工作流是這組能力的現成用法。
- **和 CrewAI 相同的是多角色與可寫死的流程。** CrewAI 的員工是 role、goal、backstory，流程是順序或階層。MS-Agent 的員工是一份 yaml 上的 LLMAgent 或 CodeAgent；開放題靠主責分派，固定題靠 chain 或 DAG。深度研究把交接物放成磁碟上的證據夾，而不只是上一棒的文字。
- **和 Deep Agents 相同的是 harness 語彙**：子 agent、上下文壓縮、skills、工具前的人為核准。Deep Agents 把虛擬檔案系統當預設辦公桌。MS-Agent 的辦公桌是本機專案目錄加上 `output_dir`，沙盒另掛 `/data`。
- WebUI、MCP Playground、Agent Hub 和幾條領域應用，是同一套 SDK 的介面與範例。PyPI v1.6.0 的舊 WebUI 不要當成現行工作台。

## 使用情境

### 開放題研究，主張要能對回證據

- 適合誰：要交一份可追溯研究報告的開發者。
- 在什麼情況使用：問題沒有固定步驟，Researcher 自己決定何時叫 Searcher 和 Reporter。
- 帶來的價值：計畫、證據和章節留在輸出目錄，人可以打開檔案核對，也可以用 cache 從中斷處續跑。

### 用排好的工位，從需求生出專案

- 適合誰：希望階段固定、中間產物可檢查的人。
- 在什麼情況使用：CodeGenesis 的七段或四段流程，或 FinResearch 這種同時開工再會合的 DAG。
- 帶來的價值：每一棒有自己的設定與產出；人知道現在停在哪一張桌子，而不是只看到一段很長的聊天。

### 人只在關卡出現，其餘交給排程

- 適合誰：想讓助手每天做監控或整理，又不要它默默改檔、連外網的人。
- 在什麼情況使用：`cron` 到點啟動；改檔或 `curl` 這類動作碰到權限規則時停下來問。
- 帶來的價值：班表與關卡分開。實際詢問畫面尚未實測。

## 我們可以學什麼

- 值得借鑑的 product idea：員工可以是「主責加上可召喚的專責」，也可以是 yaml 裡排好的工位。交接物應該是檔案（計畫、證據、章節），人打得開抽屜核對。權限記憶和專案記憶要分開：前者記得「這項工具上次准了沒有」，後者記得「這個專案做過什麼」。
- 值得借鑑的 interaction / workflow：順序流程是檔案從這一桌傳到下一桌。DAG 是兩張桌子同時做，再走到會合桌。Skills 是牆上的薄索引，拿下來才展開全文。Skill Evolution 是下班後用分數改手順，驗證集變好才換上。Cron 是牆上的鐘。Snapshot 是開工前的房間照片。對外連線即使其他權限放寬，仍要人再點一次。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。中間是主責桌，名牌寫現在的目標。Searcher 與 Reporter 是兩側固定工位；順序工作流是一排工位，文件沿桌子傳。FinResearch 那種 DAG 會同時點亮蒐集桌和搜尋桌，兩份文件再送到彙整桌。權限櫃臺擋在檔案櫃和終端機前面，人要蓋章才放行。Skills 是牆上的薄索引。Session 是桌下的完整對話抽屜，當下 context 是桌面，太滿就收成一張摘要。專案記憶是房間的檔案櫃，`MEMORY.md` 放在櫃上。證據夾放在研究桌旁，報告上的句子對得回某一夾。Cron 是牆上時鐘。沙盒是隔開的小隔間，成品從 `/data` 窗口遞出。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。走進去看得到誰在場：Researcher 站在中間的計畫桌，Searcher 在靠窗的位子查資料，Reporter 在另一張桌子寫。DAG 進行時，兩個人同時在自己的位子上，做完把資料夾送到彙整桌。人要核准時，員工停在終端機旁等你走過去。證據室可以進去，打開某一夾，核對報告裡的一句話。沙盒是玻璃小間，裡面跑程式，成品從窗口遞出來。Cron 員工到點會自己進門，即使你不在。Snapshot 是開工前拍下的房間，可以走回去看當時的檔案。重點是看見誰在場、誰在哪裡做事。
- 需要重新設計的地方：WebUI 是瀏覽器工作台（聊天、檔案樹、工具串流），不要把它畫成 2D 或 3D 辦公室。文件和程式還沒對齊：Workflow 頁只有 chain，FinResearch 已經用 DAG；壓縮器在設定文件裡仍是現行選項，在原始碼對照表裡標成 deprecated；TUI 預設 `restricted`，yaml 缺省卻是 `auto`。記憶至少有 session、壓縮、`unified_memory`、舊的 `default_memory` 和權限記憶，不能畫成同一個抽屜。Agent Hub 搬的是跨框架的設定檔，不是把 OpenClaw 或其他 runtime 的人放進同一間房間。權限對話、沙盒是否真的隔離、WebUI 核准是否走同一套 enforcer，都尚未實測，先不要做死在空間介面上。

## 初步看法

- 最有價值的部分：檔案化的交接（證據綁回主張），加上權限關卡和分層記憶。這三樣都能變成看得到的工位、櫃臺和抽屜。
- 最大限制或疑問：它仍是給開發者呼叫的框架。`main`、文件站和 PyPI v1.6.0 差一截。核准手感、記憶召回和沙盒邊界要跑過才知道。
- 是否值得進一步研究或親自體驗：值得，尤其是 DAG 工位、證據夾，以及人在權限櫃臺的介入。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://ms-agent-en.readthedocs.io （中文文件 https://ms-agent.readthedocs.io/zh-cn ）
- Repository：https://github.com/modelscope/ms-agent
- Documentation：https://ms-agent-en.readthedocs.io/en/latest/ ；本次對照的是 `main` 上的 `docs/en`（Config、LLMAgent、Workflow、AgentSkills、Tools、CLI、CodeGenesis）與 `webui/README.md`
- License：`main` 的 `LICENSE` 為 Apache License 2.0（Copyright 2023-2025 Alibaba ModelScope；附錄範例仍寫 Copyright 2020-2022）
- README（`main`）：harness、三層脈絡、chain／DAG、skills、Agent Hub、Agentic Insight v2、CodeGenesis、FinResearch；PyPI 現行發行為 1.6.0
- 已讀原始碼：`ms_agent/permission/config.py`、`ms_agent/memory/utils.py`、`ms_agent/memory/memory_manager.py`、`ms_agent/memory/unified`；FinResearch 的 `workflow.yaml`；Agentic Insight v2 README
- MCP Playground：https://modelscope.cn/mcp/playground
- 早期預印本：https://arxiv.org/abs/2309.00986 （2023；描述的是當時的 ModelScope-Agent，不是現行 harness）
