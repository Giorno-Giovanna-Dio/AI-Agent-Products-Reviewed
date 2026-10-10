# Kun

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-kun-cell-ad04/reviews/kun.md)
>
> Cell ID：[https://github.com/KunAgent/Kun](https://github.com/KunAgent/Kun)
>
> Status：`untried`
>
> Category：本地優先 GUI／TUI agent 工作臺（Code、Design、Work、Rooms）
>
> Last updated：2026-10-10

## 產品介紹

Kun 是開源的本地優先 AI agent 工作臺，主語言是 TypeScript，桌面程式是 Electron。它要讓 agent 在真實專案裡把一個目標做成可檢查的交付：讀工作區、訂計畫、呼叫工具、改檔案、跑驗證，並把證據留在任務旁邊。工作分成幾塊。Code 做軟體交付，同一則任務可以切到 Design 畫布。Work 做寫作、文件、試算表和簡報。Rooms 把多個 agent 放進同一個協作空間，用私聊和群聊討論、分工、執行與評審。

使用者從 GitHub Releases 安裝桌面版，或用 Node.js 22.19+ 從原始碼跑開發環境。裝好桌面版後，在終端機執行 `kun` 進入 TUI。桌面適合看過程、審閱和控制；TUI 適合用鍵盤連續工作。本次只讀 repo 的 README、LICENSE 與文件，沒有安裝。官網是 [kun-agent.com](https://www.kun-agent.com)，首頁幾乎只有「GUI + TUI 共用本地 runtime」和 Google 帳號說明；產品細節以 repo 為準。

## 主要 Features

### 同一份資料，兩種介面

README 把桌面 GUI 和 TUI 寫成共用一個本地 `kun serve`：thread、目標、計畫、核准和背景任務要連續，而不是兩套互不相干的對話。TUI 文件把所有權寫得更細。預設情況下，GUI 或 TUI 各自持有自己啟動的 runtime；同一份資料目錄同時只能有一個 owner。Owner 離開後 runtime 停止，下一個用戶端仍透過 Service Manager 讀到同一批 thread、設定、記憶和模型連線。要即時掛上已經在跑的 runtime，使用 `kun tui --no-start`。兩邊都走本機 HTTP/SSE。

對話、偏好、日誌和 runtime 資料預設留在本機。文件寫的預設資料目錄是 `~/.kun/data`；若舊資料仍在 `~/.deepseekgui/kun`，啟動時不會另開一套。`kun serve` 文件預設聽 `127.0.0.1:18899`，TUI 則會自己選埠。選了雲端模型之後，提示、附件和任務上下文會送到該 provider。工具權限、敏感操作和擴充權限要在介面上授權。

### Code：從目標走到可審查的改動

Code 把專案、分支、任務輸入、檔案、終端機、Git／worktree、Diff、測試和審查放在同一張工作臺。用法是先給目標和約束，agent 補範圍、風險和驗收標準，再按計畫改檔、呼叫工具、跑驗證。計畫、Todo、工具呼叫、檔案改動、瀏覽器或終端結果、核准，都掛在這則任務上。需求變化後可以繼續、分叉、歸檔或重做計畫。需求和計畫預設可以進專案，方便進版本控制。

TUI 把這些收成命令：`/goal` 管理持久目標，`/plan` 切換規劃方式，`/tasks` 把計畫 Todo、目標、子 agent、背景 shell 和擴充工作放在一起看。`/permission` 選這條 thread 的核准策略和 sandbox，並同步到其他用戶端。文件列出的 sandbox 有 `read-only`、`workspace-write`、`danger-full-access`、`external-sandbox`。`/fork` 從完整歷史分出一條，來源對話不被改寫。`/btw` 開一條繼承快照的旁支，不改主 thread。額外資料夾會擴大這條 thread 的檔案與 sandbox 範圍；Git、審查和預設 shell 仍跟著主專案。

### Design：長在同一則 Code 任務上的畫布

Design 是 Code 工作臺裡的一種任務，不是另一個程式。人描述介面，agent 寫一份自包含 HTML 到 `.kun-design/`，中間畫布即時預覽，每一輪存一個新版本。核准後「Implement in code」會發布共用的 `DESIGN_SYSTEM.md`，再開一條新的 code thread 去實作，並記下這份設計對應哪一次實作。反方向可以把現有 UI 做成可再改的原型。設計更新晚於實作，或設計系統的雜湊對不上，側欄會標出漂移。另有一張節點畫布，把 prompt、HTML、圖片依序跑完。這張畫布產出的是平面原型和設計系統文件，不是可走進的辦公室。

### Work，以及掛到 Code 上的唯讀知識

Work 用工作區檔案樹和 assistant 起草 Markdown、摘要或詢問文件、分析試算表、從大綱做簡報，也可以用白板整理想法。PDF 與 Office 可以預覽和引用；Office 檔維持唯讀。

Code 的一條 thread 可以把 Work 工作區掛成知識庫，最多八個不重疊的根目錄，而且不能把主工作目錄再掛一次。索引是結構圖：Markdown 標題、PDF 頁、投影片、工作表範圍，放在 Kun 自己的資料目錄，不寫進 Work 工作區。它不用嵌入向量。Agent 只能用三個唯讀工具依目錄往下讀，讀到的文字標成不可信證據。掛載不會變成可寫 sandbox，也不能靠 `@` 提到知識庫就取得檔案寫入權。

### Rooms：提案等人採納，交付釘在一個 commit

Rooms 和 Code、Work 並列。建一個房間，為成員選 agent 檔案、模型和授權的本地 Git 儲存庫。預設角色有 Coordinator、Developer、Reviewer，也可以用既有的 agent 檔案。三種協作：同儕討論由成員自己決定要不要發言；協調者模式由 Coordinator 派工；定向協作則點名成員或預設回應者。

Agent 可以起草提案卡，例如固定一條約定、請求執行、加入成員。卡片本身不執行任何事，只有人能採納或略過。人明確釘選的內容才成為專案約定；約定依版本凍結進任務快照，之後改規則不會自動改寫已經開工的任務。

每個執行任務使用獨立 Git worktree，起點是所選分支已提交的 HEAD，工作區裡還沒提交的變更不會被帶進去，也不會退回直接改來源目錄。開發者的產出先釘成一個不可變 commit，Reviewer 在另一份唯讀 checkout 上看這一版。驗證必須事先宣告檢查，並對上實際的工具呼叫和命令結果；一句「成功了」不算驗收。接受交付，和把程式碼套進目標分支，是兩個動作。直接套用要目標分支乾淨且能 fast-forward；對不上就另開整合 worktree，衝突和檢查留在房間裡。關掉房間面板或切換模式不會停掉執行。Rooms 文件寫明這一版不含多位真人、雲端執行、跨裝置同步、持續自己找目標、以及遠端發 PR。房間回合要用原生 API 模型；訂閱 SDK 引擎不支援 Rooms。這些是文件界線，尚未實測。

### 記憶和迴圈

記憶分成兩種權威。`reference` 是參考證據，包含使用者匯入、工具、網頁和推論出來的文字，不能當成模型指令。`directive` 是人核准的常駐規則，每一回合都會注入，但不能蓋過 Kun 的政策、sandbox、工具權限、核准要求，或最新的明示指令。匯入和自動整理只會寫成 reference。Agent 自己的記憶和 Code 的一般記憶分開存放；預設留在來源範圍，要人明示才分享給別的對話或專案。

Create Loop 把重複工作寫成一次宣告：每一輪做什麼、上一輪輸出怎麼餵下一輪、什麼條件算做完，再加上回合上限。Hooks、MCP、Skills、排程任務和可安裝擴充，是掛在這套 runtime 上的自動化。README 另寫記憶架構參考了 Nowledge Mem 公開文件裡 thread 與 memory 分開、來源可追、混合檢索的想法；Kun 的實作仍是自己的。

## 主打賣點

- **它想被記住的是一條從目標到證據的本地工作，以及人還握著幾道門。** Code 負責做，Design 長在同一則任務上，Work 負責文件，Rooms 負責多個 agent。提案、約定、接受交付、套進分支，都要人點頭。證據（Diff、宣告過的檢查、評審所綁的 commit）留在任務旁。
- **GUI 和 TUI 是同一份工作的兩扇窗，預設卻不是一顆永遠開著的背景服務。** 資料面共用；即時 runtime 要掛上去才共用。這和「打開終端就一定連上桌面那一個 agent」是不同的操作。
- **和本 repo 裡已看過的產品相比：** OpenCode 是程式工作的 TUI 加上本地伺服器。CrewAI 是同一次 Python 執行裡的角色小隊。Paperclip 是把外部 agent 僱成員工的組織控制平面。Kun 是自己的桌面工作臺：一個 runtime 上同時有程式、設計畫布、文件和房間，房間裡的執行還用獨立 worktree 和不可變交付。
- 多種模型 provider、MCP、Skills、排程和擴充，是 agent 工作臺常見的周邊。主幹仍是任務、證據，以及人核准的那幾道門。

## 使用情境

### 在一個專案裡把小目標做成可審查的改動

- 適合誰：想讓 agent 改本地程式，又要自己看 Diff 和測試的人。
- 在什麼情況使用：目標範圍有限、做完可以驗證。桌面上看過程，或在 TUI 下指令；需要時用 `/goal`、`/plan`、`/permission`。
- 帶來的價值：計畫、工具呼叫和改動掛在同一則任務上，可以分叉或歸檔，而不是只剩一段聊天紀錄。

### 先在畫布上定介面，再交給程式 thread

- 適合誰：希望設計和實作留在同一個工作臺的人。
- 在什麼情況使用：先在 Design 任務裡改 HTML 原型，核准後發布 `DESIGN_SYSTEM.md`，再開一條 code thread 實作。
- 帶來的價值：原型版本、設計系統和「這份設計有沒有被改過」留在專案目錄旁，實作對得上當時核准的那一版。

### 讓幾個 agent 在房間裡討論，人決定能不能落地

- 適合誰：想同時看到討論、實作和評審，又不想讓 agent 直接改主分支的人。
- 在什麼情況使用：房間裡指定成員和儲存庫。Agent 起草提案，人採納後才在獨立 worktree 執行；Reviewer 看釘住的 commit，人再決定接受和套用。
- 帶來的價值：約定、檢查、Diff 和評審都留在房間。套用失敗或分支已前進時，改走整合，而不是默默蓋掉來源目錄。

## 我們可以學什麼

- 值得借鑑的 product idea：一個員工可以同時有程式桌、設計板和文件桌，但設計板是這張程式桌上的一層，不是另一家公司。目標是會持久存在的物件。證據跟任務走。規則分成「參考筆記」和「人釘過的約定」。多個 agent 的協作單位是房間，房間有角色、授權的儲存庫，以及人才能按下去的提案卡。
- 值得借鑑的 interaction / workflow：釐清目標、形成計畫、執行、檢查證據、交付或分叉。旁支提問不改主線。Rooms 裡，起草和執行分開；接受交付和套進分支分開；評審綁定一個不可變 commit，舊評審不自動覆蓋新版本。關掉視窗不等於停下工作。GUI 與 TUI 要顯示「現在誰持有 runtime」，掛上之後才看到同一串即時核准。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。Code 是一張程式桌，Diff、終端和 Todo 攤在桌面上，持久目標釘在桌角。Design 是同一張桌上的畫板，原型一版一版疊著，`DESIGN_SYSTEM.md` 是畫板和程式桌之間的交接紙；紙上的雜湊對不上時，桌面出現漂移標記。Work 是旁邊的文件桌。知識庫是唯讀書架，不能拿去當可寫抽屜。Rooms 是一張會議桌，名牌寫 Coordinator、Developer、Reviewer。提案卡放在桌中央，只有人的印章能讓它變成工作。執行發生在會議桌旁的獨立工作凳（git worktree），評審看的是釘住的那一版，套進主分支要再走一步。reference 記憶是抽屜裡的筆記，directive 和專案約定是牆上釘住的規則。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走到程式桌看誰在改哪一個 worktree，走到文件區看 Work，設計板立在程式桌旁邊，人可以站在板前翻原型版本，再把核准過的設計系統交給另一張新開的程式桌。房間是一間可走進的會議室：看得到誰在發言、誰在獨立工作凳上做事、誰拿著一份凍結的 commit 在評審。提案卡浮在會議桌上，你走過去採納或略過。接受交付是在房間裡點收；套進主分支是走到主儲存庫那一側。GUI 和 TUI 是看同一位員工的兩個介面。若兩邊各自持有 runtime，辦公室裡要顯示成兩次出勤、兩次執行，資料櫃仍是同一份。重點是看見誰在場、誰在哪裡做事。
- 需要重新設計的地方：Kun 自己的介面是桌面 GUI 和終端 TUI，屬於 ADE 工作臺。三欄、畫布和房間時間軸不能直接叫成 3D workspace，也不能把 Design 的 HTML 原型或節點圖當成可走進的辦公室。Rooms 文件寫的界線（沒有多位真人、沒有雲端執行、沒有跨裝置同步、沒有遠端 PR）先不要做進空間介面。README 把兩邊寫成共用一個 `kun serve`，TUI 文件則寫預設各自持有 runtime。辦公室裡要分成資料櫃和當班 runtime，否則人會以為終端和桌面一定同時連著同一輪執行。sandbox 四種模式和核准策略的實際手感尚未實測，先不要做成已驗證的門禁。

## 初步看法

- 最有價值的部分：任務旁的證據、Design 與 Code 的交接、Rooms 裡「人採納才執行、評審綁 commit、接受和套用分開」。
- 最大限制或疑問：它是完整的桌面工作臺，不是我們要做的空間辦公室。GUI 與 TUI 的 runtime 所有權比 README 口號細，不跑一次不知道預設體驗是哪一種。Rooms 的協作品質、核准手感，以及官網帳號是否為使用前提，都還沒看到。
- 是否值得進一步研究或親自體驗：值得，尤其是提案卡、不可變交付和「誰持有 runtime」如何出現在 2D／3D 辦公室。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://www.kun-agent.com
- Repository：https://github.com/KunAgent/Kun （GitHub API 的 `full_name` 為 `KunAgent/Kun`，預設分支 `master`，語言 TypeScript，homepage 即官網）
- README（`master`）：Code、Design、Work、Rooms、本地資料、PolyForm 授權
- License：`LICENSE` 為 PolyForm Noncommercial License 1.0.0（Required Notice：Copyright (c) 2026 xingyu）。非商業使用；商業使用、商業散佈、SaaS／託管、轉售或整合進商業產品需另取得書面授權
- Documentation：
  - https://github.com/KunAgent/Kun/blob/master/docs/kun-tui.en.md
  - https://github.com/KunAgent/Kun/blob/master/docs/rooms.md
  - https://github.com/KunAgent/Kun/blob/master/docs/DESIGN_MODE.md
  - https://github.com/KunAgent/Kun/blob/master/docs/knowledge-bases.md
  - https://github.com/KunAgent/Kun/blob/master/docs/memory-foundation.en.md
  - https://github.com/KunAgent/Kun/blob/master/docs/workflow-loop.en.md
  - https://github.com/KunAgent/Kun/blob/master/docs/independent-agents.md
  - https://github.com/KunAgent/Kun/blob/master/kun/README.zh-CN.md （runtime：核准策略、sandbox、預設埠與資料目錄）
