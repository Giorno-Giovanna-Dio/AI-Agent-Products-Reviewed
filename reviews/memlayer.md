# Memlayer

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memlayer-cell-ca43/reviews/memlayer.md)
>
> Cell ID：[https://github.com/divagr18/memlayer](https://github.com/divagr18/memlayer)
>
> Status：`untried`
>
> Category：Python LLM 記憶層（寫回與召回）
>
> Last updated：2026-10-10

## 產品介紹

Memlayer 是一個 Python 函式庫，夾在 LLM 呼叫和本機儲存中間。開發者換成它提供的 client（OpenAI、Claude、Gemini、Ollama、LMStudio），還是呼叫 `chat()`。這一層做兩件事：這句話要不要寫進記憶，以及模型要不要回頭去找。它服務的是想給單一模型加上跨對話記憶的人。PyPI 與 repo 的 `pyproject.toml` 都標 **0.1.8**，需要 Python 3.10 以上。授權是 MIT（Copyright 2025 Divyansh Agrawal）。官方文件在 [divagr18.github.io/memlayer](https://divagr18.github.io/memlayer/)。本次只讀 repo、文件與 PyPI 頁，沒有安裝。

文件和 README 主推這些包好的 provider client。repo 裡還留著 `Memory`：用 `wrap()` 包一個既有的 LLM client，只實作 embedded；self-hosted 與 cloud 會直接丟 `NotImplementedError`。那條舊路徑在沒有自訂 embedding 時，會用 OpenAI 的 embedding。

## 主要 Features

### 記憶夾在工位和檔案之間

一個 client 帶 `storage_path` 和 `user_id`。底下兩種櫃子：Chroma 放事實的向量，NetworkX 的圖放實體和關係，圖存成該目錄下的 pickle。`lightweight` 不開向量，只留圖。repo 裡有 Memgraph 的儲存檔，這次讀到的 provider 路徑接的是 NetworkX。Memgraph 是否還有在用的入口，尚未確認。

向量寫入和搜尋都帶 `user_id`，抽屜按人分開。圖的 `add_entity` 沒有 `user_id`，只有待辦任務帶使用者。多個 `user_id` 若共用同一個 `storage_path`，deep 搜尋會不會走進別人的關係線，尚未確認。

### 寫回：先過門，再在背景歸檔

Salience gate 決定這段文字值不值得留。過了才請模型抽出 facts、entities、relationships。事實進向量庫，附重要度和到期日；實體和關係進圖。沒過門就結束。`update_from_text` 把一段文件直接送進同一條整理，不必先聊天。整理跑在背景執行緒，`chat()` 不會等它做完。

寫回時機依 provider 而不同。OpenAI 與 LMStudio 在回覆前就把使用者那句改成第三人稱（例如 "My name" 改成 "The user's name"）送去整理，這兩支程式沒有再把助理的回覆送進去。Claude、Gemini、Ollama 是回覆之後，把「User + Assistant」整段送去整理。同一輪剛說的話，下一句能不能立刻找到，尚未實測。

### 召回：模型自己去翻

`chat()` 把兩個工具交給模型，`tool_choice` 是 auto：`search_memory` 和 `schedule_task`。模型沒呼叫工具，就直接回答，這一輪的 prompt 裡沒有被動注入的舊記憶。工具說明要求模型在使用者問到自己、偏好或舊對話時去搜。那是提示。模型不理它，搜尋就不會發生。

`search_memory` 要帶 tier。OpenAI wrapper 裡的深度是：

- **fast**：向量前 2 筆，不走圖
- **balanced**：向量前 5 筆，不走圖。工具沒給 tier 時用這個
- **deep**：向量前 10 筆，再用模型從查詢抽出實體，在圖上走兩跳；也會從向量結果裡的專有名詞再走一跳

`lightweight` 沒有向量，只有 deep 會走圖。README 的 tier 筆數和這段程式一致。文件 overview 把 balanced 寫成「向量加一跳圖」，和程式不同。README 宣稱 fast 低於 100ms，尚未實測。

`synthesize_answer` 不讓模型決定要不要找。它強制 deep 搜尋，並要求答案只能根據找出來的上下文。

### 到期便條、夜間整理、時間單

`schedule_task` 把待辦寫進圖，帶到期時間。下一次 `chat()` 若任務已到期，會把提醒塞成系統訊息，並把狀態改成 completed。提醒出現在下一輪開口。時間到了若沒有人再呼叫，這條路徑不會自己把便條送出去。舊的 `Memory` client 另有 `SchedulerService`，在背景把到期任務標成 triggered。現行 OpenAI client 的 `close()` 只停 curation 和關掉儲存，沒有停這個 scheduler。兩條路是否還接在一起，尚未確認。

Curation 預設約每小時一輪。到期的刪掉。還標成 active 的，用重要度、被翻閱次數、最近有沒有被看、放了多久算一個分數，低於 0.3 就改成 archived。門檻寫在程式裡。搜尋函式有記翻閱的呼叫，但把向量結果放在區域變數 `results`，傳進計數的卻是另一個一直是空的 `vector_results`。向量側的翻閱次數是否真的增加，尚未跑過確認。

每次搜尋把過程放進 `last_trace`：各步驟名稱和毫秒。這是呼叫端的時間單。

### 三種模式，預設說法不一致

`operation_mode` 同時決定門檻怎麼判斷，以及有沒有向量庫。OpenAI、Ollama、Claude 的建構子預設是 `online`（OpenAI embedding API）。modes 文件仍把 local 寫成預設。README 把 local 和 online 都標成 Default。

- **local**：本機 sentence-transformers，向量加圖
- **online**：OpenAI embeddings，向量加圖
- **lightweight**：關鍵字判斷，只有圖

這些 client 的 `salience_threshold` 預設 `0.0`，tuning 文件相同。overview 寫 `0.5`、範圍 0 到 1，和建構子註解的大約 -0.1 到 0.2 不一致。

## 主打賣點

- **它加上的是記憶這一層。** 呼叫還是 `chat()`。中間多了過門、背景歸檔、模型自己決定的召回，以及到期才放到下一輪的便條。儲存留在本機目錄。
- **和 CrewAI 的記憶放的位置不同。** CrewAI 是小隊跑完一棒後抽出事實，下一棒開始前再注入 prompt。Memlayer 包住單一模型 client。寫回在背景，召回要這次呼叫裡的模型自己叫 `search_memory`。
- **值得被記住的是過門、深度、到期。** 向量加圖、多個 provider、本機檔案，是常見檢索的組合。README 的「每句自動想起」「零設定」「100% 離線」和程式對不齊：預設 `online` 要打 embedding API，召回也不是每句都做。

## 使用情境

### 同一個使用者換話題還想認得他

- 適合誰：用 Python 包一個聊天模型、希望下次還記得名字、偏好、決定的人。
- 在什麼情況使用：同一個 `user_id` 與 `storage_path`，多輪 `chat()`。
- 帶來的價值：值得留的話在背景變成事實和關係；之後若模型呼叫搜尋，可以按 fast、balanced、deep 拿不同深度。同一輪內能否立刻召回，尚未實測。

### 把一份文件直接歸檔

- 適合誰：記憶不全來自聊天，還有郵件、筆記、說明要灌進去的人。
- 在什麼情況使用：`update_from_text`。
- 帶來的價值：同一條 salience 和整理管線，不必先演一輪對話。

### 到期的事在下一輪被提起

- 適合誰：希望模型記得「下週五交報告」這種有時間的事。
- 在什麼情況使用：對話裡請模型 `schedule_task`，之後再呼叫 `chat()`。
- 帶來的價值：到期便條在下一輪開頭就在系統訊息裡，講完標成 completed。沒有下一輪呼叫時會不會自己送達，尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：記憶是夾在工位和檔案室之間的一層。員工還是員工，抽屜還是抽屜。這一層決定哪句話進檔、找的時候走多深、哪張到期便條要在下一輪先放到桌上。
- 值得借鑑的 interaction / workflow：寫回先過門，招呼和客套進紙簍；過門的在背後歸檔，員工不必停下來等。召回是員工決定要不要走向檔案室，以及走 fast、balanced 還是 deep。人可以把一疊文件直接放上櫃檯。夜間整理員抽走過期卡片，把很久沒人翻的移到歸檔架。人要看得見「這次沒去檔案室」，也要看得見「紙條還在路上」。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層，可以是 pixel 辦公室或樓層地圖。每個 agent 一張固定工位，名牌寫模型和 `user_id`。工位後方是檔案室：語意抽屜是向量，關係牆是圖。人說話時，工位旁的文書先看這句過不過門。過門的紙條送進檔案室，事實放進這個使用者的抽屜，人和專案之間釘一條線；沒過門的丟進紙簍，樓層上看得到沒被收。召回時員工轉向檔案室：fast 抽兩張相近的卡片，balanced 多抽幾張，deep 再沿著關係牆走。人站在樓層上看到員工停一下、字條從檔案室送回工位，然後才繼續答。到期提醒是這一輪開始前已經壓在桌上的便條，念完就收走。`update_from_text` 是人把文件放上櫃檯，不必先跟員工聊天。`last_trace` 是櫃檯交回的時間單，寫這次走了哪一層、花了多久。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走進去看誰在哪張桌子。記憶層是桌子和檔案室之間的櫃檯。員工需要舊事時走到櫃檯，你看得到他站在那裡。fast 是櫃檯人員當面抽卡片；deep 是櫃檯人員走進後面的架子，並沿著牆上的關係線再找一截。寫回時，你看到文書拿著剛聽到的句子離開桌子，在門口停一下：不值得留的放進紙簍，值得留的寫成事實卡放進該使用者的抽屜，並在關係牆上釘線。你走過去可以看見新卡出現，或看見這句沒被收。提醒是有人再次走到這張桌子時，桌上已經放著到期便條；念出來之後便條被劃掉。人可以站在櫃檯要求只根據手上的檔案回答，並看見他們拿出了哪些文件。重點是看見誰在場、誰正走向櫃檯、哪張桌子的便條還在。
- 需要重新設計的地方：Memlayer 的執行痕跡是呼叫端的毫秒紀錄，辦公室要另做文書的走動。召回取決於模型叫不叫工具，樓層上要標出沒去檔案室的那一輪。寫回是背景的，要讓人看見紙條還在路上。OpenAI／LMStudio 收的是使用者那句，Claude／Gemini／Ollama 收的是整段問答，不要假設每一張桌子放進抽屜的是同一種紙。關係牆沒有依 `user_id` 隔間，在確認之前不要畫成已經分開的房間。self-hosted、cloud、Memgraph 都尚未確認有可用入口。overview 的 tier、預設模式、salience 預設和建構子不一致，先不要做進空間介面。

## 初步看法

- 最有價值的部分：記憶是獨立的一層，寫回有門檻，召回有深度，到期的事會在下一輪被看見。
- 最大限制或疑問：它是給開發者呼叫的函式庫。召回不是每句都發生，provider 之間寫回時機不同，圖是否按人隔離也尚未跑過。
- 是否值得進一步研究或親自體驗：值得，尤其是人在 2D／3D 辦公室裡怎麼看見過門、歸檔還在路上、以及員工有沒有走到櫃檯。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official docs：https://divagr18.github.io/memlayer/
- Repository：https://github.com/divagr18/memlayer
- PyPI：https://pypi.org/project/memlayer/ （0.1.8，requires Python >=3.10）
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright 2025 Divyansh Agrawal）
- 已讀：README、`docs/basics/overview.md`、`docs/basics/operation_modes.md`、`docs/services/consolidation.md`、`docs/services/curation.md`、`docs/storage/chroma.md`、`docs/storage/networkx.md`、`docs/tuning/salience_threshold.md`、`pyproject.toml`
- 已讀程式（`main`）：`memlayer/client.py`、`memlayer/services.py`、`memlayer/wrappers/openai.py` 的 `chat` 與工具迴圈；Claude、Gemini、Ollama、LMStudio 的整理呼叫點；Chroma 的 `user_id` 過濾與 NetworkX 的 `add_entity`／`add_task`
