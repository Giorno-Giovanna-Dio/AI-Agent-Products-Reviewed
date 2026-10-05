# cmux

> Cell ID：[https://github.com/manaflow-ai/cmux](https://github.com/manaflow-ai/cmux)
>
> Status：`untried`
>
> Category：原生終端機 workspace（AI coding agent 多工）
>
> Last updated：2026-10-05

## 產品介紹

cmux 是 macOS 上的原生終端機應用，以 Ghostty（libghostty）為渲染核心，針對
「同時跑很多 coding agent session」這種用法加強組織與可程式化能力。它不像
Conductor 這類 ADE 控制台去包一層 agent runtime workflow，而是把 terminal、
內建瀏覽器、workspace／分頁／分割，以及「agent 需要你」的通知，做成可組合的
底層 primitive。

典型用法是：每個 repo 或任務開一個 workspace，裡面用垂直分頁與分割 pane 並行
跑 Claude Code、Codex 等 CLI agent；sidebar 顯示 git branch、連結的 PR、工作目錄、
監聽 port 與最新通知文字；agent 透過終端序列或 `cmux notify` 觸發 attention 時，
對應 pane 會出現藍色 ring、分頁高亮，並可從通知面板一鍵跳轉。

## 主要 Features

### Workspace 與垂直分頁 sidebar

每個 workspace 可對應一個專案或遠端機器，sidebar 的垂直分頁集中顯示 branch、
PR 狀態、cwd、port 與通知摘要。價值在於平行 session 變多時，仍能用結構化 metadata
分辨「哪個 agent 在哪裡、卡在哪」，而不只靠視窗標題。

### Agent 通知（ring、面板、CLI）

支援 OSC 9／99／777 等終端通知序列，並提供 `cmux notify` 供 agent hook 呼叫。
需要人工介入時 pane 與 tab 會視覺標記，另有集中通知面板與快捷鍵跳到最新未讀。
這直接回應「macOS 原生通知沒有 context、分頁一多就找不到人」的痛點。

### 內建瀏覽器與 agent-browser 風格 API

可在 terminal 旁分割出瀏覽器 pane，並透過移植自 agent-browser 的可腳本 API
做 accessibility snapshot、點擊、填表與執行 JS。價值是 agent 能在同一視窗裡
對本機 dev server 做互動驗證，而不必另開外部瀏覽器再切換。

### CLI 與 socket 程式化

可透過 CLI／socket 建立 workspace、分割 pane、送 keystroke、開 URL 等。這讓
外部腳本或其他 orchestrator 把 cmux 當「顯示與輸入 surface」，而不是被產品內建
workflow 綁死。

### SSH workspace 與 Claude Code Teams

`cmux ssh` 可為遠端建立 workspace，瀏覽器 pane 可走遠端網路；`cmux claude-teams`
則把 Claude Code 隊友模式映射成原生分割與 sidebar metadata。價值是把多 agent
協作留在終端機語意裡，減少對 tmux 或 Electron orchestrator 的依賴。

### Ghostty 相容與原生效能

讀取既有 Ghostty 設定（主題、字型、色彩），Swift／AppKit 實作而非 Electron。
對長時間多 pane 工作較在意記憶體與啟動速度的使用者較友善。

## 主打賣點

- **Primitive，不是 prescriptive orchestrator**：官方明確定位為可組合的 terminal
  + browser + 通知 + CLI，不強迫單一 agent 使用方式；與「GUI orchestrator 鎖 workflow」
  形成對比。
- **Parallel agent 的可視化 attention**：sidebar metadata + ring／通知面板，解決
  「很多 session 但不知道誰在等你」；比泛用 macOS 通知更貼 terminal agent 情境。
- **Terminal-first 多工**：垂直分頁、分割、快捷鍵密度高，適合已習慣 CLI agent
  的開發者，而不是把 chat 當主介面。
- **部分能力只是整合既有生態**：Ghostty 渲染、agent-browser API、Claude Code Teams
  等是站在成熟元件上；cmux 的差異主要在 macOS 原生 shell 與 notification／sidebar
  產品化。

## 使用情境

### 本機多 repo 平行 coding agent

- 適合誰：同時在多個 branch／issue 上跑 Claude Code 或類似 CLI agent 的開發者。
- 在什麼情況使用：每個 repo 一個 workspace，每個 agent 一個 pane 或 surface。
- 帶來的價值：用 sidebar 與通知快速切換「需要輸入」的 session，減少在 Ghostty
  分頁間迷失。

### Agent 需要看 dev server 或登入網頁

- 適合誰：workflow 包含 E2E 或手動驗證 UI 的 team。
- 在什麼情況使用：terminal agent 與內建 browser pane 並排，必要時匯入其他瀏覽器
  cookies 以保持登入狀態。
- 帶來的價值：縮短 terminal 與瀏覽器之間的 context switch，API 可被 agent 直接驅動。

### 遠端或 Teams 模式的多 agent

- 適合誰：在 SSH 機器或 Claude Code teammate 上協作的人。
- 在什麼情況使用：遠端 workspace 或 `cmux claude-teams` 一鍵起多 pane。
- 帶來的價值：多 agent 仍用同一套 sidebar／通知模型，不必另建 tmux 儀表板。

## 我們可以學什麼

- **值得借鑑的 product idea**：把「workspace」定義成**可腳本控制的 terminal +
  browser 場域**，agent runtime 仍用使用者選的工具；產品只負責 spatial layout、
  attention 與 metadata，不搶 orchestration 的決策權。
- **值得借鑑的 interaction / workflow**：通知不是 toast 而已，而是綁在 pane／tab
  上的**可導航 attention 狀態**（ring、未讀佇列、跳轉快捷鍵）；sidebar 列的是
  **任務證據**（branch、PR、port、最後一則 agent 訊息），而不只是 chat 標題。
- **在 2D workspace 裡會變成什麼**：俯視或 pixel 辦公室裡，每個 workspace 是一
  間「工位區」，垂直分頁像同一區的多張桌面；藍 ring 表示某工位 agent 舉手；
  通知面板像總機 board；browser pane 可畫成工位旁的「預覽窗」。CLI／socket 對應
  從外部自動開房、派 agent、聚焦某工位。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室中，每個 workspace 是一組
  相鄰 desk＋螢幕牆；agent 需要人時該 desk 亮 ring 或頭頂 indicator；走過去才
  看到 terminal 與 browser 細節。SSH workspace 像遠端分館的房間，Teams 模式像
  同一房間多張 desk 同時開工。
- **不值得照搬或需要重新設計的地方**：cmux 綁 macOS／Ghostty，我們的 2D／3D
  workspace 若跨平台需抽象「pane surface」與通知通道；它不提供 Git worktree 隔離
  或 PR review 控制台（那是 Conductor 類產品層）；安全邊界仍是本機使用者權限，
  不能因為 UI 好看就當 sandbox。

## 初步看法

- 最有價值的部分：在**不取代 agent runtime** 的前提下，用 notification + sidebar
  metadata 把 parallel terminal agent 變得可管理；「Zen of cmux」的 composable
  primitive 哲學對 workspace 平台設計有參考性。
- 最大限制或疑問：僅 macOS；與 Conductor 等 ADE 的 branch 隔離、多 runtime 比較
  不在同一層；git watcher 等背景行為在 managed Mac 上可能需關閉（文件提及
  `CMUX_NO_GIT_WATCH` 權衡）。尚未親自安裝驗證。
- 是否值得進一步研究或親自體驗：值得，特別是 attention UX、可程式化 workspace
  API，以及如何與非 macOS 的 2D／3D workspace 對齊同一套 primitive。

## 後續補充（選填）

Not tried yet（本環境非 macOS，未執行 DMG／Homebrew 安裝）。

## Sources

- Official website：https://cmux.com
- Repository：https://github.com/manaflow-ai/cmux
- Documentation：https://cmux.com/docs/getting-started
- Blog（Zen of cmux）：https://cmux.com/blog/zen-of-cmux
- Related：Conductor Cell（[`reviews/conductor.md`](conductor.md)）— ADE 控制台 vs
  terminal primitive 的對照參考
