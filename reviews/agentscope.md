# AgentScope

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentscope-cell-fb52/reviews/agentscope.md)
>
> Cell ID：[https://github.com/agentscope-ai/agentscope](https://github.com/agentscope-ai/agentscope)
>
> Status：`untried`
>
> Category：Python multi-agent framework
>
> Last updated：2026-10-10

## 產品介紹

AgentScope 是開源的 Python 框架，用來組出會推理、會呼叫工具的 agent，並讓多個 agent 把工作交來交去。它服務的是寫程式的人：你在程式裡組好一個 `Agent`，用終端機對話，或用 repo 裡附的 Agent Service（FastAPI）加上 `examples/web_ui` 跑成多工作階段的應用。它是框架，不是一間可以坐進去的辦公室。

這次看的是 framework repo 的 2.0。GitHub 最新發行是 v2.0.9（2026-09-28），文件以該版為準；`main` 的 README 還有 2026-10 的實驗功能，所以發行版與 `main` 可能已有差距。尚未安裝、尚未執行。官方站 [agentscope.io](https://agentscope.io/) 把整個組織說成一疊專案（框架、記憶、評測、應用）。這個 Cell 只談 `agentscope`。旁邊的 [agentscope-runtime](https://github.com/agentscope-ai/agentscope-runtime) 是另一個 repo，負責部署與沙箱這類執行環境；這裡只把它當成兄弟專案，不把它的網址當成 Cell ID。

## 主要 Features

### 訊息與事件是同一件事的兩面

一個 agent 用 `reply_stream` 把過程吐出來：推理、工具呼叫、文字、圖片或聲音。這些事件可以再組回一則 `Msg`，所以最後留下的訊息，理論上能從事件流還原。多個 agent 擠在同一條流裡時，`reply_id` 用來分出「這一段是誰」。前端不必另做一套紀錄格式。這讓「誰正在做事、做到哪」可以從同一條流讀出來。實際還原是否總是完整，尚未實測。

### 兩種管線：審到通過，或主管派工

2.0 的 `pipeline` 模組目前匯出兩種組合，文件都標成實驗中，介面之後可能改。

**GoalPipeline** 是兩個人的迴圈。執行者做出結果，審核者對照目標判定通過、失敗或做不到。失敗時，理由退回執行者再做，直到通過或次數用完。目標跟著第一則任務進來，不是在建構時寫死。兩人可以共用一個 workspace，審核者能打開檔案、跑指令，而不只聽執行者的口頭報告。

**TeamPipeline** 是一位主管加數名成員。主管用工具 `TeamAssign` 派工，參數是成員名字和一份完整任務。成員看不到主管的上下文，所以任務單必須自己寫齊。成員彼此不直接說話，只把最後回覆交回主管；中間的推理和工具呼叫不會灌進主管的上下文。同一輪裡，不同成員可以同時做；派給同一人的多張單則照順序做。預設在主管這次回覆結束後清空成員上下文。

1.x 文件裡的 **MsgHub** 是另一種做法：同一群人裡有人回覆，其他人自動收到（`observe`），像一間會自動傳話的會議室，再搭配順序或扇出管線。2.0 的 `pipeline` 匯出與 `main` 目錄裡沒有 MsgHub。現行框架的交接，是審核迴圈和主管派工，不是那間自動廣播的會議室。

### 標準作業流程把里程碑排死

SOP 用在「步驟數量和負責人事先就知道」的工作。每個步驟有執行者，也可以有審核者；審核不過就退回同一步，次數用完整次執行失敗。步驟之間只交一份 handover，檔案和對話不會自動跟過去。執行狀態（做到哪一步、交接內容、判定）可以存成資料，之後換行程再接。有人在等工具確認時，該步進入等待，事件流先結束，答案到了再繼續。文件同樣標成實驗中。

和 GoalPipeline 相比，SOP 是一串各有預算的關卡，而不是同一個目標反覆打磨。和 TeamPipeline 相比，誰做哪一步是寫死的，不是主管臨時決定。

### 工具先過權限，人可以讓它停住

每次工具呼叫先走權限：允許、拒絕，或詢問人。模式決定沒人答的時候怎麼辦，例如探索模式偏向只讀、略過詢問的模式會直接做、無人值守時把詢問改成拒絕。人點頭時，還可以收下建議規則，之後同類呼叫不必再問。

人的介入走同一條事件流。需要確認時，agent 發出要求並停住；外面把確認結果送回去，用 `reply_id` 送到正等著的那一位。另一種停頓是外部執行：工具本體在 agent 外面做，做完再把結果送回。內建的 `AskUser` 就是這種工具，用來問人選擇題，並預期畫面留一個自由文字的「其他」。管線和 SOP 都沿用這套：停住的是某一位參與者，主管或下一步會等他做完。

### 服務層的團隊用信箱，而不是嵌在同一次呼叫裡

Agent Service 上的 **Agent Team** 和上面的 TeamPipeline 不是同一層。使用者對到的是主管的 session。主管用內建工具組隊：建立團隊、生出工人、邀請已有的 agent、傳話、解散。工人是另一個 session，有自己的狀態和事件流，可以同時跑，而不是主管行程裡的巢狀呼叫。傳話走 message bus：寫進對方信箱，再叫醒對方，下一輪推理前才讀到。`TeamSay` 可以指定一個人，也可以廣播。文件說分散式部署時這條 bus 可以是 Redis；範例預設是記憶體還是 Redis，尚未實測。

工人預設沿用主管的模型與權限。若要「只讀的偵察」和「可以改檔的人」分開，要另外註冊子 agent 範本，在建立時選定。

### 記憶放在工作區，監看畫面是另一件事

2.0.9 文件把長期記憶做成 middleware。原生的 Agentic Memory 是工作目錄裡的 Markdown：一篇一篇主題檔，加上一份短索引 `MEMORY.md` 注入系統提示；agent 自己用讀寫工具維護，也可以由另一個非同步檢索在後續推理前塞進提示。另外接 ReMe 與 Mem0。同一套文件裡，2.0 相對 1.0 的差異說明曾寫記憶模組棄用、RAG 與長期記憶仍在遷移。兩份說法哪一份對上 v2.0.9 的實際行為，尚未跑過程式，先記成尚未確認。

Workspace 是工具與程式實際跑的地方，可以是本機目錄、Docker、E2B 等，介面給 agent 看是同一套。這是執行隔離，不是平面或立體的辦公室。

**AgentScope Studio**（另一個 repo `agentscope-studio`）曾是本機的視覺化工具：專案與執行、對話、OpenTelemetry 追蹤、評測。該 repo 的說明寫明 2.0 已自帶 Web UI，Studio 不再維護、即將封存。2.0 的 Web UI 看的是推理、工具、權限確認、團隊成員與各人的事件流。兩者都是開發者監看畫面。

## 主打賣點

- **它想被記住的是：過程看得見，工具有邊界，交接方式由你選定。** 事件流讓一次回覆可以逐段看；權限和 workspace 決定工具能不能動、在哪裡動。多 agent 則收成少數幾種交接，而不是一長串自訂編排語法。
- **和 CrewAI 對一下就清楚差在哪。** CrewAI 把合作寫成角色、任務和 process（順序或階層），任務上可以掛上游 context。AgentScope 2.0 的 TeamPipeline 不先發任務清單：主管每次寫一張完整工作單，因為成員讀不到主管的上下文，而且成員之間不互相談。GoalPipeline／SOP 則把「做到有人點頭」寫死在流程裡。CrewAI 的階層是經理分派；AgentScope 的主管派工是一次工具呼叫，結果以工具回傳的形式回來。
- **1.x 的 MsgHub 群聊，是舊的傳話方式。** 現行 `main` 用管線、SOP，以及服務層的信箱。把它講成「現在仍是一間自動廣播的會議室」，會和 2.0 的程式對不上。
- Web UI、已停更的 Studio、多租戶、頻道（飛書、Discord 等）、MCP 與 skill hub，是把同一套 agent 裝進應用與除錯畫面。框架核心仍是 agent、訊息、工具、權限，以及工作怎麼交到下一個人。

## 使用情境

### 一個人在終端機裡做，危險動作先問

- 適合誰：想先確認一個 agent 會不會亂改檔的開發者。
- 在什麼情況使用：終端機啟動一個掛了讀寫與 shell 的 agent，權限停在會詢問的模式。
- 帶來的價值：工具呼叫變成一次停頓；人允許、拒絕，或收下規則。畫面是文字對話，還沒有辦公室。

### 主管派研究員和工程師，檔案放同一個櫃子

- 適合誰：要拆開「查資料」和「改程式」，又不想讓兩人互相看完整推理的人。
- 在什麼情況使用：TeamPipeline，兩名成員共用一個 workspace；主管只收到最後回覆。
- 帶來的價值：交接是一張寫完整的任務單加一份結論。若工程師卡在工具確認，人的答案會依 `reply_id` 回到他，主管等這張派工結束再繼續。

### 文章或交付要過固定關卡，中途可以存檔

- 適合誰：步驟順序事先知道、每步都要驗收的人。
- 在什麼情況使用：SOP 把大綱和正文分成兩關，審核者不過就退回同一步；等待人時把執行狀態存下來。
- 帶來的價值：進度是「停在哪一關」，下一關只拿到交接單。隔天可以從存下的狀態接，而不是重跑整段對話。agent 自己的上下文要另外存，文件寫明 SOP 狀態不含這一部分。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是一個有名字的 agent。工作怎麼移動，有三種事先講好的形狀：審到通過、主管派工、固定關卡。人的介入是一張帶 `reply_id` 的回條，送到正停住的那一位，而不是丟進一條沒有收件人的 log。
- 值得借鑑的 interaction / workflow：GoalPipeline 是做完送到隔壁審，不通過連理由退回。TeamPipeline 的成員不互相談，主管只看結論；任務單必須寫齊，因為對方看不到你的桌子。SOP 只讓交接單走到下一關。服務層的 `TeamSay` 才是塞進信箱的便條，對方被叫醒才讀。權限詢問是人走到那張桌子蓋章，並可以留下「以後這類免問」的規則。
- 在 2D workspace 裡會變成什麼：一張俯視的樓層。每個 agent 一張固定工位，名牌是名字。GoalPipeline 是相鄰兩張桌子，文件從執行桌走到審核桌，退件時紅筆意見夾回去；兩人中間的檔案櫃是共用 workspace，審核的人可以打開看真正寫下的檔。TeamPipeline 的主管桌在可派工的位置，成員桌子之間沒有傳遞通道；主管寫完整工作單送出，不同成員可以同時動，同一人的單則排隊。有人等工具核准時，那張工位停住，主管也停在對應的那張派工上。SOP 是走廊上的關卡，每一關有自己的嘗試次數，只有交接單走到下一關。服務層團隊則是每人一間自己的房間（一個 session），`TeamSay` 是走到門口塞便條或對全組廣播，信箱有東西才把人叫醒。Web UI 和已停更的 Studio 是掛在牆上的監看螢幕，用來看推理、工具和權限詢問。那是監督。樓層本身仍是這些工位、關卡和檔案櫃。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走進去看得到誰在跟誰說話。TeamPipeline 裡只有主管和成員之間有路，成員之間沒有門，所以不會看見兩個成員湊在一起討論。等人核准的人坐在自己的位子上停住；人要走到 `reply_id` 指出的那一張桌子蓋章，或回答 `AskUser`。共用 workspace 是同一組可以打開的檔案櫃。SOP 的等待是某一關卡前站著等人，狀態存下來之後，隔天還停在同一關。服務層的工人在各自房間裡同時做事，便條進信箱後才被叫醒。重點是看見誰在場、誰在等誰、工作單和結論放在哪裡。
- 需要重新設計的地方：框架沒有「人站在哪裡」。位置、走道和誰看得到誰，要另做成空間。Studio 與 2.0 Web UI 是對話、追蹤和權限面板，屬於 ADE dashboard，和 Conductor 那類監看畫面同一類；它們不是這間 2D 樓層，也不是可走進的 3D 辦公室。pipeline 與 SOP 仍標成實驗中。1.x 的 MsgHub 不要直接畫成 2.0 的會議室。長期記憶在差異說明與 2.0.9 文件裡的說法還沒對齊，記憶抽屜的規格先不要定死。

## 初步看法

- 最有價值的部分：交接被收成少數幾種看得見的形狀，而且人的回答有收件人。主管只收結論、成員不互相談，這兩條對辦公室的走道怎麼開，影響很直接。
- 最大限制或疑問：它是給開發者呼叫的框架，本次沒有跑起來。管線和 SOP 可能改介面。`main` 比 v2.0.9 新多少、服務層信箱的預設後端、以及長期記憶文件是否已取代舊的差異說明，都尚未確認。
- 是否值得進一步研究或親自體驗：值得，尤其是 `reply_id` 如何把人帶到正確的工位，以及 TeamPipeline 和 Agent Team 兩種「團隊」放在同一層樓時會不會讓人搞混。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://agentscope.io/
- Repository：https://github.com/agentscope-ai/agentscope
- Documentation（v2.0.9）：https://docs.agentscope.io/en/versions/2.0.9
- License：repo `LICENSE` 為 Apache License 2.0；GitHub license 為 Apache-2.0
- Release：GitHub `v2.0.9`（2026-09-28）；PyPI `agentscope` 2.0.9。需要 Python 3.11+
- 已讀頁面：What's AgentScope 2.0、Changelog（2.0 vs 1.0）、Message & Event、Pipeline overview、Goal Pipeline、Team Pipeline、SOP、Human-in-the-Loop、Permission overview、Long-Term Memory、Agent Team
- `main` 的 `src/agentscope/pipeline/__init__.py` 匯出 `PipelineProtocol`、`GoalPipeline`、`TeamMember`、`TeamPipeline`
- 1.x MsgHub：https://docs.agentscope.io/en/versions/1.0.21/building-blocks/orchestration （只作對照，不與 2.0 API 混用）
- Studio（兄弟 repo，官方說明已建議改用 2.0 Web UI）：https://github.com/agentscope-ai/agentscope-studio
- Runtime（兄弟 repo，不是這個 Cell）：https://github.com/agentscope-ai/agentscope-runtime
