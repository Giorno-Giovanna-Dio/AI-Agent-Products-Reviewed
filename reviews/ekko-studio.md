# Ekko Studio

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ekko-studio-cell-fb52/reviews/ekko-studio.md)
>
> Cell ID：[https://github.com/EKKOLearnAI/ekko-studio](https://github.com/EKKOLearnAI/ekko-studio)
>
> Status：`untried`
>
> Category：多 agent 控制台與節點流程工作室（ADE）
>
> Last updated：2026-10-10

## 產品介紹

Ekko Studio（舊名 Hermes Studio／Hermes Web UI）是一套本機優先的 AI 工作台。桌面版、自架 Web 和手機 App 連的是同一套 Studio。它把內建的 Ekko Agent、Hermes Agent，以及十多個已安裝的 coding CLI（Claude Code、Codex、Pi、OpenCode 等）收進同一個目錄，讓人在單人聊天、群組房間，和一張可執行的節點圖之間切換。

人實際做事的地方是對話、群組、檔案、終端機和瀏覽器工具。畫布是 Vue Flow 的流程圖：每個節點是一次 agent 步驟，線是交接。它不是一間可以走進去的辦公室。它和 Conductor、Maestro 同屬多 runtime 的 ADE 控制台；多出來的是可執行的視覺流程、Hermes profile 的控制面，以及手機或裝置可以跟著同一批 session。

## 主要 Features

### 同一份 Agent 目錄，兩種啟動方式

單聊、群組和流程節點共用一份 agent 清單。Ekko 內建。桌面版和 Docker 會帶上 Hermes runtime；用 npm 安裝時，則去找這台機器上已經有的 Hermes。Coding agent 必須裝在跑 Studio 後端的那台機器上。

啟動分成兩種。scoped 用隔離的 session 設定，可以改用 Studio 選的 provider 和模型。global 沿用該 CLI 原本的登入與設定。可用的模型、工具和 context 控制仍看那個 runtime，Studio 沒有把它們收成同一種行為。

### 單人對話裡的任務卡、核准和產出

對話會串流、展開工具呼叫，並用任務卡顯示步驟、核准和澄清問題。產生的 HTML、PDF、表格、圖片和原始碼可以在對話裡預覽。每一輪看得到 token、快取、估計成本和剩餘 context。Studio 自己的 session 存在本機 SQLite。Hermes 的 `state.db` 只被當成唯讀歷史，兩邊的狀態是分開的。

### 群組房間，以及有深度上限的交接

群組是即時聊天室。用 @ 把某個 agent 叫進來，它用自己的 profile 回覆。歷史太長會壓縮。程式裡還有 agent 之間的 handoff：可以交給房裡另一個 agent，但有深度上限。遠端結果在重啟後變成未知時，會停下來給人看，而不是自動重試。這仍是一個聊天面板，不是樓層上的房間。我們沒有實際開過房間。

### 節點流程：交下去的是上一棒的文字

流程畫布用 Vue Flow。每個節點自帶標題、agent、scoped 或 global、模型、技能、附件，以及這一步的任務。連線可以標 success、failure 或 always，也可以加結構化條件。匯流可以等全部上游成功（all），或走 any。程式裡也有回饋用的迴圈邊，以及對迴圈是否合法的檢查。節點可以設成必須等人核准。

跑起來之後，下游節點收到的是組好的一則使用者訊息：上游各節點最後一則助理輸出，加上這一步的任務。上游的整段對話不會抄進下游。一次執行會凍結當時的圖，事後可以在畫布上回放，並打開該節點自己的對話。文件寫若沒有自選目錄，會在 Studio 資料目錄下開一個流程專用目錄。這條路徑是否仍是現行預設，尚未確認。

`docs/workflow.md` 開頭仍寫「執行尚未接上」，後半以及目前 README、節點型別卻已有執行、條件、核准和快照。以 2026-10-10 的原始碼型別來看，這些能力在程式裡。我們沒有實際跑過。文件還寫 Hermes 流程節點會自動用一次性的 once 放行工具呼叫。這句是否仍是現行行為，尚未確認。單人聊天的核准與澄清，官方說明仍要人回應。

### Profile、權限，以及人從外面進來

Hermes profile 分開保存設定、記憶、技能、排程、看板和頻道。超級管理員看得到全部 profile；一般帳號只能用被指派的。把單一對話分享給 App 時，可以分別開放輸入、檔案、語音和終端機，也可以收回。

桌面版有給 agent 用的多分頁瀏覽器。分頁有控制租約、獨立 profile，以及有界的頁面操作。手機可經區網或雲端中繼跟上單聊、群組和流程進度。架構圖把 global agent 和聊天、群組、流程並列；從程式來看，`/global-agent` 主要是裝置與語音中繼，並把遠端的核准、澄清轉回本機執行。它不是另一個坐在工作室裡的總管。尚未親自連過裝置。

看板和 Journey 圖屬於 Hermes 那一側。看板是任務板。Journey 是 `hermes journey` 畫出來的技能與記憶關係圖。兩者都是控制台裡的圖，不是工作空間本身。

## 主打賣點

- 官方想被記住的是「你的 agents，一間 studio」：聊天、群組、視覺流程、檔案、語音和裝置，落在同一套本機資料上。
- 和 Conductor、Maestro 這類 ADE 控制台相比，重疊的是「多個 coding CLI 收進一個畫面」。Ekko 多出來的是可執行的節點圖，以及 Hermes profile 那套記憶、技能、看板和頻道。它沒有把 Git worktree 當成平行工作的基本單位。
- 畫布是流程工作室，不是人工作的地方。把節點圖叫做我們要做的 workspace，會和平面辦公室或可走進的辦公室搞混。
- 十多個 CLI、主題、用量圖和語音供應商清單，大多是既有聊天控制台的包裝。值得看的是交接時只傳上一棒結果、核准關卡，以及 scoped／global 兩種桌子。

## 使用情境

### 一台機器上管很多已安裝的 coding CLI

- 適合誰：本機已經裝了 Claude Code、Codex、OpenCode 等，不想為每個 CLI 各開一個視窗的人。
- 在什麼情況使用：在跑 Studio 後端的那台機器上切換 agent，看工具軌跡和產出預覽；人離開時用手機看進度或回答澄清。
- 帶來的價值：runtime 仍是原本的 CLI。Studio 負責目錄、session 和權限，而不是再發明一個模型。

### 把研究、實作、審查畫成會停下來等人的流程

- 適合誰：同一類工作會重複做，而且某一步必須人點頭才能繼續的人。
- 在什麼情況使用：研究節點的輸出交給程式節點；審查走失敗路線時回到前面；實作前先停在核准。
- 帶來的價值：交接物是上一棒的文字結果，範圍清楚。凍結的執行快照讓人事後能對到當時的圖，而不是只剩一段聊天。

### 在一間聊天室裡把問題丟給不同專長

- 適合誰：想在同一串對話裡輪流問規劃、寫程式和審查的人。
- 在什麼情況使用：開一個群組，@ 指定的 agent，或讓 agent 在深度上限內把工作交給下一個。
- 帶來的價值：人看得到誰被點名、交接停在哪裡。壓縮和深度上限讓房間不會無限拉長，也不會無限互傳。

## 我們可以學什麼

- 值得借鑑的 product idea：把「人工作的地方」和「流程圖」分開。聊天、群組、終端機是工作；節點圖是排程和交接契約。共用的是 agent 目錄和 profile，不是把所有畫面都叫成 workspace。
- 值得借鑑的 interaction / workflow：下游只拿到上游最後一則結果，整段過程留在原節點。人要介入時，是核准關卡、澄清，或收回分享權限，不是改寫別人的記憶。scoped 是乾淨的客座設定，global 是 agent 自己的舊桌子。群組交接有深度上限，結果不明就停給人看。
- 在 2D workspace 裡會變成什麼：俯視一層樓。每個 runtime 是一張工位，門上寫著現在用的是 scoped 客座還是它自己的 global 桌子，以及它裝在哪台機器上。一次流程執行是一份工作夾沿著走道傳：成功走正門，失敗走側門；all 是等齊所有上游才進下一張桌子，any 是先到先做。核准是人站在工位前，資料夾先不往下傳。群組是會議桌，@ 是把某張工位的人叫過來；深度上限是這張桌子最多傳幾手。Journey 的技能與記憶關係貼在牆上當海報，地板仍是工位，不是那張關係圖。手機是同一層樓的另一個入口。
- 在 3D workspace 裡會變成什麼：走進一間像 [Agent Office](https://github.com/AgentSystemLabs/agent-office) 那樣的工作室，看得到誰在場、誰坐在終端機前、誰的分頁瀏覽器正被租用。流程不是立體節點圖，而是你沿著走道看員工把文件夾交給下一個人。你停在核准桌，讀那一疊「上一棒結論」，再決定放行。凍結快照是事後走一遍當時的辦公室：誰做完了、哪一扇失敗的門沒被打開、哪段對話留在原座位。群組是你推門進去的會議室。裝置中繼是你人在外面，仍能對同一張桌子回答澄清，員工沒有被搬到雲上。
- 不值得照搬或需要重新設計的地方：Ekko 的主畫面是 ADE dashboard 加節點編輯器。Conductor、Maestro 也是這類控制台。側欄、Agent 卡片和 Vue Flow 畫布都不是我們的 2D／3D 辦公室。若 Hermes 流程節點仍會自動用 once 放行工具呼叫，人有沒有看過這一步，會在流程和單聊之間不一致，這點要重新設計。流程只傳最後一則輸出，中間改錯又改回的過程會被丟掉，也需要另外留下證據。

## 初步看法

- 最有價值的部分：交接物很窄，只有上一棒的最後輸出。核准和澄清是明確的停點。scoped 和 global 把「乾淨設定」和「沿用舊登入」分成兩種桌子。這些都能放進辦公室，而不必搬整套控制台。
- 最大限制或疑問：尚未實際使用。流程文件前後不一致，執行、條件、迴圈和自動 once 放行的真實手感尚未確認。Coding agent 綁在後端那台機器，畫面上的多人工作室仍是單機 runtime。授權是 BSL-1.1：非商業使用在授權範圍內，2029-05-10 轉成 Apache-2.0，商業使用需要另外向 EKKOLearnAI 取得授權。這份筆記只借概念。
- 是否值得進一步研究或親自體驗：值得。要實測的是節點交接和核准，不是再清點一次 CLI 清單。若要知道畫布上的回放是否真的對得上當時的對話，必須自己跑一條流程。

## Sources

- Official website：https://ekkostudio.xyz/（同內容亦見 https://hermes-studio.ai/）
- Repository：https://github.com/EKKOLearnAI/ekko-studio（舊網址 https://github.com/EKKOLearnAI/hermes-web-ui 會指向同一 repo）
- Documentation：https://ekkostudio.xyz/#/docs/getting-started ；repo 內 README、`docs/workflow.md`、`docs/native-coding-agents.md`、`LICENSE`（BSL-1.1）
- 對照的是 2026-10-10 的 GitHub `main` 說明與節點型別，沒有安裝或執行。
