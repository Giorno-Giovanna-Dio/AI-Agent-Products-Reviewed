# LangGraph

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langgraph-cell-9381/reviews/langgraph.md)
>
> Cell ID：[https://github.com/langchain-ai/langgraph](https://github.com/langchain-ai/langgraph)
>
> Status：`untried`
>
> Category：低階編排 runtime（圖狀態）
>
> Last updated：2026-10-10

## 產品介紹

LangGraph 是 LangChain 的開源 Python runtime，用來編排會跑很久、中間狀態要留住的 agent 與工作流程。開發者用程式定義一張圖：大家共用的狀態、做事的節點，以及決定下一棒的邊。它服務的是需要自己控制步驟的工程師。模型與工具可以接 LangChain，也可以不接。官方網站在 [langchain.com/langgraph](https://www.langchain.com/langgraph)，文件在 [docs.langchain.com](https://docs.langchain.com/oss/python/langgraph/overview)。JavaScript 版在另一個 repository [`langgraphjs`](https://github.com/langchain-ai/langgraphjs)。本次只讀 `main`（`6aa0afba`，套件版本 1.2.14）、LICENSE 與現行文件，沒有安裝或執行。

同一套生態裡，層次是分開的。[Deep Agents](deepagents.md) 坐在 LangGraph 上面，是帶檔案、子 agent、上下文與 skills 的 harness。LangChain 的 agent 迴圈也建在這層 runtime 上，包裝比較薄。LangGraph 提供的是工作怎麼流、狀態怎麼留、人在哪裡插進來，以及如何回到剛才某一拍。LangSmith 是追蹤、評估與部署的平台。文件裡的視覺除錯畫面叫 LangSmith Studio。LangGraph Studio 這個舊稱是否還單獨存在，尚未確認。這些是儀表板與平台。

## 主要 Features

### 圖狀態、節點與邊

圖有三樣東西。**狀態**是大家共用的資料，通常先定一份 schema。每個欄位可以有自己的合併規則：沒寫規則時，後寫的值蓋掉舊值；寫了規則時，新訊息可以接在舊清單後面。**節點**是函式：讀目前狀態，做一件事（呼叫模型、查資料，或只是普通程式），再交回一部分更新。**邊**決定下一棒。固定邊永遠走到同一張下一桌。條件邊看狀態，再選一站或好幾站。從起點也可以依狀態選第一站。同一節點若同時指出多個下一站，下一拍這些站會一起做。

節點做完才把更新送出去，執行以「拍」推進。同一拍裡可以有多張桌子同時工作，下一拍才接下去。圖要先 compile 才能跑，檢查點與中斷點也在這時掛上。清單長度事先不知道時，可以用 `Send` 臨時開很多張相同的桌子，各拿一份自己的輸入，做完再收攏。`Command` 讓一個節點同時改狀態並指定下一站。文件提醒，同一節點不要又寫固定邊又寫動態路由，兩條路可能一起發生。同一 runtime 也可以用 Functional API 寫成函式。這篇依 Graph API 整理，兩種寫法的手感尚未實測。

### 檢查點與持久化

要留下狀態，compile 時掛上 checkpointer，每次呼叫帶一個 `thread_id`。每一拍結束存一張檢查點，也就是這一刻的圖狀態。同一拍裡若有節點失敗，已成功的節點會留下 pending writes，恢復時那些節點不必重做。中斷、對話接續、時間旅行、故障恢復都靠這些快照。

檢查點的範圍是一條 thread，算這次工作的短期記憶。跨 thread 還要記得的偏好或事實，放在 store，那是另一個鍵值庫。記憶體版的 checkpointer 與 store 在行程結束後就沒了。文件列出可換成 SQLite（本機）或 Postgres（較長的執行）。其他 store 後端清單尚未逐一核對。用官方 Agent Server 時，文件說持久化由伺服器處理，細節尚未實測。

### 中斷

節點裡可以呼叫 `interrupt()`，把一個可序列化的問題交出來，然後整張圖停住。停住需要 checkpointer 和同一個 `thread_id`。人準備好之後，用 `Command(resume=...)` 把答案送回去，那個呼叫才拿到回傳值，節點繼續。interrupt 可以寫在函式中間，也可以依條件才停。文件另有節點前後的靜態中斷點，這次沒有展開。

恢復時，含有 interrupt 的那個節點會從函式開頭再跑一次，所以停住之前的副作用要能重做。文件寫，從 langgraph 1.2.12 起可以附回應的 schema，讓呼叫端畫出對應的欄位；Studio 會把它畫成表單。這次沒有跑過。

### 時間旅行

有檢查點就能回到過去某一拍。**重播**是從那張快照再往下執行：快照之前的節點留著原結果，之後的節點會再跑，模型與外部呼叫可能得到不同結果。**分叉**是在那張快照上改狀態，開出新的一支，原來的歷史還在。時間旅行再次經過 interrupt 時，會重新停下等人回答。只能從完整的拍邊界恢復，不能從函式執行到一半的地方恢復。

### 子圖

子圖是被當成一個節點用的另一張圖。適合把一段可重複的流程包起來，或讓不同人各寫一塊，只要出入口的狀態對得上。兩張圖若共用同一組欄位，可以把編譯好的子圖直接加進父圖。欄位不同時，要在外層節點裡把狀態轉進去、再把結果轉出來。子圖有自己的 checkpoint 命名空間。文件寫，父圖不一定立刻看見子圖內部的更新。從子圖跳回父圖的某一站，文件用 `Command` 指向父圖。

Deep Agents 的子 agent 是 harness 的委派：乾淨的上下文，做完交回一份報告。LangGraph 的子圖是圖裡面再嵌一張圖，出入口是狀態欄位。一位 Deep Agent 可以只是這張圖上的一個節點。

## 主打賣點

- **它想被記住的是控制權。** 確定的程式步驟和交給模型的步驟可以排在同一張圖裡。狀態留在檢查點。人可以在中途改狀態或回答問題，也可以回到某一拍重播或分叉。
- **Deep Agents 用這層 runtime，自己再加上辦公套件。** 檔案抽屜、子 agent、skills、待辦與終端機 `dcode` 屬於 [Deep Agents](deepagents.md)。LangGraph 是底下的日程與狀態機。想快做一個會規劃的長任務 agent，官方 README 直接指向 Deep Agents。迴圈形狀要自己畫時，才直接用 LangGraph。
- LangChain 的現成 agent、預建的工具節點，是這層能力的較高包裝。LangSmith 的追蹤、評估、部署，以及 LangSmith Studio 的逐步除錯畫面，是平台與儀表板。

## 使用情境

### 一段流程裡，有的步驟照規矩、有的步驟交給模型

- 適合誰：要把客服、審核或資料管線寫成可恢復程式的開發者。
- 在什麼情況使用：讀入、分類、查資料、起草、送出分成節點。分類用模型，送出用確定的程式，高風險時走到人審。
- 帶來的價值：哪一步可以預測、哪一步允許模型決定，留在同一張圖和同一份狀態裡。

### 敏感動作停下來，同一條 thread 稍後再繼續

- 適合誰：工具呼叫、對外寄送或改資料前必須有人點頭的團隊。
- 在什麼情況使用：節點裡 interrupt，帶著 thread_id 把問題送出。人核准、修改或拒絕後，用同一條 thread resume。
- 帶來的價值：等待可以跨行程。檢查點還在，就不必把整段對話重講一次。記憶體版檢查點在行程結束後消失，正式環境要換可持久的後端。這一點尚未實測。

### 回到剛才某一拍，重做或另開一條路

- 適合誰：要除錯、比較兩種後續、或改掉中間產物再往下的人。
- 在什麼情況使用：列出這條 thread 的檢查點，從某一拍重播；或改狀態後分叉，舊的歷史留著。
- 帶來的價值：失敗或後悔不必整件案子重來。分叉之後的節點會真的再執行，包含再次 interrupt。

## 我們可以學什麼

- 值得借鑑的 product idea：圖是這一天的班表與傳紙規則。共享狀態是全樓這一刻的案子資料夾。節點是一種工位。邊是下一棒送給誰。檢查點是每一拍結束時整層樓的快照，按 thread 分卷。interrupt 是人必須到場才能放行。子圖是樓層裡一間有自己流程的裡間。store 是跨案子還在的檔案室。Deep Agents 的那一位員工，可以只是這張班表上的一個工位或一間裡間。
- 值得借鑑的 interaction / workflow：固定邊是資料夾一定送到下一桌。條件邊是這桌看完再決定往哪送，也可以同時送給好幾桌。同一拍裡多張桌子一起做事；其中一張失敗時，這一拍已經做完的不必重做。interrupt 時樓層停在這一拍，人把答覆放回原桌，該工位從這個步驟開頭再做一次。時間旅行是翻開某一拍的快照：重播讓後面的桌子再做；分叉是複印那一拍、改一張紙、開一條新走道，舊走道還在。子圖做完只把約好的結果交回外層。
- 在 2D workspace 裡會變成什麼：一層俯視的平面辦公室。每張桌子是一個節點，名牌寫這桌做的事。樓層中央是這件案子的資料夾，桌子讀它、把更新放回去。固定路線寫在桌緣的下一站；條件路線是桌面上的分送規則。同一拍同時亮著的桌子，是這一拍一起在做的人。牆邊檔案櫃以 thread 分格，每一格是檢查點。人要核准時，該桌亮起等待，樓層停在這一拍。時間旅行是抽出某一格舊快照，讓後面的桌子再做，或複印成旁邊另一條走道。子圖是這層樓隔出的小房間，門口只進得出約定的紙。這是人工作的樓層。圖是班表與傳紙規則。LangSmith Studio 是樓外的監視螢幕，用來事後看步驟，那塊螢幕留在樓外。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走在樓層上，看見誰在哪張桌子。分類的人看完中央資料夾，把案子送到查資料的桌子，或送到人審的桌子。同一拍裡可以有兩張桌子同時有人。走廊的檔案櫃是這條 thread 的檢查點，你可以走回稍早的一刻：那一拍之後的人會再做一次，已經做完的人留在原位。分叉是旁邊多出一條走道，原來的人還在舊走道上。interrupt 時員工停在桌前，拿著單子等你寫下答案。子圖是推門進去的裡間，裡間有自己的桌子與流程，出來時把該交的結果放在外層桌上。store 是這棟樓共用、不隨單一案子收走的檔案室。圖是誰下一棒去哪裡的日程，人在辦公室裡走動。LangSmith 的追蹤與 Studio 留在辦公室外的儀表板。
- 不值得照搬或需要重新設計的地方：空間裡要看見的是工位、資料夾與檔案櫃。把 StateGraph 畫成節點連線，樓層上就只剩一張給開發者看的圖。Deep Agents 已經描述過單一員工的桌子、側桌與抽屜，這裡沿用那一層，不另做一套辦公桌。節點恢復會從頭再跑；空間若只播一段錄影，人會以為副作用沒有發生第二次。InMemory 檢查點不跨行程。子圖的內部快照和外層分開，外層樓層不該顯示裡間每一張紙。文件寫私人 state 在串流時仍可能出現，以及檢查點反序列化預設較寬、官方建議收緊允許的型別。兩者都尚未實測，先不要在辦公室裡當成已經關好的鎖。

## 初步看法

- 最有價值的部分：共享狀態、每一拍的檢查點、可暫停的 interrupt，以及回到某一拍再分叉。這四件事可以直接變成樓層的資料夾、檔案櫃、等人的桌子，和旁邊多出來的一條走道。
- 最大限制或疑問：它沒有自己的空間介面。Functional API 與 Graph API 的取捨、Agent Server 是否真的包辦持久化、LangGraph Studio 舊稱是否還在，都尚未確認。套件版本 1.2.14 來自本次讀到的 `main`，是否等於最新發行，尚未對過發佈頁。
- 是否值得進一步研究或親自體驗：值得，當作 Deep Agents 底下的日程與狀態層。若要驗證暫停與分叉的手感，再在 worktree 外跑最小的圖即可。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://www.langchain.com/langgraph
- Repository：https://github.com/langchain-ai/langgraph （本次 `main` `6aa0afba`，`libs/langgraph` 版本 1.2.14）
- License：repo `LICENSE` 為 MIT（Copyright (c) 2024 LangChain, Inc.）
- Documentation：https://docs.langchain.com/oss/python/langgraph/overview
- 已讀概念頁：Graph API、Persistence、Checkpointers、Stores、Interrupts、Use time-travel、Subgraphs、Thinking in LangGraph、Runtimes frameworks and harnesses、LangSmith Studio
- README（`main`）：durable execution、human-in-the-loop、memory，並指向 Deep Agents 與 LangSmith
- JavaScript 版：https://github.com/langchain-ai/langgraphjs
- 相關 Cell：[Deep Agents](deepagents.md)
