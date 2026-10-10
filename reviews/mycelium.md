# Mycelium

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mycelium-cell-ca43/reviews/mycelium.md)
>
> Cell ID：[https://github.com/mycelium-io/mycelium](https://github.com/mycelium-io/mycelium)
>
> Status：`untried`
>
> Category：人與 agent 共用的房間（board、記憶、協商）
>
> Last updated：2026-10-10

## 產品介紹

Mycelium 給人和他們已經在用的 coding agent 一間共用的房間。房間裡有同一條聊天、一塊工作板，和一份放在 hub 上的 markdown 記憶。人用桌面 App（或網頁）開房間、把想要的結果放上板子、看哪些事情在等自己。Agent 用 `mycelium` CLI 加入、領工作、把筆記寫進同一份記憶。文件把它寫成對等協作：不先排好固定流程，也不設一位經理把工作派下去。至少要有一個真正的 agent runtime。README 寫 Claude Code 是已經走過的路，Cursor 尚未測試。

Repo 短述寫了 shared knowledge graph。概念文件裡，那是房間裡的 markdown 筆記，可以用 `[[key]]` 互相引用；App 的 `/room/{room}/graph` 把這些引用畫成一張力導向圖，當作檔案索引。人每天看的工作在房間和板子上。本次只讀 GitHub `main` 的 README、LICENSE 與概念文件，沒有安裝。文件首頁寫明仍是實驗階段，會有破壞性變更。最新發行是 v3.0.37（2026-10-10）。

## 主要 Features

### 房間、板子、討論串

一個房間適合一個團隊或一個專案。裡面的人和 agent 共用聊天、板子和記憶。板上的一列是一件 task：一份 markdown，外加一條專屬討論串，開任務時一起建立。房間頻道只留一行：這件工作被建立、被領走、交還或完成。爭論留在那件工作自己的討論串裡，所以同時有好幾個 agent 時，人不用把每句話讀完。

板子打開時預設是 **Needs you**：等決定的事項、卡住的工作、等人看的 review。進行中、今天完成的，要另外看。紙上有兩個名字。Assignee 是「這件是給誰」。Holder 是「現在誰拿著」，用 claim 拿走、用 release 交還。Agent 可能突然停掉，claim 沒有續約就過期，工作重新可以領。README 把這個會消退的租約叫 custody，概念文件用 holder 和 claim 的過期來寫同一件事。子任務用 parent 掛在父任務下面。做完的任務當天還留在 Resolved，檔案繼續留在房間記憶裡。

### 談不攏時才請人進來調解

平常先在討論串裡講。講不攏，把 **aligner** 叫進這件工作。它是跑在 hub 上的引擎，底層用 NegMAS 做多議題的來回出價：先讀各方已經寫下的立場，一次只問一個人，對方用白話回答，它讀成接受、拒絕或還價。大家接受同一份條件就停。談不成也算結果，決定交回給人。談成之後，它在這件工作下面開新的子任務。原任務還開著，原來拿著它的人還拿著。協商不會自己把任務標完成。

**conductor** 是另一種引擎，在同一條討論串裡跑固定的回合。例如 review：作者做，審查者通過或打回。concord：已經有幾個清楚選項，大家同時打分，由程式挑「最不滿意的人也比較能接受」的那個。accord：開工前先把「這件工作到底是什麼」對齊。回合進行時，只有輪到的人能在這條討論串發言。Aligner 給的是各方還守著立場的時候。Concord 給的是選項已經列出來的時候。

每次這樣的回合都是一個 episode，記在 `log/episodes/`。之後可以搜「當時為什麼這樣定」。

### 記憶留在 hub，人走了筆記還在

房間記憶是 hub 上的 markdown 加 YAML 前言，沒有另建資料庫。常用位置分開：`work/` 是任務，`decisions/` 是決定，`failed/` 是做過不行的，`procedures/` 是以後可再做的步驟。搜尋用 hub 上的本機 embedding，不靠外部向量服務。筆記用 `[[key]]` 互連。`depends-on` 還能讓一件工作等另一件做完。其他機器不留副本，讀寫都問 hub。Agent 自己機器上的說明檔（例如 `CLAUDE.md`）不算房間記憶。隊友該找得到的，才寫進房間。

學習文件把這裡的 agent 寫成工人：這次 session 做完可以離開，下一個人讀的是留下的筆記。桌面 App 會透過 [herdr](https://herdr.dev) 叫醒已經開著的 coding agent：被點名、輪到自己、或被指派工作時，往那個 pane 打一句為什麼要醒。房間、板子、記憶和協商沒有 herdr 也能運作。herdr 補的是坐在提示字元前、自己不會來問的那一種 session。

### 誰的員工，以及身分預設是關的

Agent 可以掛一位 owner 和一個 team，用來濾出「我的 agent」，也用來看出是誰的員工改了東西。預設底下，名字只是宣稱：碰得到 hub 的人可以冒用某個 handle。給外人開的 hub 才打開登入，每次寫入才綁到真正的帳號。私密房間只是不出現在別人的房間清單裡。文件寫明，知道名字的人仍然進得去、讀得到、也寫得了。

主線旁邊的十分鐘雜事可以自己一件 task、一個 agent、一份 git worktree，避免換掉正在做主線的那一對的分支。Hub 上用 swarm 跑起來的 worker，文件說本來就各有自己的 worktree。這些行為尚未實測。

## 主打賣點

- **要被記住的是一間沒有經理的房間。** 工作是板上的一列。爭論在那一列自己的桌子上。走廊只報誰領了、誰交還、誰做完。談不攏才把調解員叫進來。
- **CrewAI 是同一次 Python 執行裡的角色小隊**，可以有 hierarchical 的 manager。Mycelium 的 README 寫：若系統裡已經有一位中央編排者在派工，多半用不到它。這裡的 agent 是各自的 runtime session，自己從板子上領工作。
- **Paperclip 把外部 agent 雇成公司員工。OpenRig 的 seat 活在 tmux。** Mycelium 把人已經在用的 agent 放進同一間房間，並用 herdr 的 pane 叫醒他們。編制單位是房間和板子。
- App 另外有記憶圖、欄位看板和表格，看的是同一批列。菌絲這個名字，以及 `[[連結]]` 畫出來的圖，在空間裡是文件櫃裡的互引。

## 使用情境

### 好幾個 agent 要一起做一個取捨

- 適合誰：已經有 Claude Code 這類 runtime，不想再寫一個 orchestrator 的人。
- 在什麼情況使用：同一件事有多個取捨，各自會重複做，或各說各話、對不出一份答案。
- 帶來的價值：工作先放上板子。談不攏再請 aligner 一次問一個人。談成變成子任務或一筆決定，人不用自己對多份平行輸出。

### 人只看還在等自己的那幾件

- 適合誰：不想把整個頻道讀完的人。
- 在什麼情況使用：好幾個 agent 同時領工作，人要知道哪裡卡住、哪裡在等決定、哪份 PR 要看。
- 帶來的價值：板子預設是 Needs you。走廊只有領取和完成的一行。Claim 過期，位子空出來，已停掉的 agent 不會看起來還在忙。

### 主線旁邊的小修正

- 適合誰：一對人（或 PM 與 coder agent）已經在同一個 checkout 上工作。
- 在什麼情況使用：冒出一個十分鐘就能做完的修正，不想把主線的分支換掉。
- 帶來的價值：雜事自己一張桌子、一位 agent、一份 worktree，修完是自己的小 PR。

## 我們可以學什麼

- 值得借鑑的 product idea：空間的單位是房間。房間裡固定三樣東西：走廊、工作板、架子上的記憶。員工是某人的 agent。一件工作同時有「紙上寫給誰」和「現在誰坐在這張桌子」。調解員是被叫進來的人，平常不站在房間中間派工。
- 值得借鑑的 interaction / workflow：走廊只報狀態，爭論要走到那張桌子才聽得到。談不攏時，調解員一次只跟一個人說話。談成是新的工作紙釘在下面，原桌的人還坐著。Claim 沒有續約，椅子空出來。做過不行的做法放在失敗架，下一班不用重試。Side quest 是隔壁小桌，有自己的工作副本。
- 在 2D workspace 裡會變成什麼：一張俯視樓層。每個 Mycelium room 是樓層上的一間專案房。房裡的牆是工作板，預設只亮需要人的幾張紙：等決定、卡住、等人看。每張紙是一張桌子。Assignee 寫在紙上，holder 坐在椅子上。Claim 過期，人離座，紙回到可領的那一疊。走廊地板上只有短句：建立、領走、交還、完成。Aligner 被叫來時走進這張桌子，輪流跟在座的人說話；談成就把新的子任務紙釘在父任務下面。記憶是牆邊的文件櫃。`[[key]]` 是這份文件指向另一份，打開才看得到。Side quest 是隔壁小隔間，裡面是另一份 checkout。私密房間不出現在樓層目錄上，門沒有鎖。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走進專案房，看見誰坐在哪張桌子。Scout 坐在「Ship passkey login」，sec 坐在子任務「Pick token storage」。走廊很安靜，只有領取和完成的一行；要聽爭論就走到那張桌。兩人談不攏，aligner 坐到中間，一次只問一個人，直到同一份條件被接受，或把決定交回給站在旁邊的人。Session 結束，工人離開，架子上的決定和 episode 紀錄還在，下一個走進房間的人讀得到。Claim 沒人續，椅子是空的。記憶圖留在檔案櫃的索引裡，房間裡看見的是人和桌子。重點是看見誰在場、誰在哪裡做事。
- 需要重新設計的地方：現在的 App 是聊天、板子、表格，加上一張力導向的記憶圖。空間辦公室要另外做出「誰坐在哪張桌」。預設身分只是宣稱。名牌若直接信任 handle，任何人都能坐進別人的位子；登入打開之後才把人和椅子綁住，這點尚未實測。官方評估在 `docs/evaluation.md`，標題是 v1.0.13z，現行發行是 v3.0.37。報告寫 14 個情境裡，有調解時 13 個達成共識、沒有時 5 個，用的是 Claude Haiku。我們沒有重跑，那些數字是否仍成立尚未確認。

## 初步看法

- 最有價值的部分：房間把走廊上的狀態和桌子上的爭論分開，再加上會過期的持有，以及被叫進來的調解員。這三件事可以直接變成樓層上的人和房間。
- 最大限制或疑問：它還在實驗，要外接真正的 agent runtime。身分預設關閉。評估報告的版本和現在的發行沒有對上。
- 是否值得進一步研究或親自體驗：值得。尤其是 Needs you、claim 過期、aligner 進桌，在 2D／3D 辦公室裡要怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Repository：https://github.com/mycelium-io/mycelium
- Docs site：https://mycelium-io.github.io/mycelium/ （文件寫明網站跟最新穩定 tag 走）
- License：repo `main` 的 `LICENSE` 為 Apache-2.0。CLI 的 `pyproject.toml` 作者欄寫 Cisco ETI
- README（`main`）：房間、板子、aligner、hub 記憶、桌面 App 與 Docker 兩條路徑
- 概念文件（`main`，`mycelium-cli/src/mycelium/docs/`）：overview、rooms、board、memory、engines、aligner、conductor、episodes、herdr、working-the-board、side-quests
- Release：v3.0.37，2026-10-10
- 官方評估：`docs/evaluation.md`（標題 Mycelium v1.0.13z；我們未重跑）
