# memsearch

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memsearch-cell-fb52/reviews/memsearch.md)
>
> Cell ID：[https://github.com/zilliztech/memsearch](https://github.com/zilliztech/memsearch)
>
> Status：`untried`
>
> Category：跨平台 agent 記憶／搜尋索引
>
> Last updated：2026-10-10

## 產品介紹

memsearch 是 Zilliz 做的記憶層，讓已經在用的 coding agent 共用同一份專案記憶。官方外掛涵蓋 Claude Code、Codex、DeepSeek Harness、OpenClaw、OpenCode。人裝上外掛之後照常聊天。回合結束時，外掛把這一輪收成短摘要，追加到當天的 Markdown。需要舊決定時，agent 自己去查，把少數片段帶回來。

留下的記憶是人讀得懂、可以手改、可以進 git 的 `.md` 檔。Milvus 是旁邊的影子索引：目錄壞了，從 Markdown 重建即可。做自己 agent 的人可以用同一套 CLI 或 Python API，不必先裝那五個外掛。本 Cell 尚未實際安裝或查詢。

## 主要 Features

### 日誌是記憶，索引是目錄

外掛把摘要寫進專案下的 `.memsearch/memory/`，一天一個檔，並在條目上留下 session 錨點。掃描器只收 `.md` 與 `.markdown`。程式碼與一般原始檔不會進索引。人若把整個目錄交給索引，進來的仍是 Markdown；gitignore 只決定哪些 Markdown 要略過。

預設會跳過隱藏路徑。外掛是把 `.memsearch/memory` 這個目錄本身當成索引根，所以裡面的日誌會被收進去。原始逐字稿留在各平台自己的 session 檔，不進向量庫。

### 先抽卡片，再翻整節，最後才看原文

查詢走混合檢索：語意向量、BM25 關鍵字，再用 RRF 把兩份排名合成一份。回傳的是帶來源路徑與行號的片段。不夠時展開該段 Markdown；還要核對原話或工具呼叫，才沿著錨點去讀該平台的逐字稿。Claude Code 上這段查詢跑在分出去的子工作，主對話只收下整理後的結果。

查的人主要是正在工作的 agent。人可以下記憶召回，或直接問「我們之前怎麼決定的」，由 agent 判斷要不要去查。人自己讀日誌、手改日誌。DeepSeek Harness 另外有一個唯讀記憶瀏覽，用來看 `.memsearch/` 裡的檔。做 agent 的人則用 CLI 或 Python API 查。

### 同一專案一櫃，換 agent 不用重講

每個外掛依專案路徑導出自己的 collection，預設不跟其他專案合併。同一個專案裡，Claude Code 寫下的摘要，Codex 或 OpenCode 可以查到，前提是它們指向同一份 Milvus。要跨專案共用，得自己指定同一個 collection。本機預設是單一檔案的 Milvus Lite，加上本機 embedding；不另選雲端時，日誌留在自己的機器。伺服器或 Zilliz Cloud 是同一套位址換後端。換 embedding 模型會讓整櫃的識別碼失效，等於搬一次櫃。

### 耐久筆記與技能草稿預設關閉

除了當天日誌，還有兩層要人打開才會跑。`PROJECT.md` 與 `USER.md` 寫在 `.memsearch/`，分別放專案近況與個人偏好。重複的流程可以蒸餾成技能候選，放在 git 追蹤的 `.memsearch/skill-candidates/`。安裝前，agent 不會把候選當成可用技能。安裝是人的動作。蒸餾時會去讀原始逐字稿裡的命令；讀不到就不猜。這兩層仍是 Markdown。`PROJECT.md` 寫在日誌目錄外面，外掛預設索引的是日誌目錄。這兩份筆記會不會自動出現在同一份搜尋結果裡，尚未確認。

## 主打賣點

它想被記住的是：五種 coding agent 共用一份人能改的 Markdown，向量庫只是可丟掉的目錄。

[gbrain](https://github.com/garrytan/gbrain) 把記憶做成會回答的腦：抽出人物與關係，交回有來源的綜合判斷。[Hindsight](https://github.com/vectorize-io/hindsight) 把新資訊整理成觀察與心智模型，主動作是學習。[cognee](https://github.com/topoteretes/cognee) 那一類把實體和關係建成知識圖譜，圖譜本身就是記憶。memsearch 的主線是搜尋索引：追加日誌，查出片段，綜合留給當下的 agent。

[ai-memory](https://github.com/akitaonrails/ai-memory) 也把 Markdown 當真實來源、索引可重建。它會把 session 收成可改寫的專案 wiki，並用一次只能領一次的 handoff 把工作交給下一個 CLI。memsearch 的交接是另一個 agent 來查同一櫃日誌。[Octop Memory](https://github.com/TencentCloud/octop-memory) 有晉升閘門，對話要變成事實才進櫃。memsearch 的寫入是摘要追加，不去裁決這句該不該升格。

混合檢索、本機向量庫、雲端向量庫，是既有搜尋能力換上 Markdown 外殼。跨平台外掛，以及目錄壞了可以從檔案重建，才是它和一般語意搜尋的差別。技能蒸餾與 PROJECT／USER 是後來加上的選配層，預設關閉，還沒取代日誌索引。

## 使用情境

### 同一專案換工具，舊決定還在

- 適合誰：同一份程式在 Claude Code、Codex、OpenCode 之間切換的人。
- 在什麼情況使用：換工具或開新 session，不想重講架構取捨。
- 帶來的價值：新 agent 查的是同一專案的日誌櫃，而不是把上一段 chat 貼進 prompt。

### 接著上次的除錯

- 適合誰：長時間修同一類問題的開發者。
- 在什麼情況使用：要找回上次 Redis、部署或權限問題怎麼處理，以及當時談過哪些檔。
- 帶來的價值：先拿到當天摘要裡的幾張卡片；不夠再展開那一節，或打開原始逐字稿。日誌裡提到的檔案來自對話摘要。原始碼本身沒有被索引。

### 人要核對 agent 記得什麼

- 適合誰：不放心記憶藏在資料庫裡的人。
- 在什麼情況使用：想看、改、或用 git 追某一天的摘要；或決定哪個重複流程可以變成技能。
- 帶來的價值：記憶本體是普通 Markdown。改檔就是改記憶。技能候選在安裝前不會自己生效。

## 我們可以學什麼

- 值得借鑑的 product idea：辦公室的記憶本體是人讀得懂的日誌。搜尋索引是可以重建的目錄抽屜。查詢分成三步，先卡片、再整節、最後原文，避免把整櫃倒進當下的工作。
- 值得借鑑的 interaction / workflow：員工做完一輪，把摘要放進檔案櫃。下一位需要舊決定時，自己去查，只把用得上的幾頁放到自己桌上。重複流程要變成技能時，先以草稿存在櫃裡，等人點頭才裝到該員工身上。
- 在 2D workspace 裡會變成什麼：一層平面辦公室或樓層。檔案櫃在樓層一角，抽屜按日期放 Markdown。員工坐在自己的桌子上做事。需要舊決定時，從櫃子抽出幾張卡片，放到桌上。那些卡片是搜尋結果，是員工拉到桌上的資料。地板仍是這層樓的工位與人在哪裡工作。Milvus 是櫃子後面的目錄抽屜，人平常面對的是紙本日誌。
- 在 3D workspace 裡會變成什麼：一間走得進去的辦公室，像 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你會看到一位員工起身，走到後面的檔案室，抽出幾頁，再走回自己的座位，把它們攤在桌上。換工具的同事進同一間檔案室。你看見的是誰去查、查完回到哪張桌子。檔案室裡是紙本日誌和走過去的人。向量留在目錄抽屜，不會被畫成可以旋轉的點雲。DeepSeek Harness 的記憶瀏覽，以及其他 ADE 側欄，是櫃檯後面的螢幕，用來讀檔。那塊螢幕不是這間走得進去的辦公室。
- 不值得照搬或需要重新設計的地方：向量庫不該畫成辦公室的地板，搜尋儀表板也不該代替檔案室。技能蒸餾和 PROJECT／USER 預設關閉，不該在空間裡自動變成新員工或新規定。換 embedding 模型等於整櫃重做。不同專案預設各用各的櫃。官方說的跨平台共用，指的是同一專案上的那五個 agent。召回準不準、摘要會不會丟掉關鍵命令，尚未確認。Cursor 不在官方外掛名單裡。

## 初步看法

- 最有價值的部分：記憶和索引分開。2D／3D 辦公室可以把檔案室和桌上的摘錄分成兩個地方。員工去檔案室，再把結果帶回來。
- 最大限制或疑問：尚未實測召回品質、摘要是否夠用、同一專案換平台是否真的不用額外設定。`PROJECT.md` 是否進同一份索引，尚未確認。先前一份候選清單把它寫成泛用的語意搜尋，並猜測授權為 Apache-2.0。上游授權是 MIT。產品是 Markdown 日誌，加上可重建的 Milvus 索引。
- 是否值得進一步研究或親自體驗：值得先把它當檔案室的目錄來學。若要體驗，放到本 repo 外面。優先看一件舊決定能否被另一個 agent 查到，以及人改 Markdown 之後索引是否跟上。

## 後續補充（選填）

Not tried yet。沒有把 upstream clone 進本 repo。研究依據是官方文件、README，以及掃描器只收 Markdown 的實作。若之後要跑，建議放在 `/workspace-labs/memsearch`。

## Sources

- Repository：[https://github.com/zilliztech/memsearch](https://github.com/zilliztech/memsearch)
- Documentation：[https://zilliztech.github.io/memsearch/](https://zilliztech.github.io/memsearch/)
- Architecture：[https://zilliztech.github.io/memsearch/architecture/](https://zilliztech.github.io/memsearch/architecture/)
- Design philosophy：[https://zilliztech.github.io/memsearch/design-philosophy/](https://zilliztech.github.io/memsearch/design-philosophy/)
- Comparison：[https://zilliztech.github.io/memsearch/home/comparison/](https://zilliztech.github.io/memsearch/home/comparison/)
- Skills from memory：[https://zilliztech.github.io/memsearch/home/skills-from-memory/](https://zilliztech.github.io/memsearch/home/skills-from-memory/)
- License：MIT（upstream `LICENSE`）
