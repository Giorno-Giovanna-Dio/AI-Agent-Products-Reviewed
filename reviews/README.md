# Product Review Methodology

這份文件定義所有產品共用的評測方式。每份 review 從
[`_template.md`](_template.md) 複製，並在測試過程中持續更新。

## 核心原則

1. **固定版本**：記錄實際測試的產品版本或完整 commit SHA。
2. **先定義任務**：執行前先寫假設、場景及 acceptance criteria。
3. **證據分級**：不要把官方宣稱、實際觀察和個人推論混在一起。
4. **公平比較**：比較產品時使用相同輸入、base commit、環境及驗收條件。
5. **不替未測功能評分**：未親自操作的項目填 `N/A`，不可從文件推測分數。
6. **狀態與決策分離**：`tried` 只代表用過，不代表值得採用。
7. **保留失敗結果**：安裝失敗、卡住或無法重現本身也是評測證據。

## Evidence labels

評測中的重要陳述使用以下標籤：

- **`[Observed]`**：本次實際操作、輸出或量測結果。
- **`[Official]`**：來自官方文件、repository 或 changelog。
- **`[Inference]`**：根據證據做出的分析，尚未直接驗證。
- **`[Unverified]`**：第三方說法、印象或等待確認的資訊。

主觀感受可以記錄，但應標成 `[Observed]`，並明確寫出測試環境與行為，避免
包裝成普遍事實。

## Review lifecycle

### 1. Intake

- 在 `projects.yaml` 建立 entry，status 設為 `untried`。
- 建立 review，記錄產品定位、來源、版本與為何值得測試。
- 此階段可以整理官方功能，但所有分數維持 `N/A`。

### 2. Test design

- 列出產品最核心的 1–3 個宣稱。
- 為每個宣稱設計可重現的任務與 acceptance criteria。
- 記錄不在本輪範圍內的功能，避免將「沒測」誤寫成「沒有」。
- 準備 disposable data、假 secrets 與可還原的環境。

### 3. Hands-on run

- 依照官方推薦方式安裝和執行。
- 保留必要的 commands、logs、screenshots、diffs、時間與成本。
- 同時記錄成功路徑、錯誤處理、cleanup 及 recovery。
- 不將正式 credentials、完整資料庫或大型 generated files 提交到本
  repository。

### 4. Analysis

- 將觀察結果對回原始假設與 acceptance criteria。
- 區分產品本身限制、環境問題及使用者設定問題。
- 與替代方案比較時，說明哪些是真正差異，哪些只是不同名稱的重疊功能。
- 更新 scorecard；沒有證據的項目仍填 `N/A`。

### 5. Decision

- Status 改為 `tried`。
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
