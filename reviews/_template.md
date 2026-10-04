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

## 2. Executive summary

- 一句話定位：
- 解決的主要問題：
- 最有辨識度的能力：
- 最適合：
- 不適合：
- 初步結論：

## 3. Why evaluate it?

### Motivation

為什麼值得投入時間？它可能補足現有產品或 workflow 的哪個缺口？

### Hypotheses

| ID | Claim to verify | Evidence needed |
| --- | --- | --- |
| H1 | | |
| H2 | | |

## 4. Test scope

### Environment

- Base repository / data：
- Starting commit / version：
- Credentials：只記錄類型，不得記錄 secret value
- Network / permission constraints：
- Resource limits：

### Scenarios

| ID | Task | Acceptance criteria | Result |
| --- | --- | --- | --- |
| T1 | | | `not-run` |
| T2 | | | `not-run` |

Result 使用 `pass`、`partial`、`fail`、`blocked` 或 `not-run`。

### Out of scope

- 尚未測試：
- 不適用：

## 5. Setup and reproducibility

### Prerequisites

-

### Steps

```text
Record the minimum reproducible commands without secrets.
```

### Setup experience

- Time to first successful run：
- Blocking errors：
- Workarounds：
- Cleanup / uninstall：

## 6. Hands-on findings

依 scenario 記錄 expected、actual、evidence 與是否可重現。不要只貼 agent
summary；優先引用 test output、diff、log 或 screenshot。

### T1 — Scenario name

- Expected：
- Actual：
- Evidence：
- Reproducible：`yes` / `no` / `unknown`
- Notes：

## 7. Product capabilities

### Core workflow

-

### Agent / model behavior

-

### Environment and isolation

-

### Review and integration

-

### Extensibility

-

## 8. UX experience

- Onboarding：
- Daily workflow：
- Information density：
- Feedback and progress：
- Error messages：
- Recovery：
- Accessibility：

主觀體驗也要附上觸發該感受的具體操作。

## 9. Architecture and data flow

- Model：
- Agent runtime / harness：
- Orchestrator：
- Execution environment：
- State and persistence：
- Git strategy：
- External services：

```text
User → Orchestrator → Agent runtime → Sandbox / host → External services
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

## 11. Reliability and operations

- Failure isolation：
- Retry / resume：
- Timeout / cancellation：
- Concurrent execution：
- Cleanup：
- Logs / observability：
- Reproducibility：

## 12. Performance and cost

| Metric | Result | Conditions |
| --- | --- | --- |
| Setup duration | | |
| Task duration | | |
| Token / API cost | | |
| Local CPU / memory | | |
| Additional subscription | | |

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

## 14. Comparison

### Baseline

- Compared with：
- Controls held constant：
- Known unfair differences：

| Dimension | This product | Baseline | Evidence |
| --- | --- | --- | --- |
| | | | |

### Unique vs overlapping capabilities

- 真正獨有：
- 相同能力、不同呈現：
- Baseline 較強：

## 15. Strengths

-

## 16. Weaknesses and constraints

-

## 17. Integration notes

- 值得採用或參考的能力：
- 可能的整合方式：
- 需要修改的系統：
- 技術與維護風險：
- License 與 attribution：
- Exit / rollback plan：

## 18. Verdict

- Verdict：`undecided` / `adopt` / `reference` / `pause` / `reject`
- Confidence：`low` / `medium` / `high`
- Decision rationale：
- What could change this decision：

## 19. Open questions

-

## 20. Next actions

-

## 21. Evidence and references

### Artifacts

- Logs：
- Screenshots：
- Diffs / branches：

### Sources

- Official documentation：
- Repository：
- Changelog：
- Third-party material：

## 22. Review history

| Date | Version / commit | Change | Commit |
| --- | --- | --- | --- |
| YYYY-MM-DD | | Initial review | |
