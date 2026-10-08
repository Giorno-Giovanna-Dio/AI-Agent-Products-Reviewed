# Oh My OpenCode

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-opencode-cell-103e/reviews/oh-my-opencode.md)
>
> Cell ID：[https://github.com/opensoft/oh-my-opencode](https://github.com/opensoft/oh-my-opencode)
>
> Status：`untried`
>
> Category：OpenCode 外掛（多角色編制）
>
> Last updated：2026-10-08

## 產品介紹

Oh My OpenCode 是裝在 [OpenCode](https://github.com/sst/opencode) 上的外掛。
它把一次程式開發，編成一間小公司：有人訪談、有人寫計畫、有人派工、有人寫
程式、有人只負責查資料。使用者留在終端機裡當主管，用一句話開工，或先走進
規劃模式把範圍談清楚。

這份 Cell 看的是 `opensoft/oh-my-opencode` 的 `dev` 分支。它是
[code-yeongyu/oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent)
的 fork，GitHub 比對顯示 **0 個自己的提交**，最後停在 2026-01-25 的
`aead4ae`（tmux 背景窗格）。上游已改名並繼續開發；這份筆記只描述這個
快照裡的編制。尚未安裝，也尚未執行。

## 主要 Features

### 兩種開工方式

短工作在提示裡加上 `ultrawork` 或 `ulw`。預設主責是 Sisyphus，自己探路、
分派背景任務，並靠待辦清單把工作做完。

較大的工作按 Tab 進入 Prometheus。它先訪談、查程式庫，再寫出計畫。人確認
後執行 `/start-work`，交給 Atlas 派工。文件寫明 Prometheus 和 Atlas 要成對
使用；單獨叫 Atlas、手上沒有計畫，行為會不穩。這點尚未實測。

### 三層編制

規劃層是 Prometheus（訪談與寫計畫）、Metis（計畫寫出前找漏掉的意圖、邊界
和驗收條件）、Momus（高準確模式底下，計畫不夠具體就退回）。

執行層是 Atlas。它讀計畫、決定哪些任務可以並行、把累積下來的做法傳給下一位，
並自己檢查結果。文件要求它把寫程式、修 bug、寫測試和 git commit 交給別人。

做事的人裡，Sisyphus-Junior 負責實作，而且不能再往下委派。Oracle 只給架構
和除錯建議，不能改檔。Librarian 查文件和其他專案，Explore 在程式庫裡快速
搜尋，multimodal-looker 讀圖片和 PDF。探索和查文件的人把摘要交回來，主責的
上下文才不會被整份原始碼塞滿。

### 用工作種類選人

派工時用類別描述這件事要什麼能力，例如畫面、深度推理、快速小改、寫文件，
而不是在提示裡點名某一個模型。類別背後有一條供應商候補順序；安裝時會依你
手上的 Claude、OpenAI、Gemini 等訂閱寫進設定。技能則是額外貼上的領域說明，
例如瀏覽器操作或 git 提交習慣。

同一份快照裡，README、編排文件和 `src/agents/AGENTS.md` 給同一角色的模型
名稱並不一致。模型指派應視為會過期的設定，角色分工才是穩定的部分。

### 計畫與共用筆記本

計畫寫在 `.sisyphus/plans/`。每個計畫還有筆記本：學到的慣例、決定、問題、
驗證結果。下一個任務開始前要讀過這些筆記，避免同一種錯再犯一次。跨 session
可以從計畫接著做。尚未確認這些檔案在中斷後實際能恢復到什麼程度。

### 做完才算，而且要親自查

Junior 若留下未完成待辦，系統會把它送回去做完。Atlas 不採信「我做完了」：
文件要求它看診斷、跑測試、讀實際改過的檔案。背景任務可以並行，這個快照還
能把背景 session 開成 tmux 窗格。那是終端機裡多看幾塊畫面，仍然是同一台
終端機。

權限按角色切開：顧問不能寫檔，Junior 不能再派工，看圖的人基本上只能讀。
這些限制寫在 agent 定義裡。實際執行時會不會被繞過，尚未確認。

## 主打賣點

- 它想被記住的是：你是主管，agent 是一組有職務的同事，而且工作要做完。
- 真正不同的地方是編制，不是再包一層聊天。規劃、指揮、實作、顧問彼此不能
  互換；指揮的人不寫程式，寫程式的人不能再分身。
- `ulw` 是把整套編制收成一個關鍵字。關鍵字本身是舊的「把 prompt 變長」的
  另一種開關；有價值的是它背後那組角色、計畫和驗收。
- 依模型強項分工、Claude Code 相容的指令與 hooks、LSP／語法搜尋，都是把
  別的 harness 裡已有的能力收進 OpenCode。這個快照的差異在於誰該用哪一種
  能力，以及結果要怎麼交回去。

## 使用情境

### 一句話就把小功能做完

- 適合誰：已經在 OpenCode 裡寫程式、不想先開一份長規格的人。
- 在什麼情況使用：範圍清楚的小改動。
- 帶來的價值：Sisyphus 自己找檔案、把搜尋丟給較輕的同事，並用待辦把工作
  留到做完。

### 先訪談，再讓指揮照計畫派工

- 適合誰：改動會跨很多檔案、或要留一份可恢復決策的人。
- 在什麼情況使用：多日工作、上線風險高、或希望有驗收條件再動手。
- 帶來的價值：Prometheus 把模糊需求問成計畫，Momus 可以退回不夠具體的版本，
  Atlas 再按計畫派工並自己查結果。

### 卡住時只請顧問，不讓顧問改碼

- 適合誰：主責 agent 在同一個錯誤裡打轉的人。
- 在什麼情況使用：需要第二種看法，但不希望顧問直接改檔。
- 帶來的價值：Oracle 只能建議。改碼仍回到執行層，並要經過驗證。

## 我們可以學什麼

- 值得借鑑的 product idea：一間小公司要有職務，不要只有「再叫一個
  subagent」。規劃、指揮、實作、搜尋、顧問是不同座位。
- 值得借鑑的 interaction / workflow：人先在規劃室把計畫釘在牆上，確認後才
  走進工作區。指揮讀牆上的計畫和走廊的筆記本，再決定誰同時開工。做完的
  宣告要由指揮親自看過畫面才算數。
- 在 2D workspace 裡會變成什麼：一層平面辦公室。一間規劃室裡坐著
  Prometheus、Metis 和 Momus，牆上釘著 `.sisyphus/plans`。門外工作區的
  Atlas 站在指揮位，不坐在鍵盤前。Junior 只在被派到的桌子出現。Explore 和
  Librarian 在檔案室，把摘要送回指揮桌，避免指揮桌堆滿原始碼。Oracle 的
  座位沒有編輯權。`ulw` 則是規劃室空著、工作區直接開工；2D 應把這個模式
  寫在門口，而不是藏在一句話裡。tmux 窗格在這裡是每張桌子上的螢幕。
- 在 3D workspace 裡會變成什麼：可以走進的同一間辦公室。人進規劃室回答
  訪談，看見 Momus 把計畫退回。確認後走到工作區，看見 Atlas 在指揮、Junior
  坐在被分到的位子、顧問室的人不能碰程式碼。走廊檔案櫃就是筆記本，下一位
  員工進門前先讀。未完成的人被送回座位，而不是從畫面消失。這是在場與交接，
  和 [Agent Office](https://github.com/AgentSystemLabs/agent-office) 同一類
  空間。OpenCode 終端機、tmux 分格、或並列的 agent 紀錄，仍然是工作檯。
- 不值得照搬或需要重新設計的地方：把編制藏進 `ulw`；把會過期的模型名稱刻成
  員工身份；讓「做完才准停」在空間裡沒有可見的停下方式。指揮和實作者必須
  是兩個能看見的人。

## 初步看法

- 最有價值的部分：職務拆開，加上牆上的計畫和指揮親自驗收。
- 最大限制或疑問：這份 fork 沒有自己的提交，落後已改名的上游非常遠。角色
  名單和模型表只代表 2026-01 的快照。`ulw` 和 `/start-work` 的實際手感、
  權限是否真的關得住，都尚未使用過。
- 是否值得進一步研究或親自體驗：編制值得留。若要體驗現在的產品，應另看
  上游 [oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent)，
  不要把這個 fork 當成現行版本。

## 後續補充

### 這份來源在產品族譜裡的位置

| 項目 | 這次看到的事實 |
| --- | --- |
| Cell ID | `https://github.com/opensoft/oh-my-opencode` |
| 預設分支 | `dev` |
| 與此 fork 最後重合的提交 | `aead4ae`，2026-01-25，訊息為背景 agent 的 tmux 窗格 |
| 相對上游 `dev` | ahead 0，behind 約 18948（比對當下的數字，之後還會變） |
| 上游現名 | [code-yeongyu/oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent)；舊網址 `code-yeongyu/oh-my-opencode` 會指向這個改名後的 repo |
| 授權 | Sustainable Use License 1.0。個人與內部使用、以及免費的非商業散佈；不是常見的 MIT 式授權 |
| 官方曾警告 | README 寫 `ohmyopencode.com` 與專案無關。尚未核對該網站現況 |

上游後續的介面（文件裡已出現的 Senpi TUI 等）不在這份筆記的範圍。

## Sources

- Repository（本 Cell）：[opensoft/oh-my-opencode](https://github.com/opensoft/oh-my-opencode)
- 快照 README：[dev/README.md](https://github.com/opensoft/oh-my-opencode/blob/dev/README.md)
- 編排說明：[understanding-orchestration-system.md](https://github.com/opensoft/oh-my-opencode/blob/dev/docs/guide/understanding-orchestration-system.md)
- 總覽：[overview.md](https://github.com/opensoft/oh-my-opencode/blob/dev/docs/guide/overview.md)
- 角色清單：[src/agents/AGENTS.md](https://github.com/opensoft/oh-my-opencode/blob/dev/src/agents/AGENTS.md)
- 上游（未納入本次產品內容）：[code-yeongyu/oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent)
