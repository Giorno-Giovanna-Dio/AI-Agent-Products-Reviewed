# OpenChamber

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openchamber-cell-0717/reviews/openchamber.md)
>
> Cell ID：[https://github.com/openchamber/openchamber](https://github.com/openchamber/openchamber)
>
> Status：`untried`
>
> Category：以 OpenCode 為核心的 ADE（跨裝置監督與出貨）
>
> Last updated：2026-10-08

## 產品介紹

OpenChamber 是包在 [OpenCode](https://opencode.ai) 外面的開源工作台。它不自己當
coding agent，而是讓人在桌面、瀏覽器、VS Code 和手機上，監督同一台機器上的
OpenCode session：決定要試什麼、看它改了什麼、把變更送到 review 和 release。
官方定位是 Agentic Development Environment，專案獨立於 OpenCode 團隊，授權
MIT。

程式與 session 留在你控制的電腦或伺服器上。桌面版會自帶對應的 OpenCode CLI；
用 CLI、網頁或 VS Code 時，則使用機器上已安裝的 OpenCode。關掉分頁或鎖上手機
之後，只要 OpenChamber 的 server（桌面 app 或 `openchamber` 行程）還在跑，
進行中的工作可以繼續。換裝置是換入口，不是把 repo 搬到雲端。

## 主要 Features

### Session Goals：自己往終點走

一個 session 可以設一條終點線。每一輪結束後，OpenChamber 用一個較小的模型當
稽核員，只看目標說明和 agent 最新回覆，判斷要繼續、已完成，還是卡住。繼續就
再送一則後續提示；完成或卡住才通知人。卡住要連續三次才停，避免一次小挫折就
結束。可以設 token 預算和自動續跑上限。目標迴圈跑在 server 上，關掉客戶端
不會停，但明確按停止一定蓋過迴圈。一次一個 session 只有一個 goal。

### Multi-run 與 Fusion

同一個 prompt 可以同時丟給多個模型，每個模型一條自己的 session，也可以各自
開 worktree，避免互相改到同一份檔案。人再決定留下哪一版。Fusion 是把各跑的
強項收成一條新的後續 session；官方 release 提到可自訂 Fusion prompt。README
寫最多五個模型，較新的 changelog 寫群組可以超過五個。這份筆記沒有實測，上限
以你安裝的版本為準。

### Changes Walkthrough：依因果讀 diff

一般 diff 照檔名排，很少是變更真正該被讀的順序。Walkthrough 把相關修改收成
站點，每站用一兩句話說明現在的程式差在哪，並標出該細看的關鍵變更，或可以略讀
的上下文。它只負責排序和解釋，不給品質判決；要判斷好壞是另一個 Review 動作。
站點錨在當時的程式內容上，之後程式變了會標 Outdated，沒被講到的變更會標
Not covered。不會自動生成，要人按下去才呼叫模型。

### 預覽，以及讓 agent 自己看畫面

開發中的網站可以開在對話旁邊。人點一個元素，就能把截圖、樣式、位置和瀏覽器
錯誤送給 agent。桌面版的瀏覽器還能讓 agent 自己開頁、點擊、輸入、切手機或桌面
版面、存截圖，用來檢查自己的成品。這是 OpenChamber 自己的 Web tool，可以開關。
遠端機器上的 dev server，桌面 app 會在本機開一個轉送埠；普通瀏覽器分頁則只能
開本機的 server。

### 從 issue 做到 pull request

連上 GitHub 之後，可以從 issue 或 PR 開一條 worktree session，把討論串（PR
還可以帶 diff）附在第一則訊息上。失敗的檢查和 review 意見可以送回 agent，再
在同一個視窗更新或 merge PR。Worktree 的起點也支援 Linear，以及擴充提供的
工單。GitLab 在官方網站上標成進行中，尚未確認是否已可用。

### 同一台機器，很多入口

Desktop、Web／PWA、VS Code、iOS／Android、CLI 看的是同一批專案和 session。
側欄可以依專案或 worktree 整理 session，並看到誰在做、誰在等、誰做完或失敗，
以及批准、排程、provider 額度、token 和費用。手機可以用一次性 QR 配對，經
Private Relay 連線：機器只對外連出去，不開 inbound port，流量端到端加密，
憑證可隨時撤銷。同一區網時會改走直接連線。需要公開網址時才用 tunnel，並應加
UI 密碼。

## 主打賣點

- 它想被記住的是：agent 繼續在你的機器上工作，人可以離開座位仍監督、比較、
  驗收，最後從同一個地方出貨。
- 和 [Conductor](conductor.md)、[T3 Code](t3code.md)、[Emdash](emdash.md)
  這類 ADE 相比，OpenChamber 不換多家 agent runtime，而是認定 OpenCode，再把
  開工、續跑、並排實驗、讀 diff、遠端介入和 PR 收尾補在外面。
- 較少見的是三件事：稽核員看不到整段聊天、只看目標和最新回覆；diff 先被排成
  導覽路線，而不是直接打分數；平行結果可以再融成一條新 session。
- Worktree、多視窗、跨裝置遙控、issue 開工、排程，這些在其他 ADE 裡已經常見，
  這裡是同一套控制面的不同入口。終端機實作還借用了 T3 Code 的瀏覽器端
  Ghostty adapter，產品本身仍是獨立的。

## 使用情境

### 人離開座位，目標繼續跑

- 適合誰：機器常開、又不想一直守在對話框旁邊的人。
- 在什麼情況使用：給 session 一個寫得清楚的完成條件，然後鎖螢幕或改用手機看通知。
- 帶來的價值：不用每輪手動說「繼續」。卡住或做完才叫人，執行仍留在自己的機器和自己的模型帳號上。

### 同一張工單，多個模型各做一版

- 適合誰：想比較做法，而不是只比較模型名稱的人。
- 在什麼情況使用：一個 prompt 開多條隔離 worktree，做完再挑一版，或用 Fusion 開新 session 收斂。
- 帶來的價值：候選版本各自留在自己的 branch 上，人看的是實際改動，不是一段自我介紹。

### 大變更要按因果讀，而不是按檔名讀

- 適合誰：要驗收 agent 交出來的一大包 diff 的人。
- 在什麼情況使用：未提交的變更、整條 branch，或 GitHub 上的 PR。
- 帶來的價值：先知道哪一站是關鍵、哪一站只是上下文，而且程式再改時會標出導覽已經過期。

## 我們可以學什麼

- 值得借鑑的 product idea：把「做到什麼算完成」從聊天裡拆出來，交給一個資訊更少的稽核員。稽核員看不到整段對話，所以目標必須自己就能看懂完成狀態。這比再加一個全能 reviewer 更接近可監督的完工條件。
- 值得借鑑的 interaction / workflow：平行實驗是多條 session 加可選 worktree，人再選擇或融合；閱讀變更和評判變更分開；客戶端只是入口，續跑、通知和 session 都掛在擁有 checkout 的那台機器上。
- 在 2D workspace 裡會變成什麼：俯視樓層上，一個專案是一層。每個 OpenCode session 是一張工位，工位標著在做、在等批准、做完或卡住。Session Goal 是工位上的終點牌，旁邊站一個只拿「目標卡和最新一輪紙條」的稽核員。Multi-run 是同一張工單複製到一排隔間，門上寫模型名稱和 worktree；Fusion 是把挑中的片段交到一張新工位。Walkthrough 是地板上的導覽路線，不是檔案牆。手機和瀏覽器是同一層樓的另一個入口，員工沒有被搬去別的樓。
- 在 3D workspace 裡會變成什麼：可走進的辦公室裡，OpenCode 員工坐在專案房間的桌子前。稽核員站在桌邊，不翻整段聊天，只核對終點牌和剛交的回報，然後決定要不要讓員工繼續。平行實驗是走廊上幾間相鄰的隔離辦公室，你走進每一間看他們實際改了什麼。Walkthrough 是專人按因果帶路，停在關鍵變更前，不在門口蓋「通過」。牆上的螢幕是正在跑的 app，員工可以自己點來檢查。你從外面用手機進來，是從加密側門走進同一間房間，看到同一個人還在做事。
- 不值得照搬或需要重新設計的地方：OpenChamber 本身是 ADE dashboard，不是平面辦公室，也不是可走進的辦公室。我們要留下的是目標稽核的資訊邊界、導覽與判決分開、平行 run 的空間隔離，以及「遠端只是進同一間房間」。不要把整套 composer 和側欄搬進 2D／3D workspace。稽核員只看最新回覆，中間做錯又改回來的過程可能被漏掉，這點要另外設計證據。Fusion 官方說的是收成新 session，它會不會真的合併 worktree 裡的程式，尚未確認。

## 初步看法

- 最有價值的部分：監督迴圈有明確的資訊切面。目標、最新回報、導覽順序、過期標記，都比「再看一次完整 transcript」更接近 workspace 裡該被看見的狀態。
- 最大限制或疑問：尚未實際使用。Multi-run 上限在 README 和 changelog 之間不一致。Private Relay 的中繼基礎設施由官方營運，enterprise 模式才改走自架 relay；端到端加密是官方說法，這份筆記沒有驗證。綁定 OpenCode 也表示換 runtime 不是這個產品要解決的事。
- 是否值得進一步研究或親自體驗：值得。若要設計「員工何時算做完、人如何遠端走進同一間辦公室、大變更如何被帶路」，這三個互動比再看一個多 provider ADE 更有差異。

## Sources

- Official website：https://openchamber.dev/
- Repository：https://github.com/openchamber/openchamber
- Documentation：https://github.com/openchamber/openchamber/tree/main/packages/docs/content/docs（Session Goals、Multi-run、Changes Walkthrough、Preview、GitHub、Private Relay、Security、Worktrees）
- 舊網址（已轉到目前 repo）：https://github.com/btriapitsyn/openchamber
