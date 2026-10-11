# iPolloWork

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ipollowork-cell-c2d2/reviews/ipollowork.md)
>
> Cell ID：[https://github.com/Devin-AXIS/iPolloWork](https://github.com/Devin-AXIS/iPolloWork)
>
> Status：`untried`
>
> Category：多引擎桌面 Agent 工作台（ADE）
>
> Last updated：2026-10-09

## 產品介紹

iPolloWork 是 Different AI 推出的桌面工作台，官方把自己放在「人和 agent 團隊共用、本機優先」的位置。預設執行引擎是 OpenCode sidecar；同一個專案裡的 agent 也可以改綁 Codex Harness 或 DeepSeek Harness。使用者在 Electron app 裡開專案、派任務、看進度，並接著改程式、文件、簡報、設計和影片。

它把各引擎的執行收成同一條 engine 邊界，用來顯示進度、任務和檔案。規劃、子代理、檢查和最後結果仍留在該引擎裡。雲端（iPolloCloud）處理身分、組織、權限和托管 worker；文件寫明本機可以不登入就用。我們沒有實際打開這個 app。

今天的畫面是側欄、對話、看板、日曆、設定和編輯器，屬於 ADE 控制台。

## 主要 Features

### 一個專案、多個引擎

內建引擎 ID 是 `opencode`（預設）、`deepseek-harness`、`codex-harness`。每個專案 agent 可以各自綁引擎、模型和模式（auto、plan、execute）。對話可以選工作範本：範本提供例子、Skills 和驗收條件，引擎自己決定怎麼做。型別註解寫明，iPolloWork 只顯示原生執行和檔案，回合結束後不會在客戶端再跑第二套交付流程。

這樣人可以在同一個專案裡混用引擎，而不必為每個 runtime 重做專案、任務和成品。

### 專案裡的人和 agent

專案設定包含目標、最多 24 個 agent（角色、prompt、skills、plugins），以及 agent 之間的 dependency 或 parallel 關係。工作項目有優先順序、期限和負責人。預設狀態分組是 planned、ready、running、review、done、failed。畫面上有看板、日曆和儀表（任務健康、用量、編排圖）。編排圖是節點圖，表示誰等誰、誰可以同時做。

任務和角色因此有一個共用的專案視圖，而不是散在各引擎自己的 chat 裡。

### 同一套插件生命週期

插件套件可以帶 portable skills、MCP、commands、agents、本機服務、介面和授權。安裝、啟用、更新、卸載走同一份清單。規格寫明會把同一套套件投影到 OpenCode 和 DeepSeek Harness；引擎專用程式只在 manifest 宣告的地方跑。Codex 是否吃同一套投影，尚未確認。

DeepSeek Harness 使用者也可以把 Design、PPT、Video 視圖裝進 DSH 的 Web UI。那是給外部 host 的薄外掛，桌面工作台本身仍在 iPolloWork。

### 生成之後還能改

工作種類包含 general、document、development、research、design、video。Session 會記下聊天紀錄以外的檔案。介面裡有 Markdown 編輯、試算表、設計畫布（可調版面與顏色，並匯出 PPTX），以及影片分鏡和相關面板。官方也提到網站。設計畫布改的是生成出來的版面。和 [Onlook](https://github.com/onlook-dev/onlook) 相比，Onlook 改的是正在跑的介面；iPolloWork 這裡改的是設計與簡報成品。網站編輯做到多深，尚未確認。

簡報、設計和影片因此可以留在同一個任務裡繼續改，不必把生成結果當成一次下載就結束。

### 批准、沙箱與記憶

安全文件要求在檔案、shell、瀏覽器、網路或帳號動作之前要人批准。伺服器的批准模式可以是 manual 或 auto；manual 逾時會拒絕。同一份文件也寫明：使用者批准的指令，不保證和作業系統帳號隔離。

Orchestrator CLI 可以把 sidecar 放進 Docker 或 Apple container，額外掛載要 allowlist。倉庫裡另有一個 microsandbox 的 Rust 範例。桌面 app 是否提供同一組沙箱開關，尚未確認。

跨對話記憶分成兩路。登入 iPolloCloud 後的 Memory Bank 會先顯示要記住的內容，等人確認才寫入該帳號。舊對話要在當下需要時才去查，不會把所有舊 chat 自動塞進新 prompt。沒登入時 Memory Bank 能否使用，尚未確認。

## 主打賣點

- 它最想被記住的是：一個本機工作台接多個 agent 引擎，插件和 Skills 用同一套生命週期，程式、文件、簡報、設計、影片生成後還能改。
- 和 [Emdash](https://github.com/generalaction/emdash)、[Conductor](https://www.conductor.build/)、[Maestro](https://github.com/RunMaestro/Maestro) 這類 ADE 相比，差別在引擎怎麼接進來。那些產品多半是偵測本機已安裝的 agent CLI，再用 worktree 分開跑。iPolloWork 自己帶 OpenCode sidecar，並用 adapter 接 Codex Harness 與 DeepSeek Harness，再把執行狀態收成同一種顯示。內建引擎 ID 沒有 Claude Code。入門文件仍把產品說成 Codex／Claude Code 的替代，README 則說它是接上這些 runtime 的工作台。兩句話要分開看。
- 和 [Nimbalyst](https://github.com/nimbalyst/nimbalyst) 相比，兩邊都讓人改 agent 旁邊的可視文件。Nimbalyst 強調 git 裡的 plain files、渲染後 diff 和 worktree。iPolloWork 強調每個 agent 綁哪一個引擎，以及設計畫布、PPTX 和影片分鏡。iPolloWork 的專案各自指向一個資料夾；有沒有用 git worktree 隔離，尚未確認。
- 多 provider、MCP、Skills、看板、本機優先、可選雲端，在其他 ADE 裡已經常見。編排圖是儀表上的節點圖，進到 2D／3D 工作場所時要變成工位之間的等待和並行，而不是把這張圖本身叫做 workspace。
- 授權不是先前猜測的 MIT。現行條款是 iPolloWork Source Available License 1.0：個人自用，以及少於三個使用者的內部非商業評估可以免費。三人以上、託管、販售或對客戶提供，都要事先書面授權，介面也要保留 iPolloWork 品牌。先前以 MIT 釋出的部分仍依原來的 MIT。

## 使用情境

### 同一專案混用引擎

- 適合誰：已經用 OpenCode，偶爾要 Codex Harness 或 DeepSeek Harness 的人。
- 在什麼情況使用：一個專案裡，寫程式或做研究用預設引擎，另一個角色改綁另一套引擎。
- 帶來的價值：專案目標、任務和成品留在同一處，各引擎仍用自己的規劃和子代理。

### 生成簡報、設計或影片後要人改

- 適合誰：產出不只有程式碼的人。
- 在什麼情況使用：對話的工作種類是 document、design 或 video，生成後要改文字、版面或分鏡。
- 帶來的價值：成品留在 session 的檔案裡，人可以直接改，不必把同一段要求重貼進下一輪 chat。

### 用看板看誰在做、哪裡要人點頭

- 適合誰：想把多個角色 agent 收成一個專案，又要自己批准高風險動作的人。
- 在什麼情況使用：任務在看板上從 planned 走到 review；shell、檔案或瀏覽器動作停下來等人。
- 帶來的價值：進度、負責人和待批准動作在同一個專案裡。三人以上或對客戶使用要先取得書面授權。這套協作實際好不好用，尚未確認。

## 我們可以學什麼

- 引擎是員工使用的工具。工作台顯示原生計畫和檔案，回合結束後不要在客戶端再跑一套隱藏的修復或交付流程。
- 角色、依賴、並行和驗收條件可以寫在專案裡，執行順序仍由引擎決定。範本是指引，固定流水線會蓋過各引擎自己的做法。
- 插件、Skills 和授權用一份生命週期，再投影到各引擎。引擎專用程式留在 adapter 後面。
- 文件、簡報畫布和影片分鏡要能被人和 agent 接著改，並掛在同一個任務上。
- **在 2D workspace 裡會變成什麼**：一張平面樓層。一個專案是一區。每個 agent 站在固定工位，工位上寫著它用的引擎（OpenCode、Codex Harness 或 DeepSeek Harness）、skills 和 plugins。工作單在看板上移動。文件、簡報畫布、設計和影片分鏡放在桌面上，人可以走過去改。dependency 是「這張桌子等下一張桌子的產出」，parallel 是相鄰工位同時開工。要批准的 shell、檔案或瀏覽器動作是工位上的待辦牌，逾時沒人處理就拒絕。日曆上的自動任務是定時亮起的工位。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室，像 [Agent Office](https://github.com/AgentSystemLabs/agent-office) 那樣看得到員工在場。員工待在自己的位子，看得出正在用哪一套引擎做事。依賴是一個人把成品放到下一張桌子，並行是相鄰工位同時開工。人走過去翻桌上的文件、簡報、設計或影片分鏡，改完再讓同一個引擎繼續。批准請求出現在員工面前，等人點頭或拒絕。若開了容器沙箱，那是這張桌子的圍欄。登入雲端後的 Memory Bank 是要先確認才放進個人櫃子的記事。
- **不值得照搬**：Electron 側欄、用量儀表和 agent 關係圖是 ADE 控制台。安全文件寫明，批准過的指令仍可能用使用者自己的帳號權限。CLI 的 Docker／Apple container 和桌面日常路徑是不是同一件事，尚未確認。授權也要一起看：這不是可以隨意嵌進產品的 MIT 工作台。

## 初步看法

- 最有價值的部分：多引擎共用專案與任務，同時把文件、設計、簡報和影片留成可編輯檔案，而且客戶端不自己再跑第二套 agent 流程。
- 最大限制或疑問：現行授權是 source-available。內建引擎沒有 Claude Code。插件投影是否包含 Codex、桌面是否有沙箱開關、網站編輯做到哪，都尚未確認。沒有實測，互動品質未知。
- 是否值得進一步研究或親自體驗：值得看它怎麼把引擎 adapter、專案角色和可編輯成品接在一起。若要做成 2D／3D 工作場所，要借的是工位、成品和批准。

## 後續補充（選填）

Not tried yet.

## Sources

- Repository：[https://github.com/Devin-AXIS/iPolloWork](https://github.com/Devin-AXIS/iPolloWork)
- Homepage：[https://www.ipollo.ai/](https://www.ipollo.ai/)
- Product docs（repo 內）：`packages/docs/start-here/get-started.mdx`；下載頁文件寫的是 [ipolloworklabs.com/download](https://ipolloworklabs.com/download)
- License：`LICENSE`（iPolloWork Source Available License 1.0）；歷史 MIT 見 `LICENSES/MIT-legacy.txt`
- Engine IDs：`packages/types/src/workspace.ts`
- 專案與工作項目：`packages/types/src/project-workspace.ts`、`packages/types/src/work-items.ts`
- 插件平台：`specs/plugin-platform-architecture.md`
- 安全與批准：`docs/security-model.md`、`apps/server/src/approvals.ts`
- 可選沙箱：`apps/orchestrator/README.md`、`examples/microsandbox-ipollowork-rust/README.md`
