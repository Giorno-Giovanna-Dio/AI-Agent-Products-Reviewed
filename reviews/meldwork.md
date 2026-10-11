# Meldwork

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-meldwork-cell-80a8/reviews/meldwork.md)
>
> Cell ID：[https://github.com/Ryder-Sun/Meldwork](https://github.com/Ryder-Sun/Meldwork)
>
> Status：`untried`
>
> Category：本機多 Agent 編排桌面（ADE）
>
> Last updated：2026-10-10

## 產品介紹

Meldwork 是一台本機優先的 Electron 桌面程式，目前公開預覽針對 Apple
silicon macOS。它偵測你已經裝好的 coding agent CLI（Codex、Claude Code、
Gemini CLI、OpenCode 等十多個），把它們放進同一個案子裡一起工作。人先
選參與者、圈定目標與工作目錄、權限和上下文，再選一種跑法：一個人做、
同一份任務讓多人各答一次，或進行多輪討論。結果要經過人採用，工作區才
會改。儲存庫已從 `Ryder-MHumble/Meldwork` 轉到現在的網址。尚未安裝或執行。

它比較像 **ADE dashboard 加上一份案子紀錄**。員工是外面那些 CLI；Meldwork
自己是這間房的規則、凍結的任務紙，以及採用前的閘門。官方把這台程式叫做
work cell：邊界在本機行程和本機資料，不在一張可以走進去的樓層。和
[Conductor](reviews/conductor.md)、[Emdash](reviews/emdash.md) 同一層，
都是編排既有 CLI 的控制台。它沒有把人放進一間辦公室。

## 主要 Features

### 人點名，再選三種深度

參與者永遠由人選定。從更大的名單自動組隊，公開文件寫明不在目前預覽裡。

- **Direct**：一個 Agent 接著做。支援時會留著它自己的原生 session。
- **Concurrent Responses**：選中的 Agent 收到同一份凍結任務，各自作答，
  屏障之後依穩定順序交卷。用來在選路之前比較做法。
- **Auto Discussion V4**：公開 README、`architecture.md`、桌面文件和
  0.1.4／0.1.5 changelog 把它寫成預覽已有的多輪流程：先平行提案，再質疑、
  協商同一份職責，依依賴做事，約定的那一位才是整合時的寫入者，其他人獨立
  覆核。Harness 只驗證大家同意的同一份計畫，不在模型外面私下派工。

同 repo 的 `docs/harness-engine-strategy.md` 開頭註明那是長期選項，不代表
已交付，並寫完整的提案場、職責契約、Outcome Network 仍未交出，持久化中心
仍主要是對話。兩套文件對「V4 做到哪裡」不一致。畫面是否真的走完這些階段，
尚未確認。

### 先凍結同一張任務紙

群組並行時，任何 Agent 拿到排程之前，任務快照和投遞計畫就先凍結。後發言
的人在屏障打開前看不到先發言的結論。這是為了讓比較成立：大家看的是同一份
brief，不是互相改寫過的版本。換工作目錄會清掉原生 session，避免把上一個
目錄的對話接到新目錄。重新命名對話則保留 session。

上下文可以帶本機 Skill、附件，以及點名的知識來源（文件寫到 Obsidian 由
主行程唯讀抓取並做成快照；飛書、釘釘仍走各 CLI，權限不由 Meldwork 管）。
這些是這次任務的範圍，不是一份跨案子的共用記憶。

### 採用是人的動作，寫入預設關著

工作區寫入是選配的流程開關。公開說法是：沒有人採用，檔案不會自己改。
V4 的描述裡，提案、質疑、執行、覆核維持唯讀；開啟寫入後，約定的 finalizer
是唯一的整合寫入者。未知的可寫結果在重試前要再過一次 Human Gate。可寫的
Agent 重開機後不會自動接著跑。

官方同時寫明：這不是作業系統沙箱。唯讀有多少效力，仍部分取決於上游 CLI
是否遵守自己的權限旗標。Codex 有環境變數可選 `read-only` 或
`workspace-write`，未設定時用唯讀。

### 發現、證據、決定要留在案子上

公開網站與 README 用同一條鏈描述每筆結果：Finding → Evidence → Decision →
Disposition。異議要留著，不能被一個中央經理收成「大家同意了」。架構上，
Artifact、Evidence、Finding、Adoption 和診斷用的工具摘要分開存；Human
Gate 存的是權限、預算和決定請求，以及人最終怎麼判。

策略文件則說目前只有有限的 evidence capsule，完整的協議物件（含
OutcomeReceipt）尚未交付。changelog 0.1.5 寫任務完成、單次呼叫成功、人的
採用是分開記的。三者對「畫面上是可點的紀錄，還是對話旁的摘要」沒有對齊。
尚未確認。

### 主行程守門，運行中斷要看得見

渲染行程拿不到執行檔路徑、憑證、任意 shell。本機 JSON 存在 Electron 使用者
資料目錄；Provider 金鑰用作業系統的 safeStorage。Run Ledger 在停機或當機
後把未結束的運行標成 interrupted，可恢復的終態再補回對話。完成的人和失敗
的人可以同時留在畫面上。排程容量不夠時，多出來的 Agent 保持排隊，而不是
從這一批裡消失。

本機資料留在這台電腦上。被選中的 Agent 仍會把提示、附件或 Skill 送到它自己的
Provider。沒有帳號，也沒有必備的應用伺服器。Cloud／Channel connector 在架構
裡有契約，正式建置預設不註冊。對話在本機，但沒有應用層加密。

## 主打賣點

- **同一張任務紙上的獨立判斷，加上人採用之前不能改檔。** 官方最想被記住的
  是這條，而不是「可以同時開很多終端機」。
- **跨 CLI，人指定誰上場。** Claude Code 自己的 Agent Teams 留在一家 CLI
  裡面。Meldwork 把 Codex、Claude Code、OpenCode 等放在同一個案子。和
  [CrewAI](reviews/crewai.md) 那種要自己寫編排的框架相比，它是開箱的桌面
  流程。
- **異議留在紀錄裡。** 策略文件把「中央 Planner 藏起落選方案」寫成明確不做。
  系統管屏障、預算、權限、排程、稽核和恢復；Agent 提方案和質疑；人選擇並
  採用。

並排看終端機、接多個 CLI、留原生 session、帶 Skill 和附件，在其他 ADE 裡
已經常見。[Conductor](reviews/conductor.md) 用 git worktree 把平行工作隔開；
Meldwork 公開比較表把自己放在「證據與採用」，而不是 worktree 吞吐量。這份
差異目前是官方定位。我們沒有跑過，不能把它當成已驗證的手感。

## 使用情境

### 同一份審查，先看三家各說什麼

- 適合誰：已經同時用兩種以上 coding CLI、要在改碼前先對齊看法的人。
- 在什麼情況使用：審 PR、看一個 bug、比較兩種做法。人點名 Codex、Claude
  Code、Gemini CLI，凍結同一份任務。
- 帶來的價值：不用在終端機之間複製上下文。三份判斷和反對意見留在同一個
  案子，人只採用點頭的那份。

### 一個人做完，session 還在

- 適合誰：這次只需要一個 Agent 的人。
- 在什麼情況使用：目標清楚、不需要第二意見。Direct 接著該 CLI 自己的
  session（支援時）。
- 帶來的價值：換視窗或重開之後還能接上同一次工作；工作目錄一換，舊
  session 會清掉，避免接錯專案。

### 多輪討論，但上場名單仍是人定的

- 適合誰：願意多花一輪，讓提案被質疑再分工的人。
- 在什麼情況使用：風險夠高，值得先提案、再協商誰寫、誰覆核。
- 帶來的價值：職責要先講清楚，寫入者只有一位。若 V4 的階段在畫面上並沒有
  做成可檢查的物件，這個情境的價值就還停在文件。尚未確認。

## 我們可以學什麼

- **值得借鑑的 product idea**：一個案子一張凍結的任務紙。每位上場的員工
  先獨立作答，屏障打開後異議仍留在本人身上。採用是人的蓋章，Agent 自報
  「做完了」不能代替。系統負責門、預算、順序和恢復；員工負責判斷。預設
  一個人做，要比較時才叫 2–4 個獨立意見。
- **值得借鑑的 interaction / workflow**：選人 → 圈範圍 → 跑 → 檢視並採用。
  並行時先凍結再放行，交卷順序穩定。排隊的人留在場上。中斷後人還在，狀態
  變成暫停；可寫的工作不會自己續跑，未知的寫入結果要再問一次人。換專案
  目錄就拆掉舊 session。
- **在 2D workspace 裡會變成什麼**：平面樓層上，一個案子是一間房。牆上釘著
  同一張任務紙。被點名的 CLI 各坐一張桌子，屏障打開前桌子背對背，看不到
  彼此的紙條。打開後，紙條貼到房間中央的長桌，分成發現、證據、決定、處置，
  反對意見不收進某一張總結。唯一能改檔案櫃的是約定的那位寫入者，其他人的
  桌子沒有筆。人走到採用桌蓋章，檔案櫃的鎖才開。沒被點名的 CLI 站在房外
  等候區，不會自己走進來。排程滿了的人站在隊列裡，人看得到誰還沒開始。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室，像
  [Agent Office](reviews/agent-office.md) 那種「看見誰在場、在哪裡做事」。
  你走進這間案子，看見有人在提案、有人拿著反對意見站著、只有一張桌子的人
  可以碰到檔案櫃、另外幾個人在覆核。Human Gate 是檔案室門口：沒有你的蓋章，
  寫入者也進不去。重開之後，未完成的人還坐在原位，牌子改成暫停，而不是從
  樓層消失。落選方案留在那個人的桌上，不會被一位經理收走。Meldwork 現在的
  Electron 視窗是這間辦公室的規章和檔案櫃規則，本身還不是這間可走的房。
- **不值得照搬或需要重新設計的地方**：把 Electron 側欄叫做 workspace，會
  和我們要的樓層搞混；控制台可以留著，空間要另做。寫入開關要在門上寫清楚
  它依賴哪一家 CLI 的旗標，因為官方自己說這不是作業系統沙箱。策略文件裡的
  自動選人、責任市場、Outcome Network 還沒交付，地板上不要先畫一個勞動市場。
  公開 README 與策略文件對 V4 完成度互相矛盾，辦公室只該畫我們能指認的階段。
  預覽只針對 Apple silicon、ad-hoc 簽名且未公證，不能把它的安裝體驗當成
  跨平台工作空間的範本。

## 初步看法

- 最有價值的部分：凍結同一份任務、獨立判斷、異議留在本人身上、採用權在人。
  這四件事可以直接變成樓層上的任務紙、背對背的桌子、不收走的反對意見，和
  檔案室門口。
- 最大限制或疑問：它是 ADE dashboard，員工是外部 CLI。證據鏈在公開頁寫成
  日常物件，在策略文件裡仍是有限摘要，尚未確認畫面上哪一種是真的。寫入
  保護不是沙箱。GitHub 的 `LICENSE` 與 `COMMERCIAL_USE.md` 是 Apache-2.0；
  README 與官網仍寫 Meldwork Non-Commercial Source License 1.0。哪一份才是
  目前要遵守的授權文本，尚未確認。對話存在本機但沒有應用層加密；本機優先
  仍會把內容送到各 Agent 的 Provider。
- 是否值得進一步研究或親自體驗：值得，但只為了核對一件事：V4 的提案、質疑、
  單一寫入者、採用紀錄，在畫面上是走得進去的物件，還是對話旁邊的說明。
  文件已經夠寫出空間隱喻；手感要等實機。

## 後續補充（選填）

Not tried yet。官方 README、架構、changelog 與策略文件已足夠回答 workspace
要的問題：誰被點名、任務紙何時凍結、誰可以寫、異議留在哪裡、中斷後人還在
不在。策略文件與公開產品頁對 V4 完成度的落差，留待實測再補。

## Sources

- [Repository](https://github.com/Ryder-Sun/Meldwork)
- [先前網址 Ryder-MHumble/Meldwork](https://github.com/Ryder-MHumble/Meldwork)（現在轉到上面的網址）
- [Official site](https://ryder-sun.github.io/Meldwork/)
- [Architecture](https://github.com/Ryder-Sun/Meldwork/blob/main/architecture.md)
- [Desktop README](https://github.com/Ryder-Sun/Meldwork/blob/main/desktop/README.md)
- [Changelog](https://github.com/Ryder-Sun/Meldwork/blob/main/CHANGELOG.md)
- [Harness engine strategy（文件自標為長期選項，不代表已交付）](https://github.com/Ryder-Sun/Meldwork/blob/main/docs/harness-engine-strategy.md)
- [Product strategy](https://github.com/Ryder-Sun/Meldwork/blob/main/docs/product-strategy.md)
- [LICENSE](https://github.com/Ryder-Sun/Meldwork/blob/main/LICENSE)
- [COMMERCIAL_USE.md](https://github.com/Ryder-Sun/Meldwork/blob/main/COMMERCIAL_USE.md)
