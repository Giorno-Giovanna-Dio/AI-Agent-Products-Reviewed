# Claude Code Bridge

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-claude-codex-bridge-cell-c2d2/reviews/claude-codex-bridge.md)
>
> Cell ID：[https://github.com/SeemSeam/claude_codex_bridge](https://github.com/SeemSeam/claude_codex_bridge)
>
> Status：`untried`
>
> Category：可見的多 agent CLI 工作台
>
> Last updated：2026-10-09

## 產品介紹

Claude Code Bridge（指令與簡稱都是 CCB）是一個輕量的多 agent 終端工作台。它把已經裝在機器上的 Codex、Claude、Gemini 和其他 CLI agent 掛進同一個專案的可見畫面：人可以同時看到他們、坐進某一格直接打字，agent 之間也能用同一套訊息把工作交出去，再把結果交回原來的對話。

一個專案以目錄裡的 `.ccb` 為錨點，背後有一個常駐的 `ccbd`。前台關掉之後，專案狀態還在。空白專案預設只開一扇 `main` 視窗、一位名叫 `demo` 的 agent，並挑機器上第一個可用的 CLI（順序是 Codex、Claude、Gemini，然後其他）。要同時看很多人，得在設定裡把視窗和分格排好。Linux、macOS、WSL 的畫面後端是 tmux；原生 Windows 測試版改用 Herdr。這份筆記只讀文件與設定契約，沒有實際跑起來。

## 主要 Features

### 看得見的原生終端

互動式 CLI 被放進真正的終端分格，邊框顯示設定裡的 agent 名字。側欄列出視窗、agent、活動和最近的通訊。人可以在任何一格直接打字。設定契約把服務型 provider（例如 DeepSeek Harness 的 `dsh`）寫成受管的 host／log 畫面：請求走它自己的介面，那個格子負責生命週期和日誌。

### 人與 agent 共用的佇列

每個目標 agent 有一條依時間排隊的信箱。別人的 `ask` 和交回來的結果都要等這一輪做完，後面的回覆排在後面；不同 agent 仍可同時工作。人正在輸入時，進來的訊息會先等：輸入框空了才投遞；有草稿最多等 180 秒，時間到會清一次草稿再送。忙碌、選單或認不出輸入框時則繼續等，不清、不送。文件寫明這套草稿保護目前建議在受管 tmux 裡用 Claude、Codex、OMP 測試，其他 provider 還不能當成同樣會保護草稿。截止清草稿時會用 Ctrl-C，若人同一瞬間也在打字，仍可能被打斷。

### 交接：ask、chain、silence

人用 `/ask <agent>` 或 `ccb ask` 把任務交給具名 agent。需要對方的結果才能繼續時用 `--chain`：子任務完成後，結果會當成續作送回原來的 agent。不需要把成功結果帶回來時用 `--silence`。進行中的修正用 `ccb followup` 打進已經在跑的那一輪；文件寫 Codex 在受管 TUI、且已綁定 thread 與 turn 時才接受，Claude 分格和其他沒有對應原語的 provider 會明確拒絕。`ccb watch`、`queue`、`inbox`、`trace` 用來看 job、信箱和整條通訊。取消、重試、重送都留下可追溯的紀錄。

### 工作目錄、對話與權限邊界

每位 agent 有自己的 provider、工作目錄、權限、恢復方式和可選角色。工作目錄可以是專案根目錄、git worktree、外部路徑，或幾位 agent 共用的 worktree 群組。Claude 與 Codex 的受管對話用獨立的 home，和人平常自己開的 Claude／Codex 對話分開；工作目錄是上下文，身份是專案裡的 agent 名字加上 provider 與對話編號。設定裡看得到 `permission` 欄位，使用手冊的例子是 `manual`；`ccb -s` 只管 provider 的自動權限。專案設定若自訂啟動命令模板，要另外用 `ccb config approve-commands` 核准，核准紀錄放在使用者狀態目錄。完整權限枚舉這次沒有逐項核完，尚未確認。

### 共用記憶與角色包

`.ccb/ccb_memory.md` 是全專案共用的協作筆記：規則、限制、交接慣例放這裡。可選的角色包（另一個 repo 的 Agent Roles Spec）把技能、記憶和工具依賴包成可安裝、可掛上、可移除的角色，例如架構審查。同一個角色可以在專案裡開兩個實例，之後必須用實例名字來 ask。設定契約還允許依容量在一輪工作裡臨時掛出額外 agent；文件寫這些人要等迴圈命令確保之後才出現。這部分尚未實測。

### 手機遙控與 Rich 檔案面

Android app 是遠端控制器：切換專案、視窗和 agent，看對話、傳文字、開終端、傳檔。閘道預設只聽 loopback；區網要綁一個私有介面；遠端走 Tailscale Serve。配對檔案決定手機拿到的範圍，例如觀看、內容、終端、上傳、下載。Rich 模式用 WezTerm 在終端裡瀏覽檔案、預覽，並可用系統預設程式打開。設定面板只綁 loopback，token 放環境變數或權限收緊的檔案。

## 主打賣點

- 它想被記住的是：多家 CLI 同時出現在一個畫面裡，彼此用同一套 ask／信箱交接，人還能隨時坐進某一格。
- Nimbalyst 和 Maestro 是較重的桌面 ADE，帶著看板、視覺文件和桌面審閱。CCB 的表面停在終端分格和側欄，多出來的是信箱、佇列，以及把結果送回原對話。
- 空白專案先是一位 `demo`。多 agent 佈局、角色包、手機和 Rich 是之後加上的能力。分格本身來自 tmux 或 Herdr；CCB 加上專案級 daemon、具名 agent、佇列和交接。

## 使用情境

### 同一專案裡看幾位 CLI 同時做事

- 適合誰：已經在用 Codex、Claude 或其他 CLI，想在一個終端裡看他們的人。
- 在什麼情況使用：一個功能要有人寫、有人審，或同一 repo 用 worktree 分開寫。
- 帶來的價值：每個格子是對方原本的終端，人看得到進度，也能直接打字接手。

### 讓一位 agent 問另一位，結果回到原對話

- 適合誰：希望 agent 互相派工，自己不用當傳話的人。
- 在什麼情況使用：審查、查證或長報告要交回正在做的那一輪。
- 帶來的價值：`--chain` 把結果送回呼叫者；`--silence` 讓獨立檢查自己跑完。事後可以用 `trace` 把 job、訊息和回覆串起來。

### 離開座位仍能看和插話

- 適合誰：專案 daemon 開在一台機器上、人會離開鍵盤的情況。
- 在什麼情況使用：手機查看對話、傳一句話，或傳檔。
- 帶來的價值：前台關掉專案還在；手機權限跟配對檔案走，閘道預設留在本機。實際連線手感尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：協作單位是「具名 agent」，provider 是他背後的執行系統。同一個角色開出兩個實例時，問話必須點名到唯一的那一位。
- 值得借鑑的 interaction / workflow：人正在寫的草稿有優先權，進來的訊息在托盤裡等；忙碌或選單時不硬送。交接可以選擇把結果送回原對話，或做完即可。取消和修正留下軌跡。
- 在 2D workspace 裡會變成什麼：一層樓的平面辦公室。每個視窗是一間房，每位 agent 是一張桌子，桌上是他真實的終端。側欄是這層樓的平面圖：誰在哪一間、最近在動、桌上最後幾張紙條。共用記憶是房間白板。git worktree 是另一張桌子上的另一疊紙。人在某張桌子打字時，送來的紙條先放在托盤。人可以坐下直接用那台鍵盤。關掉看樓層的畫面之後，桌子上的工作還在。
- 在 3D workspace 裡會變成什麼：一間走得進去的辦公室。掛上的 agent 是坐在位子上的人，你看得到誰在場。互動式 CLI 是你可以站在身後看螢幕、再坐上椅子接手的同事。服務型 provider 是角落一台帶著日誌窗的機器，你看它的運轉紀錄。`ask` 是把紙條送到對面桌子；`--chain` 是對方做完把答案走回來，原來的工作接著做。取消是叫他停手，牆上仍留著這張紙條的經過。手機是從別的地方看進同一間辦公室、說一句話，範圍由配對時發給的鑰匙決定。
- 不值得照搬或需要重新設計的地方：草稿保護和 followup 跟特定 provider 的畫面綁在一起，換一家 CLI 行為就不同。辦公室裡要讓「這張桌子會不會保護你正在寫的字」變成看得見的狀態。用 Ctrl-C 清草稿和人的打字搶同一個鍵，接手時要標明現在是誰在打。專案自訂啟動命令需要另行核准，這個邊界值得留；`permission` 的完整選項尚未確認，不能把手冊裡的 `manual` 當成全部。授權是 AGPL-3.0，若嵌進閉源工作空間要另外處理。

## 初步看法

- 最有價值的部分：可見的原生終端，加上專案級信箱，讓人看、等、接手，並把結果送回原對話。
- 最大限制或疑問：尚未執行。多 provider 的草稿保護、Codex 以外的 followup、權限枚舉、手機配對的實際範圍，都還沒有親手確認。Windows 路徑是 beta，且依賴 Herdr。
- 是否值得進一步研究或親自體驗：值得。下一步應只跑一個本機專案，看兩格並排、一條 `--chain`，以及人正在打字時訊息會不會等。

## 後續補充

Not tried yet.

## Sources

- Repository：https://github.com/SeemSeam/claude_codex_bridge
- Package：`@seemseam/ccb`（`package.json` 與 `VERSION` 標示 8.7.8；此版本未經本 Cell 執行）
- User guide：https://github.com/SeemSeam/claude_codex_bridge/tree/main/docs/manuals/user-guide
- Layout and config contract：https://github.com/SeemSeam/claude_codex_bridge/blob/main/docs/ccb-config-layout-contract.md
- Session isolation：https://github.com/SeemSeam/claude_codex_bridge/blob/main/docs/claude-session-isolation-contract.md
- Related role spec（另一個 repo，不是這個 Cell）：https://github.com/SeemSeam/agent-roles-spec
