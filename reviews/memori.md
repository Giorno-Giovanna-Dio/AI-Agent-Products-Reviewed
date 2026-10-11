# Memori

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memori-cell-fb52/reviews/memori.md)
>
> Cell ID：[https://github.com/MemoriLabs/Memori](https://github.com/MemoriLabs/Memori)
>
> Status：`untried`
>
> Category：Agent memory / SQL memory layer
>
> Last updated：2026-10-10

## 產品介紹

Memori 是 MemoriLabs 的 agent 記憶層。它站在你已經在用的程式和模型之間：對話照常送出，Memori 先記下這一輪是誰、哪個 agent、哪一段工作，再把整理過的事實留在你自己的資料庫，或留在他們的 Memori Cloud。下一輪要回答之前，相關的幾條會被找出來，放進這一次的上下文。

它給已經有 agent、卻每次都從空白開始的人用。接法有兩條。Python 或 TypeScript SDK 把現有的模型客戶端包起來，呼叫時自動記下、自動召回。MCP、OpenClaw 外掛和 Hermes provider 則把寫入留在背景，要不要翻記憶由 agent 自己呼叫工具。本 Cell 尚未實際跑過。

## 主要 Features

### 沒有歸屬，就沒有記憶

每筆記憶掛三個名字。Entity 是人、組織，或被記住的對象。Process 是哪一個 agent 或程式。Session 是這一串相關的模型呼叫。SDK 預設給一個 UUID，也可以自己換段，或接回舊的一段。官方寫明：不設 attribution，Memori 不會做記憶。

範圍不一樣。關於這個人的事實、偏好、技能，以及知識圖譜，跟著 entity 走。同一個使用者的客服 agent 和銷售 agent，看得到同一份事實。Process 自己的屬性留在那個 agent。原始對話綁在 entity、process、session 三者一起。

OpenClaw 與 MCP 另外用 project 把專案隔開。這次讀到的 BYODB 資料表是 entity、process、session，沒有 project 欄。兩邊的 session 算法也不一樣：MCP 文件把 session 寫成 entity 加上 UTC 的年月日時。不能假設 SDK 的一段對話和 MCP 的一段是同一條。這一點尚未實測。

### 回答前先看筆記，整理放在後面

SDK 包住模型客戶端之後，每次送出前會抽出使用者這句話。有 entity 才繼續。它用這句話去找這個人的事實，過了相關門檻的才寫進 system prompt，放在 `memori_context` 裡，並註明只有相關才用，同時可以附上摘要。沒有 entity，或沒有過門檻，這次呼叫維持原樣。

回應回來後，原始對話先存進資料庫，這段不擋回答。進階萃取在背景把對話收成事實、偏好、技能、規則、事件、人、關係，以及主詞—謂詞—受詞。短腳本若要等萃取做完，文件建議再等它結束。文件寫這套萃取預設免帳號但有額度，提高額度要 API key。向量在本機用 fastembed 的 all-MiniLM-L6-v2 產生；2026-05 的 changelog 寫明已拿掉 sentence-transformers。萃取用的模型是否整段在你的機器上跑，尚未確認。

MCP、OpenClaw、Hermes 把寫和讀拆開。寫入在回合之後發生。讀取要 agent 自己叫 `memori_recall`。回合開始可以叫 `memori_recall_summary` 看較寬的狀態。上下文被壓縮之後，`memori_compaction` 給一份恢復用的簡報：進行中的任務、還沒關上的迴圈、常設指令、環境、上一動和下一動。這是這一班桌面上該留的工作狀態。這個人長期的事實是另一疊紙。

召回還可以帶來源和訊號，而且必須成對，例如決定配上 commit、執行配上失敗、任務配上結果。這是在說「我要哪一種記憶」，不是把整段歷史倒出來。

### 事實住在 SQL，向量是欄位

BYODB 把表建在你已經有的庫：SQLite、PostgreSQL、MySQL、MariaDB、Oracle、CockroachDB、TiDB、OceanBase，也接受 MongoDB，以及 Neon、Supabase、RDS 這類相容引擎。事實表同時有內文和 embedding 欄位。召回時先取出這個 entity 的向量（有上限），在行程內做餘弦相似度，再和詞彙重疊混成排序分數，取前幾條。產品要你準備的是自己的資料庫，不是另外一套向量資料庫。Memori Cloud 則把儲存與搜尋交給他們的 API。

知識圖譜是同一套庫裡的 subject、predicate、object。同一條關係重複出現會累計次數，也可以直接用 SQL 查。預設召回注入走的是事實與向量。文件把圖譜寫成可供語意搜尋；沿圖走出一段推論再回答，有沒有進到預設路徑，尚未確認。

文件也寫記憶會依重要性衰退。表上有出現次數和最後出現時間。現行 Python 排序讀得到的是相似度加詞彙分數。時間怎麼把舊事實往後推，這次沒有看成一條明確公式，衰退行為尚未確認。

### 做過的事，和說過的話

官方主標是記憶來自 agent 做了什麼，不只是說了什麼。Agent trace 要把工具呼叫、決定、步驟和結果收成兩種東西：可查的結構化紀錄，以及一直更新的摘要。MCP 的進階萃取可以另帶 trace。OpenClaw 外掛宣稱會在每回合之後抓住工具、決定和結果。

同一份程式也寫明，對話訊息只存角色和內文，工具呼叫的欄位沒有留在重播用的歷史裡。召回舊對話時會拿掉會讓模型拒絕的工具列。所以「記得做過的事」比較像萃取之後的事實，以及外掛留下的 trace，而不是把原始工具往返再播放一次。SDK 自動路徑實際提煉到什麼程度，尚未確認。

### 舊頁上的 conscious／auto，現行樹裡沒有這個名字

官網仍有一頁 Dual Memory Modes。它講 conscious ingest：啟動時把標成 conscious-info 的記憶抄進短期，整段對話固定帶著。也講 auto ingest：每一問再抓幾條相關記憶。那頁的建構方式是另一套參數，和現在的 `Memori(conn=...).llm.register` 不同。現行 repo 的 `memori/` 與 `docs/` 搜不到這兩個開關。那一頁不能當成現在的行為。

現在對得上的分法是：SDK 在每次呼叫前自動注入，接近「每一問都查」。MCP 與外掛要 agent 自己決定要不要打開抽屜。一輪開始就該在桌上的那疊，比較像 summary 和 compaction 簡報。

## 主打賣點

Memori 最想被記住的是：記憶先標明是誰的、哪個職位寫的、哪一段工作，然後在回答前用到，而且可以留在你已經在跑的資料庫。

和 [gbrain](https://github.com/garrytan/gbrain) 的差別在產物。gbrain 用清楚的記憶動詞，交出有來源的綜合回答，並指出還缺什麼。Memori 交出掛在人與職位上的事實列，由 SDK 放進下一次 prompt，或等 agent 來取。綜合答案和缺口清單不是它的主要介面。

和 [Hindsight](https://github.com/vectorize-io/hindsight) 的差別在邊界。Hindsight 以 memory bank 為一顆腦，retain、recall、reflect 分開，觀察會被加強或削弱，開工可以先讀整理好的頁。Memori 的邊界是 entity、process、session，存在你的 SQL 或他們的 Cloud。文件裡沒有一個對等的 reflect 步驟去長出心智模型。

LoCoMo 的準確率、token 節省，以及企業案例裡的花費數字，都是官方說法。本 Cell 沒有重跑，不把它們當成已經核對過的差異。

包很多模型、用很少行程式接上，是攔截呼叫的包裝。這裡真正不同的地方，是歸屬、SQL 裡的事實，以及「自動放進 prompt」和「agent 自己召回」這兩種時機。

## 使用情境

### 同一位客人，不同櫃台

- 適合誰：同一個使用者會碰到客服、銷售、導覽好幾個 agent 的產品。
- 在什麼情況使用：客人告訴其中一個櫃台自己的偏好或環境。
- 帶來的價值：事實跟著人走，另一個櫃台不用重問。每個櫃台自己的對話留在自己的 session。

### 寫程式的 agent 換一輪還認得這個專案

- 適合誰：用 Claude Code、Cursor、Codex，或 OpenClaw、Hermes 的人。
- 在什麼情況使用：新的一輪開始，或上下文被壓縮、工作做到一半。
- 帶來的價值：先召回慣例與決定。壓縮之後用簡報接回未完成的任務，而不必把整段聊天再貼一次。

### 一件多步驟的工作要算同一段

- 適合誰：一個 agent 會連續呼叫多次模型才做完一件事的人。
- 在什麼情況使用：同一次任務裡的步驟應該記在一起，下一件任務再換段。
- 帶來的價值：session 把這一件事和下一件事分開。長期事實仍然留在這個人身上。

## 我們可以學什麼

值得借鑑的是三層紙。人的檔案（entity 的事實）跟著人走。職位的筆記（process）留在那張桌子。這一輪的對話，以及壓縮後的簡報，是這一班桌面上的工作狀態。員工開口前要先看過其中一層。

在 2D workspace 裡，這是平面的辦公室、地圖或樓層。記憶長成桌上的幾張便條：事實、日期，以及這張便條屬於這個人還是這個職位。客人從客服桌走到銷售桌，人的檔案還在；上一張桌子的對話稿留在原位。上下文被清掉時，桌上留下一張簡報：還沒做完的事、常設指令、下一步。人看到的是員工開口前低頭看便條。Memori Cloud 的 Memories、Analytics、Playground 是儀表板，可以當牆上的紀錄。我們要做的工作空間是那張桌子和那些便條。

在 3D workspace 裡，這是可以走進去的辦公室，例如 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。員工進房時已經認得這個人，也認得這間房裡做過的決定，訪客不必從頭自我介紹。你看得到他在開口前停在桌邊讀卡片。SDK 那種每次都自動注入，像員工說話前一定會看桌上那疊。MCP 那種自己呼叫 recall，像他可以選擇要不要打開抽屜。辦公室應該讓人看見這個選擇：有時憑已經攤開的簡報回答，有時起身去查。圖譜若要出現，是牆上連結人與決定的別針。員工仍然是先讀便條再說話的那個人。

進階萃取綁額度與 API key。辦公室若要自己的記憶，要先弄清整理這一步發生在哪裡。工具呼叫的原文沒有進對話重播；若要記得做過的事，要另外留執行痕跡。官方基準數字和那頁已經對不上現行程式的 conscious／auto 開關，留在研究紀錄即可。

## 初步看法

- 最有價值的部分：人、職位、這一輪分開，而且召回發生在回答之前。自動注入和 agent 自己去查，是兩種可以放進辦公室的動作。
- 最大限制或疑問：尚未使用。衰退公式、圖譜是否參與預設召回、SDK 是否真的把工具結果提煉成事實，都尚未確認。官網舊的雙模式頁容易讓人以為現在還有 conscious 記憶。LoCoMo 與企業節省數字只停留在官方敘述。
- 是否值得進一步研究或親自體驗：值得把歸屬和「開口前看便條」帶進 workspace 設計。若要體驗，放在本 repository 以外，優先看同一個人跨兩個 process 時事實是否共用、對話是否隔離，以及壓縮後的簡報能不能接回工作。

## Sources

- Official website：https://memorilabs.ai
- Repository：https://github.com/MemoriLabs/Memori
- Documentation：https://memorilabs.ai/docs/memori-byodb/ 與 https://memorilabs.ai/docs/memori-cloud/
- License：Apache License 2.0（repository `LICENSE`）
- 舊的 Dual Memory Modes 頁（API 與現行原始碼不符，不當作現況）：https://memorilabs.ai/docs/core-concepts/overview/
- 官方 LoCoMo 論文（本 Cell 未重跑）：https://arxiv.org/abs/2603.19935
