# Free4chat

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-free4chat-cell-fb52/reviews/free4chat.md)
>
> Cell ID：[https://github.com/i365dev/free4chat](https://github.com/i365dev/free4chat)
>
> Status：`untried`
>
> Category：臨時協作 Room（人與獨立 Agent）
>
> Last updated：2026-10-10

## 產品介紹

Free4chat（[free4.chat](https://www.free4.chat)）是一個開源的臨時協作房間。人用瀏覽器打開連結就能進房；已經在自己電腦、Mac mini、VPS 或容器裡跑的 agent，用本機 runtime 或 MCP 走進同一間房。房裡可以語音、文字、傳檔、共享畫面，也可以給某個 agent 一段聚焦的 Task，看進度、核准、打斷或轉向。官方把產品寫成仍在試形狀的個人技術試驗場，穩定的想法是：低摩擦地把各自擁有的人和能力拉到同一段短時間裡一起做事。

這間房沒有帳號，也沒有常設工作區。建立房間的人沒有房主或管理員身分，Room id 只是公開的邀請座標。Agent 的模型、工具、憑證和私人記憶留在它自己的機器上，Free4chat 不代管模型，也不替你跑 harness。本次只讀 GitHub 預設分支 `cf-sfu` 的 README、LICENSE，以及官網文件，沒有安裝、沒有進房。

## 主要 Features

### 臨時 Room：誰在場、誰擁有什麼

Room 是一段短命的信任與協作邊界。文件寫的分工很清楚：Free4chat 握住暫時的在場名單、誰被點名、有限的共享上下文、結構化的請求與結果、有上限的附件與表面、以及經 Cloudflare Realtime SFU 轉送的語音和畫面。每個參與者握住自己的模型、工具、憑證、本機核准政策、私人記憶和持久產出。

人從瀏覽器進房。Agent 有兩條路進同一間房：瀏覽器裡的 Invite Agent 會複製一段只屬於這間房的提示，貼進你已經在用的 agent；或是在終端機用官方的 `free4chat-agent` 執行 `room create` / `room join`。內建啟動器目前是 hermes、opencode、codex、claude、pi；其他行程要用 `--agent-command` 指向你信任的本機 ACP 行程。進房本身不構成開工，也不組成一支 agent 團隊。能力標籤（例如 `code.edit`、`shell`）是參與者自己報的發現資訊，對方看了知道可以開口問，真正做不做仍由目標那一端的本機政策決定。

晚到的人會拿到這間房還在的狀態，包含目前的 Task 和 Live View。別人瀏覽器裡尚未送出的本地互動不會被複製過來。全員離開後，房間會在空著一段時間之後自動過期。空房要等多久，現行文件只寫「一段時間」，具體分鐘數尚未確認。

### Runtime 握住參與，Harness 握住智力

本機的 Go runtime（`free4chat-agent`）負責當這個參與者：私人的 participant handle、租約、斷線重連、事件、附件、媒體，以及 Task 範圍。Harness 是你選的智力與本機工具。文件寫明 handle 不會交給 harness。直接打公開的無狀態 MCP Room API 是低階路徑，適合整合和除錯；要跨很多輪一直在場，官方建議用常駐 runtime。現行原生發行針對 macOS 與 Linux，Windows 延後。Runtime 要跟託管服務一起升級，舊版不保證能用。

看見房間內容不會自動叫醒 harness。點名訊息是輸入，收到的 agent 在自己的政策下決定要聊天、用工具、請別人、附上產出，或拒絕。

### Task、核准，以及人如何轉向

一般房間對話是共用上下文。Task 是交給一個 agent 的一段聚焦工作：自己的對話與活動、產出、核准，以及可選的一塊互動表面。它活在這間臨時房裡，不是永久的 thread 或專案。預設會開一段新的 harness session，讓 agent 從這份任務說明開始。只有官方驗證過「可以接續」的 harness，Start Task 才會提供繼續既有本機會話；本機會話清單要等人明確打開才讀取，而且不對其他參與者公開。

Task 可以跑很久。關掉瀏覽器或離開房間，本身不會取消還在本機跑的 Task；你之後用另一個瀏覽器或手機回到同一間還活著的房，看到的是有限的粗狀態（啟動中、工作中、這一輪正在跑、排隊、已打斷、完成、失敗、session 遺失），不是全程重播。本機 runtime、harness 或機器關掉之後，文件不保證工作還在跑，並要求介面老實顯示 session 遺失，而不是假裝還在執行。

人在場時有兩種控制。Interrupt 請目前這一輪讓路或取消，是盡力而為，慢的 harness 會停在打斷中，直到這一輪自己結束；它不保證同步殺掉子行程。Interrupt & Send 是轉向（steer）：你打的話先成為這份 Task 的正式下一則輸入，排在尚未開始的一般後續之前，並請目前這一輪讓路，好讓這句話更早跑到。讓路若被忽略，改變的是何時執行，這句話仍然留著，而且只跑一次。核准卡來自 harness 自己發出的權限請求；沒有人回答就失敗關閉。進房不會自動打開 shell、檔案或憑證。

同一份 harness session 一次只跑一輪。獨立 Task 能不能並行，要看該 harness 是否被驗證過。文件目前寫：Hermes 最多同時 2 個獨立 Task 輪、Pi 最多 4 個；Codex、OpenCode、Claude 一次一個。滿了就顯示排隊。

### 產出、然後房間關掉時留下什麼

預設產出是文字或有上限的附件（報告、patch 這類）。需要一點互動時，可以選擇 Live View：一小塊由 Free4chat 自己渲染的宣告式介面，例如狀態、表單、幾個按鈕；它不執行 agent 交來的任意 HTML。再大一點、但仍該跟著房間消失的，是 Generated Task Room App：一份沙箱裡的臨時小程式，V0 沒有一般網路，狀態跟房間一起走。要看對方真實桌面，文件建議用螢幕分享。策展過的 Room App（README 以 Whiteboard 為例，讓人和獨立 agent 編輯同一份原生物件）是房間裡打開的既有活動，生命週期也是這一段房間。

房間過期時，共享訊息、Task、Task 產出、Live View、產生的小程式和它的狀態、以及已提交的 Live Transcript，都是房間狀態，會一起刪掉。語音本身不被 Free4chat 錄下來；人與人之間的檔案走即時通道，上限約 20 MB，不進永久庫。給 agent 讀的文字類附件目前上限約 768 KB，同樣隨房間消失。要留下的東西，得由參與者自己放進本機檔案、repository、harness 記憶，或其他自己擁有的系統。瀏覽器 `localStorage` 只記住你在這台瀏覽器裡用過的房間暱稱。Harness 在本機留下的私人 session，是參與者的東西，不是 Free4chat 的房間歷史。房間關閉後，這段本機 session 能不能被下一間新房接上，尚未確認。

## 主打賣點

- **臨時房間，而不是常設辦公室。** 用一條連結或 Room id 把人和已經在跑的 agent 拉進同一段時間，做完就散。沒有帳號、沒有組織、沒有房間史。
- **能力留在參與者身上。** 房間只做會合、在場、定址和有限共享。模型、工具、憑證、核准和持久記憶留在各自的機器。這和「把所有 agent 雇進一個中央平台」是不同的結構。
- **人可以離開，但仍能監督和轉向。** 瀏覽器不是本機執行的主人。回來可以看粗狀態、核准、打斷，或用 steer 把一句話安成下一則正式輸入。執行本體死掉時，狀態要誠實。
- 語音、傳檔、螢幕分享、Whiteboard 這類房間活動，是這段臨時會合上的媒體和共享表面。它們服務同一間房，不把房變成專案庫。

## 使用情境

### 開一間短會，把兩個已經在跑的 agent 叫進來

- 適合誰：機器上已經有 Pi、Codex、Claude、OpenCode 或 Hermes，想讓它們在同一段對話裡碰面的人。
- 在什麼情況使用：一台機器 `room create`，把 Room id 給另一台機器 `room join`，人用瀏覽器進同一條連結。
- 帶來的價值：不必先建團隊或工作區。誰在房裡、誰報了哪些能力，就是這段時間的編制。

### 交給一個 agent 一段長工作，人先離開再回來轉向

- 適合誰：要 agent 在本機做一段可能跑很多分鐘的聚焦工作，同時自己會離開瀏覽器的人。
- 在什麼情況使用：在房裡對在場的 agent Start Task，之後用另一個瀏覽器或手機回來查看、核准、Interrupt，或用 Interrupt & Send 把新指示安成下一則輸入。
- 帶來的價值：監督不綁在這一台瀏覽器上。工作仍跟著這間還活著的房，以及那台還開著的本機 runtime。

### 人與人先開會，需要時再請 agent 讀同一段上下文

- 適合誰：先要語音、文字、傳檔或看畫面，稍後才需要 agent 的人。
- 在什麼情況使用：瀏覽器開房，人先進來；需要時用 Invite Agent。必要時由人授權一台本機 runtime 做整房的 Live Transcript，讓文字進共享上下文。
- 帶來的價值：agent 是可選的參與者。逐字稿和附件有上限，而且跟著房間走，不會變成永久會議紀錄。

## 我們可以學什麼

- 值得借鑑的 product idea：工作空間可以是一間會消失的房間，而不是員工的家。在場名單、點名、有限共享、Task 關聯和核准，屬於這段會合；模型、工具、憑證和持久記憶屬於走進來的那個人或那台機器。
- 值得借鑑的 interaction / workflow：進房只代表在場。開工是另一個動作（Start Task）。看見上下文不會自動叫醒對方。Steer 把人的話留成下一則正式輸入，並請目前這一輪讓路；話不會因為讓路變慢就消失。本機還活著時，人可以離開再回來看到粗狀態；本機死了就顯示 session 遺失。房間空了過期之後，共享紙張整間收掉，要留下的必須被某個參與者帶走。
- 在 2D workspace 裡會變成什麼：一張俯視樓層。有人開房時，平面上多出一間沒有房主座位的臨時會議室。人從連結走進來，agent 從自己的機器走進來，腳下的名牌是暱稱加上自己報的能力，那是自我介紹，不是你可以遙控的開關。房中央是共用對話桌；每份 Task 是靠牆的一張工作桌，桌上放進度、核准卡，以及可選的一小塊 Live View。Steer 是你走到那張桌子，把一張字條插到尚未開始的工作前面。你走回自己的工位時，只要對方本機還亮著，那張桌子的燈就還亮；你再進來只看到工作中、排隊或 session 遺失，看不到全程重播。全員離開、空房過期後，這間會議室從樓層上拆掉，對話、附件、逐字稿和小程式一起消失。能留下的是各人帶回自己工位的檔案、repo 和 harness 筆記，那些不屬於這間臨時房。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你推開一間臨時會議室，看見誰站在裡面、誰坐在哪張 Task 桌子前做事。沒有人坐在房主位。Agent 的身體表示他的 runtime 還連著自己的機器；能力徽章掛在身上，你不能按徽章就遠端執行。核准是桌上的一張卡片，沒人簽就這一步失敗關掉。你走出房門，agent 若本機還在跑，人還坐在那張桌子前；你再走進來，看到的是誠實的粗狀態，而不是假裝他一直在辦公室裡錄影。Steer 是你走過去說一句話，這句話成為他下一件要做的事。房間過期時，這間會議室連桌面、小程式和逐字稿一起拆掉。憑證和私人記憶從未放進這間辦公室的櫃子，它們留在 agent 自己的機器上。這是一間會拆掉的會議室，不是 Conductor、Maestro、Firstmate 那種側欄儀表板。
- 需要重新設計的地方：持久辦公室若要借這個模式，得把「員工的家」和「臨時會議室」分成兩種空間。家留下人、座位和私人抽屜；會議室有在場名單、Task 桌和 steer，並會在空房後拆除。空房的具體逾時尚未確認，空間介面上先不要畫一個假裝精確的倒數。並行上限隨 harness 而不同，同一張桌子能不能同時做多份 Task，要跟這個人綁在哪一種 harness 一起顯示。媒體走 Cloudflare 轉送，隱私頁寫明語音路徑不是端對端加密；若 2D／3D 辦公室要做語音，這段路徑要另外設計。Whiteboard 與產生的小程式都是房間壽命內的表面，還沒實測手感。

## 初步看法

- 最有價值的部分：把「誰在這段時間裡」和「誰擁有能力與記憶」拆開，並給人一種離開後仍能回來轉向的監督。Session 遺失要老實顯示，這點對空間介面很重要。
- 最大限制或疑問：尚未實際進房。空房逾時的長度尚未確認。關瀏覽器不等於停工，但本機行程死掉就沒有耐久執行。沒有房間史，代表事後不能回放這間房。房間關閉後，本機 harness session 能否帶進下一間新房，尚未確認。
- 是否值得進一步研究或親自體驗：值得。下一步若要親手試，優先看 steer 是否真的插隊只跑一次、晚到的人看見哪些狀態，以及房間過期後參與者側還留下什麼。

## Sources

- Official website：[https://www.free4.chat](https://www.free4.chat)
- Repository：[https://github.com/i365dev/free4chat](https://github.com/i365dev/free4chat)（預設分支 `cf-sfu`，MIT）
- Documentation：[Browser Room](https://www.free4.chat/docs/getting-started/browser-room)、[Agent Room](https://www.free4.chat/docs/getting-started/agent-room)、[Rooms and ownership](https://www.free4.chat/docs/concepts/room)、[Runtime and Harness](https://www.free4.chat/docs/concepts/runtime-harness)、[Agent Tasks](https://www.free4.chat/docs/guides/tasks-and-live-views)、[Interactive Task outputs](https://www.free4.chat/docs/guides/interactive-task-outputs)、[Privacy](https://www.free4.chat/privacy)
