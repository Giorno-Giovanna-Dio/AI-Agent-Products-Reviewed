# 貢獻準則

這個 repository 是公開的 AI agent products 蒐集樞紐。產品、框架、工作台、
記憶層和 runtime 散落在不同的 repository 與官方網站。這裡把它們收成同一份
目錄：每個產品是一個 **Cell**，看得出它的性質，以及它在一組 AI agent
orchestration team 裡補上的部分。

目錄用來對照，不用來排名。原始碼留在原來的專案。這裡只保存來源、洞察，
以及我們從公開文件或實際體驗裡讀到的東西。授權是 [MIT](LICENSE)。

## 你可以怎麼參加

- 提出一個還沒收錄的產品。
- 為一個產品寫洞察筆記，並把它登記成 Cell。
- 修正已收錄 Cell 的過時資訊、失效連結，或放錯的席位。
- 補上你實際用過的體驗。只有真正用過，才把 status 改成 `tried`。

只想提案、先不寫完整筆記時，開一個 Issue，用「建議收錄一個產品」範本。

## 什麼適合收進來

收進來的是 AI agent 產品，或組起 agent 團隊時會用到的一塊：

- 會拆工、派工、交接的編排層
- 人用來監督、比較、介入的工作台
- 看得到誰在做事的空間
- 真正執行任務的 agent 或組裝它的 runtime
- 跨回合還用得到的記憶
- 技能、程序、完成定義
- 執行時的沙盒、終端、瀏覽器或權限邊界
- 用來評測與留下軌跡的工具
- 人和 agent 一起改的畫面、文件、表格

一個產品只要能放進上面某個席位，就值得收。它不一定要是桌面 App，也可以是
函式庫、外掛、教程，或一份會被 agent 拿去用的想法規格。

已經收錄的來源不要再開一個 Cell。Repository 改名或轉移時，新的 canonical
URL 成為 ID，舊網址放進 `aliases`。

## 一個新 Cell 要交什麼

一個新產品只開一個 pull request，裡面有三處更新：

1. `reviews/<slug>.md`：洞察筆記。從 [`reviews/_template.md`](reviews/_template.md) 複製。
2. [`cells.yaml`](cells.yaml)：一筆 metadata。先確認這個 Cell ID 還沒有出現。
3. 五份 README 的席位表，同一個席位下各加一列，Cell 連結相同：
   - [`README.md`](README.md)（英文，倉庫預設首頁）：欄位 Cell、Nature、Contribution。
   - [`README.zh-TW.md`](README.zh-TW.md)（繁體中文）：欄位 Cell、性質、貢獻。
   - [`README.ja.md`](README.ja.md)（日文）：欄位 Cell、種類、貢献。
   - [`README.ko.md`](README.ko.md)（韓文）：欄位 Cell、성질、기여。
   - [`README.es.md`](README.es.md)（西班牙文）：欄位 Cell、Naturaleza、Contribución。

Slug 用小寫和連字號，例如 `gstack`、`agent-office`。

### Cell ID

ID 是來源的 canonical URL。

- 有公開 GitHub repository：`https://github.com/{owner}/{repo}`
- 沒有公開 repository：使用官方網站 URL
- 去掉 `.git`、query、fragment 和尾端 `/`

### 筆記要回答的五件事

寫作細節見 [`reviews/README.md`](reviews/README.md)。五段都要在：

1. 產品介紹
2. 主要 Features
3. 主打賣點
4. 使用情境
5. 我們可以學什麼

用繁體中文，句子短、直接。用自己的話說明這個產品幫誰、怎麼用、哪裡不一樣。
不要逐段翻譯官方 README，也不要羅列全部 API。

分得清這三層：官方說法、你親自體驗到的、你的判斷。不確定就寫「尚未確認」。
沒有實際安裝也可以完成一篇筆記；這時 status 維持 `untried`。

### 席位、性質、貢獻

README 的分類表每個 Cell 只占一個主要席位。欄位是：

| Cell | 性質 | 貢獻 |
| --- | --- | --- |

- **席位**擇一：指揮、治理、工作台、在場、員工、記憶、方法、邊界、驗證、交付表面。定義在 README 該節的席位表。
- **性質**：這個產品是什麼的短語，例如「桌面 ADE」「編排框架」「長期記憶」。
- **貢獻**：一句話，寫它在這個席位補上的能力。它若還碰到別的席位，寫在這一句裡，不另開一列。

Cell 名稱連到 GitHub 渲染後的筆記。PR 還沒合併時，連結裡的分支用你的
branch；合併進 `main` 時改成 `main`。`cells.yaml` 的 `evaluation.review_web`
用同一個 URL。

筆記開頭的 Category 寫成「席位／性質」。

### cells.yaml

沿用檔案裡現有的形狀。至少要有：

- `id`：canonical URL
- `name`
- `source_type`：`repository` 或 `product`
- `aliases`
- `evaluation.status`：預設 `untried`
- `evaluation.review`：`reviews/<slug>.md`
- `evaluation.review_web`：GitHub 渲染 URL
- `evaluation.updated_at`：`YYYY-MM-DD`

`license` 只寫你從公開頁面確認到的授權。沒看到就寫 `unknown`。

## Pull request

1. Fork 這個 repository，從 `main` 開分支。
2. 一個新產品一個 PR。修正同一個 Cell 的筆記、分類和 metadata 可以放在一起。
3. 標題可以用 `docs: add <name> Cell`。修正則寫清楚改了哪個 Cell。
4. PR 說明裡放上這篇筆記的 GitHub 渲染連結，讓人先在瀏覽器讀完再決定要不要合併。
5. 不要把別的重構、或其他新產品，夾進同一個 PR。

使用 Cursor、而且這個 repo 的 agent skill 可用時，可以呼叫
[`/create-cell-pr`](.cursor/skills/create-cell-pr/SKILL.md)。它走的是同一套
規則：一個來源、一篇筆記、一個 PR。人直接改檔也可以，不需要先裝任何工具。

## 不要放進來的東西

- upstream 的程式碼、submodule、整包 clone，或大型產生檔。需要試跑時，clone
  到這個 repository 外面。
- token、密碼、cookie、私人對話、未公開的客戶資料，或完整資料庫。
- 把官方數字、benchmark 或「已支援」寫成你已經驗證過的事實。
- 多個新產品合在同一個 PR。

## 合併時會看什麼

- 這個 Cell ID 是唯一的 canonical URL。
- 筆記用自己的話回答了那五件事。
- 席位只選一個，性質和貢獻對得上筆記裡的描述。
- 沒有把別人的程式碼帶進來。
- 不確定的地方寫了「尚未確認」。

維護者可能會請你把席位移到另一節，或把重複的來源併進既有 Cell 的 `aliases`。

## 支持這個目錄

補上一個 Cell，就是在支持這份目錄。若要資助維護，用
[GitHub Sponsors](https://github.com/sponsors/Giorno-Giovanna-Dio)。
