# OpenShell

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openshell-cell-7aa3/reviews/openshell.md)
> - [Pages 閱讀器](https://giorno-giovanna-dio.github.io/AI-Agent-Products-Reviewed/#/reviews/openshell.md)（`main` 部署後）
>
> Cell ID：[https://github.com/NVIDIA/OpenShell](https://github.com/NVIDIA/OpenShell)
>
> Status：`untried`
>
> Category：Policy-enforced agent sandbox runtime
>
> Last updated：2026-10-06

## 產品介紹

OpenShell 是 NVIDIA 開源的 **agent 安全執行 runtime**：讓自主 AI agent 能讀
檔、裝套件、打 API、用憑證做事，但**不能**不受控地碰你的資料、secrets 或
任意網路。你為每個 agent（或每個 sandbox）撰寫 **policy**（檔案、行程、
網路、API、憑證可用範圍），OpenShell 在執行期用核心層級機制強制執行，並在
policy 變更生效前用形式化驗證標出高風險擴權。

它**不是** coding agent 本體，也不是 ADE 看板。典型用法是：在本機或 K8s 上
跑 **gateway**（控制平面）→ 用 CLI 或 SDK 建立 **sandbox** → 在 sandbox 裡
跑 OpenCode、Pi 等 agent CLI；agent 對外的 TCP／DNS 都經 **supervisor**
中介，憑證由 gateway 管理、只在 policy 允許的 endpoint 注入。與 Sandcastle
這類「編排 git + 容器」的 library 互補：OpenShell 專注 **隔離邊界與
permission 語意**，agent harness 仍可外掛（例如 Pi 文件建議用 OpenShell 補
權限模型）。

## 主要 Features

### Gateway 控制平面

集中管理 sandbox 生命週期、身分、policy、provider 憑證、連入 sandbox 的
互動 session，以及 operator／end user 的存取控制。建立 sandbox 時由 **compute
driver** 依 runtime（Docker、Podman、K8s、VM）拉起 workload 與 **supervisor**，
並在 agent 啟動前確認隔離邊界已就緒。

### Supervisor／Sandbox 雙邊界

**Sandbox** 與不可信 agent 同側：負責子行程、exec／terminal、以可信程序
觀察辨識「哪個程式」發出請求，並攔截 TCP／DNS 轉交 supervisor。**Supervisor**
在可信側：依 policy 決定允許與否、代開連線、注入憑證、維持與 gateway 的
連線。Workload 對外 egress 預設全拒，僅允許到 supervisor 的受保護通道（fail
closed：連線斷了會凍結 agent）。

### Policy 與形式化驗證（prover／advisor）

Policy 描述檔案、行程、網路目的地、HTTP 方法、雲端 metadata 等規則。
Agent 或操作者提議放寬規則時，gateway 內的 **policy prover** 用形式化方法
檢查是否新增「帶憑證連到新 host」「新 API 方法」等風險；有 finding 則阻擋
自動核准，留給人工審核。另有獨立 `openshell-prover` 可在 CI 對 policy 做
離線檢查（尚未在本 repo 實測）。

### Providers 與 inference 路由

把服務名稱綁到儲存的 credential；agent 在 sandbox 內**看不到**明文
secret，supervisor 只在 policy 允許的請求上附加憑證。文件另述 inference
相關路由，方便 agent 呼叫核准過的 model endpoint。

### 多 runtime 與 K8s／GPU

同一套 policy 語意可跑在 Docker／Podman 容器、Kubernetes pod（需 CNI 支援
NetworkPolicy）、或 MicroVM 等；sandbox 可配置 image、GPU、生命週期。Helm
可部署 gateway 叢集化控制平面。

### CLI、SDK 與 agent skills

安裝腳本提供 `openshell` CLI（建立 sandbox、審 policy 等）。Python／TypeScript／
Go／Rust SDK 連到 gateway（不取代 CLI 安裝）。repo 內 **Agent Skills** 可
用 `npx skills add NVIDIA/OpenShell` 教 coding agent 撰寫 policy、操作 CLI。

### 可擴充性

Middleware、interceptors、isolation backend、compute driver 等 extension
點，讓新 runtime 或企業整合掛進同一 supervisor／policy 模型。

## 主打賣點

- **執行期 + 變更前雙重治理**：不只容器「關起來」，而是每次檔案、syscall、
  連線都過 policy；放寬規則前還有 prover 擋高風險 diff——這比「信任 agent
  自律」或單純 Docker bind mount 更接近 enterprise agent fleet 需求。
- **憑證與 agent 分離**：agent 拿能力但不拿 secret，對 multi-tenant 或
  長時間 autonomous 任務是核心差異。
- **Runtime 中立**：同一 policy 跨 Docker／K8s／VM，決策集中在 supervisor，
  不是每種 orchestrator 各寫一套 ad hoc 規則。
- **相對常見替代**：純 Docker／Vercel sandbox 偏重隔離與資源，較少內建
  「逐請求 credential 注入 + 形式化 policy review」；E2B 等託管 sandbox
  是服務形態，OpenShell 偏自架 control plane（與 Paseo daemon、Sandcastle
  編排可疊加而非直接取代 ADE UI）。

部分能力（例如各 runtime 的 feature parity、Windows WSL 實驗支援）仍須
對照官方 support matrix；本筆記未 hands-on 驗證。

## 使用情境

### 多 agent 在本機或 CI 跑工具，又怕 prompt injection

- 適合誰：團隊已用 Claude Code／Codex／OpenCode 等 CLI，但不想給 full
  host 權限。
- 在什麼情況使用：每個任務一個 sandbox policy，只開放 repo 目錄與特定
  package registry／API。
- 帶來的價值：agent 被誘導 exfiltrate 時，network fence 與 credential
  邊界比「同一 user 跑 terminal」可控。

### Agent 執行中動態要新權限（網路／API）

- 適合誰：長時間 autonomous workflow，agent 會依需求請求新 host 或方法。
- 在什麼情況使用：gateway 收到 policy 變更提案 → prover／advisor 標風險 →
  人類在 UI／CLI 核准。
- 帶來的價值：把「權限升級」變成可審計、可阻擋的流程，而不是 silent
  `chmod` 或改 firewall。

### K8s 上跑 agent fleet

- 適合誰：已有 Kubernetes，想統一 sandbox 與 gateway HA。
- 在什麼情況使用：Helm 部署 gateway，compute driver 在叢集內起 pod 級
  sandbox＋supervisor。
- 帶來的價值：與現有 NetworkPolicy／身分基礎設施對齊，policy 語意與本機
  一致（尚未確認各發行版 K8s 的實務限制）。

## 我們可以學什麼

- 值得借鑑的 product idea：
  - **Policy 是一等公民**，不是 sandbox 的 afterthought；workspace 應顯示
    「這個 agent 目前 policy 邊界」與「待審的擴權 diff」。
  - **Human-in-the-loop 放在 policy delta**，而不是只在最終 diff 上點 merge。
  - **Harness 與 runtime 分離**：Pi／Sandcastle 管 loop 與 git；OpenShell
    管 trust zone——2D／3D workspace 不該重寫 seccomp，而該**視覺化**這層
    契約。
- 值得借鑑的 interaction / workflow：
  - Agent 請求新 network rule → prover finding → 主管核准／拒絕 → sandbox
    內 agent 才繼續；可對應 workspace 的「升級工單」或「開新房間」。
  - Supervisor 回報真實 executable identity，減少 agent 偽造 path 騙 policy。
  - Fail closed（supervisor 斷線即凍結）對應「員工離崗即停機」的 spatial
    敘事。
- 在 2D workspace 裡會變成什麼：
  - 平面地圖上每個工位／房間綁一個 sandbox id；房間外框顏色表示 policy
    嚴格度（檔案可寫、egress 允許清單）。
  - 「待審 policy 變更」出現在中央 inbox 或工位上方 alert，點開看 prover
    標紅的 credentialed reach。
  - Gateway 是樓層管理室：誰能 spawn sandbox、attach terminal，不與 agent
    聊天 UI 混為一談。
- 在 3D workspace 裡會變成什麼：
  - 可走進的 **sandbox 房間**：agent avatar 只在房內活動；門外是 supervisor
    「警衛 desk」，所有對外連線像排隊通過單一出口。
  - 不同 runtime（Docker pod vs VM）是不同 **建築材料**，但同一套 policy
    規則貼在門上——使用者理解「規則一致」而非「容器名稱」。
  - 憑證注入可視覺化為「鑰匙在警衛身上，員工只有工牌」——避免 3D 場景
    誤導成 agent 真的持有 API key。
- 不值得照搬或需要重新設計的地方：
  - OpenShell **沒有** task board、worktree、multi-agent 聊天——spatial
    workspace 仍須自己的 goal／handoff 模型。
  - 安裝與 kernel／runtime 依賴偏重（Linux、macOS Apple Silicon、WSL
    實驗）；2D／3D 前端不能假設人人已跑 gateway。
  - 形式化 prover 對一般使用者可能太抽象——UI 需翻譯成「這會讓 agent 用
    你的 GitHub token 連到新網域」這類句子。
  - 匿名 telemetry 預設開啟（可關）——企業 workspace 需對應隱私開關敘事。

## 初步看法

- 最有價值的部分：把 **autonomous agent 需要的自由度** 與 **operator 需要
  的 deny-by-default** 用同一套 policy／supervisor 模型串起來，且與 Pi 等
  harness 生態明確對位。
- 最大限制或疑問：實際 UX 成本（policy 撰寫、首次 agent 跑起來的核准
  步驟）、各 runtime 成熟度，以及與現有 ADE「一鍵 worktree」相比的整合
  工作量——皆未親自驗證。
- 是否值得進一步研究或親自體驗：是。若 2D／3D workspace 要把「trust
  boundary」做成使用者看得懂的原語，OpenShell 是目前少數把 enforcement
  講清楚的開源參考。

## 後續補充（選填）

Not tried yet.

## Sources

- Official website：https://docs.nvidia.com/openshell/latest/index.html
- Repository：https://github.com/NVIDIA/OpenShell
- Documentation：Architecture、Policies、Sandboxes、Gateways（docs.nvidia.com/openshell）
