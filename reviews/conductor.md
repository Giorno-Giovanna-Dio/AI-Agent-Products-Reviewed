# Conductor

> 狀態：`tried`
>
> 初步判斷：適合比較不同 agent runtime，以及同時進行多個相對獨立的任務。

## Index

- [產品定位](#產品定位)
- [已確認的功能](#已確認的功能)
- [實際體驗](#實際體驗)
- [與 Cursor 的比較](#與-cursor-的比較)
- [Agent runtime](#agent-runtime)
- [Proposal–Verifier Agent System](#proposalverifier-agent-system)
- [限制與風險](#限制與風險)
- [待驗證項目](#待驗證項目)
- [參考資料](#參考資料)

## Metadata

- 官方網站：[conductor.build](https://www.conductor.build/)
- 官方文件：[Conductor Docs](https://www.conductor.build/docs)
- 測試日期：2026-10-04
- 測試版本：未記錄
- 執行環境：macOS native
- 評測性質：初步實測與官方文件查核

## 產品定位

Conductor 是管理 coding-agent 工作區的桌面應用程式。它將 Claude Code、
Codex、Cursor Agent 與 OpenCode 等不同 agent harness 放進同一套
workspace、Git、review 與 pull request 流程。

它的大部分操作概念與 Cursor 相近。較有辨識度的價值是保留不同 agent
runtime 的原生行為，並讓多個實作或任務在隔離的 Git worktree 中平行
進行。

最適合的情況：

- 同時有數個相對獨立、可分開合併的任務。
- 想用相同需求比較不同 agent runtime 或實作方向。
- 想同時啟動多個 UI 版本並排檢查。
- 在意 agent portability，而不想把工作流程綁在單一 runtime。

## 已確認的功能

### Agent 與 workspace

- 支援 Claude Code、Codex、Cursor Agent 和 OpenCode。
- 每個獨立 workspace 使用自己的 Git worktree、branch、檔案、程序、
  diff 與 pull request 流程。
- 同一 workspace 可以開多個 agent tabs；它們共用 branch 與目前的程式碼
  狀態。
- 可從 branch、pull request、GitHub issue 或 Linear issue 建立 workspace。
- 可保留、合併、封存或丟棄不同實驗分支。

### 執行與預覽

- 可設定 setup、run 和 archive scripts。
- 本機 workspace 會取得 `CONDUCTOR_PORT` 起算的十個連續 ports，方便同時
  啟動多個版本。
- 固定 port、共用資料庫或單一 Docker stack 無法平行時，可將 run mode
  設為 `nonconcurrent`。
- Git ignored 檔案不會因 worktree 自動出現，需透過 Files 設定或 setup
  script 處理。

### Review 與整合

- Diff Viewer 支援 unified diff、依 commit 過濾與行內 comments。
- Checks 可彙整 Git、CI、deployment、review comments 與 todos。
- 可協助建立 pull request、處理 review feedback、修正 checks、merge 並
  archive workspace。

Conductor 提供的是比較與 review 的環境；它不會自動、客觀地判定哪份實作
最好。公平比較仍需固定 base commit、prompt、驗收條件及測試方式。

## 實際體驗

以下是本次操作的主觀觀察，不應視為固定產品規格：

- UI 視覺較單調、偏文字導向，感覺介於 shell 與 Cursor 之間。
- 任務完成時會播放提示音。
- 回答通常很長，較接近所選 model/runtime 的原始輸出風格。

「輸出很長是因為沒有 skills 限制」目前沒有足夠證據。Conductor 官方文件
明確支援 skills 與 repository instructions；輸出風格也可能受到 model、
reasoning level、agent runtime 或專案設定影響。

實測時曾顯示 Codex 使用 `gpt-5.6-sol` 與 high reasoning，但預設模型會隨
版本更新。官方 v0.89.1 changelog 已將 GPT-6.1 Sol 列為新的 Codex 預設，
因此不把特定模型寫成永久產品能力。

## 與 Cursor 的比較

Cursor 現在同樣支援平行 agents、subagents、Git worktrees、Cloud Agents
以及 `/best-of-n`，因此「平行執行」和「多模型比較」並非 Conductor
獨有。

| 面向 | Conductor | Cursor |
| --- | --- | --- |
| 核心定位 | 多種 agent harness 的 workspace 控制台 | IDE、agent runtime 與 cloud agent 平台 |
| 比較對象 | Claude Code、Codex、Cursor Agent、OpenCode 的不同 runtime | Cursor runtime 內的不同模型或多次執行 |
| 本機隔離 | Git worktree、workspace scripts、自動分配 ports | Git worktree 與 setup scripts |
| 平行策略 | 同 workspace 共享狀態；不同 workspace 使用獨立 branches | Subagents、worktrees、Cloud Agents 與 `/best-of-n` |
| 主要優勢 | Agent portability 與 native runtime 比較 | IDE 整合、完整 agent orchestration、cloud 與 multi-repo |

Cursor 的 `/best-of-n` 可對相同任務執行多個模型並讓使用者挑選結果，但每個
候選仍由 Cursor runtime 的 orchestration、tools、prompts、context handling
與 permissions 執行。Conductor 執行 Codex 或 Claude Code 時，則是在比較
各自完整的 agent harness，而不只是底層模型。

## Agent runtime

Agent runtime（Conductor 文件使用 **harness**）是包在模型外、讓模型能實際
工作的軟體層，包括：

- Prompt 與 context 管理
- Tools 與 shell 執行
- 檔案編輯及 diff
- Permission 與 approval 流程
- Skills、hooks 與專案 instructions
- Retry、錯誤處理與任務生命週期

可以粗略類比為：

> Model 是引擎；agent runtime 是車子的其餘部分；Conductor 是可以操作多台
> 不同車子的車庫。

所以同一個 GPT 或 Claude 模型放進不同 runtime，仍可能產生顯著不同的工作
方式和結果。

## Proposal–Verifier Agent System

Proposal–Verifier 架構不必使用兩套 agent runtime。一套 runtime 配合隔離的
agent contexts 已能形成實用基線。

| 設定 | 特性 |
| --- | --- |
| 同一 agent 自我檢查 | 成本最低，但容易確認自己的錯誤 |
| 相同模型、不同 context | 良好的基線 |
| 相同 runtime、不同模型 | 增加推理多樣性 |
| 不同模型、不同 runtime | 獨立性最高，但成本與複雜度也最高 |

在 Conductor 中可依目的選擇：

- 驗證同一份實作：implementer 完成後，在同一 workspace 開新的 verifier
  chat；verifier 應先保持 read-only。
- 比較多份候選實作：從相同 base commit 建立不同 workspaces，使用相同
  acceptance criteria 和測試。
- 多 runtime 交叉驗證：只有在高風險、安全敏感或需求特別模糊時才值得增加
  額外成本。

Verifier 應具備：

- 全新的 context。
- 原始 acceptance criteria。
- 直接檢查實際 diff，而非信任 implementer summary。
- 執行測試並重現行為。
- 以具體 evidence 回報。
- 不在「驗證」時默默重寫 implementation。

## 限制與風險

- 本機應用以 macOS 為主。
- Local workspace 是開發狀態隔離，不是 security sandbox；agent 和命令以
  目前使用者權限直接在 Mac 上執行。
- Local session 主要保存在本機，但 model request 仍會送往所選 provider。
- Cloud workspace 會將 session inputs/outputs 存在 Conductor 管理的
  infrastructure。
- 一個 branch 同時只能被一個 Git worktree checkout。
- Worktree 隔離不會自動隔離外部資料庫、Docker volumes、全域服務或 secrets。
- 多個平行實作仍可能在最後整合時產生語意或 merge conflicts。

## 待驗證項目

- 以完全相同 prompt 和 base commit 比較至少兩種 agent runtimes。
- 實測同一 workspace 的 implementer/verifier 工作流。
- 確認多個 UI workspace 同時執行時的 ports、資料庫與登入狀態隔離。
- 記錄輸出長度是否能透過 skills、instructions 或 reasoning 設定改善。
- 評估本機權限範圍是否符合實際專案的安全需求。

## 相關工具

- [gstack](gstack.md)：可由 Conductor Quick Start 初始化，但它是獨立的
  open-source agent skills 套件，不是 Conductor 自有的 workflow。

## 參考資料

- [Conductor: Harnesses](https://www.conductor.build/docs/reference/harnesses)
- [Conductor: Parallel agents](https://www.conductor.build/docs/concepts/parallel-agents)
- [Conductor: Git worktrees](https://www.conductor.build/docs/concepts/git-worktrees)
- [Conductor: Scripts](https://www.conductor.build/docs/reference/scripts)
- [Conductor: Security and permissions](https://www.conductor.build/docs/reference/security-and-permissions)
- [Conductor v0.89.1 changelog](https://www.conductor.build/changelog/0.89.1-gpt-6-1-sol)
- [Cursor: Worktrees and best-of-N](https://cursor.com/docs/configuration/worktrees)
- [Cursor: Subagents](https://cursor.com/docs/subagents)
