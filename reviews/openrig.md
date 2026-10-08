# OpenRig

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openrig-cell-7aa3/reviews/openrig.md)
> - [Pages 閱讀器](https://giorno-giovanna-dio.github.io/AI-Agent-Products-Reviewed/#/reviews/openrig.md)（`main` 部署後）
>
> Cell ID：[https://github.com/mvschwarz/openrig](https://github.com/mvschwarz/openrig)
>
> Status：`untried`
>
> Category：Multi-agent harness（tmux + 本機 daemon）
>
> Last updated：2026-10-06

## 產品介紹

OpenRig 是開源的 **multi-agent harness**：它不取代 Claude Code、Codex 或 Pi
這類 coding agent，而是把多個 harness 收成「有編制、有地址、可恢復」的
團隊。你用 YAML（RigSpec）描述 rig、pod、seat 與成員關係，再用
`rig up` 一次啟動 tmux session、注入 startup 檔與就緒檢查；之後透過 CLI、
TUI 或 MCP 看 topology、派工、傳訊，而不是在終端機裡手動記哪個視窗是誰。

定位介於「純 CLI 腳本編排」（例如 Sandcastle 的 sandbox/worktree API）與
「桌面 ADE 看板」（Maestro、Emdash）之間：核心狀態在本機 **daemon + SQLite**，
每個 agent 仍是可 attach 的真實 terminal session。官方 slogan 是 harness
包 model、rig 包 harnesses——重點是 **持久團隊**（角色、共用脈絡、各自負責的
工作），而不是單次 AFK run。

典型流程：在 repo 目錄選預設 team（如 `starter` 的 builder + reviewer），
確認 provider 與模型就緒後 `rig up`；透過 kernel 對話空間或 `rig send`
`dev-build@starter` 下達有邊界的任務，請 builder 把項目記進 queue、完成後
叫同 rig 的 reviewer 檢查 **同一候選變更**；人類讀 artifact 與 review 再
派下一項。內建 `workshop`、`factory` 等較大編制，並可 `rig grow` /
`rig shrink` 在運行中調整 topology。

## 主要 Features

### RigSpec 與 Seat 地址

RigSpec 以 YAML 宣告 rig：pod 分組、成員、邊（協作關係）、continuity 政策
與 `culture_file`。**Seat** 是穩定角色地址（例如 `dev-build@starter`）：
佔用 seat 的對話可以換，但身份、guidance 與「誰擁有哪些脈絡」的契約不變。
AgentSpec 則是可重用的 agent 藍本（skills、hooks、profiles、startup 合約）。

### 本機 daemon、tmux 與多 harness runtime

架構為 CLI / TUI / MCP → Hono HTTP daemon → domain services → SQLite +
tmux + runtime adapter。每個 seat 跑在 tmux 裡，人類可直接 attach。
Managed runtime 包含 Claude Code、Codex，以及透過 RPC runner 的 Pi / Oh My Pi；
另有 terminal node 類型。這讓 **同一 rig 內混用不同 provider** 成為一級能力
（例如 starter 預設 builder 用 Claude、reviewer 用 Codex）。

### 啟停、快照與 discover/adopt

`rig up` / `rig down` 管理整個 rig 生命週期；`rig down --snapshot` 保存
topology，`rig up <name>` 從快照還原並回報各 node 是 resumed、fresh-primed
或需人工決定。`rig discover` 指紋現有 tmux 裡的 Claude/Codex session，
`rig adopt` 納入管理，降低「已經開了一堆 terminal 再重來」的摩擦。

### 跨 agent 通訊與協調面

`rig send`、`rig broadcast`、`rig chatroom` 在 seat 之間傳遞工作與討論；
`rig seat set-typing-guard` 可在人手動打字時暫停自動訊息，避免 agent 把
內容打進正在編輯的 seat。Rig 層的 **Culture**（`CULTURE.md`）疊在 OpenRig
預設文化之上，定義團隊協調規範；pod 則提供 **共享 guidance 與 context**，
但每個 agent 仍保留自己的 context window。

### Queue 與 owned work

文件中的 first-run 路徑要求 builder **在 queue 裡記錄任務 ID**，並把 review
綁在「exact candidate」上，而不是泛泛地說「做完了」。CLI 提供
`rig queue list` 等查詢（依 destination seat 過濾）。這把 multi-agent 流程
從「聊天接力」拉成 **可追蹤的工作所有權**：誰在實作、誰在審、候選變更指哪一
份，尚未實測 queue 語意與 MCP 工具細節。

### TUI、terminal workspace 與 MCP

TUI 以表格與 **topology graph** 呈現 rig / pod / seat 狀態，並可深入 seat
runtime、model、context 用量等；另含 Specs、Projects、Feed、System 等視圖。
Herdr 或 cmux 可把同一 rig 的多個 seat terminal 開在同一 workspace（例如
`rig terminal open starter --provider herdr`），TUI 負責 **協調狀態**，
terminal provider 負責 **實際 PTY**。MCP 暴露 `rig_up`、`rig_ps`、`rig_send`、
`rig_chatroom_send` 等，讓 agent 能操作自己的 topology。

### RigBundle 與可攜 team

RigBundle 是帶 SHA-256 完整性、內嵌 AgentSpec 的封存，可 `rig bundle install`
或從 GitHub 資料夾連結安裝，在機器之間分享同一套編制；`workshop` 等即為
bundle 形式發佈的 team 範本。

### 權限、hooks 與觀測（高觸碰本機）

Managed launch 會寫入 provider trust、hooks、workspace 內 `.openrig/` 等；
activity relay 把事件（不含 prompt 全文）送到 daemon `/api/activity/hooks`。
Per-seat / per-rig **permission policy**（floor、full_bypass、inherit 等）與
原生 Claude/Codex 模式分開管理；預設並非 YOLO。這是 harness 層對 **sandbox
與審計** 的取捨：方便編排，但使用者需閱讀「what OpenRig changes on your
machine」並自行備份設定檔。

## 主打賣點

- **Seat 與 pod 當一等公民**：角色地址、分組共用脈絡、YAML 可版本控管，
  比「多開幾個 tab」更接近 **編制管理**。
- **混用 Claude Code / Codex / Pi 的同一套 rig**：不是單一 vendor ADE，而是
  本機 daemon 統一 lifecycle 與通訊。
- **tmux 真實性 + 快照恢復**：保留 terminal 可介入性，又避免 reboot 後
  terminal sprawl；discover/adopt 銜接既有 session。
- **Agent 可操作自己**：MCP + queue/culture/send 讓 lead 協調 specialist，
  人類只在 decision / review 邊界介入。

相對於 Maestro 類桌面 ADE，OpenRig 較像 **基礎設施型 harness**（daemon、
SQLite、tmux、hooks），UI 以 TUI 與外部 terminal workspace 為主，React web UI
僅 maintenance。相對於 Sandcastle，它更強調 **長期團隊與人機協調**，而非
單次 sandbox run 的 API。

## 使用情境

### 單 repo 內「建 + 審」閉環

- 適合誰：已在用 Claude Code 或 Codex、想加獨立 reviewer seat 的開發者。
- 在什麼情況使用：一個 bounded change，需要 explicit review 與 queue 紀錄。
- 帶來的價值：builder 與 reviewer 分 harness，候選變更與 review 對齊同一
  artifact，減少「自己審自己」。

### 持續產品開發的多角色 factory

- 適合誰：願意維護較大 RigSpec、接受本機 daemon 與 tmux 依賴的 power user。
- 在什麼情況使用：lead / advisor / build / QA / design / 雙 reviewer 等
  長期編制。
- 帶來的價值：topology 可視化、廣播與 chatroom 支援跨 pod 協調，快照還原
  降低長跑專案的中斷成本。

### 把散落的 terminal session 收成 managed rig

- 適合誰：已經手動開多個 Claude/Codex tmux、需要統一 send/ps/權限政策的人。
- 在什麼情況使用：discover → adopt → 補上 RigSpec 與 culture。
- 帶來的價值：不強制重開 session，但之後派工與狀態查詢有單一入口。

## 我們可以學什麼

- 值得借鑑的 product idea：**Stable seat address**（`role@rig`）作為委派、
  queue destination 與 MCP 工具的共用主鍵；pod 共享 culture/guidance，seat
  保留個別 context 與 permission 覆寫。
- 值得借鑑的 interaction / workflow：TUI graph + table 雙視角看 topology；
  typing guard 保護人手 seat；send 訊息時要求 agent 自行 **claim work**（queue
  ID）並把 review 綁定 exact candidate；kernel operator 引導選 team 而非從
  空白 YAML 開始。
- 在 2D workspace 裡會變成什麼：俯視 **office floor**，pod 是房間或區域，
  seat 是固定工位（標示 runtime、model、queue 深度、是否 awaiting auth）；
  Feed 與 queue 是牆上的任務板；點工位 attach 到 tmux 或開 herdr 分屏。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，不同 pod 是不同隔間，
  avatar 代表 seat 狀態（typing、blocked on permission、in review handoff）；
  lead 在「kernel 室」發派，人類在需要決策的 seat 門口收到通知。
- 不值得照搬或需要重新設計的地方：對本機設定檔與 provider hooks 的高觸碰
  寫入需在我們產品裡做更清晰的 preview/rollback；Linux/Node 版本與 Windows
  支援邊界；若目標是低門檻 workspace，daemon + tmux 門檻高於純 Electron ADE。

## 初步看法

- 最有價值的部分：把 multi-harness team 建模成 **可宣告、可快照、可發現**
  的 rig，並用 seat 地址統一派工、queue 與 review handoff。
- 最大限制或疑問：本機副作用與權限模型複雜；尚未確認 queue/MCP 在大型
  factory 下的效能與衝突解決；Pi seat 與 Claude/Codex 混用時的 context
  同步細節需 hands-on。
- 是否值得進一步研究或親自體驗：值得。與本 repo 的 Sandcastle（程式化 sandbox）、
  Pi（minimal harness）、Maestro（桌面 ADE）形成互補三角，特別適合驗證
  **2D/3D office 裡的 seat 編制與 owned work** 該長什麼樣子。

## Sources

- Official website：https://openrig.dev/
- Repository：https://github.com/mvschwarz/openrig
- Documentation：https://github.com/mvschwarz/openrig/tree/main/docs/reference
