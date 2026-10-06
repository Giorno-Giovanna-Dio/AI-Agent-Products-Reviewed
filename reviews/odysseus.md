# Odysseus

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-odysseus-cell-7aa3/reviews/odysseus.md)
>
> Cell ID：[https://github.com/odysseus-dev/odysseus](https://github.com/odysseus-dev/odysseus)
>
> Status：`untried`
>
> Category：Self-hosted unified AI workspace
>
> Last updated：2026-10-06

## 產品介紹

Odysseus 是一套可自架的 **AI 工作空間**：在自家機器或 Docker 裡跑一個
Web 應用，把聊天、agent、本機或 API 模型、研究、文件編輯、郵件、筆記、
待辦與日曆收進同一介面。官方定位是「你的模型、你的硬體、你的資料」，
而不是再訂一個雲端 super-app。

典型用法是 `docker compose up`（或原生 Python）後在瀏覽器開 UI，用
Settings 設定 provider 與整合；具 **admin** 身分的使用者可開 shell、
讀寫檔案、管 MCP、跑 Cookbook 下載／serve 模型等。產品自比 **admin
console**：能力大、預期部署在受信任的內網，不是對公網開放的 SaaS。

## 主要 Features

### Chat 與 Agent 模式

支援本機與遠端 API 模型，agent 可走 tools、MCP、檔案、shell、skills 與
memory。威脅模型文件把 agent 視為高權限：非 admin 預設擋掉 shell、
檔案、郵件、MCP 等；admin session 則接近完整機器控制。對 workspace
設計來說，這是 **單一 UI 裡的通用 agent runtime**，不是專為 git worktree
coding 編排而生。

### Cookbook（模型發現、下載與 serving）

依硬體建議模型、處理下載與背景 serve（含 GPU overlay、遠端 SSH server、
可選 Docker 管 daemon）。讓「選模型 → 跑起來 → 在 Chat 裡用」留在同一
產品內，降低自架 LLM 的碎片設定。Roadmap 也指出小 context 模型上的
prompt／tool 膨脹是實際痛點。

### Deep Research 與 Compare

Deep Research 做多步網路研究、讀來源、產報告；Compare 做盲測並排試模型
再綜合。兩者把 **研究與評估** 當一級功能，而不只是 chat 的附加 prompt。

### Documents、Notes、Tasks 與 Calendar

文件編輯器偏寫作與 AI 修訂；Notes／Todos 可與 agent 互動（Roadmap 還想
加「指派 todo 給 agent」）；Calendar 含 CalDAV 與 **排程 agent 任務**。
這把「知識與時間」和對話綁在同一資料目錄（`data/`），不是外掛 Notion。

### Email 與生活整合

IMAP/SMTP 收件、分類、摘要、提醒與回覆草稿。自架 workspace 少見把
**信箱** 和 agent 放在同一 trust boundary；也代表 prompt injection 面
（郵件、網頁、memory）被官方明確列為安全議題。

### 自架執行與 bundled 服務

Docker Compose 預設綁 localhost，常 bundled ChromaDB、SearXNG、ntfy 等；
認證、2FA、角色權限與 internal tool loopback 在 THREAT_MODEL 有清楚
說明。已知缺口包含 **無 shell 沙箱**、部分 SSRF 修復進行中——部署者
需當成特權控制台看待。

## 主打賣點

- **真正差異**：一個 AGPL 自架包裡同時有 **agent 工具鏈、本機模型
  Cookbook、研究生產線、文件／生產力模組與郵件**，目標是「個人或小團隊
  的 AI 基地台」，不是只做 coding ADE 或只做 chat UI。
- **與 Paseo／Conductor 等對照**：後者偏 **多 provider coding agent
  編排與 worktree**；Odysseus 偏 **生活＋工作合一的 Web 控制台**，agent
  是其中一條主幹，旁邊還有 research、compare、email、calendar。
- **包裝 vs 新能力**：主題、gallery、語音等是體驗層；**Cookbook +
  排程 agent 任務 + untrusted content 包裝政策** 才是影響 architecture
  的核心（資料與工具都在同一 process／volume）。

## 使用情境

### 想完全自管資料與模型的人

- 適合誰：不想把 mail、筆記、對話都散在不同雲端服務的進階使用者。
- 在什麼情況使用：homelab 或本機 Docker 起 Odysseus，API key 與本機
  權重留在 `data/`。
- 帶來的價值：一個 URL 處理 chat、research、文件與信箱，模型可選 API
  或 Cookbook serve。

### 本機 GPU 上試模型與做研究

- 適合誰：有 NVIDIA／AMD 或願意用 CPU serve 的 hobbyist。
- 在什麼情況使用：Cookbook 依 VRAM 建議模型 → Deep Research 或 Compare
  評估品質。
- 帶來的價值：研究與 serving 不用另開 ComfyUI + 另一套 chat 前端（但
  整合深度尚未在本 repo 驗證）。

### 小團隊內網共用 AI 控制台

- 適合誰：信任邊界清楚的 LAN／Tailscale 小組。
- 在什麼情況使用：admin 開工具與 MCP，一般使用者僅 chat／文件等
  受限能力。
- 帶來的價值：角色分離比「所有人 SSH 同一台機器」更易理解；仍須自行
  處理暴露面與備份。

## 我們可以學什麼

- **值得借鑑的 product idea**：把 **workspace 當「模組化大廳」**（chat、
  research、docs、mail、calendar 共用同一使用者、同一 memory／Chroma、
  同一模型設定）；**Cookbook** 當 runtime 子系統，而不是 Settings 裡
  一行 API URL；**排程 agent 任務** 把「之後再跑」從 cron 腳本收進 UI。
- **值得借鑑的 interaction / workflow**：THREAT_MODEL 的 **admin vs
  non-admin 工具閘**、**untrusted context 包裝**（搜尋、郵件、skill 當
  data 而非 system）；Compare 的盲測流程可映射到「多 runtime 並排驗收」；
  Deep Research 的步驟化報告適合在 workspace 裡做成 **可追蹤的任務
  pipeline**（每步 artifact 可 review）。
- **在 2D workspace 裡會變成什麼**：俯視 **一棟自架總部**：中央是 Chat／
  Agent 工位；左翼 Research 室（Deep Research 流水線）；右翼 Document
  與 Notes 書架；樓下 Mailroom 與 Calendar 牆；地下室 **Cookbook 機房**
  顯示 GPU、serve 狀態與模型佇列。使用者拖任務到「晚間排程」格，像
  在平面地圖上排 agent 班次，而不是 ADE 側欄的一串 tab。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室裡，**admin** 像持
  鑰匙的館長，能進機房（shell、MCP）；**訪客帳號** 只能在會議室聊天、
  看文件。Deep Research 像研究小組在會議室白板前輪流讀來源；Compare
  像隔音評測間裡兩個「模型員工」盲測答題；郵件室 agent 整理 inbox
  但不自動外寄除非人類確認——空間表達 **模組與權限**，不是 3D 圖表炫技。
- **不值得照搬或需要重新設計的地方**：無 sandbox 的 shell／檔案工具與
  「像 admin console」定位，和我們若要做 **可協作、可審計的 2D／3D
  office** 可能衝突——我們可能需要更細的 task sandbox 與 provenance；
  功能面過廣（mail + calendar + gallery）對 **pure coding workspace**
  可能是噪音；AGPL 與 bundled 服務運維成本需單獨評估。Agent prompt
  對小 context 過重是官方承認問題，借鑑時應預留 **分層 context 與
  tool 精簡** 策略。

## 初步看法

- 最有價值的部分：自架場景下 **「模型生命週期 + agent + 研究生產力」**
  一體化的產品形狀，以及文件化清楚的 **角色／注入面** 安全思考。
- 最大限制或疑問：是否真能在長期維護下讓 email、CalDAV、Cookbook 各
  平台都可靠；與我們聚焦的 **multi-agent coding orchestration** 重疊
  有限；尚未確認實際 UI 在大量 session／任務下的可觀測性。
- 是否值得進一步研究或親自體驗：若雷達關心 **self-hosted 統一 workspace
  與 local model ops**，值得 Docker smoke test；若只關心 git 平行 agent，
  優先級可低於 Orca／Paseo 一類 ADE。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：https://odysseus-dev.github.io/odysseus/
- Repository：https://github.com/odysseus-dev/odysseus
- Documentation：https://github.com/odysseus-dev/odysseus/blob/main/website/setup.md、https://github.com/odysseus-dev/odysseus/blob/main/THREAT_MODEL.md、https://github.com/odysseus-dev/odysseus/blob/main/ROADMAP.md
