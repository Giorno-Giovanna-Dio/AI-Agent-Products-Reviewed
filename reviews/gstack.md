# gstack

> Cell ID：[https://github.com/garrytan/gstack](https://github.com/garrytan/gstack)
>
> Status：`untried`
>
> Category：Agent workflow skills
>
> Last updated：2026-10-04

## 產品介紹

gstack 是一套為 coding agents 準備的 open-source skills。它把產品開發拆成
不同角色和階段，例如釐清需求、規劃、code review、瀏覽器 QA 和 shipping。

它不是 Conductor 的內建 workflow。Conductor Quick Start 可以協助初始化
gstack，但 gstack 本身是 Garry Tan 維護的獨立 MIT repository。

## 主要 Features

### 完整開發流程

把 agent 工作串成 `office-hours → plan → implement → review → QA → ship →
retro`，讓不同階段有明確目的，而不是只靠一個長 prompt。

### 不同專業角色

提供 product、CEO、engineering、design、QA 和 release 等 skills，讓 agent
在每個階段採用不同的檢查角度。

### Browser QA

`/browse` 和 `/qa` 讓 agent 操作 Chromium、檢查實際頁面、修正問題並重新
驗證。`/qa-only` 則只回報結果，不修改程式碼。

### Review 與 Shipping

`/review` 用於合併前檢查；`/ship` 則把 tests、coverage、push 和 pull request
串成發佈流程。

### 第二意見

`/codex` 不是 gstack 的預設工作方式。gstack 主要跑在 **Claude Code** 這個
runtime 上；`/codex` 是從 Claude Code 裡再叫 OpenAI Codex CLI，用另一套
runtime 做 review、challenge 或諮詢。

所以「第二意見」指的是：原本由 Claude Code 做的事，再請 Codex 用不同
context 和不同 agent system 檢查一次。gstack 自己也把這個 skill 標成
Claude wrapper，因此它不會在 Codex host 上再呼叫自己。

## 主打賣點

- 重點不是新的 model，而是把開發經驗和檢查清單包成可重複使用的 skills。
- 從產品構想到 QA 和發佈提供一套 opinionated workflow。
- 可以與 Conductor 等 workspace 工具搭配，但不依賴特定 workspace UI。
- 和 [gbrain](https://github.com/garrytan/gbrain) 互補：gstack 管流程與角色，gbrain 管跨 session 記憶。兩者應視為不同 Cell。

## 使用情境

### 將模糊想法整理成可執行計畫

- 適合誰：容易直接叫 agent 寫 code、卻缺少前期思考的人。
- 在什麼情況使用：新 feature 或產品方向仍不清楚。
- 帶來的價值：先經過產品、設計和 engineering 角度整理。

### 建立固定 Review 和 QA 習慣

- 適合誰：希望 agent 在交付前自行檢查的團隊。
- 在什麼情況使用：功能完成但尚未合併或發佈。
- 帶來的價值：將 review、瀏覽器測試和 regression tests 變成固定步驟。

## 我們可以學什麼

- Skills 可以作為 workspace 中可重用的 workflow blocks，而不只是 prompt
  shortcuts。
- 不同角色可以共享同一份 artifact，例如產品規劃產生的內容再交給
  engineering review 和 QA。
- 2D workspace 可以把 skills 呈現為 pipeline、stage cards 或可組合節點。
- 這裡說的 3D，不是 Firstmate、Maestro 或 Conductor 那種 ADE 分頁加上象徵性
  team orchestration。那些本質上仍是 agent dashboard。
- 比較接近的 3D 方向是
  [Agent Office](https://github.com/AgentSystemLabs/agent-office)：把 agent
  放進 3D 辦公室，用位置、桌子、樓層表示誰在做什麼。它目前偏陽春，但比
  ADE 更接近「空間裡的團隊」。
- gstack 的價值不是再做一個 ADE，而是把 office-hours、review、QA、ship
  這些階段變成 3D office 裡可看見的工作站、房間或 desk 流程。
- 我們應學習階段與 handoff 的設計，而不是直接複製所有 skill 名稱。

## 初步看法

- 最有價值的部分：把軟體開發方法變成 agents 可重複執行的角色和流程。
- 最大限制或疑問：skills 很多，可能增加 context、輸出長度和使用者理解成本。
- 是否值得進一步研究或親自體驗：值得先研究 workflow 設計；不必立即測完每個
  skill。

## 後續補充（選填）

若之後實測，優先選 `/office-hours`、`/review` 和 `/qa` 各一個代表性流程，
而不是逐一測試全部 skills。

## Sources

- [gstack repository](https://github.com/garrytan/gstack)
- [gstack skills reference](https://github.com/garrytan/gstack/blob/main/docs/skills.md)
- [/codex skill](https://github.com/garrytan/gstack/blob/main/codex/SKILL.md)
- [Conductor Quick Start reference](https://www.conductor.build/changelog/0.43.0-codex-skills-plan-mode-fast-mode)
- [Agent Office](https://github.com/AgentSystemLabs/agent-office)
