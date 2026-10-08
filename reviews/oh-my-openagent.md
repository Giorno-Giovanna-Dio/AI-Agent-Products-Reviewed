# OmO

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-openagent-cell-90bd/reviews/oh-my-openagent.md)
>
> Cell ID：[https://github.com/code-yeongyu/oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent)
>
> Status：`untried`
>
> Category：Multi-model orchestration layer
>
> Last updated：2026-10-08

## 產品介紹

OmO（repository 名稱是 oh-my-openagent）是一層多模型編排手續，加在既有的 coding harness 上。人用一句話或一個關鍵字把工作交出去；主 session 負責拆工、派工、收證據。真正改檔案的是按工作類型叫出來的暫時工人。

目前 README 主推的入口是終端機指令 `omo`。它跑在專案自己 fork 的 [pi](https://github.com/badlogic/pi-mono)（叫做 senpi）上，編排已經內建。同一套手續也可以當外掛放進 OpenCode；文件另有 Codex（LazyCodex）版，並可與原生版並存。從 OpenCode 或 LazyCodex 過來時，`omo setup` 把供應商金鑰、MCP、skills 和模型選擇帶進 `~/.omo`。這份筆記只談這一層編排。

## 主要 Features

### 關鍵字切換手續：`ulw` 與 `mass ulw`

IntentGate 只認 prompt 裡寫明的關鍵字，再注入對應的模式說明。打 `ulw` 或 `ultrawork`，主 agent 會先讀專案、自己做筆記、把工作派出去、用證據檢查，然後一直做到它認為可以停。

`mass ulw`（文件裡寫 `mass-ulw`）用在「有些任務必須等別的任務做完」。主 agent 列一張工作依賴表：每個節點有 id、要做的事、category、`dependsOn`。`workflow` 工具一次跑一個階段；下一階段是另一次 run。失敗的節點可以用 retry、send 或 amend 補救，已經做完的不必重跑。`/dag` 打開這張表的細節。README 說的 graph engineering，指的就是這張「誰先做、誰等誰」的任務表，以及官網示意的一波一波 agent。圖裡右側的 Herdr 窗格來自另一個專案 [omo-herdr-dag](https://github.com/jc01rho/omo-herdr-dag)。

### 按工作類型派工

主 session 不把椅子讓給別人。它用 `task` 工具派工。實作帶 category，例如 `architect`、`visual-engineering`、`ultrabrain`、`deep`、`quick`、`writing`，以及高低兩檔的 `unspecified`。每個 category 對到一條模型後備鏈和該用的 skills，並開一個乾淨的 worker session，做完把結果交回。研究與審計畫則叫四個唯讀專員：`explore`（在程式庫裡找）、`librarian`（查文件與外部範例）、`plan-consultant`、`plan-reviewer`。後兩個只在你明確走 `/ulw-plan`、而且還沒執行時才叫得出來。

人不必在 prompt 裡寫模型 id。連不上的 category 會被標成不可用。文件裡的具體模型名字變動很快，這次沒有核對它們是否都能連上。

### 先訪談、再執行、斷線可以接著做

`/ulw-plan` 讓同一個主 agent 先當規劃者。它先用唯讀研究摸專案：意圖清楚才問你真正要拍板的事；意圖模糊就採用它宣布的預設。你點頭之後，計畫才寫進 `.omo/plans/*.md`。寫之前由 plan-consultant 找缺口，寫完由 plan-reviewer 多輪檢查，只打能指出來的阻擋項。

`/ulw-execute` 在同一 session 執行。主 agent 只做選計畫、拆工、派工、收 verdict 和記證據，產品程式交給工人。每個勾選項要留下計畫重讀、自動驗證、手動 QA、對抗式 QA 和清理收據。工人說做完不夠，要另一個 reviewer 點頭，`- [ ]` 才改成 `- [x]`。進度在 `.omo/boulder.json`，證據在 ledger。session 斷了，再下一次 `/ulw-execute` 會從剩下的勾選項繼續。`--make-pr` 把該階段的 worktree 交成 pull request；`--ship` 會留到 PR 合併。

單純打 `ulw` 不會叫那兩個審計畫的專員，它只在自己的筆記本裡自評。

### Team mode：同時做、中途要說話

預設關閉。幾條線會碰到同一塊程式、做的過程需要互相通報時才開。主 session 當 lead，`team_create` 開最多 8 個成員（預設同時 4 個），共用信箱和任務清單。可以選擇每人一個 git worktree，也可以選擇 tmux 窗格看即時輸出。信箱發出就走，不等回覆。唯讀專員不能進團隊，仍走 `task`。

和 `mass ulw` 的差別在動線：`mass ulw` 是有先後的階段；team mode 是重疊的座位，中途用信箱交換發現。

### Kibitzer：便宜模型在旁邊提醒

記憶放在一份 git 管理的 markdown。每個主 session 旁有一個唯讀的小模型。它只在還沒判斷過的記憶被觸及時才醒來，讀工作區、對話和記憶，唯一動作是 nudge 主 agent。它不改檔，也不寫記憶。`/search` 是人手動回想的入口。這套行為來自文件，尚未確認實際提醒是否準。

### 權限、驗收與人可以插手的地方

`AGENTS.md` 是給 agent 看的說明。文件寫明的硬限制是設定裡的 permission、工具允許清單、唯讀專員的工具表，以及 guard hooks。宿主是 OpenCode 時，還可以走到 OpenCode 自己的 permission gate。背景 agent 繼承 session 的工作目錄，但模型之後下的 shell 不被 OmO 鎖在那個目錄裡。

原生 `omo` 沒有把 OpenCode 的工具權限一起搬過來。原生版有沒有獨立沙箱，尚未確認。實驗性的 computer use 讓 agent 讀螢幕與無障礙樹，並在背景點擊；停止和弦會暫停，只有人能讓它繼續。

## 主打賣點

- 它想被記住的是：人只講想要什麼。`ulw` 自己做完，`/ulw-plan` 先問清楚再寫計畫，`mass ulw` 把整份工作變成一張有先後的任務表。官方宣言把人中途接手當成系統失敗。
- 和單模型、單 session 的 coding agent 相比，差在領班手續：category 派工、階段依賴、書面計畫、獨立驗收、跨 session 的 boulder。主 session 像領班員工在執行這套團隊手續。
- 多模型混用、平行 tool call、修好壞掉的參數、用訂閱代替輪詢、skills 按需載入，是 agent loop 上的打磨。官網把這些和「取消 Cursor」「一小時做完別人七天的工作」放在一起，效果尚未確認。
- `/dag`、可選的 tmux 窗格，以及 README 圖裡的 Herdr 側欄，是這套手續旁邊的進度板。

## 使用情境

### 一句話交給領班，人先離開

- 適合誰：已經在用 OpenCode 或 Codex CLI，或願意改跑原生 `omo` 的人。
- 在什麼情況使用：任務講得清楚，但不想自己拆步驟。在 prompt 加上 `ulw`。
- 帶來的價值：研究、實作、驗證分成不同工人；人回來看證據，而不是看每一則工具呼叫。

### 大改動先留下書面計畫

- 適合誰：要改既有系統、想留下決策紀錄的人。
- 在什麼情況使用：`/ulw-plan` 訪談並點頭，再 `/ulw-execute`。中間可以離開，下次同一條指令接著做。
- 帶來的價值：範圍在 `.omo/plans`，驗收在 ledger。交出去的是 PR，勾選項沒過就不算做完。

### 步驟有先後，或幾條線要中途對齊

- 適合誰：研究、遷移，或會互相卡住的平行改動。
- 在什麼情況使用：有依賴就打 `mass ulw`。會改到同一處、而且需要中途通風報信，就開 team mode。
- 帶來的價值：下一階段還沒輪到時，門是關著的。需要同時說話時，lead 用任務清單和信箱協調，不必把所有上下文塞回同一段 prompt。

## 我們可以學什麼

- 值得借鑑的 product idea：
  - 編排是領班在執行的團隊手續。主桌握有計畫、待辦和證據；工人按工作類型臨時出現，做完把結果交回。
  - 人用關鍵字選手續：自己做完（`ulw`）、先訪談（`/ulw-plan`）、有先後的任務表（`mass ulw`）、要中途說話的小隊（team mode）。
  - 記憶是領班旁邊的書記（Kibitzer），只提醒、不改檔。
- 值得借鑑的 interaction / workflow：
  - 派工說「這是哪一種工作」，模型鏈藏在 category 後面。
  - 工人說完成不算完成。獨立 reviewer 看過證據，勾選才翻面。
  - 計畫檔和 boulder 讓人離開再回來時，同一份工作還在桌上。不可逆、會花錢、會刪東西的決定仍要人點頭。
  - 官方把人中途接手當成失敗訊號。空間辦公室仍要留一扇能走進工位的門，預設動線則是委派之後看驗收。
- 在 2D workspace 裡會變成什麼：
  - 平面辦公室的主桌是主 session。category 工人是地圖上暫時亮起的工位：研究角、視覺角、快速修改角，這一波結束就收掉。
  - `mass ulw` 是樓層上的先後房間。第一波沒有把證據放到門口，下一波的門不開。人站在主桌說 `mass ulw`，或坐下來回答 `/ulw-plan` 的提問。
  - Team mode 是同一層樓裡可以傳紙條的幾張桌子。Kibitzer 是主桌旁的小櫃檯，檔案櫃是 git 裡的 markdown，只在領班要重複舊錯時敲一下桌子。
  - `/dag` 和 tmux 窗格掛在牆上當進度板。人要插手時回到主桌回答問題，或走到某個工位看那一輪的證據與 diff。
- 在 3D workspace 裡會變成什麼：
  - 可走進的辦公室裡，領班坐在主桌。你進門先看到誰在場、哪一波工位亮著。`visual-engineering`、`deep`、`quick` 是不同的專業座位，只在有任務時有人坐著。
  - `mass ulw` 的依賴是走廊裡的房間順序。走到下一間之前，上一間桌上要有驗證收據。這是工作怎麼交接。
  - 唯讀專員的座位沒有筆。Team mode 的人用桌上的信箱交換發現，只有 lead 能對全員廣播。
  - 人的介入是走到主桌，回答只有人能拍板的問題；computer use 進行時可以按下停止，再由人決定是否繼續。做完的包裹放在走廊盡頭，那是 PR。
  - 隔天回到同一張主桌，boulder 和計畫檔還在，領班從下一個勾選項繼續。Kibitzer 站在領班側後方，不進別人的座位改東西。
- 不值得照搬或需要重新設計的地方：
  - 宣言想把人移出迴圈。驗收失敗時，辦公室需要一個站得進去的工位，否則沒有位置插手。
  - 權限目前主要是工具允許清單和宿主的 gate。背景 shell 可以寫到 repo 外面。2D／3D 辦公室要另外標出哪些座位能碰檔案、螢幕和憑證。原生版沙箱尚未確認。
  - 官網和 README 的口號很滿。workspace 應顯示階段、證據和誰在等誰。

## 初步看法

- 最有價值的部分：一次大工作被拆成領班、按類型出現的工人、書面計畫、獨立驗收，以及一張有先後順序的任務表。這可以直接變成辦公室裡的波次和座位。
- 最大限制或疑問：公開文件的行銷語氣很重，模型名單變動快，computer use 標成實驗，team mode 預設關閉。`mass ulw` 實際跑起來的依賴表、失敗恢復，以及人中途插手的手感，都尚未確認。原生 `omo` 與 OpenCode 外掛是否同一套權限，文件只說明 OpenCode 的工具權限不會被搬過去。
- 是否值得進一步研究或親自體驗：值得。若要做「領班委派、工人按波次出現、驗收才算完成」，應先看 `/ulw-plan`、`/ulw-execute` 和 `mass ulw` 的任務表。附帶的 skills 清單不必先收齊。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：https://omo.dev
- Repository：https://github.com/code-yeongyu/oh-my-openagent （預設分支 `dev`）
- Documentation：https://omo.dev/docs
- README（dev）：https://github.com/code-yeongyu/oh-my-openagent/blob/dev/README.md
- Orchestration：https://github.com/code-yeongyu/oh-my-openagent/blob/dev/docs/guide/orchestration.md
- Features：https://github.com/code-yeongyu/oh-my-openagent/blob/dev/docs/reference/features.md
- Team mode：https://github.com/code-yeongyu/oh-my-openagent/blob/dev/docs/guide/team-mode.md
- Migrating from OpenCode：https://github.com/code-yeongyu/oh-my-openagent/blob/dev/docs/guide/migrating-from-opencode.md
- Manifesto：https://github.com/code-yeongyu/oh-my-openagent/blob/dev/docs/manifesto.md
- License：https://github.com/code-yeongyu/oh-my-openagent/blob/dev/LICENSE.md （正文符合 SPDX `SUL-1.0`；第三方元件沿用各自授權。GitHub API 先前把 license 標成 Other／NOASSERTION）
