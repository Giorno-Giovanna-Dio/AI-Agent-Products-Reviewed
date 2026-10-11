# Daem0nMCP

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-daem0n-mcp-cell-ca43/reviews/daem0n-mcp.md)
>
> Cell ID：[https://github.com/9thLevelSoftware/Daem0n-MCP](https://github.com/9thLevelSoftware/Daem0n-MCP)
>
> Status：`untried`
>
> Category：長駐 MCP 記憶與治理 daemon
>
> Last updated：2026-10-10

## 產品介紹

Daem0nMCP 是開源的 Python MCP server，坐在 Claude Code 或 OpenCode 旁邊，跨過一次次對話還醒著。它幫的是用 coding agent 改專案、卻不想靠自己記得去翻昨天筆記的人。每個專案的資料放在該專案的 `.daem0nmcp/`。官方要解決的是：agent 每次開新 session 都從空白開始，markdown 要人記得去讀；這個 daemon 在相關題目出現時把過去的決定、規則和失敗送上來，並在改東西之前先擋一下。

使用方式是把它接到既有的 coding agent。進場先聽簡報，動手前先請示，做了決定要寫下來，做完要回報結果好不好。人閒下來時，它會自己回頭看失敗過的決定。本次只讀 GitHub 上的 README、LICENSE、AGENTS.md、changelog、多專案文件，以及 covenant、hook、dreaming 的模組說明，沒有安裝。`pyproject.toml` 的版本是 6.6.6，與 tag `v6.6.6` 一致。`CHANGELOG.md` 開頭停在 6.0.0，和 README 的 6.6.6 小節還沒對上。舊網址 `DasBluEyedDevil/Daem0n-MCP` 會轉到現在這個 repo。

## 主要 Features

### 進場、請示、記下、回報

v5.1 起，官方把原本幾十個 MCP 工具收成八個工作流程：commune（進場與狀態）、consult（動手前的情報）、inscribe（寫記憶與連結）、reflect（結果與核對）、understand（程式理解）、govern（規則與自動召回）、explore（圖譜與時間）、maintain（整理與跨專案）。每個流程用 action 選動作。旁邊另有三個認知工具：用現在的知識重看舊決定、檢查規則是否過時、拿已存的證據做正反討論。這樣 context 裡少一長串工具定義，節奏卻固定。README 寫 59 個動作，changelog 寫 60，數字尚未對齊。

### 沒聽簡報，門先關著

官方把這套紀律叫 Sacred Covenant。協議是一條鏈：進場先拿簡報，改狀態前先做 preflight，決定寫進去，結果再封存。AGENTS.md 寫，跳過簡報時工具會回 `COMMUNION_REQUIRED`；跳過請示時，會改東西的工具回 `COUNSEL_REQUIRED`。原始碼裡的 covenant 仍用舊工具名描述同一條鏈，請示憑證預設約五分鐘。整合後的 workflow 動作是否每一個都接到這道閘，尚未逐一核對，也尚未實測。

Claude Code 的 hook 是另一層在場。原始碼說明寫：session 開始帶簡報；編輯前若沒有近期請示就擋下，並把該檔案相關的記憶送回來；命中 `must_not` 的 shell 會被擋，一般警告只是附帶上下文；改完後建議把重要變更記下來；session 結束時試著從對話捕捉決定。Git pre-commit 的官方說明是擋下還掛著未結決定的提交。這些都還沒跑過。

### 記憶會衰、會衝突、會交到檔案上

記憶分成 decision、pattern、warning、learning。決策和學習會隨時間變淡（預設半衰期約 30 天），pattern 和 warning 留著。寫入時會對近期記憶做衝突檢查，以前失敗過的類似做法會被提出來。失敗結果在之後的召回裡加權。大約十筆可以釘成一直熱著的工作記憶，進簡報。記憶可以掛到檔案或程式實體，也可以彼此連成因果。官方設定裡，成功結果累積到門檻（預設三次）可以升成較穩的 fact。召回同時用關鍵字和向量。Auto-Zoom 會依問題難度選快路徑、混合搜尋或圖譜；設定表裡主開關預設關閉、shadow 預設打開。它現在會不會真的改走哪條路，尚未確認。

### 人離開以後，daemon 還在

閒置一段時間（預設約 60 秒沒有工具呼叫），背景 dreaming 會重看失敗過的決定，把心得存成帶 dream 標記的 learning，人一回來就讓出。檔案有變動時，可以經桌面、log 或編輯器輪詢通知。每個專案的庫在 `.daem0nmcp/storage/daem0nmcp.db`。多個 repo 可以各存各的再互相連結，也可以收到同一個父目錄。簡報、搜尋、誓約狀態、叢集和記憶圖有 MCP Apps 的 HTML 畫面；沒有視覺宿主就退回文字。那些畫面是 daemon 自己的螢幕。

規則引擎可以在行動前給出硬限制。reflect 裡有一個在沙箱跑 Python 的動作，官方寫走 E2B。那是被允許的執行範圍。沒有額外服務時沙箱是否仍可用，尚未確認。OpenTelemetry 是選用套件。

## 主打賣點

- 它要人記住的是一份會擋門的記憶：daemon 一直在場，進場要聽簡報，改動前要請示，失敗要回報，下次更容易被翻出來。
- 和 gbrain、Hindsight 這類記憶庫相同的是跨 session 還能召回。Daem0nMCP 多出來的是紀律：簡報和請示寫進協定，寫入與變更可以被擋下，結果會改下一次的排序。
- 八個 workflow、新的向量模型、圖譜、壓縮、HTML 圖表，是同一套「記住、召回、治理」的加深。Auto-Zoom 在預設設定下尚未成為正式路線。`.claude-plugin/plugin.json` 仍寫 2.6.0，和套件 6.6.6 尚未對齊。

## 使用情境

### 新的 coding session 接上昨天的決定

- 適合誰：用 Claude Code 或 OpenCode、不想靠自己記得去讀筆記的人。
- 在什麼情況使用：新 session 開始，先拿簡報（近期決定、警告、失敗做法、git 變化），再改檔。
- 帶來的價值：交接發生在進場。昨天的結論不用等人貼上。

### 同一個檔案不要再踩同一個坑

- 適合誰：會反覆改同一區程式的人。
- 在什麼情況使用：動手前記下做了什麼、為什麼、結果好不好，並把記憶掛到檔案。
- 帶來的價值：下次碰到類似做法，失敗會被提起來；警告留得比一般決策久。

### 多個 repo 共用決定，仍看得出歸屬

- 適合誰：前後端拆開、卻要同一套架構決定的人。
- 在什麼情況使用：各 repo 自有資料庫並互相連結，或收到父目錄的單一庫。
- 帶來的價值：召回可以跨專案，卡片仍標著是哪一倉的。實際查到的範圍尚未實測。

## 我們可以學什麼

- 值得借鑑的 product idea：長駐的 MCP 是辦公室裡不下班的書記。訪客 agent 來上班、下班；書記留在原位，保管決定、規則、失敗，以及還沒回報的結果。
- 值得借鑑的 interaction / workflow：進門先領簡報，否則後面的門關著。改檔或下指令前先請示，請示有時效。決定寫成卡片，掛到檔案上。做完走回來蓋章：成或敗。敗的卡片下次更顯眼。人走了，書記在空檔重讀失敗卡，留下帶出處的新筆記。桌上只留一小疊正在用的熱記憶，整櫃檔案不要倒出來。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層。Daem0n 是固定櫃檯，名牌一直亮著，就算沒有 coding agent 在場。新來的 agent 先停在櫃檯領簡報包（警告、失敗做法、近期決定、這份 git 變化），領了才進得了工位。工位旁的抽屜貼著檔案路徑；請示就是打開那一抽，看 `must_not` 有沒有貼在抽屜上。決定是寫好的卡片，從 agent 的桌子交到櫃檯，再歸到對應檔案。結果章蓋在同一張卡上。還沒蓋章的卡留在櫃檯的待辦夾，提交前可以被擋下。樓層安靜時櫃檯的燈還亮，書記在重看失敗卡。連結的專案是鄰房，書記可以走過去取筆記，筆記仍標著哪一房。MCP Apps 的圖表和簡報是櫃檯上的螢幕。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你進門就看見書記還坐在原位，即使今天沒有人開著 Claude Code 或 OpenCode。訪客 agent 在自己的工位寫程式，進門要先走到書記桌前；簡報沒領到，後面的門是鎖的。請示是走到那份檔案的櫃子前，書記把相關卡片遞過來；`must_not` 是櫃子上的鎖。交接看得到：卡片從訪客手上交到書記，再放進標著路徑的抽屜；做完訪客走回來蓋結果章。那一小疊熱記憶放在書記桌面，多的放回櫃裡。人離開後，書記仍獨自坐著翻失敗卡，新寫的學習條帶著 dream 出處。隔壁翼的連結專案有自己的櫃子；書記可以去取，你看得出卡片從哪一翼來。重點是誰在場、誰在哪裡交接。
- 需要重新設計的地方：儀式用語和一整份 action 清單留在文件裡。樓層和房間只保留進場、請示、交卡、蓋章、上鎖。圖譜和簡報 HTML 留在書記的螢幕。workflow 名稱和舊 covenant 工具名是否完全接上，以及 Auto-Zoom、沙箱在沒有額外服務時是否生效，都尚未確認，先不要寫死成空間規則。

## 初步看法

- 最有價值的部分：一個不下班的在場者，用簡報、請示、結果回報把記憶變成交接。
- 最大限制或疑問：它接在現有 coding agent 上。閘門是否對每個新 workflow 動作都生效，要跑過才知道。套件 6.6.6、changelog 停在 6.0.0、plugin.json 2.6.0，三套版本敘事尚未對齊。
- 是否值得進一步研究或親自體驗：值得，尤其是人走了 daemon 還在，以及請示的鎖在 2D／3D 裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Repository：https://github.com/9thLevelSoftware/Daem0n-MCP （舊網址 https://github.com/DasBluEyedDevil/Daem0n-MCP 會轉到這裡）
- GitHub Pages：https://9thlevelsoftware.github.io/Daem0n-MCP/
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright 2026 DasBluEyedDevil）
- 套件：`pyproject.toml` 版本 6.6.6，Python >=3.10，入口 `daem0nmcp`
- README（`main`）：八個 workflow、誓約、記憶衰減、dreaming、Claude Code hooks、OpenCode、專案資料目錄
- 亦讀：`AGENTS.md` 協議、`CHANGELOG.md`（寫到 6.0.0）、`docs/multi-repo-setup.md`、covenant／hook／dreaming 的模組說明
- 本次閱讀的 `main` commit：`00809c67c03938014ac3ea470ef3600f7ccebabc`（2026-08-08）。沒有安裝，沒有執行。
