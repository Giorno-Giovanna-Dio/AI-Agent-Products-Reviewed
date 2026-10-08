# Herdr

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-herdr-cell-7aa3/reviews/herdr.md)
>
> Cell ID：[https://github.com/herdrdev/herdr](https://github.com/herdrdev/herdr)
>
> Status：`untried`
>
> Category：Coding agent terminal runtime（persistent multiplexer）
>
> Last updated：2026-10-06

## 產品介紹

Herdr（herdr）是 **coding agent 的終端 runtime**：背景常駐 server 持有真實
PTY 與 pane 佈局，client（TUI）可隨時 attach／detach。使用者照常在 pane 裡跑
Claude Code、Codex、Pi、OpenCode、Cursor CLI 等既有 agent，Herdr **不取代、不包裝**
這些 harness，只負責「終端活著、佈局可還原、狀態可被讀取與編排」。

典型流程是：在專案目錄執行 `herdr`，建立 **workspace**（通常對應一個 repo 或任務），
在 **pane** 裡啟動 agent，用分割與 **tab** 並行多工；`ctrl+b q` 分離 client 後
agent 仍跑在 server 上，換機器 SSH 回來再 `herdr` 即可接續。sidebar 彙整各 workspace
內 agent 的 **working／blocked／done／idle**，避免在多 pane 裡逐格找「誰在等你按
Yes」。

## 主要 Features

### Workspace／Tab／Pane 階層

**Workspace** 是頂層專案容器（建議一 repo 或一調查一 workspace）；**Tab** 是
同一 workspace 內的版面（例如 agents、logs、server）；**Pane** 是真終端，可分割、
重新命名、從 CLI 讀取輸出與送鍵。sidebar 狀態由內部 agent 彙總，讓「哪個專案需要
人」一眼可見。

### 背景 server + 可分離 client

預設為 **server 擁有程序與 pane 狀態**，client 只是 UI。分離 client 不停止 agent；
`herdr server stop` 才結束 session 與 pane 程序。多 client 可同時連同一 server，各自
瀏覽不同 workspace／tab（同 tab 的尺寸由最後互動的 client 主導）。

### Agent 偵測與狀態機

Herdr 用前景程序、**screen manifest**（證據式比對 terminal 底部緩衝）與可選
integration 辨識 pane 內的 **agent**，並標記：

| 狀態 | 意義 |
| --- | --- |
| `blocked` | 需要輸入、核准或決策 |
| `working` | 正在執行 |
| `done` | 已完成且尚未被使用者「看過」 |
| `idle` | 已結束或等待，且已被看過 |
| `unknown` | 無法可靠分類 |

文件特別區分 **client 的 Done badge** 與 **server 的 seen state**（CLI／API 用後者）。

### Session 持久化與還原

**Detach** 最強：程序從未停止，畫面與對話都在。**Server 重啟** 則原 PTY 程序消失，
但可還原 workspace／tab／pane 佈局、cwd；部分 agent 可透過官方 integration 的
**session reference** 重啟對話（非所有 agent／程序都能還原）。可選
`experimental.pane_history` 在重啟後 replay 終端畫面（預設關閉，因可能含 secret）。
更新時另有 **handoff** 路徑，盡量讓支援中的 server 在升級過程保持程序存活。

### Agent-native CLI 與 Socket API

同一控制面分三層：**Agent skill**（教 pane 內 agent 如何用 Herdr）、**CLI**
（`herdr workspace create`、`herdr pane run`、`herdr agent wait --until done` 等）、
**Raw socket**（事件訂閱、自訂工具）。Agent 可互開 pane、讀輸出、**prompt 彼此、
等待 blocked**，而不是盲目送 keystroke。

### 多機與遠端

可將本機 workspace 與透過 SSH 儲存的遠端機器放在 **同一個 Herdr 視窗**，agent 列表
合併顯示、連線各自重連。適合「筆電關蓋但家裡 desktop 上的 agent 還在跑」或
多 repo 分散在不同 host 的用法。

### 滑鼠優先 TUI + tmux 風格鍵盤

可點選 pane／tab／workspace／agent、拖曳分割、右鍵選單；亦可 `ctrl+b` 前綴模式操作。
單一 Rust binary，跨 macOS／Linux／Windows（無 Electron）。

### 插件與整合

插件市場擴充 pane 與 workflow；文件提供「為你的 agent 加入 Herdr 支援」與
內建 integration 清單，以改善偵測準確度。

## 主打賣點

- **「Runtime your agents live on」**：定位是 **持久終端與可編排 surface**，不是新
  ADE 或新 harness。與 Conductor／Maestro 的 worktree 控制台互補，與 Pi 這類
  harness 是上下層關係。
- **Blocked 一等公民**：從 terminal 內容推 agent 狀態，sidebar 直接回答「誰卡住」；
  這比單純 tmux + 人工掃描更接近 **human-in-the-loop 的 observability**。
- **Agent 可當 first-class 編排者**：Socket／CLI 讓 agent **等待** 另一 agent 真的
  blocked 或 done，而不是 sleep 猜時間——對 multi-agent 編排是實質差異。
- **輕量部署敘事**：一個 binary、現有 terminal，stars／下載量高（以官方 README／
  網站數字為準，尚未自行驗證）。

**較像既有能力的重新包裝**：tmux／Zellij 的分離與分割；cmux 類的「agent 通知」——
Herdr 的差異在 **agent 狀態模型 + API + 跨機合併 + 針對 coding CLI 的 manifest**，
而不是發明新的 agent 能力。

## 使用情境

### 長時間 AFK 多 repo 並行

- 適合誰：同時開多個 Claude Code／Codex session 的 individual 或 small team。
- 在什麼情況使用：每個 repo 一 workspace，多 pane 跑 implement／test／review；
  關筆電或 SSH 斷線後仍希望 build／test 繼續。
- 帶來的價值：detach 不殺程序；回來時從 sidebar 先看 **blocked**，再 attach 決策。

### Agent 編排 agent

- 適合誰：用腳本或 harness 內 extension 做 fan-out／pipeline 的進階使用者。
- 在什麼情況使用：一 pane 的 agent 用 `herdr pane split` + `herdr agent wait`
  協調另一 pane 的 reviewer 或 tester。
- 帶來的價值：編排邏輯可寫在 agent 可呼叫的 API 上，不必依賴特定 ADE 的內建 workflow。

### 本機 + 遠端混排

- 適合誰：有 homelab 或固定 build 機的開發者。
- 在什麼情況使用：筆電 Herdr 視窗同時顯示本機與 SSH 機器上的 agent。
- 帶來的價值：減少「這台 agent 在哪台機器」的認知負擔；仍須自行管理 SSH 與
  各機上的 Herdr server（細節以文件為準）。

## 我們可以學什麼

- **值得借鑑的 product idea**：把 **workspace 邊界** 定成「專案／任務容器」，底下
  才是 tab／pane；**runtime 擁有 PTY**，UI 只是 client——這與 future 2D／3D workspace
  的「server-owned execution、多 surface attach」一致（對照 T3 Code、Paseo 等 Cell）。
- **值得借鑑的 interaction / workflow**：
  - **blocked／working／done** 比「最後一行 log」更適合當 **attention 訊號**；
  - **agent wait** 語意（等到 genuinely blocked 或 done）適合做 handoff gate；
  - detach 後 **sidebar 仍彙總** 多 workspace，是人離開現場時的「總機 board」。
- **在 2D workspace 裡會變成什麼**：俯視平面地圖上，每個 **workspace 是一棟或一區**，
  pane 是工位；**blocked** 工位閃爍或排隊在「待核准」走道；點工位 attach 到真
  terminal 或 embed 串流（Herdr 已提供 read／event API 方向）。遠端 SSH 機器可畫成
  地圖上的 **分館**。
- **在 3D workspace 裡會變成什麼**：可走進的辦公室中，每 workspace 是一間大室，
  每 agent 坐在 pane 工位；**blocked** 時員工舉牌或門口亮燈；人走近才看到 terminal
  細節。Herdr 不提供 avatar，但 **狀態機 + 持久 runtime** 可直接餵給 spatial UI。
- **不值得照搬或需重設計的地方**：
  - Herdr **不是** OpenShell／Sandcastle 那類 **policy／container sandbox**；agent 仍
    在 **使用者 OS 權限** 下跑（例如 Claude 的 bypass permissions 是 agent 行為）。
    若 2D／3D workspace 要 enterprise trust zone，需另建 sandbox 層，Herdr 只適合當
    **顯示與編排** 參考。
  - Screen manifest 維護成本高；我們若支援多 harness，需決定 **偵測 vs 顯式 registration**。
  - 重度 tmux 使用者可能認為「只是更好的 mux」——spatial workspace 必須加 **任務／
    artifact／handoff** 語意，不能只複製 pane。

## 初步看法

- 最有價值的部分：**持久 PTY runtime + agent 狀態 + agent 可呼叫 API** 三件套，
  正好填「harness 之上、ADE 之下」的 execution layer。
- 最大限制或疑問：安全邊界等同本機 shell；server 重啟後非 agent 的一般程序無法
  還原；與 cmux（macOS 原生）、Conductor（worktree ADE）的取捨需依平台與 workflow
  評估（尚未 hands-on 比較）。
- 是否值得進一步研究或親自體驗：值得。若我們 workspace 需要 **detach 不斷線、
  blocked 聚合、agent 互等**，應在 `/workspace-labs/herdr` 試跑 multi-pane + socket
  編排，而非把 upstream clone 進本 repo。

## 後續補充（選填）

Not tried yet。本筆記僅依官方 README、herdr.dev 文件與 repository 內 docs 撰寫，
未安裝 binary 或驗證偵測準確度。

## Sources

- Official website：https://herdr.dev
- Repository：https://github.com/herdrdev/herdr
- Documentation：https://herdr.dev/docs/（concepts、session-state、socket-api、agents）
