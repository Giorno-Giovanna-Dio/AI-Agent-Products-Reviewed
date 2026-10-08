# OpenCode

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-opencode-cell-9e0b/reviews/opencode.md)
>
> Cell ID：[https://github.com/anomalyco/opencode](https://github.com/anomalyco/opencode)
>
> Status：`untried`
>
> Category：Open-source coding agent（終端機／桌面客戶端）
>
> Last updated：2026-10-08

## 產品介紹

OpenCode 是開源的 AI coding agent。你在一個專案目錄裡打開它，用自然語言
請它讀程式、改檔、跑指令。介面有終端機 TUI、桌面 app，以及 IDE 擴充。
模型不綁一家：自己的 provider key，或官方整理過的 OpenCode Zen，都能接。

它比較像**一位可換服裝的員工**，加上這位員工可以再叫專家進來。終端機
只是看待這位員工的一個窗口。`opencode` 啟動時會同時帶起本地 HTTP
server；TUI、桌面、IDE 都是連到這個 server 的客戶端。`opencode serve`
可以單獨把 server 開著，讓別的程式用同一套 session 來驅動。尚未實際
安裝或執行。

Repository 已從 `sst/opencode` 轉到 `anomalyco/opencode`。官方網站是
[opencode.ai](https://opencode.ai)。

## 主要 Features

### 同一張桌子上的兩種身份：Build 與 Plan

內建兩個主要 agent，用 Tab 在**同一個 session** 裡切換。Build 是預設，
檔案與 shell 都開著，用來動手改。Plan 用來先看、先想：寫檔與 bash 預設
要先問過你。切換的是這個人此刻准不准動東西，對話還在同一條線上。

另外有幾個不出現在選單裡的系統角色：把過長上下文壓成摘要、幫 session
取標題、寫摘要。它們在背景跑，人不用指派。

### 子 agent 開出子 session

主要 agent 可以用 Task 工具叫子 agent，你也可以在訊息裡 `@` 點名。內建
三種：

- **General**：多步驟、可平行的一般工作，工具幾乎全開，但文件寫明它不用
  todo 工具。
- **Explore**：只讀，在這個專案裡找檔案、搜尋、回答結構問題。
- **Scout**：只讀，向外看文件與依賴。它可以把依賴的原始碼 clone 到
  OpenCode 管理的快取裡對照，而不改你的工作目錄。

子 agent 會生出**子 session**。TUI 用方向鍵走這棵樹：往下進入第一個子
session，左右切換兄弟，往上回到父 session。Server 也能列出某個 session
的 children、待辦與 diff。

你也可以用 Markdown 或設定檔自訂 agent：各自的說明、模型、步數上限與
權限。`permission.task` 能限制這個 agent 可以叫哪些子 agent；被拒絕的
不會出現在 Task 工具的說明裡。文件同時寫明，人仍可用 `@` 直接點名，這條
限制擋的是模型自己叫人。

### 權限是每扇門的規則

每個動作是 allow、ask 或 deny。範圍涵蓋讀寫、搜尋、bash、派出子 agent、
載入 skill、網頁、問使用者問題，以及碰到專案 worktree 外面的路徑
（`external_directory`）。bash 與路徑可以用 pattern，**後寫的規則蓋過先
寫的**。同一個工具連叫三次且參數相同，會觸發 `doom_loop`，拿來打斷卡住
的迴圈。

人回應一次核准時可以選擇記住。規則能寫在全域，也能寫在單一 agent 上，
agent 的規則優先。Plan 就是用這套機制把「先不要改」做成身份，而不是另做
一個唯讀產品。

### 專案說明、skills，以及這次對話的檔案

`/init` 會看過專案並寫出 `AGENTS.md`，官方建議把它 commit 進 git，讓之後
的 session 知道怎麼 build、測試，以及哪些慣例檔名看不出來。個人規則放在
`~/.config/opencode/AGENTS.md`，不進 repo。沒有 `AGENTS.md` 時會退回
`CLAUDE.md`。

Skills 是做某件事才載入的 `SKILL.md`，專案與家目錄都有，也認得 Claude 與
`.agents/skills` 的位置。權限可以按 skill 名稱允許、詢問或藏起來。

這次對話的進度在 session 上：待辦、檔案 diff、從某一則訊息 fork 出新
session、`/undo` 與 `/redo` 把改動與當時的提問一起倒回。對話預設不分享；
`/share` 才產生連結。長對話由那個隱藏的壓縮角色收成較短的續寫上下文。
這些是**這次工作的紀錄**，和寫進 repo 的 `AGENTS.md` 是兩層。

### 一個 server，很多個窗口

TUI 是 client。桌面 app 與 IDE 外掛連的是同一個本地 server。Server 有
session、權限回應、abort、事件串流，也可以非同步送 prompt。預設聽
`127.0.0.1:4096`。這讓「人在終端機看」和「另一個程式在旁邊看同一場工作」
可以同時存在。桌面 app 還能在回覆完成或 session 出錯時發系統通知。細節
尚未實測。

## 主打賣點

- **開源、模型可換的 coding agent**：終端機是主場，桌面與 IDE 是同一套
  server 的其他窗口。本 repo 裡的 [Conductor](reviews/conductor.md)、
  [Maestro](reviews/maestro.md)、[Orca](reviews/orca.md)、
  [Agent Office](reviews/agent-office.md) 多半把 OpenCode 當成可僱用的
  CLI 員工；這個 Cell 看的是那位員工自己怎麼工作。
- **身份與權限綁在一起**：Build／Plan 不是兩種聊天模式名稱，而是同一張
  桌子上「能不能改、能不能跑指令」的兩套門禁。自訂 agent 沿用同一套。
- **委派會留下一棵走得進去的 session 樹**：子工作有自己的對話，父 session
  還在。人可以下去看專家在做什麼，再走回主桌。
- **專案記憶與這次對話分開**：`AGENTS.md` 與 skills 是下次還在的說明；
  session 的待辦、diff、fork、undo 是這一回合的現場。

終端機裡的讀寫搜尋、MCP、slash command，在其他 coding agent 裡已經常見。
OpenCode 比較特別的是把 client／server 拆開、把子 agent 做成可走訪的子
session，以及用 allow／ask／deny 把「計畫」和「施工」收成同一個人的兩套
權限。桌面 app 是另一個客戶端，它仍然是這位員工的視窗，和
[Conductor](reviews/conductor.md) 那種同時編排很多 CLI 的控制台是不同層。

## 使用情境

### 先在 Plan 裡對齊，再切回 Build 動手

- 適合誰：不想讓 agent 一看完需求就改檔的人。
- 在什麼情況使用：Tab 到 Plan，把功能與限制講清楚，必要時附上畫面；同意
  之後切回 Build，請它照剛才的結論改。
- 帶來的價值：計畫和施工共用同一條對話，但施工前的門禁不同。不滿意可以用
  `/undo` 連改動帶提問一起倒回。

### 主桌派只讀的探路，自己留在原對話

- 適合誰：大型 repo，或要對照上游依賴才敢改的人。
- 在什麼情況使用：讓 Explore 在專案裡搜，或讓 Scout 把依賴源碼放到管理
  快取裡對照；人用方向鍵進入子 session 看過程，再回到父 session 決定要
  不要改。
- 帶來的價值：搜尋的上下文留在側房，主桌只接結論。Scout 碰工作目錄外的
  路徑時，還有 `external_directory` 這道門。

### 別的介面看同一場工作

- 適合誰：想在終端機裡做，同時讓 IDE、桌面或自己的工具讀 session、diff
  與權限詢問的人。
- 在什麼情況使用：讓 `opencode` 或 `opencode serve` 開著，其他 client 連
  同一個本地 server。
- 帶來的價值：工作狀態在 server 上，窗口可以換。這也是為什麼許多 ADE 能
  把 OpenCode 當成員工接進來。

## 我們可以學什麼

- **值得借鑑的 product idea**：一位員工、兩套門禁、一棵子 session。Build
  與 Plan 是同一個人換徽章。子 agent 是從這張桌子走出去的側房，有父節點，
  做完主桌還在。專案的 `AGENTS.md` 是牆上的慣例，session 的待辦與 diff 是
  桌上這一回合的紙。
- **值得借鑑的 interaction / workflow**：人可以中途換徽章（Tab），可以
  走進子 session 再走出來，可以在門口對一次動作說允許並記住，也可以把某一
  則訊息 fork 成另一條對話。卡住時，連續三次相同工具呼叫會被標出來。壓縮
  長對話的是背景職員，不必占一張辦公桌。
- **在 2D workspace 裡會變成什麼**：平面樓層上，一個專案 worktree 是一間
  房。主 session 是房內那張固定辦公桌，桌牌會在 Build 與 Plan 之間換，換的
  時候檔案櫃和終端機的鎖跟著變。General、Explore、Scout 的子 session 是桌
  後排成一列的側桌；往下走到第一張，左右換張，往上回到主桌。Explore 的側
  桌沒有筆。Scout 的側桌連著工作目錄外的依賴書架，跨過那條線要再經過一道
  `external_directory` 的門。待辦與 diff 貼在主桌邊緣。`/undo` 是把桌子倒
  回某一張紙。TUI、桌面、IDE 是這間房的不同窗口，房況在本地 server 上。
- **在 3D workspace 裡會變成什麼**：你走進專案辦公室，看見一個人坐在主桌。
  Tab 時他換上施工或規劃的徽章，櫃子的鎖跟著響。他叫人時，走廊裡多出房間：
  你走下去看 General 在平行做事，左右是其他子 session，走回走廊就回到主桌。
  Explore 的房間是玻璃隔間，裡面的人只能翻資料。Scout 的房間有一扇通往
  館外書庫的門，書庫是依賴快取，不是這間辦公室的檔案櫃。權限詢問是有人站
  在門口等你點頭；選記住，就變成門上的長期告示。Fork 是從對話的某一刻複製
  出相鄰的一間辦公室。壓縮、標題、摘要是不站在樓層上的後勤。多個 client
  是不同的門可以同時看見同一間辦公室。
- **不值得照搬或需要重新設計的地方**：TUI 的上下左右是一次只看一間房的樹狀
  導覽。2D／3D workspace 應該同時看見主桌和側房，而不是只能走進其中一間。
  權限「後寫的規則贏」很適合設定檔，不適合讓人在地板上猜現在哪條告示有效，
  門上要顯示**最後生效**的那條。`@` 可以叫到 Task 權限已經拒絕的子 agent，
  若辦公室只畫「這個人不能叫誰」，會和實際行為不一致，兩種召喚路徑都要看
  得見。隱藏的壓縮角色不該被畫成多餘的員工。桌面 app 是 client，不要把它
  當成我們要做的辦公室。

## 初步看法

- 最有價值的部分：session 樹加上可換的權限身份。這直接對應「主桌／側房」
  和「同一個人何時准許動手」。client／server 拆開，也說明辦公室的狀態不該
  綁死在某一個視窗。
- 最大限制或疑問：它是一位 coding agent，不是多員工的樓層，也沒有把 git
  worktree 本身當成一等的空間單位（worktree 主要出現在權限與外掛上下文裡）。
  子 session 在 TUI 裡是否真的讓人看得懂平行工作，尚未親測。桌面 app 與
  TUI 同時看同一 server 時的手感也尚未確認。
- 是否值得進一步研究或親自體驗：值得當「員工 runtime」參考，尤其是許多
  其他 Cell 已經把 OpenCode 雇進來。若要驗證側房導覽，再在
  `/workspace-labs/opencode` 跑一個會叫 Explore 的 session 即可。

## 後續補充（選填）

Not tried yet。官方文件已足夠回答 workspace 需要的問題：誰是主要員工、
子工作如何變成可走訪的 session、權限如何把計畫與施工分開、狀態放在本地
server 而不是某個視窗裡。

## Sources

- [OpenCode repository](https://github.com/anomalyco/opencode)
- [OpenCode docs](https://opencode.ai/docs)
- [Agents](https://opencode.ai/docs/agents/)
- [Permissions](https://opencode.ai/docs/permissions/)
- [Rules](https://opencode.ai/docs/rules/)
- [Agent Skills](https://opencode.ai/docs/skills/)
- [Server](https://opencode.ai/docs/server/)
- [先前網址 sst/opencode](https://github.com/sst/opencode)
