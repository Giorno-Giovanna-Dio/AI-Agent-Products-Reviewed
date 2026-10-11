# Memory OS

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memory-os-cell-fb52/reviews/memory-os.md)
>
> Cell ID：[https://github.com/ClaudioDrews/memory-os](https://github.com/ClaudioDrews/memory-os)
>
> Status：`untried`
>
> Category：Agent memory / Hermes memory stack
>
> Last updated：2026-10-10

## 產品介紹

Memory OS（官方全名 Hermes Agent Memory Operating System）是 Claudio Drews
做給 Hermes Agent 的本機記憶層，授權是 MIT。公開的預設分支 `main` 停在標籤
v0.2.0、commit `e03db1f`（2026-06-10）。它把工作區檔案、對話資料庫、事實庫、
跨 session 紀錄、向量檢索，和一份會自己長頁的 wiki，接在同一個 Hermes 上，
並在每次呼叫模型前塞進一小段相關舊事。

使用者要先有 Hermes Agent，再用安裝腳本裝上 Icarus 外掛、SQLite，以及
Docker 裡的 Qdrant、Redis、ARQ worker。語言模型可以沿用 Hermes 已支援的
供應商。這份筆記談的是這個 repo。它不是 Memori（MemoriLabs），也不是
Memora（agentic-box）。

## 主要 Features

### 層次底下是檔案、資料庫和背景服務

官方圖有七層。拆開之後，真正在跑的是這幾樣：

- **檔案。** 工作區的 `MEMORY.md`、`USER.md`、`CREATIVE.md` 每回合進
  system prompt。跨 session 的 Fabric 是帶 frontmatter 的 markdown。Wiki
  是 vault 裡的 concepts、entities、comparisons。身份規則寫在 `SOUL.md`
  和 `rulebook.md`。
- **資料庫。** 對話在 `state.db`（SQLite + FTS5）。可累積信任分數的事實
  在 `memory_store.db`。語意檢索在 Qdrant 的 `knowledge_base` collection。
- **背景程序。** Qdrant、Redis、ARQ worker 是三個 Docker 服務。安裝腳本
  會加一條每小時的 wiki ingest cron。衰退、去重、vault curator 在架構文件
  裡是建議排程，沒有全部寫進這支安裝腳本。
- **名字，而不是 namespace。** `HERMES_AGENT_NAME` 會印在 Fabric 條目上。
  沒設的時候，條目都叫 `agent`。多個 collection 可以並存在同一台 Qdrant，
  這套預設只用 `knowledge_base`。

公開版本沒有作業系統的 process、cgroup 或 Linux namespace。看得到的邊界
是服務自己的：Redis 密碼、可選的 Qdrant API key、worker 把 wiki 掛成唯讀、
session 搜尋用唯讀 SQLite、ingest 拒絕跑出 wiki 目錄的路徑。Ground Truth
規定終端機輸出、注入記憶、官方文件、訓練知識誰比較權威。那是來源排序。

### 呼叫前注入，並要求 agent 當真

Icarus 在 `pre_llm_call` 從 Fabric、Qdrant、舊 session、事實庫各取一點，
標成 `[fabric]`、`[qdrant]`、`[sessions]`、`[facts]`。同一 session 不重複
貼；太短的客套話整段跳過。作者在 Layer 7 寫道：身份文件若沒把這些區塊列為
權威，agent 仍會再搜一次，表現得像沒有記憶。2026-06 的補記又說，規則在
prompt 裡也不保證會照做，所以他們加了一段動手之前先清點注入內容的步驟。
這段行為尚未在本 Cell 驗證。

### 事實要回饋，wiki 靠班表長大

事實可以新增、全文搜尋、按實體查看、標有幫助或沒幫助。信任分數要有回饋
才會離開起點 0.5。Wiki 把 raw 文件收成有 frontmatter 的頁，再由每小時的
ingest 送進 Qdrant。週期性的 decay 會歸檔較舊、較不重要的 AI 內容；太像的
向量會合併。這是倉庫保養。

### 跨 agent 交接是檔案上的指派

`fabric_write` 可以把未完成的事 `assigned_to` 另一個名字，或用 `agent:id`
標出正在 review 哪一筆。`fabric_pending` 列出指給自己的事。交接物是共用
目錄裡的一張 markdown，上面有名字。公開 main 沒有做成「B 讀不到 A 的抽屜」。

## 主打賣點

- 它想被記住的是：Hermes 下一個 session 還認得專案、決定和理由，而且注入
  進去的內容要被當成已經知道的事。
- 對照 [gbrain](https://github.com/garrytan/gbrain) 和
  [Hindsight](https://github.com/vectorize-io/hindsight)，「OS」多出來的
  是層次和打掃班表。gbrain 是可搜尋、可綜合的共用腦。Hindsight 把一顆腦
  放進隔離的 memory bank，還有 retain、recall、reflect。Memory OS 把檔案、
  SQLite、向量和 wiki 排成服務，再用 cron 做 ingest。隔離和權限沒有因此
  變成作業系統。Hindsight 的 bank 更接近「這間房間有主人」。
- 混合向量檢索、信任分數、自動 wiki，是常見記憶產品的再包裝。比較特別的
  是明文的來源權威：終端機狀態、注入記憶、官方文件、訓練知識，衝突時誰贏。
- 它是 Hermes 的外掛加上本機 runtime（Docker 服務與 hook）。它不是任意
  agent 都能 import 的函式庫，也不是會排程員工的核心。

## 使用情境

### 同一個 Hermes 隔天繼續同一個專案

- 適合誰：每天開 Hermes、不想重講技術決定的人。
- 在什麼情況使用：上次修過的設定、選過的工具，下一個 session 還要用。
- 帶來的價值：呼叫模型前先帶入相關舊對話和事實。注入是否真的減少重講，
  尚未實測。

### 兩個有名字的 Hermes 交接未做完的事

- 適合誰：同一台機器上有兩個設了 `HERMES_AGENT_NAME` 的 Hermes。
- 在什麼情況使用：A 寫一筆 open 任務並指定 B，B 用 `fabric_pending` 領走。
- 帶來的價值：交接是一筆有名字的檔案。沒設名字時，條目會塌成同一個
  `agent`。誰不能讀誰的紀錄，公開版本沒有門禁。

### 筆記進 vault 之後，讓班表去索引

- 適合誰：已經把文件放在 vault、希望 agent 搜得到的人。
- 在什麼情況使用：新的 raw 文件要變成 wiki 頁，再進 Qdrant。
- 帶來的價值：整理被排進每小時的 ingest，而不是每次對話臨時做一次 RAG。
  衰退和 curator 仍要自己排程。這條 cron 在乾淨機器上是否寫得進去，尚未確認。

## 我們可以學什麼

- 辦公室的記憶要分層，而且層要有權威。桌面上的檔案、抽屜裡的事實、檔案室
  的 wiki、開工前已經塞到手上的那一頁，不該混成同一個搜尋框。人要看得出
  這一輪用了哪一層、哪一層說了算。
- 開工前先把相關舊事放到員工手上。客套話不必觸發搜尋。同一段工作不重複貼
  同一張紙。另外要有固定班表做歸檔和去重，否則抽屜會滿。
- 在 **2D workspace** 裡，這會是一層平面的樓：每張桌子有自己的抽屜（這個人
  的事實和進行中的 Fabric），走廊盡頭是共用檔案室（wiki 和向量庫）。門上的
  權限是誰可以打開哪張桌子。打掃的人沿走廊收走過期的紙。人仍站在這一層樓
  上看誰在哪張桌子工作。
- 在 **3D workspace** 裡，這是可以走進去的辦公室，像
  [Agent Office](https://github.com/AgentSystemLabs/agent-office)。員工坐
  下來可以打開自己的記憶：手上已經有注入的那幾張紙。權限是誰可以走進誰的
  辦公室。共用 wiki 是一間檔案室，被允許的人走進去查。背景整理看起來像有人
  在檔案室歸檔。
- 這套層次不該畫成 Conductor、Firstmate、Maestro 那種 ADE dashboard。
- 不值得照搬整座 Docker，也不該因為名字叫 OS 就以為已經有 process、
  namespace 或門禁。Ground Truth 目前是寫進 `SOUL.md` 的句子；作者自己
  記錄過規則在、行為仍走捷徑。未來的辦公室要的是抽屜、門和權威順序。

## 初步看法

- 最有價值的部分：把「存得到」和「被當成真的」拆開，並讓整理工作有班表。
- 最大限制或疑問：OS 是比喻。公開 main 的多 agent 是檔案上的名字。它只服務
  Hermes。另有未合併分支 `memory-os-corrected-2026-09-20`（2026-09-20，比
  main 超前 42 個 commit），文件自稱尚未發布；裡面多了安裝 profile 分開、
  session 隔離，以及矛盾要人明確化解。那些是否改變產品定位，尚未確認。本
  筆記以預設分支為準。召回品質沒有親自用過。
- 是否值得進一步研究或親自體驗：值得留下「分層記憶、來源權威、打掃班表」
  這個概念。若要體驗，放到 `/workspace-labs/memory-os`，不要把上游 clone
  進這個 repo。

## 後續補充

Not tried yet。閱讀依據公開 `main` @ `e03db1f`（標籤 v0.2.0，2026-06-10）
的 layer 文件、`infrastructure/architecture.md`、`setup.sh` 與 Icarus 的
Fabric 工具。沒有安裝 Hermes，也沒有啟動 Docker。

## Sources

- [Memory OS repository](https://github.com/ClaudioDrews/memory-os)
- [七層說明](https://github.com/ClaudioDrews/memory-os/tree/main/layers)
- [Infrastructure](https://github.com/ClaudioDrews/memory-os/blob/main/infrastructure/architecture.md)
- [MIT License](https://github.com/ClaudioDrews/memory-os/blob/main/LICENSE)
- [gbrain](https://github.com/garrytan/gbrain)
- [Hindsight](https://github.com/vectorize-io/hindsight)
- [Agent Office](https://github.com/AgentSystemLabs/agent-office)
