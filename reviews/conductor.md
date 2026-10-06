# Conductor

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/conductor.md)
> - [Pages 閱讀器](https://giorno-giovanna-dio.github.io/AI-Agent-Products-Reviewed/#/reviews/conductor.md)（`main` 部署後）
>
> Status：`tried`
>
> Cell ID：[https://www.conductor.build/](https://www.conductor.build/)
>
> Category：Multi-agent coding workspace
>
> Last updated：2026-10-04

## 產品介紹

Conductor 是一個管理 coding agents 的 macOS 應用程式。使用者可以在同一個
介面中操作 Claude Code、Codex、Cursor Agent 和 OpenCode，並為不同任務建立
彼此隔離的 workspaces。

它的概念像是把 terminal、Git worktrees、agent chats 和 pull request workflow
放進同一個控制台。每個 workspace 可以獨立開發、執行、預覽和決定是否合併。

## 主要 Features

### 多種 Agent Runtimes

可以在同一產品中使用 Claude Code、Codex、Cursor Agent 或 OpenCode。這不只
是在比較 models，也是在比較它們各自的 tools、prompts、context 和 permission
處理方式。

### 隔離的 Workspaces

每個 workspace 有自己的 Git branch、worktree、檔案和執行程序。多個 agents
可以平行工作，不會直接修改同一份 checkout。

### 平行比較不同做法

可以讓不同 agents 解相似任務，再透過 diff 和實際執行結果選擇較好的版本。
Conductor 提供比較環境，但不會自動判斷哪個結果最好。

### 同時預覽多個版本

Conductor 會為本機 workspace 分配不同 ports，適合把多個 UI 實作同時跑起來
並排比較。

### Review 與 Pull Request Workflow

內建 diff、comments、checks 和 pull request 流程，讓 agent 的結果能從實驗
workspace 回到正常的 code review。

## 主打賣點

- 最大特色是 agent portability：同一套 workspace workflow 可以使用不同
  agent runtimes。
- 適合同時有多個相對獨立任務的情況。
- 能比較的不只是 GPT 和 Claude models，而是完整的 Codex、Claude Code、
  Cursor 或 OpenCode 行為。
- Cursor 現在也有 worktrees、parallel agents 和 `/best-of-n`；Conductor
  真正較不同的是把多個原生 agent runtimes 放在同一個控制台。

## 使用情境

### 同時處理多個獨立任務

- 適合誰：需要同時推進多個 issues 或 features 的開發者。
- 在什麼情況使用：每個任務可以使用獨立 branch 和 pull request。
- 帶來的價值：不用等待上一個 agent 完成才開始下一個工作。

### 比較不同 Agents 或實作方向

- 適合誰：想了解不同 agent runtime 實際差異的人。
- 在什麼情況使用：同一需求可能有多種技術或 UI 解法。
- 帶來的價值：保留多個候選版本，再由人進行 review 和選擇。

### Implementer–Verifier

- 適合誰：希望 agent 實作後再由另一個 context 檢查的人。
- 在什麼情況使用：重要、模糊或風險較高的修改。
- 帶來的價值：verifier 可以直接看 diff 和測試，不只相信 implementer 的
  summary。這不一定需要兩套 runtimes；不同 context 已是實用起點。

### UI 並排實驗

- 適合誰：想快速比較數個前端版本的設計或產品團隊。
- 在什麼情況使用：多個 workspace 都需要啟動自己的 app。
- 帶來的價值：不同 ports 讓版本可以同時預覽。

## 我們可以學什麼

- Workspace 可以成為 agent 任務的主要單位，將 chat、branch、runtime、
  preview 和 review 狀態放在一起。
- Model 與 agent runtime 應分開呈現，因為相同 model 在不同 runtime 中也會
  有不同行為。
- 平行 agents 需要清楚顯示「誰在做什麼、在哪個 branch、目前什麼狀態」。
- 人不應只收到一串 agent output；更重要的是能比較結果、查看 evidence 並
  決定保留哪一個。
- 目前這類 workflow 用 2D sidebar、cards、diff 和並排 preview 已很有效。
  3D 只有在大量 agents、tasks 和 dependencies 需要空間分群時才可能增加價值。

## 初步看法

- 最有價值的部分：將不同 agent runtimes 放進一致且可比較的 workspace 模型。
- 最大限制或疑問：本機 agents 擁有目前使用者權限，worktree 並不是 security
  sandbox；平行結果仍需要人判斷和處理整合衝突。
- 實際體驗：UI 偏文字和 terminal 風格；完成任務有提示音；回答通常較長。
- 是否值得繼續研究：值得，尤其是 workspace、runtime identity、parallel
  state 和 side-by-side comparison 的呈現方式。

## 後續補充（選填）

Agent runtime 可以粗略理解為：model 是引擎，runtime 是車子的其餘部分，
Conductor 則是操作多台不同車子的車庫。

實測時曾看到 Codex 使用 `gpt-5.6-sol` 和 high reasoning，但預設模型會隨
版本更新，因此不把特定 model 視為 Conductor 的永久產品特色。

[gstack](gstack.md) 可以由 Conductor Quick Start 初始化，但它是獨立的 agent
skills 套件，不是 Conductor 自有的 workflow。

## Sources

- [Official documentation](https://www.conductor.build/docs)
- [Harnesses](https://www.conductor.build/docs/reference/harnesses)
- [Parallel agents](https://www.conductor.build/docs/concepts/parallel-agents)
- [Git worktrees](https://www.conductor.build/docs/concepts/git-worktrees)
- [Security and permissions](https://www.conductor.build/docs/reference/security-and-permissions)
- [Cursor worktrees and best-of-N](https://cursor.com/docs/configuration/worktrees)
