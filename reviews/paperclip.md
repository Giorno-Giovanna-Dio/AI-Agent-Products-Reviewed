# Paperclip

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-paperclip-cell-7aa3/reviews/paperclip.md)
>
> Cell ID：[https://github.com/paperclipai/paperclip](https://github.com/paperclipai/paperclip)
>
> Status：`untried`
>
> Category：Multi-agent org control plane（公司級編排）
>
> Last updated：2026-10-06

## 產品介紹

Paperclip 是開源的 **AI 公司控制平面**：以 Node.js 伺服器搭配 React
介面，讓你在同一套系統裡定義公司目標、組織架構、預算與治理規則，再把
各種外部 agent（Claude Code、Codex、Cursor、OpenClaw、Pi 等）當「員工」
接進來，由 **heartbeat** 喚醒執行並回報進度與成本。

它表面像任務管理器，底層則是 org chart、目標對齊、審批關卡、技能庫與
多公司隔離。官方用「若 OpenClaw 是員工，Paperclip 是公司」來區隔：**不
取代 agent runtime**，而是編排誰在什麼角色下做哪件工作、為什麼做、花多少
錢、何時需要人點頭。可自架（`npx paperclipai onboard`），也有 Paperclip
Cloud 候補名單；尚未實際安裝驗證。

## 主要 Features

### 公司、目標與任務階層

**Company** 是一級物件：有頂層 goal、員工、收支與任務樹。工作以 issue／
task 形式存在，並透過 parent 鏈一路連回公司目標，讓 agent 與人都能回答
「這件事為什麼要做」。任務可含留言、文件、附件、產出物、blocker 與審核
交接，而不是散在聊天視窗裡。

### Agent org chart 與 adapter

每位「員工」是 agent：有職稱、匯報關係、能力描述、預算與 **adapter**
（如何被呼叫、身份設定檔、執行環境）。內建與外掛 adapter 可接本機 CLI
session、shell、HTTP／webhook（例如 OpenClaw 風格遠端 bot）。Paperclip
不規定 prompt 格式，只要求「可被喚醒、可觀測、可授權」。

### Heartbeat 執行與工作區

**Heartbeat** 佇列負責喚醒：合併重複喚醒、檢查預算、解析 workspace、
注入 secrets、載入 skills，再呼叫 adapter。執行可綁專案 repo、git worktree
或沙箱延伸；run 留下 log、用量與 session 狀態，支援有限度的失敗恢復與
需人工介入的標記（細節以官方 spec 為準）。

### 治理、審批與成本

可設定 review／approval 階段、暫停／改派／終止 agent、board 層級決策。
**Budget** 可落在公司、agent、專案、目標等層級，依回報用量追蹤並在
threshold 警告或 hard stop。這把「多 agent 24/7 自主跑」和「老闆仍要管錢
與風險」接在一起。

### Skills、routine 與可攜性

**Skill Studio** 與公司級 skills（含 GitHub repo 匯入、版本與 eval 等
方向）讓營運能力可共享、可測；**Routines** 用 cron／webhook 等觸發週期
任務並自動開 issue、喚醒負責 agent。組織可匯出／匯入範本（secrets 會
scrub），方便複製「一家 AI 公司」的結構而非只複製程式碼。

### 多公司與部署模式

單一部署可跑多個 organization，彼此任務、agent、權限與活動紀錄隔離。
預設 **local_trusted** 本機免登入；**authenticated** 模式支援登入、
membership 與更適合對外暴露的綁定策略（尚未確認企業 RBAC 深度）。

## 主打賣點

- **管「公司」而不是管 PR**：定位是 autonomous company 的 command &
  control，不是 coding ADE 或 code review 工具；工程產出（worktree、
  preview、PR 連結）是 hook，不是核心敘事。
- **BYO agent、一張 org chart**：多 provider 並存，用匯報線與委派表達
  協作，而不是假設只有一種 CLI。
- **Goal-aware + 可審計的任務執行**：工作、對話、產出、成本、審批都掛在
  issue 上，適合「很多 terminal 同時跑但要看懂全公司在幹嘛」的場景。
- **控制平面 vs 執行平面**：agent 在哪跑由 adapter 決定；Paperclip 管
  編排、權限、預算與狀態——這和 Emdash／Maestro 等 **ADE dashboard**
  的「平行 coding task」是不同抽象層（可互補）。
- 部分能力（插件生態、ClipHub／範本市集、雲端託管）仍在 roadmap 或
  waitlist，不宜當成已全部落地的事實。

## 使用情境

### 用 AI 團隊追一個生意目標

- 適合誰：想讓 CEO／CTO／行銷等多角色 agent 分工、又需要對齊 KPI 的
  創辦人或 small team operator。
- 在什麼情況使用：目標是營收、成長、內容或營運，而不只是修一個 repo。
- 帶來的價值：任務樹與 goal 鏈讓自主 agent 較不易偏題；預算與審批降低
  無限 burn。

### 混用 OpenClaw、Claude Code、Cursor 等

- 適合誰：已有不同 agent 工具、不想被單一 vendor 鎖死的使用者。
- 在什麼情況使用：遠端 bot 負責對外、本機 coding agent 負責實作、HTTP
  adapter 接自訂腳本。
- 帶來的價值：統一 org、任務指派與 heartbeat，而不是各自一套 spreadsheet。

### 自架多「虛擬公司」實驗

- 適合誰：想在同一台機器上跑多個獨立 agent 組織的進階使用者。
- 在什麼情況使用：A/B 兩套策略、不同產品線、或教學 demo。
- 帶來的價值：多 company 邊界與可攜範本降低複製成本（匯出前仍須人工
  審查敏感路徑與 env，官方文件有提醒）。

## 我們可以學什麼

- **Company + org chart 是 workspace 的語意骨架**：角色、匯報、委派比
  「多開幾個 chat tab」更能表達 multi-agent 協作；任務應帶 goal ancestry。
- **Heartbeat 是編排節拍，不是 UI 動畫**：喚醒佇列、checkout lock、
  blocker 與 recovery issue 是把「很多自主 worker」變成可運維系統的關鍵。
- **Human oversight 用 board 抽象**：預算、審批、暫停／改派是預設路徑，
  預設畫面應回答「公司在做什麼、花多少、要等誰點頭」，而非先堆 transcript。
- **Adapter 邊界清楚**：我們的 workspace 應顯示 adapter 類型、run 狀態、
  workspace 解析結果，而不是假設所有 agent 都是同一種 terminal。
- **在 2D workspace 裡會變成什麼**：俯視「公司大樓」平面圖——頂層是
  goal 與預算儀表；每一層是 org 單位；格子是 issue（顏色表示 blocked／
  待審／執行中）；heartbeat 像脈衝沿匯報線流動；審批關卡是電梯口的閘機；
  多 company 是並排兩棟建築，切換即換地圖。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室——agent 依 org chart
  坐在不同區域（executive 層、工程區、營運區），頭上或 desk 螢幕顯示
  當前 issue 與 goal 摘要；人走向某員工介入或批准；預算快用完時該區
  燈光變紅；routine 觸發像夜班同事定時進辦公室開工；這是 **spatial
  observability of a company**，不是 3D 知識圖或模型 viewer。
- **不值得照搬或需重設計**：完整 Jira／GitHub 替代品不是目標；企業級
  RBAC 不應搶在「5 分鐘內 CEO 完成第一件任務」之前；把 Paperclip 整包
  ADE 側欄搬進我們 workspace 會混淆「公司編排」與「coding task 平行跑」
  兩種模型——可借 governance／goal 鏈，UI 仍應服務 spatial office 隱喻。

## 初步看法

- 最有價值的部分：把 multi-agent 從「工具集合」提升為 **有目標、有組織、
  有成本與審批的公司隱喻**，且 execution 與 control 分離的 adapter 模型。
- 最大限制或疑問：產品面廣（skills、插件、gateway、EE 政策），自架複雜度
  與 cloud 路線尚未親自驗證；和 coding-first ADE 的交界（worktree、PR）
  需 hands-on 才能評 UX 是否夠輕。
- 是否值得進一步研究或親自體驗：值得，尤其 org／heartbeat／budget 如何
  呈現在我們設想的 2D／3D office 裡，以及與既有 Cell（Agent Office、
  Emdash、Paseo）的分工。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：[https://paperclip.ing](https://paperclip.ing)
- Repository：[https://github.com/paperclipai/paperclip](https://github.com/paperclipai/paperclip)
- Documentation：[https://docs.paperclip.ing](https://docs.paperclip.ing)
- Product definition（repo `doc/PRODUCT.md`）：經 GitHub API 閱讀摘要
- Goal / architecture（repo `doc/GOAL.md`）：經 GitHub API 閱讀摘要
