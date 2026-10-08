# OpenClaw

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openclaw-cell-7aa3/reviews/openclaw.md)
> - [Pages 閱讀器](https://giorno-giovanna-dio.github.io/AI-Agent-Products-Reviewed/#/reviews/openclaw.md)（`main` 部署後）
>
> Cell ID：[https://github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
>
> Status：`untried`
>
> Category：跨平台個人／團隊 AI assistant（Gateway + channels）
>
> Last updated：2026-10-06

## 產品介紹

OpenClaw 是開源的 **AI 助理 runtime**：在你自己的電腦或伺服器上跑一個長駐的
**Gateway**，把模型、工具、記憶與各種通訊 **channel**（WhatsApp、Telegram、
Slack、Discord、iMessage 等二十多種，外加 macOS／iOS／Android／Windows／Linux
原生 app）接成同一套助理。使用者多半在**已經在用的聊天 app** 或 Control UI／CLI
裡下指令，由 agent 代為執行 shell、排程、裝置節點動作等「真的會動手」的工作。

定位是 **your machine, your rules**：狀態、記憶與憑證預設留在本機；Claude Code、
Codex、Copilot、本地模型等以 **plugin／agent runtime** 形式接入，而不是綁死單一
vendor。個人筆電與團隊共用 Gateway 在文件上被描述為**同一套產品、只差設定**，
由 OpenClaw Foundation（501(c)(3)）治理，MIT 授權、無付費 tier 或官方代管服務。

本筆記僅依 GitHub README 與公開 docs 整理，**尚未實際安裝或操作**。

## 主要 Features

### Gateway 控制平面

單機一個 Gateway daemon 持有 channel 連線、session、工具事件與 WebSocket API；
CLI、Web Control UI、TUI、macOS app 等都以 client 連上 Gateway。價值是把
「誰能連線、誰能發 agent run、事件怎麼推播」集中在一個可設定、可審計的控制點，
而不是每個 channel 各跑一個 bot 行程。

### 多 channel、同一助理

WhatsApp、Telegram、Slack、Discord、Signal、Google Chat、Matrix、IRC 等由
Gateway 統一管理；WebChat 與遠端可經 SSH／Tailscale 等與其他 client 共用隧道。
對使用者而言，助理出現在**既有通訊習慣**裡，而不是強迫換到單一 IDE 或網頁 chat。

### 可替換的 agent runtime 與工具層

模型 provider、Codex／Claude Code／Copilot 等 **vendor harness**、內建 agent loop、
MCP client／server、skills（AgentSkills）、slash commands 與 plugin SDK 分層擴充。
OpenClaw 保留 channel、session、policy 與 state，把「模型怎麼跑一輪 turn」交給
可插拔 runtime——適合想換模型或 harness 但不想重接所有通訊端的人。

### Nodes 與裝置能力

macOS／iOS／Android／headless **node** 以 `role: node` 連同一 WebSocket，提供
camera、螢幕錄製、定位、Canvas 等裝置端命令（需配對與核准）。這把「在聊天裡
下指令」延伸到**實體裝置與桌面能力**，而不只限於 Gateway 主機上的 shell。

### 安全與 policy（文件主張）

架構敘述強調 **trusted Gateway / untrusted execution**：sandbox、node、雲端 worker
可與 Gateway 憑證分離；工具拒絕與 exec 核准以**程式 enforced policy** 為主，而非
只靠 system prompt。DM channel 預設對未知發送者 pairing；sandbox 預設關閉、需
自行硬化——文件有明確說明，**我們未驗證實際部署行為**。

### 自動化、儀表板與互通協定

排程（cron）、session dashboard、A2UI widget、OpenTelemetry／Prometheus、
OpenAI-compatible HTTP API（預設關閉）、A2A、Agent Client Protocol 等，偏向
**可觀測、可整合**的 agent 基礎設施，而不只是單一聊天 UI。

## 主打賣點

- **「Any OS, any platform」的助理入口**：channel 廣度 + 原生 app + 自架 Gateway，
  差異在「助理跟著你的訊息 app 與裝置走」，不是另一個 ADE 分屏 IDE。
- **資料與治理敘事**：本機 state、可選 telemetry、Foundation 簽 release、plugin 化
  核心——對比單 process harness「整包在同一 OS user」的模型，賣的是**可分割的信任
  邊界**（實際安全仍取決於設定）。
- **Harness 嵌入而非只做 API 轉發**：可驅動 Codex app-server、Claude Code stdio 等
  **原生 lifecycle**，OpenClaw 管 channel 與 policy；這比「只接 chat completion API」
  更接近「整個助理產品」。
- **舊能力的新包裝**：MCP、skills registry（ClawHub）、多 model provider 在業界已
  常見；OpenClaw 的整合深度與 channel／Gateway 一體化才是主要差異，不是發明新
  協定名詞。
- 與本 repo 的 [Paseo](paseo.md)、[Orca](orca.md) 等相比：後者偏 **coding agent
  控制平面／ADE**；OpenClaw 偏 **生活與工作通訊裡的 proactive 助理**，coding 是
  能力之一而非唯一 workspace 隱喻。

## 使用情境

### 個人：在 Telegram／WhatsApp 遠端吩咐本機做事

- 適合誰：希望助理在手机上可及、但執行與資料留在自己機器的人。
- 在什麼情況使用：排程、查 mail、跑 script、透過 node 拍照或螢幕等（依已啟用
  tools 與 channel 設定）。
- 帶來的價值：減少「人坐到電腦前才開始 agent session」的摩擦；代價是必須認真處理
  DM pairing 與 tool 權限。

### 小團隊：共用 Gateway、同一 session 多 human

- 適合誰：文件所述可共用 Gateway 的團隊（例如內部 dogfood `team.openclaw.ai`）。
- 在什麼情況使用：多人 credit、history 與 channel 接入同一部署。
- 帶來的價值：channel 與 policy 集中管理；**多人邊界與審計細節尚未確認**（需讀
  enterprise／teams 文件與實際設定）。

### 整合者：把 OpenClaw 當 agent 後端

- 適合誰：已有 OpenAI client、MCP 生態或 A2A peer，需要可自架的 session／tool 閘道。
- 在什麼情況使用：啟用 Gateway HTTP API、MCP server、plugin 自訂 channel。
- 帶來的價值：協定與 plugin 面廣；維運與升級（versioned state、migration）需納入
  平台成本。

## 我們可以學什麼

- **值得借鑑的 product idea**：以 **Gateway** 作為 workspace 外的「總機」——session、
  channel 身份、agent run、node 能力、排程與 audit 都掛在 runId／session 上，human
  從任意 surface 介入同一條任務線，而不是每個 UI 各複製一份 bot。
- **值得借鑑的 interaction / workflow**：**streaming agent 事件**（accept → 事件流 →
  final summary）適合在 workspace 裡做「進行中／可中斷／可追問」的統一進度模型；
  pairing 與 **fail-closed approval** 可對應 workspace 裡「誰能派工給哪個 agent」
  的可視化核准佇列。
- **在 2D workspace 裡會變成什麼**：平面地圖上可畫 **Gateway 機房**（channel 連線、
  health）、**進行中的 agent run**（附來源 channel 圖示）、**待核准 tool exec** 與
  **已配對 node**；human 點某一 run 可看到跨 Telegram／Control UI 的同一 thread 狀態，
  像總機室而非單一 chat tab。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室裡，**接待／總機**角色代表 Gateway
  policy，各 **channel 入口**像不同門；agent 員工在「執行區」（sandbox／node）做事，
  人從任何門進來下指令時，同一員工抬頭顯示 run 狀態——強調 **誰在哪裡執行、誰批准
  危險工具**，不是 3D 圖表。
- **不值得照搬或需要重新設計的地方**：channel 爆炸與 plugin 面過廣，在 2D／3D
  workspace 應抽象成 **少數原語**（派工、核准、run、artifact），避免把 ClawHub／
  每個 channel 都做成一個 3D 物件；OpenClaw 偏 **助理＋通訊**，若我們主軸是 **parallel
  coding worktree**，應借 Gateway／policy 思路，而非複製「在 WhatsApp 裡寫 code」的
  完整 UX。

## 初步看法

- 最有價值的部分：把 **multi-surface 助理** 收斂到單一 Gateway + 可替換 runtime，
  並在文件層把 trust boundary 講清楚，適合當「agent 基礎設施」標本研究。
- 最大限制或疑問：預設 sandbox 關閉、設定面大；**實際 task 執行品質、computer use
  深度、團隊 RBAC 體感** 皆未 hands-on；極高 star 數不代表已驗證我們關心的 workspace
  編排問題。
- 是否值得進一步研究或親自體驗：若我們要設計 **human 從手機／聊天介入長任務** 或
  **policy-gated tool exec**，值得在 disposable 環境試裝 Gateway + 單一 channel；
  若只關心 IDE 內 parallel agents，優先級低於 Orca／Conductor 類 ADE。

## Sources

- Official website：https://openclaw.ai
- Repository：https://github.com/openclaw/openclaw
- Documentation：https://docs.openclaw.ai（Getting started、Gateway、Why OpenClaw、Architecture）
- Discovery：GitHub starred scan
