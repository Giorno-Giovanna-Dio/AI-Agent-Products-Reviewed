# OpenHands

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openhands-cell-80a8/reviews/openhands.md)
>
> Cell ID：[https://github.com/OpenHands/OpenHands](https://github.com/OpenHands/OpenHands)
>
> Status：`untried`
>
> Category：自架 coding-agent 控制台（ADE）＋軟體工程 agent runtime
>
> Last updated：2026-10-10

## 產品介紹

OpenHands 以前叫 OpenDevin，中間也曾放在 `All-Hands-AI/OpenHands`。這兩個舊網址現在都會轉到這個 repository。你打開的產品叫 **Agent Canvas**：一個自架的開發者控制台。選一個後端、一個 agent 身份，開一場對話，請它讀程式、改檔、跑指令。控制台負責把對話、終端機、瀏覽器、檔案和自動化畫出來。它不自己執行那些動作。

真正跑迴圈的是旁邊的 Agent Server，實作在姊妹 repo [software-agent-sdk](https://github.com/OpenHands/software-agent-sdk)。排程和 webhook 在另一個 [automation](https://github.com/OpenHands/automation)。同一套 SDK 也支撐 OpenHands CLI 與 Cloud。這個 Cell 看的是 Canvas 這扇窗，以及窗口後面的 conversation 和 sandbox。

預設員工是 OpenHands 自己的軟體工程 agent。同一張桌子也能用 Agent Client Protocol（ACP）請別人來坐：文件裡的內建預設是 Claude Code、Codex、Gemini CLI；README 還列出 Pi、OpenCode，以及任何講 ACP 的 agent。員工可以站在你的目錄裡，也可以站進 Docker 或遠端 sandbox。尚未實際安裝或執行。官方網站是 [openhands.dev](https://openhands.dev)。

## 主要 Features

### 一場對話，一個會跑的 runtime

工作單位是 **conversation**。一場對話有自己的訊息、工具呼叫、檔案變更、選好的 agent profile，以及這場才載入的外掛。過程寫成一條只追加的事件紀錄。

Conversation 負責編排：開始、暫停、把狀態留下。Agent 是裡面做事的人。Workspace 是他站的地方。本機路徑會在同一個行程裡跑。接到 Docker 或遠端 sandbox 時，同一支對話 API 改走 HTTP 與 WebSocket，人在 Agent Server 裡做事。換的是房間。

Agent Canvas 可以同時連多台 Agent Server，從畫面切換本機、容器、VM 或 OpenHands Cloud。官方寫明：Canvas 不提供隔離。後端若直接跑在你的機器上，agent 就用你的帳號權限。要有牆，得另外用容器、sandbox 或 VM。

文件裡的沙盒大致兩種擺法，尚未親測：

- 整包控制台（畫面、Agent Server、自動化）放進一個 Docker，只掛你指定的專案目錄。
- 控制台留在主機，**每一場對話**再開自己的容器。容器有自己的金鑰和持久化目錄。閒置超過文件設定的時間會停掉，之後還可以再叫起來。多場對話若掛進同一個主機目錄，檔案仍然共用；文件建議分開目錄或 worktree。

已經在本機跑過的舊對話，不會自動搬進新容器。切到每場一個容器之後，那些舊對話在畫面上會變成可讀的封存，不再開即時連線。

對話變長時，輸入框旁有 context 用量，也可以手動把歷史壓短。壓縮是後勤，不必占一張辦公桌。

### 身份在坐下時就定了

Agent profile 決定這場對話用誰：內建 OpenHands agent，或一個 ACP agent。OpenHands profile 再指向一份 LLM 設定；ACP profile 用對方 CLI 自己的模型和登入。對話開始之後不能換成另一個 profile。要換人，就開新的一場。

具名的 OpenHands profile 可以從工具清單裡拿掉能力。文件寫明：拿掉工具就是拿掉那項能力。只在提示裡叫它不要做，擋不住。同一份 profile 也可以限制看得到哪些 secret、哪些 MCP server。Secret 的執行點在 Agent Server，Canvas 只記住你勾了誰。ACP 時，Agent Server 負責把對方的 CLI 拉成子行程；Canvas 只把對話畫出來。

### 主桌可以等人，也可以另開一桌

SDK 的 Task 工具是同步委派。主 agent 叫子 agent，然後停著等結果。子對話會存下來，之後用 task id 續寫。內建角色裡，code-explorer 只讀、bash-runner 跑指令、web-researcher 上網、general-purpose 做一般任務。專案也可以把專家寫成 Markdown，放在 `.agents/agents/` 或 `.openhands/agents/`。這發生在同一場對話的 runtime 裡：主員工還坐在原桌，旁邊多了一把椅子。

Agent Canvas 可以另開一場仍連回父對話的子對話，做完把結果交回。本地子對話可以選擇獨立 worktree，或繼續用父對話那份 workspace。Cloud 子對話用那次選定的 repo 與 branch。日常流程的文件另說：你可以留在原來的對話，同時讓另一個 agent 在自己的 workspace 裡查，再從對話列表進去看摘要和 diff，然後才決定要不要 push。這份「另一個 agent」是不是每一次都走子對話，尚未確認。共用 workspace 時，兩個人會改到同一批檔案。

### 人怎麼看改動、怎麼把筆拿回來

對話總覽可以打開 **Commits** 抽屜。最近的 commit 和尚未 commit 的變更放在同一張清單。上面的 git 動作（commit、pull、push、開 pull request）會再傳一段提示給 agent，由 agent 去下 git。

不走這條路時，可以對某一則訊息 `Branch from here`。新對話從那個點岔出去，原來的對話留在側欄。從自己的訊息岔出去時，那則話會回到輸入框，讓你改完再送。文件說這要後端支援 conversation fork。

`/goal` 會讓 agent 一直做到某個目標。每一輪由另一個 judge 模型檢查還缺什麼。目標還在跑時，你送一則普通訊息，目標會被打斷，控制權回到你。

動作要不要先問人，掛在這場 conversation 的確認政策上。SDK 寫了三種：全部都問、都不問、只問被安全分析判成高風險的。需要確認時，執行停在等待確認；人可以接受，或拒絕並附理由，讓 agent 換做法。Agent Server 有對應的回應端點。Canvas 畫面上這個暫停長什麼樣子，尚未確認。

中途暫停也在 SDK 與 Agent Server：停在下一步之間，暫停時還能改口，再繼續跑。正在進行的模型呼叫要先結束，暫停才生效。Canvas 只寫到活動標籤會在暫停或做完時消失。畫面上有沒有一顆明確的暫停鈕，尚未確認。

重複卡住的工具呼叫，SDK 有 stuck detection。它讀事件流，不改狀態。這是背景警報，不是另一位員工。

### 排班表，各自開自己的對話

自動化是另一條生命週期。時間到了，或 GitHub、webhook 事件來了，就派一場新對話給 Agent Server。文件裡的 software factory 把分派、開發、審查、合併看守做成四個獨立自動化，各自綁不同的 profile 和 token。審查的身份可以寫 review，文件裡的範例不給它 push 的權限。看守那一格不啟動 agent，只在驗收狀態通過時合併。官方有一段示範宣稱多個 issue 被平行做完並合併。那是官方說法，這裡沒有複驗。

對話列表可以把自動化跑出來的場次隱藏，或只顯示它們。釘選的對話不會被這個篩選藏起來。

## 主打賣點

- **控制室和員工拆開**：Agent Canvas 是 ADE dashboard。員工、事件、workspace、確認，活在 Agent Server。同一場對話可以站在本機目錄，也可以站進 Docker 或遠端 sandbox。
- **一場對話雇用一種身份**：OpenHands 自己的 agent，或 ACP 進來的 Claude Code、Codex、Gemini。工具和 secret 範圍在坐下時發完，這場對話中途不換人。
- **委派看得到誰在等、檔案櫃有沒有分開**：同步子任務會讓主 agent 停下來等。Canvas 的子對話可以選擇獨立 worktree，或共用同一份檔案。共用時，文件自己提醒會互相踩到。
- **人收回控制的點寫在 runtime**：岔出新對話、用普通話打斷目標、暫停，以及高風險動作前停住等你點頭。未提交變更和 commit 放在同一疊紙上。那疊紙上的 git 按鈕，仍是請 agent 動手。

終端機、改檔、瀏覽器、MCP、換模型，在其他 coding agent 裡已經常見。OpenHands 比較特別的是把「看的控制台」和「跑的 conversation／sandbox」拆開，並讓別人的 CLI 坐進同一張對話桌。這和 [Conductor](reviews/conductor.md)、[Orca](reviews/orca.md) 那種雇用很多 CLI 的 ADE 同層，也和 [Sandcastle](reviews/sandcastle.md) 那種用程式編排 worktree 與容器的函式庫互補。Canvas 多了自己的 agent，以及可以切換的後端。它和 [Agent Office](reviews/agent-office.md) 那種走得進去的辦公室是不同層：Canvas 是控制台。

## 使用情境

### 先看計畫，再讓同一個人改

- 適合誰：不想讓 agent 一讀完需求就改檔、push 的人。
- 在什麼情況使用：開一場對話，先請它只檢查、只說明。確認政策或 profile 拿掉寫入工具。同意之後再請它改，並在 Commits 抽屜看尚未提交的變更。
- 帶來的價值：計畫和施工可以留在同一場對話。不滿意就從某一則訊息岔出新對話，原來的那條還在。

### 主桌繼續，隔壁用自己的 worktree

- 適合誰：手上有一件事在做，另一件可以平行查的人。
- 在什麼情況使用：請 Canvas 開子對話，或依日常流程派一個獨立 agent。相關改動放在不同 workspace 或 branch。做完先看摘要和 diff，再決定要不要 push。
- 帶來的價值：另一條 PR 的上下文不必塞進主對話。選了共用 workspace，就是同一櫃檔案兩支筆；選獨立 worktree，才是另一間房。

### 用輪班表處理 issue，人看狀態

- 適合誰：想把分派、實作、審查、合併拆成固定班次的團隊。
- 在什麼情況使用：每個班次是一個自動化，綁自己的 profile 和 token。開發的人能改 code。審查的人依那份 secret 寫 review。看守的班次不叫 agent。
- 帶來的價值：權限在身份上，不靠每一則提示臨時拜託。人從自動化活動和 PR 狀態介入，不必坐在同一條聊天裡扮演四個角色。

## 我們可以學什麼

- **值得借鑑的 product idea**：一場對話是一張桌子，agent 是坐在那裡的人，workspace 是這間房的檔案櫃與門。本機目錄、Docker、遠端 sandbox 是同一張桌子可以搬去的三種樓。控制台只是看這棟樓的窗口。身份在人坐下時發，包含工具和 secret，這場對話中途不換徽章。
- **值得借鑑的 interaction / workflow**：人可以用岔路留下原對話，可以用普通話打斷還在跑的目標。Runtime 可以在下一步之前暫停，也可以在高風險動作前停住等點頭。未提交的紙和已提交的 commit 放在同一疊。git 按鈕若只是再寫一張紙條給員工，辦公室裡還要留人自己能拿筆的位子。委派要讓人看見主桌正在等，還是隔壁已經獨立開工。
- **在 2D workspace 裡會變成什麼**：平面樓層上，一個 workspace 是一間房，一場 conversation 是房裡的一張桌子。桌牌是這場選的 profile：OpenHands，或 ACP 進來的 Claude Code、Codex、Gemini。這張桌子不能中途換人，要換人就開新桌。同步的 Task 子 agent 是拉到主桌旁邊的椅子，主員工停著等。Canvas 的子對話若用獨立 worktree，是走廊上另一間有自己檔案櫃的側房；若共用 workspace，則是同一間房裡的第二張桌子，兩支筆會碰到同一疊紙。桌緣貼著活動：在讀檔、在跑指令，或 Thinking。Commits 是桌緣那疊未提交變更加最近 commit。確認政策是門上的告示：每一步都要點頭、都不問，或只有高風險才停。`/goal` 是釘在牆上的目標，judge 每一輪走過來蓋章；你開口說話，那張紙就放下。自動化是排班表，到點才有人進房。看守班次的桌子沒有寫程式的人，只有檢查章戳的職員。後端切換是這張平面圖在看哪一棟樓。本機直跑沒有牆，平面圖上要畫得出這間房沒有門。
- **在 3D workspace 裡會變成什麼**：你走進一棟走得進去的辦公室。大廳的後端切換決定你進哪一棟：筆電那棟幾乎沒有隔間；Docker 那棟每一場對話是一間小室，閒置太久燈會滅，人還可以再進來。員工坐在對話桌前，胸口徽章是 profile，這場對話裡徽章不會換。他叫同步子 agent 時，旁邊多一把椅子，他停下來等那個人講完。他開子對話且用獨立 worktree 時，走廊多一間房，門牌是 branch；你走進去看 diff，走回走廊，主桌還在。若子對話共用檔案櫃，你應該看見兩個人站在同一個櫃子前。高風險動作是他手停在半空，等你點頭，或拒絕並說原因。暫停是他在目前這一步做完後坐下。Commits 桌在房側，未提交的紙和 commit 疊在一起。ACP 員工帶自己的工具箱，坐在你的小室裡，由 Agent Server 看管那個子行程。審查員和開發員在不同小室，鑰匙不同。看守員不寫 code，章戳齊了才去合併。這是辦公室裡看得到人在哪裡做事。Agent Canvas 的側欄聊天仍然只是控制室的一塊螢幕。
- **不值得照搬或需要重新設計的地方**：聊天、檔案抽屜、Commits、自動化列表是 ADE dashboard。不要把這塊螢幕叫做 2D／3D workspace，也不要只把側欄對話清單搬進樓層。git 動作若永遠變成「請 agent 再做一次」，人就無法直接接手那疊紙。共用 workspace 的子對話若畫成兩間看起來分開的房，會騙到人。Task 工具的同步等待若藏在同一條聊天裡，樓層上看不出主員工正停著。確認政策活在 SDK；Canvas 怎麼把「停下來等你」畫出來尚未確認，不能假設已經有一顆清楚的核准鈕。預設本機安裝沒有隔離，平面圖和立體辦公室都要把「沒有牆」畫出來。profile 中途不能換人，這條限制應該寫在桌牌上。

## 初步看法

- 最有價值的部分：對話、員工、workspace 三層分開。Sandbox 可以按整棟樓關，也可以按每一場對話關。身份上的工具與 secret 範圍，比在提示裡寫「先不要 push」更像門。
- 最大限制或疑問：這個 repo 是控制台，agent runtime 在姊妹 repo。同步 Task、Canvas 子對話、日常流程裡的「另一個 agent」，文件沒有畫成同一種空間單位，尚未親測。Canvas 是否把確認暫停和 pause 做成看得到的介入，也尚未確認。Commits 抽屜裡的 git 動作是提示，不是人直接操作。
- 是否值得進一步研究或親自體驗：值得當「控制室如何對應一間間 sandbox 房」的參考。若要驗證，再在這個 repo 外面看一場會開子對話、並在確認政策下停住的 session。不要把 upstream clone 進這個 radar repo。

## 後續補充（選填）

Not tried yet。官方文件已足夠回答 workspace 需要的問題：誰是員工、對話和 runtime 怎麼拆、sandbox 是整包還是每場對話、人如何岔路與打斷目標、委派時工作目錄是共用還是分開。確認畫面的實際手感，以及子對話在側欄裡是否走得進去，留到親測。

## Sources

- [OpenHands repository（Agent Canvas）](https://github.com/OpenHands/OpenHands)
- [先前網址 OpenDevin/OpenDevin](https://github.com/OpenDevin/OpenDevin)
- [先前網址 All-Hands-AI/OpenHands](https://github.com/All-Hands-AI/OpenHands)
- [OpenHands 網站](https://openhands.dev)
- [Agent Canvas 架構](https://docs.openhands.dev/openhands/usage/agent-canvas/architecture)
- [Conversations](https://docs.openhands.dev/openhands/usage/agent-canvas/conversations)
- [Agent Profiles](https://docs.openhands.dev/openhands/usage/agent-canvas/agent-profiles)
- [ACP Agents](https://docs.openhands.dev/openhands/usage/agent-canvas/acp-agents)
- [每場對話一個 Docker](https://docs.openhands.dev/openhands/usage/agent-canvas/backend-setup/docker-execution)
- [Software Agent SDK](https://github.com/OpenHands/software-agent-sdk)
- [Conversation 架構](https://docs.openhands.dev/sdk/arch/conversation)
- [Security and confirmation](https://docs.openhands.dev/sdk/guides/security)
- [Pause and resume](https://docs.openhands.dev/sdk/guides/convo-pause-and-resume)
- [Task tool set](https://docs.openhands.dev/sdk/guides/task-tool-set)
- [File-based agents](https://docs.openhands.dev/sdk/guides/agent-file-based)
- [Software factory（官方情境）](https://docs.openhands.dev/openhands/usage/use-cases/software-factory)
- [Daily workflow（官方情境）](https://docs.openhands.dev/openhands/usage/use-cases/daily-workflow)
