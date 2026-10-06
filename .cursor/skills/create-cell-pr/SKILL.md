---
name: create-cell-pr
description: Turn a GitHub repo or product URL into a Cell 產品洞察筆記 and open one dedicated pull request. Use when the user shares a repository, asks to form a Cell, write an insight note, review an AI product, or wants a sub-agent to package research without cloning upstream code into this repo. Also trigger on 洞察筆記、Cell PR、產品介紹、gstack-style review, or “幫我研究這個 repo”.
---

# Create Cell PR

把一個外部產品整理成 Cell 洞察筆記，並為它單獨開一個 PR。

這個 repository 是 2D／3D AI agent workspace 的技術雷達。目標是萃取可學習的
產品概念，不是複製對方的 README，也不是把 upstream 程式碼放進本 repo。

## When to use

- 使用者丟出一個 GitHub repo 或官方產品網址。
- 使用者要求寫「產品洞察筆記」、形成 Cell、或開 Cell PR。
- 需要讓不同 sub-agents 平行處理多個產品。
- 明確呼叫 `/create-cell-pr`。

不要用這個 skill 做完整 QA、clone 評測、或一次把很多產品塞進同一個 PR。

## Inputs

最少需要一個來源：

```text
/create-cell-pr https://github.com/owner/repo
```

可選：

- 使用者已經寫下的體驗或重點
- 想比較的替代產品
- 是否只要研究、不要 hands-on

若一次給多個 URL，每個來源開一個獨立 sub-agent / 獨立 PR。不要合併處理。

## Output

每個來源只產出一個 Cell：

- 一份洞察筆記：`reviews/<slug>.md`
- catalog 中的一筆 Cell
- README 狀態表的一列
- 一條專用 branch 和一個 draft PR

## Hard rules

1. **不要把 upstream clone 進這個 git worktree。** 原始碼留在對方 repo。
   若真的需要執行，放到本 repo 外的 disposable 目錄，例如
   `/workspace-labs/<slug>`，並且不要 commit 進去。
2. **不要照抄官方 README、API 清單或全部 skills。** 用簡單的話重寫。
3. **一個 Cell 開一個 PR。** 不要把多個新產品放進同一個 PR。
4. **Cell ID 是 canonical source URL。**
   - GitHub repo：`https://github.com/{owner}/{repo}`
   - 移除 `.git`、query、fragment、尾端 `/`
   - 沒有公開 repo 的產品：使用官方網站 URL
5. **先寫產品與 features，不要先寫 testing。** Hands-on 是選填。
6. **skills、templates、輔助功能不必親自測完。** 文件或 source 已夠清楚就可以。
7. 未實際使用時，status 維持 `untried`。
8. 不確定就寫「尚未確認」，不要把官方宣稱寫成已驗證事實。
9. 不要提交 secrets、token、完整資料庫或大型 generated files。

## Workflow

### 1. Confirm the Cell

- 讀 `README.md`、`reviews/README.md`、`reviews/_template.md`。
- 從 `main` 開一條只服務這個 Cell 的 branch。
- 若目前 agent 有指定 branch 命名規則，遵守它；否則使用
  `cursor/add-<slug>-cell`。
- 先檢查這個 Cell ID 是否已存在。已存在就更新同一份 review，不要新建重複
  Cell。

Canonical ID：

```text
repository Cell: https://github.com/mattpocock/sandcastle
product Cell:    https://www.conductor.build/
```

Repo 改名或轉移時，新 URL 當 ID，舊網址放進 `aliases`。

Slug 用小寫、連字號，例如 `gstack`、`gbrain`、`sandcastle`。

### 2. Research, do not copy

先從官方來源理解產品，而不是開始測試：

- GitHub about、README、docs、LICENSE
- 官方網站或 changelog
- 和本 repo 已有 Cells 的關係

只收集能回答這五件事的資訊：

1. 這是什麼產品？
2. 有哪些真正重要的 features？
3. 主打賣點是什麼？
4. 適合哪些使用情境？
5. 我們可以學到什麼？

### 3. Write the insight note

複製 `reviews/_template.md` 為 `reviews/<slug>.md`。正文使用繁體中文，
語言簡單直接。重點是這五段：

```markdown
# Product name

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/<slug>.md)
>
> Cell ID：[https://github.com/owner/repo](https://github.com/owner/repo)
>
> Status：`untried`
>
> Category：
>
> Last updated：YYYY-MM-DD

## 產品介紹

## 主要 Features

## 主打賣點

## 使用情境

## 我們可以學什麼
```

寫作要求：

- `產品介紹`：一至兩段說明它是什麼、幫誰、使用者怎麼用。
- `主要 Features`：只寫影響定位或 workflow 的功能，每個功能說明價值。
- `主打賣點`：它最想被記住的差異，以及哪些只是舊能力的新包裝。
- `使用情境`：2–3 個情境，寫適合誰、何時用、帶來什麼價值。
- `我們可以學什麼`：萃取這個產品在未來 **2D workspace** 和 **3D workspace**
  裡會變成什麼。兩者都是工作空間，不是資料視覺化，也不是 3D 物件。

可以保留 `初步看法`、`後續補充`、`Sources`。不要讓測試步驟、API 或安裝
教學蓋過這五段。

特別萃取：

- agents、tasks、goals 如何被組織
- context、memory、handoff 如何流動
- runtime、sandbox、permissions 如何管理
- 進度、artifacts、review 如何呈現
- 人如何委派、比較、介入和驗證
- 它比較像辦公室、員工、共用記憶，還是只是 ADE dashboard

### 2D / 3D workspace 定義

寫 `我們可以學什麼` 時，使用這組定義，不要自行發明：

- **2D workspace**：平面的工作空間。可以是 pixel 風格辦公室、地圖、樓層或
  俯視畫面。人仍然在「一個地方」裡工作，只是介面是二維。
- **3D workspace**：可走進的辦公室介面，例如
  [Agent Office](https://github.com/AgentSystemLabs/agent-office)。重點是
  看到員工在場、誰在哪裡做事，不是去做 3D 模型、立體圖譜或可旋轉物件。
- **都不是**：把知識圖譜立體化、做裝飾用 3D object，或把 ADE 側欄叫成
  workspace。
- **ADE dashboard**（Conductor、Firstmate、Maestro 這類）可以記錄，但不要
  把它寫成我們要做的 2D／3D workspace。

因此不要寫「若 3D 沒幫助就用 2D」。要寫的是：這個產品進到 2D workspace
會長怎樣，進到 3D office 又會長怎樣。

### 4. Register the Cell

優先更新 `cells.yaml`。若這個 branch 還只有 `projects.yaml`，就更新它，
但 Cell ID 仍使用 canonical URL。

必要欄位：

- `id`：canonical source URL
- `name`
- `source_type`：`repository` 或 `product`
- `aliases`
- `evaluation.status`：預設 `untried`
- `evaluation.review`：`reviews/<slug>.md`
- `evaluation.review_web`：GitHub 渲染 URL（見下方網頁閱讀）

同步更新 `README.md` 狀態表，一列只代表一個 Cell。Cell 名稱欄連到 **GitHub
渲染**，不要用相對路徑 `reviews/<slug>.md`（避免在 IDE 開 raw Markdown）。

#### 網頁閱讀（所有 Cell 通用）

洞察筆記的 canonical 檔案仍是 `reviews/<slug>.md`，但**預設閱讀體驗是瀏覽器中的
GitHub 渲染**：

| 時機 | URL |
| --- | --- |
| PR 尚未 merge | `https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/<branch>/reviews/<slug>.md` |
| 已在 `main` | 同上，`<branch>` 改為 `main` |

撰寫規則見 [`reviews/README.md`](../../../reviews/README.md)。

### 5. Open the PR

- 只 commit 這個 Cell 的檔案。
- commit message 例如：`docs: add <name> Cell insight note`
- push 後開 **draft PR**，base 為 `main`。
- PR 只描述這一個 Cell，不要夾帶重構或其他產品。

PR 應讓人先看到洞察筆記，再決定 merge。PR 描述裡也應附上 **GitHub 渲染**
連結（優先指向 PR branch）。

### 6. 回覆使用者（閱讀連結）

開完 draft PR 後，在**對話結尾**用一小段 **「網頁閱讀」** 附上連結，方便使用者在
瀏覽器讀完整筆記，而不是請對方去開 workspace 裡的 `.md`：

1. **GitHub 渲染（立即可用）**：`<branch>` 換成本次 PR 的 branch。
2. 可簡短附 PR URL；洞察內容以 GitHub 渲染連結為主。

## Hands-on is optional

只有這些情況才執行產品：

- 文件或 source 無法回答會影響 workspace 設計的問題
- interaction quality 無法從文字判斷
- 安全、權限或資料處理是核心風險

否則寫 `Not tried yet`，status 保持 `untried`。

## Done when

- [ ] 沒有把 upstream 放進本 repo
- [ ] `reviews/<slug>.md` 有五個核心段落
- [ ] Cell ID 是唯一的 canonical URL
- [ ] catalog 和 README 已更新（含網頁閱讀連結與 `review_web`）
- [ ] 只有這個 Cell 的專用 PR
- [ ] 對話結尾已附上 GitHub 渲染閱讀連結
- [ ] 用簡單的話寫出可學習的重點，而不是翻譯官方文件
