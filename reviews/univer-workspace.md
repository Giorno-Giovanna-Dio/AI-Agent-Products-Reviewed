# Univer Workspace

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-workspace-cell-80a8/reviews/univer-workspace.md)
>
> Cell ID：[https://github.com/dream-num/univer-workspace](https://github.com/dream-num/univer-workspace)
>
> Status：`untried`
>
> Category：開源 Office 協作工作區（文件表面）
>
> Last updated：2026-10-10

## 產品介紹

Univer Workspace 是一套可自行部署的開源 Office 協作產品。人和 AI agent
在同一批表格、文件、簡報、關聯表和畫布上一起做、一起改，改完由人決定要不要
併回大家正在看的那一版。文件引擎是
[Univer](https://github.com/dream-num/univer)。這裡的 workspace 是**文件工作區**：
資料夾、表格、文件、簡報，像一套可自架的辦公室軟體。它是文件協作表面，不是
一間可以走進去的辦公室。

三個入口連到同一台 Workspace Server。Browser 用來直接編輯、整理空間、審改動。
Workspace Agent 裝在自己的電腦上，用對話指文件、看草稿；它建在 DSH（DeepSeek
Harness）上，自己沒有使用者表。CLI 給已經在跑的 agent 遠端操作同一批文件。
Server 管身份、存放和權限。文件留在伺服器，對話留在本機。預設連到
[space.univer.ai](https://space.univer.ai)。尚未安裝或執行。

## 主要 Features

### 個人空間與團隊空間裡的文件樹

一個人一個 Personal Space。團隊用 Team Space，成員角色是 admin、editor、
viewer，再加上不寫進成員表的 Space owner。樹狀 Node 管名稱和位置；真正的內容
是 Resource：Univer 文件（sheet、doc、slide、board、base）或一般檔案 Blob。
官方說這些文件可以互相引用，來源一改，引用跟著變。這部分尚未實測。

### 人和 agent 改同一份，agent 先待在草稿

大家共用的正本叫 trunk。Agent 不直接改正本，而是開 Worktree：一份隔離草稿。
生命週期是 draft → ready → merging → merged；衝突或失敗退回 ready；ready
可以 reopen 回 draft；draft 或 ready 可以 discard。標成 Ready 會凍結這一版，
要再改得先 reopen。合併以單份文件為單位：一份成功、另一份衝突時，成功的不會
被整批倒回。審閱預設看草稿，也可以開結構化雙欄比較。變更通知只叫畫面重新讀，
不從那條線把草稿內容推出去。

User Worktree 只有建立者看得到，還可以跨空間放文件。Team Worktree 綁一個
團隊空間。「誰可以來審」和「誰可以讀那份文件」分開。私人的團隊草稿，空間
owner 和 admin 看得到摘要，也能代為 reopen 或 discard，但讀不到草稿內容。
對整個空間可見的草稿，成員可以找到並唯讀審閱。

### 團隊 Issue 是任務單，草稿是另一套

Issue 只存在 Team Space，有空間內編號、留言、標籤、指派，以及指向檔案的引用。
時間線把留言和事件排在一起；標籤刪了、檔名改了，歷史仍留當時的名字。資料模型
寫明這一版 Issue 不和 Worktree 綁在一起，也不保存文件版本。所以「要做什麼」
和「這一版草稿」目前是兩套東西。

### 文件上的討論和版本掛在正本

五類文件在正本編輯器有串接留言。開得了檔就能留言，不必先有編輯權；改留言限
作者，刪除還要作者或 owner／admin。Worktree 和合併預覽不讀、也不寫正本留言。
正本另有版本歷史；草稿審閱不掛這套歷史。編輯器裡看得到協作者在線，那是**這份
文件的房間**，不是辦公室裡的座位。

### 權限在伺服器算

有效角色每次由伺服器從擁有者、團隊成員、個人空間的節點授權和連結分享算出來。
客戶端帶的角色不被信任。找不到的東西回 404，避免洩漏它存不存在。個人空間才能
直接分享和開連結；匿名讀者固定是 viewer，就算連結寫的是 editor，也不能改樹、
丟回收站或再分享。團隊空間用成員角色，不靠逐檔授權。空間也可以公開唯讀。
Worktree 一律要登入。

CLI 登入不把密碼交給 agent：它印出短網址和驗證碼，人在瀏覽器核准，agent 再
換一次自己的 session。Workspace Agent 的遠端權限仍以 Workspace 為準。

### Agent 先檢查，再交審閱連結

CLI 帶著和該版本對齊的操作說明。Agent 用程式介面改內容，再用結構化檢查、截圖、
版面檢查和 PDF 看結果，然後把審閱連結交出來，不自動合併。官方也說 agent 可以
做出綁住儲存格的試算表小應用（指標、圖表、控制項）。那些 HTML 是 Blob；文件
寫 Blob 第一版不進 Worktree，也沒有版本歷史。小應用的改動會不會走同一套草稿
審閱，尚未確認。

## 主打賣點

- **共同成品是文件，不是聊天視窗裡的附件。** 表格、文件、簡報放在個人或團隊
  空間裡，人用瀏覽器直接改，agent 用 CLI 或本機對話去改同一批身份。
- **真正不同的是必須審的草稿。** Worktree 把 agent 的中間稿和大家正在看的正本
  分開。Ready 是一條清楚的交件線；合併或退回由人決定。
- **三個入口，一台管權限的伺服器。** 瀏覽器、本機 Agent、CLI 都不自己發明
  門禁。Agent 程式沒有自己的使用者表。
- **五種文件在同一套 runtime。** 試算表、文件、簡報、關聯表、畫布，以及互相
  引用，來自 Univer 引擎。Workspace 把它做成可部署的產品，並加上空間、分享、
  Issue 和審閱。

一起改、留言、匯入匯出 xlsx／docx／pptx，在既有辦公室軟體裡已經常見。這裡
比較特別的是把 agent 草稿做成分支，以及把任務單放進團隊空間、卻還不跟草稿綁
在一起。本機 Agent 是文件旁邊的對話視窗，和同時編排很多程式員工的控制台是
不同層。

## 使用情境

### 請 agent 改季報，自己決定要不要併回

- 適合誰：不想讓 agent 直接改大家正在看的那份表或簡報的人。
- 在什麼情況使用：用 CLI 或 Workspace Agent 指向團隊空間裡的檔案，讓 agent
  在 Worktree 裡改、自己檢查，再交出審閱連結。
- 帶來的價值：中間稿不進正本。人在瀏覽器看草稿或雙欄比較，接受才合併，不對
  就 reopen 或丟掉。

### 用團隊 Issue 把文件工作派出去

- 適合誰：團隊空間裡需要留言、指派、標籤，並把任務指到某些檔案的人。
- 在什麼情況使用：開一張 Issue，寫清楚要改哪幾份 Node，agent 用 CLI 讀討論、
  回覆、改完再標狀態。
- 帶來的價值：任務單和檔案在同一個空間。限制是這一版 Issue 不記住對應的
  Worktree，草稿還是得另外打開審。

### 個人空間分享出去看，草稿仍不外流

- 適合誰：想把一份表或文件用連結給別人看，同時讓 agent 在旁邊改的人。
- 在什麼情況使用：個人空間開連結分享或指定 editor／viewer；agent 的 Worktree
  仍要登入，匿名讀者進不了草稿。
- 帶來的價值：正本的分享範圍和草稿的可見範圍是兩扇門。連結分享只存在個人
  空間；團隊空間靠成員角色和空間是否公開唯讀。

## 我們可以學什麼

- **值得借鑑的 product idea**：人和 agent 的共同物件是一份有身份的文件，放在
  個人櫃或團隊櫃裡。正本和草稿是同一份文件的兩種狀態，不是兩段聊天。任務單
  （Issue）可以指向檔案，但這一版還沒有變成「這張任務就是那份草稿」。權限回答
  「你能不能打開這張紙」；Worktree 的可見性另外回答「你能不能進這間審閱」。
- **值得借鑑的 interaction / workflow**：人說要什麼結果 → agent 在隔離草稿裡
  改 → 用內容和畫面自己檢查 → 標成 Ready，凍結這一版 → 人比較正本和草稿 →
  合併、退回重開，或丟掉。登入時 agent 只交出網址和驗證碼，密碼不進對話。
  管理員對別人的私人草稿可以收回或退回，但看不到裡面的紙。
- **在 2D workspace 裡會變成什麼**：平面樓層上，團隊空間是一區，個人空間是自己
  的隔間。表格、文件、簡報是桌上的紙，不是地板的格子。Agent 坐在側桌改副本，
  桌牌寫著草稿或待審。你走過去，把正本和副本並排，再決定放回主桌或退回。
  Issue 是牆上的任務卡，卡片指著某張紙，卡片本身還不是那份草稿。留言是紙上的
  便利貼，而且目前只貼在正本上；草稿桌上還沒有這些貼紙。誰在線出現在打開的
  那張紙旁邊。這套產品沒有把人畫在樓層上，若要做 2D 辦公室，座位得我們自己補。
- **在 3D workspace 裡會變成什麼**：你走進一間可走的辦公室。團隊空間是一間房，
  個人空間是私人辦公室。你看見誰在場、坐在哪張桌子。表格躺在桌上，是工件，
  房間本身不是一張表。Worktree 是旁邊的審閱桌：agent 在那裡改，標成 Ready 後
  你走過去看雙欄比較，合併才回到主桌的正本。私人草稿是一扇關著的門，門上有
  摘要；管理員可以收回或退回，但進不去讀裡面的紙。協作者 presence 今天只存在
  文件房間裡，進到 3D 辦公室要變成「這個人站在這張桌子前」。
- **不值得照搬或需要重新設計的地方**：不要把試算表格子或文件畫布當成辦公室
  平面圖，也不要因為產品名叫 workspace，就把這套瀏覽器編輯器當成我們要做的
  2D／3D 空間。HTML 小應用留在桌上的成品，不要變成地板。Issue 和 Worktree
  沒有綁在一起，若辦公室裡「任務」和「這一版草稿」必須是同一件事，得自己接。
  Blob 不進草稿，不能假設每種檔案都有審閱分支。本機 Agent 是文件旁邊的對話
  程式，DSH 是它的 runtime；文件伺服器不是員工本人。

## 初步看法

- 最有價值的部分：agent 的中間稿和正本分開，交件線是 Ready，人決定合併。
  分享範圍和草稿可見範圍也分開。這對「桌上的紙」和「側桌的副本」很有用。
- 最大限制或疑問：它是文件表面，沒有空間座位。任務單和草稿是兩套。草稿上看
  不到正本留言，審的時候討論怎麼接上，文件只寫「不讀寫」，沒有說明這是不是
  刻意的產品選擇。登入文件寫了密碼、GitHub、Discord 和部署方自己的 OAuth，
  資料模型有一處只列 GitHub，實際啟用哪些尚未確認。互動品質尚未親測。
- 是否值得進一步研究或親自體驗：值得當「文件成品與審閱分支」的參考。若要
  驗證審閱畫面和權限手感，再在獨立實驗區跑 Browser 與 CLI，不要把 upstream
  放進這個雷達 repo。

## 後續補充（選填）

Not tried yet。官方 README、HTTP 概念、資料模型、架構與 CLI／Agent 說明已足夠
回答 workspace 需要的問題：共同物件是文件、草稿如何交人審、任務單放在哪裡、
權限由誰算。畫面是否真的讓人看得出「正本」和「副本」，尚未確認。

## Sources

- [Univer Workspace repository](https://github.com/dream-num/univer-workspace)
- [Univer Workspace README](https://github.com/dream-num/univer-workspace/blob/main/README.md)
- [HTTP concepts](https://github.com/dream-num/univer-workspace/blob/main/apps/workspace/contracts/http/concepts.md)
- [Data model](https://github.com/dream-num/univer-workspace/blob/main/apps/workspace/docs/data-model.md)
- [Architecture](https://github.com/dream-num/univer-workspace/blob/main/apps/workspace/docs/architecture.md)
- [Application design](https://github.com/dream-num/univer-workspace/blob/main/apps/workspace/docs/application-design.md)
- [Workspace CLI](https://github.com/dream-num/univer-workspace/blob/main/apps/cli/README.md)
- [Workspace Agent](https://github.com/dream-num/univer-workspace/blob/main/apps/agent/README.md)
- [預設服務 space.univer.ai](https://space.univer.ai)
- [品牌站 univer.ai](https://univer.ai)
- [文件引擎 Univer](https://github.com/dream-num/univer)
