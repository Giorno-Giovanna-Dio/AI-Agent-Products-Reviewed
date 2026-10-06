# Paseo

> Cell ID：[https://github.com/getpaseo/paseo](https://github.com/getpaseo/paseo)
>
> Status：`untried`
>
> Category：Self-hosted multi-agent control plane
>
> Last updated：2026-10-05

## 產品介紹

Paseo 是一套自架（self-hosted）的 coding agent 控制平面。它在你的機器上跑一個
**daemon**，真正執行 Claude Code、Codex、Copilot、OpenCode 等既有 CLI agent；
桌面、手機、網頁和終端機則是連到同一台 daemon 的**客戶端**。

使用者不必把 repo 或 secrets 交給雲端 orchestrator：agent 仍在你本機或遠端
主機的 dev environment 裡跑，Paseo 負責派工、看進度、送 follow-up，以及從
手機或別台裝置遙控。官方定位是「一個介面管多 provider、多裝置、平行任務」，
而不是再包一層新的 model。

## 主要 Features

### 本機 daemon + 多 surface 客戶端

Daemon 管理 agent 行程、WebSocket API 與 MCP；Expo app（iOS／Android／web）、
Electron 桌面、CLI 和可自架的 web UI 都連同一後端。價值在於**執行環境留在
你的機器**，介面可以隨身帶走。

### Provider 抽象

同一套 workspace 與 session 模型，可切換不同 agent CLI 與 model（例如
`claude/opus` 與 `codex/gpt-5.5`）。重點是比較**完整 runtime 行為**（tools、
權限、模式），不是只換 API endpoint。

### Workspace 優先於 chat

產品以 **project → workspace → session** 組織工作：workspace 是任務容器
（含 working directory），session 是其中的 agent、terminal、browser 等分頁。
多個 session 可同時屬於同一任務，比「一條長對話」更接近實際開發節奏。

### 隔離模式（local / worktree）

Workspace 可綁既有目錄，或用 git worktree 開獨立 branch 與目錄。平行 feature
或 PR 導向的工作可以分開檔案樹，最後再 archive 時清理 worktree。

### 跨裝置連線

手機可透過 E2E 加密的 **relay**（配對 QR）、Tailscale 直連，或 SSH 轉發連到
遠端 daemon。CLI 也可對 `--host` 在另一台機器上 `run`、`attach`、`send`。

### Agent 編排（orchestration）

啟用 MCP「Paseo tools」後，執行中的 agent 可在同一 host 上啟動 subagent、
跨 provider 分工、互送 prompt、查進度、建 workspace／worktree，或使用
排程與 heartbeat 延續任務。另有 `/paseo-handoff`、`/paseo-advisor`、
`/paseo-committee` 等 skills 封裝常見編排模式。

### CLI、SDK 與插件

CLI 覆蓋 app 能力；`@getpaseo/client` 供自建整合；TypeScript 插件可擴充
主題、側欄、provider 等（插件在 daemon 與 client 環境執行，需自行信任來源）。

## 主打賣點

- **真正差異**：自架 daemon + 多 provider + 跨裝置（尤其手機遙控本機 agent），
  並把「任務容器（workspace）」放在 chat 之上；編排是 first-class（subagent
  track、MCP、CLI、skills），不是附屬功能。
- **與 Conductor 等 macOS workspace 的交集**：多 runtime、worktree、平行任務、
  人類 review diff——但 Paseo 更強調**遠端觸達**與**headless／Docker／多 host**，
  而不是單一桌面 ADE。
- **包裝 vs 新能力**：多 provider UI、語音輸入、無 telemetry 是體驗與政策；
  workspace／subagent／relay 模型才是影響 architecture 的核心。

## 使用情境

### 離開書桌仍要推進 agent 任務

- 適合誰：長時間跑 agent、又不想綁在 terminal 前的人。
- 在什麼情況使用：本機 daemon 跑 implementer，通勤時用手機看輸出或送
  follow-up。
- 帶來的價值：執行仍在本機環境，控制面移到 mobile／web。

### 一 agent 派工給另一 provider

- 適合誰：想用 Claude 規劃、Codex 實作，或獨立 reviewer 不改檔的人。
- 在什麼情況使用：高風險改動、需要第二意見或平行研究。
- 帶來的價值：subagent 在同一 workspace 可見，主 agent 完成後收到通知，
  人類可隨時介入任一 session。

### 遠端 dev box 或 homelab 上的 agent

- 適合誰：在 VPS、NAS 或公司工作站跑 agent 的進階使用者。
- 在什麼情況使用：CLI／Docker 起 daemon，Tailscale 或 SSH 從筆電連入。
- 帶來的價值：算力與 repo 在遠端，操作體驗仍像本地 app。

## 我們可以學什麼

- **值得借鑑的 product idea**：把 **daemon（執行與狀態）** 和 **client（呈現與
  輸入）** 拆開；用 **workspace** 當任務的 stable boundary，session 只是
  裡面的活動；subagent 是同一任務下的分工，不是另一個無關 chat。
- **值得借鑑的 interaction / workflow**：Subagents track 讓編排可見；agent
  profile +「When to use」讓 orchestrator 選專長；人類可在任一 tab attach、
  stop、送指令——控制權始終可收回。
- **在 2D workspace 裡會變成什麼**：俯視或樓層圖可畫成 **project 區塊**，
  每個 **workspace 是一間任務室**，裡面的 **session tabs** 是並排工位
  （implementer、reviewer、terminal）；**不同 host** 是不同建築或機房節點；
  手機使用者像在地圖上點選房間看 live feed，而不是另開一套雲端 IDE。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室裡，**daemon 所在機器**
  是一棟樓；走進某 project 的 workspace 房間，看見數位員工（各 provider／
  profile）坐在工位；**subagent** 像從主座席站起來到隔壁桌協作；你在手機
  上像遠端主管進大門（relay 配對），走到房間口看進度、插話或叫停——空間
  表達「誰在同一任務、誰是派出去的下屬」，不是 3D 模型展示。
- **不值得照搬或需要重新設計的地方**：插件與 MCP 工具預設關閉是合理的
  安全預設，但對新手可能隱藏編排能力；relay 便利但增加信任面（雖宣稱
  E2E）；我們若做 2D／3D workspace，需決定是否也要「自架 daemon」或改為
  團隊共用 runtime——Paseo 偏個人／小團隊自管機器，不是多租戶 SaaS 模型。

## 初步看法

- 最有價值的部分：workspace 容器 + 跨裝置連 daemon + 可見的 multi-agent
  編排，和本 repo 研究的「人如何委派、比較、介入」高度相關。
- 最大限制或疑問：尚未實際安裝；license 在 GitHub 顯示為 mixed／NOASSERTION，
  商用前需自行確認；與 Conductor、Cursor 等原生 ADE 的 UX 深度差異需
  hands-on 才判斷得準。
- 是否值得進一步研究或親自體驗：值得；優先驗證 workspace／subagent 流程
  與 relay 連線，不必先測完所有 provider 與插件。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：https://paseo.sh
- Repository：https://github.com/getpaseo/paseo
- Documentation：https://paseo.sh/docs（Getting started、Workspaces、Orchestration、Connectivity）
