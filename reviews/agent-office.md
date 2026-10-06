# Agent Office

> Cell ID：[https://github.com/AgentSystemLabs/agent-office](https://github.com/AgentSystemLabs/agent-office)
>
> Status：`untried`
>
> Category：3D agent workspace
>
> Last updated：2026-10-05

## 產品介紹

Agent Office 是一套開源的 **3D 辦公室介面**，讓人與多個 coding agent 在同一
個可走動的空間裡協作。每個 GitHub repository 對應大樓裡的一層樓；你在空
桌按鍵僱用 Claude Code、Codex、OpenCode、Cursor 等 worker，agent 的即時
終端機會顯示在桌前的筆電上，任何人都能走過去一起看、一起打字。

它跑在本機或團隊共用的伺服器上（瀏覽器連進 Node 後端），不是把 agent
嵌在 IDE 側欄，而是把 **「誰在忙、誰在等你、誰做完了」** 變成空間裡看得
見的狀態。官方也提供 `/lite` 的平面版，方便手機或慢速裝置查看同一批 worker
與待辦。

專案標示為個人 workflow 快速迭代中的 WIP，API 與畫面可能常變；MIT 授權，
TypeScript 實作，支援 macOS、Linux、Windows。

## 主要 Features

### 一 repo 一樓層

電梯選 GitHub repo 後，辦公室會 clone（或沿用已有 checkout）並開一層樓。
該樓的 worker、任務佇列、Issues／PR 看板都綁在這份 checkout 上，多專案
並行時用「換樓層」切換上下文，而不是在一個 flat 列表裡混雜所有 repo。

### 桌位上的 live terminal

每個 worker 有獨立 PTY；伺服器用 headless xterm 鏡像畫面，筆電螢幕以 diff
推給瀏覽器。多人可共用同一終端，誰在打字誰決定終端尺寸。重啟後 PTY 可
被託管程序保留並接回，scrollback 也會存檔回放。

### 需要人介入時「叫得動你」

Agent 停下來等人回覆時，桌上有紅色信標、全螢幕橫幅與警報聲；完成時 worker
會跳動並響鈴。按 **N** 可跳到下一個在等的 worker。狀態主要來自 Claude Code
hooks、OpenCode／Codex 插件，以及 DeepSeek Harness 的 ACP session 事件，
而不是只靠猜終端輸出。

### GitHub 牆上的 Issues 與 PR

Issues、PR 掛在軟木公告板；可把 issue 交給 worker、排隊任務。可為 worker
開獨立 git worktree 與 `office/<name>` 分支，按 **O** 一鍵 push 並 `gh pr create`。
多 repo 任務可在一個 workspace 裡為各專案建 worktree，並在 PR 描述裡互相
連結。PR 合併後可設定 worker 自動「下班」並清理 worktree。

### Agent 管理其他 agent

透過 `agent-office` MCP（或 `office-workers` CLI），worker 可列出、僱用、
傳訊、遣返其他 worker，例如「PR 已 merge 的全部送回家」。這把 orchestration
放進 agent 可呼叫的工具，而不只限於人點 UI。

### 協作與輕量 2D

語音（WebRTC）、聊天、休息區電視螢幕分享、白板。`/lite` 以 2D 列出 worker、
待回覆事項、終端與看板，並補上手機鍵盤缺的快捷鍵——同一後端，兩種空間
密度。

### 可替換的「地圖」主題

除預設辦公室外，可用 JSON 定義城堡、太空站等 map：座位 id 與 worker 邏輯
不變，只改視覺與「遣返 worker」的演出。說明 3D 層主要是 **presentation
skin**，核心仍是同一套樓層／桌位／狀態機。

## 主打賣點

- **Spatial observability**：多 agent 並行時，用走動、信標、筆電畫面回答
  「現在誰在幹嘛、誰卡在我這」——這是 Conductor、Maestro 一類 ADE dashboard
  較少做的隱喻。
- **Terminal-first，不是 chat-first**：主介面是共享 shell，適合已習慣
  Claude Code／Codex CLI 的人，而不是另建一套聊天 UI 再轉命令。
- **GitHub 原生工作流**：clone、board、worktree、PR 一條龍嵌在空間隱喻裡，
  而不是外掛一個無關的 ticket 系統。
- **團隊可部署**：預設 localhost；AWS／Azure／Railway 等腳本用 SSH tunnel
  或 Tailscale Serve 讓多人同進一間辦公室，並支援 per-account 的 Claude／
  GitHub 登入（仍共用同一 OS user，安全模型需自行評估）。

相對而言，裝飾性內容（酒吧、高爾夫、換皮 map）是體驗加分，不是核心
orchestration 能力；語音輸入、費用統計等是便利功能，其他 workspace 也可用
不同方式補上。

## 使用情境

### 一人多 worker 並行開發

- 適合誰：同時跑好幾個 agent 處理不同 issue 或 repo 的個人開發者。
- 在什麼情況使用：需要肉眼掃描誰在等 approval、誰已可 review PR。
- 帶來的價值：減少在多个終端分頁間切換，用空間位置記住「那張桌是誰」。

### 小團隊共用一間「agent 辦公室」

- 適合誰：願意把 agent runtime 裝在共用 VM／Tailscale 節點上的團隊。
- 在什麼情況使用：多人要看同一 worker 終端、一起語音討論、共用 PR 看板。
- 帶來的價值：協作感接近 pair programming room，但對象包含 autonomous agents。

### 手機或遠端快速處理阻塞

- 適合誰：人不在桌機前、但 agent 常等人點 approve 的使用者。
- 在什麼情況使用：收到信標／通知後用 `/lite` 回覆或跳轉到該 worker。
- 帶來的價值：3D 主體驗留給大螢幕，2D lite 負責 **human-in-the-loop 的
  低摩擦介入**。

## 我們可以學什麼

- **值得借鑑的 product idea**：用 **樓層＝repo、桌位＝worker 實例、筆電＝
  PTY 視窗** 固定 mental model；狀態從 agent runtime hooks 匯入，而不是
  純 UI 動畫。PR／issue 是牆上實體，任務佇列是辦公室內的流程，讓
  orchestration 與軟體工程 artifact 同場出現。
- **值得借鑑的 interaction / workflow**：「Attention 協議」——needs input、
  done、working 要有不可忽視的多通道提示（視覺＋聲音＋快捷鍵 N）。共享
  terminal 的 late-join snapshot、worktree 隔離並行 worker、merge 後自動
  遣返，都是 multi-agent 實務細節。Agent 透過 MCP 管理 agent，是把
  「辦公室管理員」角色也 agent 化的一種路線。
- **在 2D workspace 裡會變成什麼**：`/lite` 已是雛形——樓層選擇變下拉或
  側邊 repo 列表；桌位變 worker 卡片網格，信標變高亮邊框或排序置頂；
  筆電變內嵌終端 pane。pixel 風格辦公室仍可保留「走道」隱喻，但互動
  以點選為主，不必完整 3D 導航。GitHub board、queue、changes diff 可
  維持同一資料模型。
- **在 3D workspace 裡會變成什麼**：Agent Office 本身就是這類產品的參考
  實作：第一人稱或第三人稱在樓層間移動，走近才開 terminal；信標與動畫
  傳達阻塞與完成；電梯換 repo。我們若做 3D office，需決定要保留多少
  「遊戲化」裝飾 vs 專注在 worker 狀態與 handoff；map JSON 插件化值得
  沿用，以便品牌或文化主題而不 fork 核心。
- **不值得照搬或需要重新設計的地方**：WIP 節奏與高度 opinionated 的
  單作者 workflow，不一定適合企業 RBAC。多 account 仍共用 OS user，
  機密隔離不足時不能當零信任沙箱。重度依賴 GitHub CLI 與特定 agent CLI
  生態；若我們要 vendor-neutral 或 cloud-only agent，要重做 floor／hook
  抽象。裝飾玩法（高爾夫、DJ）對研究型 radar 可選，避免喧賓奪主。

## 初步看法

- 最有價值的部分：把 **multi-agent 可觀測性與 human-in-the-loop** 做成
  可走動的 3D 辦公室，並用 hooks＋worktree＋GitHub 板把「真實開發狀態」
  接進空間，而不是只做 avatar 皮。
- 最大限制或疑問：變更頻繁、部署與權限模型偏「信任同事＋信任已登入者能
  跑 shell」；尚未確認在更大團隊、非 GitHub 流程下的適配成本。
- 是否值得進一步研究或親自體驗：對 2D／3D workspace 方向是 **高優先
  參考 Cell**；若我們要驗證 interaction 手感或 hook 延遲，再 clone 到
  `/workspace-labs/agent-office` 做 hands-on，而非把 upstream 放進本 repo。

## Sources

- Official website：（無獨立官網；以 repository 為準）
- Repository：[https://github.com/AgentSystemLabs/agent-office](https://github.com/AgentSystemLabs/agent-office)
- Documentation：[docs/features.md](https://github.com/AgentSystemLabs/agent-office/blob/main/docs/features.md)、[docs/how-it-works.md](https://github.com/AgentSystemLabs/agent-office/blob/main/docs/how-it-works.md)
