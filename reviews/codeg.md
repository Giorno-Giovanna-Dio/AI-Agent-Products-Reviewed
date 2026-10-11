# Codeg

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-codeg-cell-fb52/reviews/codeg.md)
>
> Cell ID：[https://github.com/spacering-net/codeg](https://github.com/spacering-net/codeg)
>
> Status：`untried`
>
> Category：多 agent 程式 ADE（session 彙整與跨 runtime 委派）
>
> Last updated：2026-10-10

## 產品介紹

Codeg 是開源的多 agent 程式工作台。它不自己當 coding agent，而是用
[Agent Client Protocol](https://agentclientprotocol.com) 驅動你機器上的
Claude Code、Codex、OpenCode、Pi、Grok 等官方 CLI，把牠們收進同一套對話、
diff 和權限提示。桌面 app、自己架的 server，或 Docker，跑的是同一套核心。
手機和 Telegram、Lark、微信只是連回這台機器的入口，檔案和 agent 行程不在
手機上。

人把專案資料夾打開，可以匯入各 agent 已經寫在磁碟上的舊 session，或新開一輪
對話。需要別人幫忙時，在同一句話裡 `@` 另一種 agent，由現在這位 lead 交出
一段寫好的任務。想把工作放下就走，則改用 To-do：每張工單自己一份 git
worktree，做完停在 review，等人看過 diff 才合併。

這是 ADE dashboard，和 Conductor、Maestro 同一類控制台。它不是平面辦公室，
也不是走得進去的辦公室。

## 主要 Features

### 一種對話殼，包很多種 agent

官方文件寫內建十五種 agent，也可以再接任何 ACP agent。Codeg 當 client，
agent CLI 當 server。工具呼叫、即時 diff、計畫和權限卡都畫成同一種結構化
對話。各 CLI 的登入、模型和更新仍留在原廠；Codeg 不另做一套模型。

### 把各家 session 收成可搜尋的歷史

Claude Code、Codex、OpenCode 等各自把紀錄放在不同目錄或資料庫。Codeg 掃這些
原生存放處，依專案目錄匯進來，可以搜尋，也可以用原來的 agent 接著做。匯入
之後，用 `@` 點一則舊 session，另一位 agent 可以讀到標題、用了誰、工作目錄、
狀態和近期訊息。這是唯讀查詢，不會把那則舊 session 接起來繼續跑。各 agent
仍然看不到彼此的存放處，中間人是 Codeg。

### 跨種類委派，但工人從零開始

委派預設關閉。打開之後，能接受 MCP 的 lead 多一個委派工具，經由旁邊的
`codeg-mcp` 叫 Codeg 再開一則真正的 session。交出去立刻返回，lead 可以同時
派好幾位。工人看不到 lead 的對話和開著的檔案，只看到任務文字，所以 brief
必須自己就完整。預設只准一層。官方文件寫 OpenClaw 和 Pi 收不到這個工具，
只能當工人，不能當 lead。

工人預設坐在 lead 的同一個資料夾，不會自動拿到 worktree。兩個人改同一檔會
互踩，而且彼此不知道對方存在。委派適合把結果收回同一則回答。要各自分開落地，
文件把人指向 To-do。

人看得到過程：lead 的回覆裡有委派卡，旁邊可以打開工人的即時對話。那個畫面
不能代打下一輪，但權限問題會出現在那裡。工人在等你時會標成 awaiting
approval。取消 lead 會連帶停掉它派出的工人。

### To-do：隔離的 checkout，強制停在 review

To-do 是一張看板，大致分成待做、進行中、需要你、完成。一張任務是標題、說明，
加上要用哪個 agent。開始時 Codeg 開一份 worktree，分支釘在當時的 commit，
所以你中途切分支不會把任務帶走。Agent 可以在任務分支上自由 commit，但不准
自己合併或推回基底分支。

做完一定先到 review。你看變更和 diff，後續可以選重做、繼續加、只問問題不要
改檔，或再檢查一遍。同意之後，由 agent 在自己的 session 裡處理合併，包含
衝突。資料夾可以打開「審過就自動落地」，但預設是關的。每個資料夾同時跑幾張
有上限，文件寫預設是 2。GitHub、GitLab 等的 issue 或 pull request 可以變成
這種任務。

### 人還在場的幾個入口

權限沒有全域「全部允許」。提示卡停在輸入框上方，一次一張，選項來自該 agent
自己的模式。回合還在跑時，支援的 agent 可以插話，不必等它講完。已完成的回覆
可以分叉成新 session，原對話留著；能不能從「這一則」而不是整段結尾分叉，
取決於 agent。

Infinite Canvas 是沒有邊界的畫板：依資料夾或 agent 分區，把根對話、檔案和
終端機釘在上面。卡片展開後仍是同一套可打字的對話。子 agent 不會變成畫布上的
卡片。官方文件自己把這塊板和地圖分開。

資料預設放在這台機器的 `~/.codeg/`。Codeg 沒有自己的雲端帳號。模型呼叫仍由
各 agent CLI 打到它們的供應商。

## 主打賣點

- 最想被記住的是：很多種 coding agent 的舊對話可以收進同一個地方，而且一種
  agent 可以把一段工作交給另一種，雙方都是看得到的 session。
- 和 Conductor、Maestro 的交集是多 runtime、worktree、人在旁邊看 diff。
  Codeg 較特別的是讀各家磁碟上的 session，以及 lead 用冷啟動 brief 跨種類
  派工。Conductor 在既有筆記裡更像 macOS 上並排比較不同 runtime 的車庫；
  Maestro 更強調清單式 Auto Run 和 moderator 群聊。這份筆記沒有並排實測。
- 內建 git、終端機、issue 面板、排程 automation、skills 和 MCP，是 ADE 裡
  常見的工程迴圈，不是新的工作單位。Infinite Canvas 是把同一套對話卡片自由
  擺放，不是另一種產品。

## 使用情境

### 換過好幾個 coding agent，歷史散在各處

- 適合誰：同一份 repo 上用過 Claude Code、Codex、OpenCode 或其他 CLI 的人。
- 在什麼情況使用：想搜尋上週某次對話，或讓現在這位 agent 讀到另一位上次做到哪。
- 帶來的價值：歷史不再鎖在單一 CLI 的目錄裡。查詢是唯讀的。真的要繼續做，
  仍是開新的工作，或用原來的 agent 把那則 session 接著跑。

### 一位 lead 指揮，另一種 agent 做專長或複查

- 適合誰：想留在自己習慣的對話裡，又想借另一種 agent 寫測試、改文件或看 diff 的人。
- 在什麼情況使用：工作可以寫成一段自足的任務，而且不需要跟工人來回澄清。
- 帶來的價值：工人是冷的，沒看過 lead 的理由，複查比較不容易被同一段敘事說服。
  代價是 brief 寫不好，結果就空。權限仍要人在工人的畫面上點。

### 多張工單同時做，但合併權留在人

- 適合誰：想離開鍵盤，又不要 agent 自己把分支合進主線的人。
- 在什麼情況使用：每張 To-do 一份 worktree，做完停在 review。issue 或 pull
  request 也可以直接變成工單。
- 帶來的價值：平行不會改到你正在用的那份 checkout。看 diff、退回、或同意
  合併，是分開的動作。

## 我們可以學什麼

- 值得借鑑的 product idea：session 是記憶的單位，agent 種類只是這則 session
  的身份。跨 agent 的交接不該假裝大家共用一段聊天，而該是一份寫給陌生人的
  brief，加上對舊 session 的唯讀摘錄。
- 值得借鑑的 interaction / workflow：委派和 To-do 是兩種隔離。委派共用資料夾，
  結果收回同一則回答。To-do 自動給 worktree，而且每條路都要經過 review 才算
  完成。後續指示還要分「重做」「加做」「只問不改」。等批准的工人必須自己走到
  人面前，不能只在巢狀抽屜裡安靜卡住。
- 在 2D workspace 裡會變成什麼：一層俯視樓層。每個專案是一塊地板，每份
  worktree 是旁邊一張自己的桌子，桌上標分支名。匯入的舊 session 是牆邊檔案櫃，
  抽屜寫著當初是哪種 agent 寫的。Lead 坐主桌，把一張寫滿的任務紙送到另一種
  agent 的桌子；對方沒聽到主桌的對話。To-do 看板在牆上，卡片只會移到人的審核桌，
  不會自己跳回主線。等人批准時，那張桌子舉手。Codeg 的無限畫布在這裡不該再是
  一塊貼對話卡片的板，那些卡片要變成你走過去的工位。
- 在 3D workspace 裡會變成什麼：一間走得進去的辦公室，像
  [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你看得到誰在場：
  Claude Code 在一張桌子，Codex 在另一張。To-do 的工人在自己的隔間，因為
  checkout 是分開的。被委派、又沒有另給目錄的工人站在 lead 的同一間房，檔案櫃
  是同一份，可能互相改到。你走向舉著權限單的人，工作才會繼續。審核是走廊盡頭的
  桌子，完成的 worktree 放在那裡等人收下。手機或 Telegram 是同一棟樓的側門，
  員工沒有被搬去別的機器。牆上的螢幕可以像 Codeg 那樣列出對話和 diff；螢幕不是
  辦公室。
- 不值得照搬或需要重新設計的地方：Codeg 是 ADE dashboard。側欄、看板和無限畫布
  都是在排同一套聊天面板。子 agent 在畫布上不可見，workspace 不該沿用這個隱藏。
  官方隱私頁寫 Codeg 自己不加 sandbox；檔案和終端機常常跑在 Codeg 行程裡，
  worktree 只隔離 git，不隔離權限。刪除對話在同一頁寫的是先藏起來，不是從資料庫
  擦掉，agent 自己的 transcript 也另有一份。這些在辦公室裡要做成看得到的門和抽屜。

## 初步看法

- 最有價值的部分：兩種交接被分清楚。一種是冷啟動、結果收回對話；一種是隔離
  checkout、人在 review 閘門放行。舊 session 可以當唯讀記憶被另一種 agent 讀到。
- 最大限制或疑問：尚未實際使用。委派是否穩定走 Codeg 的工具，而不是 agent
  自己的子 agent，文件承認曾經走岔，現在靠提示把通道綁回去，這份筆記沒有驗證。
  十五種 agent 的能力差很大。Computer use 和內建瀏覽器的分享範圍要以隱私頁為準，
  用之前要再讀一遍。
- 是否值得進一步研究或親自體驗：值得，尤其是「誰看得到誰的記憶」和「哪種平行
  工作該有自己的房間」。不必為了再確認一次 ADE 側欄而先裝起來。

## Sources

- Official docs：https://docs.codeg.app
- Repository：https://github.com/spacering-net/codeg
- Documentation：
  - [Conversation Aggregation](https://docs.codeg.app/guide/aggregation)
  - [Multi-Agent Collaboration](https://docs.codeg.app/guide/multi-agent)
  - [To-dos](https://docs.codeg.app/guide/tasks)
  - [Git & Worktrees](https://docs.codeg.app/guide/git)
  - [Infinite Canvas](https://docs.codeg.app/guide/canvas)
  - [The Workspace](https://docs.codeg.app/guide/workspace)
  - [Architecture](https://docs.codeg.app/reference/architecture)
  - [Privacy & Security](https://docs.codeg.app/reference/privacy)
- License：Apache-2.0（repository `LICENSE`）
- 研究時的版本：GitHub 最新 release 為 v0.34.0（2026-10-07）。行為隨版本改，以上依當天文件，沒有實測。
