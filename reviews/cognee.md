# Cognee

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cognee-cell-fb52/reviews/cognee.md)
>
> Cell ID：[https://github.com/topoteretes/cognee](https://github.com/topoteretes/cognee)
>
> Status：`untried`
>
> Category：Agent memory / knowledge graph
>
> Last updated：2026-10-10

## 產品介紹

Cognee 是給 AI agent 用的開源記憶平台（Apache-2.0）。文件、程式碼和對話先放進一個有名字的資料集，再被整理成可以搜尋的知識圖譜。下一個 session 或下一個 agent 用問答把相關的一段調出來，而不是把上一輪聊天整段貼進 prompt。

它主要給要替 agent 接長期記憶的人用：本機 Python 套件、HTTP API，或 Claude Code、Codex、Cursor 這類客戶端的 MCP／外掛。沒有雲端模型金鑰時，官方用本機小模型抽實體、用本機模型做嵌入；要生成回答才需要另外設定語言模型。也有 Cognee Cloud。本 Cell 沒有執行過。

## 主要 Features

### 先放進櫃子，讀懂，再發問

底層三步用白話講：

- **add**：把原料放進某個資料集。這時只是進檔案櫃，還沒被讀懂。
- **cognify**：把櫃子裡的東西切成片段，抽出人、事物和關係，寫成圖譜，並做嵌入，之後才查得到。程式碼走另一條路，編成符號和依賴的圖；官方說這條路可以不呼叫語言模型。
- **search**：指定要查片段、要一段有圖譜上下文的回答，或要查程式符號。

現在對外的入口收成四個動詞。**remember** 是放進記憶：沒有 session 時，一次做完歸檔、建圖和預設的整理；有 session 時先寫進這一輪的便條。**recall** 是問記憶。**improve** 是把這一輪裡值得留下的規則收進永久圖譜。**forget** 是刪掉一筆或整櫃。MCP 預設露出的是 remember、recall、forget，加上程式圖查詢。

### 這一輪的便條，和跨班還在的圖譜

Session 是短記憶，鍵是「哪一個人」加「哪一輪」。內容直接寫進快取，不切塊、不抽實體，所以快。官方預設大約七天沒再寫就過期，實際時限尚未確認。永久記憶是資料集裡的圖譜，跨 session 還在，直到被 forget。

Recall 可以先在這一輪便條裡找，沒有再查永久圖譜，結果會標來源是 session 還是 graph。要寫回答時，查到的圖譜上下文和先前問答會一起放進這一次的 prompt。也可以只要查到的上下文、先不生成答案，適合做成交給下一個人的材料。這些是文件描述，尚未親自跑過。

### 換班時留下的是規則，不是整段聊天

`improve` 帶著 session 時，會先丟掉被標成有害或信心不夠的指引，再讓一輪策展決定什麼值得留下。已經知道、站不住、或不耐久的會被拒絕。通過的教訓被改寫成獨立文件，再走 add 和 cognify 放回同一個資料集，並標成 `session_learnings`。新的 session 可以問到這條規則，即使原來的對話已經不在範圍裡。

官方的 Company Brain 範例把三樣東西放進同一櫃：一段文件事實、一小段程式的符號圖、以及從兩輪對話蒸餾出的發布規則。接著用全新的 session 一次問到三邊。Claude Code 和 Codex 外掛會在 session 結束、每隔一段工具呼叫、以及閒置時自動跑 improve。蒸餾會不會留錯或留太少，尚未確認。

### 資料集是誰能查這櫃的邊界

每個資料集有自己的讀、寫、刪權限，可以授給人或 agent。搜尋只回到對方有權看的櫃子。Cloud 文件寫每個資料集使用分開的圖庫與向量庫。自架時，多租戶是預設，每一支 API 都要登入的使用者；單人示範可以把存取控制關掉。隔離是否如文件所說，尚未確認。

寫入時可以掛上命名群組（例如某個專案或某個 agent）。之後查詢可以只看那一組。這是文件上的範圍開關，尚未確認實際好不好用。

### 圖譜是被查的記憶，畫面是攤開的附圖

Agent 查的是圖譜裡的節點、關係、片段和出處，再加上向量裡的語意。關聯式資料庫記下文件、片段和來源。三份儲存一起構成記憶。

`visualize` 畫的是有上限的一小團鄰居：可以從某次 recall 用到的節點長出來，也就是「這一題背後的子圖」。那是人打開來核對的文件。MCP 沒有把整張圖塞給 agent。

## 主打賣點

- 最想被記住的是：agent 的長期記憶是一份可自架的知識圖譜。文件、程式和對話裡學到的規則可以進同一櫃，下一輪用 recall 取出來用。
- 跟只做向量搜尋不同的地方是，它同時留出處、語意和關係。查的時候可以拿證據，也可以拿一段回答。
- 跟 [gbrain](https://github.com/garrytan/gbrain) 一樣都有「記得、回想、忘掉」，但 Cognee 底下明確分成「放進櫃子」和「讀成圖」。這一輪便條和永久圖譜是兩層，換班靠蒸餾，不是靠綜合回答順便標出還缺什麼。gbrain 的綜合與缺口，本 Cell 沒有在 Cognee 裡看到同等的產品承諾。
- [Hindsight](https://github.com/vectorize-io/hindsight) 用 memory bank，把觀察收成心智模型。Cognee 的 improve 也是把這一輪收成耐久內容，留下的是寫進圖譜的教訓文件；程式另外有符號圖。
- [ai-memory](https://github.com/akitaonrails/ai-memory) 的真相是 git 裡的 Markdown wiki，交接是可認領的 handoff。Cognee 的真相是圖譜和向量，交接是蒸餾進資料集。
- [Octop Memory](https://github.com/TencentCloud/octop-memory) 把證據晉升成事實，並用套件換宿主。Cognee 是便條經策展後寫成教訓、再讀進同一櫃。換地方比較像把這套服務自架起來，或用官方說的 COGX 從其他記憶系統匯入。匯入是否完整，尚未確認。
- 免金鑰的本機小模型、以及用單一 Postgres 同時當圖庫，官方都標成 demo 或需另外授權的能力。README 上的 BEAM 分數本 Cell 沒有重跑，不當作已驗證結果。

## 使用情境

### 公司檔案櫃，新來的人直接問

- 適合誰：希望文件、程式和團隊規則不要散在各人聊天紀錄裡的人。
- 在什麼情況使用：新人或新 agent 要接一個已經做過決定的專案。
- 帶來的價值：三類材料在同一資料集裡。全新 session 仍能問到「誰維護這支 API、發布時要守哪條規則、哪段程式在擋重複扣款」。這是官方範例的意圖，尚未實測。

### Coding agent 換班

- 適合誰：用 Claude Code、Codex 或 Cursor，同一 repo 會跨很多 session 的人。
- 在什麼情況使用：這一輪結束，下一輪不該重讀整份 tool log。
- 帶來的價值：外掛在背景把通過的教訓寫進永久圖譜。下一班 recall 開工。人要看的是留下的規則，不是整段對話。

### 核對回答從哪裡來

- 適合誰：不放心 agent 憑記憶作答的人。
- 在什麼情況使用：回答牽涉舊決定或程式依賴，需要看它引用了什麼。
- 帶來的價值：結果帶來源。人可以打開這一題背後的子圖，檢查關係有沒有接錯，再決定要不要讓這條教訓進永久櫃。

## 我們可以學什麼

- 值得借鑑的 product idea：辦公室有兩層記憶。桌上的便條是這一輪；檔案櫃是跨班還在的圖譜。換班時把值得留下的規則寫成卷宗放回櫃子，下一個人坐下自己調卷。
- 值得借鑑的 interaction / workflow：員工進座位先 recall。調出來的東西標明來自這一輪還是檔案櫃。人介入的時刻是「這條規則要不要進永久櫃」，以及答案的出處對不上的時候。權限是某一櫃的鑰匙。程式的符號圖和文件卷宗可以放在同一間檔案室的不同架子上。
- 在 2D workspace 裡會變成什麼：俯視的樓層。後面是檔案室，一排櫃子是資料集。員工坐在自己的桌子上工作。桌上是這一輪便條，和剛調出來的薄卷。換班時，通過的教訓被放進櫃子，整疊聊天留在原桌，過期就收掉。圖譜畫面是櫃檯上可攤開的一張紙，紙上只有這一題用到的鄰居，看完收回抽屜。樓層仍是人與員工工作的地方。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，像 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。檔案室在座位後面。員工坐下，先向檔案室調卷，卷宗放到桌上再開工。Session 結束時，短短一份「留下的規則」被夾進永久卷宗；沒通過的便條留在桌上。人走過去可以看見誰在查檔、哪份卷宗被打開。知識圖譜是某份卷宗裡的附圖，放在桌上供人核對。
- 不值得照搬或需要重新設計的地方：本機 UI、搜尋面板和 MCP 工作區是後台。放進辦公室會變成 ADE 側欄。圖譜視覺化不該變成大廳裡的主體。歸檔、建圖、蒸餾都可能很重，閒聊不該句句 cognify。免金鑰路徑和 Postgres 圖庫官方自己標成 demo。多租戶的鎖還沒實測，不能直接畫成牆上已經鎖好的櫃子。

## 初步看法

- 最有價值的部分：這一輪和永久圖譜分開，換班單位是蒸餾後的教訓，不是聊天複印。圖譜是被查的記憶，畫面只是核對用的附圖。
- 最大限制或疑問：尚未確認蒸餾的取捨、recall 自動選查法是否準、資料集隔離在自架時是否乾淨。本機小模型的抽取品質官方也說 demo 級。BEAM 數字未重跑。
- 是否值得進一步研究或親自體驗：值得當成「圖譜記憶如何進辦公室」來讀。若要體驗，優先看同一資料集、新 session 能否召回上一輪留下的規則，以及 visualize 是不是只打開那一題背後的子圖。

## 後續補充（選填）

此 Cell 只做產品研究，沒有 clone，也沒有執行。若之後要體驗，建議放在 `/workspace-labs/cognee`，不要放進本 repo。

## Sources

- Official website：[https://www.cognee.ai](https://www.cognee.ai)
- Repository：[https://github.com/topoteretes/cognee](https://github.com/topoteretes/cognee)（Apache-2.0）
- Documentation：[https://docs.cognee.ai/](https://docs.cognee.ai/)
- [Remember](https://docs.cognee.ai/core-concepts/main-operations/remember)、[Recall](https://docs.cognee.ai/core-concepts/main-operations/recall)、[Improve](https://docs.cognee.ai/core-concepts/main-operations/improve)、[Sessions](https://docs.cognee.ai/core-concepts/sessions-and-caching)
- [Company Brain 範例](https://docs.cognee.ai/examples/company-brain)、[Graph visualization](https://docs.cognee.ai/guides/graph-visualization)、[MCP](https://docs.cognee.ai/cognee-mcp/mcp-overview)
- 相關 Cell：[gbrain](https://github.com/garrytan/gbrain)、[Hindsight](https://github.com/vectorize-io/hindsight)、[ai-memory](https://github.com/akitaonrails/ai-memory)、[Octop Memory](https://github.com/TencentCloud/octop-memory)
