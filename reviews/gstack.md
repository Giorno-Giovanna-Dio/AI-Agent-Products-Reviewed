# gstack

> 狀態：`backlog`
>
> 注意：目前只完成資料查核，尚未獨立安裝與實測。

## Metadata

- Repository：[garrytan/gstack](https://github.com/garrytan/gstack)
- License：MIT
- Tested commit：尚未測試
- 發現來源：Conductor Quick Start

## 產品定位

gstack 是 Garry Tan 維護的獨立 open-source agent skills 套件，不是
Conductor 自己的 workflow template。Conductor 可以透過 Quick Start
初始化 gstack，但兩者應分開評測。

它把軟體開發流程拆成具名的專業角色與 skills，涵蓋產品構想、規劃、
engineering/design review、實作檢查、瀏覽器 QA、發佈及 retrospective。

官方描述的主要生命週期是：

```text
office-hours → plan → implement → review → QA → ship → retro
```

## 已確認的能力

- `/office-hours`：整理產品構想與需求。
- `/autoplan`：串接 CEO、design 與 engineering review。
- `/review`：對目前 branch 的變更進行 pre-landing review。
- `/browse`：透過 Chromium 進行瀏覽器操作與截圖。
- `/qa`：測試、修正、重新驗證，並為修正產生 regression tests。
- `/qa-only`：只產出 QA 報告，不修改程式碼。
- `/ship`：執行測試、檢查 coverage、push 並建立 pull request。
- `/codex`：使用 Codex 提供第二意見。

完整 skill 清單仍在快速演進，因此正式評測時應固定 tested commit，而不是只
記錄 `main` branch。

## 與 Conductor 的關係

Conductor 是 workspace 與 agent harness 管理層；gstack 是放進 agent
context 的工作流程和角色規則。兩者可以一起使用，但解決的問題不同：

| 工具 | 主要責任 |
| --- | --- |
| Conductor | 隔離 worktrees、執行不同 agent runtimes、管理 diff 與 PR |
| gstack | 規範 agent 在規劃、review、QA 與 shipping 階段如何工作 |

因此，使用 gstack 不代表一定需要 Conductor；在 Conductor 看到 gstack，也
不代表它是 Conductor 的內建專屬能力。

## 待驗證項目

- 安裝流程及其對既有 agent instructions 的影響。
- 實際支援哪些 agent runtimes，以及各 runtime 的功能差異。
- `/autoplan` 是否能有效收斂需求，而不是只增加輸出長度。
- `/review` 與獨立 verifier agent 的重疊程度。
- `/qa` 的 browser automation、修正品質與 regression tests。
- `/ship` 是否會做出超出預期的 repository 或 GitHub 寫入操作。
- Skills 數量、context consumption 與 token 成本。

## 初步判斷

它值得作為獨立候選繼續測試，尤其適合研究如何把 proposal、review、
verification 與 shipping 組成可重複的 agent workflow。目前尚無足夠實測
證據給出 `adopt` 或 `reject` 結論。

## 參考資料

- [gstack repository](https://github.com/garrytan/gstack)
- [gstack skills reference](https://github.com/garrytan/gstack/blob/main/docs/skills.md)
- [Conductor v0.43.0: Codex skills and gstack Quick Start](https://www.conductor.build/changelog/0.43.0-codex-skills-plan-mode-fast-mode)
