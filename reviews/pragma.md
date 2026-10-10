# Pragma

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pragma-cell-80a8/reviews/pragma.md)
>
> Cell ID：[https://github.com/pqpo/pragma](https://github.com/pqpo/pragma)
>
> Status：`untried`
>
> Category：跨 harness 的 Agent Team 平台（Desktop／CLI／SDK）
>
> Last updated：2026-10-10

## 產品介紹

Pragma 是預覽中的平台，用來把「怎麼跟 AI 一起做事」收成可以帶走的 Agent Team。一個團隊包含專家、專家團、流程、工具、上下文、記憶、權限，以及人要點頭的關卡。你可以在 macOS 桌面 App 裡組好它，用 CLI 從終端機叫它，或用 SDK 嵌進自己的程式。同一次任務裡，不同步驟可以綁不同的模型，也可以綁不同的 agent harness。公開說明列出 Claude Code、Codex、PI、Qoder CLI 與 Antigravity。倉庫裡另有 OpenCode 的 runtime 套件；README 的能力表沒有把它列進去，桌面版是否已開放，尚未確認。PI 對應的是 [Pi](reviews/pi.md) 那條 coding-agent CLI。

它把 harness 留在原位。那些程式仍負責讀檔、跑指令、維持自己的 session。Pragma 管的是：這一步該用哪一種執行環境、上下文怎麼交到下一個人、人什麼時候介入、做完的經驗要不要升成知識或技能。授權是 AGPL-3.0（README 寫 AGPL-3.0-only）。本次只讀 GitHub 上的 README、架構與使用文件，沒有安裝，也沒有跑 Desktop 或 CLI。作者先前的 SmartCropper、SmartCamera 是 Android 影像函式庫，和這個 repo 是不同產品。

## 主要 Features

### 專家、專家團、流程走同一棵執行樹

Expert 是一位有說明、工具和 runtime 綁定的專家。ExpertTeam 多一位協調者：成員之間預設不能互相打斷；協調者在這次團隊執行裡可以派工、續做、轉向、等待或中斷。Flow 把專家、團隊、子流程和 HumanTask 排成步驟。這些都進同一棵樹：一項 Mission 底下是 Execution，再底下是各次 Invocation，包含人要回答的互動。取消、恢復、用量和事件用同一套身份。

### 模型與 harness 分開綁

模型決定推理，harness 決定規劃、工具、session 和權限長什麼樣。每個專家或流程步驟可以各自綁一組「runtime × 模型」。權限也拆兩層：Pragma 管理的工具和 MCP 走 Host 的核准；檔案、shell、網路、git 走該 harness 原本的權限模式。工作方式寫在 YAML 裡，本機 Host 再把需求綁到這台機器上實際裝好的環境。

### Mission Board：這次任務的白板

Mission 是一件會跨多次輸入、多個 session、甚至跨 harness 的工作。白板分兩區：任務內共享，以及只屬於目前 runtime context 的私人筆記。計畫、進度、決策、交接說明是約定路徑上的一般條目（`plan.md`、`progress.md`、`decisions.md`、`handoffs/`），沿用同一套讀寫，而不是另做一套專用狀態機。真正的檔案留在 workspace，白板只記受控的相對路徑。大輸出寫進系統區，下一棒拿到摘要和引用。多人改同一條目要帶 revision，衝突要重讀再合併。預設不把整面白板塞進每一輪 prompt。

### 記憶先提煉，知識與技能要人核准

執行事件先進 Evidence，再由隱藏的 Memory Curator 收成情景記憶（以前做過什麼）和語意記憶（現在相信什麼）。獨立專家只看自己的履歷；團隊或流程裡還看得到這次資產的共同履歷，不會把其他專家的個人記憶混進來。知識和技能是另一層：Store Revision Agent 與 Skill Revision Agent 只產生草稿，人審過才發布。Memory 不直接改正式 Skill。文件寫記憶功能預設關閉。刪掉一項 Mission 不會自動清掉已經提煉出的長期記憶；忘記要另外做，而且不可復原。這套閉環的實際品質尚未實測。

### 工作方式可以打包，評測跟定義分開

`.pragma` bundle 帶走可攜的 YAML 與選中的資產。另一個 Host 讀進來之後，要明確把需求綁到本地的 harness 或外掛，才能編譯執行。同名技能或知識庫不會靜默覆蓋，要人選取代、保留或複製。Evaluation 是獨立、可版本化的資源。Flow 的 Run Dry 不啟動 runtime、不呼叫模型，用來檢查路徑和提示詞有沒有接對。Desktop 與 CLI 共用同一個本機 Host。架構文件寫目前沒有 Web、Server 或 Worker app。

內建還有幾位系統專家：Memory Curator、Store Revision Agent、Skill Revision Agent、Evaluation Judge，以及一位名叫 Pragma 的預設通用 agent。這位預設員工可以自己把工作做完，也可以幫忙建立專家、團隊、流程和 Mission。

## 主打賣點

- **可複用的單位是整套工作方式。** YAML DSL、不可變 revision 和 `.pragma` bundle 讓方法可以版本化，搬到另一台機器後再綁上那裡的 harness。
- **跨 harness 仍是同一項 Mission。** [CrewAI](reviews/crewai.md) 的角色活在同一次 Python 執行裡。[Paperclip](reviews/paperclip.md) 是公司控制平面，用 heartbeat 叫醒外部員工。Pragma 把工作方式本身做成可攜定義，執行時再接上 Claude Code、Codex、[Pi](reviews/pi.md) 這類 harness。[Conductor](reviews/conductor.md)、[Maestro](reviews/maestro.md) 是同時看很多終端機的桌面控制台；Pragma 的桌面是在組團隊和跑 Mission。
- **交接、記憶、評測分開。** 這次任務的白板、跨任務的記憶、以及要人核准才生效的知識與技能，生命週期不同。評測集用來比較改了 prompt、流程、模型或 runtime 之後，交付有沒有變好。
- 桌面 Studio、CLI 的人或 JSON 輸出、SDK 嵌入，是同一個本機 Host 的三種入口。官方想被記住的句子是：組好一次 Agent Team，換地方還能用。

## 使用情境

### 同一項交付裡換不同專家和 harness

- 適合誰：一件工作要先釐清需求、再實作、再獨立審查，而且每一步適合的工具不一樣的人。
- 在什麼情況使用：用 Flow 把步驟排開，實作綁 coding harness，審查綁另一個 runtime，中間用 HumanTask 停下來等人點頭。
- 帶來的價值：決策和產物引用留在同一項 Mission 的白板上，下一棒不用重看整段對話。官方示例裡的模型名稱是示意，實際綁定由 Host 決定。

### 把一套做法打包到另一台機器

- 適合誰：想把已經跑順的專家或流程交給同事，或嵌進自己的程式的人。
- 在什麼情況使用：匯出 `.pragma` bundle，另一端用 Desktop 匯入，或用 `@pragma/interpreter` 載入、`@pragma/core` 執行。
- 帶來的價值：帶走的是方法與選中的資產。本機的 runtime、權限和密鑰在新環境重新綁定，這台電腦的路徑不會變成權威。

### 重複做過的事沉成可審的知識

- 適合誰：希望重複任務慢慢變穩，同時要自己決定正式技能能不能改的人。
- 在什麼情況使用：打開記憶（文件寫預設是關的），讓事件變成 Evidence；知識庫和 Skill 以草稿出現，人核准後才掛回專家。
- 帶來的價值：情景記憶、當前事實、正式知識分開。技能學習還有門檻：文件寫同一位 producer 要累積多段高價值、跨對話的成功履歷。門檻是否夠、草稿品質如何，尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是 Expert，桌上的工具是 harness，職務說明是可攜的 YAML。團隊方法是可以搬到另一間辦公室的聘僱包；座位上的機器留在本地。Mission 是一件會跨好幾次對話的工作。
- 值得借鑑的 interaction / workflow：共享白板只放計畫、進度、已決定的事和交接摘要；私人抽屜留給這位專家的草稿。檔案本體在 workspace，白板上是路徑。改同一張紙要先看版本，衝突要露出來。人站在 HumanTask 關卡才放行。記憶館員在後面整理證據，知識上架和技能改版都要人簽名。名叫 Pragma 的預設員工可以自己做，也可以去組團隊。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層。每位 Expert 是固定工位，名牌寫角色，桌上標著這次綁的 harness。ExpertTeam 是一間小辦公室，協調者的桌子在中間。Flow 是地板上的路線，HumanTask 是路線上要蓋章的櫃檯。牆上一塊 Mission Board，貼著 plan、progress、decisions 和 handoff 紙條。私人筆記留在工位抽屜。記憶室在樓層後面：Curator 把 Evidence 收成履歷和事實卡；知識架和技能櫃只有人簽過才更新。`.pragma` 是可以拿到另一層樓的資料夾，那邊的工位再插上自己的機器。Studio 裡編 YAML 是工位上打開的文件，樓層地圖負責顯示誰在哪個位子。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你看見誰在場：名叫 Pragma 的預設員工在前台，專家在自己的位子上做事，Memory Curator、Store Revision、Skill Revision、Evaluation Judge 在後間。後間可以走進去看，他們不占主樓層的工位。走到某張桌子，看到的是這次 Invocation 和白板上的交接。協調者把工作放到成員桌上；成員做完把引用放回共享桌。HumanTask 是你站在那裡才能放行的門。換 harness 是換這張桌子上的工具，人還是同一位員工。重點是看見誰在哪裡、交接放在哪裡。
- 不值得照搬的地方：樓層不要做成 Pragma Desktop 的分頁（Studio、記憶管理、評測）。那是員工用來編團隊的工具，屬於 ADE dashboard。不要為每一次 Invocation 都站一個新人；座位是 Expert，桌上的這一輪工作才是 Invocation。不要把完整對話抄上共享白板。不要讓記憶自動改正式技能，Pragma 自己也是草稿加人審。Desktop 目前公開發行是 macOS 預覽版、未簽章；Windows／Linux 桌面包不在這次公開發行裡。CLI 文件寫 macOS 與 Windows。沒有雲端 Host。OpenCode adapter 在原始碼裡，公開能力表未列，是否已對使用者開放尚未確認。以上都尚未實測。

## 初步看法

- 最有價值的部分：工作方式和「哪一個 harness 在跑」拆開，再用同一項 Mission 的白板交接。記憶可以自動提煉，知識和技能要人核准才生效。
- 最大限制或疑問：產品仍是 preview。研究當日最新預覽標籤是 v0.2.54，桌面只覆蓋 macOS 且未簽章。記憶預設關閉。跨 harness 交接是否真的比較省上下文、草稿品質如何，都尚未確認。AGPL-3.0 會影響把它嵌進對外網路服務的方式。
- 是否值得進一步研究或親自體驗：值得。若要做 2D／3D 辦公室，下一步該看 Mission Board 和 HumanTask 在真實多 harness 任務裡人怎麼介入。

## Sources

- Repository：https://github.com/pqpo/pragma
- README（英文）與 [README.zh-CN](https://github.com/pqpo/pragma/blob/main/README.zh-CN.md)
- [目前架構概覽](https://github.com/pqpo/pragma/blob/main/docs/architecture/current-architecture-overview.md)
- [定位與差異](https://github.com/pqpo/pragma/blob/main/docs/strategy/pragma-positioning-and-competitive-differentiation.md)
- [Mission Board](https://github.com/pqpo/pragma/blob/main/docs/articles/03-mission-board.md)
- [Memory 使用指南](https://github.com/pqpo/pragma/blob/main/docs/usage/memory.md)
- [Experts](https://github.com/pqpo/pragma/blob/main/docs/usage/agents.md)、[Flows](https://github.com/pqpo/pragma/blob/main/docs/usage/flows.md)
- 作者文章（GitHub homepage）：https://www.pqpo.me/2026/08/16/agent-team-builder-multi-harness-runtime/
- License：GitHub SPDX `AGPL-3.0`；README 寫 AGPL-3.0-only
- 研究當日最新預覽發行：v0.2.54（2026-10-10）
