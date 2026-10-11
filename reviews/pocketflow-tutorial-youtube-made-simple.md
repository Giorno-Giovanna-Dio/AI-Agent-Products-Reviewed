# Youtube Made Simple

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-youtube-tutorial-cell-ca43/reviews/pocketflow-tutorial-youtube-made-simple.md)
>
> Cell ID：[https://github.com/The-Pocket/PocketFlow-Tutorial-Youtube-Made-Simple](https://github.com/The-Pocket/PocketFlow-Tutorial-Youtube-Made-Simple)
>
> Status：`untried`
>
> Category：PocketFlow 教學應用（長來源收成一頁淺白說明）
>
> Last updated：2026-10-10

## 產品介紹

Youtube Made Simple 是一個教學用的小應用，用來把一支很長的 YouTube 影片收成一頁讀得完的說明。官方要解決的情況是：影片可以長達數小時，人沒有時間看完，希望先抓住主題，而且說明淺白到像在跟小孩講。使用者在終端機執行程式、交出網址，跑完後打開專案目錄裡的 `output.html`。沒有帶網址時，程式會在終端機問一次。

這個 repo 是教學專案，不是 [PocketFlow](https://github.com/The-Pocket/PocketFlow) 框架本身。PocketFlow 是它 import 的圖形流程庫：節點、邊、一份共用資料。這份教學只用了直線流程和逐項批次，把「長逐字稿」做成「主題卡片，再做成一頁 HTML」。本次只讀 README、`docs/design.md`、`flow.py`、`main.py`、產出 HTML 的程式與 LICENSE，沒有安裝，也沒有跑過。

## 主要 Features

### 四個工位，一條路

流程在 `create_youtube_processor_flow` 裡寫死四站，用 `>>` 接起來：處理網址、抽出主題和問題、逐個主題改寫、產出 HTML。每一站都是同一套節奏。`prep` 從共用資料拿出這站要的東西，`exec` 做事，`post` 寫回去，並回傳 `default`。下一站因此是固定的。程式裡沒有第二種走向，也沒有人在中間改派。

### 一張愈填愈滿的共用表

`main.py` 一開始只放進 `url`。第一站寫上 `video_info`：標題、整份逐字稿、縮圖網址、影片 id。第二站寫上 `topics`。提示詞要求最多 5 個主題、每個主題最多 3 個問題；程式只把主題清單截到 5 個，問題數量沒有再截。每條問題先有三格：`original`、`rephrased`、`answer`。後兩格先是空字串。第三站把空格填上。第四站寫上 `html_output`。工作走到哪，就是看這張表上哪些格子還空著。

### 逐張填卡片，手上仍是全文

第三站是 `ProcessContent`，繼承 PocketFlow 的 `BatchNode`。它把每個主題配上同一份逐字稿，逐張送進模型：改短標題、改清楚問題、寫一段淺白答案。對照 PocketFlow 目前 `main` 的 `pocketflow/__init__.py`，`BatchNode` 是一個做完再做下一個，教學程式沒有改用平行批次。做完後用原本的主題標題、原本的問題文字，把結果填回同一張卡片。對不上的格子就留空。提示詞把逐字稿叫成 excerpt，放進去的是全文。這次沒有跑，對得準不準、全文太長時會不會失敗，尚未確認。

### 出口是一頁，旁邊有一本日誌

第四站把標題、縮圖、還有有內容的問答，收成一頁 HTML，寫進共用表，同時覆蓋存成 `output.html`。沒有問題、或問與答有一邊是空白的，就不會出現在這一頁。`main.py` 從啟動就把日誌寫到 `youtube_processor.log`。repo 裡的 `examples/` 是作者事先放好的範例頁，不是這次執行的結果。

### 失敗時留在原工位

四個節點都用 `max_retries=2`、`wait=10` 建立。對照 PocketFlow 的 `Node`，`exec` 丟出例外時會在同一站再試，兩次嘗試之間等待 10 秒，不會改走到別站。`requirements.txt` 只寫 `pocketflow>=0.0.1`。本次讀到的是框架 repo 的目前 `main`，和教學專案實際會裝到的版本是否同一份，尚未確認。

## 主打賣點

- 它想讓人記住的是這條收斂：一支很長的影片，變成少數主題、每題一個淺白答案，最後是一頁可以打開的 HTML。README 的說法是像對五歲小孩解釋，幾分鐘就能追上。
- 和角色小隊不同的地方，在於交接物。這裡沒有員工互相說話。下一站只讀上一站寫在共用表上的欄位。PocketFlow 提供圖和共用儲存；這個教學把那套用在「長來源、大綱卡片、淺白答案、一頁成品」。
- 手寫風格的頁面、YouTube 當輸入、以及「我一小時做完」的教學敘事，是這題的包裝。抽出大綱再改寫成短說明，是常見的摘要管線。repo 內建的 `call_llm` 呼叫的是 Anthropic Vertex 上的 Claude；README 要你自己換成別的模型包裝。換了之後頁面品質如何，尚未確認。

## 使用情境

### 來不及看完的長訪談

- 適合誰：想先知道一支長影片在講什麼、再決定要不要看原片的人。
- 在什麼情況使用：手上有網址，逐字稿取得到，並且可以等幾次模型呼叫。
- 帶來的價值：出口是一頁主題和淺白問答，而不是另一份跟逐字稿一樣長的文章。

### 照設計文件做一條同樣形狀的管線

- 適合誰：要學 PocketFlow 怎麼把節點接起來的人，或要讓程式代理照文件長出類似小應用的人。
- 在什麼情況使用：輸入換成別的長文字，但還是「先釘大綱卡片，再逐張填淺白說明，最後收成一頁」。
- 帶來的價值：`docs/design.md` 先寫共用表和每一站讀寫哪些欄位，`flow.py` 是同一張圖的實作。兩份之間有小差異，見下方初步看法。

### 只在門口交件、在櫃檯取頁

- 適合誰：接受「最多約五個主題、每題一段淺白答案」這個固定形狀的人。
- 在什麼情況使用：不打算在抽出主題之後先改卡片，再讓後面開始寫答案。
- 帶來的價值：人的動作只有交 `url` 和打開 `output.html`。中間沒有審核站。

## 我們可以學什麼

- 值得借鑑的 product idea：長材料先變成一張帶空格的卡片。主題和問題先釘上，改寫和答案先留白，後面的工位只填空。人看到的成品是一頁說明。
- 值得借鑑的 interaction / workflow：四個工位排成一條走廊，交接只靠牆上那張共用表。批次是同一排桌子逐張處理，寫完放回原來那張卡。失敗就留在原工位再試。人在門口交網址、在最後一桌取頁。
- 在 2D workspace 裡會變成什麼：一層樓的平面圖，走廊上四個工位。門口有人放下網址紙條。第一桌把標題、縮圖和整卷逐字稿釘在共用牆。第二桌讀完整卷，在牆上釘最多五張主題卡，每張卡最多約三條問題，答案欄是空白。第三段是一排相同的桌子，同一時間只有一張桌子有人在寫；那個人桌上仍攤著整卷逐字稿，寫完把改寫和淺白答案填回牆上對應的那一格，位置用原本的標題和問題對上。最後一桌把寫完的卡片訂成一頁，放在櫃檯，檔名是 `output.html`。日誌本放在門邊。人看牆上哪幾格還是空白，就知道這份工作停在哪一桌。
- 在 3D workspace 裡會變成什麼：一間走得進去的辦公室，像 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。進門看得見四個位置，也看得見誰站在哪裡。收件的人在第一張桌，桌上攤開逐字稿。中間的人面對共用板釘主題卡，空白的答案欄從門口就看得出來。再往裡是一排主題桌：只有正在寫的那張桌站著人，其他桌的卡片還空著等。因為是逐張做，走進去時只會看到一個人在動筆。寫完的人把卡片放回板上原來的位置，同一張卡從空白變成寫好。最後一間是取件櫃檯，櫃上只有這一頁說明。某站失敗時，那個人留在同一張桌再試，不會換房間。重點是看見誰在哪一站、哪張卡還沒寫完。
- 需要重新設計的地方：這四站是流程上的函式，共用表是唯一的記憶。若辦公室裡還要放有角色、會互相問話的員工，座位種類要分開。教學程式讓每一題各帶一份全文；空間裡比較清楚的做法是逐字稿只釘在牆上一份，寫字的人走過去看。填回卡片靠標題和問題原文完全相同，模型若改了 `original`，答案會對不回那一格，卡片需要穩定編號。實際對不準的比例尚未確認。`output.html` 每次覆蓋同一個檔，兩份工作會搶同一個取件櫃，一張工單要有自己的一頁。第二站和第三站之間沒有讓人抽掉一張主題卡的門口；若要這種介入，走廊上要多一道人走得過才放行的門。`youtube_processor.log` 是門邊的紀錄本，辦公室要顯示的是誰在哪一桌。

## 初步看法

- 最有價值的部分：共用表先留空再被下一站填滿，出口是一頁給人讀的說明。進度就是哪些格子還空著。
- 最大限制或疑問：形狀是固定的，中間不能改大綱。全文逐字稿會重複送進後面的每一題，很長時的結果尚未實測。樣本 `call_llm` 寫死 Anthropic Vertex；`requirements.txt` 同時列了 `openai` 和 `anthropic`，和這支函式怎麼搭配，尚未確認。設計文件把批次節點叫 ProcessTopic，並把 `url` 放在 `video_info` 裡；程式的類別叫 `ProcessContent`，`url` 在共用表最外層，`video_info` 沒有 `url`。以 `flow.py` 為準。
- 是否值得進一步研究或親自體驗：值得看這張愈填愈滿的卡片，在平面樓層和走得進去的辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Repository：https://github.com/The-Pocket/PocketFlow-Tutorial-Youtube-Made-Simple （`main` 最新提交 `9c222f875a71377259033704c86c01d554d1fe8c`，2025-04-17，訊息為 Update README.md）
- Design：repo 內 `docs/design.md`
- Flow：repo 內 `flow.py`、`main.py`、`utils/html_generator.py`、`utils/call_llm.py`
- Framework（另一個產品，只用來對照節點契約）：https://github.com/The-Pocket/PocketFlow 的 `pocketflow/__init__.py`，以及 https://the-pocket.github.io/PocketFlow/
- License：MIT（Copyright (c) 2024 Zachary Huang）
- README 內的 Colab 與頁尾連結仍寫舊路徑 `The-Pocket/Tutorial-Youtube-Made-Simple`。對該路徑呼叫 GitHub API 會回到現在這個 repo。本次沒有打開 Colab，範例 HTML 也沒有逐頁核對。
