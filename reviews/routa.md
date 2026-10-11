# Routa

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-routa-cell-c2d2/reviews/routa.md)
>
> Cell ID：[https://github.com/phodal/routa](https://github.com/phodal/routa)
>
> Status：`untried`
>
> Category：軟體交付的多 agent 協調台
>
> Last updated：2026-10-09

## 產品介紹

Routa 是 Phodal（Fengda Huang）維護的開源協調層，用來把軟體交付從「一條很長的聊天」拆成看得到的工作物件。一個 workspace 下面掛著 codebase、worktree、session、任務、筆記、看板與專員。人先選 workspace、接上一個 agent provider、掛上 repo，再從 Session、Kanban 或 Team 三種入口開工。Web 是 Next.js，桌面是 Tauri 殼加 Rust／Axum，兩邊用同一份 `api-contract.yaml` 對齊詞彙；另外有 `routa-cli`。授權是 MIT。Repo 建立於 2026-02-16，文件站在 [phodal.github.io/routa](https://phodal.github.io/routa/)。GitHub Releases 上的桌面包裝最新 tag 是 v0.19.0（2026-06-22）；主線在那之後仍有推送。這次沒有安裝或執行。

它比較像交付控制台，加上一組會被叫去做事的角色，而不是一間已經蓋好的辦公室。Workspace 是共用的工作範圍；Spec note、看板卡片與 trace 是交接物；ROUTA、CRAFTER、GATE 與各泳道專員是員工契約。人仍然在網頁或桌面裡看列表、看板與執行紀錄。

## 主要 Features

### Workspace 先於全域聊天

Session、任務、筆記、看板、codebase、worktree、記憶與排程都掛在 workspace 上。MCP 工具也要先解析目前的 workspace，避免「git status 到底是哪個 repo」這種含糊。桌面偏本機：SQLite、本機 agent 程式、本機 worktree 與 trace 檔。Web 可以用 Postgres 或 SQLite。這讓多個專案不會混在同一個隱藏的全域清單裡。

### 三種開工方式，編排起點不同

官方設計筆記寫明這三種都是一等入口，差別在編排從哪裡開始：

- **Session**：先開一條可恢復的執行線。預設角色是協調者 ROUTA，需要時才拉出 CRAFTER（實作）與 GATE（驗證）。適合探索、除錯與第一次跑通。
- **Kanban**：從卡片與泳道開始。預設欄位是 backlog、todo、dev、review、done、blocked。卡片移進有開啟自動化的欄，會排隊開出新的 ACP session。同一塊板有並行上限（文件寫預設 1），並會丟掉已經移走或已經有 session 的過期項目。
- **Team**：從一位 lead 開始。Lead 用真正的子 session 分波派工，Team 畫面看得到這些子線。Lead 的提示要求重疊的檔案範圍不要同時改，也要求這一波不要一次鋪太多人。

### Spec note：先寫規格，再派工

內建 ROUTA 協調者的契約是：自己不改檔，先用 `set_note_content` 寫 Spec note。規格裡用 `@@@task` 區塊描述目標、範圍、完成定義與驗證指令；提示宣稱這個工具會把區塊轉成任務。接著把計畫交給人，等人批准才把任務派給 CRAFTER，做完再派 GATE。Team 的 lead 是另一套契約：需求已經清楚時直接派工，不等計畫批准。兩套「人何時該插手」同時存在。伺服器有沒有真的擋住「沒批准就派工」，尚未確認；目前看得到的是角色提示，不是像看板關卡那樣寫成共用政策。

### 看板是協調匯流排

看板不是只把任務狀態畫出來。泳道移動可以觸發專員 session。Backlog 專員要把粗需求收成一張卡片上的 canonical YAML 故事；後面的泳道被要求重讀、拒絕含糊的故事，再補上執行摘要、實作證據或審查結論。Review 與 Done 預設走 GATE。進 review／done 的交付條件（有 commit、worktree 乾淨，done 還要分支達到可開 PR）寫在欄位政策裡，REST 與 MCP 的移卡走同一套判斷，不靠提示自己記得。GitHub Issues 同步被定位成疊加層，看板資料本身仍是本機為主。

### Provider 收成 ACP，執行環境另有沙箱

Claude Code、OpenCode 等 runtime 先轉成 ACP session 更新，再寫入持久化、trace 與畫面串流。模型選擇放在 provider 層。Rust 端另有 Docker sandbox：可限制網路、環境變數、唯讀／可寫掛載，以及連出去的 worktree。架構同時保留本機 agent 行程與 Docker worker。日常 Session 是否預設進容器，尚未確認。

### 進度、證據與審查

Trace 記下 session、訊息、工具呼叫、檔案變更與 git 脈絡。Harness Monitor 用「知道規則、實際跑了什麼、觀察到什麼、能不能往下送」來解釋一次 run。Review 有發現項與嚴重度。卡片隨泳道累積故事、證據與結論，所以交接物在卡片上變厚，而不是只留在某一段聊天裡。

筆記在 TypeScript 側有協作更新。架構把「交付用的 workspace memory」說成一類產品資料，但這次讀到的功能清單裡，`/api/memory` 是行程記憶體監控（並標成相容舊路徑），沒有看到獨立的 workspace memory 產品路由。人和 agent 之間目前能對上的共用記錄，主要是 note、卡片工件、trace 與子 session。記憶產品是否已經能當辦公室的共用大腦，尚未確認。

另外，workspace 裡有一個 Spec 頁（`/workspace/:workspaceId/spec`），功能清單把它寫成「本地 docs／issues 的關係板」。這和協調者維護的 Spec note 不是同一個表面。畫面上會不會讓人以為是同一份規格，尚未確認。

## 主打賣點

Routa 想被記住的是：工作從 workspace 與明確階段開始，目標、任務、session、證據與審查留在板上，而不是埋進一條聊天。泳道專員一份比一份嚴，review／done 的放行條件由伺服器執行。

和已有 Cell 對照，這點成立的範圍要收斂：

- [Conductor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/conductor.md) 與 [Maestro](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/maestro.md) 已經是多 runtime 的桌面控制台，也用 worktree 做平行工作。Routa 同樣把 provider 收進一層轉接。多出來的是「移動卡片會排隊開新 session」，以及 review／done 的交付政策跨 UI 與 MCP。Maestro 的 Playbook 是文件清單驅動長跑；Routa 的重複流程刻在泳道與欄位政策上。
- [Nimbalyst](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/nimbalyst.md) 也有看板，重點是 session 狀態與可編輯文件。Routa 的看板會因為換欄而叫專員，卡片本體要長出 YAML 故事與證據。
- [Paperclip](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-paperclip-cell-7aa3/reviews/paperclip.md) 管的是公司目標、組織、預算與喚醒。Routa 的 Team 是一次交付裡的 lead 與專長分工，範圍停在這個 workspace 的軟體工作，沒有看到公司級預算與 org chart。
- [Sandcastle](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/sandcastle.md) 是沒有看板的 sandbox／worktree 程式庫。Routa 自己有 Docker sandbox 政策與產品畫面；它沒有把每次執行都收成 Sandcastle 那種腳本 API。預設是否隔離，尚未確認。
- [Agent Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/agent-office.md) 用可走進的樓層回答「誰在哪張桌子」。Routa 今天沒有這層空間。Repo 裡名為 office 的套件是 docx／pptx／xlsx 文件讀取，和辦公室無關；它在主流程裡的分量尚未確認。

多 provider、MCP、worktree、trace、看板 UI，這些是這類控制台常見的包裝。Routa 真正值得單獨記的是：卡片當匯流排、規格 note 先於派工、關卡由伺服器擋，以及 Session／Kanban／Team 三種編排起點分開。

## 使用情境

### 一個需求要走完規格、實作、驗證

- 適合誰：希望 agent 先交出可讀計畫、人點頭後才改 code 的開發者。
- 在什麼情況使用：開 Session，讓 ROUTA 寫 Spec note 與 `@@@task`，批准後再分波交給 CRAFTER 與 GATE。
- 帶來的價值：目標、驗收與驗證指令留在 note 上，後續的人與專員讀同一份規格，而不是翻很長的聊天。

### 多張卡片沿同一條交付線前進

- 適合誰：想用固定階段推進多個故事，並讓 review 真的擋住髒 worktree 的人。
- 在什麼情況使用：把需求放上 Kanban，讓 backlog 到 done 的專員依序改寫卡片，人只處理被退回或卡在 blocked 的項目。
- 帶來的價值：狀態、證據與「為什麼不能進 done」寫在卡片上；同一塊板的排隊避免多張卡片同時衝進自動化泳道。

### 一次改動跨前後端或數個子系統

- 適合誰：協調本身比寫碼更花時間的人。
- 在什麼情況使用：用 Team，由 lead 派出 researcher、frontend、backend、qa 等子 session，並錯開會改到同一批檔案的工作。
- 帶來的價值：子 session 出現在 Team 畫面裡，人可以看誰在做哪一塊、何時該插話，而不是只有一條父聊天。

## 我們可以學什麼

- 值得借鑑的 product idea：workspace 是樓層的邊界；卡片是會移動的工作；Spec note 是這張工作的契約；專員是有崗位的人；trace 與證據是桌上的紙。Session、Kanban、Team 是三種不同的開工方式，應該在空間裡分成不同區域，而不是三種按鈕打開同一種聊天。
- 值得借鑑的 interaction / workflow：下游不直接相信上游的自述，卡片每過一站就多一層工件。人批准規格、伺服器批准進 review／done，這兩種「點頭」要分成兩種動作。Blocked 要寫原因與該退回哪一站。過期的自動執行要取消，避免人已經把卡片移走、系統還在跑。
- 在 2D workspace 裡會變成什麼：一個 workspace 是一層平面樓層，牆上掛著這層的 codebase 與 worktree 門牌。樓層分成三區。Session 區是一張主桌，協調者坐著只改規格紙，Crafter 與 Gate 在需要時才出現在側桌。Kanban 區是六條輸送帶（backlog、todo、dev、review、done、blocked）；卡片走到有自動化的格子，那一格就出現專員，同一塊板預設排隊、一次處理有限張數。每過一格，卡片上多釘一張紙：故事 YAML、執行摘要、實作證據、審查結論或完成紀錄。Review 與 Done 是閘口，缺 commit、worktree 不乾淨，或 done 的分支還沒準備好開 PR，卡片就停在閘口，原因寫在卡片歷史上。Team 區是 lead 的主位加上這一波才擺出來的幾張子桌，檔案範圍重疊的人不坐進會同時改同一處的桌子。Spec note 釘在協調者旁邊，等人點頭才放人去改 code。另一面牆的 Spec 關係板若保留，要標成「本地 issue 關係」，避免和規格紙看成同一張。
- 在 3D workspace 裡會變成什麼：同一層可走進的辦公室。人從 workspace 大廳進來，牆上是 repo 與 worktree。Session 是一間可恢復的辦公室：協調者站著拿規格，實作者坐側桌改碼，驗證者稍後進門只對驗收條件。Kanban 是車間，人走在泳道之間，看到哪張卡片停在哪一站、哪一站有人、隊列有多長；Blocked 是旁邊小間，牆上寫卡關類型與退回路線。Team 是開放區：lead 在前方，只有這一波的專長員工有椅子，做完離席，下一波才坐下；人走到某張桌子可以讀子 session、傳話，或把會踩到同一批檔的人隔開。若沙箱有開，它是一間玻璃房，門上寫著網路、環境變數與哪些目錄唯讀。人的工作是走到規格桌批准、走到閘口看證據，或把卡片送回上一站。進度是誰在哪一站、卡片上多了什麼紙，而不是側欄裡的聊天串流。
- 不值得照搬或需要重新設計的地方：今天的 Next.js／Tauri 畫面仍是控制台。把側欄、看板與 trace 頁直接叫成 2D 或 3D workspace，會錯過「人走去某一站」這件事。Session 協調者等人批准、Team lead 對清楚需求直接派工，這兩種規矩若不明示，人會不知道現在該不該插手。提示裡的「不信任上一站」要有伺服器政策與卡片工件才算數。雙後端語意對齊成本高，不必為了學這個產品先複製一整套 Next.js 加 Rust。Docker sandbox 已有政策模型，但不能把它寫成每一次 agent 都在隔離環境裡。文件讀取套件也不要做成辦公室的視覺。

## 初步看法

- 最有價值的部分：看板換欄會排隊叫專員，review／done 的放行是伺服器政策；規格 note 與 `@@@task` 把「先對齊再改 code」做成協調者的預設動作。
- 最大限制或疑問：尚未執行，三種入口的手感、批准是提示還是硬閘、預設是否進 sandbox、Spec 頁和 Spec note 是否容易混淆，都尚未確認。共用記憶目前不能當成已經做好的產品能力。
- 是否值得進一步研究或親自體驗：值得。它適合拿來對照「交付站點如何放進平面樓層與可走進的辦公室」，而不是拿來當空間辦公室的成品。

## 後續補充（選填）

Not tried yet.

## Sources

- Repository：[https://github.com/phodal/routa](https://github.com/phodal/routa)
- Docs site：[https://phodal.github.io/routa/](https://phodal.github.io/routa/)
- Architecture：[docs/ARCHITECTURE.md](https://github.com/phodal/routa/blob/main/docs/ARCHITECTURE.md)
- Execution modes：[docs/design-docs/execution-modes.md](https://github.com/phodal/routa/blob/main/docs/design-docs/execution-modes.md)
- ADR 0003 workspace、0004 kanban automation、0007 delivery policies：[docs/adr/](https://github.com/phodal/routa/tree/main/docs/adr)
- Specialist contracts：`resources/specialists/core/routa.yaml`、`resources/specialists/workflows/kanban/`、`resources/specialists/team/agent-lead.yaml`（經 GitHub API 閱讀，未執行）
- Feature inventory：[docs/product-specs/FEATURE_TREE.md](https://github.com/phodal/routa/blob/main/docs/product-specs/FEATURE_TREE.md)
- Sandbox：`crates/routa-core/src/sandbox/`（經 GitHub API 閱讀介面，未跑容器）
- License：MIT（repo `LICENSE`）
- Desktop release tag：v0.19.0，發布於 2026-06-22（GitHub Releases API）
