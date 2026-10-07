# llmwiki

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-compiler-cell-7aa3/reviews/llm-wiki-compiler.md)
>
> Cell ID：[https://github.com/atomicstrata/llm-wiki-compiler](https://github.com/atomicstrata/llm-wiki-compiler)
>
> Status：`untried`
>
> Category：Knowledge compiler / agent context
>
> Last updated：2026-10-07

## 產品介紹

llmwiki 是一套知識編譯器。你把論文、文件、筆記、網頁，或 Claude／Codex／Cursor 的 session 匯出檔丟進去，它會先用 LLM 編成一份可互連、可追溯來源的 markdown wiki。之後人和 agent 都是查這份編好的知識，而不是每次從原始檔重新拼答案。

它幫的是需要長期重用同一批知識的人：研究者、文件維護者，以及想給 agent 穩定 context 的工程師。使用方式是本機 CLI：ingest → compile → query／view；也可以開 MCP server，讓 Claude Desktop、Cursor 或 Claude Code 直接操作同一份 wiki。

## 主要 Features

### 先編譯，再查詢

核心做法不是把原始檔切成 chunks 再每次重找，而是先把來源編成有類型的 wiki 頁。概念有自己的頁、頁和頁用 `[[wikilink]]` 連起來，之後的問題、context pack 和 MCP 工具都跑在這份成品上。官方把它對齊 Karpathy 的 LLM Wiki pattern：compile-time 先做結構，query-time 只負責取用。

### 跨來源合併成一頁

編譯分成兩段：先從所有變更來源抽出概念，再一次寫頁。同一概念出現在多份來源時，會合成一頁並留下合併後的出處，而不是讓重複片段在檢索時互相搶位置。沒改過的來源用 hash 跳過，所以日常是增量編譯，不是每次整庫重做。

### 引用、過期與孤兒頁

段落和主張會指回來源檔與行號。來源改了，對應頁會變成 stale；來源被刪光，頁會變成 orphaned。lint、status、viewer 和 MCP 都會標出這些狀態，再用 refresh 只修該修的頁。這讓知識本身有進度，而不只是「又問了一次」。

### 上架前的審核佇列

編譯結果不必立刻成為 live context。低信心、互相矛盾、schema／出處不合，或從外部 OKF bundle 匯入的頁，可以先停在 candidate 佇列。人批准後才寫進 wiki；拒絕的會進 archive。查詢得到的答案也可以先審再存成頁，讓「生成」和「成為辦公室知識」分開。

### 給 agent 的 context pack

Agent 不是被餵整庫原始檔。它可以先看 `wiki_status`，再要一份有 token 預算的 evidence pack：主頁、相關鄰居、引用、新鮮度和建議下一步。MCP 也讓 agent ingest、compile、query、lint 和匯入匯出，但讀多寫少的工具不必先有 LLM 憑證。這比較像去圖書館借一疊附出處的資料，而不是把整排書架搬到桌上。

### 領域設定檔，而不是另做一套產品

沒有 profile 時，它就是經典的 concepts／queries wiki。加上 `.llmwiki/profile.json` 後，同一套 compiler 可以宣告實體類型、關係、生命週期關卡、workflow 和 artifact。AutoSci、Newsroom 只是範本，不是內建的研究或編輯 App。真正執行排程、跨專案協調的 coordinator，官方也把它留在這個 repo 外面。

## 主打賣點

- 最想被記住的差異是：**知識要先編成可檢查的成品，再讓人與 agent 重用**。查詢結果可以存回 wiki，知識會越用越厚，而不是問完就丟。
- 和傳統 RAG 不同：RAG 的主物是原始片段；llmwiki 的主物是編好的頁。它仍有語意搜尋、BM25 和連結擴展，但檢索對象是 wiki，不是 raw chunks。
- 和 [gbrain](https://github.com/garrytan/gbrain) 也不同：gbrain 比較像辦公室記得誰、做過什麼決定；llmwiki 比較像圖書館，把文件編成可引用、可審核的藏書。官方另有 Atomic Memory 當 agent 執行期記憶，可從 wiki 匯出過去，但那是另一層，不是這個 Cell。
- 本機 viewer 主題、多種匯出格式、各種 LLM provider，多半是同一套編譯結果的包裝。值得學的是編譯、引用、審核和 context pack，不是瀏覽器皮膚。

## 使用情境

### 把研究或專案文件編成共用圖書館

- 適合誰：手上有論文、ADR、設計文件或筆記，而且之後還會反覆查的人。
- 在什麼情況使用：同一批來源會被很多人、很多 session 重用，不值得每次重讀原文。
- 帶來的價值：概念合併成穩定頁，引用可回原文，過期時也知道哪一頁該重修。

### 給 coding agent 穩定的專案知識包

- 適合誰：不想每次把 README、會議記錄和舊 session 整包塞進 prompt 的人。
- 在什麼情況使用：agent 開始任務前，需要一份有出處、有預算的 context，而不是再掃一次檔案樹。
- 帶來的價值：員工先去圖書館拿 evidence pack，再動手；節省重發現，也讓答案對得回原頁。

### 生成知識先審核，再成為 live context

- 適合誰：在意 agent 寫進共用知識的對錯，或要匯入外部知識包的團隊。
- 在什麼情況使用：compile、query --save，或匯入別人的 OKF bundle 之後。
- 帶來的價值：人只在審核桌介入，而不是讓每一句生成立刻變成辦公室正式記憶。

## 我們可以學什麼

- 值得借鑑的 product idea：把「原始來源」和「可進辦公室的知識」分開。來源是地面真相；wiki 頁才是員工查、借、審、交接的成品。
- 值得借鑑的 interaction：compile、review、query-save、refresh 是四種不同動作。進度不只是 task 完成，也包括哪一頁過期、哪一頁還在排隊、哪一份 context pack 被帶走。
- 在 2D workspace 裡，這會變成平面辦公室裡的一間圖書館或檔案室。員工把原料送到編譯台，書架上是編好的頁，審核桌放著還沒上架的 candidate，wikilink 比較像樓層地圖上的相鄰房間。過期頁會在書架上被標出來。Agent 出門前先來這裡拿一疊附引用的資料夾。這仍是一個地方裡的工作，不是資料視覺化。
- 在 3D workspace 裡，這是可走進的圖書館房間，不是立體知識圖譜，也不是可旋轉的 3D 物件。會看到編譯員在處理新來源，審核員坐在待批桌前，其他員工走進圖書館問問題、拿到 context pack，再走回自己的位子。人介入的位置就是那張審核桌：知識還沒上架前，先放在那裡等人決定。
- 這不是 Conductor、Firstmate、Maestro 那種 ADE dashboard，也不該把 `llmwiki view` 的本機瀏覽器當成 workspace。viewer 只是看書的窗戶；辦公室要呈現的是誰在編譯、誰在審核、誰把知識帶走。
- 和已有 Cells 的關係：gstack 是走進辦公室的員工，gbrain 是他們記得的人事與決定，llmwiki 是他們共用的藏書。Agent Office 目前看得到誰在場，但還缺一間真正的圖書館，讓員工先查編成的知識，而不是每次重讀原料。
- 不值得照搬全部 CLI、profile schema、匯出格式或 viewer 主題。領域生命週期也可以之後再學；先吸收「編譯成品、引用、審核關卡、context pack」這幾個 primitives。

## 初步看法

- 最有價值的部分：把知識從一次性 retrieval 變成可檢查、可過期、可審核、可交接的辦公室資產。
- 最大限制或疑問：編譯要花 LLM 成本，也比較適合會被重用的來源，不適合一直在變的雜訊日誌。CLP、workflow、OKF 的表面很多，第一次容易把 compiler 誤認成研究或編輯產品。實際編譯品質、審核摩擦和 context pack 是否真的比直接丟檔更好，尚未親自驗證。
- 是否值得進一步研究或親自體驗：值得先研究 compile／review／context pack 這組概念；不必先跑完整 AutoSci 或 Newsroom 範本。

## 後續補充（選填）

此 Cell 目前只做產品研究，沒有 clone 或實測。若之後要體驗，優先驗證三件事：同一概念跨來源是否真的合成一頁、candidate 批准前是否進不了 live context，以及 `get_context_pack` 給 agent 的證據是否帶得出處與新鮮度。

## Sources

- [llmwiki repository](https://github.com/atomicstrata/llm-wiki-compiler)
- [Official docs](https://llmwiki.atomicstrata.ai)
- [Karpathy LLM Wiki pattern](https://llmwiki.atomicstrata.ai/concepts/karpathy-pattern)
- [How it works](https://llmwiki.atomicstrata.ai/concepts/how-it-works)
- [MCP agent integration](https://github.com/atomicstrata/llm-wiki-compiler/blob/main/docs/guides/mcp-agent-integration.mdx)
- [gbrain](https://github.com/garrytan/gbrain)
- [Agent Office](https://github.com/AgentSystemLabs/agent-office)
