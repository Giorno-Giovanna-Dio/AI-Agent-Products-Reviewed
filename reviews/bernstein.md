# Bernstein

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-bernstein-cell-80a8/reviews/bernstein.md)
>
> Cell ID：[https://github.com/sipyourdrink-ltd/bernstein](https://github.com/sipyourdrink-ltd/bernstein)
>
> Status：`untried`
>
> Category：GitHub Action／多 agent 編排（Python scheduler）
>
> Last updated：2026-10-10

## 產品介紹

Bernstein 是 sipyourdrink-ltd 維護的開源 Python 專案（Apache-2.0，網站
[bernstein.run](https://bernstein.run)）。它把「一個目標交給好幾個 CLI
coding agent」收成可檢查的流程：先用一次規劃把目標拆成任務，之後改由普通
Python 排程決定誰領工作、何時重試、何時合併。官方支援 Claude Code、Codex、
Gemini CLI 等多種 CLI。狀態標為 beta，由個人維護，小版本可能改介面。

這次的來源網址是 GitHub Marketplace 上的 action 列表，不是另一個 git
repo。那支 composite action 就發布在這個 repo 的 `action.yml`。它在
GitHub Actions 裡安裝 `bernstein`，然後跑一個目標、一份 plan，或是「依照
失敗的 CI log 去修」。Action 沒有辦公室介面。多 agent、worktree 和檢查都
發生在它叫起來的那個程序裡。

## 主要 Features

### 協調迴圈沒有模型

一次規劃把目標拆成帶角色、負責檔案和完成訊號的任務。接下來的 tick loop 是
Python：領任務、開 agent、看心跳、重試、合併。官方說同一份 plan 重播會得到
同一張任務圖。重播對齊的是排程，模型每次仍可能寫出不同的 diff。這一點尚未
實測。

### 一個任務一間 worktree

Coding 任務預設各自一個 git worktree，主線先保持乾淨。預設不共用可寫工作區；
大家共用的是任務 backlog，領取是原子的。關掉 worktree 時，任務會落在同一份
checkout，那是關掉隔離。產物型任務（報告、資料集）走 `.sdd/workspaces/` 下的
目錄，完成條件是簽過的 lineage receipt，不是一次 git commit。Docker 等
sandbox 是選用；這支 Action 的預設路徑沒有打開它們。

### Janitor、閘門、審稿、合併

Janitor 不採信 agent 說的「做完了」。它看具體訊號：檔案在不在、測試過不過。
品質閘門可以再跑 lint、型別、PII 掃描。審稿是後一段，可以把不合格的工作退回
佇列。核准閘門放在驗證和合併之間。通過的分支進 FIFO merge queue，再回主線。
跨模型檢查可以把 diff 交給另一個模型看。這些關卡在 Bernstein 程序裡面。
Marketplace action 沒有把它們拆成獨立的 CI job。

### GitHub Action 實際只有三條路

腳本只分三種跑法。給了 plan 檔，就跑 `bernstein run`，不再臨場拆解。`task`
正好是 `fix-ci` 時，下載失敗 job 的 log（沒有就退回這個分支最近一次失敗），
取最後約 200 行當成目標，再依次數重試。其他任何文字——包含註解裡寫的
`review-pr` 和 `decompose`——都只是 `bernstein -g` 的目標句子，腳本沒有另外
分支。

Repo 若沒有 `bernstein.yaml`，Action 會寫一份很短的預設：選定的 CLI、
`max_agents: 2`、兩句約束（先跑測試、commit 要寫清楚）。已有設定檔就不會
覆寫，此時 Action 的 CLI 選項也不會再寫進去。

跑完之後，除非設了 `BERNSTEIN_SKIP_AUTO_COMMIT`，Action 會 `git add -A`，
以 `bernstein[bot]` commit，並 push 到目前 branch。這一步在 Bernstein 自己的
merge queue 外面。它也會在 PR 或 issue 上留摘要，並從 `.sdd/run-summary.json`
和 `.sdd/evidence` 填 Actions 的輸出。

### 事後對得起來的紀錄

每次執行有 replay journal；lineage spine 預設開著。HMAC 審計鏈要另外打開。
審查者可以拿收據和公鑰離線檢查，不必重跑。狀態放在 `.sdd/` 檔案裡。
`bernstein live`（終端機）和 `bernstein gui serve`（瀏覽器）讀同一套 task
API。Action 用 headless／quiet，不會打開這兩塊畫面。

## 主打賣點

- 它最想被記住的是：指揮不交給模型。排程可以重播，每個任務有自己的
  worktree，做完要留下事後查得到的紀錄。
- 和「CI 裡包一個 agent」真正不同的地方在程序內部。一個目標會拆開，同時跑
  的是數個短命 CLI（Action 預設上限是 2），下一棒由 Python 決定，合併前有
  janitor。從 workflow 檔案外面看，它仍是一個 job、一條指令。
- 安裝 CLI、把 log 貼進目標、跑完 commit 再 push，這些是常見的 CI 包裝。
  Bernstein 自己倉庫裡較小心的 auto-heal、issue 拆解和 PR review，都沒有呼叫
  這支 Marketplace action，而是另寫 checkout、標籤和權限。註解裡的
  review-pr／decompose 也不是 Action 的獨立模式。

## 使用情境

### CI 紅了，用失敗 log 再開一輪

- 適合誰：已經用 GitHub Actions、希望失敗時先有一輪自動看 log 的維護者。
- 在什麼情況使用：workflow 把任務設成 `fix-ci`。Action 把失敗 log 收成目標，
  重試數次，最後在 PR 上留言。
- 帶來的價值：不用手抄 log。代價是預設會把工作區變更 push 回目前 branch。
  這條 workflow 檢出的是失敗的 SHA 還是預設分支，要看呼叫端怎麼寫，Action
  自己不保證。尚未用這支 Action 跑過。

### 已經寫好的 plan，不要臨場再拆一次

- 適合誰：願意把階段、角色和依賴寫進 YAML 的人。
- 在什麼情況使用：Action 指向那份 plan，走 `bernstein run`。
- 帶來的價值：任務圖是人寫的，模型不再決定下一站。角色圍欄能不能擋下 agent
  在任務裡的越權，官方這樣說，尚未確認。

### 在機器上看多個 worktree，而不是只看 CI log

- 適合誰：想看「誰領了哪張任務」的人。
- 在什麼情況使用：本機跑 `bernstein -g` 或 `bernstein run`，另開
  `bernstein live` 或 GUI。這不是 Marketplace action 的路徑。
- 帶來的價值：同一套 task API 上有終端機和瀏覽器，可以看進度、成本和核准
  佇列。它們是值班儀表板，不是可走進去的辦公室。

## 我們可以學什麼

- 值得借鑑的 product idea：把指揮和樂手分開。協調是普通程式（誰領下一張
  任務、何時重試、何時合併），模型只用在兩端：一開始拆目標，以及可選的品質
  審查。每個 coding 任務有自己的 worktree，完成要靠看得到的訊號，而不是
  agent 自己宣布做完。
- 值得借鑑的 interaction / workflow：人交出一個目標或一份寫好的 plan。
  Scheduler 原子領取 backlog。Agent 在自己的 worktree 裡改完並 commit。
  Janitor 和品質閘門擋在合併前，審稿者可以退回，核准的人站在合併之前，衝突
  走佇列。Agent 之間用檔案訊號和佈告欄交接，不是一條共用聊天。GitHub Action
  只是開工的碼頭：裝好 CLI、送進目標或失敗 log、結束後留言。
- 在 2D workspace 裡會變成什麼：俯視樓層上，每一格是一個 worktree 工位，門牌
  寫任務名和角色。階段是地板上的通道，例如釐清範圍、收集、檢查、交付。正在
  跑的 agent 站在那一格，桌上貼著 log 的最後幾行。Janitor 是通往主線月台前的
  檢查亭，沒有具體訊號就不放行。預算是牆上的錶。`fix-ci` 是一張亮紅燈的桌子：
  失敗 log 送到空工位，修完才准離開。Action 結尾把所有變更推上目前分支，在
  這張地圖上應畫成一條會把髒檔倒上主線地板的輸送帶，並且停在檢查亭前面。
- 在 3D workspace 裡會變成什麼：可走進的辦公室。每個任務是一間小室，門牌是
  角色和任務，走過去看得到誰在改檔。寫 code 的人離開後，審稿的人走進同一間
  室接班。Janitor 站在走廊，核准的人站在通往主線房間的門口。CI 失敗時，修復
  的人被領進那間還亮紅燈的室。GitHub Action 是大樓的卸貨碼頭：它叫人上班、
  把貨送出門，本身不是你走進去工作的樓層。
- 不值得照搬或需要重新設計的地方：不要把「跑完就 commit 並 push 目前分支」
  當成完成。產品自己較嚴的 CI 路徑沒有用這支 composite action。entrypoint
  註解裡的 review-pr 和 decompose 沒有獨立實作；Bernstein 本體是否對這兩個
  句子另有行為，尚未確認。預設設定只有兩個 agent 和兩句話，那不是政策檔。
  HMAC 和簽章收據可以是桌上的檔案夾，不必做成辦公室的主畫面。`bernstein live`
  和 GUI 是 ADE dashboard，不要把它們叫做 2D 或 3D workspace。角色圍欄是否
  真的擋得住，尚未確認。

## 初步看法

- 最有價值的部分：協調不交給另一個模型，隔離用 worktree，完成用 janitor 的
  具體訊號。這比「一個 prompt、一個 CLI、一個 commit」更接近未來 workspace
  的執行層。
- 最大限制或疑問：Marketplace action 比產品薄。它預設同時只有兩個 agent，
  收尾的 `git add -A` push 也比內部的 merge queue 粗。官方自己說 beta、要
  pin 版本。我們沒有跑過，角色圍欄和重播是否如文件所說都尚未確認。
- 是否值得進一步研究或親自體驗：值得當治理和執行模型的參考。要動手時再在
  repo 外跑一次最小的 `bernstein -g`，不必先把這支 Action 接上自己的 CI。

Not tried yet.

## Sources

- [Bernstein repository](https://github.com/sipyourdrink-ltd/bernstein)
- [GitHub Marketplace：Bernstein — Multi-Agent Orchestration](https://github.com/marketplace/actions/bernstein-multi-agent-orchestration)
- [bernstein.run](https://bernstein.run)
- [Architecture](https://docs.bernstein.run/en/latest/architecture/ARCHITECTURE/)
- 本 repo 的 [`action.yml`](https://github.com/sipyourdrink-ltd/bernstein/blob/main/action.yml) 與 [`action/entrypoint.sh`](https://github.com/sipyourdrink-ltd/bernstein/blob/main/action/entrypoint.sh)（Action 的實際分支）
- 產品自己的 CI 路徑（未使用這支 Marketplace action）：[`auto-heal.yml`](https://github.com/sipyourdrink-ltd/bernstein/blob/main/.github/workflows/auto-heal.yml)、[`bernstein-issues-decompose.yml`](https://github.com/sipyourdrink-ltd/bernstein/blob/main/.github/workflows/bernstein-issues-decompose.yml)、[`bernstein-pr-review.yml`](https://github.com/sipyourdrink-ltd/bernstein/blob/main/.github/workflows/bernstein-pr-review.yml)
