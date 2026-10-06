# Maestro

> Cell ID：[https://github.com/RunMaestro/Maestro](https://github.com/RunMaestro/Maestro)
>
> Status：`untried`
>
> Category：Multi-agent coding orchestration（桌面 ADE）
>
> Last updated：2026-10-05

## 產品介紹

Maestro 是跨平台的桌面應用，定位為「AI agent 編排指揮中心」。它不改寫你
已在 Claude Code、Codex、OpenCode 等工具上設好的 MCP、skills 與權限，而是
把多個 agent 工作階段收進同一個高速、鍵盤優先的控制台，讓 power user 同時
推進多個專案與長時間無人值守的任務佇列。

典型用法是：在 Maestro 裡為每個 repo 或任務開 agent tab，用 Git worktree
平行開發，或用 Auto Run 把 markdown 清單逐項丟給 agent（每項通常是新
session、乾淨 context）；需要跨專案討論時則開 Group Chat，由 moderator
agent 協調多個 session 回答同一問題。

## 主要 Features

### 多 Provider Agent 管理

同一介面管理 Claude Code、OpenAI Codex、OpenCode、Factory Droid、Copilot-CLI
（beta）等 runtime。Maestro 標榜自己是 provider 的 pass-through：底層認證與
工具行為與原生 CLI 一致，差別在於任務導向的 session 管理與批次執行，而非
互動式 REPL 為主。

### Git Worktrees 與平行開發

可從 branch 選單建立 worktree 子 agent，各自目錄隔離；主 repo 可繼續手動
開發，子 agent 在背景處理清單任務，完成後可一鍵開 PR。這把「平行 agent」
綁在 Git 分支模型上，降低同 checkout 衝突。

### Auto Run 與 Playbooks

以資料夾內 markdown checkbox 清單驅動工作：Spec-Driven 模式逐項勾選執行，
Goal-Driven 模式則對單一文字目標反覆 spawn 新 agent 直到達標或停止。Playbook
是可重複使用的文件集合，支援循環、佇列多文件、CLI/cron 觸發，並可選「每 task
或每 document 開 fresh context」。官方強調長時間 unattended run（例如近
24 小時）與完整 run history。

### Group Chat（Moderator 協調）

多 agent 共室對話：使用者指定 moderator，由它 @mention 各 session、追問、
彙整答案。與單次 cross-agent @mention 不同，moderator 會多輪協調直到問題
被回答。可混用本機與 SSH remote agent；預設會等 busy agent 空出來再派工，
避免同 repo 雙 process 寫檔。

### 鍵盤優先與可觀測性

Command palette（`Cmd+K`）、快速切 agent、雙模式 terminal（AI / shell）、
用量儀表板、token 成本追蹤、輸出過濾、TTS 完成通知等，服務「少碰滑鼠、
多 tab 並行」的工作型態。內建 file explorer、git diff、特化資料檢視（JSON、
Parquet 等），減少在 app 外切換。

### Agent Resilience 與遠端控制

對 overload、quota 等錯誤自動重送 prompt、退避或讀取 reset 時間；Auto Run
批次可恢復。內建 web server 與 QR，可從手機監控 agent；另有 `maestro-cli`
供 headless、JSONL 輸出與 CI 整合。

## 主打賣點

- **編排與吞吐量**：相對「一個 chat 慢慢聊」，Maestro 賣的是多 agent 並行、
  清單化 Auto Run、可離開鍵盤仍推進任務。
- **Fresh context 紀律**：每個 checkbox 或 iteration 常是新 session，把
  context 污染當成 first-class 設計問題，而不是靠超長對話硬撐。
- **Moderator 式多 agent 協作**：Group Chat 把「誰去問誰、何時彙整」交給
  AI，人只提問題與 @ 對象。
- **與 Conductor 等 ADE 的交集**：同屬多 runtime、worktree、平行比較路線；
  Maestro 更突出開源（AGPL-3.0）、跨平台、Playbook/CLI、鍵盤流與長跑
  Auto Run；Conductor 在我們既有筆記中更偏 macOS 原生 workspace 與 PR
  整合（尚未在此 Cell 做並排實測）。

## 使用情境

### 多專案並行與清單化交付

- 適合誰：同時維護多個 repo、習慣把 work 寫成 spec/checklist 的開發者。
- 在什麼情況使用：功能拆解成 markdown 任務，夜間或離開時讓 Auto Run 逐項
  執行。
- 帶來的價值：人負責寫計畫與驗收，agent 負責重複性實作；每 task 乾淨
  context 降低長對話漂移。

### Worktree 實驗與一鍵 PR

- 適合誰：需要主線繼續開發、同時讓 agent 在分支上試作的團隊。
- 在什麼情況使用：UI 或 refactor 想平行試幾條 branch。
- 帶來的價值：目錄隔離比「多 terminal 同一 folder」更安全；與 Maestro 的
  agent tab 一一對應較直覺。

### 跨 codebase 的架構問答

- 適合誰：frontend / backend / infra 分屬不同 Maestro session 的人。
- 在什麼情況使用：「auth 在前後端怎麼接」這類需要多 context 的問題。
- 帶來的價值：Group Chat moderator 代勞路由與 synthesis，人不必手動 copy
  paste 多個 chat。

## 我們可以學什麼

- **Task 文件即 workflow 契約**：用 markdown checkbox + Playbook 把 agent
  工作外部化、可版控、可 CLI 觸發；這比只存 chat log 更適合 replay 與
  audit（含 `runs/` 工作副本選項）。
- **Context 切分策略要可配置**：「每 task / 每 document / goal iteration 開
  新 session」應在 workspace 裡可見、可選，讓人理解為何某步結果不受前步
  污染。
- **Busy agent 與檔案衝突是產品問題**：Group Chat 的「只使用空閒 agent」
  預設與等待提示，是把 concurrency hazard 暴露給使用者；future workspace
  需要類似 lock / worktree / read-only 模式的統一語彙。
- **Moderator 是編排 primitive**：不必每個 multi-agent 場景都讓人當調度；
  可學其「委派路由 + 多輪追問 + 合成」的角色，但須保留人可插嘴與停止。
- **在 2D workspace 裡會變成什麼**：俯視「專案樓層」— 每個 agent tab 是一
  間工位，Auto Run 清單是輸送帶上的任務卡（狀態：queued / running / done /
  blocked on error）；Group Chat 是中央會議桌，moderator 座位連線到各工位；
  worktree 分支可畫成平行車道。人點工位看 diff、成本與 run history，而不是
  只看 chat 串流。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室— 員工（agent）坐在各
  自 desk，頭頂顯示 provider 與 branch；Auto Run 播放時員工依次起身到「任
  務白板」前執行；Group Chat 時眾人聚到會議室，moderator 主持發言順序；
  手機遠端對應「在走廊看監控牆」。重點是誰在哪、是否在忙、是否在等 quota
  重試— 空間承載編排狀態，不是 3D 模型展示。
- **不值得照搬**：純 ADE 側欄 + terminal 美学若直接當成「spatial workspace」
  會混淆；gamification（achievements、conductor 等級）對 enterprise 編排
  未必必要；AGPL 與遠端 tunnel 的安全邊界需另評估，不能假設與 sandbox 等價。

## 初步看法

- 最有價值的部分：Playbook/Auto Run 把「長時間、多步、fresh context」做成
  一等公民；Group Chat moderator 是多 agent handoff 的可操作範例。
- 最大限制或疑問：仍是本機/SSH 上 agent 權限，worktree 非安全沙箱；與
  Conductor、Cursor parallel agents 的差異需實測互動品質與 PR 流程才能定論。
- 是否值得進一步研究或親自體驗：值得，尤其若 future workspace 需要「可版控
  任務佇列 + 可恢復長跑 + 跨 session 協調」的產品形狀。

## 後續補充（選填）

Not tried yet。尚未在本機安裝 Maestro 或執行 Auto Run / Group Chat。

## Sources

- Official website：[https://RunMaestro.ai](https://RunMaestro.ai)
- Repository：[https://github.com/RunMaestro/Maestro](https://github.com/RunMaestro/Maestro)
- Documentation：[https://docs.runmaestro.ai](https://docs.runmaestro.ai)
- Auto Run + Playbooks：[https://docs.runmaestro.ai/autorun-playbooks](https://docs.runmaestro.ai/autorun-playbooks)
- Group Chat：[https://docs.runmaestro.ai/group-chat](https://docs.runmaestro.ai/group-chat)
