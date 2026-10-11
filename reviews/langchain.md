# LangChain

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langchain-cell-ca43/reviews/langchain.md)
>
> Cell ID：[https://github.com/langchain-ai/langchain](https://github.com/langchain-ai/langchain)
>
> Status：`untried`
>
> Category：Python agent framework（模型、工具、agent loop）
>
> Last updated：2026-10-10

## 產品介紹

LangChain 是開源的 Python 框架，給要自己組 agent 的開發者用。GitHub 上的一句話是 agent engineering platform。現行文件把主入口收成 `create_agent`：一個模型、一組工具、一段 system prompt，加上可選的 middleware，組成會自己呼叫工具的 loop。使用者在自己的程式裡叫它跑完一段工作。官方網站是 [langchain.com](https://www.langchain.com)，文件在 [docs.langchain.com](https://docs.langchain.com/oss/python/langchain/overview)。本次只讀 repo 的 README、LICENSE、`libs/README.md` 與現行文件，沒有安裝。

這個 repo 是框架層，要和旁邊的產品分開。持久執行、串流、中斷與存檔在 LangGraph，那是另一個 repository，LangChain 的 agent 建在它上面。更高一層、已經擺好規劃、檔案與子 agent 的是 [Deep Agents](https://github.com/langchain-ai/deepagents)，本目錄已有 Cell，這裡只說明上下層，不重寫那一套辦公桌。LangSmith 是同一家的觀測、評估與部署平台。JavaScript 版在另一個 repository。這個 monorepo 裡，現在要裝的 `langchain` 在 `libs/langchain_v1`；舊的具名 chain 在 `libs/langchain`（套件 `langchain-classic`）。第三方整合大多已拆到別的 repo。

## 主要 Features

### 一個 agent loop

名字來自 chain。哲學頁寫，早期的 chain 是寫死的步驟，例如先檢索再生成。1.0 起，高階抽象收成一個建在 LangGraph 上的 agent；舊的 `LLMChain` 這類留在 `langchain-classic`。README 仍說把可互換的元件接起來，指的是組裝方式。現在的主工作流是：模型看對話，決定要不要叫工具，結果回到桌上，再看一次，直到不再叫工具。

`create_agent` 要模型、工具、system prompt，以及可選的 middleware。模型走同一套介面，供應商可以換。換坐在這張桌子上的人，抽屜裡的工具留在原位。

### 工具是出去辦事

工具是一段函式，或執行到一半才掛上的能力（文件舉例是從 MCP 載入）。模型只看到名稱、說明和參數，再用它去讀外部系統、寫資料、叫 API。呼叫失敗可以重試，或改成把錯誤訊息放回桌上，這是 middleware 的政策。每次模型開口前，也可以依當下狀態把抽屜縮小，只留下這一步用得到的工具。

### 中介層是這張桌子的家規

Middleware 掛在 loop 的前後：模型開口前、回答後、每次工具前後。會改到工作怎麼交接的幾項如下。摘要在對話變長時把舊紙壓短，桌子才不會被中間過程淹沒。待辦清單給 agent 一支 `write_todos`，複雜工作可以自己標成待辦、進行中、完成。人的關卡在指定工具真正執行前暫停，例如寄信或寫入；人可以核准、改參數、拒絕，或直接回答。暫停要有 checkpointer，狀態才留得住，之後用同一個 thread 繼續。呼叫次數、模型失敗時改坐另一個模型、個資遮罩，也掛在同一張桌子上。

### 主管把專家當成一次工具呼叫

多 agent 文件的做法是：主管 agent 決定叫哪位專家、給什麼輸入、怎麼拼結果。預設專家無狀態，不記得上次；對話記在主管這邊。每次委派用乾淨的上下文，重的過程留在側間，只交回一份結果。需要時可以把主管的對話歷史傳進去。專家若要自己留一條對話，文件要求另開持續模式。內建的 `task` 工具、檔案側桌與預設規劃屬於 Deep Agents，那是已經收錄的 harness。

### 圖把確定的職員和會判斷的房間放在同一層

`create_agent` 回傳的是編好的 LangGraph，家規跟著這個節點走。周邊若超出「一直做到做完」，就把這個 agent 放進更大的圖：入口用寫死的步驟分類，再把工作送進某一間 agent，或同時分給幾張桌子，或把產出接到下一個確定步驟。狀態留在 thread，中斷後從存檔繼續。哪一段必須照章走、哪一段交給模型，畫在同一張樓層上。LangGraph 是地板；這個 Cell 講的是放在地板上的那張桌子。

### 這一條對話，和跨天的檔案櫃

短期記憶是同一個 thread。加上 checkpointer 之後，這段對話的狀態會存下來；下次帶同一個 thread id，就從這一疊紙繼續。文件把它比成同一串郵件。長期記憶是 LangGraph 的 store，用 namespace 和 key 存 JSON，可以跨 thread 再讀，工具在執行時讀寫。摘要處理的是這一條對話太長；store 處理的是換一天還要記得的事。

## 主打賣點

- **它想被記住的是一張可組裝的桌子。** 模型介面統一，loop 固定，家規用 middleware 一片片加上。要管整層樓的走向時，把這張桌子放進圖。
- **和 Deep Agents、CrewAI 差在誰先幫你擺桌子。** Deep Agents 是同一家的現成 harness。CrewAI 是角色、任務、process 的小隊。LangChain 是空桌子：你決定抽屜裡有哪些工具、哪一步必須等人。
- **Chain 這個名字留下，主路徑換成 agent loop。** 固定步驟的舊 chain 在 classic 套件。現在的交接有兩種：工具結果回到同一人，或圖上的邊把資料夾送到下一站。
- LangSmith 的追蹤、評估、部署留在觀測平台。大量模型整合是同一介面的供應商，工作方式仍是這一個 loop。

## 使用情境

### 一個員工，幾樣工具，敏感動作要人點頭

- 適合誰：要在自己的服務裡放一個會叫工具的 agent 的開發者。
- 在什麼情況使用：工作是看情況選工具做到完，只有寄信、寫入這類動作要人看過。
- 帶來的價值：人不用重排整條流程。agent 跑到關卡就停，狀態留在 thread，人核准、修改或拒絕後再繼續。

### 主管收件，專家在側間做完再交一張紙

- 適合誰：一件工作會跨領域，又不想讓搜尋和草稿塞滿同一個上下文。
- 在什麼情況使用：主管 agent 把子任務當成一次工具呼叫派給專家；專家預設不記得上次。
- 帶來的價值：主管桌上只留結論。若要現成的檔案側桌和規劃清單，用已收錄的 Deep Agents。

### 先照章分類，再送進會判斷的房間

- 適合誰：前後步驟必須確定，中間一段才需要模型。
- 在什麼情況使用：圖的入口是分類節點，依結果把工作送進某個 agent，做完再接到寫回或下一個確定步驟。
- 帶來的價值：哪一段可以審計、哪一段可以臨機，留在同一張樓層。人看得出工作停在哪一站。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是一個 loop。名牌是 system prompt，坐著的人是可替換的模型，抽屜是工具，家規是 middleware。樓層是圖：照章辦事的職員和會判斷的房間用走廊相連。Chain 變成工作怎麼交到下一站。
- 值得借鑑的 interaction / workflow：叫工具是這個人起身到櫃台辦事，回條放回自己桌上，再決定下一步。子 agent 是把任務紙送進側間，側間用乾淨的桌子做完，只交回一份結果。人的關卡是送到面前等蓋章：核准、改內容、退回，或改由人直接回答。摘要是桌面紙太多時先歸檔。thread 是這串對話留在這張桌子；store 是跨天的檔案櫃，按人或主題分層。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層，可以是 pixel 辦公室或樓層平面。每個 `create_agent` 是一張固定工位，名牌寫 prompt。座位上的人可以換成另一個模型，抽屜不動。工具是工位旁的櫃台。舊的固定 chain 若還留著，是紙沿著固定櫃台傳過去。圖是地板上的走道：分類員在入口，依規則把資料夾送到某一間 agent 房，或同時複印給兩張桌子。HITL 是工位旁的待蓋章匣，人走過去才放行。thread 編號貼在這疊紙上，隔天回到同一疊。Deep Agents 那間已經擺好檔案櫃和待辦板的房間留在它自己的 Cell；這張樓層上的 LangChain 工位由你決定要擺什麼。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。進來先看到誰在哪張桌子。Agent 坐在自己的位子上，叫工具時轉身向櫃台或打電話。主管委派時，把任務紙送到側間；側間的人用乾淨的桌子做完，再把一份結果送回。你看得到主管還在座位上、專家在側間。持續模式打開時，側間留下自己的筆記；預設則是這次做完，側間不記得上次。圖的走廊是走道：入口的分類員照規則把資料夾放到該去的房間。人要核准時，員工帶著草稿走到你面前停下。辦公室留在原地，因為 checkpoint 還在，離開再回來仍是同一個 thread。重點是看見誰在場、工作停在哪一站、結果從哪張桌子交出來。LangSmith 的 trace 留在事後的紀錄牆。
- 需要重新設計的地方：LangChain 的執行軌跡在 log 和 LangSmith，空間辦公室要另做「誰在哪一桌」。模型整合留在抽屜裡的供應商。LangSmith、Studio 與部署控制台是觀測與上線，樓層和可走的辦公室另做。舊的具名 chain 留成固定櫃台；主要編制是工位、櫃台、側間和走廊。子 agent 預設無狀態，專家若要記得上次，座位上要明示持續模式。本次沒有跑過暫停、換模型和圖的交接，手感尚未確認。

## 初步看法

- 最有價值的部分：一個人反覆用工具，和樓層上確定的交接，拆成工位與走廊。家規跟著工位走。
- 最大限制或疑問：它是給開發者呼叫的框架。實際專案常把 Deep Agents 的 middleware 掛回這張空桌子，空間上仍要把「自己擺的工位」和「預先擺好的 harness」分成兩種座位。
- 是否值得進一步研究或親自體驗：值得，尤其是暫停時人怎麼在辦公室裡被走到，以及走廊如何讓人看出工作停在哪一站。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://www.langchain.com
- Repository：https://github.com/langchain-ai/langchain
- Documentation：https://docs.langchain.com/oss/python/langchain/overview
- Products：https://docs.langchain.com/oss/python/concepts/products
- Philosophy：https://docs.langchain.com/oss/python/langchain/philosophy
- Middleware：https://docs.langchain.com/oss/python/langchain/middleware/overview
- Human-in-the-loop：https://docs.langchain.com/oss/python/langchain/human-in-the-loop
- Subagents：https://docs.langchain.com/oss/python/langchain/multi-agent/subagents
- Short-term memory：https://docs.langchain.com/oss/python/langchain/short-term-memory
- Long-term memory：https://docs.langchain.com/oss/python/langchain/long-term-memory
- LangGraph overview：https://docs.langchain.com/oss/python/langgraph/overview
- License：repo `master` 的 `LICENSE` 為 MIT（Copyright LangChain, Inc.）
- README（`master`）：agent engineering platform；Deep Agents、LangGraph、LangSmith 的分工
- `libs/README.md`：`langchain_v1` 是現在的 `langchain`，`libs/langchain` 是 `langchain-classic`
