# AI Agent Products Reviewed

這個 repository 是 AI 產品實驗的控制中心。可自行執行的候選專案會
clone 到本 repository 以外的獨立實驗區；這裡只保存可重現的來源資訊、
測試紀錄與採用決策。沒有公開 repository 的產品則記錄官方頁面、版本與
實測環境。

## Repository 結構

- [`projects.yaml`](projects.yaml)：候選專案的結構化 metadata。
- [`reviews/README.md`](reviews/README.md)：共同評測規則與證據標準。
- [`reviews/`](reviews/)：每個專案的實際體驗與結論。
- `/workspace-labs/<project-id>`：建議的本機實驗位置，不屬於本
  repository，也不會被 Git 追蹤。

## 評測流程

1. 將候選產品加入 `projects.yaml`，狀態設為 `untried`。
2. 若有公開原始碼，clone 到獨立實驗區並記錄實際測試的完整 commit SHA；
   否則記錄產品版本。
3. 優先依照 upstream 官方文件啟動；Docker 並非強制要求。
4. 依照 [`reviews/README.md`](reviews/README.md) 的方法，使用
   [`reviews/_template.md`](reviews/_template.md) 建立評測文件。
5. 更新狀態與結論，並以一項明確變更建立一個 atomic commit。

候選專案不應直接放在本 repository 之下。若實驗時需要修改產品程式碼，
應另外 fork 該產品；修改提交在產品 fork，評測結果則提交在這裡。

## 狀態

| Project | Source | Tested revision | Runtime | Status | Verdict |
| --- | --- | --- | --- | --- | --- |
| [Conductor](reviews/conductor.md) | [Website](https://www.conductor.build/) | 未記錄 | macOS native | `tried` | `undecided` |
| [gstack](reviews/gstack.md) | [GitHub](https://github.com/garrytan/gstack) | 尚未測試 | Agent skills | `untried` | `undecided` |

狀態值：

- `untried`：已收錄，但尚未實際使用。
- `tried`：已經實際體驗過。

採用與否由獨立的 `Verdict` 欄位表示，不混入使用狀態。
