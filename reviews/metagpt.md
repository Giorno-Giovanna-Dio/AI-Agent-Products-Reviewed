# MetaGPT

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-metagpt-cell-9381/reviews/metagpt.md)
>
> Cell ID：[https://github.com/FoundationAgents/MetaGPT](https://github.com/FoundationAgents/MetaGPT)
>
> Status：`untried`
>
> Category：Python multi-agent framework（軟體公司 SOP）
>
> Last updated：2026-10-10

## 產品介紹

MetaGPT 是開源的 Python 多代理人框架，用來把多個 LLM 編成一家軟體公司。使用者給一行需求，公司依角色交出產品需求、系統設計、任務和程式，預設寫進 `workspace`。它服務的是想用一句話開工的人，以及想自己組角色、自己接 SOP 的開發者。文件在 [docs.deepwisdom.ai](https://docs.deepwisdom.ai/main/en/)。本次只讀 README、LICENSE、教學與 source，沒有安裝、也沒有跑 CLI。

官方把核心講成 `Code = SOP(Team)`：軟體公司的標準作業程序變成角色之間的交接，而不是一個聊天室輪流說話。倉庫已從 `geekan/MetaGPT` 轉到 FoundationAgents（舊網址 HTTP 301）。2025 年他們另推商業產品 [MGX](https://mgx.dev/)。MGX 和這份開源程式的對應範圍，尚未確認。

## 主要 Features

### 有名字的角色，和預設實際在班的人

每個角色有 name、profile、goal、constraints。經典名單是產品經理 Alice、架構師 Bob、專案經理 Eve、工程師、QA Edward。這次讀到的 `main`（`11cdf466`，2026-01-21）在 `generate_repo` 裡實際雇用的是隊長 Mike、產品經理、架構師、Engineer2（Alex）、資料分析 David。專案經理、舊的 Engineer、QaEngineer 在這段雇用清單裡被註解掉，沒有進預設團隊。

### 文件交接：PRD、設計、任務、程式

固定 SOP 打開時（`use_fixed_sop`），角色用 `_watch` 訂閱上一棒的動作。使用者需求進來，產品經理準備文件並寫 PRD，放在 `docs/prd`。架構師盯的是「寫 PRD」這個動作，交出系統設計，放在 `docs/system_design`。專案經理盯「寫設計」，拆任務到 `docs/task`。舊的工程師盯任務清單，寫程式，並可做 code review、程式摘要。QA 盯摘要，再寫測試、執行、除錯。訊息帶三個路由欄位：`cause_by`（哪一個動作產生這封信）、`sent_from`、`send_to`。source 註解寫明，檔案本體改走專案目錄裡的路徑，不再整份塞進訊息。`RoleZero.use_fixed_sop` 預設是 `False`，所以這條「看上一種信封才開工」的鏈，預設沒有打開。

### 環境是信箱，預設先經過隊長

`Team` 預設 `use_mgx=True`，環境是 `MGXEnv`。一般訊息會先加上隊長為收件人。隊長再用 `publish_team_message` 點名同事，`send_to` 填的是對方的名字，並被要求把路徑、語言、框架、限制整段抄進信裡，因為同事只看這封信。公開聊天會再標 `<all>`。人若指定收件人，可以跟某一角色直接聊，那一輪不經過隊長分派。環境負責把信依地址放進各角色的私人緩衝；角色再從緩衝挑出 `cause_by` 屬於自己訂閱的動作、或收件人是自己名字的信。

### 輪次、預算、存檔

CLI 用 `--investment` 當這家公司的預算上限（預設 3.0），花費超過就停。`--n-round` 限制模擬輪數（預設 5），全員閒置也會停。團隊可序列化，之後用 `--recover-path` 接回。`--inc` 與 `--project-path` 用來在既有 repo 上加需求。這些是 CLI 與 source 的描述，尚未實測。

### 口頭四步，和空著的專案經理桌

隊長指令仍把軟體開發寫成四步：產品經理寫 PRD、架構師寫系統設計、專案經理排程、工程師寫程式。小需求可以跳過文件，直接交給工程師。資料類工作整包給 David，不拆開。預設雇用名單沒有專案經理。口頭 SOP 和實際座位不一致。執行時隊長會不會把工作派給一個沒被雇用的角色，尚未確認。

教學頁仍列出 `--no-implement`。現行 `generate_repo` 一律雇用 Engineer2，過去依這個旗標決定要不要雇工程師的分支已被註解。這個旗標現在還會不會改變行為，尚未確認。

同一套框架另有研究員、辯論、狼人、Minecraft、Stanford Town 等劇本，以及 Data Interpreter 這條單人分析流程。那是可替換的場景，不是這次軟體公司主線。

## 主打賣點

- **它要被記住的是一家會交文件的軟體公司。** 角色、SOP、以及 PRD／設計／任務／程式這串產物，是 MetaGPT 和「一個會寫 code 的聊天窗」分開的地方。
- **和 Paperclip 同是公司隱喻，管的層不一樣。** Paperclip 是組織控制平面：把 Claude Code、Codex 這類外部 agent 雇成員工，用 heartbeat、目標鏈、預算和審批看管他們，自己不取代那些 runtime。MetaGPT 的 runtime 就是這些角色，他們活在同一次 Python 執行裡。交接物是文件夾，信箱是環境。預算在兩邊都有，一邊卡的是外雇員工的花費，一邊卡的是這家 LLM 公司自己的 API 花費。
- **和「角色接力」相近，多出來的是文件種類被寫進 SOP。** 順序上仍是上一棒的產出交給下一棒。MetaGPT 把上一棒收成指定文件，並用「這封信是哪個動作寫的」決定誰該拆信。預設路徑則改由隊長用自然語言點名。
- MGX 產品頁、AFlow 等論文是研究與商業周邊。開源核心仍是角色、環境、訊息和文件。

## 使用情境

### 用一行需求開一家小軟體公司

- 適合誰：想從一句話得到一組文件和一份程式的人。
- 在什麼情況使用：`metagpt "Create a 2048 game"`，或在 Python 裡呼叫 `generate_repo`。
- 帶來的價值：需求、PRD、設計、程式留在同一個專案目錄，下一個人看到的是文件，不只是終端機 log。產出品質尚未實測。

### 軟體線和資料線分開派工

- 適合誰：同一個需求裡既要做網站、又要抓資料或做分析的人。
- 在什麼情況使用：隊長依指令把資料工作整包給資料分析，把軟體開發拆給產品、架構、工程。
- 帶來的價值：兩種工作有不同的桌子和工具，不會擠在同一段 prompt 裡。實際會不會守住這條分界，尚未確認。

### 中斷後從存檔把公司叫回來

- 適合誰：跑到一半預算或輪數用完、想接續的進階使用者。
- 在什麼情況使用：團隊序列化之後，用 `--recover-path` 從 `team` 存檔恢復，再跑下一輪。
- 帶來的價值：公司狀態留在檔案裡，而不是只存在一次 process 的記憶。恢復是否完整，尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：公司是一組有名字、有目標的座位。工作是一份會長大的文件夾：需求、PRD、系統設計、任務、程式。信只負責把下一棒叫起來。
- 值得借鑑的 interaction / workflow：環境是辦公室信箱。每封信有寄件人、收件人，以及「這封信是哪一個動作寫的」。固定 SOP 是下一桌只拆上一種信封。預設的隊長模式是信先到隊長桌，隊長抄一份完整說明再點名，路徑和限制要留在信上，因為收件人沒有別的來源。人可以直接走到某一桌私聊，那一輪不經隊長。預算用完，整間辦公室停工。文件放在專案抽屜，信裡帶路徑。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室，是做事的地方。Alice 的產品桌、Bob 的架構桌、Alex 的工程桌、David 的分析桌、Mike 的隊長桌各有固定工位，名牌寫 goal。需求紙從門口放到隊長桌。PRD 文件夾從產品桌移到架構桌，系統設計再送到工程桌；資料工作的紙只送到分析桌。專案經理 Eve 的桌子畫在平面圖上，預設編制那張椅子是空的。QA Edward 的桌子同樣在，預設沒人坐。人要直接問某人時，走到那張桌子，信就留在那一桌。牆上的預算錶歸零，全員停手。文件在桌與桌之間移動，人站在這個樓層裡看誰有文件、誰在等信。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。進門先看到隊長 Mike 在分信。Alice 在產品桌寫 PRD，Bob 在架構桌翻那份文件，Alex 在工程桌寫程式，David 的分析桌在另一側。空著的專案經理椅讓人看見：SOP 寫了這一步，今天沒有人上班。人可以走到某一張桌子直接說話。桌上是可以拿起來的文件夾，裡面是 PRD 或設計。重點是看見誰在場、誰在哪裡做事。
- 需要重新設計的地方：空間介面要呈現「誰坐在哪、桌上是哪一份文件、信要不要經過隊長」，不必把動作類別名做成選單。固定 SOP 預設是關的，預設路徑是隊長點名；兩套同時畫成每一桌都必經 PRD、設計、任務，會和現行雇用名單衝突。空椅要留著，用來表示 SOP 有這一步、這次沒雇人。MGX 的畫面尚未確認，不要把它當成這個 repo 的行為。

## 初步看法

- 最有價值的部分：角色座位、會移動的文件，以及環境只負責把信送到該拆信的人。
- 最大限制或疑問：預設編制和隊長口頭上的四步不一致；固定 SOP 預設關閉；`--no-implement` 和現行雇用名單對不齊。沒有跑過，文件品質和隊長會不會漏抄路徑都尚未確認。`setup.py` 寫 1.0.0，文件站仍把 v0.8 標成 stable，PyPI 與這份 `main` 是否同一套，尚未確認。
- 是否值得進一步研究或親自體驗：值得，尤其是文件在桌子之間移動、空椅、以及隊長轉信在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://mgx.dev/ （2025 年推出的商業產品；本次未使用）
- Repository：https://github.com/FoundationAgents/MetaGPT
- 舊網址：https://github.com/geekan/MetaGPT （2026-10-10 確認 HTTP 301 轉到 FoundationAgents）
- Documentation：https://docs.deepwisdom.ai/main/en/
- License：repo `LICENSE` 為 MIT（Copyright (c) 2024 Chenglin Wu）
- 本次閱讀的 `main`：`11cdf466d042aece04fc6cfd13b28e1a70341b1f`（2026-01-21）。README、`docs/tutorial/usage.md`、`software_company.py`、`team.py`、`environment/base_env.py`、`environment/mgx/mgx_env.py`、角色與 `const.py` 的文件路徑
- Paper：Hong et al., MetaGPT, ICLR 2024，https://openreview.net/forum?id=VtmBAGCN7o
