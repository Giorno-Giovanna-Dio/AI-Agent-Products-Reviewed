# LiveKit Agents

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-livekit-agents-cell-9381/reviews/livekit-agents.md)
>
> Cell ID：[https://github.com/livekit/agents](https://github.com/livekit/agents)
>
> Status：`untried`
>
> Category：Python 即時語音 agent 框架（Room / Session）
>
> Last updated：2026-10-10

## 產品介紹

LiveKit Agents 是開源的 Python 框架，用來把一段程式放進 LiveKit 的即時房間，讓它成為會聽、會說、也能看畫面的參與者。開發者用程式定義這位參與者怎麼接話、何時換人、工具做完要交回什麼；使用者多半在網頁、手機或電話裡跟它說話。官方網站是 [livekit.io](https://livekit.io)，文件在 [docs.livekit.io/agents](https://docs.livekit.io/agents)。同生態的 Node.js SDK 在另一個 repository [agents-js](https://github.com/livekit/agents-js)，這份 Cell 只看 Python repo。本次只讀 GitHub README、LICENSE、repo 內架構說明，以及 2026-10-10 渲染的文件，沒有安裝。

房間是媒體空間：人、agent、電話都是 participant，用音訊、視訊和資料軌彼此連線。Agent 程式先向 LiveKit server（Cloud 或自架）登記成 agent server，等派工（dispatch）才開一個 job 進程進房。官方把這層和商業的 LiveKit Cloud（部署、Inference、觀測）分開。Cloud 的 Agent insights 與瀏覽器裡的 Agent Builder 是儀表與原型台。這份筆記要學的是人與 agent 在同一間房裡怎麼工作。

## 主要 Features

### 派工、Job 與房間裡的座位

文件建議多數應用用明確派工：`agent_name` 是路由用的名字，畫面上的顯示名稱是另一件事（`Participant.name`）。派工可以帶一段 job metadata（字串，上限 512 KiB），例如使用者是誰。沒設 `agent_name` 時，每個新房間都會自動派一位 agent。文件寫這種自動派工不適合多數應用，因為不帶 metadata，也不管這個房間需不需要人。

每個 job 跑在自己的進程。官方說其中一個當掉不會拖垮同一台 server 上的其他 job；agent 若意外斷線，server 大約 15 秒內會再派一位進同一間房。預設在最後一位非 agent 參與者離開時關房。CLI 有三種跑法：`console` 用本機麥克風、不必連 server；`dev` 熱更新並連上 LiveKit；`start` 給正式環境。這些是文件與 README 的說法，尚未實測。

`AgentSession` 負責這一段對話：收輸入、走語音管線、叫模型、把聲音送回去，並發出可觀測的事件。管線可以是 STT、LLM、TTS 的組合，也可以換成 realtime 模型。`RoomIO` 預設把房間裡的音軌接到 session。文件寫 agent 預設只聽、只回「第一個進房的人」（linked participant），之後可用 `set_participant` 換人。同一間房要同時應付很多人時，要另外設定，預設是對著一個人說話。

### Agent、Task、工具

文件把長期控制、短工作、副作用分成三種東西。

**Agent** 握住這場 session。它帶 instructions、工具，以及自己的推理方式。同一時間有一位 active agent。

**工具** 是模型決定要不要呼叫的函式。它可以查外部系統、用 RPC 請前端做事，或直接交回另一個 Agent 來換手。連續工具呼叫有上限，文件寫 `max_tool_steps` 預設 3；達到上限後會再做一次不准用工具的回覆，把目前結果說出來。慢的工具可以改成非同步：`ctx.update()` 把進度寫進對話，模型用自己的話講給使用者聽，通話繼續；`ctx.with_filler()` 是不進對話紀錄的填充語。預設非同步工具會做完。取消要另外打開，因為訂單、寫入、付款不適合做到一半被打斷。重複呼叫可以設成允許、拒絕、取代，或先請模型確認。

**Task**（`AgentTask`）是短命的。它暫時拿走 session 的控制權，做到 `complete(結果)` 就把一個有型別的結果交回去，自己不留下。文件寫只能在三個地方等待它：agent 上場時（`on_enter`）、下場時（`on_exit`），或某個工具的函式本體裡。Supervisor 模式用的就是這個：一位 agent 一直當主人，需要蒐集地址或同意時才把話筒暫時交給 task，結果回來後主人繼續講。主人留在這場 session 裡。

**TaskGroup** 把多個 task 排成順序，群組內共用同一份對話。做完可把過程摘要交回主人（`summarize_chat_ctx` 預設開）。使用者要改前面的答案時，文件說模型可以退回先前的步驟；每個步驟的 description 是給模型看的，用來判斷該退到哪一格。文件把 TaskGroup 標成實驗中，API 可能會改。退回的實作是用內部例外把任務堆疊重排。這是文件對機制的描述，尚未實測。

### 換手、脈絡與誰有哪些工具

Handoff 把整場的控制權交給下一位 agent。可以從工具回傳新的 agent，讓模型決定何時換；也可以用 `update_agent` 由程式換。換手後，原 agent 不再參與，除非之後再交回來。換手會在對話裡留下 `AgentHandoff`，記下舊的與新的 agent id。

脈絡預設留在原位。新的 agent 或 task 只看得到自己的 instructions。要帶對話，得明確傳 `chat_ctx`。`copy(exclude_instructions=True)` 只帶輪次，不帶上一棒的系統提示。對話太長時，文件建議另叫一次模型做成摘要、只留最後幾輪，或把關鍵事實放進 session 的 `userdata`。`session.history` 仍保存整場紀錄。那是 session 的全本，新上台的人要另傳 `chat_ctx` 才會放進自己的提示。這段是文件寫的規則。

權限在這套框架裡主要是「這位 agent 帶哪些工具」。文件舉的例子是：收款的 agent 有付款 API，一般詢問的 agent 沒有。Toolset 可以把一組工具一起加上或拿掉。工具很多時，Python 有測試中的動態發現（先搜尋再載入定義）；該頁把 Node.js 標成尚未提供。MCP 的內建 Toolset，工具總覽寫的是 Python only。換座位時帶走的是工具清單。文件沒有另給一份作業系統沙箱，也沒有 coding agent 那種逐道指令先問人的門。

慢推理可以留在同一場通話裡。Subagent delegation 讓前台用低延遲模型繼續說話，背景另開一個 context 給較慢的模型。結果晚一輪才進對話。文件提醒：換手會丟掉還在跑的更新，除非把工具放進 `AsyncToolset`，結果才會交給當時在台上的人。背景工作預設會做完；話題已經換掉時，要另外標成可取消。

每位 agent 還可以蓋掉 session 的模型與輪次設定。例如收帳號的人可以容許較長的停頓。換到下一位沒設的人，就回到 session 預設。

### 輪次、人進場，以及看得到的進度

語音的進度首先是誰在說話。文件寫預設使用 LiveKit 的 turn detector：在語音活動偵測之上，用語句的意思和聲音判斷這輪說完了沒有。使用者在 agent 說話時插話，另有 adaptive interruption，用來分辨真的要搶話，還是只是附和。也有純 VAD、交給 realtime 模型、或手動（例如按住才說）等模式。輪次模型的授權和框架的 Apache-2.0 分開：`MODEL_LICENSE` 寫這些模型只能搭配 LiveKit Agents 使用。

人的介入，文件裡最具體的是暖轉接 `WarmTransferTask`。它會另開一間房給人類客服、用 SIP 撥過去、對來電者放等待音樂、把對話紀錄交給這位人類，並暫時關掉來電者的進出。人類手上有接通、拒絕（可附理由）、以及偵測到語音信箱等工具。任務結束時交回這位人類的 identity。文件寫 Node.js 1.5 起放在穩定的 workflows；Python 仍在 `beta.workflows`。另一條介入是把工具轉給前端 RPC：資料只在瀏覽器裡（例如定位），或要前端改畫面時，由客戶端把結果填回來。長工具中途若要問人，用 `ctx.foreground()` 先把話筒交回使用者，問完再繼續。

測試在框架內：`session.run(user_input=...)` 可以斷言下一個事件是某次工具呼叫，或請另一個 LLM 當評審看回覆意圖。文件另有 Agent Simulations 跑整段對話。正式環境的 Agent insights 在 LiveKit Cloud：逐輪逐字稿、工具與換手、trace、log、錄音排在同一條時間軸上。文件寫這套觀測只對 Cloud 專案可用；完全自架的媒體伺服器沒有這條。這是儀表。

Agent Builder 是 Cloud 專案裡的瀏覽器原型台：寫一段 instructions、選模型、設 HTTP／客戶端 RPC／MCP 動作，或以欄位蒐集結構化答案，然後部署。Tasks 頁寫 Data Collection 會編成 `AgentTask` 與 `TaskGroup`；Builder 的限制頁則寫 builder 目前不支援 workflows、handoffs、tasks、視覺、realtime 模型與測試。兩頁都是官方文件。builder 畫面實際會產出什麼，尚未確認。

## 主打賣點

- **它加上的是「房間裡的即時參與者」，以及誰握有這一輪。** Agent、Task、換手、輪次偵測，是這套框架讓人記住的核心。派工決定誰進哪一間房；session 決定這一刻誰在說話。
- **和 CrewAI 相同的興趣** 是多角色、交接、人在關卡點介入。CrewAI 的 crew 是同一次 Python 執行裡的角色接力，產出多半是文件。LiveKit 的交接發生在一通還開著的通話裡：換的是話筒與工具。
- **主人留在場上，和主人離席，是兩種流程。** Supervisor 把短工作交給 task，型別結果交回原位。Handoff 是控制權整段移交，脈絡預設留在上一棒。背景推理則讓前台繼續講，結果晚點送達。
- 可替換的 STT／LLM／TTS、電話、MCP、大量 plugin，是這組房間模型的周邊。Agent Builder 與 Cloud insights 是部署與事後查看的儀表。

## 使用情境

### 電話先進線，必要時把人接進來

- 適合誰：要做進線或外撥客服的開發者。
- 在什麼情況使用：agent 先聽完問題；要轉人工時走暖轉接，人類先在側房聽到摘要，再決定接通或拒絕。
- 帶來的價值：人進場前已經有對話紀錄，來電者在等待時聽到的是等待音樂。實際通話品質尚未實測。

### 要一格一格問完，而且允許改前面的答案

- 適合誰：掛號、訂位、同意錄音這類要收回結構化結果的流程。
- 在什麼情況使用：主人 agent 留在 session；同意、地址、付款拆成 task。順序固定且常常要倒退時，用 TaskGroup。付款若要另一組工具與指示，再 handoff 給收款 agent。
- 帶來的價值：每一格交回有型別的結果，主人可以先檢查再往下說。TaskGroup 仍標實驗，倒退是否順手尚未確認。

### 前台繼續說話，難的推理放在後面

- 適合誰：語音助理不能空幾秒，但有些問題需要較慢的模型。
- 在什麼情況使用：前台用低延遲模型接話，分析類問題用非同步工具交給背景模型；定價或必須核對的答案則改成阻塞，等結果再講。
- 帶來的價值：使用者聽到的是持續的對話。文件要求前台在結果回來前先不要猜答案，否則兩段話會打架。這是官方的設計建議，尚未實測。

## 我們可以學什麼

- 值得借鑑的 product idea：工作的單位是一間房和一場 job，員工是被派進去的參與者。長期在場的是 Agent，暫時借用話筒的是 Task，晚點把結論送回來的是背景推理。誰握有這一輪，是和「任務做到哪」分開的狀態。`userdata` 是換人後仍留在桌上的事實；`session.history` 是全本紀錄，預設要另外交出才會變成下一棒的提示。
- 值得借鑑的 interaction / workflow：派工是把具名員工送進指定房間，並附上一張 briefing（metadata）。輪次偵測是誰可以開口的規則；附和不等於搶話。Task 是借走桌子直到 `complete`。TaskGroup 的退回是走回前面某一格，description 告訴模型該回哪裡，做完把摘要交回主人。Handoff 預設桌面只留新來者自己的指示；要帶筆記得顯式交出 `chat_ctx`。非同步的 `ctx.update` 是邊做邊說；`foreground` 是做一半必須先問人。暖轉接是來電者留在原房聽等待音樂，人在側房看過紀錄後才走進來或拒絕。可取消是選擇加入的，因為有些工作不能做到一半停。
- 在 2D workspace 裡會變成什麼：一張俯視樓層圖。每一間 LiveKit room 是地圖上的會議室，座位只有被派工時才有人。正在說話的人有發言標記；使用者插話是站起來，附和只是點頭，發言權留在原位。Supervisor 留在主桌。Task 是主桌旁邊的側桌：專員過去問完，把填好的表格帶回，主桌這段時間在等。TaskGroup 是一排表格，退回是走回前面那張，桌上筆記仍在，結束時收成一頁摘要放回主桌。Handoff 是離座換人；沒帶 `chat_ctx` 時，新來的人桌上只有自己的指示，全本紀錄在房間的錄音裡，要另外攤開。背景推理在後間進行，前台繼續對訪客說話，結論晚一點送進這間會議室。暖轉接時，訪客留在原位聽等待音樂，人在隔壁簡報室看摘要，再決定走進來或搖頭。Agent insights 的時間軸和 Agent Builder 是牆上的螢幕與牆外的製圖桌。它們是儀表，地板本身仍是這場通話。
- 在 3D workspace 裡會變成什麼：一間走得進去的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。進房就看得到誰在場：訪客、正在台上的 agent、也許還有一位電話參與者。Active agent 坐在面對訪客的位子。Task 執行時，專員暫時坐下問問題，supervisor 仍在房裡，等表格送回才再開口。Handoff 是前一位起身離開、下一位坐下；沒交出筆記時，新的人看不到先前的對話紙。`userdata` 是桌上兩人都能翻的資料夾。背景推理在側房，門開著，前台繼續說話；結果走回來才被說出來。若在結果回來前就換手，而且工具沒放進 `AsyncToolset`，文件寫更新會被丟掉：側房的人做完，台上已經換成另一位。暖轉接是走到簡報室，人類先聽摘要，再加入原通話或拒絕。重點是看見誰在場、誰握有這一輪、誰在等。
- 需要重新設計的地方：LiveKit 的房間跟著這通通話活著，人走了 job 就結束。空間辦公室若要有下班後仍在的座位，要另外標「這張椅子只在這通通話裡存在」。權限是工具清單，進到辦公室要畫成不同座位上的鑰匙。換手預設下一棒只看自己的指示，空間裡要看得見筆記有沒有帶過去。TaskGroup 文件標實驗，退回的地板規則先別做死。內建的逐字稿時間軸只在 LiveKit Cloud；自架辦公室要自己做觀測，而且那條時間軸是儀表，地板另做。Builder 限制頁寫它不支援 handoffs 與 tasks，辦公室的編排需要比一段 prompt 表單更多的座位與交接。語音裡的安靜像斷線，coding agent 埋頭在終端機裡則是正常忙碌；兩種在場要分成兩種狀態。Python 的暖轉接仍在 beta 命名空間，Node.js 文件寫已穩定，兩邊是否同一套行為尚未確認。輪次模型的授權綁這個框架，可借的是「說完了沒有」這個狀態。

## 初步看法

- 最有價值的部分：房間、輪次、暫時借用的 task，以及人從側房走進來。這讓「誰在場、誰在說話」成為工作狀態。
- 最大限制或疑問：它是給開發者呼叫的語音框架。換手後提示裡到底剩什麼、TaskGroup 退回是否好用、暖轉接的手感，都要跑過才知道。Cloud 觀測與自架也是兩套畫面。
- 是否值得進一步研究或親自體驗：值得，尤其是發言權、側桌交回表格、以及暖轉接在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://livekit.io
- Repository：https://github.com/livekit/agents （本次讀到的 `main` 上 `version.py` 為 1.8.6；commit `294de277c49ffa879ff7194a08f87c1a96a87a99`，2026-10-09。未安裝、未執行）
- Node.js SDK（另一個 repo，非本 Cell）：https://github.com/livekit/agents-js
- Documentation：https://docs.livekit.io/agents （本次頁面渲染時間 2026-10-10）
- License：repo `LICENSE` 為 Apache-2.0（NOTICE：Copyright 2023 LiveKit, Inc.）。輪次模型另見 `MODEL_LICENSE`，僅能搭配 LiveKit Agents 使用
- README（`main`）：即時參與者、派工、換手示例、`console`／`dev`／`start`
- 已讀概念頁：Introduction、Workflows、Agents & handoffs、Tasks & task groups、Supervisor、Subagent delegation、Sessions、Turns、Tools、Async tools、Toolsets、Forwarding、Agent dispatch、Server lifecycle、Job lifecycle、WarmTransferTask、Agent Builder、Observability、Agent insights
