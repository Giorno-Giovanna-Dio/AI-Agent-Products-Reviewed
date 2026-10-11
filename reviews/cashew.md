# Cashew

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cashew-cell-ca43/reviews/cashew.md)
>
> Cell ID：[https://github.com/rajkripal/cashew](https://github.com/rajkripal/cashew)
>
> Status：`untried`
>
> Category：Agent 記憶（個人思考圖）
>
> Last updated：2026-10-10

## 產品介紹

Cashew（PyPI 套件名 `cashew-brain`）是一份給已經在跑的 agent 用的持久記憶。作者是 Raj Kripal Danday。記憶是單一 SQLite 檔：想法是節點，衍生關係是邊，向量也放在同一個檔裡。人用 Claude Code、OpenClaw，或 repo 以外的 Hermes 外掛來查它、寫它。它不雇用員工、不排任務、也不提供執行環境。

本次只讀 GitHub 上的 README、LICENSE、哲學、架構、睡眠協議、CLI 與 skill，沒有安裝。套件版本在 `pyproject.toml` 與 tag `v1.2.1` 是 1.2.1；`main` 在該 tag 之後還有 24 個 commit，最新一筆是 2026-10-10 的「用 Completed 筆記關掉承諾」。下面凡是碰到這段差距，都標成尚未實測。

## 主要 Features

### 有界的回憶

查詢先用向量找幾顆種子，再沿著邊做深度有限的走訪，把一小疊相關節點帶回這次對話。作者在 PHILOSOPHY 寫，自己那顆腦每次主題查詢大約是幾百字，圖變大時這份量仍大致不動。那是作者對自己資料的說法，尚未重現。架構文件同時寫明：種子掃描會隨節點數變長，走訪本身才有上限。超過大約十萬個向量時，文件說種子掃描需要真正的近鄰索引。

### 從對話抽出節點

值得留下的決定、觀察、洞察、信念、事實、承諾，經 LLM 抽成節點。類型是給後來閱讀的提示。1.1.0 的 changelog 寫：引擎不再用類型做過濾或加分，信心分數欄位也拿掉了。修正應抽成新節點。skill 要求留下模式與理由，略過逐字稿、短暫狀態和程式碼。領域分成「這個人的知識」和「agent 自己的操作經驗」，名稱可在設定裡改。

### 隱私標籤

敏感內容用 `vault:private`。在團體場合查詢時，skill 要求排除這個標籤。我讀到的抽取程式只在呼叫端傳入 tags 時才寫標籤，資料表預設是空字串。架構文件寫「預設標成私人」，和這段程式不一致。`--tags` 與 `--exclude-tags` 寫在 `scripts/cashew_context.py`；主入口 `cashew_cli.py` 的 context／extract 參數表沒有它們。尚未執行，所以裝好的 `cashew` 指令吃不吃這兩個旗標，尚未確認。另有 `scripts/declassify.py` 會列出超過 7 天、尚未褪色的私人節點。它會不會直接改標籤，這次只看到候選查詢，尚未確認。

### Think、Sleep、釘選、完成筆記

Think 在閒時走一輪，找跨領域連結，或專看彼此拉扯的內容（CLI 有 `general` 與 `tension`）。Sleep 做整理：交叉連結、把沒被用到的標成褪色、合併近似重複、把常被取用的升成永久。人也可以 `pin` 一個節點，讓它跳過遺忘。

`main` 上 2026-10-10 的提交讓 sleep 多一個收尾：筆記若以 `Completed:` 開頭，前言點名的那個 commitment（未永久、也不是另一張完成筆記）會被標成褪色，之後不再被當成未完成工作。前言若寫成嘗試、部分、仍開啟、被擋住，就什麼都不關。沒有新的邊或欄位。這是讀提交說明，尚未跑過。

### 接在別人的 agent 上

Claude Code 的 structural mode 用 hook 在每次實質提問時查一次，注入的內容標成待核對的線索，不是事實。閒聊短句不注入。查詢失敗就什麼都不塞，也不擋住這一輪。OpenClaw skill 在上下文即將被壓縮前，先把重點抽出來再清桌。Hermes 走 repo 外的外掛 `hermes-cashew`。`cashew serve` 把嵌入模型留在本機 unix socket，避免每次查詢都重載。抽取和 think 需要外接 LLM；檢索走本地模型。

程式裡的預設嵌入模型是 `thenlper/gte-large`。README、架構文件和 `config.example.yaml` 仍寫 MiniLM。新安裝會落到哪一個，尚未確認。

### 看圖的畫面

`cashew dashboard` 在瀏覽器畫力導向圖，並用動畫顯示搜尋怎麼一跳一跳走。預設聽在本機 `127.0.0.1:8765`。那是看記憶內容的儀表。CLI 另留著 `complete-context`（dfs、hierarchical、breadth_first）和 `complete-sleep`（說明文字仍寫階層演化）。架構文件則說不要合成階層，結構靠連結和遺忘長出來。兩套說法都在 repo 裡，哪一條是現在的主路徑，尚未實測。

## 主打賣點

- 它要人記住的是：同一個人、同一段工作關係的證據會留下來；沒再被用到的會淡掉；每次帶進對話的份量有上限。哲學寫成「圖很笨，推理在上面」：邊沒有語意，節點類型不加分。
- 和 [gbrain](https://github.com/garrytan/gbrain)、[Hindsight](https://github.com/vectorize-io/hindsight) 一樣，都是記憶層，不是辦公室，也不是 agent 框架。Cashew 比較特別的整理方式，是睡眠班的遺忘，以及用一張 `Completed:` 筆記讓承諾退出「還開著」的那疊。
- 力導向 dashboard、以及仍寫著階層的 complete 指令，是查看和舊路徑。它們不是這份記憶的組織方式本身。

## 使用情境

### 新的一輪先查再答

- 適合誰：已經用 Claude Code 或 OpenClaw，討厭每次重開都失憶的人。
- 在什麼情況使用：要回答先前的決定、偏好、人際或專案脈絡。
- 帶來的價值：員工進門先拿到一小疊線索，而不是靠模型的通則猜這個人。

### 上下文要被清掉之前先交班

- 適合誰：會把長對話壓縮掉的 agent 宿主。
- 在什麼情況使用：OpenClaw skill 在壓縮前把決定、承諾、修正寫進 SQLite。
- 帶來的價值：下一個人坐下時，桌上還有上一班留下的卡片。整段逐字稿不必留下。

### 用完成筆記收掉承諾

- 適合誰：會把待辦抽成 commitment 節點的人。
- 在什麼情況使用：做完後寫一張 `Completed:` 筆記，前言點名那張卡的 ID，再跑 sleep。
- 帶來的價值：未完成清單會自己變短。含糊的「還沒好」不會誤關。這條行為在 `main`，尚未實測。

## 我們可以學什麼

- Agents、tasks、goals：Cashew 沒有編制，也沒有目標。呼叫它的程式才是員工。承諾是檔案室裡的一張卡，不是派工單。
- Context、memory、handoff：一個 SQLite 檔是這個人的檔案室。新回合先查。注入的是帶分數、待核對的線索。上下文即將被清掉之前，先把決定送回檔案室。使用者的架子和 agent 自己的架子分開標，查詢仍可以跨過去。
- Runtime、sandbox、permissions：本機程序加上 unix socket 上的嵌入服務。沒有執行沙箱，也沒有工具權限模型。隱私是標籤。`pin` 是人把一張卡改成永久。
- 進度、artifacts、review：進度是統計、褪色稽核、釘選，以及 think 新寫的節點。沒有審核收件匣。完成不是狀態欄，而是另一張筆記；睡眠班讀了才把舊承諾褪色。
- 人如何委派：人不派工。人說話、釘選、寫完成筆記、決定哪些私人卡可以公開。agent 被要求拿證據質疑說法，而不是順著人稱讚。

- 值得借鑑的 product idea：記憶是一間會遺忘的檔案室。檔案室變大，送到桌上的資料夾厚度仍大致一樣。做完一件事，是寫一張完成筆記，讓舊承諾退出未完成的那疊。
- 值得借鑑的 interaction / workflow：進門先查，而且可以由 hook 強制，不必靠員工記得。抽出來的是決定與修正。交班發生在清桌之前。私人卡在團體場合留在櫃裡。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層。檔案室在一側，員工坐在工位。查詢時，檔案員沿著有限的走道取出一小疊卡片放到桌上。使用者與 agent 是兩排架子，檔案員可以跨排走。私人櫃上鎖；人在共用會議時，檔案員不開那櫃。Think 是夜班，在架子間釘「這兩張可能有關」的紙條，紙條是線索，還沒蓋章。Sleep 在後間把沒人再看的卡片褪色、把重複的併成一張、把常被拿的放進永久抽屜。櫃檯上的 `Completed:` 筆記讓睡眠班把對應承諾從「還開著」的那疊拿掉。人在樓層上看得見檔案員、夜班和後間工人各自站在哪裡做事。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走進去看得到誰在哪：工位上的 agent、檔案室走道裡的查詢員、在架子間移動的 think、後間的 sleep。交班是有人把卡片從即將被清空的桌子送回檔案室，下一個坐下的人桌上已經有資料夾。人走到櫃檯看新的連結紙條，決定它是新發現還是回聲。私人庫是一間你看得到有人進去、內容卻不會跟著員工走進會議室的房間。重點是看見誰在場、誰在哪裡做事。
- 需要重新設計的地方：多個呼叫端是來同一間檔案室辦事的人，不是一組被雇用的小隊。架構文件把多個 agent 共用圖譜寫成之後的方向，尚未確認現在有沒有。`cashew dashboard` 的力導向圖和 BFS 動畫是看架子的顯微鏡，不要把它做成這間辦公室，也不要做成可旋轉的立體圖譜。文件彼此還沒對齊的地方（預設嵌入模型、預設隱私、階層指令、主入口缺的標籤旗標）先不要做進空間介面。

## 初步看法

- 最有價值的部分：清桌前的交班、有上限的回憶，以及用完成筆記讓承諾退出未完成清單。
- 最大限制或疑問：它不組織員工與目標。README、架構、skill、CLI 對模型、階層和隱私預設說法不一致。`main` 比已標記的 1.2.1 超前，完成筆記那條還沒進我讀到的 changelog 版本說明。
- 是否值得進一步研究或親自體驗：值得，尤其是交班和完成筆記在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Repository：https://github.com/rajkripal/cashew （無官方網站；homepage 為空）
- License：`main` 的 `LICENSE` 為 MIT（Copyright 2026 Raj Kripal Danday）
- 套件：`pyproject.toml` 名稱 `cashew-brain`，版本 1.2.1；tag `v1.2.1`。`main` HEAD `972ccd773021d1603f919f1fb0d92d3d92e21e2f`（2026-10-10），比該 tag 超前 24 commits
- 已讀：README、PHILOSOPHY.md、docs/architecture.md、docs/sleep-protocol.md、CHANGELOG.md、config.example.yaml、`cashew_cli.py`、`scripts/cashew_context.py`（context／extract 段）、`core/config.py` 的預設模型、`skills/claude-code/SKILL.md`、`skills/openclaw/SKILL.md`、`scripts/declassify.py` 開頭
- 作者文章（未當作功能證據）：https://open.substack.com/pub/rajkripaldanday/p/i-built-my-ai-a-brain-and-it-started
