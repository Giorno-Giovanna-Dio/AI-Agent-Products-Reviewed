# Pi

> Cell ID：[https://github.com/earendil-works/pi](https://github.com/earendil-works/pi)
>
> Status：`untried`
>
> Category：Extensible coding agent runtime
>
> Last updated：2026-10-05

## 產品介紹

Pi 是一套以 TypeScript 為主的 **minimal agent harness**，核心是可組合的
runtime 與函式庫，而不是一個塞滿功能的單一 App。對終端使用者，它主要呈現
為 `@earendil-works/pi-coding-agent`：在專案目錄裡啟動的互動式 coding
agent CLI（TUI）。對整合者，同一套能力可以走 print／JSON 模式、RPC，或
直接用 SDK 嵌入自家產品。

設計哲學是「讓 Pi 配合你的工作流，而不是反過來」。內建只保留強預設（多
provider LLM、agent loop、工具呼叫、終端 UI），刻意 **不** 內建 sub-agents、
plan mode 這類 opinionated 功能；需要時用 extensions、skills、npm 上的 Pi
packages 自己長出來，或請 Pi 幫你寫 extension。

## 主要 Features

### 模組化 monorepo

把「叫 model」「跑 agent loop」「持久化對話」「畫 TUI」「寫 CLI」拆成可
單獨引用的套件：`pi-ai`（統一多 provider API）、`pi-agent-core`（工具與
狀態）、`pi-durable`（可恢復的 conversation／task runtime）、`pi-tui`、
`chord`（composition／RPC／replicated state）等。coding-agent 是其中一個
預設組裝，不是唯一入口。

### Extensions（可執行擴充）

TypeScript extension 在 Pi **同一 process** 內執行，可註冊 tools、`/`
commands、lifecycle hooks、shortcut、session state、甚至改 UI。適合需要
攔截危險指令、接第三方 API、或把 workflow 變成程式行為的場合；信任邊界
等同於使用者本機權限。

### Skills（按需載入的指令包）

實作 [Agent Skills](https://agentskills.io/specification) 規格：啟動時只
把 skill 名稱與 description 放進 system prompt，模型在任務匹配時才讀完整
`SKILL.md` 與附檔。比 extension 輕，適合「更多 context、不必新 executable
integration」的工作流；也可用 `/skill:name` 強制載入。

### 多種操作介面

同一 harness 可互動 TUI、非互動 print／JSON、RPC 遠端控制，或以 SDK 建
app。官方以 [OpenClaw](https://github.com/OpenClaw/OpenClaw) 作為整合範例。
Slack／chat 自動化在姊妹 repo `earendil-works/pi-chat`（尚未確認細節）。

### pi-durable（實驗性持久 runtime）

對話、model turn、tool call 在 **顯示給使用者之前** 先 commit 到 storage；
process 中途掛掉可從 storage 續跑。概念包含 conversation fork、task graph、
child tasks、compaction、handoff 等，偏向「可觀測、可恢復的 agent 後端」，
與單次 CLI session 互補。

### 沙箱與權限（外掛，非內建）

Pi **沒有** 內建 filesystem／network／credential 的 permission 模型，預設
等同啟動它的使用者 OS 權限。文件建議用 Gondolin micro-VM extension、
Docker 整包跑、或 OpenShell 等政策沙箱補強。

## 主打賣點

- **Harness，不是全家桶**：預設夠用，進階能力用 extension／package 生態
  擴充，避免把每種 workflow 都 bake 進 core。
- **Library-first**：多 provider、agent loop、durable state、TUI 都可被其他
  產品引用，coding CLI 只是 showcase。
- **Skills 規格互通**：與 Agent Skills 生態對齊，和 Cursor／其他 agent 的
  skill 目錄思路相近，但 Pi 自己管 runtime 與載入時機。
- **供應鏈與安裝路徑刻意保守**：pi.dev installer 與 install-lock 釘 transitive
  deps、`--ignore-scripts`、審核 lockfile 變更等，定位偏「長期維護的 OSS
  agent 基礎設施」。

相對常見 coding agent（內建 plan／sub-agent UI 的 IDE 或託管產品），Pi 更
像 **可塑形的 runtime + 終端 front-end**；plan mode 若存在，多半是 community
package 或自己 extension，而非產品預設畫面。

## 使用情境

### 自訂 coding agent 產品

- 適合誰：要在自家 app、CI 或內網跑 agent，又不想從 raw LLM API 重寫 loop。
- 在什麼情況使用：需要統一 Anthropic／OpenAI／Google 等 provider，並控制
  tools、hooks、輸出格式（JSON／RPC）。
- 帶來的價值：重用 pi-ai + agent-core（或 pi-durable），專注在 UX 與政策，
  而不是 token streaming 與 tool 協定。

### 個人／團隊終端工作流

- 適合誰：習慣 CLI、想組 npm 可分享的 extensions／skills／themes。
- 在什麼情況使用：repo 內 `.pi` 或使用者目錄放 project skills，用 `/`
  commands 固化 review、deploy、或內部工具。
- 帶來的價值：比「只改 system prompt」更可控；比寫完整 IDE 插件更輕。

### 需要 crash-safe 或長時間任務的 backend

- 適合誰：建 multi-step agent 服務、要 fork conversation 或 task graph。
- 在什麼情況使用：pi-durable + storage backend，前端用 watch API 或自建 UI。
- 帶來的價值：「先持久化再展示」對齊 workspace 裡 progress／handoff 的
  可靠性需求（API 仍標 experimental）。

## 我們可以學什麼

- 值得借鑑的 product idea：
  - **Core 瘦、生態胖**：sub-agents／plan 不進 default，降低 core 與 UI 耦合。
  - **Extension vs Skill 分層**：executable integration 與 lazy-loaded
    instruction bundle 分開，對應 workspace 裡「員工能力」vs「SOP 文件」。
  - **Durable commit-before-show**：進度與 transcript 以 storage 為真，UI 只是
    projection；適合 multi-tab／multi-human 觀看同一任務。
- 值得借鑑的 interaction / workflow：
  - Lifecycle hooks（`before_agent_start`、`agent_before_settle`）作為 human
    gate 或外部系統同步點。
  - `/skill:name` 強制路由 vs 模型自行選 skill——workspace 可做成「主管指派
     playbook」與「員工自選技能」兩種委派。
  - RPC／JSON 模式讓 **同一 agent** 可被 2D／3D front-end 驅動，而不綁 TUI。
- 在 2D workspace 裡會變成什麼：
  - 每個「工位」或 map 上的 agent 對應一個 pi-durable conversation 或 task
    node；平面地圖顯示 settle 狀態、tool 進行中、fork 分支。
  - Skills 變成工位旁可拖曳的 SOP 卡片；extensions 變成「裝備」改變該工位
    可用 tools（仍須標示 trust／sandbox 層級）。
  - 人類點選某工位即 attach 到 Pi TUI 或 embed panel，底層走 RPC 同步 transcript。
- 在 3D workspace 裡會變成什麼：
  - 員工 avatar 代表不同 conversation／child task；走進某張桌子 = 進入該
    harness 的 root 或 fork，聽到／看到的是 durable storage 重播 + live stream。
  - 「辦公室」不實作 permission——仍由 Docker／OpenShell 房間標示哪些 avatar
    只能在哪個 sandbox zone 執行 shell。
  - Sub-agent／task graph（若用 pi-durable）可視覺化成辦公室裡的臨時協作小組，
    而不是 ADE 側欄的 flat list。
- 不值得照搬或需要重新設計的地方：
  - Extension 與 host **同 process、同 OS 權限**——spatial workspace 必須另建
    視覺化的 trust／capability 邊界，不能假設使用者讀過 extension 文件。
  - 預設無 plan mode：2D／3D workspace 若要做「多 agent 編排敘事」，需我們
    自己的 goal／task 模型，不能等 Pi 內建。
  - TUI-first 體驗：office 場景應以 watch／RPC 為主，終端 differential render
    僅作 power-user 附屬。

## 初步看法

- 最有價值的部分：把 coding agent 拆成 **可嵌入的 runtime 套件** + 明確的
  extension／skill 擴充面；pi-durable 的 commit 語意對 multi-surface workspace
  尤其 relevant。
- 最大限制或疑問：權限與沙箱外置；pi-durable API experimental；實際 extension
  生態成熟度與 Cursor 類 IDE 的整合深度 **尚未確認**（需 hands-on 或看
  pi.dev docs 更新）。
- 是否值得進一步研究或親自體驗：值得。若我們 workspace 需要「同一 task
  多 front-end、可恢復 transcript」，應優先試 pi-durable + RPC，而非只評
  CLI TUI。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：https://pi.dev
- Repository：https://github.com/earendil-works/pi
- Documentation：https://pi.dev/docs/latest
- Related chat automation：https://github.com/earendil-works/pi-chat
- RFCs：https://rfc.earendil.com/keyword/pi/
