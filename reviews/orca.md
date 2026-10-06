# Orca

> Cell ID：[https://github.com/stablyai/orca](https://github.com/stablyai/orca)
>
> Status：`untried`
>
> Category：Multi-agent coding workspace（ADE）
>
> Last updated：2026-10-05

## 產品介紹

Orca 是 Stably（Lovecast Inc.）開源的 **Agent Development Environment（ADE）**，
定位為「管理一整隊平行 coding agents 的編排器」。使用者用自己的 Claude、Codex
等訂閱，在桌面端（macOS、Windows、Linux）啟動任意 **終端機 CLI agent**；每個
agent 通常跑在獨立的 **git worktree** 裡，Orca 在同一個應用中追蹤對話、終端、
diff 與合併流程。

它不只是 chat 視窗：內建 VS Code 風格編輯器、Ghostty 級終端分屏、Chromium
瀏覽器與 Design Mode、GitHub／Linear 整合，以及可從手機監控與追問的 companion
app。遠端則可透過 **SSH worktree** 把 agent 放到較強的機器上，仍在本機 UI 操作。

## 主要 Features

### 平行 Worktree 與多 Agent Runtime

同一個 prompt 可 fan-out 到多個 agent（文件常以五路為例），各自在隔離的
worktree 中實作，再並排比較 diff 與執行結果、合併勝出方案。支援 Claude Code、
Codex、OpenCode、Pi 等大量 CLI agent，重點是 **runtime 可替換、訂閱自帶**，
不是綁單一 vendor。

### 桌面 IDE + 終端一體

Orca 把編輯器、無限分屏終端、scrollback 持久化與 agent session 綁在同一套
workspace。對習慣「看 terminal 才算真在寫 code」的使用者，比純 chat IDE 更接近
實際工作流。

### 行動 Companion

iOS／Android companion 可在 agent 完成或需要 attention 時推播，並從手機送
follow-up。這把 human-in-the-loop 延伸到離開桌面的情境，而不是只能坐在主機前
等 thread 結束。

### 內建 Review 與議題整合

可在 app 內瀏覽 GitHub PR、issue 與 Linear 看板，從任務一鍵開 worktree；並支援
在 AI 產生的 diff 上逐行註解，再把註解餵回 agent 修改，減少在瀏覽器與 IDE 之間
來回切換。

### Design Mode 與檔案拖曳進 Prompt

在內建 Chromium 中點選 UI 元素，可把 HTML、CSS 與裁切截圖直接注入 agent prompt；
編輯器也支援拖檔案或圖片進對話。這縮短「看到畫面 → 描述給 agent」的距離。

### SSH 遠端 Worktree 與 Orca CLI

SSH 模式在遠端機器跑 agent，本機仍享有完整編輯、git 與終端體驗（含自動重連、
port forwarding）。`orca` CLI 則讓 agent 也能腳本化建立 worktree、snapshot、
click／fill 等，形成 **人 ↔ Orca UI ↔ agent ↔ Orca CLI** 的可程式化迴圈。

## 主打賣點

- **開源 MIT + 跨平台桌面**，相較僅 macOS 或閉源 ADE，部署與社群延伸空間更大。
- **平行 worktree 是核心物件**，不是附屬功能：比較、合併、多 runtime 都圍繞
  「多份隔離 checkout」組織。
- **自帶訂閱跑任意 CLI agent**，Orca 賣的是 shell 與編排，不是 model API。
- 與 [Conductor](conductor.md) 同屬 ADE／多 agent 控制台，但 Orca 更偏完整
  IDE（終端、瀏覽器、行動端、遠端 SSH、開源）；Conductor 在本 repo 已標為
  `tried`，Orca 尚未實測。
- 部分能力（worktree、parallel agents、best-of-N 類比較）與 Cursor 等產品重疊；
  Orca 的差異在 **多 runtime 中立** 與 **開源可 fork** 的整合深度，而非單一
  editor 內建 agent。

## 使用情境

### 同一需求多路試作（Best-of-N）

- 適合誰：願意用訂閱額度換取多份實作再人工挑選的 builder。
- 在什麼情況使用：UI 或架構方向不明、希望同時看 Claude Code 與 Codex 等
  不同 runtime 的結果。
- 帶來的價值：worktree 隔離降低互相踩檔，Orca 提供並排 diff 與合併入口。

### 多議題並行、從 Linear／GitHub 開工

- 適合誰：issue 驅動、同時有多張 ticket 的團隊或 solo maintainer。
- 在什麼情況使用：從看板或 PR 列表直接 spawn worktree，不必手動開五個 terminal。
- 帶來的價值：任務來源與 agent 執行環境在同一 app 閉環。

### 離桌監控與遠端算力

- 適合誰：agent 跑很久、或本機想 offload 到遠端 build 機的使用者。
- 在什麼情況使用：SSH worktree + 手機 companion 通知。
- 帶來的價值：人不必綁在單一機器前，仍保留介入與追問能力。

## 我們可以學什麼

- **值得借鑑的 product idea**：以 **worktree（或等價隔離 workspace）** 作為
  agent 任務的一級 citizen；chat、branch、runtime 身份、preview port、review
  狀態都掛在同一個 task 上，而不是散在 tab 與外部 git 指令裡。
- **值得借鑑的 interaction / workflow**：平行結果要支援 **比較 → 註解 → 餵回
  agent → 合併** 的證據鏈；行動端只做「通知 + 輕量 steering」，不試圖在手機
  上複製完整 IDE。
- **在 2D workspace 裡會變成什麼**：可把每個 worktree 畫成平面辦公室裡的一
  **工位／隔間**（2D 俯視或 pixel 樓層），工位上標 agent runtime、branch、
  狀態（執行中／待 review／已合併）；中央走道或牆面是 diff 比較與 PR 看板。
  這是 **spatial 化編排狀態**，不是把 Orca 現有 sidebar 貼上像素皮。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室中，每個 worktree 對應一張
  桌子與一位「在場的 agent 員工」；人走近某桌才展開 terminal／diff；遠端 SSH
  工位可視覺化成另一棟或另一層。重點是 **誰在哪個隔離環境做事**，而非 3D
  模型展示。
- **不值得照搬或需要重新設計的地方**：Orca 本體是 **ADE dashboard**（分屏、
  tab、終端），不是我們定義的 2D／3D workspace 終局；若只做「更炫的 Conductor
  側欄」價值有限。worktree 亦 **不是安全 sandbox**（權限仍接近本機使用者），
  平行合併仍靠人判斷衝突——spatial UI 不能假裝已解決 trust boundary。

## 初步看法

- 最有價值的部分：開源、跨平台地把 **多 runtime × 平行 worktree × review
  閉環** 收斂成一套產品敘事，並補上行動與 SSH 兩種「人不在主機前」的路徑。
- 最大限制或疑問：repo 體積大、迭代極快，文件與 changelog 可能領先本筆記；
  實際權限模型、enterprise 與 team 功能尚未在本 Cell 驗證。
- 是否值得進一步研究或親自體驗：值得，尤其與 Conductor、Cursor worktrees
  對照 **task 物件模型** 與 **parallel state 的可視化** 時。

## 後續補充（選填）

Not tried yet。若 hands-on，建議記錄：平行 fan-out 上限、合併衝突 UX、mobile
延遲與 SSH 斷線恢復，以及與自帶 Claude／Codex 登入的 rate-limit 顯示是否影響
workflow。

## Sources

- Official website：https://onorca.dev/
- Repository：https://github.com/stablyai/orca
- Documentation：https://www.onorca.dev/docs/
- Parallel worktrees：https://www.onorca.dev/docs/model/worktrees
- Mobile：https://www.onorca.dev/docs/mobile
- SSH worktrees：https://www.onorca.dev/docs/ssh
- CLI overview：https://www.onorca.dev/docs/cli/overview
