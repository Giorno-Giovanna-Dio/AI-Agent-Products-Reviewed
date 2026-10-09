# LifeOS

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-lifeos-cell-90bd/reviews/lifeos.md)
>
> Cell ID：[https://github.com/danielmiessler/LifeOS](https://github.com/danielmiessler/LifeOS)
>
> Status：`untried`
>
> Category：個人 AI harness（身份與完成定義）
>
> Last updated：2026-10-08

## 產品介紹

LifeOS（前身 PAI，Personal AI Infrastructure）是 Daniel Miessler 的通用 AI harness。它不自己當一套 coding agent，而是裝進你已經在用的 harness 裡。官方寫 Claude Code 是目前測得最多的路徑；設計上說不綁單一廠商，但安裝文件也寫明：always-on hooks 目前只在 Claude Code 完整接上。Cursor、Codex 等其他 harness 先拿到 skill、USER 資料、可選的 Pulse，以及每次工作階段載入的 context。其他 harness 的 always-on 行為是否已補上，尚未確認。

它要記住的是你是誰、在意什麼，以及「做完」長什麼樣子，再讓 harness 裡的模型沿著這件事做事。安裝方式是把一句話交給 agent（讀 `https://ourlifeos.ai/install` 並安裝），或在 macOS／Linux 的 Claude Code 上用 curl 腳本。需要 bun。個人設定放在 USER 樹；安裝與升級文件說不會覆寫已有的個人內容。這份筆記沒有實際安裝。

## 主要 Features

### Current State → Ideal State

整套系統繞著一件事：寫下現在在哪、想去哪，再用可以檢查的步驟把差距補上。人生層用 TELOS（使命、目標、問題、信念等，安裝後用 interview 問出來）。單次任務用 ISA：一份寫明「做完」的文件，條件要能被工具驗證。價值是把「幫我做」變成「對著一份完成定義往上爬」，而不是每輪重新解釋你要什麼。

### Digital Assistant 身份

官方把你對話的那一層叫 Digital Assistant（DA）：有名字、有身份檔，和使用者（principal）配成一對，工作階段開始時載入。公開安裝流程包含 interview，用來命名助手並寫入 TELOS。文件另有一套 DA heartbeat、成長與多 DA 的設計，但寫明 Pulse 裡的 Assistant 實作不進公開 release，公開安裝拿不到那棵程式。公開安裝實際會有多主動、會不會自己問「要不要做下一步」，尚未確認。

### Algorithm：對著 ISA 往上爬

ISA 是名詞，Algorithm 是動詞。官方現在的說法是：沒有事先猜難度的模式或層級（早期 E1–E5 與固定階段已在 2026 年中拿掉），難度在爬的過程裡露出來；沒有工具證據就不能把一條條件算完成。網站把同一件事收成四步：寫下完成標準、往上走一步、用工具證明、把學到的東西折回去。文件裡的攀爬狀態另有一組名字（Traverse、Marking、Ascending 等）。新鮮安裝時畫面上到底走哪一組名稱，尚未確認。人可以用白話把力氣加大或縮小。

### Cortex 記憶

Cortex 是 LifeOS 的記憶產品名（路徑仍是 harness 設定樹裡的 `LIFEOS/MEMORY/`）。文件描述：工作中用 hook 寫下、休息時整理，內容包含熱層事實、有類型的知識（人、公司、想法、研究）、學習與工作紀錄。檢索走檔案與搜尋，不是先建一個獨立向量資料庫。它服務的是「現在到底在哪」，好讓 ideal state 的差距對準現實。和 [gbrain](https://github.com/garrytan/gbrain)、[Hindsight](https://github.com/vectorize-io/hindsight) 不同：那些比較像可以單獨部署的記憶層；Cortex 綁在「這個人的 LifeOS」上，跟身份與完成定義一起載入。跨 session 能不能真的找回一筆決定，尚未確認。

### 一整包 skill，裝進既有 harness

從 v6 起，公開發行把系統收成一個 skill 目錄（`LifeOS/`）：編排、Algorithm、hooks、文件，以及一整庫會自己被觸發的技能。官方說技能很多，這裡不逐條列。和 [gstack](https://github.com/garrytan/gstack) 不同：gstack 比較像一組各自上場的員工劇本；LifeOS 把技能收成同一位 DA 能用的動作，重點仍是「這層知道你是誰、做完是什麼」。

### Pulse：看系統在跑的儀表

Pulse 是可選的 Life Dashboard，一個本機 daemon（文件寫 port 31337），用來看目前在做什麼、記憶與子系統是否健康，以及各次攀爬的進度。macOS 另有選單列 app；Linux 文件寫 systemd user unit。它是觀測表面。沒有 Pulse，文件說 LifeOS 仍然是 LifeOS。我們沒有打開過這個畫面。

### USER 與系統分開

系統檔（會公開發行的程式與範本）和使用者資料分開。文件寫 Claude Code 佈局裡，`~/.claude/LIFEOS/USER/` 連到 `~/.config/LIFEOS/USER/`。安裝是加法：缺的才補，不蓋掉已有個人檔。其他 harness 的設定根目錄要靠安裝時偵測，不應假設一定是 `~/.claude`。升級是否真的從不碰到 USER，尚未確認。

## 主打賣點

- 它想被記住的是：harness 負責執行，LifeOS 負責「你是誰」和「怎樣算做完」。官方比喻是引擎與車。我們的判斷是，它坐在 harness 上面，不是另一間辦公室，也不是另一套 ADE。
- 和只裝 skills 的做法不同：技能很多，但是包在同一套身份、TELOS、ISA 和記憶裡。和獨立記憶產品不同：記憶用來量現況，好讓攀爬有起點。
- 舊能力的新包裝：hook、skill、subagent、狀態列、本機 dashboard，coding harness 本來就有類似物。LifeOS 把它們收成同一套「現況 → 理想、沒有證據不算完成」的循環，並給助手一個持久身份。Euphoric Surprise（要讓人喊出聲的那種好）是他們自己定的品質尺，尚未驗證它會不會變成可操作的檢查，還是口號。
- Harness-agnostic 是設計目標。安裝文件同時承認：完整 always-on 行為今天以 Claude Code 為準。這點不該寫成「任何 agent 裝上就一樣」。

## 使用情境

### 把同一個助手裝進已經在用的 coding harness

- 適合誰：已經用 Claude Code（或其他文件點名的 coding harness），不想換 runtime，但厭倦每輪重講自己是誰、要怎樣才算做完的人。
- 在什麼情況使用：寫程式、研究、寫作或生活目標，希望同一套完成定義跟著走。
- 帶來的價值：身份、目標和「做完」留在 harness 旁邊；升級文件承諾不改你的 USER。實際手感尚未確認。

### 一件工作要先寫完成標準再動手

- 適合誰：不信任「看起來做完了」的人。
- 在什麼情況使用：任務模糊，容易做出一堆沒有對過標準的產出。
- 帶來的價值：ISA 把完成寫成可被工具打臉的條件；Algorithm 規定沒有證據不能結案。人可以用白話加重力道，或事後把 ISA 打開再爬一輪。

### 看一位私人助手有沒有往你的目標靠近

- 適合誰：想要一位記得你的 DA，而不是一間多員工公司的人。
- 在什麼情況使用：想看現況和理想差多遠、這輪攀爬關了哪些條件、記憶有沒有變厚。
- 帶來的價值：Pulse 把這件事放到一塊儀表上。它是 dashboard，不是你走進去工作的房間。公開安裝裡 DA 會不會自己心跳、工作會不會穩定寫進私人 GitHub issue，文件對部分元件標成不進公開包，尚未確認。

## 我們可以學什麼

LifeOS 比較像一位私人員工，加上這位員工和你共用的記憶與完成尺，裝在別人的 harness 裡上班。它不是一間公司辦公室（沒有 org chart），也不是我們要做的 2D／3D workspace。Pulse，以及文件裡的 agents 看板，比較接近 Conductor、Maestro 那種 ADE dashboard：拿來看進度，不是空間本身。

組織方式是三層，不是任務看板優先：TELOS 是人生目標，ISA 是這一輪的完成定義，Algorithm 是往上爬的動詞。工作紀錄文件想收到私人 GitHub issue，再讓 Pulse 的 Work 分頁去讀；那是帳本，不是房間。一位 DA 當門面，平行 agent 是這次攀爬借來的手，做完就散。委派文件本身已標成退役，公開安裝怎麼分派，尚未確認。

Context 從身份與 TELOS 灌進工作階段；憲法層要靠專用啟動（把 system prompt 檔附上去），普通 `claude` 不會載入。記憶走 Cortex，hook 寫入、需要時再取回。Handoff 的單位是 ISA 與工作紀錄，不是把整段 chat 丟給下一個人。權限沿用宿主 harness：hook 用程式擋工具呼叫，本機服務文件寫綁 loopback。LifeOS 沒有自己的沙箱產品。人的介入點是：安裝前逐項同意、用白話調力氣、看證據才讓條件結案、Doctor 看哪些外掛能力是活的、壞的或你拒絕的。

- **在 2D workspace 裡會變成什麼**：一層個人樓層的平面圖。正中央是一位有名字的 DA 坐在自己的位子，不是一排匿名 terminal。牆上一塊板子左右對照 Current State 和 Ideal State，TELOS 是這層樓的長期目標，正在爬的 ISA 是桌上那張紙。Cortex 是位子旁邊的檔案櫃：開工前先抽資料夾，而不是先問人。平行中的研究、建造、驗證是同一層樓的幾張桌子，人走過去就能看到哪張桌子在忙。Pulse 是牆上的螢幕，像 ADE dashboard 那樣報狀態，地板本身仍是這個人的工作區。USER 是上鎖的私人抽屜，系統升級只換公共區的工具，不重排你的家具。
- **在 3D workspace 裡會變成什麼**：一間走得進去的私人辦公室，像 [Agent Office](https://github.com/AgentSystemLabs/agent-office) 那樣看得到誰在場、在哪裡做事。門口是這位 DA，桌上攤著 ISA，每一條完成條件是一張紙，工具拿出證據才蓋章。旁邊檔案室是 Cortex，DA 進去拿人、決定或學習再回來講話。臨時 agent 只在這次攀爬時出現在側桌，結束後離開，不會變成常駐部門。人走進房間可以改理想狀態、退回一張沒有證據的紙，或比較兩次攀爬。牆上螢幕才是 Pulse；那塊螢幕不是辦公室。這不是把知識圖譜做成可旋轉的立體物件。
- **不值得照搬的地方**：不要把整庫 skill 搬進空間裡變成一堆員工。不要把 Euphoric Surprise 當已驗證的指標。不要把 harness-agnostic 講得比安裝文件更滿。私人 DA 的記憶是一個人的，不能直接當成整間辦公室的共用記憶；團隊共用仍要另看 gbrain、Hindsight 那一類。Pulse 的看板可以當牆面儀器，不要把它叫成我們要蓋的 workspace。

## 初步看法

- 最有價值的部分：把「你是誰」和「怎樣算做完」做成 harness 上面的一層，讓技能和記憶都服務這次攀爬，而不是再堆一個 dashboard 或再發一包技能。
- 最大限制或疑問：公開文件和公開安裝不一致的地方不少（DA heartbeat 實作、部分工作捕捉 hook 標成不進公開包；其他 harness 的 always-on 尚未接上）。Claude Code 路徑文件最完整，其餘路徑的實際行為尚未確認。我們沒有跑過安裝，也沒有看過 Pulse。
- 是否值得進一步研究或親自體驗：值得。下一步若要動手，應在 repo 外的目錄安裝，只驗證三件事：interview 之後新工作階段是否帶著 TELOS、一條 ISA 條件是否真的要工具證據才結案、USER 在升級後是否原樣留下。

## Sources

- Official website：[https://ourlifeos.ai](https://ourlifeos.ai)
- Repository：[https://github.com/danielmiessler/LifeOS](https://github.com/danielmiessler/LifeOS)
- Documentation：[https://docs.ourlifeos.ai](https://docs.ourlifeos.ai)
- License：[MIT](https://github.com/danielmiessler/LifeOS/blob/main/LICENSE)
- Install：[https://ourlifeos.ai/install](https://ourlifeos.ai/install)（對應 repo `LifeOS/INSTALL.md`）
- Getting started：repo `LifeOS/GETTING-STARTED.md`
- 撰寫時讀過、沒有執行產品的文件：Core Components、Algorithm、Cortex／Memory、Pulse、DA subsystem、System／User Boundary、ISA、Skills、Work、Security model
