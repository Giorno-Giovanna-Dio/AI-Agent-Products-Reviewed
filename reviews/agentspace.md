# AgentSpace

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentspace-cell-80a8/reviews/agentspace.md)
>
> Cell ID：[https://github.com/HKUDS/AgentSpace](https://github.com/HKUDS/AgentSpace)
>
> Status：`untried`
>
> Category：飛書式協作 web workspace（人與數字員工）
>
> Last updated：2026-10-10

## 產品介紹

AgentSpace 是香港大學 Data Intelligence Lab（GitHub 組織 HKUDS）的開源專案，TypeScript，Apache-2.0。官方定位是：人與 agent 組成同一個團隊，待在同一個工作空間裡。人掌握方向和授權；agent 被寫成有崗位、有 owner、有責任邊界的數字員工，負責協調和執行。它要處理的情況是，好用的 agent 往往只活在某一個人的終端機或帳號裡，訊息、文件、審批和執行輸出沒有共同的家，換一家 CLI 就要把上下文重做一遍。

使用者打開的是 Next.js 網頁工作台（本機說明寫 `http://127.0.0.1:1455`，另有託管站 [hire-an-agent.online](https://hire-an-agent.online/)）。側欄把通知、訊息、聯絡人、任務看板、審批、知識與文件、數字員工、組織圖、費用、表格、排程放在同一個工作區。真正跑 CLI 的是本機或遠端 daemon。所以他們說的 workspace，是**帶文件表面的網頁協作工作台**：頻道裡可以有 markdown、表格、簡報和文件，也可以連到 Google Workspace。它是儀表板加文件，平面樓層和可走進的辦公室是我們後面要翻譯的形狀。本次只讀 README、領域模型和畫面路由，沒有安裝、沒有登入。

這個 Cell 只指 HKUDS/AgentSpace。其他組織也有同名產品，不在這份筆記裡。

## 主要 Features

### 數字員工可以借用、轉移

展板把每個 agent 顯示成組織資源：角色、摘要、owner、就緒狀態、技能、知識、綁定的 runtime，以及它出現在哪些頻道。隊友可以申請使用；owner 和 admin 有審核佇列。員工還能匯出成簽過名的 [OpenAgent](https://github.com/5dive-ai/openagent) persona card，預設會拿掉操作說明、技能和 owner 身份。價值是把一個好用的 agent 變成可以交接的崗位，而不是鎖在個人聊天視窗裡。

### 頻道、任務、文件共用一套物件

空間單位是 channel，種類是群組或私訊。人和 agent 都是成員，訊息角色分成 human 與 agent。任務掛在頻道上，有負責人、優先級，狀態是待辦、進行中、阻塞、完成。頻道文件有版本；版本會記下最後編輯者是人還是 agent，以及這版是手動、agent，或交接觸發的。文件協作 run 可以平行或依序：每一步指定 agent、依賴哪些前一步，以及交給下一手的東西是文件、附件還是訊息。頻道、任務、知識頁、表格和檔案上，人和 agent 都能留評論或提出變更。執行輸出、附件和文件版本留在工作區。價值是跨天的工作有下一手負責人，聊天紀錄只是過程。

### AgentRouter 與遠端 daemon

AgentRouter 負責啟動不同的 agent CLI，並把事件、session、結果和診斷收成同一種契約。README 把它放在工作區和業務佇列之外：工作區仍擁有任務，它只負責啟動與正規化。路徑上包含 Claude Code、Codex、OpenCode、OpenClaw、Hermes、Antigravity（`agy`）；Gemini CLI 與 NanoBot 標成舊的 provider runtime。官方說法是：換 harness 時，員工身份、技能、知識和權限留在工作區。這點尚未實測。Web 和 CLI 都走同一套服務；任務、審批和通知進佇列後，由 `agent-space-daemon` 在有 provider CLI 的機器上執行。

### 權限、審批，以及還沒做完的隔離

工作區成員是 owner、admin 或 member，可用邀請連結或 8 位加入碼。頻道另有加入、邀請和讀寫。私訊範圍限於參與者和相關 agent 的 owner。審批種類包含任務產出、文件更新、訊息草稿、runtime 工具、知識提案、外部資料操作。權限中心按資源或按人查看授權，並管理 daemon token 與 Google 憑證委託。套件裡已有 sandbox 介面：本機或 cube，能讀寫檔、執行命令、做快照。README roadmap 仍把「多 agent 隔離與 sandbox 政策層」列為計畫。隔離實際到什麼程度，尚未確認。

側欄還有工作流規則（訊息、任務完成、文件更新、排程可觸發發訊息、建任務、點名 agent、改表格或 webhook）、定時任務、範本庫、技能庫、費用和績效。這些是協作套件常見模組，影響日常操作，不改變上面的物件模型。

## 主打賣點

- 它最想被記住的是：人和數字員工用同一套組織上下文工作。飛書那類產品為人的頻道、文件和審批而做；這裡把 agent 做成同一套物件裡的成員。
- 和「一個人、一個終端、一場聊天」相比，差異在崗位可以招募和借用、執行器可以換、交接寫在文件 run 上、敏感動作進審批佇列。
- 費用總覽、績效看板、多維表格、應用市場、範本庫，是協作套件裡常見能力的再包裝。組織圖是人類卡片與 agent 卡片，或按頻道分組，畫面是清單。示範影片檔名是 multi-agent war room；README 描述的是多個 agent 把一件高影響決策推進到人的審批。中文示範腳本提到「總控室」，程式裡看得到的空間單位仍是 channel。影片裡有沒有空間畫面，尚未確認。

## 使用情境

### 小團隊把零散原料做成跨天交付

- 適合誰：大約十人以內的創辦團隊、交付或代營運工作室。
- 在什麼情況使用：手上有 brief、約束、預算和舊紀錄，要拆給協調、內容、執行、風險這類崗位，並在中途改條件。
- 帶來的價值：原料進工作區，候選員工上崗後進頻道；阻塞時有升級和下一手；結果沉成文件、任務狀態和附件。這是官方示範腳本，尚未實測。

### 同一位員工，換執行器

- 適合誰：同一件工作有時用 Claude Code、有時用 Codex 或其他已接入 CLI 的團隊。
- 在什麼情況使用：任務改 runtime，角色說明、技能和權限希望留在原員工身上。
- 帶來的價值：AgentRouter 只換 harness。上下文是否真的不丟，尚未確認。

### 敏感動作先經過人

- 適合誰：不希望 agent 直接改外部表格、發出訊息或呼叫工具的人。
- 在什麼情況使用：文件權限、runtime 工具、知識提案或外部資料操作需要留下誰批准的紀錄。
- 帶來的價值：人留在授權點，agent 繼續協調其餘步驟。官方提到快速連按的審批節奏，手感尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：數字員工是有 owner 的崗位，和人共用頻道、任務、文件。高風險動作進審批。員工身份和執行器分開，換 CLI 不必重做崗位。
- 值得借鑑的 interaction / workflow：交接要寫明種類（文件、附件或訊息）和依賴；任務要有阻塞狀態；文件版本要能看出這版是交接產生的。人介入的地方是審批佇列和頻道成員，執行輸出掛在工作區物件上。
- 在 2D workspace 裡會變成什麼：平面樓層上，一個頻道是一間房。數字員工是房裡的座位，座位標著 owner 和就緒狀態。任務貼在座位或牆上，阻塞用醒目的標記。審批是門口的收件盤。文件是桌上的紙；依序交接是把紙或附件送到下一張桌子，平行 run 是多張桌子同時在動。組織圖變成這層樓的目錄。
- 在 3D workspace 裡會變成什麼：可走進的辦公室裡，頻道是房間，只有這個頻道的人和 agent 在場。走進房間能看到誰負責哪張任務、桌上是哪一版文件。依序交接是把文件或附件交到下一張桌子；等人批准時，對方出現在你桌前。Daemon 所在的機器是後面的機房，房間裡看見的是員工、任務和產物。
- 不值得照搬或需要重新設計的地方：側欄模組清單（費用、績效、市場、表格）留在營運後台，不要把整面儀表板搬進樓層。War room 這個稱呼留在示範名稱，房間的資料來源用 channel 成員。Sandbox 政策仍在 roadmap，權限中心不能直接當成已隔離的執行環境。2026-07-09 的新聞把 Slack 放在測試分支，不要當成 main 上的能力。型別裡有飛書、Notion、Microsoft 365 等外部文件提供者；README 明確寫完成的是 Google Workspace 與已合併的飛書整合，其餘是否可用尚未確認。

## 初步看法

- 最有價值的部分：人和 agent 操作同一批工作物件，交接和審批是模型裡的一等欄位，執行器可以換。
- 最大限制或疑問：畫面模組很多，空間感停在頻道名稱和卡片式組織圖。多 agent 隔離仍是計畫。程式最後推送時間是 2026-07-24；之後未合併的分支沒有逐一核對。本次沒有跑起來。
- 是否值得進一步研究或親自體驗：對 workspace 設計值得留著，優先學頻道、交接和審批的資料模型。若要驗證審批手感或跨天上下文會不會丟，再放到本 repo 以外的目錄執行。

## Sources

- Official website：[https://hire-an-agent.online/](https://hire-an-agent.online/)
- Repository：[https://github.com/HKUDS/AgentSpace](https://github.com/HKUDS/AgentSpace)
- Documentation：[README.md](https://github.com/HKUDS/AgentSpace/blob/main/README.md)、[README_ZH.md](https://github.com/HKUDS/AgentSpace/blob/main/README_ZH.md)、[deploy/FOUNDER_EXECUTION_SHOWCASE.md](https://github.com/HKUDS/AgentSpace/blob/main/deploy/FOUNDER_EXECUTION_SHOWCASE.md)
- 領域模型：`packages/domain/src/workspace.ts`、`packages/domain/src/channel-document-runs.ts`、`packages/domain/src/collaboration.ts`；畫面路由在 `apps/web/app/w/`
