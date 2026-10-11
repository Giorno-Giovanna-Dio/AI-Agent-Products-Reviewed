# cc-haha

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cc-haha-cell-c2d2/reviews/cc-haha.md)
>
> Cell ID：[https://github.com/NanmiCoder/cc-haha](https://github.com/NanmiCoder/cc-haha)
>
> Status：`untried`
>
> Category：本機桌面程式工作台（ADE）
>
> Last updated：2026-10-09

## 產品介紹

cc-haha 是裝在自己電腦上的開源桌面應用，給想用白話改專案、又要親眼看到改了哪些檔的人。你選一個資料夾、說出目標，內建的 CLI 核心 `claude-haha` 會讀檔、改檔、跑指令。視窗分成三塊：左邊是專案和歷史，中間是對話，右邊可以拉出 diff、檔案和網頁預覽。公開發行名稱是 Claude Code Haha；這次閱讀對到 2026-10-08 的 v0.7.0 文件與程式結構，沒有安裝。

官方入門頁寫明，這份 CLI 是從 Claude Code 原始碼修出來的，已經打進 macOS、Windows、Linux 安裝包，不必先裝官方 CLI 或 Node.js。桌面、手機瀏覽器（H5）和即時通訊都連到同一台電腦上的本機 Server；真正跑工具的仍是 CLI 子程序。工作階段、設定、技能和記憶預設放在 `~/.claude`。專案本身沒有用來收集對話的雲端帳號。模型、MCP、即時通訊和更新檢查，會把你選用的內容送到對應的第三方。

## 主要 Features

### 一條本機會話，多個入口

每個工作階段是一個 CLI 子程序。桌面、H5 和 IM adapter 走同一套本機 REST 與 WebSocket，只是登入方式不同。手機上看進度、補一句話，或從微信、飛書、釘釘、Telegram、WhatsApp、企業微信、QQ、Slack 下指令，任務仍在這台開著的電腦上執行。H5 可以看對話、附件和權限按鈕；桌面工作區、內嵌終端、Computer Use 和桌面寵物留在電腦上。

### 五檔權限與回合檢查點

預設是「詢問權限」：改檔或跑高風險指令前先停下來，可選這一次、這條工作階段、或拒絕。另外還有自動接受編輯、自動模式、只出計畫、以及跳過權限。一輪做完會列出改過的檔，可以連檔案和對話一起退回，或只退對話。檢查點記的是編輯工具碰過的檔；經由 shell 寫出去的檔不在裡面，要靠 git 兜底。等你審批時，標籤會亮琥珀色，直到你處理完。

### 分支、worktree 與並行工作階段

新的工作階段可以選分支，或開獨立 worktree，讓試驗離開主工作目錄。也可以叫它再開工作階段分頭做。文件寫一組協作同時最多三個工作階段，Git 專案用獨立 worktree，彼此用訊息回報。每個子工作階段仍走自己的權限；別的工作階段傳來的訊息，要你在這一條上另外批准。

### 子 Agent、團隊與 Workflow

活動面板把這一條工作階段裡的待辦、子 Agent、背景工作和團隊成員放在一起。內建角色包含探索（Explore）、規劃（Plan）和收工驗收（verification）等；自訂 Agent 寫成 Markdown，使用者級在 `~/.claude/agents/`，專案級跟倉庫走。Agent Teams 有隊長、具名隊友和互相傳訊。Workflow 是另一層：模型寫一份編排腳本，在背景用子代理做並行或流水線。工具說明要求使用者明確開口（或打開他們稱為 ultracode 的選項）才會跑，避免一項普通任務默默拉起大量代理。階段畫面和斷點續跑的手感尚未實測。

### 技能市場

技能是給 Agent 的手冊，Agent 是帶著自己上下文去辦事的分身。側邊欄的技能市場從 ClawHub 和 SkillHub 放出一份隨應用發布的精選清單（文件寫近 400 個），卡片上的安全標記來自來源方的掃描。安裝前可以看它會跑哪些指令、Hook 和網路，這份清單仍要人自己讀過。桌面也讀 `~/.agents/skills/`，和其他客戶端共用同一份技能。

### 在同一套核心裡換模型

Claude、ChatGPT、Grok 可以登官方帳號；DeepSeek、Kimi、智譜 GLM 等有現成 API 預設；LM Studio 和 Ollama 的本機端點也能接。子 Agent 可以沿用主工作階段的模型，或單獨指定。這是在同一份 harness 裡換供應商和模型。

### Computer Use 與桌面寵物

Computer Use 讓 Agent 截圖、點擊、輸入，去操作沒有 API 的桌面軟體。macOS 文件寫原生元件把動作送給目標應用，螢幕上是虛擬游標，真實滑鼠和鍵盤仍可自己用；Windows 的相容執行器會移動真實滑鼠；Linux 文件寫尚未提供執行器。同一時間只有一個工作階段能佔用控制鎖。桌面說明寫確認啟用後，不再逐個應用彈出授權；架構文件仍描述前台應用與授權等級檢查。兩份說法怎麼同時生效，尚未實測。

桌面寵物預設關閉。搭搭、弧弧、補補、回回會依任務改動作：在做事、在等你、或失敗。旁邊可展開進行中的任務，點進去會打開主視窗並跳到該工作階段。寵物不能代批權限，H5 裡也看不到它。

### 審閱與軌跡

右側工作區可以對未提交改動、分支或某次提交看 diff，也能在應用內開瀏覽器預覽。軌跡把同一條工作階段拆成系統提示、注入的記憶與技能、模型回覆和每一次工具呼叫，方便查它為什麼這樣改。

## 主打賣點

- 它最想被記住的是：本機優先的 Claude Code 桌面殼。終端裡的權限、子 Agent、技能和記憶變成面板和檢查點，人離開座位仍能用手機或聊天軟體接同一條工作階段。
- Conductor 把 Claude Code、Codex、Cursor Agent、OpenCode 等不同 CLI 放進同一個 macOS 控制台，每個任務有自己的 workspace。cc-haha 自帶一份 CLI 核心，在這份核心裡切換模型和權限。兩邊都有 worktree 和 diff。cc-haha 另外把技能市場、Computer Use、桌面寵物，以及微信、飛書、Telegram 等入口放進同一個應用。官方描述把它寫成另一種本機桌面工作台。
- 多 Agent 角色、檔案式記憶、五檔權限、worktree 和 MCP，入門頁直接說是同一套 CLI 能力換成圖形介面。桌面端加上的是看得到的等待狀態、回合回滾、精選技能市場、遠端批准，以及用寵物動作表示「該不該切回來」。

## 使用情境

### 在自己的電腦上改一個 Git 專案

- 適合誰：想把目標說清楚，然後自己看 diff 再放行的人。
- 在什麼情況使用：功能、修 bug 或改畫面都在一個資料夾裡完成，而且你在場。
- 帶來的價值：每一輪改了哪些檔、哪次工具呼叫失敗，都留在對話和軌跡裡；不滿意可以退回這一輪編輯工具碰過的檔。

### 人離開座位，電腦繼續跑

- 適合誰：任務會停下來等批准，而人可能在手機上。
- 在什麼情況使用：桌面應用開著，H5 或已配對的 IM 連到同一條工作階段。
- 帶來的價值：進度、補一句話和權限決定可以遠端做。工作區、終端和 Computer Use 仍在電腦前。公網隧道或 IM 會經過該服務商，文件把這點寫在隱私說明裡。

### 大範圍調查或套用既有做法

- 適合誰：一次要看很多模組，或已經有一套固定流程的人。
- 在什麼情況使用：派 Explore 平行讀碼，收工時用 verification 跑測試；需要大量子代理時明確要求 Workflow。想少重複交代時，從技能市場裝一本手冊，新工作階段才會載入。
- 帶來的價值：調查的中間過程留在子 Agent 自己的上下文，主對話只收回結論。技能佔上下文，裝太多不會自動變強。

## 我們可以學什麼

- 值得借鑑的 product idea：執行只有一個本機核心，桌面、手機和聊天軟體是同一條工作階段的不同門口。等待批准是一等狀態，標籤、側邊欄和寵物旁的任務列用同一種琥珀色告訴你該回來。
- 值得借鑑的 interaction / workflow：回合檢查點和 shell 寫檔分開；子工作階段的訊息不能代替權限；Workflow 要人明確開口才放大成多代理；技能是手冊、Agent 是分身，市場上的安全標記要標成「來源方說的」。
- 在 2D workspace 裡會變成什麼：一層平面樓層。每個工作階段是一張桌子，獨立 worktree 是隔開的桌子，檔案不會掉回主桌。待辦進度是桌上的紙。等你批准的桌子亮琥珀燈，你走過去才簽名。寵物的動作變成像素角色：低頭做事、東張西望、垂頭。技能市場是樓層上的架子，每本手冊標著來源掃描結果。手機和 IM 是同一張桌子的遙控，人還在這層樓的工作裡。macOS 的 Computer Use 是角色去操作另一面螢幕，你的游標還在；Windows 則是它拿走這張桌子的真實滑鼠，樓層上要標出來。
- 在 3D workspace 裡會變成什麼：可走進的辦公室。你走到某張工位，看見誰在改哪個 worktree，牆上是這一輪 diff。verification 是走到牆前給出通過、失敗或部分通過的人。Agent Team 是同一間裡能傳紙條的同事，隊長在門口收收斂。記憶是檔案櫃裡的四類筆記：使用者偏好、回饋、專案動態、外部連結。Workflow 是你明確下令才開工的產線，階段可以中斷再續。你人在外面時，工位上的人繼續做，你用對講機批准；寵物的小動作讓你從走廊就看出這張工位在等你。
- 不值得照搬或需要重新設計的地方：現在的主畫面是側邊欄、對話、工作區和活動面板，那是 ADE dashboard。2D 樓層和 3D 辦公室要搬走的是工位、等待燈、diff 牆和遠端批准，而不是把整塊側欄貼進場景。Computer Use 的啟用範圍、Windows 截圖是否含未授權視窗、以及 H5 令牌，都要在空間裡變成看得到的邊界。技能「精選」不是安全保證。

## 初步看法

- 最有價值的部分：同一條本機會話可以從桌面、手機和聊天軟體接上，而且「在等你」被做成看得到的狀態。回合回滾把人的檢查點和工具呼叫對齊。
- 最大限制或疑問：尚未安裝，權限手感、worktree 隔離、Workflow 續跑和 Computer Use 是否真的不搶 macOS 滑鼠都尚未確認。大量多 Agent 與記憶行為沿用 Claude Code 核心，桌面文件和架構文件對 Computer Use 授權的寫法也不完全同一句。隱私頁註明更新於 2026-08-05，和 v0.7.0 之間有沒有漏記的網路行為，尚未確認。
- 是否值得進一步研究或親自體驗：值得看遠端批准和在場狀態，再決定要不要把寵物式的動作放進 2D 角色或 3D 工位。不必先複製整個 Electron 工作台。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：https://cchaha.ai
- Repository：https://github.com/NanmiCoder/cc-haha
- Documentation：https://github.com/NanmiCoder/cc-haha/blob/main/docs/start/index.md 、https://github.com/NanmiCoder/cc-haha/blob/main/docs/desktop/index.md 、https://github.com/NanmiCoder/cc-haha/blob/main/docs/desktop/sessions.md 、https://github.com/NanmiCoder/cc-haha/blob/main/docs/desktop/agents.md 、https://github.com/NanmiCoder/cc-haha/blob/main/docs/desktop/skills.md 、https://github.com/NanmiCoder/cc-haha/blob/main/docs/desktop/computer-use.md 、https://github.com/NanmiCoder/cc-haha/blob/main/docs/desktop/pets.md 、https://github.com/NanmiCoder/cc-haha/blob/main/docs/desktop/remote.md 、https://github.com/NanmiCoder/cc-haha/blob/main/docs/internals/index.md 、https://github.com/NanmiCoder/cc-haha/blob/main/docs/start/privacy.md
- Release：https://github.com/NanmiCoder/cc-haha/releases/tag/v0.7.0
