# Amux

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-amux-cell-80a8/reviews/amux.md)
>
> Cell ID：[https://github.com/mixpeek/amux](https://github.com/mixpeek/amux)
>
> Status：`untried`
>
> Category：Self-hosted coding-agent control plane（共用看板＋具名 worker）
>
> Last updated：2026-10-10

## 產品介紹

Amux 是放在既有 coding agent 上面的控制層。Claude Code、Codex、Gemini CLI、
OpenCode、Ollama 仍是各自的員工，預設跑在 tmux 裡，也可以改掛
[Herdr](reviews/herdr.md)。Amux 給這支隊伍一面共用看板、彼此傳話的管道、
排程，以及從瀏覽器或手機中途改口的入口。官方網站是 [amux.io](https://amux.io)。
repository 現況是一支本機 Rust binary：SQLite、儀表板嵌在 server 裡，預設
`https://localhost:8824`。尚未安裝或執行。

它比較像**辦公室的櫃檯與佈告欄**。人把工作放上牆，具名 worker 領走一張卡；
人也可以從儀表板或手機打字進某個正在跑的終端。狀態在 server 上，窗口可以換。
這仍是 ADE dashboard：你看到的是看板和 worker 清單。樓層本身要另外長出來。

2026-10-10 的 README 和公開 FAQ 描述的不是同一套產品。README 寫 Rust、多種
CLI、本機 bearer token（localhost 免驗證）。FAQ 仍寫 Python、`brew`／`pipx`、
只服務 Claude Code，並說沒有內建 auth。下面以 repository README 與 `docs/`
為準。授權是 MIT 加 Commons Clause：可以自己用、改、自架；拿去轉賣需要另外
授權。GitHub 的 license API 把它標成 Other／NOASSERTION。

## 主要 Features

### 八個原語組成控制層

README 把產品收成八樣東西：看板、worker、排程、檔案系統、group、記憶、
環境變數、訊息。新能力應該是這八樣的組合。worker 是一條有耐久身份的
agent lane，停了再開，名字還在。group 是幾個人共用的標籤：同一組看得到
彼此，並共用記憶、環境與門檻。HTTP 與環境變數仍寫舊名 `session`／`tag`，
因為一次改名會弄斷正在跑的人。

### 牆上的卡只有一份

看板是全隊的工作真相。領卡是 compare-and-swap，兩個人不能同時拿走同一張。
欄位大致是 backlog、todo、doing、review、done、verified、discarded。`done`
是做完。`verified` 是更強、而且可選的說法：已經在正式環境確認過。多數卡
停在 done。

卡片類型決定關門前要勾什麼。預設 `code` 最嚴，做到 done 要有實作與測試；
決策、調查、文件用較輕的類型，關掉時要留下結果。指南寫明：型別標錯時，
工人會被迫用假的 merge 過關，應該改型別。每個 worker 在 `doing` 軟上限
一張，第二張要明講 override。人擁有的卡（`owner_type: human`）不會被自動
改派。有指定 reviewer 時，走到 done 或 verified 的確認必須來自那位
reviewer，作者不能自己蓋章。`force` 可以繞過，而且會留下紀錄。

人打給 worker 的提示，預設會變成一張卡。短句、控制詞、slash command，或
加上 `[no-board]`，就不會上牆。依賴還沒做完的卡，自動領取會跳過。

### 傳話、偷看、改口是三扇門

訊息是 worker 之間、以及人對 worker 的文字，在**回合邊界**送達，不插進
模型正在吐字的中間。官方說 server 記錄真正的寄件人，出處是紀錄，不是內文
裡自己寫的名字。README 又說每個 worker 看得到整隊：誰在線、手上有什麼、
在做什麼，並且可以在打斷之前偷看別人的終端。

協調文件寫得更緊：共用看板並不授權你去指揮對方、花對方的額度或代發對外
動作。來自 worker 的輸入要先屬於同一個 group。這兩句話的邊界尚未確認。
人的 steering 是另一扇門：從儀表板或手機打進正在跑的 session，不必先停掉
這次執行。

### 記憶與環境分層，預設只能改自己那層

記憶是說明與知識，由全域、group、再到這位 worker 疊起來。環境變數用同一套
疊法，決定這個人碰得到哪些第三方 API。讀寫各層的入口是 `/api/scope`。預設
agent 只能寫自己那一層；`AMUX_SCOPE_WRITE_AGENTS=1` 才准寫 group 與全域。

排程是 cron 式的重複或單次工作，有跑過的稽核紀錄。官方另外說有自我節奏的
overnight loop。換模型也是官方說法：同一個正在跑的 worker 可以換模型或
provider，上下文留在原處。換完之後實際留下多少，尚未確認。

關門清單的查找順序，指南寫的是：這張卡、卡片類型、這位 worker、欄位預設。
README 則說設定沿著卡、worker、group、全域解析。group 插在門檻的哪一層，
兩份文件沒有對齊，尚未確認。

### 當機之後分三種帳

README 的一句話是：watchdog 會自動壓縮上下文、重啟當掉的 session、重播上一
則訊息。`docs/harness-recovery.md` 寫得比較嚴。意圖已經存好但還沒送出，
可以接著送。已經送出而且有收據，就認那張收據，不要再打一次。領了工作卻
沒有收據，不能標成成功，也不該盲目重播任意 shell 或對外動作。尚未實測
watchdog 實際走哪一種。

終端預設是 tmux。改掛 Herdr 要在設定裡打開，而且 README 寫明這條路沒有進
CI，綠燈只證明選得到 backend。對 OpenCode，server 裡有一條結構化協定，讓
提示、訊息、取消與狀態不必全靠刮終端畫面；刮畫面被寫成退路。這條協定蓋到
多廣，尚未確認。

文件還描述一種專案模式：一個專案一條 branch、一個 checkout，任務一次只准
一個寫入者。worker 是執行紀錄，不是 git workspace 的主人。Epic #46 仍把
自動 worktree 與環境隔離列為要做的項目。儀表板上現在是共用一個 checkout，
還是已經能每人一棵 worktree，尚未確認。

## 主打賣點

- **控制層，模型只負責推理。** Epic 寫明不要做私有的推理迴圈、固定的
  研究員到評論管線，或讓模型產生協調閒聊。這和
  [CrewAI](reviews/crewai.md) 那種角色與流程框架是不同層：Amux 想擁有的是
  執行、狀態、恢復，以及誰領了哪張卡。
- **隊伍的共享物件是看板。** 領卡是一次原子交換。`doing` 一次一張。人的
  承諾有鎖。指定 reviewer 時，作者不能自蓋章。`done` 和 `verified` 是兩種
  強度的完成。
- **員工有耐久的名字。** 停了再坐回同一張桌子。group 決定誰共用記憶、環境，
  以及誰聽得見誰。人用儀表板或手機改口，不必坐在那台終端前。
- **和本 repo 其他控制台並排時，牆比視窗重要。**
  [Conductor](reviews/conductor.md)、[Maestro](reviews/maestro.md)、
  [cmux](reviews/cmux.md) 也同時看很多 coding agent。Conductor 把隔離的
  worktree 當成工作單位。Amux 把全隊一面牆放在更前面；隔離的 checkout 在
  文件裡是專案模式加上路線圖。它也不取代
  [OpenCode](reviews/opencode.md) 或 [Herdr](reviews/herdr.md)：前者可以是
  一位員工，後者可以是員工坐著的終端 runtime。

多工作階段、看板、cron、從手機看狀態，在其他控制台裡已經常見。比較值得
記住的是領卡時的那一次交換、完成前的門檻、由 server 蓋章的寄件人，以及
恢復時把「沒送出」和「沒有收據」分開。

## 使用情境

### 好幾位 CLI 員工領同一面牆的工作

- 適合誰：同一台機器上同時開 Claude Code、Codex、OpenCode，又不想兩個人
  改同一件事的人。
- 在什麼情況使用：人把卡放到 todo。閒置的 worker 領走指派給自己、而且沒有
  被依賴擋住的卡。人的卡留在人身上。
- 帶來的價值：衝突發生在領卡的那一下。每個做著的人桌面上，軟上限是一張
  doing。

### 指定一位同事蓋章

- 適合誰：希望「做完」和「有人看過」分開的人。
- 在什麼情況使用：卡片寫上 reviewer。由那位 worker 把狀態推到 done 或
  verified，並勾掉門檻。要繞過就用 force，紀錄留在卡上。
- 帶來的價值：驗證是另一個人的動作。verified 留給要在正式環境確認的事。

### 人離開座位，仍能在下一回合改口

- 適合誰：agent 會跑很久、人會離開電腦的人。
- 在什麼情況使用：排程在固定時間丟一句話。人用儀表板或 iOS app 看誰卡住，
  直接打進那個 session，或傳一則會在回合邊界送達的訊息。官方建議遠端走
  Tailscale，不要把 8824 暴露到網路上。本機呼叫者免 token；這顆 token 擋
  的是非本機。
- 帶來的價值：窗口可以是瀏覽器或手機，隊伍的狀態仍在本機 server。這扇門是
  控制台的延伸。

## 我們可以學什麼

- **值得借鑑的 product idea**：辦公室需要一面所有人都讀寫的牆。卡是唯一的
  實體：領走、依賴沒清掉就不能拿、人的承諾有鎖、doing 有座位上限。員工是
  具名且停了還在的位子。group 是共用告示、共用鑰匙、聽得見彼此的一區。記憶
  是分層告示，預設員工只能在自己桌上加註。完成有兩枚印章。控制層保持為
  櫃檯，不再發明一位會寫程式的人格。
- **值得借鑑的 interaction / workflow**：人說話可以變成卡，也可以用
  `[no-board]` 聲明這只是問一句。傳話在對方抬頭時送達。改口是走到桌邊直接
  說。打斷前先偷看終端。寄件人由辦公室蓋章。恢復時先分清這件事根本沒送出、
  已經有收據，還是領了卻沒有證據。指定的審查者自己走到卡前面蓋章。
- **在 2D workspace 裡會變成什麼**：平面樓層中央是一面卡牆，欄位是牆的格子，
  每張卡只有一份。worker 是有名牌的桌子，名牌在關機後還在。group 是隔開的
  辦公區，區內共用一塊告示板與同一組對外鑰匙。doing 的格子旁，每張桌子只有
  一把椅子。human 的卡掛在上鎖的軌道。訊息是對方這回合結束時滑到桌角的
  紙條，章是櫃檯蓋的。偷看是走到玻璃旁讀終端，再決定要不要敲門。排程是牆
  上的時鐘，到點把一張紙放到桌上。手機和瀏覽器是這層樓的不同窗口，樓層在
  本機 server。終端縮圖留在桌子裡，地圖本身是牆、桌子和門。
- **在 3D workspace 裡會變成什麼**：你走進辦公室，看見具名的人坐在位子上。
  胸口的徽章是現在的模型；換模型是換徽章，人還是同一位，桌上的對話還在。
  中央是可以走過去拿的卡牆，兩個人同時伸手，只有一個人拿得走。審查者若被
  寫在卡上，作者站到章前面也蓋不下去，必須那位同事走過來。人擁有的卡在
  一道上鎖的欄杆後。同 group 的人在同一區，聽得見彼此。別區的人即使看得到
  牆，也不因此獲得指揮權；這道門要畫出來，因為文件裡「看得到整隊」和
  「同組才能傳話」是兩句話。當機時，夜間警衛分開三種帳：沒送出的接著送，
  有收據的認帳，沒有證據的標成中斷。大門和手機側門進來，看見的是同一間
  辦公室。
- **不值得照搬或需要重新設計的地方**：worker 網格、終端縮圖和手機遙控是
  ADE 的窗口，樓層要畫的是牆、位子和門。Epic 裡的自動 worktree 隔離、能力
  制權限、MCP broker 還在路線圖上；文件裡較具體的現況是一個專案、一個寫入
  者。刮終端判斷誰在忙是退路，在場與忙碌應走結構化狀態，像它對 OpenCode
  正在做的那條路。`force` 與自動回答卡住的提示若存在，必須是看得到的動作。
  `session`／`tag` 與 worker／group 兩套名字不要一起出現在地板上。公開 FAQ
  仍描述已退役的安裝方式，不能拿來當平面圖。`.mdai` 是會依來源重算的筆記
  檔，README 寫目錄介面還另案；它是檔案，不是立體圖譜，也不是這間辦公室。

## 初步看法

- 最有價值的部分：多個 CLI 員工共用一面牆、坐具名的位子、讀分層告示。領卡、
  門檻、審查者、人卡上鎖，都是辦公室裡看得到的規則。控制層自己不再做一個
  會推理的人格。
- 最大限制或疑問：它是控制台。網站 FAQ 和 README 互相矛盾。隔離的工作目錄
  到底是現況還是路線圖，尚未確認。偷看整隊與 group 才能傳話，邊界也尚未
  確認。Herdr backend 沒有 CI。自我修復的行銷句和恢復文件不一致。
- 是否值得進一步研究或親自體驗：值得當「多員工如何共用一面牆」的參考，並
  和 Conductor 以 worktree 為工作單位的做法對照。若要驗證，應在 repo 外的
  disposable 目錄跑，並以 README 的 `./install.sh` 為準。

## 後續補充（選填）

Not tried yet。官方 README、看板指南與恢復文件已足夠說明 workspace 需要的
物件：誰是員工、工作如何變成唯一的卡、記憶如何分層、人從哪裡介入。沒有
實際跑過領卡衝突、換模型或當機重播。

## Sources

- [amux repository](https://github.com/mixpeek/amux)
- [README](https://github.com/mixpeek/amux/blob/main/README.md)（2026-10-10）
- [LICENSE](https://github.com/mixpeek/amux/blob/main/LICENSE)（MIT + Commons Clause）
- [Board system guide](https://github.com/mixpeek/amux/blob/main/docs/guide.md)
- [Harness recovery](https://github.com/mixpeek/amux/blob/main/docs/harness-recovery.md)
- [One project, one working checkout](https://github.com/mixpeek/amux/blob/main/docs/project-single-worktree.md)
- [Roadmap epic #46](https://github.com/mixpeek/amux/issues/46)
- [amux.io](https://amux.io)
- [FAQ](https://amux.io/faq/)（與 README 不一致，見產品介紹）
- [Agent-to-agent orchestration](https://amux.io/guides/agent-to-agent-orchestration/)
