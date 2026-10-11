# Open Office

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openoffice-cell-c2d2/reviews/openoffice.md)
>
> Cell ID：[https://github.com/longyangxi/OpenOffice](https://github.com/longyangxi/OpenOffice)
>
> Status：`untried`
>
> Category：2D pixel team workspace
>
> Last updated：2026-10-09

## 產品介紹

Open Office 是 [longyangxi/OpenOffice](https://github.com/longyangxi/OpenOffice) 裡的產品，畫面上的名字是 Open Office。它是一間俯視的像素辦公室：多個 AI agent 以同一個團隊的身分，在同一層地板上把一件作品做完。人僱用有名字的成員，隊長先交出計畫，人批准之後開發者寫程式、審查者給通過或退回，最後試著打開一個可看的預覽。角色會走路、坐下打字、頭上冒泡泡。旁邊有團隊對話，也可以切成整頁終端機視窗。

這和 Apache OpenOffice 無關。實作是 TypeScript：Next.js 加 PixiJS 畫地板，Node gateway 負責事件和編排，桌面殼用 Tauri，授權是 MIT。文件寫的快速開始是 `npx bit-office`（gateway 套件名也是 bit-office）。README 裡的 clone 網址 `open-office.git` 目前不存在，實際來源以這個 GitHub repo 為準。尚未實際啟動。

## 主要 Features

### 同一層地板上的座位

地板是可編輯的 tile：桌子、椅子、沙發、白板。新來的人先閒逛。狀態變成工作中或等待批准時，會找一張空椅子坐下；團隊成員做完仍留在自己的位子，單獨僱用的人則把位子讓出來，閒下來可以去沙發。頭上的泡泡對應等人許可、工作中、做完或出錯。團隊對話和日誌會變成短短的說話泡泡。點角色會選到他。

場景透過 `SceneAdapter` 接上同一份狀態。換渲染方式不必重寫編排。像素辦公室會跟著真實狀態動，所以它比一張靜態背景多。決定下一步的仍是階段、派工和 worktree，走路本身不把檔案交出去。控制台模式會卸掉像素場景，改成整頁終端機對話；那個畫面是對話面板，工作空間是這張地板。

### 四段交付：構想、設計、執行、完成

隊長先把需求收成計畫。人可以退回修改，或批准後進入執行。執行時隊長把工作派給開發者，審查者回 `PASS` 或 `FAIL`。第一次失敗會帶著審查意見直接退回開發者再審，省掉隊長來回；再失敗才回到隊長決定換做法、改派人，或接受現況。回合數有上限（文件預設審查最多 3 輪、直接修復 1 次、隊長呼叫有預算）。完成後交出預覽和摘要。人再給意見會回到執行，或結束專案。

預設班底有名字：隊長 Marcus、審查員 Sophie，以及可僱用的開發向成員（例如前端、後端、原型、設計、產品）。官方首頁寫 23 個角色；原始碼的僱用清單是這 6 位加上自動加入的隊長和審查員，另外 `packages/orchestrator/agents` 有 18 份角色說明，啟動時同步到 Claude Code 的 agents 目錄。23 這個數字對不上這兩份清單，尚未確認它指哪一份。

### 每人一塊 git worktree

同一個 repo 上，每個 agent 有自己的 worktree 和本機分支，目錄在 `~/.open-office`（開發模式是 `~/.open-office-dev`）底下，不放進專案裡，避免 agent 往上走到主 repo。做完預設自動 commit 再 squash 合併回主線，也可以改成等人按合併。合併後可以撤銷；合併前可以退掉該 agent 最後一次 commit。文件寫同步衝突時主線優先、合併進主線時 agent 的改動優先。分支不推到遠端。這些行為來自原始碼與編排器說明，尚未實機確認。

### 預覽、評分、分層記憶

完成時會依序試著得到可看的結果：agent 指定的預覽指令、靜態 HTML、輸出裡的網址，或 `dist` 一類的建置產物。人可以給專案 1 到 5 分的評分。記憶分成當下對話、這次任務的結構化摘要、單一 agent 的長期事實，以及跨 agent 的專案知識；編排器另外記住重複出現的審查問題和技術偏好，下次寫進開發者的提示。設計文件裡的目錄和 gateway 用的 `~/.open-office` 不完全同一套寫法，現行到底寫進哪裡、兩套是否都已接上，尚未確認。

### 人從辦公室、分享連結或 Telegram 介入

敏感路徑和危險指令（例如刪除、安裝套件）會先問人。批准計畫、取消任務、結束專案、合併或撤銷，都是明確的指令。Telegram 可對已僱用的成員下指令、看狀態、取消任務；有設定對外通道時，完成訊息可以帶預覽連結。分享連結能以擁有者、協作者或旁觀者身分進同一間辦公室；旁觀者實際能改什麼，尚未確認。本機已在跑的 `claude`、`codex` 等行程會被掃進地板，標籤是行程編號，它們不走這套派工，只有輸出流。

Gateway 偵測已安裝的 CLI。文件把 Claude Code 和 Codex 標成在他們的流程裡用過，Gemini、Copilot、Cursor、Aider、OpenCode、Pi、Sapling 標成實驗或尚未端到端驗證。我們沒有跑任何一個後端。

## 主打賣點

- 它想被記住的是：你看見一整個團隊在同一間辦公室裡，把計畫、實作、審查和預覽走完。
- 和 [Agent Office](https://github.com/AgentSystemLabs/agent-office) 的差別在空間和實作，兩者是不同產品。Agent Office 是走得進去的 3D：一層樓對一個 GitHub repo，桌上筆電是即時終端機，你走過去看誰卡住。Open Office 是俯視的 2D 像素地板：位子表示這個人正在這條團隊流程裡做事，交接靠階段機、審查退回和每人一個 worktree。
- 像素層會反映狀態（坐下、閒逛、許可泡泡、說話），所以它比「聊天窗外貼一張辦公室圖」多一層。派工、退回、合併和預覽才是工作怎麼交出去。整頁終端機視窗把地板拿掉之後，就回到對話控制。
- 多個 CLI、桌面殼、Telegram 和費用統計，是同一套團隊流程的不同入口。首頁的「8 個後端、23 個角色」和原始碼清單不一致，尚未確認。

## 使用情境

### 一個人把一個小作品交給固定班底

- 適合誰：想一次看到計畫、實作、審查和可打開結果的個人開發者。
- 在什麼情況使用：用一句話描述要做的東西，批准計畫後看成員坐下、說話、交出預覽。
- 帶來的價值：交接有固定節奏，失敗有次數上限，完成的定義包含看得到的結果。

### 同時看見受僱團隊和本機已經在跑的 agent

- 適合誰：機器上已經開著 Claude、Codex 或其他被掃描的 CLI 的人。
- 在什麼情況使用：外部行程出現在同一層地板，受管理的團隊坐在自己的位子。
- 帶來的價值：兩種人出現在同一個地方。外部行程不進派工，只有輸出；會不會和團隊改到同一份檔案，尚未確認。

### 人離開座位仍要批准或看結果

- 適合誰：需要遠端點頭、取消，或把辦公室分享給另一個人的使用者。
- 在什麼情況使用：用 Telegram 看狀態和預覽連結，或用分享連結以不同身分加入。
- 帶來的價值：批准計畫、危險操作和評分仍由人做。旁觀者權限尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：團隊成員有固定位子，單獨的人做完就讓出位子。工作、等人、做完用走路和泡泡表示。完成要能打開預覽，而不只是一段摘要。
- 值得借鑑的 interaction / workflow：審查第一次失敗直接退回開發者，第二次才回到隊長，並且有回合上限。每人一個穩定的 worktree，讓同一個工作目錄可以續接；合併、退回、撤銷合併是人按得到的動作。記憶分成當下、這次任務、這個人、全隊，另加重複審查問題，避免把整段聊天塞回提示。
- 在 2D workspace 裡會變成什麼：Open Office 本身就是平面工作空間，一張俯視的像素地板。放進 2D workspace 時，它保持這一層：隊長的計畫在白板前等人改，開發者坐在自己的 worktree 位子打字，審查者頭上是通過或退回，預覽開在房間裡的螢幕。閒置的人去沙發。分享進來的人站在同一層，看得到誰在說話。費用、設定和整頁對話留在地板旁邊的面板。
- 在 3D workspace 裡會變成什麼：同一套團隊放進走得進去的辦公室時，你走到會議桌才能批准 Marcus 的計畫。開發者各自一張桌子，螢幕上是自己那條分支的改動。Sophie 退回時走到開發者桌邊，第二次失敗才走回隊長。預覽是房間裡可打開的成品，許可泡泡是你要走過去處理的信號。這和 Agent Office 仍是兩種做法：Agent Office 以樓層對 repo、以桌上即時終端機為主；Open Office 的 3D 形狀仍是這條階段流程和每人一個 worktree，空間用來顯示團隊此刻在哪一階。
- 不值得照搬或需要重新設計的地方：自動合併預設開啟，衝突時自動選邊。研究用的 workspace 應先讓人看見 diff。只搬角色走路、不搬階段和預覽，就會變成有辦公室皮的聊天。文件裡的套件名、clone 網址和角色數量彼此不一致。safe 模式實際擋哪些指令、旁觀者能做什麼，都還沒確認，不能當成已隔離的沙箱。

## 初步看法

- 最有價值的部分：可見的團隊座位，加上有上限的審查退回、每人一個 worktree，以及完成時要交出預覽。
- 最大限制或疑問：像素地板和整頁對話誰是主路徑；首頁的角色數量、後端數量和原始碼清單不一致；記憶目錄是否兩套並行。尚未確認。
- 是否值得進一步研究或親自體驗：值得，因為它已經是 2D 團隊工作空間，又能對照 Agent Office 的 3D。Not tried yet。若要驗證座位動畫和派工是不是同一條事件，應在本 repo 以外的目錄跑，不要把 upstream 放進來。

## Sources

- Repository：[https://github.com/longyangxi/OpenOffice](https://github.com/longyangxi/OpenOffice)
- License：repository `LICENSE`（MIT）
- Team workflow：[team-workflow.md](https://github.com/longyangxi/OpenOffice/blob/master/team-workflow.md)
- Orchestrator：[packages/orchestrator/README.md](https://github.com/longyangxi/OpenOffice/blob/master/packages/orchestrator/README.md)
- Memory：[packages/memory/README.md](https://github.com/longyangxi/OpenOffice/blob/master/packages/memory/README.md)
- Scene adapter：[apps/web/src/components/office/scene/README.md](https://github.com/longyangxi/OpenOffice/blob/master/apps/web/src/components/office/scene/README.md)
- 對照用的另一個產品（未合併）：[Agent Office](https://github.com/AgentSystemLabs/agent-office)
