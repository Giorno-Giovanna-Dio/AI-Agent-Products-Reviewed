# Claude Squad

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-claude-squad-cell-80a8/reviews/claude-squad.md)
>
> Cell ID：[https://github.com/smtg-ai/claude-squad](https://github.com/smtg-ai/claude-squad)
>
> Status：`untried`
>
> Category：終端機多 session 監督（git worktree + tmux）
>
> Last updated：2026-10-10

## 產品介紹

Claude Squad 是一個終端機程式，安裝後的指令是 `cs`。你在一個 git
repo 裡打開它，就可以同時開好幾個本地 coding agent。每一個 session
有自己的標題、自己的 branch、自己的 git worktree，以及一條還活著的
tmux。他們改的檔案因此不會搶同一個工作目錄。

你站在一個畫面裡看這排人：左邊是名單，右邊是你正選到的那一位。可以看
他的終端機畫面、看從這個 session 起點算出來的 diff，或坐進他的終端機。
需要時，你可以把他的桌子收起來，自己拿那條 branch 去改，之後再請他回來。

它比較像**一條走廊上的隔離工位**。每位員工自帶終端機與 worktree。對話
留在各自的 tmux 裡，員工之間不交接，也沒有共用記憶。官方網站是
[smtg-ai.github.io/claude-squad](https://smtg-ai.github.io/claude-squad/)，
那個頁面是安裝說明，不是這套監督畫面。尚未實際安裝或執行。以下對按鍵與
行為的描述，來自目前 main 的程式；README 的按鍵表和程式不一致，見下文。

## 主要 Features

### 一個任務，一間隔離的房

新 session 要先取一個名字（程式限制 32 個字元）。預設從你當時的 HEAD
開一條新 branch，前綴是使用者名稱，worktree 放在
`~/.claude-squad/worktrees`，不在 repo 裡面。也可以改接一條已經存在的
branch；這種 branch 在 session 清掉時不會被刪。

開房時可以選 profile：每一個 profile 是一個啟動指令。預設是 `claude`。
README 舉了 Codex、Aider、Gemini。GitHub 簡介還提到 OpenCode 與 Amp。
程式只是把那段指令丟進 tmux，所以「任何本地 CLI 都能當員工」是啟動方式，
不是 Squad 自己的 runtime。同一時間最多 10 個 session。

### 走廊上的狀態是「畫面有沒有在變」

名單上的圖示不是 agent 回報的進度。程式大約每半秒抓一次 tmux 畫面：

- 畫面跟上一次不同：轉圈，當作正在做事。
- 畫面停著，而且沒有對上已知的許可問句：標成待命。
- Checkout 之後：暫停。worktree 收掉，branch 還在，上次的 diff 行數留在名牌上。

完整 diff 只算你正在看的那一位，其他人只算增加與刪除的行數。Diff 的起點
是這個 session 開始時記下的 commit。多個 repo 的 session 混在同一張名單
時，branch 後面會補 repo 名稱。

### 看、走進、走出來

Tab 在三個窗之間切：終端機預覽、diff、終端機本身。Enter 坐進選定的
session，`ctrl-q` 回到走廊。名單可以用 `J`／`K` 調整順序。

### 把位子讓給人，再請員工回來

`c` 是 checkout。若 worktree 有未提交的改動，它先在本地 commit，訊息是
時間戳，而且帶 `--no-verify`。然後拆掉 worktree、把 tmux 留下但暫停，
並把 branch 名稱複製到剪貼簿。說明文字請你到自己的工作目錄改這條
branch，改完再 resume。

`r` 把 worktree 裝回來、把 tmux 接上。若你的主工作目錄正停在那條
branch，它會拒絕恢復：一條 branch 不能同時被人和這個 session 坐著。
tmux 若因為重開機或 `tmux kill-server` 消失，載入時會把那個 session
收成暫停，讓你用 resume 救回來，而不是讓整張名單載入失敗。

### 推出 branch

目前程式裡的 `p` 會把改動 `git add .`、再用時間戳 commit（同樣
`--no-verify`）、推上 origin，然後用 `gh browse` 打開那條 branch 的網頁。
幫助文字寫「create a PR」，程式沒有呼叫建立 pull request。README 仍把
這個動作寫成按鍵 `s`。需要已安裝的 `gh`。尚未親自按過，以上是原始碼。

### 人離開之後，仍有人替畫面按 Enter

`cs -y` 或設定裡的 `auto_yes` 是實驗旗標。官方說法是自動接受提示。
程式實際做的比較窄：只在畫面裡對上三種英文句子時送 Enter。

- Claude：`No, and tell Claude what to do differently`
- Aider：`(Y)es/(N)o/(D)on't ask again`
- Gemini：`Yes, allow once`

Claude、Aider、Gemini 的信任資料夾提示也會被另外關掉。OpenCode、Amp、
Codex 沒有出現在這組句子裡。你關掉 TUI 時，若這個旗標開著，會留下一個
隱藏的 daemon 繼續按；下次打開 `cs` 會先把 daemon 殺掉。這是在讀終端機
文字，不是權限 API。

## 主打賣點

- **平行的單位是隔離工位，不是多分頁聊天。** 每位員工有自己的 branch、
  worktree 和還開著的終端機。人用一張名單巡視，用 checkout 把 branch
  接回自己手上，再用 resume 把員工請回來。這和 [cmux](cmux.md) 那種
  「很多終端機 pane」相近，但這裡的房間本體是 git workspace。也和
  [Conductor](conductor.md) 那種編排 workflow 的控制台不同層：Squad
  不規定任務怎麼拆，只看管已經在跑的本地 CLI。
- **人可以換班，而不是只能在對話裡插話。** 走進 tmux 是插話。Checkout
  是員工離席、桌子被拆走、你拿著 branch 紙條回到自己的主桌。
- **自動接受是賣點，也是最薄的一層。** 官網用「同時監督、隔離、先審查再
  送出」來說明為什麼要用它，另外主打背景完成與 yolo／auto-accept。
  隔離與換班在程式裡是真的。自動接受只認得三句畫面文字，而且人關掉視窗
  之後 daemon 還會繼續按。官網的「10x your productivity」是行銷句子，
  尚未驗證。

多個 CLI、tmux、git worktree、一個 TUI 名單，各自都是舊工具。Squad 把它們
收成「一個任務一間房，人可以接手那條 branch」。員工之間沒有共同目標、
沒有交接、也沒有共享記憶。

## 使用情境

### 同一個 repo 裡同時做幾件互不相干的事

- 適合誰：已經會用 Claude Code、Codex、Aider 或其他本地 CLI，又不想為了
  平行工作而複製整個 repo 的人。
- 在什麼情況使用：在 repo 裡執行 `cs`，為每個任務開一個 session，必要時
  用左右鍵換 profile，或接到一條既有 branch。
- 帶來的價值：每個任務有自己的 worktree，主工作目錄不會被幾個 agent 同時
  改。走廊上看得到誰的畫面在動、各自的 branch 與 diff 行數。

### 做到一個段落，人要把 branch 拿回來自己改

- 適合誰：不想在 agent 的終端機裡做最後修改的人。
- 在什麼情況使用：按 `c` 讓 session 先把未提交的改動收成本地 commit 並
  暫停，到自己的工作目錄 checkout 那條 branch；改完、離開那條 branch 之後
  按 `r`。
- 帶來的價值：交接的是 branch，不是一段聊天摘要。主目錄還坐在那條
  branch 上時，員工不能回來，避免兩個人同時坐同一張桌子。

### 人離開終端機，仍想讓已知的許可框被按掉

- 適合誰：願意讓實驗旗標在背景按 Enter 的人。
- 在什麼情況使用：用 `cs -y` 開著，確認畫面停在 Claude、Aider 或 Gemini
  那些已知問句上，然後關掉 TUI，讓 daemon 繼續。
- 帶來的價值：官方想要的是「人不用守著每一個 yes」。代價是它只認得固定
  英文句子，而且 commit 會跳過 hook。這條路徑尚未實測，不該當成已確認的
  安全行為。

## 我們可以學什麼

- **值得借鑑的 product idea**：小隊不必是會開會的團隊。它可以是同一條
  走廊上的隔離工位。一個工位等於一個有名字的任務、一條 branch、一張不跟
  別人搶檔案的桌子，以及一台還開著的終端機。人的工作是巡視、坐進去，或
  把桌子收起來自己坐。狀態可以很粗：畫面在跳、畫面停了、人已經離席。
- **值得借鑑的 interaction / workflow**：委派時只要命名、選哪種 CLI、選
  新 branch 或舊 branch，並可選第一句話。比較發生在走廊：所有名牌同時
  在，但一次只把一間房的內容放大，其他人只留 diff 行數。介入有兩種深度，
  走進終端機是插話，checkout 是換班。驗證是看「從這個 session 起點以來」
  的 diff，再決定要不要把 branch 推上去。員工之間沒有 handoff。
- **在 2D workspace 裡會變成什麼**：平面樓層就是你打開 `cs` 的那個 repo。
  走廊左側最多十張名牌，上面是標題、轉圈或待命或暫停、branch，以及
  `+新增 -刪除`。右側是你正對的那間房：預覽窗、貼在牆上的 diff，或一張
  可以坐進去的終端機。新房間的桌子不在這層樓的檔案櫃裡，而在家目錄的
  worktree 倉庫。Checkout 時員工離席，桌子被拆走，你手上拿到 branch
  紙條，回到自己的主桌；主桌若還坐在那條 branch，員工不能回來。不同
  profile 是雇進空房的不同員工，他們不互相說話。Auto-yes 是走廊裡一個
  看螢幕按 Enter 的人，只認得三種告示上的英文；你離開樓層之後他還在。
- **在 3D workspace 裡會變成什麼**：你走在這座 repo 的走廊，最多十間
  辦公室。玻璃上貼著 branch 和 diff 行數。裡面的人螢幕在跳就是工作中，
  螢幕停著是待命，門上鎖、桌子收起是暫停，但 branch 還在。你推門坐下是
  attach，起身回到走廊是 detach。Checkout 時他先把未提交的改動收成一個
  本地 commit，然後離開，辦公室被清空，你拿著 branch 名走到自己的位子。
  Push 是一輛車把 branch 送到 GitHub 並打開網頁，不是在會議室裡審 PR。
  沒有共用的記憶室。那個按 Enter 的人若要留在辦公室裡，必須站在門口讓人
  看見；你離開大樓之後他仍會去按那些認得的告示。
- **不值得照搬或需要重新設計的地方**：用終端機上的英文字串判斷「正在等
  許可」，再由隱藏 daemon 送 Enter。2D／3D 的門應該收到真正的許可事件，
  而且人離開之後誰還在按，必須站在門口。`git add .`、`--no-verify` 和
  時間戳 commit 不該變成換班或送出的預設動作。TUI 一次只放大一間房；
  辦公室應該同時看見所有工位，選中的那間可以更大。10 個上限和 32 字標題
  是這個 TUI 的限制。幫助文字說建立 PR、README 仍寫按鍵 `s`，程式實際是
  `p` 並且只打開 branch 頁面；房間裡發生的事要以真正的動作為準。官網
  landing page 與這個 TUI 都是監督窗，不是我們要做的 workspace。

## 初步看法

- 最有價值的部分：把 git worktree 做成空間單位，並用 checkout／resume
  讓人和 agent 換班。隔離比「多開幾個聊天」更接近辦公室。
- 最大限制或疑問：進度與許可都是在刮終端機畫面。員工之間沒有交接。Push
  不會產生審查對話。README、幫助文字和按鍵表彼此不一致。自動接受在人離開
  後仍繼續，這對手感與風險的影響尚未確認。
- 是否值得進一步研究或親自體驗：值得當「隔離工位 + 換班」的參考。若要
  驗證自動接受是否真的只按到那三句、以及 checkout 後自己改 branch 的手感，
  再在 `/workspace-labs/claude-squad` 跑即可。這次沒有執行。

## 後續補充（選填）

Not tried yet。官方 README、網站與目前 main 的程式已足夠回答 workspace
需要的問題：房間是 worktree 加 tmux、狀態來自畫面是否變化、人用 checkout
接手 branch、自動接受是讀固定英文句子。授權是 AGPL-3.0。最近的 release
是 2026-08-20 的 v1.0.20。

## Sources

- [Claude Squad repository](https://github.com/smtg-ai/claude-squad)
- [Official page](https://smtg-ai.github.io/claude-squad/)
- [README](https://github.com/smtg-ai/claude-squad/blob/main/README.md)
- [v1.0.20](https://github.com/smtg-ai/claude-squad/releases/tag/v1.0.20)
- [AGPL-3.0](https://github.com/smtg-ai/claude-squad/blob/main/LICENSE.md)
- 目前 main 的 session、按鍵與 daemon 程式（用來核對 README 按鍵表；尚未執行）
