# AI Agent Products Reviewed

這個 repository 是建立 2D／3D AI agent workspace 前的產品研究與技術雷達。
目標不是替產品排名，而是理解目前前沿產品如何組織 agents、tasks、context、
execution environments 與 human oversight，並萃取可用於未來 workspace
設計的 interaction 和 system primitives。

可自行執行的候選專案會 clone 到本 repository 以外的獨立實驗區；這裡只
保存來源資訊、產品與 feature 分析、實際體驗，以及對 2D／3D workspace 的
設計啟示。沒有公開 repository 的產品則記錄官方頁面與可取得的版本資訊。

## Research goals

每個產品 review 應協助回答：

- 它代表了哪一種新的 agent workspace 或 interaction model？
- 它如何呈現 agents、tasks、branches、sandboxes、artifacts 與進度？
- 使用者如何委派、比較、介入、驗證及收回控制權？
- 哪些能力來自 model，哪些來自 agent runtime 或 orchestration？
- 它在 2D workspace（平面／pixel 風格工作空間）裡會變成什麼？
- 它在 3D workspace（可走進的辦公室介面，不是 3D 物件）裡會變成什麼？
- 哪些 pattern 值得採用、重新設計或明確避免？

主要研究面向：

1. Workspace 與 spatial organization
2. Multi-agent orchestration
3. Context、memory 與 handoff
4. Runtime、sandbox 與 permissions
5. State、progress 與 observability
6. Human-in-the-loop control
7. Artifacts、provenance 與 review
8. Collaboration 與 extensibility

## Repository 結構

- [`projects.yaml`](projects.yaml)：候選專案的結構化 metadata。
- [`reviews/README.md`](reviews/README.md)：共同評測規則與證據標準。
- [`reviews/`](reviews/)：每個專案的實際體驗與結論。
- `/workspace-labs/<project-id>`：建議的本機實驗位置，不屬於本
  repository，也不會被 Git 追蹤。

## 評測流程

1. 將候選產品加入 `projects.yaml`，狀態設為 `untried`。若來源是 GitHub
   repo 或官方網址，可呼叫
   [`/create-cell-pr`](.cursor/skills/create-cell-pr/SKILL.md) 讓 sub-agent
   寫洞察筆記並開獨立 PR。
2. 若有公開原始碼，clone 到獨立實驗區並記錄實際測試的完整 commit SHA；
   否則記錄產品版本。
3. 依照 [`reviews/README.md`](reviews/README.md) 的方法，使用
   [`reviews/_template.md`](reviews/_template.md) 建立評測文件。
4. 只有在關鍵問題無法透過官方文件、source 或 demo 釐清時，才依 upstream
   推薦方式進行 hands-on validation；Docker 並非強制要求。
5. 更新狀態與初步看法，並以一項明確變更建立一個 atomic commit。

候選專案不應直接放在本 repository 之下。若實驗時需要修改產品程式碼，
應另外 fork 該產品；修改提交在產品 fork，評測結果則提交在這裡。

## 狀態

| Cell | Cell ID | Tested revision | Runtime | Status |
| --- | --- | --- | --- | --- |
| [Conductor](reviews/conductor.md) | [Website](https://www.conductor.build/) | 未記錄 | macOS native | `tried` |
| [gstack](reviews/gstack.md) | [GitHub](https://github.com/garrytan/gstack) | 尚未測試 | Agent skills | `untried` |
| [gbrain](reviews/gbrain.md) | [https://github.com/garrytan/gbrain](https://github.com/garrytan/gbrain) | 尚未測試 | Agent memory | `untried` |

狀態值：

- `untried`：已收錄，但尚未實際使用。
- `tried`：已經實際體驗過。
