# Atomic Agents

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-atomic-agents-cell-ca43/reviews/atomic-agents.md)
>
> Cell ID：[https://github.com/Eigenwise/atomic-agents](https://github.com/Eigenwise/atomic-agents)
>
> Status：`untried`
>
> Category：Python schema-composed agent library
>
> Last updated：2026-10-10

## 產品介紹

Atomic Agents 是 Eigenwise 的開源 Python 函式庫，用來把 AI 流程拆成小零件。它服務的是自己寫接線的開發者，而不是坐在桌面 ADE 裡看終端機的人。官方文件在 [eigenwise.github.io/atomic-agents](https://eigenwise.github.io/atomic-agents/)。底層用 Instructor 呼叫模型，用 Pydantic 檢查輸入和輸出。本次只讀 GitHub `main` 的 README、LICENSE、升級說明、文件與 `pyproject.toml`，並對過 PyPI 版本號，沒有安裝。

一個 AtomicAgent 做一件事。系統提示寫背景、步驟和輸出規定。進和出各是一份 schema。對話回合放在 ChatHistory。這一輪才要看的資料，從 context provider 注入系統提示。下一步怎麼走，寫在你的 Python 裡。`main` 的 `pyproject.toml` 與 PyPI 都是 `2.10.3`，需要 Python 3.12 以上。授權是 MIT，著作權人是 Kenny Vaneetvelde。

## 主要 Features

### 一張桌子：一份輸入、一份輸出

AtomicAgent 用型別參數綁定 input schema 和 output schema，兩者都繼承 `BaseIOSchema`。預設是一句 `chat_message` 進、一句 `chat_message` 出。要固定形狀就自己加欄位，例如搜尋詞、信心分數、建議問題。`SystemPromptGenerator` 把背景、步驟、輸出規定組成系統提示。這張桌子收什麼、交什麼，在程式裡看得到。換題目時改 schema 和提示，而不是把整段自由文字重寫一遍。

### 用 schema 對齊來交接

文件把組合寫成 schema 對齊：上一棒的 output schema 就是下一棒工具或 agent 的 input schema。查詢 agent 可以直接產出搜尋工具能吃的那份結構，中間不必再翻譯。要換搜尋服務，就改成另一個工具的 input schema。零件能換，是因為接口是同一張表。

### 工具是機器，呼叫點留在程式裡

工具指南寫明框架沒有 `tools=[...]` 這種參數。工具是帶 input schema、output schema 和 `run()` 的物件，本身不知道 agent、提示或記憶。兩種用法。順序已經知道：直接呼叫，agent 的輸出就是工具的輸入。下一步要看使用者怎麼說：做一個 choice agent，輸出是幾個工具輸入 schema 的 Union，Instructor 只接受其中一種；你的程式再依型別分派。加一台機器就要加一個分支，選擇留在看得到的地方。工具一多時，文件建議改成分層路由。Union 變大之後好不好用，尚未實測。

Atomic Forge 是一組可下載的工具原始碼，例如搜尋、計算機、網頁、PDF、天氣。CLI `atomic`（Atomic Assembler）把選中的工具下載進你的專案，附 schema、範例、依賴和測試。README 仍寫之後也會下載 Agents 和 Pipelines，這部分尚未確認。MCP connector 可以把外部 MCP 工具收成同樣的 schema 物件。現行 `MCPTransportType` 的參數預設是 `HTTP_STREAM`，也有 SSE 和 STDIO。MCP 是接外面工具的一種方式，組合模型仍是 schema 和你寫的呼叫。

### 回合抽屜和當下公告是兩條線

ChatHistory 存對話回合。一則訊息有角色、schema 內容和 turn id。`run()` 會開新回合，把輸入和回覆寫進歷史，並可存檔、讀回、限制訊息數。Context provider 在每次呼叫時把一段文字放進系統提示，例如檢索結果、使用者身份、時間、任務進度。文件把兩者分開：歷史跟著對話留下；context 每次重算，適合這一輪才有效的資料。

多個 agent 怎麼共用脈絡，記憶指南列了五種你自己接的寫法：共用同一份 ChatHistory；各自一份歷史；把上一棒的輸出手動放進下一棒的歷史；主管和工人共用一個 context provider；長流程把事實放進外部記憶，再經 provider 注入。哪一種比較不容易把上下文塞滿，尚未實測。

### 流程是食譜，主管是另一個 agent

編排指南是寫法，不是一個會自己維持編制的 runtime。順序管線把上一份輸出餵給下一份輸入。平行是多個 agent 同時處理同一份輸入再匯總。Router 先分類，再交給對應的專職 agent。Supervisor 是另一個 agent 看工人的結果；文件示例不通過就把回饋寫進下一輪，並把重試上限設成 3，那是示例參數，不是框架硬上限。Hooks 掛在 Instructor 的事件上：送出前、回來後、API 失敗、輸出不符合 schema。`get_context_token_count()` 把系統提示、歷史和工具的 token 分開算。串流和 async 在 v2 拆開。這些行為尚未實跑。

已讀的編排與記憶指南裡，放行的是另一個 agent。沒有看到內建的等人蓋章。人要介入，得自己在 Python 裡停一站。範例目錄是否另有人機工具，尚未逐一確認。

## 主打賣點

- **它加上的是小零件、型別接口，和你自己寫的接線。** 單一目的、可重用、可預期，是官方想被記住的句子。真正可以帶走的是：輸出表單就是下一棒的輸入表單，而且呼叫點留在你的程式。
- **和 CrewAI 的分工不同。** CrewAI 把角色、任務和 process 放進同一次執行的小隊，工具可以交給 agent 決定。Atomic Agents 把工具留成獨立物件。要模型挑選時，它交出的是哪一份輸入 schema，分派仍是你寫的分支。
- **可預期來自 schema 檢查和你寫死的順序。** 平行、路由、主管複查都是可以照著寫的模式，不是一家一直醒著的公司。
- Forge、Assembler、給程式助理看的 agent skills、MCP connector，是把零件帶進專案，或讓助理少猜 API。開源核心仍是 AtomicAgent、schema、context，以及你寫的組合。

## 使用情境

### 固定管線：查詢、搜尋、整理

- 適合誰：步驟順序已經知道的開發者。
- 在什麼情況使用：先產生搜尋詞，再呼叫搜尋工具，再交給整理 agent。查詢 agent 的輸出 schema 直接用搜尋工具的輸入 schema。
- 帶來的價值：中間沒有多一次「要不要搜尋」的判斷。換搜尋服務只換接口。

### 問題決定用哪一台機器

- 適合誰：下一步真的取決於使用者自由輸入的人。
- 在什麼情況使用：choice agent 的輸出是搜尋或計算機等工具輸入的 Union，程式依型別呼叫。
- 帶來的價值：模型只能交出其中一種合法參數。哪一台被選到，在分支裡看得到。工具很多時要不要改成分層路由，文件有建議，尚未實測。

### 寫完交給另一個 agent 驗收

- 適合誰：想要複查或改寫循環的人。
- 在什麼情況使用：工人交出符合 schema 的結果，主管 agent 批准或寫回饋；或把上一棒輸出手動放進下一棒的歷史。
- 帶來的價值：交接是一份結構。人不在這條迴圈裡，除非你自己加停點。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是一張固定工位，收一件輸入、交一件輸出。工具是機器。Context 是牆上會更新的公告。歷史是抽屜裡的回合。組合是走廊：上一張表單對得上下一張托盤，紙就能傳過去。
- 值得借鑑的 interaction / workflow：順序是文件從這桌走到下桌。平行是幾張桌子同時看同一份文件。路由是接待先分類，再送進專職房間。主管走到工人桌上看表單，通過就收走，不通過就把批註夾回下一輪。Schema 對不上是托盤拒收，hook 讓這次拒收被看見。人要蓋章，就是走廊上多一站。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層，可以是 pixel 辦公室、地圖或樓層。每張桌子是一個 AtomicAgent，名牌寫背景那一句職責。桌面兩個托盤，左進右出，托盤形狀就是 schema。順序管線是一排桌子，紙從右托盤滑進下一張左托盤，形狀不合就停在桌上。工具是桌旁的機器，例如搜尋終端或計算機。固定流程是直接去按那台機器。Choice agent 是前台蓋章，決定紙送哪一台，仍要有人把紙拿過去。Context provider 是每回合開工前更新的公告板：檢索結果、現在幾點、這位使用者。ChatHistory 是桌下抽屜，一回合一疊。共用歷史是房間中央同一個抽屜櫃。各自歷史是私人抽屜。Agent-to-agent 是有人把做好的表單放進另一張桌子的抽屜。主管桌看工人的輸出。平行是同一張地圖上三張桌子同時亮著。Token 計數是公告板和抽屜有多滿的刻度。這是樓層上的工作位置。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走進樓層，看見這一棒誰在位子上。順序流程裡，查詢的人在自己的桌子填搜尋表，填完資料夾送到旁邊的搜尋機器，整理的人等資料夾到了才開始。還沒輪到的桌子是空的，所以看得到誰在場、誰在等。平行時，情緒、主題、摘要三個人同時站在三張桌子前看同一份文件。Router 是門口的接待把工作領到技術、創意或分析的房間，你跟著走就知道落到誰那裡。Supervisor 走到工人桌前看那張輸出表，點頭就帶走，搖頭就把批註留下，工人留在位子上再做一輪。Context 是牆上的白板，事實和決定寫在上面，兩個員工抬頭就看得到，下一輪會重寫。歷史是各桌的檔案櫃。共用歷史是房間中間的同一個櫃子。把訊息交給另一個 agent，是走進他的櫃子放進一疊。工具是房間裡的設備，要有人走過去操作。重點是看見誰在哪一桌、托盤裡是哪一種表單、紙現在在走廊的哪一段。
- 需要重新設計的地方：Atomic Agents 的執行軌跡是 Python 呼叫、schema 和 hook。空間辦公室要另做「現在哪一桌在跑、托盤對不對、公告板更新了什麼」。這些 agent 是被呼叫時才工作的函式。ChatHistory 可以存下來，但沒有一份一直坐在座位上的員工身份。若同一間辦公室還要放 CrewAI 那種同一次執行的角色小隊，或一直開著的 terminal seat，座位種類要分開。Assembler 能否下載現成 Agents 和 Pipelines，README 仍寫 soon，尚未確認。人的放行關卡也尚未在已讀文件裡看到，先不要做成框架自帶的房間。

## 初步看法

- 最有價值的部分：schema 就是交接契約。Context 和回合歷史分開。分派留在看得到的程式裡。
- 最大限制或疑問：它是給開發者呼叫的函式庫。多 agent 模式是文件食譜。延遲、Union 變大之後的手感，都還沒跑過。
- 是否值得進一步研究或親自體驗：值得，尤其是托盤對齊、公告板對抽屜、主管走過去驗收，在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official docs：https://eigenwise.github.io/atomic-agents/
- Repository：https://github.com/Eigenwise/atomic-agents
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright 2024 Kenny Vaneetvelde）
- Version：`main` 的 `pyproject.toml` 與 PyPI 皆為 2.10.3；Python >=3.12。本次未安裝。
- 已讀：README、`UPGRADE_DOC.md`、`docs/guides` 的 tools、orchestration、memory、hooks，以及 `docs/api` 的 agents、context。MCP 預設傳輸對照 `connectors/mcp/mcp_definition_service.py`。
