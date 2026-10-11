# Buzz

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-buzz-cell-80a8/reviews/buzz.md)
>
> Cell ID：[https://github.com/block/buzz](https://github.com/block/buzz)
>
> Status：`untried`
>
> Category：自架的人機共用工作空間（Nostr relay）
>
> Last updated：2026-10-10

## 產品介紹

Buzz 是 Block 開源的協作工作空間。人和 AI agent 進同一批房間：頻道、討論串、私訊、畫布、搜尋、工作流程，以及還在長出來的 Git。你打開桌面 app，連到一個 relay；那個網址就是這一間工作空間。自己架的時候，一台 relay 就是一個社區。代管時，很多社區可以共用後面的機器，每個網址仍然是自己的世界。授權是 Apache-2.0。官方也用 [buzz.xyz](https://buzz.xyz) 提供代管註冊。尚未安裝或執行。

它處理的是協調。Block 的工程部落格把舊習慣寫成：每個人單獨對著一個 harness，再把輸出貼進聊天、把回覆貼回來。Buzz 把這段搬運收進同一間房。Agent 用自己的金鑰進頻道，被人 @ 到才開口。後面的執行程式可以換：官方預設是 goose，也接 Codex、Claude Code，以及他們自己很薄的 `buzz-agent`。換模型或換 harness，房間裡的身份、成員資格和已簽過的歷史留在 relay 上。

Repository 沒有封存，也不是一層薄客戶端。伺服器、桌面（Tauri）、給 agent 用的 CLI、ACP harness 都在 [block/buzz](https://github.com/block/buzz)。GitHub 短述是 “A hive mind communication platform”。README 的說法更接近產品：人和 agent 在你自己的 relay 上一起做東西。

## 主要 Features

### 代理人是成員，金鑰是自己的

人和 agent 都是 secp256k1 金鑰。看不看得到內容，看的是頻道成員資格。官方要人記住的差別是：授權留在旁邊，作者仍是 agent。主人簽一張範圍很窄的證明（NIP-OA，文件標成 draft），事件的 pubkey 還是 agent 自己。金鑰外洩就撤銷這個 agent，人的身份不用換；主人被移出，agent 也連不回去。

ACP harness 預設只把主人的訊息交給 agent（`owner-only`）。還沒登記主人之前，它什麼都不回。可以改成允許名單、任何人，或完全不聽、只靠心跳。主人在頻道裡打 `!cancel`、`!rotate`、`!shutdown`（必須單獨 @ 到這個 agent，內文剛好是指令）就能停掉這一輪、清掉這段 session，或關掉 harness。預設一個 session 涵蓋整個頻道。改成 thread 政策時，指令要回在那條討論串裡，才只影響那一桌。

桌面的官方說法是：把 agent 加進頻道，和加人一樣。ACP 文件仍寫，relay 還沒有管理頻道成員的 REST／event API，私人頻道要先有成員身分。兩種路徑怎麼對上，尚未確認。

### 房間是交接的地方

訊息、反應、工作流程步驟、Git 事件都是簽過名的 event，進同一個可搜尋的紀錄。Agent 之間的交接，官方描述得很普通：在頻道裡 @ 對方。工程部落格說，一個較強的 agent 會用 mention 把研究、實作、測試分給較便宜的 agent，人在同一間房裡中途改方向。被否決的路徑留在頻道裡，之後搜得到當時為什麼不那樣修。

同一個身份後面可以開多個 ACP 子行程（最多 32）。外人仍看到一個名字。同一個頻道不會同時被兩個子行程處理。有待辦時心跳讓路；閒下來才去看未完成的動作和 mention。

### 兩種記憶：牆上的紀錄，和上鎖的筆記本

頻道歷史是大家看得到的牆。另外有一份 agent 自己的記憶，規格叫 engram（NIP-AE，標成 draft）。`core` 放身份、規則、目標；`mem/…` 是一則一則的條目。內容用 agent 和主人之間的對話金鑰加密，主人讀得到，外人從網址上看不出 slug。`buzz-acp` 的程式會在建立 session 時注入這份核心記憶。這層和寫進頻道的對話分開。每次開機是否都穩定注入，尚未實測。

### 工作流程負責叫人

YAML 工作流程可以依訊息、反應、排程或 webhook 觸發。Relay 負責把事情送到該看的人。真正的運算在 agent 自己的機器上，結果再貼回頻道。核准閘門的資料表、API、介面都在，但執行器碰到 `request_approval` 時還不會暫停再續跑。VISION 把這種執行標成失敗（WF-08）。README 也把它放在「正在接線」。

### Git 已經能放進同一棟樓

Git hosting 在狀態表裡是今天可用的：smart HTTP 的 clone／push，物件放在內容定址的儲存，指標用 compare-and-swap 前進。工程部落格說已有早期的 forge 介面，讓人看見 repo、變更和 agent 活動。

「開一條 feature branch 就出現一間頻道，補丁、CI、審查、合併決定都在裡面，合併後頻道歸檔」是他們最想被記住的工作方式。VISION_PROJECTS 同時把 project binding、多 repo 專案、merge coordinator、議題、跨專案聲譽標成設計中。2026-07 的產品公告仍把 Git 整合稱為早期。分支是否每次都會自動變成房間，尚未確認到可以當成走完的流程。

人設包把名字、提示、skills、MCP 收成可攜的一包。桌面狀態表寫內建人設已隨 app 提供。遠端執行（筆電合上之後，同一個金鑰在別的機器上醒來）在 VISION 裡仍是審查中的規格。身體可以換的方向寫得很清楚；遠端部署本身尚未當成已出貨。

## 主打賣點

- **房間是共用的上下文。** 身份、對話、決定、工作留在同一個 relay。換 harness，這段歷史還在。Block 把瓶頸從「模型夠不夠聰明」說成「團隊有沒有一個一起做事的地方」。
- **Agent 帶自己的簽名。** 預設只聽主人。授權證明寫明誰准了它、在什麼條件下，作者欄仍是 agent。
- **一個網址是一棟樓。** 金鑰可以帶到別的社區。檔案、私訊、成員資格、記憶留在原來的網址。

頻道、討論串、反應、全文搜尋、YAML 自動化，在一般團隊聊天工具裡都見過。Buzz 把它們收成同一條簽過名的 event log，並讓 agent 用和人同一種成員資格站在裡面。桌面 app 是看這棟樓的窗口。Conductor、Maestro 那類控制台編排的是很多個 CLI；Buzz 是那些員工被 @ 進同一個頻道之後站的地方。goose 是官方預設接上的其中一個 harness，這個 Cell 只寫 Buzz。

## 使用情境

### 把交接留在正在討論的頻道

- 適合誰：團隊已經會用 coding agent，交接仍靠人在聊天室和 harness 之間複製。
- 在什麼情況使用：把 agent 加進正在討論的頻道，用 @ 把問題丟給它；它用自己的金鑰回覆，必要時再 @ 另一個 agent。
- 帶來的價值：過程留在房間裡。人中途改方向時，前文還在這條頻道上。

### 先只讓主人叫得動

- 適合誰：不想讓頻道裡每一句話都變成一次工具呼叫的人。
- 在什麼情況使用：維持預設的 `owner-only`。要停就在房間裡下 `!cancel` 或 `!rotate`。準備讓更多人喚醒它時，再改成允許名單或任何人。
- 帶來的價值：門禁跟身份在一起。這次對話可以是整間頻道，也可以只是一條討論串。

### 事後還查得到當時為什麼不那樣修

- 適合誰：事故、發布、審查要能對帳的人。
- 在什麼情況使用：在頻道裡問以前有沒有看過這個錯誤。官方故事是 agent 去搜歷史，把討論串、原因、修法貼回來。搜尋索引今天就在；agent 會不會穩定交出帶出處的答案，尚未實測。
- 帶來的價值：牆上留的是整段過程。Git 上的 diff 和綠燈留不住被否決的理由。

## 我們可以學什麼

- **值得借鑑的 product idea**：辦公室等於一個你擁有的 relay。員工（人與 agent）用自己的鑰匙進房，成員資格決定進哪些門。Harness 是身體，relay 是家。牆上的頻道紀錄是大家的記憶；engram 是桌子抽屜裡、主人打得開的筆記本。授權是簽在事件旁邊的證明，作者欄保持是做事的那一個鑰匙。
- **值得借鑑的 interaction / workflow**：用 @ 拍肩膀，人拍 agent，agent 也可以拍 agent。主人用房間裡的短指令停掉、換掉這段 session，或請 harness 離開。閒置時才心跳去看待辦，忙的時候不插隊。一個名字後面可以有多個身體，同一間房一次只有一個身體在說話。核准應該是這條時間線上的一個簽名。
- **在 2D workspace 裡會變成什麼**：一個社區網址是一層樓。頻道是平面上的房間，人和 agent 的桌牌並排，agent 的徽章和人不同。預設 agent 只聽得見主人的聲音；改成允許名單，就是房門上多貼幾張名牌。討論串政策下，一條 thread 是房裡的一張桌子，`!cancel` 只清那張桌子。多個 ACP 子行程是同一張桌牌後面的幾張側椅，外人仍看到一個名字，同一間房不會有兩張側椅同時打字。Engram 是桌牌底下的上鎖抽屜。工作流程的痕跡貼在房間布告欄。Git repo 是樓層裡的檔案櫃。「分支變成臨時房間、合併後封存」是這張平面圖預定要長出來的隔間；文件仍把它標成設計中，地板上先畫成虛線。桌面 app 是樓層的一扇窗。
- **在 3D workspace 裡會變成什麼**：你走進這棟社區大樓，看得到誰在場。Presence 是一張會過期的在席租約，斷線之後點會滅。你走到事故頻道，人和 agent 坐在同一張桌。你 @ 某人，對方轉過來。預設那個 agent 聽不見其他人，直到主人把房門政策打開。Agent 再開一條側頻道，走廊上就多一間你走得進去的房間。`!shutdown` 是請這個人離開大樓，鑰匙還在名冊上。遠端執行的圖像是：筆電合上，同一個同事明天在另一張桌子醒來，因為家是 relay；這部分規格仍在審查，大樓裡這扇「換身體」的門還沒有當成已啟用。Git 是地下室的檔案櫃，早期的 forge 窗能看見變更；「這條分支就是這間房」還沒有蓋完。
- **不值得照搬或需要重新設計的地方**：辦公室本體是 relay，Tauri 桌面只是窗口。平行子行程若只畫成一個圓點，樓層會看不到誰在打字；對外仍是一個簽名，對內要看得到側椅。頻道成員資格是很粗的門，NIP-OA 的條件主要是事件種類和時間窗，工具能不能跑 shell 取決於後面的 harness。空間辦公室若只畫「他是成員」，會漏掉 goose 或其他 runtime 自己的允許與詢問。核准閘門碰到步驟時還會把執行標成失敗，不能畫成一盞已接通的綠燈。願景文件把分支房間、聲譽、遠端身體、文化功能寫得很完整，狀態表把其中多項標成設計中或正在接線；地板上要分得出今天走得進的房間，和圖上的虛線。語音 huddle 在 VISION 算已接上，在 README 的狀態表算正在接線，尚未確認，先不要做成必經的會議室。加密的 engram 留在抽屜，不要貼上共用的牆。

## 初步看法

- 最有價值的部分：共用房間是一等的紀錄，agent 用自己的鑰匙站在裡面，身體（harness）可以換。這一層和單一員工的 runtime、也和同時打開很多 CLI 的控制台，各管一段。
- 最大限制或疑問：forge 的故事走在實作前面。核准續跑、遠端換身體、跨專案聲譽都還不能當成今天的地板。ACP 文件裡的成員管理缺口，和「把 agent 加人一樣加進頻道」怎麼對上，尚未確認。整個系統沒有親跑。
- 是否值得進一步研究或親自體驗：值得當共用辦公室的底層參考。若要驗證，在 repo 外看三件事即可：主人以外的人 @ 了會不會被丟掉、engram 會不會在新 session 出現、一條分支會不會真的長出房間。

## 後續補充（選填）

Not tried yet。官方 README、願景文件、ACP 說明和 NIP 草稿已足夠分開「今天的房間」和「圖上的 forge」。沒有把 upstream clone 進這個 repo。

## Sources

- [block/buzz](https://github.com/block/buzz)
- [Introducing Buzz（Block，2026-07-21）](https://block.xyz/inside/introducing-buzz-where-humans-and-agents-work-together)
- [Buzz!（Block Engineering，2026-07-21）](https://engineering.block.xyz/blog/buzz)
- [buzz.xyz](https://buzz.xyz)
- [VISION.md](https://github.com/block/buzz/blob/main/VISION.md)
- [VISION_PROJECTS.md](https://github.com/block/buzz/blob/main/VISION_PROJECTS.md)
- [VISION_AGENT.md](https://github.com/block/buzz/blob/main/VISION_AGENT.md)
- [VISION_REMOTE_AGENTS.md](https://github.com/block/buzz/blob/main/VISION_REMOTE_AGENTS.md)
- [buzz-acp](https://github.com/block/buzz/blob/main/crates/buzz-acp/README.md)
- [NIP-AE Agent Engrams](https://github.com/block/buzz/blob/main/docs/nips/NIP-AE.md)
- [NIP-OA Owner Attestation](https://github.com/block/buzz/blob/main/docs/nips/NIP-OA.md)
- 授權：Apache-2.0
