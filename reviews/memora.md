# Memora

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memora-cell-fb52/reviews/memora.md)
>
> Cell ID：[https://github.com/agentic-box/memora](https://github.com/agentic-box/memora)
>
> Status：`untried`
>
> Category：Shared MCP memory store
>
> Last updated：2026-10-10

## 產品介紹

Memora 是 [agentic-box/memora](https://github.com/agentic-box/memora) 的 MCP 記憶服務。Agent 透過工具把事實、待辦、問題和文件放進同一份庫，下次開工再依主題把還有效的內容取出來。官方用語是「持久的集體記憶」。它自己不派工、不決定下一步；倉庫裡的 SOUL 把角色寫成記憶層：存、找、連結、摘要，並保留來源編號讓呼叫方自己核對。

這份筆記只談這個 Memora。MemoriLabs 的 Memori、以及另一個 Memory OS，是別的產品，名字不要混用。PyPI 上要裝的套件是 `memora-mcp`；裸套件名 `memora` 是無關專案。

「集體」在這裡的意思，是多個 MCP 客戶端可以指向同一個 store。沒有員工名冊，也沒有把一件工作從某人交到下一個人的協定。範圍分成三層：整庫（網址選定，session 黏住）、庫內專案（要明示，不再用關鍵字猜測）、以及 server 行程上的工具面（leader 與 worker 兩種部署設定）。同步則是整份資料庫檔案或遠端資料庫，兩台 agent 同時改同一份雲端 SQLite 時會衝突，不會自動合併成共同記憶。

閱讀時三個入口不是同一版，尚未實測一般安裝會落到哪一版。GitHub `main` 停在 v0.4.6（`c5624e8`，2026-09-23）。最新 GitHub Release 是 v0.5.5（2026-09-24），標籤續到 v0.5.7（2026-09-25），都在 release 分支，尚未回到 `main`。PyPI 的 `memora-mcp` 在 2026-10-10 仍是 0.3.3。下面的產品概念以 `main` 的文件與程式為準，0.5 只在它改變同步與寫入方式時另外標出。

## 主要 Features

### Absorb：寫入前先分類，舊知識留下譜系

新事實進庫時，模型會把每一則判成重複、更新、矛盾、相關或全新。重複略過；更新會產生新版本並標成取代舊版；矛盾與相關用有類型的邊連起來；一次送進的相關事實可以合成一則較完整的記憶。可以先 dry run 看會發生什麼。

讀取預設跟著目前有效的版本走。指定 id 時可以走到譜系末端，或把整條歷史拿出來。一段敘述不能取代待辦或問題：類型不同就降成相關連結，任務不會因為一句「確認過了」從有效清單消失。這是 0.4.6 寫明的閘門。

### 三層範圍：庫、專案、工具面

一台行程可以用登錄表同時服務多個 store。客戶端靠網址路徑 `/mcp/<name>` 選庫，選擇綁在 MCP session 上。開在某一庫的 session 再拿去打另一庫，仍然落在原來那庫。選錯網址時工具一樣成功，寫入會進別的專案；官方要呼叫方用統計結果核對自己實際連到哪一庫。

庫裡面還有專案。專案來自明示參數、記憶自己的 metadata，或恰好一個已設定的專案標籤。0.4.5 拿掉用內文關鍵字猜專案的做法，因為像 embedding、workspace 這類詞會把記憶歸錯櫃，還連帶讓錯誤的更新蓋掉舊知識。搜尋與 JSON API 可以依專案過濾。沒設定專案清單時，就不會從標籤推專案。

第三層是工具面，用環境變數選整台 server 暴露哪些工具。worker 設定看得到吸收、搜尋、連結、建立與更新，以及待辦和問題。主題摘要、文件、區段、刪除和較完整的列表留在 leader 設定。這是行程或容器的設定。文件寫明：同一台容器要同時服務領班和工人時，必須用較寬的那一組，否則領班會失去摘要。它還沒有「這個人是領班」的 session 身份。

### 主題摘要是開工時的一疊資料

`memory_digest` 依主題打包還有效的搜尋命中、譜系、相關記憶編號、對得上的待辦和問題，並附上原始 id。官方把它定義成聚合，預設不做模型綜述；綜述參數保留著，尚未開啟。呼叫方覺得太寬或太窄時，可以拿 id 回去看原文。leader 工具面才有這個摘要。worker 工具面沒有，所以「誰負責做開工簡報」目前是部署選擇。

### 同步與事件：資料庫怎麼移動，以及那只門鈴

`main` 上的雲端路徑有兩種。S3／R2 是把整份 SQLite 下載到本機快取，寫完再上傳。遠端檔案的版本標記變了就拒絕覆蓋，要另外把遠端拉下來。這是單寫者的檔案同步。D1 則是遠端資料庫本身，多個客戶端打同一台 server 時可以共寫一庫；D1 上的整庫取代沒有交易，中斷時可能留下半套資料，官方要保留匯出檔再跑一次。

0.5.0 起的 release 把本機 SQLite 當成主檔，再依序複製到 D1，並把圖形檢視改成唯讀。0.5.7 讓 JSON API 的 absorb 在本機主檔上成為一次交易、同一把冪等鍵重送會得到同一份結果。`main` 上的同一條 API 仍會拒絕寫入。這些行為還沒在 `main` 的 README 裡，也尚未實測。

事件比較窄。功能列表寫「agent 之間的通訊」。程式只在記憶帶有 `shared-cache` 標籤時寫一筆事件，內容是記憶 id、標籤與時間。別人用 poll 取走，並可把事件標成已讀。已讀是整庫一顆旗標。這像櫃門上的門鈴，呼叫方要自己約定那個標籤。它有沒有被拿來做交接，尚未確認。

### 圖譜是看這櫃記憶的窗子

本機或 Cloudflare 上有互動圖譜、時間軸、操作歷史，以及對記憶提問的聊天面板。0.5.1 的 release 還加了 force-graph 的 2D／3D 視角。那是記憶節點的圖，用來瀏覽這一櫃裡有什麼。人的工作仍發生在呼叫 Memora 的 agent 裡。

## 主打賣點

- 它最想被記住的是：很多 agent 可以共寫一份會去重、會留譜系的記憶，再用主題摘要把現行內容取出來。
- 和常見替代相比，真正不同的是範圍怎麼切，以及「更新」怎麼不擦掉舊版和任務。庫用網址隔開，專案要明示，工人和領班的工具面可以收窄。事實與待辦分類型，敘述不能取代待辦。
- 語意搜尋、知識圖譜和對記憶聊天，是許多記憶產品都有的檢索與瀏覽。Memora 的圖譜視窗也屬於這一層。
- [gbrain](https://github.com/garrytan/gbrain) 偏有來源的回答，[Hindsight](https://github.com/vectorize-io/hindsight) 偏會整理觀察的 memory bank，[ai-memory](https://github.com/akitaonrails/ai-memory) 偏可認領的專案 wiki 交接，[Octop Memory](https://github.com/TencentCloud/octop-memory) 偏可搬走的事實櫃。Memora 更像一台大家連上去的記憶 server：交接物是摘要包和來源 id，同步物是整庫或遠端資料庫。
- 變更紀錄把名為 clmux 的客戶端寫成預期的主要讀取方，並為它加了 JSON API。公開 GitHub 上沒有對到 `agentic-box/clmux`，那個客戶端是否仍在別處，尚未確認。

## 使用情境

### 同一個專案的多個 coding agent 共寫一櫃

- 適合誰：同一台機器或同一個 HTTP 服務上，有多個 Claude Code、Codex 或其他 MCP 客戶端的人。
- 在什麼情況使用：這個 session 做的決定，下一個 session 或另一個 agent 還要用。
- 帶來的價值：大家指向同一個 store、同一個專案。吸收會跳過重複、把過時內容收成譜系，而不是每人各留一份聊天紀錄。

### 領班先拿摘要，工人只負責記入

- 適合誰：已經把工具面拆成 leader 與 worker 兩種部署的人。
- 在什麼情況使用：開工時要一次看到相關記憶、未完成的待辦和問題；工人則把新事實和工單寫回去。
- 帶來的價值：摘要附著來源 id，領班可以點回去核對。若領班和工人打的是同一台寬工具面的 server，這個分工在空間上看不出來，權限也沒有跟著人走。

### 知識更新了，任務板還在

- 適合誰：會把「我們現在相信什麼」和「還沒做完的工單」放在同一庫的人。
- 在什麼情況使用：一段新的說明和一張未完成的待辦很像，但不該讓說明把待辦標成過時。
- 帶來的價值：類型閘門把這種情況降成相關連結。人看到的是兩張對得上的卡片，待辦仍留在有效清單裡。

## 我們可以學什麼

- 值得借鑑的 product idea：共用記憶是辦公室裡的一排櫃子，範圍要先於搜尋。整庫是哪一排、專案是哪一個抽屜、誰看得到摘要，這三層要能分開指。更新留下舊版，任務和事實用不同抽屜，敘述不能把工單抽走。
- 值得借鑑的 interaction / workflow：員工開工時拿到的是一疊帶編號的資料（現行命中、譜系、待辦、問題），覺得不對就拿編號去翻原文。寫入前先分類：重複不佔新卡片，矛盾並排，相關的可以合成一張。session 黏在當初選的那一排櫃子上，對話中途換網址也不該悄悄改寫到別櫃。
- 在 2D workspace 裡會變成什麼：俯視的樓層上有一面板或一排櫃子，人仍在這層地板工作。每一個 store 是一排櫃子。專案是抽屜標籤。員工坐下時，桌上攤開的是主題摘要那一疊，卡片角落有來源編號。有人 absorb 時，新卡片貼上，舊版翻到後面還能翻出來。待辦和問題是另一排夾子，不會被一段說明蓋掉。`shared-cache` 是櫃門上的燈，亮了表示有人放了一張特別標記的卡片；誰把燈關掉是整櫃共用的，所以燈不適合當成「這件事已經交給某人」。圖譜可以掛在牆上當索引。地板上的工作位置仍是這間平面辦公室。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，例如 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走過去就看得到哪些員工共用同一排櫃子，以及誰正站在櫃前寫。領班把摘要拿到會議桌，工人在櫃前放入新卡片。矛盾的兩張卡片並排，等人走過去看。S3 那種整檔衝突，表現成櫃門暫時打不開、要先取回遠端那一份，兩個人不能同時改同一本檔案。force-graph 的 3D 視角留在牆上的檢視窗，辦公室本體是人與員工在場，大家看得到誰在寫哪一櫃。
- 不值得照搬或需要重新設計的地方：把「集體記憶」直接做成多 agent 編制。Memora 沒有人的身份，工具面收窄是整台 server 的開關。把 force-graph 或側欄聊天當成工作空間，會得到一張記憶圖，人仍然不在場。事件的已讀旗標是整庫一顆，不能直接當交接佇列。三個發行入口版本不同，設計共用櫃之前要先選定自己跑的是 `main`、release，還是 PyPI。

## 初步看法

- 最有價值的部分：共用櫃的邊界（庫、專案、工具面）和寫入紀律（去重、譜系、事實不能取代待辦、摘要必須帶來源 id）。這對未來辦公室比「再做一個搜尋側欄」更有用。
- 最大限制或疑問：它是記憶 server，交接仍要呼叫方自己做。worker 預設拿不到摘要。事件是否真的在 agent 之間傳遞，尚未確認。`main`、v0.5.x 與 PyPI 0.3.3 的行為差很多，D1 與本機主檔哪一種才是現在的共寫方式，也尚未確認。clmux 作為主要客戶端的現況尚未確認。
- 是否值得進一步研究或親自體驗：值得先把它當共用櫃的原語來讀。若要 hands-on，優先看三件事：兩個客戶端打同一 store 時專案會不會串櫃、leader 與 worker 打同一台 server 時工具面是否真的分開、以及主題摘要裡的待辦是否還在。實驗放在本 repo 以外即可。

## Sources

- Repository：[https://github.com/agentic-box/memora](https://github.com/agentic-box/memora)（`main` @ `c5624e8`，v0.4.6，2026-09-23）
- 產品自述：README、`SOUL.md`、`agent.yaml`、`memora/tool_profile.py`、`memora/storage.py`（專案解析與 `shared-cache` 事件）
- Changelog：[main 的 CHANGELOG.md](https://github.com/agentic-box/memora/blob/main/CHANGELOG.md) 至 0.4.6；[v0.5.7 的 CHANGELOG](https://github.com/agentic-box/memora/blob/v0.5.7/CHANGELOG.md) 補 0.5.0–0.5.7（尚未合併進 `main`）
- Release：[v0.5.5](https://github.com/agentic-box/memora/releases/tag/v0.5.5)（GitHub Releases 頁面上的最新一筆，2026-09-24）
- Package：[memora-mcp on PyPI](https://pypi.org/project/memora-mcp/)（2026-10-10 為 0.3.3）
- 相關 Cell：[gbrain](https://github.com/garrytan/gbrain)、[Hindsight](https://github.com/vectorize-io/hindsight)、[ai-memory](https://github.com/akitaonrails/ai-memory)、[Octop Memory](https://github.com/TencentCloud/octop-memory)
