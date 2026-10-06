# Emdash

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/emdash.md)
> - [Pages 閱讀器](https://giorno-giovanna-dio.github.io/AI-Agent-Products-Reviewed/#/reviews/emdash.md)（`main` 部署後）
>
> Cell ID：[https://github.com/generalaction/emdash](https://github.com/generalaction/emdash)
>
> Status：`untried`
>
> Category：Multi-agent coding workspace（ADE）
>
> Last updated：2026-10-05

## 產品介紹

Emdash 是 General Action 推出的開源桌面應用，自稱 Agentic Development
Environment（ADE）。它把「一次只開一個 terminal 跑 coding agent」改成以
**任務（task）** 為單位：每個任務在獨立的 Git worktree 與 branch 裡跑你
已安裝的 agent CLI（Claude Code、Codex、Cursor、OpenCode 等），完成後在
同一個 app 裡看 diff、改檔、查 CI、開 PR。

產品定位是 **provider-agnostic**：不綁單一模型或 vendor，而是偵測本機
PATH 裡的 agent CLI，並透過 lifecycle hooks 追蹤狀態、通知與可恢復的
session。資料以本機 SQLite 為主，官方標榜 local-first，程式碼與對話預設
不送到 Emdash 伺服器（各 agent 供應商仍依其政策處理 prompt 與程式碼）。

## 主要 Features

### 平行任務與 worktree 隔離

每新增一個 task 就建立對應 worktree，branch、terminal、對話與 review 狀態
綁在同一任務上。多個 agent 可同時跑，避免共用同一 checkout 互相踩檔。

### 多 provider CLI 與 hooks

支援數十種 coding agent CLI，安裝後自動偵測。部分 provider 可寫入帶
marker 的 lifecycle hooks，讓 Emdash 在 session 內追蹤進度；在 Emdash
外執行 agent 時 hooks 不影響正常使用。

### Issue 與工單匯入

**可從 Linear、GitHub、Jira、GitLab、Asana、Notion、Monday.com 等來源把
issue 丟進 agent 任務，把「票務系統」和「agent 實作」接在同一 workflow。**

### Review、GitHub 與 CI

內建 diff 檢視、檔案編輯、建立 PR，並可在 app 內查看 GitHub Actions 等
check 狀態，把實驗 branch 收斂成可 merge 的變更。

### 排程與自動化（Automations）

可設定 recurring agent runs（例如定期掃 bug、release 前檢查），保留歷史、
重跑與可 review 的任務紀錄，把「一次性對話」延伸成可重複的操作。

### Library（prompts、skills、MCP）

集中管理可重用的 prompt、skills 與 MCP server 設定，並同步到支援的
agent，減少每個 task 從零設定工具的摩擦。

### 遠端專案與 tmux

透過 SSH/SFTP 在遠端機器上跑相同 worktree 流程；支援 tmux，讓本機或遠端
長時間 session 在重連後仍可接續。網站亦提到以 setup/teardown script
配置 workspace，細節以官方文件為準。

### 內建瀏覽器預覽

在 task 內開 local dev server 或遠端 preview，不用切換到外部瀏覽器就能
驗證 agent 改動。

## 主打賣點

- **開源、跨平台、不鎖 provider**：Apache 2.0，macOS／Windows／Linux，
  與 Conductor 等 macOS 原生 ADE 相比，強調「自架心智模型」與 agent
  生態相容面。
- **Task = worktree + agent + review 一條龍**：賣點是平行探索與 best-of-N
  式比較，而不是單一 chat 視窗。
- **工單匯入 + 排程**：把 agent 工作接到真實工程流程（issue tracker、
  週期性維護），不只是 ad-hoc prompt。
- **Library 與 MCP**：把 prompts／skills／工具當成團隊可共享資產，接近
  IDE 外掛生態，但面向 agent runtime。
- 與「只在 Cursor／VS Code 裡加 parallel agents」相比，Emdash 更像獨立
  **ADE 控制台**；與 Conductor 類似，但多了開源、更廣整合與 automations
  等方向（尚未實測細部 UX 差異）。

## 使用情境

### 同一需求多 agent 或多次嘗試

- 適合誰：想 parallel 試不同 agent 或不同實作策略的開發者。
- 在什麼情況使用：一個 bug 或 feature 有多種修法，或要比較 provider 行為。
- 帶來的價值：worktree 隔離降低衝突成本，集中 diff 與 PR 流程加速收斂。

### 從 Linear／Jira／GitHub Issue 直接開工

- 適合誰：票務驅動、希望 agent 從 issue 描述起跑的團隊。
- 在什麼情況使用： backlog 項目需要實作或初步 patch。
- 帶來的價值：減少複製貼上 context，issue 成為 task 的 seed。

### 遠端或 GPU 機器上跑 agent

- 適合誰：本機輕、運算或 repo 在 SSH 可達的伺服器上。
- 在什麼情況使用：大型 monorepo、需要特定環境或長時間 job。
- 帶來的價值：桌面 app 仍提供 task／review 統一介面，底層執行在遠端。

## 我們可以學什麼

- **Task 是首要物件**：branch、runtime、terminal、對話、preview、PR 狀態
  應綁在同一識別上，而不是散在 tab 與 folder 裡。
- **Provider 是外掛、workspace 是殼**：UI 應呈現「哪個 task 用哪個 CLI、
  目前 hook 回報什麼狀態」，而不是假設只有一種 agent。
- **工單與排程是編排層**：incoming issue 與 recurring run 可視為 task
  factory，人仍負責 approve merge 與衝突處理。
- **Library 降低重複設定**：prompts、skills、MCP 適合做成 workspace 級
  共享層，再下放到各 task。
- **在 2D workspace 裡會變成什麼**：俯視「專案樓層」，每個 task 是一格
  工位（顯示 branch 名、agent 圖示、狀態：執行中／待輸入／待 review）；
  issue 匯入像從左側 conveyor 進工位；automations 是定時亮起的工位；
  diff 比較區是中央 review 長桌，可並排多個 task 的變更摘要。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室中，每個 agent 坐在
  對應 worktree 的 desk，頭頂或螢幕顯示 ticket ID 與 CI 燈號；人走向
  某 desk 介入 terminal 或批准；排程任務像夜班員工在固定時段開工；
  「best-of-N」像同一會議室裡多張白板並列，人選一版帶回主線。
- **不值得照搬**：ADE 側欄＋列表已是成熟 pattern；worktree 不是 security
  sandbox，平行結果仍要人 merge 與解衝突；35 個 provider 的 capability
  矩陣在 workspace 裡應簡化為「此 task 可用能力」，避免設定地獄。

## 初步看法

- 最有價值的部分：開源、local-first 的 parallel task 模型，以及 issue
  匯入、automations、Library 把 agent 工作接進工程節奏。
- 最大限制或疑問：與 Conductor 等同屬 ADE dashboard，對我們要建的 spatial
  workspace 是互補參考而非直接產品形態；遠端 provisioning 與 hook 行為
  細節尚未確認。
- 是否值得進一步研究或親自體驗：值得，特別是 automations、Library 與
  multi-provider 狀態同步如何影響 human oversight。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：[https://emdash.sh/](https://emdash.sh/)（亦見 [emdash.com](https://emdash.com/)）
- Repository：[https://github.com/generalaction/emdash](https://github.com/generalaction/emdash)
- Documentation：[https://emdash.sh/docs](https://emdash.sh/docs)
- Providers：[https://emdash.sh/docs/providers](https://emdash.sh/docs/providers)
- Y Combinator：[https://www.ycombinator.com/companies/emdash](https://www.ycombinator.com/companies/emdash)
