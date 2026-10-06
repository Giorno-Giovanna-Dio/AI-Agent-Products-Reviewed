# Sandcastle

> Cell ID：[https://github.com/mattpocock/sandcastle](https://github.com/mattpocock/sandcastle)
>
> Status：`untried`
>
> Category：Sandbox agent orchestration（TypeScript library）
>
> Last updated：2026-10-05

## 產品介紹

Sandcastle 是 Matt Pocock（Total TypeScript）維護的 TypeScript 函式庫，npm
套件名為 `@ai-hero/sandcastle`。它用程式編排「在隔離環境裡跑 coding agent」
這整件事：你呼叫 `run()` 或組合 `createWorktree()`、`createSandbox()`，
指定 agent（例如 Claude Code、Codex）與 sandbox（Docker、Podman、Vercel
microVM 等），agent 在 sandbox 裡改 code、commit，最後依 branch 策略合併
或留在指定分支。

它沒有自己的 workspace UI，比較像 CI 腳本或自訂工具裡的 **agent runtime
harness**：負責 git worktree、容器生命週期、prompt 注入、log 與 commit
結果，而不是替使用者排任務看板或聊天介面。

## 主要 Features

### 單次 AFK 執行（`run()`）

一行 API 完成 sandbox 建立、agent 執行、迭代上限、idle timeout，以及回傳
commits、branch、iterations。適合腳本化「丟 prompt、等 agent 交 commit」。

### 可重用的 warm sandbox（`createSandbox()`）

同一容器裡連續跑多輪 agent（例如先 implement 再 review），中間還能用
`sandbox.exec()` 跑測試當 gate。依賴與 build 產物可留在 sandbox，不必每輪
重開容器。

### Worktree 一等公民（`createWorktree()`）

把 git worktree 從 sandbox 拆開：可先 `interactive()` 在人機對話裡探索，
再把同一 worktree 交給 sandbox 內的 AFK agent。關閉時若還有未提交變更會
保留 worktree 路徑，方便人工接手。

### Branch 策略

- **head**：agent 直接寫 host working tree（bind-mount 預設）。
- **merge-to-head**：在暫存分支上工作，結束後 merge 回 HEAD。
- **branch**：commit 落在具名分支，可重用同一 worktree。

這讓「平行多 agent」與「合併回主線」變成明確設定，而不是隱藏在 UI 背後。

### Sandbox 與 agent 皆可插拔

內建 Docker、Podman、Vercel、`noSandbox()`；agent 端目前文件以 `claudeCode()`
為主，並提到 Codex、Pi 的 session resume。自訂 provider 用
`createBindMountSandboxProvider` 或 `createIsolatedSandboxProvider`。

### Prompt 引擎（不綁 workflow）

用 `promptFile` 或 inline `prompt`；檔案內可 `` !`shell` `` 平行拉動態
context（例如 `gh issue list`），`{{KEY}}` 與內建 `{{SOURCE_BRANCH}}` /
`{{TARGET_BRANCH}}` 做替換。completion signal（預設
`<promise>COMPLETE</promise>`）可提早結束迭代。官方刻意不在 library 內
塞 opinionated 任務管理。

### Session resume 與 fork

可 `--resume` 延續 Claude Code / Codex 對話；`fork()` 從同一 session 分岔
多條子對話。文件強調 **fork 只隔離 session JSONL，不隔離 branch/worktree**；
要安全平行 fan-out 必須各自指定不同 `branch` 策略。

### 觀測與控制

log 可寫檔或 stdout，`onAgentStreamEvent` 轉發 agent 串流；`AbortSignal`
可取消 run；結構化 `output` 可從 stdout 抽 typed 結果（需單次 iteration）。

## 主打賣點

- **Git 與 sandbox 編排一體化**：worktree、merge、commit 收集是核心，不是
  聊天產品的外掛。
- **Provider-agnostic**：換容器後端或 agent CLI 不必重寫整套流程。
- **腳本／CI 友善**：TypeScript API 適合 parallel AFK agents、review pipeline、
  自訂 orchestrator；與 [Conductor](reviews/conductor.md) 這類 macOS
  workspace **互補**——Conductor 管「人看的控制台」，Sandcastle 管「程式
  驅動的執行環境」。
- **刻意薄的工作流層**：不像 [gstack](reviews/gstack.md) 打包成多角色
  skills；Sandcastle 只保證「在哪跑、改哪條 branch、怎麼合併」。

## 使用情境

### 平行處理多個 issue 或分支

- 適合誰：已在用 Claude Code / Codex CLI、想一次開多個 AFK 任務的團隊。
- 在什麼情況使用：每個任務對應不同 `branch`，用 `Promise.all` 跑多個
  `run()`。
- 帶來的價值：隔離 sandbox 降低 agent 互相踩檔案的風險，結束時拿到明確
  commit SHA 列表。

### Implement → 測試 → Review 管線

- 適合誰：希望固定「寫 code → 跑 test → 第二個 model review」的人。
- 在什麼情況使用：單一 `createSandbox()` 內多段 `sandbox.run()`，中間
  `exec("npm test")`。
- 帶來的價值：同一 warm 環境省掉重裝依賴，管線邏輯寫在 TypeScript 而非
  手動複製 prompt。

### 人機探索後交 AFK 收尾

- 適合誰：需要先互動釐清需求、再離開讓 agent 長跑的使用者。
- 在什麼情況使用：`createWorktree()` → `wt.interactive()` → `wt.run()`。
- 帶來的價值：同一 worktree 承接 context，AFK 階段仍強制 sandbox。

## 我們可以學什麼

- **值得借鑑的 product idea**：把 **branch + worktree + sandbox** 當成
  agent 任務的「物理座標」——每個任務有 branch 名、worktree 路徑、容器
  生命週期；merge 策略決定任務何時回到主線。這比只在 chat 裡記 task id
  更適合 coding agent。
- **值得借鑑的 interaction / workflow**：**split ownership**（worktree 與
  container 分開關閉）、dirty worktree 保留、implement/review 在同一
  sandbox 累積 commit——都是「人可介入、machine 可接棒」的 handoff 點。
- **在 2D workspace 裡會變成什麼**：平面地圖上每一格是一個 **worktree／
  branch 槽位**；正在跑的 `run()` 顯示為該格上的 agent 動畫與 log 尾端；
  `merge-to-head` 完成時可視化成箭頭合流回「主線月台」。平行多 agent 就是
  多格同時亮起，每格綁不同 branch 名稱與 commit 列表，而不是一個聊天分頁
  列表。
- **在 3D workspace 裡會變成什麼**：每個 sandbox 或 worktree 是一間 **可
  走進的小室**——門牌是 branch 名，室內是 agent（Claude/Codex）在改檔；
  `interactive()` 時門開著、使用者也在室內；切到 AFK 時門關上但窗外仍可看
  log 串流。review 第二輪 agent 走進**同一間室**接續 commit 歷史，像換班
  而非換公司。這是「辦公室隔間 + 產線」，不是 ADE 側欄。
- **不值得照搬或需要重新設計的地方**：預設權限模式對 AFK 偏 aggressive
  （文件提到 `--dangerously-skip-permissions`）；在 workspace 產品裡應把
  permission 與 sandbox 政策做成可見、可 per-room 設定。fork 與平行 branch
  的規則對一般使用者太 technical，UI 必須防止「多 agent 寫同一 working
  tree」這類 footgun。

## 初步看法

- 最有價值的部分：用 TypeScript 把 **git 隔離 + 容器隔離 + 多段 agent
  編排** 做成穩定 primitive，適合當未來 workspace 的 execution layer。
- 最大限制或疑問：需要 Docker／Podman 或 Vercel 等環境；沒有內建 human
  review UI、PR 或任務看板。與 Conductor、Cursor parallel agents 的差異在
  **編排入口是 code 不是 app**——我們若要 2D/3D workspace，可能要「視覺化
  Sandcastle 概念」而非直接嵌入此 library。
- 是否值得進一步研究或親自體驗：值得先當架構參考；hands-on 可等到要設計
  workspace 的 sandbox／branch 模型時，在 `/workspace-labs/sandcastle` 跑
  最小 `run()` 即可，不必測完所有 provider。

## 後續補充（選填）

Not tried yet. 若實測，建議只驗證：Docker + `branch` 策略 + 兩段
`createSandbox().run()` pipeline，並確認 commit 與 worktree 保留行為。

## Sources

- [Sandcastle repository](https://github.com/mattpocock/sandcastle)
- [npm `@ai-hero/sandcastle`](https://www.npmjs.com/package/@ai-hero/sandcastle)
- [ADR 0003 — reuse worktree by default](https://github.com/mattpocock/sandcastle/blob/main/docs/adr/0003-reuse-worktree-by-default.md)
- [ADR 0018 — fork is session-only](https://github.com/mattpocock/sandcastle/blob/main/docs/adr/0018-fork-is-session-only.md)
