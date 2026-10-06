# gstack

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gstack.md)
>
> Cell ID：[https://github.com/garrytan/gstack](https://github.com/garrytan/gstack)
>
> Status：`untried`
>
> Category：Agent workflow skills
>
> Last updated：2026-10-04

## 產品介紹

gstack 是一套為 coding agents 準備的 open-source skills。它比較像一組不同
專長的員工：產品、CEO、工程、設計、QA、發佈，而不是一個新的 workspace
畫面。每個 skill 代表一種人會怎麼看問題、檢查工作和交接。

它不是 Conductor 的內建 workflow。Conductor Quick Start 可以協助初始化
gstack，但 gstack 本身是 Garry Tan 維護的獨立 MIT repository。

## 主要 Features

### 完整開發流程

把 agent 工作串成 `office-hours → plan → implement → review → QA → ship →
retro`，讓不同階段有明確目的，而不是只靠一個長 prompt。

### 不同專業角色

提供 product、CEO、engineering、design、QA 和 release 等 skills。每個 skill
比較像一位專長不同的員工，用自己的檢查清單和工作習慣來看同一件事。

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

- 重點不是新的 model 或新的 UI，而是把開發經驗包成可重複僱用的專業員工。
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

- gstack 比較像員工，不是 dashboard，也不是房間本身。每個 skill 是一位有
  專長的人：誰來想、誰來做、誰來查、誰來發佈。
- 不同員工可以交接同一份 artifact，例如產品員工寫完規劃，再交給工程和 QA。
- 2D 可以把這些員工畫成角色卡或團隊列表；那仍然只是名冊，不是辦公室。
- 這裡說的 3D，不是去做 3D 模型或立體物件，也不是 Firstmate、Maestro 或
  Conductor 那種 ADE 分頁加上象徵性 team orchestration。那些本質上仍是
  agent dashboard。
  3D 指的是可以走進辦公室、看到員工在場的介面。
- 比較接近的 3D 方向是
  [Agent Office](https://github.com/AgentSystemLabs/agent-office)：辦公室是
  空間，gstack 則是走進這個空間裡的員工。Agent Office 目前偏陽春，但已經
  把「誰坐在哪、正在做什麼」放進 3D。
- 未來升級版不該再做一個 ADE，而是讓這些不同技能的員工真的出現在 office
  裡，需要時才被叫來，做完就把東西交給下一位。
- 我們應學習角色、專長和交接，而不是直接複製所有 skill 名稱。

## 初步看法

- 最有價值的部分：把軟體開發方法變成一組可重複僱用的專業員工。
- 最大限制或疑問：skills 很多，可能增加 context、輸出長度和使用者理解成本。
  這也是未來 3D office 的 improvement gap：不該讓整間公司同時站在面前，
  而要只叫目前真正需要的那位員工進來。
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
