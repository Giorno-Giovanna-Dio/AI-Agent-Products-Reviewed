# Univer

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-cell-80a8/reviews/univer.md)
>
> Cell ID：[https://github.com/dream-num/univer](https://github.com/dream-num/univer)
>
> Status：`untried`
>
> Category：可嵌入的 Office 文件 runtime
>
> Last updated：2026-10-10

## 產品介紹

Univer 是 DreamNum 的開源 Office SDK。它把試算表、文件、簡報做成可以嵌進自己產品的編輯 runtime：同一套模型在瀏覽器裡畫出來給人改，也可以在 Node.js 裡無介面運算。官方標語是 “The Office Harness for AI Agents”。這裡的 harness 指的是人和 agent 可以操作同一份 Office 內容，不是這個 repo 自己在派工、排座位或記住目標。

這個 repository 擁有的是開源 runtime：插件系統、Facade API、公式引擎、Canvas 繪製，以及 Sheets、Docs、Slides 的模型與編輯介面，包含留言、驗證與格式。倉庫說明寫明，商業協作、匯入匯出、伺服器與企業能力屬於 Univer Pro，不在本 repo。把人和 agent 放在一起建立、協作、審閱 Office 內容的產品，是另外一個專案 [Univer Workspace](https://github.com/dream-num/univer-workspace)，建在這套 SDK 上。本 Cell 沒有實際嵌入或執行。

## 主要 Features

### 一份文件，兩種入口

使用者把 SDK 嵌進自己的頁面，或在 Node 上跑同一套架構。試算表有公式、篩選、排序、資料驗證、條件格式、附註與表格；文件有富文本模型與編輯介面；簡報有資料模型與編輯套件。官方把這三種表面放在同一套插件、命令與 Facade 上，所以人在畫面上改的，和程式在伺服器上讀寫的，可以是同一份內容。Sheets 是目前最完整的表面。簡報在開源側仍在演進，和官網「完整簡報引擎」的說法差多少，尚未確認。

官網還把畫布、關聯表、PDF 和試算表、文件、簡報放進同一個 runtime，並說一份 `.univer` 裡可以互相嵌入，來源一改、引用跟著更新。本 repo 明確擁有的是 Sheets、Docs、Slides。畫布、關聯表、PDF 在這份開源碼裡完整到什麼程度，尚未確認。README 把 Bases 的資料庫模型放在 Pro。

### 公式是這份成品的計算

開源公式引擎負責解析、函式、相依與計算，瀏覽器與 Node 走同一條路。人和 agent 若都呼叫這套引擎，看到的是同一組算出來的值，而不是各自貼一份數字。進階效能、跨檔參照，文件指向 Pro 的公式套件。官網宣稱 Rust 計算 runtime、五百多個內建函式，以及 SpreadsheetBench 的準確率。這些是官方說法，本 Cell 尚未驗證，也尚未確認它們是否屬於這份 Apache-2.0 的引擎。

### 留言釘在內容上

開源套件提供串留言：可以寫在儲存格或文件上，回覆、修改、標成已解決。留言跟著那一格或那一段，而不是浮在產品外面的聊天室。文件寫，跨人同步要再接協作與 Pro 的留言資料源；留言的讀寫權限要單獨覆蓋，不會自動沿用「能不能改這份文件」。

### 權限點在文件上，身份在應用裡

試算表可以在活頁簿、工作表、儲存格範圍上設保護，分開限制編輯、檢視、複製、留言等操作。文件同時寫明：這是可擴充的基礎能力。組織、名單、權限規則的持久化要應用自己接，所以只開編輯器時，權限清單是空的是預期情況。協作層的身份也是同一種切分：協作 SDK 提供 OT、修訂、快照、即時同步與在場狀態，並用 Middleware 讓應用擋下請求。它不提供使用者、角色、層級、分享或檔案空間。留言與草稿的政策要另外裝，不會繼承核心的編輯規則。

### 草稿、檢查、再合併

Office SDK 文件描述一條給 agent 的路徑：載入一份 Unit（一份文件實例，例如一本 workbook），檢查結構，執行修改，再用渲染截圖與版面檢查看結果。隔離草稿官方叫 Worktree：agent 在草稿上改多輪，標成 Ready 後凍結那一版，人在網頁上看渲染結果，選擇合併回主幹或退回再改。文件寫明這是文件內容的隔離草稿，不是 Git worktree。合併仍要通過主幹的授權與提交。

這條路徑拆在兩層。協作 SDK 擁有草稿的服務、房間與合併；AI SDK 提供可組合的載入、檢查、執行與截圖，命令名稱、身份與授權由宿主應用擁有。README 把即時協作、編輯歷史與 Worktree 所需的協作能力列在 Pro，並寫套件與授權依功能而異。本 repo 的套件目錄是編輯核心，沒有協作伺服器，也沒有文件裡引用的 CLI 套件。AI SDK 各套件放在哪個 repo、哪一種授權，尚未逐包確認。

## 主打賣點

- 它最想被記住的是：一套可嵌入、可自架的 Office runtime。試算表、文件、簡報共用模型，人在畫面上改，程式與 agent 用同一套介面讀寫。
- 和「再開一個雲端試算表網站」相比，辨識度在於宿主擁有介面、身份與資料。官方強調瀏覽器與 Node 同構、插件可拆可換、無介面運算。我們沒有和其他套件並排實測。
- 「Office Harness for AI Agents」、人和 agent 並排改同一份檔，是這層 runtime 的用法，也是官網的說法。會建立員工、派工作、審閱一整間工作區的，是 [Univer Workspace](https://github.com/dream-num/univer-workspace) 以及 CLI、MCP、skills 等別的專案。那些不是這個引擎自己的編排器。
- 即時協作、匯入匯出、圖表、樞紐、進階公式，README 列為 Pro。開源核心已經能編輯、計算、留言、設保護點。把商業層的完整辦公室說成這個 repo 已內建，會超過倉庫自己的邊界。

## 使用情境

### 在自己的產品裡放一份共用的表

- 適合誰：要在 SaaS、內部工具或 BI 流程裡嵌試算表或文件，並讓程式能讀寫同一份內容的團隊。
- 在什麼情況使用：人在頁面上改格子或段落，自動化或 agent 用同一套 Facade 改同一份 Unit。
- 帶來的價值：審閱物件是那份算得動、畫得出來的文件，而不是另貼一份匯出檔。嵌入後的手感尚未確認。

### Agent 先改草稿，人看畫面再合併

- 適合誰：願意讓 agent 改 Office 內容，但要先看過渲染結果才進主檔的人。
- 在什麼情況使用：文件描述的 Worktree。Agent 在隔離草稿上改、做結構檢查與截圖，標成 Ready；人在網頁上選擇合併或退回。
- 帶來的價值：主檔不會被每一輪中間改動直接蓋掉。這條流程依賴協作能力，授權與套件邊界見上文，本 Cell 尚未跑過。

### 伺服器先算，人打開同一份來核對

- 適合誰：要在 Node 上做公式或文件處理，再把結果交還給人看的流程。
- 在什麼情況使用：無介面 runtime 載入同一種模型，算完或改完，人再用瀏覽器裡的編輯器打開。
- 帶來的價值：計算與畫面共用一份內容模型。官網的大規模效能數字尚未驗證。

## 我們可以學什麼

- **值得借鑑的 product idea**：未來的工作空間需要一份人和 agent 真正共用的文件成品。Univer 把這份成品做成 runtime：試算表、文件、簡報是同一套可計算、可渲染的物件，留言釘在物件上，權限點也釘在物件上。編排、目標、座位留給辦公室；引擎只回答「這一份現在長什麼樣、誰可以改哪一塊」。
- **值得借鑑的 interaction / workflow**：委派是對同一份 Unit 下修改，而不是另開一份聊天摘要。Agent 的檢查分成結構（格子、段落、投影片的內容）和畫面（渲染截圖、版面檢查）。交接用草稿：改動先留在隔離稿，Ready 凍結待審的那一版，人可以繼續改、退回或合併。合併才回到主幹，而且仍走主幹的授權。在場狀態（房間、Presence）表示誰正連著這份文件。身份、角色、檔案空間仍由辦公室自己的門禁決定，引擎留 Middleware 讓應用擋下讀寫、留言與合併。
- **在 2D workspace 裡會變成什麼**：一層平面辦公室，地板是 pixel 辦公室、地圖或樓層。房間中央是一張共用桌，桌上放著一份 Univer 檔：試算表、文件、簡報是這份成品的頁，格子不是地板。人和 agent 站在桌子四周。留言是釘在某一格或某一段上的紙條。保護範圍是桌面上被罩住的一塊，罩住的是文件，不是房間的門。公式引擎是桌子底下的計算機，四周的人看到同一組數字。Worktree 是旁邊一張小桌，上面是同一份文件的草稿；標成 Ready 後，人走過去看已經畫出來的那一頁，再決定搬回主桌或退回去改。
- **在 3D workspace 裡會變成什麼**：一間走得進去的辦公室，接近 [Agent Office](https://github.com/AgentSystemLabs/agent-office) 那種能看見誰在場、人站在哪裡。會議室裡有一塊共用板，板上是那一本 workbook、那一份文件或那組投影片。人走過去才站到板前。協作層的 Presence 可以變成「誰正站在這塊板旁邊」，但 Univer 沒有把使用者做成 3D 員工，在場的身體是辦公室要接上的。Agent 是站在板前改這份文件的人，不是格子本身。草稿在側間的另一塊板上。Ready 表示這間側間停在待審的那一版，人走進側間看渲染結果，合併後才把改動帶回主會議室的那塊板。確認方式是站到板前面，看算出來的表、排好的頁或投影片。
- **不值得照搬或需要重新設計的地方**：不要把試算表格子做成房間，也不要把外掛清單當成員工名冊。不要把標語裡的 harness 讀成這裡已經有目標、記憶和座位；那些在 Workspace 與各個宿主，不在這個引擎。協作 SDK 明文不提供使用者與檔案空間，進辦公室時門禁仍要自己設計，而且留言、草稿、合併各自要一條政策。開源編輯核心和 Pro 的協作、匯入、進階公式是兩層，workspace 若嵌這套成品，要先決定哪一層在桌上。官網的效能與評測數字尚未驗證，不能直接當成這間辦公室的能力。

## 初步看法

- 最有價值的部分：一份可計算、可渲染的 Office 成品，讓人的畫面和 agent 的讀寫落在同一個物件上，並用釘在內容上的留言與「草稿、凍結、合併」分開中間改動和主檔。
- 最大限制或疑問：這個 repo 是文件 runtime，不是 agent 編排器。即時協作、Worktree、AI SDK 寫在 Office SDK 文件裡，倉庫自己把協作與伺服器劃給 Pro。AI SDK 套件的所在 repo 與授權尚未逐包確認。畫布、關聯表、PDF 是否都在這份開源碼裡，尚未確認。本 Cell 尚未使用。
- 是否值得進一步研究或親自體驗：值得，而且對象是「桌上那份文件」，不是把 Univer 當成 2D／3D 辦公室本體。若 workspace 要讓人與 agent 圍著同一份算得動的表或文件工作，這套 runtime 的模型、留言錨點和草稿審閱邊界值得接著看。先看 [Univer Workspace](https://github.com/dream-num/univer-workspace) 怎麼把這套引擎放進人和 agent 的工作區。

## Sources

- Official website：https://univer.ai/
- Repository：https://github.com/dream-num/univer
- Repository scope：https://github.com/dream-num/univer/blob/dev/DREAMNUM.md
- Documentation：https://docs.univer.ai
- AI SDK：https://docs.univer.ai/ai
- Worktree：https://docs.univer.ai/ai/worktree
- Collaboration SDK：https://docs.univer.ai/server/collaboration/overview
- Permission control：https://docs.univer.ai/guides/sheets/features/core/permission
- Comments：https://docs.univer.ai/guides/sheets/features/comments
- Univer Workspace（另一個產品，建在這套 SDK 上）：https://github.com/dream-num/univer-workspace
- License：Apache-2.0（GitHub license，本 repo `LICENSE`）
