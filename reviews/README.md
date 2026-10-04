# Product Review Guide

這個 repository 保存的是產品洞察，不是其他專案 README 的副本。原始碼留在
upstream 或 fork；review 只挑出對未來 2D／3D AI agent workspace 有幫助的
概念。

每份 review 先回答五個問題：

1. 這是什麼產品？
2. 它有哪些重要 features？
3. 它的主打賣點是什麼？
4. 它適合哪些使用情境？
5. 我們可以從中學到什麼？

## Writing style

- 使用簡單、直接的語言，讓沒看過產品的人也能理解。
- 不逐段翻譯官方 README，也不羅列所有 API 和設定。
- 只保留與產品定位、主要 workflow 或 workspace 設計有關的 features。
- 說明 feature 帶來的價值，不只寫名稱。
- 區分官方說法、親自體驗與我們的判斷，但不必讓標籤干擾閱讀。
- 不確定的資訊直接寫「尚未確認」。
- 沒有實測也可以完成產品研究；只有實際用過才將 status 設為 `tried`。

## What to extract

特別注意這些可借鑑的方向：

- Agents、tasks 和 goals 如何被組織。
- Context、memory 和 handoff 如何流動。
- Runtime、sandbox、branches 和 permissions 如何被管理。
- 進度、狀態、diffs 和 artifacts 如何呈現。
- 使用者如何委派、比較、介入和驗證。
- 它在 2D workspace 和 3D workspace 裡分別會變成什麼。

2D 和 3D 都是 workspace：

- 2D：平面工作空間，例如 pixel 風格辦公室、地圖或樓層。
- 3D：可走進的辦公室介面，例如 Agent Office。
- 都不是在做 3D 物件、立體圖譜，也不是 ADE dashboard。

## Optional follow-up

只有當文件或 source 無法回答重要問題時，才增加 hands-on、prototype、安全
分析或公平比較。這些內容放在 review 後方作為補充，不應蓋過產品介紹和可
學習的重點。

## Status

- `untried`：已研究，但尚未實際使用。
- `tried`：已經實際體驗過。

使用 [`_template.md`](_template.md) 建立新的產品 review。

若要把一個 GitHub repo 或產品網址整理成 Cell 並開獨立 PR，呼叫
[`/create-cell-pr`](../.cursor/skills/create-cell-pr/SKILL.md)。一個來源只
建立一個 Cell PR；多個來源請平行呼叫多次。
