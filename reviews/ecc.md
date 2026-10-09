# ECC

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ecc-cell-90bd/reviews/ecc.md)
>
> Cell ID：[https://github.com/affaan-m/ECC](https://github.com/affaan-m/ECC)
>
> Status：`untried`
>
> Category：Harness 工程程序
>
> Last updated：2026-10-08

## 產品介紹

ECC 是裝進既有 coding harness 的工程程序。官方 README 的標題與網站產品名都是 ECC；第三方文章常把全名寫成 Everything Claude Code。它最完整的安裝方式是 Claude Code plugin（`ecc@ecc`）。Codex 有原生 plugin；Cursor 與 OpenCode 是能力較少的 adapter；GitHub Copilot 只有指令檔；Gemini、Zed 等其餘 harness 更薄。使用者裝一次，同一條環就留在 harness 裡：`plan → test → implement → review → verify → remember → improve`。

它是一本會長大的共用程序手冊，加上按任務叫來的專門 agent，再加上本地 Markdown 記憶。那些 agent 有自己的 context 和工具權限，進場時像員工；ECC 本身是程序與記憶的裝法，不是一個人，也不是可走進的辦公室。網站上的 Control Pane 用來查看 session 與 worktree，那是操作者的 dashboard。Repository 的 LICENSE 是 MIT。付費 GitHub App（ECC Pro）是另一條託管路線；這個 Cell 談的是公開 repo 裡的工具組。

## 主要 Features

### 一次裝上的工程環

新功能先做成可修改的計畫，再進入測試先行、實作、換一份乾淨 context 審查，最後核對 build、lint、型別與測試。修 bug 則先寫出會失敗的測試。這段順序留在已安裝的程序裡，不必在每次 prompt 重講。

### 把 context 留小，其餘放外面

官方原則是：優化 context window，其餘都持久化。Skills 在任務需要時才載入。Rules 會一直佔著 context，所以要按語言或專案挑著裝。Hooks 在模型外面跑確定性檢查。學到的 instinct 在 session 開始時注入，預設最多 6 條，而且信心要達到門檻（預設 0.7）。`/context-budget` 用來查看 context 壓力。完整 plugin 會把已安裝目錄廣告給模型；在意 context 時，官方建議改用 selective 或 manual profile。

### Instincts：把重複成功收成行為

Continuous learning v2 把 session 裡反覆出現的做法收成很小的 instinct：一個觸發、一個動作、一個信心分數，預設留在目前專案，避免一個專案的寫法污染另一個專案。同一條 instinct 出現在多個專案且信心夠高時，可以提升到全域；`/evolve` 再把相關 instinct 聚成 skill、command 或 agent。觀察靠 hook。Skill 文件裡的背景分析 agent 預設是關的，要另外打開才會分析。Windows 原生上，observer 與 memory vault 寫入有官方標出的未解缺陷。這套學習我們尚未實測。

### 寫的人與審的人分開 context

`/code-review` 把審查交給 code-reviewer，用另一份 context 找回歸與盲點。官方要留下的是一條證據：計畫、失敗測試、通過測試、審查發現、最後驗證。人可以在實作前改計畫，也可以依審查結果補回歸測試。

### 掃描 harness 自己的門與鑰匙

AgentShield 掃的是 agent 設定：prompt、hooks、MCP、權限、secrets、agent 與 skill 檔。`/security-scan` 是工作流程說明；實際掃描需要已經安裝、且由使用者自己審過的 `ecc-agentshield`。另有 GateGuard，在破壞性 shell 指令執行前擋下。我們沒有跑過 AgentShield。官方列出的檢查項目是產品說明，尚未確認實際效果。

### 跨 harness 的交接筆記

Memory Vault 用可讀的 Markdown 保存持久 context 與 handoff。專案與團隊記憶放在 `.ecc/memory/`，個人記憶放在 `~/.ecc/memory/`。交接要標明從哪個 harness 交給哪個 harness。官方把每一筆記憶定義成未審核的 context：不能當可執行政策，重要內容要對照權威來源，人認可後才寫進受治理的專案文件。只裝 skill 或 Claude plugin 時，這個 CLI 不會自動出現在 PATH。

## 主打賣點

- 它想被記住的是：裝一次，agent 就帶著整條工程環工作，對話裡只留當下需要的東西。
- 規劃、測試先行、專門審查角色和 slash 入口，是我們已經從 [gstack](https://github.com/garrytan/gstack) 與 [mattpocock skills](https://github.com/mattpocock/skills) 理解過的技能包。README 寫到 68 個 agents、293 個 skills，這只說明它是一套很大的協調系統；目錄本身不是功能清單。
- Fresh-context review 仍屬這套審查習慣。gstack 的第二意見是換一個 runtime；mattpocock skills 用雙軸 code-review。ECC 把「寫作者不要用同一份 context 審自己」寫進固定環。這是隔離規則，還不是一種新的員工。
- Instinct 是新原語：重複做對的事變成有信心分數、可分專案、可再進化的行為，人手再寫一份 skill 覆蓋不到這件事。
- AgentShield 是另一個新原語：把 prompt、hook、MCP 和權限當成要掃描的攻擊面。我們沒有實測 AgentShield。
- 「優化 context window，其餘都持久化」是可以單獨拿走的操作規則：技能按需載入、規則慎選、hook 在模型外、記憶和 instinct 不整包塞進對話。

## 使用情境

### 讓同一個 harness 每次走同一條環

- 適合誰：已經用 Claude Code，希望計畫、測試、實作、審查有固定順序的人。
- 在什麼情況使用：開新功能，或修一個可以先寫出失敗測試的 bug。
- 帶來的價值：順序留在已安裝的程序裡。Cursor 或 OpenCode 上是否同等，尚未確認。

### 把這個專案反覆改對的習慣留下來

- 適合誰：同一個 repo 裡，agent 常被糾正同一類寫法的人。
- 在什麼情況使用：希望下次還記得，又不想把這個專案的習慣帶到其他專案。
- 帶來的價值：instinct 預設鎖在專案範圍，跨專案且信心夠高才考慮提升。學習是否真的發生，尚未確認。

### 改完 agent 設定後先看它能碰到什麼

- 適合誰：把專案說明、hooks、MCP 和權限放進 repo 的人。
- 在什麼情況使用：這些設定要提交或交給別人之前。
- 帶來的價值：掃描對象是 harness 的門與鑰匙，發現仍要人決定改不改。我們沒有跑過 AgentShield。

## 我們可以學什麼

ECC 在未來 workspace 裡是共用程序手冊，加上按步驟進場的員工，加上一櫃未審核的交接筆記。Control Pane 那種 session 總覽留在辦公室外面，當值班看板。

- Agents、tasks、goals：目標先落成可改的計畫，再交給測試與實作。審查者是另一個有自己 context 和工具權限的 worker。
- Memory、handoff：session 摘要、instinct、Markdown handoff 分開存放。交接註明來源 harness 與下一個 harness。記憶先標成未審核，人核對後才升成正式規定。
- Permissions：確定性檢查放在 hook，例如破壞性 shell 在執行前被擋下。Codex 的 hook 需要人明確信任。各 harness 的 hook、agent、skill API 不對等。
- 人如何介入：改計畫、看證據軌、決定審查發現要不要補測試、決定哪張交接筆記升格、決定掃描發現要不要修。

**在 2D workspace 裡會變成什麼**

平面辦公室或樓層上，這條環是一條走道：計畫桌、測試桌、實作桌、隔開的審查桌、驗證桌，盡頭是檔案櫃。桌上只放這一步需要的 skill。Instinct 是這個專案房間牆上的少數習慣條；信心不夠的收在抽屜，不貼滿整層樓。Memory vault 是櫃子裡的 Markdown 交接單，封面寫著尚未審核，直到人把它抄上正式牆。AgentShield 是沿這層樓檢查門、鑰匙、hook 和 MCP 插座的人，留下一張評級。

**在 3D workspace 裡會變成什麼**

可走進的辦公室，例如 [Agent Office](https://github.com/AgentSystemLabs/agent-office)，會看到誰在場、人在哪一張桌子。寫程式的人在工作桌；審查的人在另一間，桌上只有計畫、失敗測試、通過測試和 diff，聽不到寫作者剛才的對話。Instinct 是這位員工進門時已經帶上的少數習慣，數量有上限，不會變成額外站一排的人。人可以走到計畫桌改藍圖，或到檔案櫃把筆記升成牆上的規定。AgentShield 像保安沿走廊檢查權限與外掛，留下評級。Control Pane 留在辦公室外，session 列表不建成樓層。

**不值得照搬的地方**

整包目錄同時進場會把 context 塞滿。未審核記憶不能當政策。Cursor、OpenCode 的 adapter 與 Claude Code plugin 的能力差距要分開看。

## 初步看法

- 最有價值的部分：context 要留小、重複行為長成 instinct、harness 設定本身要被審查。
- 最大限制或疑問：完整安裝會把很大的 catalog 廣告給模型。各 harness 支援不對等。背景 instinct 分析在文件預設是關的；實際安裝後是否打開，尚未確認。AgentShield 的實際發現也尚未確認。Windows 原生上 continuous-learning observer 與 memory vault 寫入有官方未解問題。
- 是否值得進一步研究或親自體驗：值得研究 instinct 的專案範圍，以及審查桌如何與寫作桌隔離。不必測完整 skill 目錄。這個筆記沒有實測 AgentShield。

## Sources

- Repository：[https://github.com/affaan-m/ECC](https://github.com/affaan-m/ECC)（README、LICENSE；MIT，Copyright 2026 Affaan Mustafa）
- Website：[https://ecc.tools](https://ecc.tools)
- Continuous learning：[skills/continuous-learning-v2/SKILL.md](https://github.com/affaan-m/ECC/blob/main/skills/continuous-learning-v2/SKILL.md)
- AgentShield（文件，未實測）：README Security 一節、[ecc-agentshield](https://www.npmjs.com/package/ecc-agentshield)
- Platform matrix：README「Platform Support」
