# Octop Memory

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-memory-cell-740f/reviews/octop-memory.md)
>
> Cell ID：[https://github.com/TencentCloud/octop-memory](https://github.com/TencentCloud/octop-memory)
>
> Status：`untried`
>
> Category：Portable agent memory runtime
>
> Last updated：2026-10-08

## 產品介紹

Octop Memory 是給 LLM agent 用的長期記憶 runtime。它負責把對話收進來、
抽出值得留下的事實、在下一輪依問題召回一段塞得進 prompt 的上下文，並把
這包記憶搬到下一個宿主。執行 agent、排程與介面仍由宿主負責。

它是 [Octop](https://github.com/TencentCloud/Octop) 生態裡的記憶元件，
也以 Python 套件、CLI 或 JSON-RPC bridge 獨立運作。目前現成接線是
OpenClaw（子行程 bridge）與 Hermes Agent（行程內 provider，文件要求
具備 MemoryProvider 的 v0.10.0 以上）。核心安裝只需 Python 3.12 與
SQLite／FTS5；沒有模型也能手動存事實並做關鍵字召回。PyPI 已有 1.0.0，
套件分類仍標 Alpha，成熟度尚未在本 Cell 驗證。

## 主要 Features

### 對話先當證據，通過晉升才變成事實

寫入分成幾層：原始事件是不可改的證據；候選是從證據抽出的主張；通過檢查
後才成為可被召回的事實（官方稱 AtomCard）。實體頁是事後整理的檔案，標成
dirty 才重寫，而且會保住人類寫的 `## My Notes`。事件摘要走另一條路，從
原始事件整理，不取代事實本身。

晉升會看價值、證據、實體、重複與衝突。閒聊可以丟掉，相同事實可以合併，
互相矛盾的才留下給人處理。候選不必全部等人審。手動 `store` 可以不經模型，
直接寫成事實。

### 召回要塞進這一輪，而不是倒出整個檔案櫃

查詢預設走事實與原始事件的全文檢索，認出實體時補上頁面標題，有向量索引
才加向量。命中已整理的事實時，原始對話退到後面；正在進行的這一輪也不再
灌回 prompt。結果會去重、排序，再依字數預算裁切。對話紀錄搜尋與事實召回
是兩條 API。

### 記憶可以打包，換宿主時帶走

`.hmpkg` 把一份記憶在支援的宿主之間搬移，官方路徑包含 OpenClaw、Hermes，
以及 Octop／舊 harness 工作區裡的 `memory.sqlite`。namespace 把同一後端裡
的不同儲存隔開：SQLite 用表名前綴，PostgreSQL 用共用 schema 裡的 namespace
鍵。換座位時搬的是這包記憶，不是整段 chat。

### 宿主只接線，晉升規則留在 runtime

OpenClaw 與 Hermes 都對模型暴露搜尋與讀取，capture 發生在 hook，而不是
讓模型自己決定要不要寫記憶。OpenClaw 有多種 profile（預設、低延遲、主動
預取、隱私、歸檔）。隱私 profile 會把原始正文換成占位，metadata 仍可能
留下。自動提煉在 OpenClaw 需要設定 LLM endpoint；沒有模型時只留下原始層。
Hermes 文件把同步回合定義成背景 capture；它是否會自己跑到事實層，尚未
確認，不能假設與 OpenClaw 相同。

### 執行進度與長期記憶分開

可選的 LangGraph checkpoint 存在同一類資料庫裡，用來恢復執行狀態。它的
命名空間與記憶 namespace 不同：一個是「這件工作做到哪」，一個是「這間
辦公室記得什麼」。

## 主打賣點

- 最想被記住的是：值得留下的記憶要能跨 session，還要能帶到下一個 agent。
- 真正不同的地方是晉升閘門與可攜套件。事實要有證據；衝突才打斷人；換
  OpenClaw、Hermes 或 Octop expert 時，帶走的是同一份庫，而不是把聊天
  紀錄貼進新 prompt。
- [gbrain](https://github.com/garrytan/gbrain) 把記憶做成動詞與有來源的
  綜合回答。[Hindsight](https://github.com/vectorize-io/hindsight) 把記憶
  做成會整理觀察與心智模型的 server。[ai-memory](https://github.com/akitaonrails/ai-memory)
  把 coding CLI 的專案知識做成可共享的 wiki。Octop Memory 更像每個 agent
  可搬走的檔案櫃，檔案櫃的規則（晉升、預算、隔離）是產品本身。
- 向量檢索與原始碼裡的 dashboard 比較像既有記憶產品的附加面。官方寫明
  真實 Chroma／Qdrant 尚未驗過，而且 dashboard 不進 PyPI wheel。全文檢索、
  晉升與 `.hmpkg` 才是現在能靠文件說清楚的主線。
- 發行入口已從舊的 harness-memory 命名改為 octop-memory。官方遷移說明寫
  資料格式、內部表與 checkpoint 編碼維持不變。舊 GitHub
  `TencentCloud/harness-memory` 目前是 404，所以 Cell ID 只用現在的 repo。

## 使用情境

### 個人助理記住偏好，下一輪不用重講

- 適合誰：自架 Octop，或把 OpenClaw／Hermes 當日常助理的人。
- 在什麼情況使用：同一個人跨很多 session，偏好與決定會累積。
- 帶來的價值：沒有模型也能先存事實並用關鍵字找回；有模型時才把對話提煉
  成可引用的事實。

### 員工換桌子，檔案櫃跟著走

- 適合誰：已經在一個宿主累積記憶，想改用另一個 agent 宿主的人。
- 在什麼情況使用：從 OpenClaw 換到 Hermes，或在 Octop 的 expert 工作區
  之間搬記憶。
- 帶來的價值：`.hmpkg` 與明確的 sqlite 路徑讓記憶成為可交接的物件，換
  宿主不必從空白開始。

### 只有事實打架時才叫人來看

- 適合誰：不想每句對話都審核，又怕 agent 悄悄改寫舊決定的人。
- 在什麼情況使用：新說法與已存事實衝突，或證據對不上。
- 帶來的價值：人處理的是衝突與修正；修正會留下後繼事實並讓舊事實失效，
  而不是默默覆蓋。實體頁上人寫的筆記在重寫時被保留。

## 我們可以學什麼

- 值得借鑑的 product idea：把「這一輪對話」、「辦公室相信的事實」和
  「工作做到哪」分成三樣東西。記憶是可搬走的物件，執行 checkpoint 留在
  原座位。
- 值得借鑑的 interaction / workflow：員工開工時只把預算內的相關事實放到
  桌上；進行中的對話不要再灌回 prompt。人的介入點是衝突印章，以及檔案上
  不會被自動重寫的筆記。換宿主等於帶走公事包。
- 在 2D workspace 裡會變成什麼：俯視的檔案室。每個 namespace 是一排櫃子。
  抽屜分別放原始證據、待人看的衝突、已歸檔事實，以及會更新的實體檔案。
  `root → branch → leaf` 是櫃子上的資料夾標籤。員工坐下時，桌上只有這一
  題用得上的薄檔案。門口放著 `.hmpkg` 公事包，換座位時帶走。
- 在 3D workspace 裡會變成什麼：可走進的辦公室後方有一間檔案室。OpenClaw
  與 Hermes 是兩張不同的桌子，共用同一套歸檔規則。員工換桌時拿起公事包。
  互相矛盾的文件放在人的桌上等蓋章。實體檔案架上的卷宗會在標成 dirty 後
  重寫，人寫的那一節留在卷宗裡。LangGraph checkpoint 是螢幕上還沒做完的
  工作；檔案櫃是人離開後辦公室仍記得的事。看得到誰去查檔，重點是人與
  員工在場，而不是把事實畫成可旋轉的圖譜。
- 不值得照搬或需要重新設計的地方：原始碼 dashboard 與 CLI 樹狀檢視是後台
  終端，放進辦公室會變成 ADE 側欄。向量搜尋在官方文件裡仍未對真實後端
  驗收。Hermes 與 OpenClaw 的自動提煉深度不同，不能用同一個「對話結束就
  變成事實」的動畫。隱私模式仍可能留下 metadata，空間上的隔離要另外設計。

## 初步看法

- 最有價值的部分：記憶有晉升狀態、有證據、有搬移單位，而且和 agent
  執行狀態分開。這對 2D／3D 辦公室比「側欄搜尋歷史」更接近共用檔案室。
- 最大限制或疑問：尚未確認召回品質、衝突檢查會不會太容易或太難觸發，
  以及三個審核入口（CLI、bridge、原始碼 dashboard）是否做同一件事。
  向量路徑官方自己標成未驗。和 [Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/octop.md) 本體的
  per-agent sqlite 如何對上 namespace，仍需對照實際部署。
- 是否值得進一步研究或親自體驗：值得先把它當 Octop／OpenClaw 的記憶
  原語來讀。若要 hands-on，優先看一件事能否從原始事件變成事實、衝突是否
  真的等人，以及 `.hmpkg` 換宿主後召回是否還是同一批事實。

## 後續補充（選填）

此 Cell 只做產品研究，沒有 clone，也沒有執行套件。若之後要體驗，建議放在
`/workspace-labs/octop-memory`，不要放進本 repo。

## Sources

- Official website：[https://octop.cloud](https://octop.cloud)（Octop 產品站；本 repo 的 GitHub homepage）
- Repository：[https://github.com/TencentCloud/octop-memory](https://github.com/TencentCloud/octop-memory)
- Documentation：[整合指南](https://github.com/TencentCloud/octop-memory/blob/main/docs/integrations.md)、[詞彙](https://github.com/TencentCloud/octop-memory/blob/main/docs/agent/GLOSSARY.md)、[專案地圖](https://github.com/TencentCloud/octop-memory/blob/main/docs/agent/PROJECT_MAP.md)
- Package：[octop-memory on PyPI](https://pypi.org/project/octop-memory/)
- 相關 Cell：[Octop](https://github.com/TencentCloud/Octop)、[OpenClaw](https://github.com/openclaw/openclaw)、[gbrain](https://github.com/garrytan/gbrain)、[Hindsight](https://github.com/vectorize-io/hindsight)、[ai-memory](https://github.com/akitaonrails/ai-memory)
