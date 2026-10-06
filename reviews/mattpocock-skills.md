# Skills For Real Engineers (mattpocock/skills)

> Cell ID：[https://github.com/mattpocock/skills](https://github.com/mattpocock/skills)
>
> Status：`untried`
>
> Category：Agent workflow skills
>
> Last updated：2026-10-05

## 產品介紹

這是 Matt Pocock 公開的一組 **agent skills**，目標是讓 coding agent 做「真工程」：
對齊需求、維持共用語言、用測試與 review 建立回饋，而不是只靠長 prompt 或
全自動流程框架。Skills 以 Markdown 指令檔形式存在，可裝進 Claude Code、Codex
等支援 Agent Skills 標準的 harness。

使用者通常先在 repo 跑一次 `/setup-matt-pocock-skills` 設定 issue tracker、
triage 標籤與文件位置，之後用 `/grill-me`、`/grill-with-docs` 對齊方向，
再用 `/to-spec`、`/to-tickets`、`/implement` 等技能把對話變成 spec、工單與
實作。整體定位是 **小塊、可組合、可改寫** 的紀律，刻意避開 GSD、BMAD、
Spec-Kit 那類「流程包辦一切」的做法。

## 主要 Features

### 對齊與 grilling（user-invoked）

`/grill-me` 與 `/grill-with-docs` 在動手前用訪談把設計分支問清楚；後者還會
同步建立或更新 `GLOSSARY.md` 與 ADR，把專案術語變成 agent 與人共用的語言。
底層 reusable 的 model-invoked `grilling` 也被 triage、wayfinder 等技能重用。

### Spec、工單與實作鏈

`/to-spec` 把已討論內容收成 spec 並發到 issue tracker；`/to-tickets` 拆成
tracer-bullet 工單並標示 blocking 關係；`/implement` 依 spec 或 tickets 實作，
在約定邊界跑 `/tdd`，結束前走 `/code-review`。`/implement-spec` 則在單一
integration branch 上把整份 spec 當 **task graph**，對 ready frontier 並行跑
implementer subagent。

### 回饋迴圈與品質

`/tdd` 推 red-green-refactor；`/diagnosing-bugs` 用分階段診斷迴圈處理難 bug；
`/code-review` 並行檢查 **Standards** 與 **Spec** 兩軸；`/retro` 在 session 後
建議改進 agent 環境（steering 檔、檢查、工具等）。

### 長期工作與 handoff

`/wayfinder` 把超大範圍工作拆成 issue tracker 上的 decision tickets，逐張
釐清路徑；`/handoff` 把當前對話壓成 handoff 文件，讓另一個 agent 延續。
`/research` 可背景調查並寫入附引用來源的 Markdown。

### 雙通道安裝與分發

**Claude Code 官方 marketplace plugin**（`mattpocock-skills`）提供唯讀、可自動
更新的整包訂閱；**skills.sh**（`npx skills add mattpocock/skills`）則把可編輯
檔案複製進專案，適合 fork 與自改。兩者同時安裝會重複，文件要求二選一。

### User-invoked 與 model-invoked 分層

User-invoked 技能只能由使用者打 slash 觸發，負責 **編排**；model-invoked 可由
agent 在任務符合時自動選用，承載 **可重複的紀律**。編排 skill 可呼叫 model
skill，但不會再呼叫另一個 user-invoked skill。

## 主打賣點

- 把軟體工程經典（對齊、共用語言、小步回饋、模組深度）濃縮成可插拔 skills，
  強調 **使用者仍握有控制權**，而不是把流程外包給 monolithic 方法論。
- **Grill + glossary** 被定位成最高 ROI 的溝通修補，直接針對「agent 沒懂你要
  什麼」與「廢話太多」兩大失敗模式。
- 與 [gstack](https://github.com/garrytan/gstack) 類似都是「員工／技能包」，但
  mattpocock/skills 更偏 **單 repo 工程紀律與 issue tracker 整合**，且明確區分
  編排層與紀律層；gstack 更偏完整 product→ship 角色劇本與 browser QA。
- Claude plugin 與 skills.sh 雙軌，分別服務「訂閱更新」與「擁有並改寫」兩種
  心態；Codex 原生 plugin 尚未確認（ADR 記載仍 defer，通用路徑是 skills.sh）。

## 使用情境

### 新功能開工前先對齊，而不是直接寫 code

- 適合誰：常遇到 agent 做錯方向、或需求自己還沒想清楚的開發者。
- 在什麼情況使用：任何非 trivial 變更的起手式。
- 帶來的價值：用 grilling 與 glossary 降低 misalignment，後續 spec／implement
  才有穩定輸入。

### 把對話變成可追蹤的 spec 與工單 graph

- 適合誰：已用 GitHub／Linear 等 tracker、希望 agent 產出與人類流程接軌的團隊。
- 在什麼情況使用：討論已足夠，需要發 spec、拆 ticket 或並行 implement。
- 帶來的價值：`to-spec`／`to-tickets`／`implement-spec` 把 **artifacts 與
  blocking 關係** 外化到 tracker，而不是只留在 chat history。

### 跨 session 或跨 agent 延續大專案

- 適合誰：單次 context 裝不下、或要在不同 harness 間交接的人。
- 在什麼情況使用：wayfinder 規劃長路線，或 session 結束前需要交接。
- 帶來的價值：`handoff` 與 decision tickets 把 **memory／handoff** 做成文件
  與 tracker 狀態，減少從零重講。

## 我們可以學什麼

- **值得借鑑的 product idea**：把 agent 失敗模式對應到具名 skill（對齊、語言、
  TDD、架構 survey），並用 setup skill 一次綁定 repo 的 tracker 與文件慣例；
  編排（user-invoked）與紀律（model-invoked）分開，避免 slash 命令爆炸卻又
  缺底層 reusable 行為。
- **值得借鑑的 interaction / workflow**：grill → spec → tickets（含 blocking
  graph）→ implement（含 subagent 並行）→ 雙軸 code-review → PR 模板；wayfinder
  把「太大裝不下 context」變成 tracker 上的決策地圖，而不是一次塞滿 prompt。
- **在 2D workspace 裡會變成什麼**：平面辦公室或樓層圖上，每位「員工」對應一
  類 skill（Griller、Spec writer、Implementer、Reviewer）；當前 active 的
  user-invoked 流程像 **接待台流程卡**，task graph 與 ticket 狀態顯示在牆上
  Kanban；glossary／handoff 是共用白板，不是 chat 側欄。
- **在 3D workspace 裡會變成什麼**：走進 office 時，只有被叫進來的 skill「員工」
  在桌前工作（例如 grilling 時只有訪談官在場）；implement-spec 並行時像多張
  桌子同時施工，但共用同一面 spec／tracker 牆；retro 結束後像後勤在調整工具
  間（steering、CI），而不是全公司同時開會。
- **不值得照搬或需要重新設計的地方**：skills 數量多、且與 Matt 個人工程品味
  綁很深，直接複製 slash 名稱對我們 workspace 未必合适；Claude plugin 與
  skills.sh 雙軌是上游分發策略，我們若要內建 skills 應設計 **單一來源 truth +
  可編輯 overlay**；plugin 對 Codex 的 manifest 限制（ADR）提醒我們 catalog
  結構要配合各 harness 的安裝模型，不能只假設一種目錄樹。

## 初步看法

- 最有價值的部分：用 **grilling + 共用語言 + tracer tickets + 並行 implement**
  把「溝通、計畫、執行、驗證」拆成可組合 primitives，且保留人類控制權。
- 最大限制或疑問：學習曲線與 slash 記憶成本不低；未親自跑 setup 與 tracker
  整合，實際 handoff 品質與 subagent 並行邊界仍待驗證。
- 是否值得進一步研究或親自體驗：值得作為 **agent 工程紀律** 的對照 Cell；不必
  先測完每個 skill，但可選一條小 feature 走 grill → to-tickets → implement。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：https://aihero.dev/skills
- Repository：https://github.com/mattpocock/skills
- Documentation：repo `README.md`、各 skill 的 `SKILL.md`、`.agents/adr/`
