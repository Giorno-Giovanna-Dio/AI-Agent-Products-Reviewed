# PocketFlow

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-cell-ca43/reviews/pocketflow.md)
>
> Cell ID：[https://github.com/The-Pocket/PocketFlow](https://github.com/The-Pocket/PocketFlow)
>
> Status：`untried`
>
> Category：Python 圖式 LLM 框架（Node / Flow / Shared Store）
>
> Last updated：2026-10-10

## 產品介紹

PocketFlow 是 Zachary Huang 的開源 Python 框架，用來把一次 LLM 應用寫成一張圖。開發者定義 Node，用動作把它們連成 Flow，節點之間靠一份 Shared Store 交資料。它服務的是要自己組流程的工程師，以及官方所說的 agentic coding：人先把流程設計寫清楚，再讓 coding agent 照著實作。官方文件在 [the-pocket.github.io/PocketFlow](https://the-pocket.github.io/PocketFlow/)。本次讀了 repo 的 README、LICENSE、文件，以及 `main` 上的 `pocketflow/__init__.py`，沒有安裝。

這份 Cell 只談這個框架。同一個 repo 裡的 cookbook 是用法範例。Codebase Knowledge、Youtube Made Simple 這類教學 repo 是別的 Cell，這裡只把它們當成固定流程和 map-reduce 的例子。README 列出的 TypeScript、Java、C++、Go、Rust、PHP 移植也是別的 repo。PocketFlow 本身是約一百行的程式庫，辦公室畫面要我們自己畫。

## 主要 Features

### Node：先讀櫃、再計算、再寫回

最小單位是 Node，三步是 prep、exec、post。prep 從 shared 讀資料。exec 做計算（多半是 LLM 或工具），文件規定這一步不要碰 shared。post 把結果寫回 shared，並回傳一個動作字串；沒回傳就當 `"default"`。Node 可設 `max_retries` 和 `wait`（秒）。預設 `max_retries=1`，也就是 exec 失敗不重試。重試用完可改 `exec_fallback` 交一個替代結果。三步把「資料放哪」和「計算做什麼」分開。

### Flow：沿動作往下走

Flow 從一個起點開始，看 post 回傳的動作，走到對應的下一節點，直到沒有路。`>>` 是預設路，`- "動作" >>` 是有名字的岔路，所以可以分支，也可以走回前面形成迴圈。單獨 `node.run` 只跑那一節，不往下走；正式跑要用 `flow.run`。Flow 自己也是 Node，可以嵌進更大的 Flow，父層的 params 會合併進子層。文件裡的例子是報銷審核（核准、退回修改、拒絕）和訂單管線（付款、庫存、出貨三段子流程）。

### Shared Store 與 Params

節點溝通主要靠 Shared Store：一份大家事先講好欄位的資料。簡單時是記憶體裡的 dict，要留存時文件說可以換成資料庫。prep 讀、post 寫。Params 是父 Flow 這次塞進來的短標籤（檔名、編號），給 Batch 認這是哪一件工作，執行中不改。交接就是上一節寫進 store、下一節再讀出來。框架沒有另外的記憶體產品；對話歷史、檢索結果、最終答案都是你自己設計的欄位。

### Batch、Async、平行

BatchNode 把 prep 回傳的清單逐件丟給 exec，最後一次 post。BatchFlow 則是用不同的 params 把同一條 Flow 重跑多遍，例如一檔一輪。Async 節點用 async 的 prep、exec、post，文件舉的用途包含等使用者回饋，以及多 agent 彼此協調；AsyncNode 要放在 AsyncFlow 裡。平行批次用 `asyncio.gather` 同時跑多件 I/O。文件寫明：彼此有依賴就不要平行、注意供應商速率、Python GIL 蓋不住重 CPU。本次讀到的核心檔對同一份 shared 做 gather，沒有看到鎖。搶寫同一欄位會怎樣，尚未實測。

### Workflow、Agent、多 Agent

這些是文件上的組法，不是額外的類別。Workflow 是把一件大事拆成一串節點，例如大綱、撰寫、修訂。拆太粗，一次 LLM 呼叫做不完；拆太細，每一節又失去上下文。Agent 是一張會看 context、從動作清單裡選下一步的節點，選完可以走去搜尋再繞回來，直到選擇回答。文件要的 context 是夠用的一小段（任務、做過的動作、目前狀態）。動作要分得清楚，也可以留「退回上一步」。多 Agent 是進階組法：文件寫多數時候先不要用，溝通用 shared 裡的 queue。範例是兩個 async 節點玩 Taboo，一個給提示、一個猜詞，同時跑、用兩個 queue 傳話。RAG 是離線索引 Flow 加上線上問答 Flow。Map-reduce 用 batch 先拆再合。結構化輸出是請模型交 YAML，再用 assert 檢查，失敗就靠 Node 重試。

### 人的設計與關卡

Agentic Coding 指南把順序寫成：人先釐清需求與流程，寫進 `docs/design.md`（流程、工具、shared 欄位、每個節點的 prep／exec／post），實作交給 coding agent。指南寫明，人說不清這張圖，agent 就無法把工作自動跑完。跑起來之後，人可以出現在節點裡：async 的 post 可以停下來等人核准或拒絕，再決定下一條邊。Cookbook 另有範例，不是框架本體：命令列笑話產生器把人的評語送回下一輪；FastAPI 範例用網頁按鈕核准或退回，並用 SSE 推狀態。Supervisor 範例是多一個節點檢查研究 agent 的答案，不好就整段重跑。自我檢查也可以是另一個 LLM 節點。這些都是圖上的一站。

### 工具與除錯圖要自己接

文件故意不附 LLM、搜尋、向量庫、語音。理由是供應商 API 常改，也方便換成自己的模型。視覺化同樣沒有內建，只給一段把圖走成 Mermaid 的範例。那是給開發者看結構的圖。

## 主打賣點

- **它要人記住的是 Graph 加 Shared Store，而且核心小到可以整份讀完。** 官方說 100 行、零依賴、沒有供應商綁定。本次在 `main` 讀到的 `pocketflow/__init__.py` 是 99 行，只 import 標準庫。PyPI 上的 `pocketflow` 版本是 0.0.3，依賴清單是空的。這個版本和現在的 `main` 是否同一份碼，尚未確認。
- **和 CrewAI 放在一起看，資料模型不同。** CrewAI 給的是 role、goal、task、process。PocketFlow 給的是節點、動作邊、一份共用資料。角色、目標、記憶都要自己放進 store 的欄位。
- **Agentic coding 是這套抽象的用法。** 流程夠小、設計文件夠清楚，coding agent 才寫得出節點。教學 repo 就是照這張設計文件做出來的應用。
- 現成工具、網頁審核、Mermaid，是範例。開源核心仍是那一個圖執行器。

## 使用情境

### 一篇文章拆成三張桌子

- 適合誰：想把寫作步驟固定下來的開發者。
- 在什麼情況使用：題目進來，依序產生大綱、草稿、修訂稿。
- 帶來的價值：每一段的輸入輸出都落在 shared 的欄位，下一站只讀上一站留下的東西。

### 一個會自己決定要不要再搜尋的節點

- 適合誰：步驟事先排不死、要依目前資料分支的人。
- 在什麼情況使用：決定節點在 search 和 answer 之間循環，搜尋結果累積在 context。
- 帶來的價值：目標和做過的動作留在同一份 store，路是 post 回傳的動作。

### 人先畫圖，關卡再等人

- 適合誰：想讓 coding agent 實作、又要在關鍵步看過結果的人。
- 在什麼情況使用：先寫 design.md；執行中某一節停下來等核准，或加一個 supervisor 節點把不合格的答案送回去。
- 帶來的價值：人的工作是定圖和放行。

## 我們可以學什麼

- 值得借鑑的 product idea：工作是圖上的一站，交接是共用櫃的欄位，下一步是動作字串。員工名冊、長期記憶、權限都還沒有；先有樓層和櫃子，角色才放得進去。
- 值得借鑑的 interaction / workflow：prep 是開櫃取件，exec 是低頭做事，post 是放回並選擇走廊。固定流程是一排工位。Agent 是站在決定桌看櫃子，再走到搜尋或回答。多個人同時在場時，用托盤傳話。人先在白板畫 design.md，執行中走到亮著「等人」的那一桌蓋章。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室（pixel 樓層或地圖）。每個 Node 是固定工位，名牌寫這一步的職責。Flow 是地上的走道，`"approved"`、`"search"` 這類動作是岔路口的路標，迴圈是走回同一張桌子，巢狀 Flow 是房間裡的小房間。正在執行的工位亮燈。Shared Store 是樓層中央的共用櫃：上一站把大綱、context、答案放進指定抽屜，下一站只打開自己該看的抽屜。Params 是夾在這次工作單上的短標籤，例如檔名，不進共用櫃。Batch 是同一張桌子按工作單重做，或同一條走道重走一遍。平行是幾張桌子同時亮、伸手進同一櫃；平面上要標出誰正在寫同一個抽屜。人的位子有兩個：開工前站在白板改 design.md，執行中走到停住的關卡桌按核准或退回。Supervisor 是多出來的驗收桌。RAG 是兩段樓層，索引房先把資料放進櫃，問答房再來取。人在這張樓層裡工作。Mermaid 是白板旁邊的設計草圖。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你進門就看見誰站在哪一桌。決定桌的人看完共用櫃，選了搜尋就走到搜尋桌，把結果放回櫃子，再走回決定桌；資料夠了就坐到回答桌。寫作流程是一排工位，大綱桌的人離開後，撰寫桌的人才開始。訂單這類巢狀流程是套房，付款間做完才進庫存間。Taboo 那種多 agent 是兩個人都在房間裡，各自的托盤上遞提示和猜測，你看見誰在等對方的下一張紙條。等人的關卡是某張桌子的人停住、看向門口；你走過去看草稿，核准就讓他走向下一桌，退回就留在原地重做。design.md 是牆上的白板，走道和櫃子欄位是人先畫好的。重點是看見誰在場、誰在哪裡做事、櫃子裡剛被放下什麼。
- 需要重新設計的地方：PocketFlow 的執行是函式呼叫，加上你自己寫進 store 的欄位。空間辦公室要另做「現在亮的是哪一桌」。它沒有角色卡、私人記憶和權限開關；若要和 CrewAI 的角色小隊放在同一層樓，座位種類要分開。多 agent 文件說多數時候先不要用。平行寫同一份 dict 沒有鎖，尚未實測。FastAPI cookbook 的核准網頁是一塊等人的畫面，留在關卡桌即可。PyPI `0.0.3` 與 `main` 是否一致，尚未確認。

## 初步看法

- 最有價值的部分：圖、動作邊、共用儲存這三件，加上人先寫設計文件再讓節點跑。
- 最大限制或疑問：它是程式庫。誰在哪一桌、櫃子衝突、關卡怎麼被看見，都要工作空間自己補。版本對齊尚未確認。
- 是否值得進一步研究或親自體驗：值得，尤其是 context 欄位、動作岔路、等人放行在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official documentation：https://the-pocket.github.io/PocketFlow/
- Repository：https://github.com/The-Pocket/PocketFlow
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright (c) 2024 Zachary Huang）
- Core：`pocketflow/__init__.py`（本次讀到 99 行，僅標準庫）
- 已讀文件：Home、Agentic Coding、Node、Flow、Communication、Batch、Async、Parallel、Agent、Workflow、Multi-Agents、RAG、Structured Output、Map Reduce 索引、Viz
- Cookbook 僅作範例：supervisor、cli-hitl、fastapi-hitl、multi-agent
- PyPI：`pocketflow` 0.0.3（尚未安裝，未與 `main` 核對）
