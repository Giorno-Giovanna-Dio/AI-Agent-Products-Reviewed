# T3 Code

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-t3code-cell-7aa3/reviews/t3code.md)
>
> Cell ID：[https://github.com/pingdotgg/t3code](https://github.com/pingdotgg/t3code)
>
> Status：`untried`
>
> Category：Agent harness 控制面（桌面／Web／行動遠端操控本機 CLI agents）
>
> Last updated：2026-10-06

## 產品介紹

T3 Code 是 T3 Tools 開源的 **agent harness 控制面**：不在雲端替你跑 model，而是
連到你電腦上已登入的 **Claude Code、Codex、Cursor CLI、Grok Build、OpenCode、
Antigravity** 等 provider，用同一套 UI 開 thread、看 diff、批核權限、管 worktree
與 PR。執行、Git、終端機與 provider 憑證都留在 **擁有 workspace 的那台機器**
（server／environment）；Web、Electron 桌面與 iOS／Android app 只是透過驗證過的 RPC
遠端操控，不會用客戶端自己的檔案系統或訂閱冒充主機。

典型流程是在本機或遠端主機跑 `t3`／桌面 app 當 server，再從手機、瀏覽器或另一台
電腦配對連線（LAN、Tailscale、SSH、或官方的 **T3 Connect** 隧道）。專案早期、
MIT 授權，團隊明說靈感來自 Codex desktop、Conductor、Claude Desktop、Cursor Glass，
但想要更輕、更遠端、且 fork 友好。

## 主要 Features

### 多 provider、本機訂閱驅動

只要主機上已安裝並登入對應 CLI，T3 Code 就能用 adapter 正規化指令與事件，不必
綁單一 vendor。新增 provider 的設計目標是改 adapter，而不是散彈式改整個 domain。

### 跨平台 client + 遠端連線

同一個 environment 可被 **桌面（含 winget／Homebrew／deb／AUR）、Web
（app.t3.codes）、行動 app** 控制。**T3 Connect** 免自行 port forward；
也可 LAN 配對、Tailscale HTTPS、或桌面管理的 **SSH**（在遠端自動下載 server
runtime）。一台機器可掛多條 route，client 依速度排序自動 failover；也可關閉
本機 environment，讓某台電腦純當「遠端遙控器」。

### Thread、worktree 與 PR 生命週期

文件與架構描述以 **thread** 為單位串起對話、run、checkpoint 與 settlement。
Worktree 可自動命名（前綴、語意前綴或自訂 prompt）；checkpoint 用 hidden Git ref
快照 workspace，而不污染使用者 branch。PR 連結有新舊 capability 協商，避免
client／server 版本不一致時誤呼叫；merge 後有 server 端的 settlement／PR sync
邏輯，減少「agent 已 merge 但 UI 還以為 open」的落差。

### 權限模式與 human-in-the-loop

每個 thread 可選 **Supervised、Auto-accept edits、Auto、Full access**；預設與
專案 override 分層管理。不同 provider 對「自動審核」支援不一（例如 Grok 沒有
Auto-accept edits），T3 在邊界上轉成統一的批准 UX，而不是假裝所有 CLI 行為一致。

### 多機與負載偏好

連上兩台以上 environment 時，可開 **Auto balance**，依 CPU／記憶體偏好把**新 thread**
派到較閒的機器；已存在的 thread 不搬家。這是把「fleet of dev machines」當成
資源池，而不是單機 ADE。

### 自動化：排程與 Webhook

行動端可管理跨 environment 的 **scheduled tasks**；桌面／Web 可建 **webhook**
觸發（例如 GitHub PR 事件），prompt 用 placeholder 吃 JSON body。需對外 URL 時
依賴 T3 Connect；可選離線暫存 webhook（最多 24 小時，有隱私取捨）。

### 設定繼承與 `t3.json`

Environment 預設、專案 override、repo 內 **`t3.json`** 三層解析（例如新 thread
workspace、worktree submodule 策略、actions 列表）。Web／桌面用「Applying settings
for …」明確標示 bulk edit 範圍，避免以為改的是全域預設。

## 主打賣點

- **遠端優先的控制面**：官方把行動 app 與 T3 Connect 當一等公民，目標是「agent
  跑在桌機、人在任何地方介入」，而不只是本機多視窗。
- **開源 MIT + 可自架 server**：相較閉源 ADE，文件強調若方向走偏，使用者仍有
  fork 材料；RPC contract 與 event-log orchestration 寫得偏長期演進。
- **Execution locality 清楚**：remote client **不得**替換 environment 的 filesystem／
  credentials——這和「把 repo 同步到雲端 IDE」是不同安全與心智模型。
- **Conductor 同類、但更跨平台、更偏 harness**：平行 worktree、多 runtime、PR
  整合這些 ADE 敘事都有，但 T3 不賣 macOS-only workspace，而是 **CLI harness +
  任意 client**；與 [Conductor](conductor.md)、[Orca](orca.md) 等並列時，差異在
  **遠端連線深度、開源、以及 provider 中立 adapter**，不是 3D／pixel office。
- **包裝 vs 新能力**：多 provider 支援、權限模式、worktree 命名在業界已不罕見；
  較少見的是 **route 學習（LAN／Tailscale 自動追加）**、**版本協商的 PR 多連結
  protocol**、以及 **webhook 離線 hold** 與 Connect 整合。

## 使用情境

### 通勤或離桌時批核 agent

- 適合誰：主機常開 long-running agent、又不想綁在螢幕前的人。
- 在什麼情況使用：桌機跑 `t3 service`，手機用 T3 Connect 或已配對 route 連同一
  environment。
- 帶來的價值：權限請求、follow-up、PR 狀態在 pocket 裡處理，執行仍在本機訂閱
  額度內。

### 多台開發機當 agent 池

- 適合誰：家裡桌機 + 筆電 + 遠端 Linux box 都有 provider CLI 的使用者。
- 在什麼情況使用：全部加入 Connections，開 Auto balance 或手動選 machine 開新
  thread。
- 帶來的價值：把算力與 checkout 分散，client UI 統一，不必 SSH 手動開五個 tmux。

### CI／GitHub 事件驅動 agent

- 適合誰：想讓 PR 開啟、CI 失敗等事件自動 spawn 一輪 review／fix agent。
- 在什麼情況使用：T3 Connect 公開 webhook URL + 簽章驗證 + 自訂 prompt 模板。
- 帶來的價值：事件入口在控制面，agent 仍跑在受控 environment，而不是把 token
  丟進無狀態 serverless（細節需自行評估安全邊界）。

## 我們可以學什麼

- **值得借鑑的 product idea**：把 **environment（機器 + checkout + provider）**
  當成第一級資源，thread 是跨 client 的 durable intent；human 從任何 surface
  接入同一條 event log，而不是每個 client 各存一份 chat。
- **值得借鑑的 interaction / workflow**：（1）**配對與多 route** 降低「遠端 =
  只有一種 VPN」的摩擦；（2）**權限模式 per thread** 讓委派時可預設「這條 thread
  只能 supervised」；（3）**turn 結束 vs checkpoint／PR 收尾分開記錄**，避免 UI
  長時間顯示 agent 還在跑。
- **在 2D workspace 裡會變成什麼**：平面地圖上每棟「機器／environment」是一格
  building，裡面的 **thread 是員工工位**；route 線表示連線品質，Auto balance 像
  派工演算法把新任務分到較空的樓層。人點工位看到的是同一套批准、diff、PR 狀態，
  而不是另開 ADE 側欄。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室裡，**每個 environment 是一間
  實體機房**，agent thread 是在機房裡工作的人；你從手機「走進」同一間機房是
  視角切換，不是把 agent 搬到雲端。T3 Connect 像公司 VPN 大門，LAN route 像
  內網捷徑。
- **不值得照搬或需要重新設計的地方**：T3 Code 本質仍是 **ADE／harness dashboard**，
  不是 spatial office 產品；我們若做 2D／3D workspace，應吸收 **environment 邊界、
  event-sourced thread、遠端介入** 等 primitive，而不是複製整個 composer UI。
  Webhook 離線暫存、Connect 依賴雲端 relay 的 trust model 也要依自家部署重新
  設計。專案極早期、貢獻門檻高，API 與行為仍可能大幅變動。

## 初步看法

- 最有價值的部分：**remote-first、server-owned execution** 的契約寫得清楚，且與
  Conductor 類「本機 macOS ADE」形成互補參考；多 route 與 load balancing 對
  multi-machine solo dev 很貼地。
- 最大限制或疑問：尚未實測連線穩定度、provider 邊角差異與早期 bug；T3 Connect
  的可用性與資料留存政策需自行確認。與 Cursor 內建 agent 並用時，價值主張是
  「統一遠端殼」還是「多開一層」仍待 hands-on。
- 是否值得進一步研究或親自體驗：值得——若我們關心 **human 如何從手機／第二螢幕
  介入本機 agent fleet**，這份架構文件比 README 更有設計含量。

## Sources

- Official website：https://t3.codes
- Repository：https://github.com/pingdotgg/t3code
- Documentation：https://github.com/pingdotgg/t3code/tree/main/docs（含 remote-access、permission-modes、project-settings、internals/overview）
