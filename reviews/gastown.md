# Gas Town

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-gastown-cell-c2d2/reviews/gastown.md)
>
> Cell ID：[https://github.com/gastownhall/gastown](https://github.com/gastownhall/gastown)
>
> Status：`untried`
>
> Category：多 agent workspace 編排（Go CLI／tmux）
>
> Last updated：2026-10-09

## 產品介紹

Gas Town 是一套用 Go 寫的多 agent workspace 管理器，命令是 `gt`，授權
MIT。它讓一個人同時調度多個 coding agent（官方預設 Claude Code，README
也提到 Codex、Copilot、Gemini、Cursor），讓他們在不同專案上工作。工作
狀態不放在聊天記憶裡，而寫進 Beads 帳本（Dolt），session 掛了還找得回
來。使用者先在本機建一座 town（文件範例是 `~/gt/`），再跟 Mayor 講要做
什麼；Mayor 把工作拆成 convoy，丟給有名字的 worker。人自己的位子叫
Crew。尚未實際安裝或執行。

它比較像一座有編制的 coding 樓層，而不是單一聊天視窗。每個專案是一座
rig；worker 在 git worktree 裡改 code；完成後由 Refinery 排隊驗證再合
進主線。現在的畫面是終端機、tmux，以及一頁會自動刷新的 web dashboard。
這頁 dashboard 是監控用的 ADE 畫面。下面談的 2D／3D，是同一套角色放進
工作空間之後會變成什麼。

## 主要 Features

### Town、Rig，以及兩種工人

Town 是總部，管跨專案的協調。Rig 包住一個 git 專案，裡面有自己的
worker、Witness 和 Refinery。Polecat 是有固定身份、但 session 會結束的
工人：做完就拆掉 worktree，履歷還在。Crew 是人（或長期代理人）的工作
區，用完整 clone，生命週期由人決定，適合需要判斷、會拖很久的事。價值
是：短暫的執行和長久的身份分開，不會因為關掉一個 session 就弄丟「誰做
過什麼」。

### Hook、Beads 與 Convoy

每個 agent 有一個 hook，上面掛著目前該做的 bead。官方原則 GUPP 是：hook
上有工作就執行，不要再等人確認。Bead 是最小工作單位，存在 Dolt，town
級（`hq-`）和 rig 級分開記。Convoy 把相關 bead 捆成一批在途工作，方便
看整批進度，而不是只看單一聊天。價值是：重啟、換 session、跨專案時，工
作單還在，而且每筆動作都掛著 actor（例如某隻 polecat 或某個 crew）。

### Mayor 派工，分子當流程模板

官方建議的用法是先跟 Mayor 說目標。Mayor 拆任務、開 convoy、把 bead
sling 到 agent 的 hook。重複流程可以寫成 formula，再實例化成 molecule，
步驟本身也是可追蹤的 bead。設計文件寫明：依技能自動派工仍是計畫，現在
是人手 `gt sling`。價值是：人對「鎮長」下目標，而不是自己在每個終端機
裡貼 prompt；但自動匹配能力還不能當成已上線。

### 看門狗、升級，以及人可以插手的地方

Witness 盯單一 rig 裡卡住或死掉的 polecat，可以 nudge 或交給人接手。
Deacon 在全 town 巡邏；Dogs 做維護，不是拿來寫產品功能。Agent 遇到阻
礙用 `gt escalate` 依嚴重程度往上送，最後可以到人（Overseer）。`gt
feed` 有 problems 視圖，把長時間沒進度、殭屍 session、閒置等狀態分開；
文件寫可用按鍵 nudge 或 handoff。`gt dashboard` 把 agent、convoy、
hook、queue、escalation 放在同一頁。價值是：規模變大時，人看的是「誰
卡住、要不要介入」，而不是翻每一段 transcript。

### Refinery：工人不直接推 main

Polecat 做完是推分支、送 merge request。Refinery 以批次驗證、失敗再對
半切開的方式合併（文件稱為 Bors 式佇列）。Crew 則可以自己推 main。價
值是：平行工人的結果有一個合併碼頭和驗證關卡，主線不會被多個 agent 同
時直推。

### Handoff 與 Seance

Context 滿了可以用 handoff 把工作交到新 session。Seance 讓 agent 從
`.events.jsonl` 找到前一個 session，問它當時的決定。價值是：交接的是
「這段工作的記憶」，不是把整個 repo 再讀一遍。信件、nudge 則是 agent
之間的即時溝通；細節尚未親自操作。

## 主打賣點

它想被記住的是：多 agent 寫 code 時，要有一座會自己往前推的 town。工作
在 hook 上就跑，狀態在 Beads，合併走 Refinery，每一步都有名字可追。

角色名稱（Mayor、Polecat、Witness、Deacon、Refinery）是這套編排的包裝。
底下仍是 git worktree、tmux session、issue tracker 和活動串流。和
Paperclip 都在講「一群有角色的 agent」，但 Gas Town 的單位是 coding
rig、worktree 和 merge queue；Paperclip 的單位是公司目標、編制和預算。
Agent Office 才是可走進去的辦公室。Gas Town 今天把辦公室落成目錄、tmux
和一頁 dashboard。

設計文件把「依履歷自動派工」和跨組織 federation 標成尚未完成，並寫目前
是單一 town。README 另有 Wasteland，描述透過 DoltHub 在多個 town 之間
張貼、認領工作和累積名聲。兩份說法不一致，完成度尚未確認，不能把願景
當成已驗證功能。

## 使用情境

### 一個人同時跑很多 coding agent

- 適合誰：要在多個 repo 上平行開 agent、又怕 session 一斷進度就消失的人。
- 在什麼情況使用：工作可以拆成明確 bead，再組成 convoy，交給 polecat。
- 帶來的價值：身份、工作單和合併佇列留在 town 裡，人用 Mayor 和 feed
  看整批在途工作，而不是一人盯一個終端機。

### 人留在 Crew，雜事丟給 Polecat

- 適合誰：自己仍要做需要判斷的改動，同時想把界線清楚的任務分出去的人。
- 在什麼情況使用：探索、長期分支留在 crew；可平行、做完可驗收的項目 sling
  給 polecat，由 Refinery 合併。
- 帶來的價值：人的工作區不會被工人的 worktree 蓋掉，主線有驗證關卡。

### Session 死掉或 context 滿了

- 適合誰：已經在跑長任務、遇到 agent 卡住或對話被截斷的人。
- 在什麼情況使用：靠 hook 上的 bead 恢復工作，必要時 handoff 或 seance
  問前一個 session；Witness 把殭屍和久無進度的 agent 浮上來。
- 帶來的價值：接手的是同一份工作單和身份履歷，不必從頭重講目標。

## 我們可以學什麼

- 值得借鑑的 product idea：把 agent 分成「鎮長、常駐員工、臨時工人、
  巡場、合併碼頭」。臨時工人的 session 可以死，名字和履歷不要死。工作
  單（bead／convoy）是共享記憶，聊天只是當下的執行。
- 值得借鑑的 interaction／workflow：人對 Mayor 下目標；hook 代表「這人
  現在該做的一件事」；卡住時升級，而不是默默空轉；合併是一個有人看管的
  碼頭，不是每個工人自己推 main。Seance 把「問上一班留下的決定」變成一
  個動作。
- 在 2D workspace 裡會變成什麼：一張俯視的小鎮平面。鎮公所是 Mayor。每
  座 rig 是一塊工作場，裡面有固定的 crew 桌子，以及 polecat 的臨時工位
  （一個 worktree 一張凳子，人走了凳子收起，名牌還在）。Convoy 是場子之
  間的一批貨。Refinery 是場邊的合併月台，MR 在月台上排隊。Witness 在自
  己的場子裡巡，Deacon 沿小鎮走。Problems 視圖就是哪些工位亮起久無進度、
  session 已死或閒置。人站在自己的 crew 桌，走向卡住的工位做 nudge 或
  handoff。
- 在 3D workspace 裡會變成什麼：可走進的小鎮辦公室。你看得到 Mayor 在鎮
  公所、哪些 polecat 正坐在自己的工位改分支、哪些 crew 桌整天有人。你走
  到某個工人旁邊看他的 hook 和 bead，或把卡住的人叫醒。Refinery 是一間
  看得到佇列和驗證結果的廠房。Deacon 沿走廊巡邏。Seance 是走到上一班留
  下的桌子，翻當時的決定。這是「員工在場、工作在某個位子上」，不是把
  bead 依賴圖做成可以旋轉的立體物件。
- 不值得照搬或需要重新設計的地方：`gt feed` 和 `gt dashboard` 可以當監
  控資料來源，但它們是 ADE dashboard，不該直接當成我們要做的 2D／3D
  workspace。角色命名很密（polecat、wisp、molecule、dog），進到空間裡
  要翻譯成看得到的工位和狀態，而不是把術語原樣貼在牆上。技能自動路由和
  多 town 聯盟都還不能當現成機制來抄。

## 初步看法

- 最有價值的部分：身份持久、session 短暫、工作在 hook／bead、合併有
  Refinery。這四件事直接對應「辦公室裡誰在、位子上有什麼、做完送到哪」。
- 最大限制或疑問：介面仍是 CLI、tmux 和單頁 dashboard，小鎮只存在目錄
  結構和名詞裡。Wasteland 與設計文件對 federation 的說法不一致。自動派
  工尚未完成。隔離主要是 git worktree，另有可選的 Docker Compose；權限
  模型尚未親自確認。
- 是否值得進一步研究或親自體驗：值得。下一次應看 Mayor 派工、polecat
  生命週期和 Refinery 佇列在真實 session 裡是否真的讓人少盯終端機。

## 後續補充（選填）

Not tried yet.

## Sources

- Repository：[https://github.com/gastownhall/gastown](https://github.com/gastownhall/gastown)（GitHub about：Gas Town - multi-agent workspace manager；Go；MIT；無 homepage）
- 舊網址（301 到上一列）：[https://github.com/steveyegge/gastown](https://github.com/steveyegge/gastown)
- README、`docs/overview.md`、`docs/glossary.md`、`docs/design/architecture.md`、`docs/why-these-features.md`：經 GitHub API 閱讀，未執行程式
- 可選執行方式：原生 `gt`（文件亦提供 Docker Compose sandbox，dashboard 預設埠 8080）
