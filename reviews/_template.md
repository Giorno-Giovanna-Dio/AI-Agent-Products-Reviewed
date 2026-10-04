# Product name

> Status：`untried`
>
> Verdict：`undecided`
>
> Confidence：`low`

開始前先閱讀 [`README.md`](README.md) 的評測規則。重要陳述應標為
`[Observed]`、`[Official]`、`[Inference]` 或 `[Unverified]`。

## 1. Metadata

| Field | Value |
| --- | --- |
| Project ID | |
| Category | |
| Homepage | |
| Repository | |
| License | |
| Fork | |
| Tested version / commit | |
| Test date | |
| Evaluator | |
| Host OS / hardware | |
| Installation / runtime | |
| Models / agent runtimes | |
| Pricing plan | |

## 2. Product introduction

### What is it?

用一至兩段說明產品是什麼、服務誰，以及如何完成主要工作。這裡應讓沒接觸
過產品的人先建立完整輪廓，不從安裝或 testing 開始。

### Positioning

- Product category：
- Primary problem：
- Target users：
- Maturity / stage：
- Main interaction model：
- Deployment model：

### Core workflow

```text
Describe the typical user journey from input to outcome.
```

### Why it is interesting

- 最有辨識度的能力：
- 可能補足的現有 workflow 缺口：
- 最適合：
- 不適合：

## 3. Feature map

先從官方資料整理功能，再透過實際體驗更新 Verification。不要因為尚未測試
就省略重要 feature，也不要將官方宣稱寫成已驗證事實。

| Feature | What it does | User value | Evidence | Confidence |
| --- | --- | --- | --- | --- |
| | | | `[Official]` | `medium` |

Confidence 使用 `low`、`medium` 或 `high`；它表示目前證據強度，不代表是否
親自操作過產品。

### Core capabilities

-

### Workflow and automation

-

### Collaboration and review

-

### Integrations and extensibility

-

### Environment and deployment

-

### Safety and controls

-

## 4. Frontier relevance and workspace implications

### What is at the edge?

- Novel technical capability：
- Novel interaction model：
- Enabling technology：
- Why this was not practical before：
- Maturity：`experimental` / `emerging` / `established`

### Workspace primitives

把產品拆成可重用概念，而不是只複製畫面或 feature 名稱。

| Primitive | How this product implements it | 2D relevance | 3D relevance | Evidence |
| --- | --- | --- | --- | --- |
| Agent | | | | |
| Task / goal | | | | |
| Context / memory | | | | |
| Runtime / sandbox | | | | |
| State / progress | | | | |
| Artifact / provenance | | | | |
| Human control | | | | |

### Implications for a 2D workspace

- Layout / canvas：
- Navigation：
- Relationships：
- Progress visualization：
- Comparison and review：

### Implications for a 3D workspace

- What depth or position represents：
- Spatial relationships：
- Navigation and camera：
- Collaboration / presence：
- Benefit over 2D：
- Risk of visual complexity：

### Design extraction

- Pattern worth adopting：
- Pattern requiring redesign：
- Pattern to avoid：
- Prototype question：

如果 3D 沒有增加理解、操作或協作價值，應明確記錄「2D 較適合」，而不是
為了符合產品方向強行建立空間隱喻。

## 5. Product experience

此區優先描述產品的實際操作方式；尚未使用時可根據官方 walkthrough 建立
初稿，但必須標成 `[Official]`。

- Onboarding：
- Primary user journey：
- Information architecture：
- Feedback and progress：
- Human control / approvals：
- Error messages and recovery：
- Accessibility：

主觀體驗也要附上觸發該感受的具體操作。

## 6. Differentiation

### Unique capabilities

-

### Common capabilities presented differently

-

### Missing or weaker capabilities

-

## 7. Alternatives and comparison

### Closest alternatives

| Product / workflow | Why users would compare it |
| --- | --- |
| | |

### Comparison

| Dimension | This product | Alternative | Evidence |
| --- | --- | --- | --- |
| Positioning | | | |
| Core features | | | |
| Interaction model | | | |
| Deployment | | | |
| Pricing | | | |
| Constraints | | | |

若比較來自文件而非相同條件實測，必須明確標示。

## 8. Pricing, requirements, and constraints

- Pricing model：
- Required subscriptions / API keys：
- Supported platforms：
- Hardware / runtime requirements：
- Usage limits：
- License：
- Known constraints：

## 9. Architecture and data flow

- Model：
- Agent runtime / harness：
- Orchestrator：
- Execution environment：
- State and persistence：
- Git strategy：
- External services：

```text
User → Product / orchestrator → Runtime → Execution environment → Services
```

## 10. Security and privacy

- Execution boundary：
- Host permissions：
- Secret handling：
- Network access：
- Data sent to providers：
- Local / cloud retention：
- Approval controls：
- Destructive-action safeguards：

未確認的 security claim 必須標成 `[Unverified]`。

## 11. Review focus

完成產品與 feature mapping 後，再列出最值得透過 hands-on experience 回答
的問題。不是所有功能都需要在同一輪驗證。

| ID | Product claim / question | Why it matters | Validation method | Evidence needed |
| --- | --- | --- | --- | --- |
| Q1 | | | `official-docs` | |
| Q2 | | | `source-review` | |

Validation method 可使用 `official-docs`、`source-review`、`demo-review`、
`hands-on` 或 `prototype`。

## 12. Hands-on experience (optional)

- Decision：`run` / `skip` / `defer`
- Reason：

若選擇 `skip` 或 `defer`，記錄原因後即可移至下一節；不需要為了完成模板而
執行產品，status 維持 `untried`。

### Environment

- Base repository / data：
- Starting commit / version：
- Credentials：只記錄類型，不得記錄 secret value
- Host OS / hardware：
- Network / permission constraints：
- Resource limits：

### Setup and reproducibility

#### Prerequisites

-

#### Steps

```text
Record the minimum reproducible commands without secrets.
```

#### Setup experience

- Time to first successful run：
- Blocking errors：
- Workarounds：
- Cleanup / uninstall：

### Scenarios

| ID | Task | Acceptance criteria | Result |
| --- | --- | --- | --- |
| T1 | | | `not-run` |

Result 使用 `pass`、`partial`、`fail`、`blocked` 或 `not-run`。

### Findings

依 scenario 記錄 expected、actual、evidence 與是否可重現。不要只貼 agent
summary；優先引用 test output、diff、log 或 screenshot。

### T1 — Scenario name

- Expected：
- Actual：
- Evidence：
- Reproducible：`yes` / `no` / `unknown`
- Notes：

### Reliability and operations

- Failure isolation：
- Retry / resume：
- Timeout / cancellation：
- Concurrent execution：
- Cleanup：
- Logs / observability：
- Reproducibility：

### Performance and cost

| Metric | Result | Conditions |
| --- | --- | --- |
| Setup duration | | |
| Task duration | | |
| Token / API cost | | |
| Local CPU / memory | | |
| Additional subscription | | |

### Out of scope

- 尚未測試：
- 不適用：

## 13. Scorecard

只評分實際測試過的面向，並為每個分數附 evidence。

| Dimension | Score | Evidence |
| --- | --- | --- |
| Setup and onboarding | `N/A` | |
| Core task quality | `N/A` | |
| UX and control | `N/A` | |
| Isolation and safety | `N/A` | |
| Reliability and recovery | `N/A` | |
| Observability | `N/A` | |
| Extensibility | `N/A` | |
| Performance / cost | `N/A` | |

## 14. Strengths

-

## 15. Weaknesses and constraints

-

## 16. Integration notes

- 值得採用或參考的能力：
- 可能的整合方式：
- 需要修改的系統：
- 技術與維護風險：
- License 與 attribution：
- Exit / rollback plan：

## 17. Verdict

- Verdict：`undecided` / `adopt` / `reference` / `pause` / `reject`
- Confidence：`low` / `medium` / `high`
- Decision rationale：
- What could change this decision：

## 18. Open questions

-

## 19. Next actions

-

## 20. Evidence and references

### Artifacts

- Logs：
- Screenshots：
- Diffs / branches：

### Sources

- Official documentation：
- Repository：
- Changelog：
- Third-party material：

## 21. Review history

| Date | Version / commit | Change | Commit |
| --- | --- | --- | --- |
| YYYY-MM-DD | | Initial review | |
