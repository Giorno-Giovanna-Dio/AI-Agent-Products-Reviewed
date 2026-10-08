# Octop

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-cell-7aa3/reviews/octop.md)
> - [Pages 閱讀器](https://giorno-giovanna-dio.github.io/AI-Agent-Products-Reviewed/#/reviews/octop.md)（`main` 部署後）
>
> Cell ID：[https://github.com/TencentCloud/Octop](https://github.com/TencentCloud/Octop)
>
> Status：`untried`
>
> Category：Self-hosted multi-user multi-agent assistant
>
> Last updated：2026-10-06

## 產品介紹

Octop 是騰訊雲開源、可完全自架的 AI 助理平台，定位偏向**家庭與小團隊**：
一位管理員、多位使用者，每人可養多個「專家（expert）」agent，並在 Web
控制台、桌面客戶端、CLI，以及飛書、釘釘、QQ、微信、Telegram、Discord、
企微等 IM 通道上同一套對話。核心承諾是資料與憑證留在本機（預設
`~/.octop/`），用單一行程 `octop run` 同時承載控制台、通道橋接與排程，
重啟後由控制面資料庫還原狀態，而不是依賴外部 message broker。

使用者先 `octop init` 完成多使用者 JWT 與模型設定，再為不同情境建立專家
（各自工作區、模型、通道、cron），或組 **AgentTeams**（Beta）讓「主持人」
專家調度其他成員。需要寫程式時可透過 **ACP** 雙向連 IDE／OpenCode／
Claude Code；需要上網或 GUI 時則用 Browser AI+、瀏覽器內 Terminal AI+ 或
遠端桌面。整體比「coding ADE」更像**可共享的數位助理後台**，IM 與排程
是 first-class 入口，而不是附屬通知。

## 主要 Features

### 單進程控制面 + HarnessProcessor

Web UI、IM、自然語言 cron 都走同一個 in-process `HarnessProcessor`，
底層是 [Octop Harness](https://github.com/TencentCloud/octop-harness)
（模型路由、tools、skills、對話 checkpoint）。好處是部署簡單、重啟可重建
狀態；代價是 heavy 平行度與故障隔離仍綁在同一 process（細節需 hands-on
才評估）。

### 多使用者與專家（Expert）模型

JWT 多使用者、admin 角色；每位使用者可有多個 expert，各自 workspace、
provider、通道與 cron。內建 expert library／market、專家分享與**共享 skill／
sub-agent 池**，減少重複配置。16 種 MBTI 人設模板只是 UX 包裝，但反映
產品想讓「不同個性的專職 agent」可被辨識與切換。

### AgentTeams（Beta）— 主持人調度

團隊以 `kind=team` 的主持人 expert 為入口：真群聊時間線、主持人並行
`dispatch` 成員、成員跑完回叫主持人總結。通道只綁主持人；成員仍是獨立
expert，可加入多個團隊。設計文件明確限制主持人工具集（偏 `agent_list`、
`ask_agent`），專業活交給成員——接近 Maestro Group Chat／moderator，但綁在
自架 IM + 工作區模型上。

### 可插拔 workspace 後端

Expert 檔案可存本機、Docker sandbox、PostgreSQL、COS／S3 等，與控制面 DB
（SQLite WAL 或 PostgreSQL）分離。記憶由 [Octop Memory](https://github.com/TencentCloud/octop-memory)
隨 workspace 搬移，配合知識庫 RAG 可在同一 deployment 內共享語料。

### 通道、Connectors 與 MCP

[Octop Gateway](https://github.com/TencentCloud/octop-gateway) 把多 IM 平台
正規化成同一訊息管線。Connectors（OAuth + MCP）擴充騰訊系與第三方資源
邊界，讓 agent 在核准後存取外部服務，而不是只靠內建 tools。

### 多 surface：Web、桌面、CLI、API

React 控制台涵蓋 chat、teams、connectors、channels、cron、knowledge、plugins；
另有 Windows／macOS／Linux 桌面與 FnOS 套件。CLI 含 `octop run`、`octop chats`、
`octop acp`；HTTP／SSE／WebSocket 供程式整合。

### ACP 雙向與外部 coding agent

Inbound：`octop acp` 當 stdio ACP server，讓 Zed、OpenCode 等使用**你的**
Octop agent。Outbound：在 chat 委派 OpenCode、Claude Code、Codex 等 runner，
並有 permission gate——Octop 當編排與政策層，coding runtime 仍在外部 CLI。

### Browser AI+、Terminal AI+、遠端桌面

Headless Chromium（[Octop Browser](https://github.com/TencentCloud/octop-browser)）
做網頁自動化；控制台內 shell 與遠端桌面（含 headless Linux 一鍵隔離桌面）
把「GUI／終端機任務」收進同一 dashboard，而不必另開 ADE。

### 安全與治理（文件宣稱）

工具核准、shell guardrail、PII redaction、多使用者隔離等寫在產品敘事裡；
尚未在本 Cell 實測，實際邊界以配置與版本為準。

## 主打賣點

- **真正差異**：自架、**多使用者 household／小團隊** + **IM 原生** +
  **專家／團隊** 產品語言，再疊 Connectors、知識庫、Browser／桌面／cron，
  形成「生活與工作助理後台」，不是只做 repo worktree 的 coding ADE。
- **與 Paseo、Maestro、Orca 的交集**：都強調自架或本機執行、多 agent、
  可編排；Octop 更偏**通道與角色化 expert**，coding 委派走 ACP outbound，
  而不是以 git worktree 為中心 UI。
- **包裝 vs 新能力**：MBTI、expert market 行銷感較強；**Harness 單進程
  統一管線**、**workspace 後端可插拔**、**AgentTeams 主持人模型**、
  **IM Gateway + 多 surface** 才是 architecture 層的學習點。

## 使用情境

### 家庭共用一台自架助理伺服器

- 適合誰：希望資料不出家、但家人各自有助理與記憶的人。
- 在什麼情況使用：NAS 或家用 PC 跑 `octop run`，成員用 Web／桌面或微信等
  通道提問；admin 分配 expert 與知識庫分享範圍。
- 帶來的價值：隱私與成本可控，且 IM 入口降低「開控制台」門檻。

### 小團隊 IM 裡派工給專家隊伍

- 適合誰：已在飛書／釘釘／企微協作、想加 AI 但不願把對話送到 SaaS 的團隊。
- 在什麼情況使用：綁定通道到主持人或單一 expert，複雜需求開 AgentTeams，
  由主持人拆派給研究、寫稿、查資料等成員。
- 帶來的價值：任務從群聊進來、結果回到同一時間線，人類仍從 IM 監督。

### 開發者：Octop 當政策層，ACP 當 coding 手

- 適合誰：已用 Claude Code／OpenCode，但想要統一記憶、排程、知識庫與工具核准。
- 在什麼情況使用：日常在 Octop chat 規劃；實作委派 ACP runner；必要時用
  Terminal AI+ 或遠端桌面排查環境。
- 帶來的價值：coding agent 不換掉，但 orchestration 與資料留在自架 stack。

## 我們可以學什麼

- 值得借鑑的 product idea：把 **deployment** 當「一戶多人的助理機房」；
  **expert** 是可分享、可複製的配置單元（workspace + persona + channels +
  cron）；**AgentTeams** 用「主持人輕工具 + 成員重工具」避免單 agent 膨脹。
- 值得借鑑的 interaction / workflow：IM 與 Web **同一 Harness 管線**；
  群聊時間線用 `speaker_agent_id` 標誰在說；cron 用自然語言定義 proactive
  推播；Connectors 把 OAuth／MCP 當擴充邊界而非硬編 API 清單。
- 在 2D workspace 裡會變成什麼：俯視「一棟自架大樓」— 每層一位使用者，
  房間是 expert，會議室是 AgentTeams 群聊牆；通道門口（飛書／Telegram）有
  訊息流入動畫；cron 是定時亮起的任務燈；Browser／遠端桌面是附屬工位。
- 在 3D workspace 裡會變成什麼：可走進的辦公室— 管理員在機房區，每位使用者
  有自己的隔間與 expert「同事」（MBTI 只影響外觀與對話風格）；主持人站在
  圓桌派工，成員回桌完成後走回主持人匯報；IM 入口像接待櫃台把訪客導到
  正確房間。
- 不值得照搬或需要重新設計的地方：單 process 對超大團隊與高併發的極限
  尚未確認；中國 IM 與騰訊 Connectors 生態綁定深，其他地區要重想通道優先
  序；AgentTeams 仍 Beta，不宜直接當唯一編排模型；把 dashboard 當 workspace
  時要刻意做 spatial 隱喻，否則只是另一個 ADE 側欄。

## 初步看法

- 最有價值的部分：自架多使用者 + expert／team 抽象 + IM Gateway 一條管線，
  對「 household／小團隊助理」比純 coding ADE 更完整；ACP 與可插拔 workspace
  後端讓 execution 可分層。
- 最大限制或疑問：單機部署下的效能與故障域、工具核准 UX 實際嚴格度、
  AgentTeams 與分享模型在邊界案例（成員離線、在途派工）的行為需實測。
- 是否值得進一步研究或親自體驗：值得——尤其若我們要做**多使用者 2D／3D
  office** 且需要 **channel-first handoff**；可與 Paseo（daemon 控制平面）、
  Maestro（moderator 編排）對照編排語意。

## Sources

- Official website：https://octop.cloud
- Repository：https://github.com/TencentCloud/Octop
- Documentation：https://github.com/TencentCloud/Octop/tree/main/docs（含 `expert-teams.md`、`acp.md`）
- Related runtime：https://github.com/TencentCloud/octop-harness · https://github.com/TencentCloud/octop-gateway · https://github.com/TencentCloud/octop-memory
