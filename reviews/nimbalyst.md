# Nimbalyst

> Cell ID：[https://github.com/nimbalyst/nimbalyst](https://github.com/nimbalyst/nimbalyst)
>
> Status：`untried`
>
> Category：Visual multi-agent coding workspace
>
> Last updated：2026-10-05

## 產品介紹

Nimbalyst 是開源的桌面工作空間，讓人與 Claude Code、Codex、OpenCode 等
coding agents 在同一套介面裡並行工作。它不只管理 chat session，還把
markdown、mockup、圖表、任務與程式碼都當成「可視覺編輯的檔案」，人和
agent 改的是同一份磁碟上的 plain files，再透過 git worktree 隔離平行
session。

使用者主要在 Electron 桌面 app 裡建立文件、開 Agent Manager、在 kanban
上追 session，並用紅綠 diff 逐步接受或拒絕 agent 建議。另有 iOS
companion，可在路上回覆 agent 問題、滑動審 diff、排下一個任務。

## 主要 Features

### 視覺化共同編輯

內建 WYSIWYG 編輯器（Markdown、mockup、Mermaid、Excalidraw、CSV、資料模型、
Monaco 程式碼等）。重點是 agent 產出先變成「看得懂的文件」，人可以直接
改內容，而不是只在 terminal 裡讀 raw patch。

### 文件級紅綠 diff 審閱

在渲染後的文件上逐步檢視 agent 提議的變更，逐段 accept 或 reject。這把
review 從純 code diff 延伸到規格、圖表與說明文件。

### 平行 session 與 worktree

多個 coding agent 可同時跑，各自綁在獨立 git worktree，降低互相踩檔的
風險。內建 branch／worktree 管理、staging、AI 起草 commit 與 embedded
terminal。

### Session kanban 與檔案連結

用看板追蹤每個 session 的狀態，可搜尋、恢復，並把 session 與它碰過的
檔案雙向連結，方便從「這次 agent 在做什麼」跳回相關 artifact。

### 任務追蹤（人與 agent 共用）

計畫、bug、feature、todo 等 tracker 存成 repo 內可讀寫的結構，agent
可新增、移動、執行任務，人也可在同一介面調整，形成共享的 work queue。

### 異質 agent 並存

同一 workspace 可並用 Codex、Claude Code，以及 alpha 階段的 OpenCode、
GitHub Copilot 等，方便比較不同 runtime 的行為而非只換 model。

### MCP 與擴充編輯器

作為 MCP client 連外部工具，並把 tool 結果渲染成視覺 widget。Extension SDK
透過統一的 `EditorHost` 合約，自訂檔案類型編輯器可與內建編輯器同等對待。

### 行動端 human-in-the-loop

iOS app 顯示哪些 agent 需要人、支援文字或語音回覆、滑動審 diff、排程
下一任務，並在 agent 等待時推播通知。

## 主打賣點

- **文件優先的可視 workspace**：差異不在「又多一個 agent chat」，而是
  把 agent 工作變成可編輯、可審、可連結的視覺 artifact，且資料留在 git
  repo，沒有專有雲端文件庫要遷出。
- **平行 agent + 隔離執行**：與 Conductor 等產品同屬 multi-agent
  orchestration 賽道，但 Nimbalyst 更強調 kanban、任務 tracker 與多種
  編輯器在同一畫面的深度連結。
- **開源 MIT + 跨平台桌面**：macOS／Windows／Linux 桌面版開源；協作 sync
  server（`wss://sync.nimbalyst.com`）為獨立專案，本 repo 只定義 wire
  protocol。
- **較像新包裝的部分**：worktree 平行 session、多 provider、內建 git——
  這些能力在 Conductor、Cursor 等也已出現；Nimbalyst 的辨識度主要在
  Lexical 系視覺編輯與文件級 diff 流程。

## 使用情境

### 規格與原型與程式碼同一專案推進

- 適合誰：希望 PRD、wireframe、架構圖和實作 code 都在同一 repo、同一
  UI 裡迭代的人。
- 在什麼情況使用：feature 從 markdown 規格寫到 mockup，再交 agent 實作。
- 帶來的價值：減少「規格在 Notion、code 在 IDE」的上下文斷裂。

### 多 agent 並行且需人工把關

- 適合誰：同時跑好幾個 session，但不想讓 agent 無審核合併大量變更。
- 在什麼情況使用：平行 worktree 開發不同分支，人在 kanban 上追進度並
  用紅綠 diff 分批接受。
- 帶來的價值：吞吐提高，但 review 粒度仍可控。

### 離開桌面的短暫介入

- 適合誰：agent 常阻塞在「需要你回答一題」的長任務上。
- 在什麼情況使用：人在外用手機回覆、批准 diff、排下一個 todo。
- 帶來的價值：降低 agent 空轉，維持 pipeline 連續性。

## 我們可以學什麼

- **Agents 與 tasks 的組織**：session 是 kanban 上的一張卡；tracker 是
  agent 與人共用的 backlog；檔案透過連結掛在 session 上，形成「任務 →
  session → artifact」三層，而不是只有 chat thread ID。
- **Context 與 handoff**：plain files + git 讓 handoff 變成「打開同一份
  markdown／diagram」，mobile 回覆再寫回 sync 協議（personal JWT vs team
  JWT 分工在 upstream 文件裡寫得很細，代表 multi-surface sync 是核心
  工程風險）。我們設計 workspace 時要預留「桌面深度編輯 + 行動輕量
  審批」兩種 context 入口。
- **Runtime 與 sandbox**：agent 跑在外部 CLI（Claude Code、Codex 等），
  Nimbalyst 管 worktree 隔離與 UI；權限與 API key 明確要求「只讀使用者
  在 app 設定裡填的 key」，避免環境變數 silently 計費——這類 guardrail
  值得在自家 workspace 照搬。
- **進度與 observability**：kanban 狀態、session 搜尋、檔案分組，加上
  MCP 結果的 widget 化，比 raw JSON log 更接近「主管看板」。
- **Human 委派與驗證**：紅綠 diff 是細粒度驗證；iOS 推播是「agent 請
  你來」的 interrupt 模型；兩者合起來是 async delegation，不是全程盯
  terminal。
- **在 2D workspace 裡會變成什麼**：一層樓的平面辦公室——左側是 session
  kanban 泳道，中央是「目前開啟的文件桌」（markdown／圖表 tile），每張
  kanban 卡可拖向某張桌子並帶出連結檔案；agent 用小 avatar 標在卡上，
  「需要人」的卡閃爍或排到「接待櫃台」格。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室——每個 worktree session
  是一間小隔間或工位，牆上貼著該 session 的 mockup 與 Mermaid；你走到
  隔間門口看到 agent 狀態燈，進門在實體化的「文件板」上點 accept／reject
  diff；手機推播對應「門口有人敲門等你簽核」。重點是**誰在哪個工位、
  哪份 artifact 在牆上**，不是 3D 模型展示。
- **不值得照搬或需重新設計**：Electron 巨石 monorepo + 自有 collab
  sync 基礎設施對我們未必是首選；extension marketplace 與大量編輯器類型
  可先縮到 2D／3D workspace 真正需要的 artifact（ spec、task、code
  diff）；若我們做 pixel 2D，kanban＋文件桌的資訊架構比複製全部
  WYSIWYG 種類更優先。

## 初步看法

- 最有價值的部分：把 multi-agent orchestration 和「人可讀、可改、可審」
  的文件 surface 綁在一起，且堅持 git-native 儲存。
- 最大限制或疑問：與 Conductor 高度重疊的 parallel session 敘事下，長期
  差異能否撐住；collab sync 與 team 功能依賴外部 worker，自架成本與
  行為尚未在本 Cell 實測確認。
- 是否值得進一步研究或親自體驗：值得。若我們要設計 2D／3D agent
  office，Nimbalyst 的 kanban、檔案連結、文件級 diff 是可直接對照的
  interaction primitive。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：https://nimbalyst.com/
- Repository：https://github.com/nimbalyst/nimbalyst
- Documentation：https://docs.nimbalyst.com/
