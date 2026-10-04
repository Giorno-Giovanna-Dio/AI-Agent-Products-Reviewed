# Product Review Methodology

這份文件定義所有產品共用的評測方式。每份 review 從
[`_template.md`](_template.md) 複製，並在測試過程中持續更新。

Review 的主要產出不是分數，而是：

- 對前沿產品與技術方向的準確理解。
- 可重用的 agent workspace primitives 與 interaction patterns。
- 對未來 2D／3D workspace 的具體設計啟示。
- 值得採用、需要重新設計及應避免的做法。
- 仍需透過 prototype 或實測回答的問題。

## 核心原則

1. **固定版本**：記錄實際測試的產品版本或完整 commit SHA。
2. **先理解產品**：先整理定位、目標使用者、核心 workflow 與 feature map，
   再決定哪些宣稱值得實測。
3. **Feature 不等於價值**：除了列出功能，也要說明它解決的問題、適用情境
   及與替代方案的差異。
4. **證據分級**：不要把官方宣稱、實際觀察和個人推論混在一起。
5. **公平比較**：比較產品時使用相同輸入、base commit、環境及驗收條件。
6. **不替未測功能評分**：未親自操作的項目填 `N/A`，不可從文件推測分數。
7. **狀態與決策分離**：`tried` 只代表用過，不代表值得採用。
8. **保留失敗結果**：安裝失敗、卡住或無法重現本身也是評測證據。
9. **不強迫 3D 化**：只有當深度、位置或空間關係能改善理解與操作時，才把
   pattern 映射到 3D；其餘情況應優先採用更清楚的 2D 表達。
10. **Hands-on 是選擇性的**：研究 skill、template、輔助工具或已能從
    source/docs 理解的能力時，不必為了完成形式而親自執行。

## Evidence labels

評測中的重要陳述使用以下標籤：

- **`[Observed]`**：本次實際操作、輸出或量測結果。
- **`[Official]`**：來自官方文件、repository 或 changelog。
- **`[Inference]`**：根據證據做出的分析，尚未直接驗證。
- **`[Unverified]`**：第三方說法、印象或等待確認的資訊。

主觀感受可以記錄，但應標成 `[Observed]`，並明確寫出測試環境與行為，避免
包裝成普遍事實。

## Review lifecycle

### 1. Product introduction

- 在 `projects.yaml` 建立 entry，status 設為 `untried`。
- 建立 review，記錄產品定位、來源、版本、目標使用者與主要問題。
- 用一段簡短敘述說清楚產品是什麼，而不是直接從安裝步驟開始。

### 2. Feature mapping

- 依核心 workflow、協作、整合、執行環境與安全控制整理 features。
- 對每個 feature 記錄用途、受益對象、證據來源及驗證狀態。
- 區分真正獨有的能力、常見能力及只是名稱不同的重疊功能。
- 此階段可以只做產品研究；所有未實測項目維持 `not-tried` 或 `N/A`。

### 3. Product comparison

- 找出最接近的替代方案及使用者目前可能採用的 workflow。
- 比較定位、功能範圍、操作模式、部署方式、價格與限制。
- 先提出差異，再決定哪些差異值得用 hands-on experience 驗證。

### 4. Frontier and workspace analysis

- 判斷哪些能力真正代表新的技術或 interaction direction。
- 將產品拆成 agents、tasks、context、runtime、artifacts、state 與 control
  等可重用 primitives。
- 分別評估它們在 2D 與 3D workspace 中的呈現方式。
- 記錄 transferable patterns、design risks，以及不值得照搬的部分。

### 5. Decide the validation method

每個重要問題可選擇最適合的 evidence，而不是一律 hands-on：

- `official-docs`：確認產品定位、功能範圍與官方 workflow。
- `source-review`：確認 open-source implementation、permissions 或 data flow。
- `demo-review`：理解 interaction、visualization 與 user journey。
- `hands-on`：驗證實際 UX、可靠性、限制或文件無法回答的行為。
- `prototype`：驗證某個 pattern 是否適合我們的 2D／3D workspace。

值得 hands-on 的情況：

- 結果會實質影響採用或 workspace design 決策。
- Interaction quality 無法從文字或影片判斷。
- 官方宣稱、source 與第三方經驗互相衝突。
- 安全、權限、資料處理或 failure recovery 是核心風險。
- 需要在相同條件下比較多個產品或 runtimes。

可以不實測的情況：

- 只是 skill、prompt template、薄封裝或輔助 feature。
- Source 和文件已足以理解其 mechanism 與限制。
- 與已研究產品高度重疊，沒有新的 workspace primitive。
- 安裝成本或風險明顯高於它對研究問題的價值。

### 6. Optional hands-on experience

- 依照官方推薦方式安裝和執行。
- 從產品最具代表性的 user journey 開始，不需要為了完整度測遍所有功能。
- 為核心宣稱設計可重現的任務與 acceptance criteria。
- 保留必要的 commands、logs、screenshots、diffs、時間與成本。
- 同時記錄成功路徑、錯誤處理、cleanup 及 recovery。
- 不將正式 credentials、完整資料庫或大型 generated files 提交到本
  repository。

沒有執行時，記錄跳過原因和目前 evidence 即可，status 維持 `untried`。

### 7. Analysis

- 將觀察結果對回原始假設與 acceptance criteria。
- 區分產品本身限制、環境問題及使用者設定問題。
- 與替代方案比較時，說明哪些是真正差異，哪些只是不同名稱的重疊功能。
- 修正 feature map 中不準確的理解，並更新驗證狀態。
- 更新 scorecard；沒有證據的項目仍填 `N/A`。

### 8. Decision

- 只有實際操作過產品時，才把 status 改為 `tried`。
- 設定 verdict，記錄理由、信心程度及需要重新評估的條件。
- 每個明確評測階段使用獨立 atomic commit。

## Status

- `untried`：已收錄，但尚未實際使用。
- `tried`：已經實際體驗過。

## Verdict

- `undecided`：證據不足，尚未做決定。
- `adopt`：適合作為主要產品或整合基礎。
- `reference`：不直接採用，但有值得借鑑的功能或設計。
- `pause`：暫不決定，等待必要條件或後續版本。
- `reject`：目前不適合需求，並已記錄具體原因。

Verdict 不是產品的永久評價，只代表指定版本、測試範圍和目前需求下的決策。

## Scorecard

只有實際測試過的面向才評分：

| Score | Meaning |
| --- | --- |
| `1` | 無法完成核心需求，或有重大阻礙 |
| `2` | 可部分運作，但需要大量 workaround |
| `3` | 可完成需求，體驗或可靠性普通 |
| `4` | 表現良好，只有少量限制 |
| `5` | 表現突出且有充分、可重現的證據 |
| `N/A` | 尚未測試或不適用 |

每個分數必須附一句 evidence。總分不是必要欄位，因為不同產品類別的各面向
權重不相同。

## Minimum comparison controls

若要比較兩個產品或 agent runtimes，至少固定：

- 相同任務與 acceptance criteria
- 相同起始資料或 Git commit
- 相同可用 tools、credentials 與 network 條件
- 相同時間、iteration 或 token limit
- 相同測試與人工 review 方法

若無法固定其中一項，必須在 comparison 中標示，不能直接宣稱某產品較好。
