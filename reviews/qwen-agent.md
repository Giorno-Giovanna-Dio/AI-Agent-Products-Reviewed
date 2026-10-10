# Qwen-Agent

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-qwen-agent-cell-9381/reviews/qwen-agent.md)
>
> Cell ID：[https://github.com/QwenLM/Qwen-Agent](https://github.com/QwenLM/Qwen-Agent)
>
> Status：`untried`
>
> Category：Python agent framework（工具、檢索、MCP）
>
> Last updated：2026-10-10

## 產品介紹

Qwen-Agent 是 Qwen 團隊的開源 Python 框架，用來把一個 Qwen 模型、一組工具，和使用者交出的文件組成一個會做事的 agent。寫程式的人建立一位 Assistant：給模型設定、角色說明、工具名單，以及要讀的檔案，再把對話送進去。它會以串流交回一長串訊息。這次讀的是 repo `main` 在 2026-03-04 的快照（`31a4d36`），只讀 README、LICENSE、文件與組裝相關的程式，沒有安裝、沒有執行。

官方 README 寫它現在是 [Qwen Chat](https://chat.qwen.ai/) 的後端。那是官方說法，尚未確認。框架本身沒有辦公室畫面。Gradio 的 `WebUI` 和範例應用 BrowserQwen 是用來查看對話與工具呼叫的視窗。

## 主要 Features

### 一位員工：說明、工具、文件

Assistant 站在會呼叫工具的 FnCallAgent 上面。建構時交三樣東西：system message（這位員工怎麼做事）、function_list（可以伸手拿到的工具）、files（一開始就放在旁邊的文件）。對話訊息裡還可以再夾檔。name 和 description 用來在多位 agent 同時出現時辨識這一位。

### 先抽文件，再進工具迴圈

每一輪開始，Assistant 先請內建的 Memory 處理檔案。Memory 把建構時的 files 和這段對話裡的檔案收成一份清單，支援 pdf、docx、pptx、txt、csv、tsv、xlsx、xls、html。有模型時，它會先產生檢索用關鍵詞。程式設定的預設策略名叫 GenKeyword；文件站的 RAG 頁仍寫預設是 SplitQueryThenGenKeyword，兩邊不一致。接著它呼叫 retrieval：文件切成塊（預設每塊 500 token），用關鍵詞 BM25，並加上 front_page_search。抽出的內容預設不超過 20000 token。解析結果經 Storage 寫在本機 `workspace/tools/doc_parser`。

抽到的段落會編成「來自某檔案的內容」，接在系統訊息後面的 Knowledge Base。若呼叫時已經傳入一段現成 knowledge，就跳過檢索，直接用那段。這一步做完，才進入工具迴圈。

### 工具迴圈：叫、執行、把結果接回同一條訊息

FnCallAgent 把目前登記的工具規格交給模型。模型若寫出 function_call，框架執行該工具，追加一則 role 為 function、名字是工具名的訊息，再把整條成長中的訊息交出來。沒有再叫工具，或呼叫次數用到預設上限 20（環境變數 `QWEN_AGENT_MAX_LLM_CALL_PER_RUN`），這一輪結束。宣告需要讀檔的工具，會額外拿到對話裡的檔案，加上一開始的 system files。自訂工具用登記方式寫下名稱、說明、參數和 `call`。官方說預設模板支援同一輪多個工具呼叫；這個迴圈是依序執行並接回訊息。是否同時發出，尚未確認。

### MCP 跟內建工具坐在同一張名單

function_list 可以放一份 `mcpServers` 設定。框架會把每個伺服器拉成子行程，並把上面的工具登記成「伺服器名-工具名」，另外還有 list_resources 與 read_resource。它們接著出現在同一個 function_map，走同一條呼叫迴圈。文件舉的例子是 memory、filesystem、sqlite。文件寫 MCP 服務可能沒有 sandbox，filesystem 只應開放指定目錄，並標成不適合直接上生產。這次沒有啟動任何伺服器。

### 兩種跑程式的地方

`code_interpreter` 在本機 Docker 裡開 Jupyter kernel，把指定的 work_dir 掛進容器的 `/workspace`。README 說只掛這個工作目錄，隔離是基本的，生產環境仍要小心。另一支 `python_executor` 用在數學推理示例，工具說明寫明沒有 sandbox、不要用於生產。兩種執行要分開看。

### 人怎麼檢查這一次組裝

終端機裡 `bot.run` 每次交出目前累積的訊息：助理的文字或 function_call，以及工具回傳。`typewriter_print` 把這條串流印出來。Gradio `WebUI` 把推理收成可展開的 Thinking，工具呼叫收成 “Start calling tool”，結果收成 “Finished tool calling”。側欄有代理名稱、描述，以及一組不能改的「插件」勾選，內容就是 function_map 的名字。檢索到的段落寫進系統訊息；Assistant 用 logger 的 debug 印出 Retrieved knowledge。聊天氣泡預設顯示的是工具步驟。要對照「抽了哪幾頁」，得看 log，或自己把系統訊息攤開。這部分的畫面尚未實測。

輸入太長時，框架會改寫上下文：先拿掉較舊的整輪，再折疊較舊的工具回傳，再拿掉較舊的工具步驟，最後才截最新的問題或回答。預設上限是 58000 token。文件寫更完整的記憶模組即將推出；這份快照的 context 頁仍是這句，是否已經另有模組，尚未確認。

GroupChat 管一份 agent 名單，可以用主持人、輪流、隨機或手動決定誰說話。文件把使用者也定義成一位 agent，並說群聊可以停下來等人、人可以打斷。BrowserQwen 是 repo 裡的範例：Chrome 擴充把目前網頁或 PDF 送進閱讀清單，本機 workstation 有編輯長文和對話兩種模式，並可叫 code interpreter。它是應用示例。是否仍能依文件裝起來，尚未確認。

## 主打賣點

- **組裝順序是它真正加上的東西。** 文件先被切塊、檢索、寫進這一輪的知識，然後同一個 agent 在同一條訊息上反覆叫工具。工具可以是內建的、自己寫的，或從 MCP 子行程登記進來的。
- **預設檢索不先要求向量資料庫。** 關鍵詞 BM25 加首頁搜尋，解析結果放在本機 workspace 目錄。長文問答另有 ParallelDocQA 這類把文件拆開再彙總的 agent。官方說這套在長文基準上勝過原生長上下文，本次沒有重跑。
- **Gradio、BrowserQwen 和各類示例是同一套迴圈的視窗。** Qwen 專用的工具呼叫模板，讓模型服務自己不會解析工具時仍能用；也可改走服務端原生解析。這是接上 Qwen 的方式。
- README 宣稱它是 Qwen Chat 的後端。尚未確認。

## 使用情境

### 帶著說明書做一件要呼叫工具的事

- 適合誰：想用程式把文件和工具交給一個 Qwen agent 的開發者。
- 在什麼情況使用：使用者交出 pdf，並允許 code interpreter 或自訂工具。
- 帶來的價值：檢索到的段落和後續工具結果留在同一條訊息裡，下一輪可以接著用。抽到哪些段落，預設要看 log。

### 把外部櫃子接到同一位助理

- 適合誰：已經有 MCP server（檔案、記憶、SQLite）的人。
- 在什麼情況使用：把 mcpServers 放進 function_list，和內建工具一起交給 Assistant。
- 帶來的價值：外部能力以「伺服器名-工具名」出現在同一張工具名單，呼叫方式與內建工具相同。權限邊界照文件是伺服器自己的目錄或資料庫；是否隔離，尚未確認。

### 在視窗裡看它叫了什麼工具

- 適合誰：想給別人試一個 agent，先看呼叫過程的人。
- 在什麼情況使用：`WebUI(agent).run()`，或 BrowserQwen 的 workstation 看網頁與文件。
- 帶來的價值：聊天氣泡展開就能看到工具名稱、參數和回傳；側欄列出插件名字。檢索到的頁和 MCP 行程要另外去對。

## 我們可以學什麼

- 值得借鑑的 product idea：一位員工是「角色說明 + 模型 + 工具名單 + 手邊文件」。上班順序固定：先從文件抽出跟這句話有關的頁，夾進這一輪的說明，再依照模型的決定去開工具。MCP 伺服器與 RAG 索引是辦公室裡的家具，人走過去使用。
- 值得借鑑的 interaction / workflow：人要能沿著同一條紀錄看三件事：這一輪從書架抽了哪幾頁、頁上寫哪個來源、然後員工打開了哪個工具抽屜、抽屜吐回什麼。工具步驟會出現在訊息串和 Gradio 的可展開區塊；被抽走的頁主要在系統提示和 debug log。群聊是主持人點名，人自己也佔一個座位，被點到才說話，也可以打斷。上下文太長時，舊的工具回傳會先被折起來，在空間裡就是把舊單據收進抽屜。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室，是做事的地方。每位 Assistant 一張固定的桌子，名牌寫 name 和 description。桌旁是工具台，每個抽屜一個工具：內建的、自訂的，或 MCP 的「伺服器名-工具名」。書架立在桌邊，文件被切成頁，解析後的紀錄收在書架底下的抽屜（本機 doc_parser 儲存）。這一輪開始，相關的頁被抽到桌面上，頁角標來源檔名。人在俯視畫面裡看見桌上攤開的頁，以及員工正打開哪一個抽屜。code interpreter 是桌子旁邊的玻璃隔間，地板只接指定的工作目錄。python executor 是沒有門的開放桌，和玻璃隔間分開擺。filesystem、memory、sqlite 這些 MCP 是靠牆的櫃子。RAG 的關鍵詞索引是書架上的分類與頁籤。Gradio 側欄的插件勾選是名牌清單，平面圖本身仍是這層樓。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走進房間，看見這位員工坐在自己的位子。這一輪他先走到書架，抽出幾頁放到桌上，頁角寫著來源檔名；你走過去可以讀那幾頁，對照他接下來的回答。接著他起身到工具台，打開一個抽屜，把結果拿回桌上。Docker 程式間是旁邊一間玻璃房，你站在外面看見他在裡面跑程式，地上只有掛載進來的那份工作目錄。MCP 的檔案櫃、記憶抽屜、資料庫櫃立在牆邊，員工走過去開櫃。GroupChat 是同一間房的幾張桌子，主持人站在中間點下一位；你自己也有椅子，被點名就開口，也可以起身打斷。重點是看見誰在場、誰站在書架還是工具台、手上是哪一頁。
- 需要重新設計的地方：檢索結果現在寫進系統提示，聊天視窗預設沒有把「抽了哪幾頁」攤在桌上。空間辦公室要把這一步變成看得見的動作。Gradio 與 BrowserQwen 的 workstation 是對話視窗，用來檢查呼叫。MCP 是否沒有 sandbox、code interpreter 的目錄掛載是否足夠、兩套關鍵詞策略哪一個會在裝到的版本生效，都尚未跑過。官方的 Qwen Chat 後端說法與長文基準成績，尚未確認。

## 初步看法

- 最有價值的部分：agent、工具、檢索段落是同一輪裡的三個物件，而且工具呼叫會留在訊息串裡讓人展開看。
- 最大限制或疑問：它是給開發者呼叫的框架。檢索頁面寫進系統提示；GUI 是聊天加上插件清單。隔離邊界，以及文件和程式的預設差異，都還沒跑過。
- 是否值得進一步研究或親自體驗：值得，尤其是「抽頁上桌」和「打開工具抽屜」在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Repository：https://github.com/QwenLM/Qwen-Agent （本次閱讀 `main` `31a4d36`，2026-03-04）
- License：Apache License 2.0
- Documentation：https://qwenlm.github.io/Qwen-Agent/en/ （repo 內 `qwen-agent-docs` 的 guide：agent、tool、rag、mcp、context）
- README、`browser_qwen.md`
- 組裝相關程式：`qwen_agent/agents/assistant.py`、`fncall_agent.py`、`memory/memory.py`、`tools/retrieval.py`、`tools/doc_parser.py`、`tools/mcp_manager.py`、`tools/code_interpreter.py`、`gui/web_ui.py`、`gui/utils.py`
