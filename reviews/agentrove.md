# Agentrove

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentrove-cell-c2d2/reviews/agentrove.md)
>
> Cell ID：[https://github.com/Mng-dev-ai/agentrove](https://github.com/Mng-dev-ai/agentrove)
>
> Status：`untried`
>
> Category：自架 AI 程式工作空間（ACP、persona、sandbox）
>
> Last updated：2026-10-09

## 產品介紹

Agentrove 是自架的 AI 程式工作空間。官方 README 的標題就是這個名字，repo 也叫 agentrove。人先開一個 workspace：空資料夾、git clone、本機資料夾，或 GitHub repo。同一個畫面裡有聊天、程式編輯器、終端機、檔案樹、diff 和 git。真正動手的是已經裝好的 coding agent：Antigravity、Claude Code、Codex、Copilot、Cursor、Grok、OpenCode。Agentrove 用 Agent Client Protocol（ACP）在這個 workspace 的 sandbox 裡啟動它們。

它要服務的是想自己把這些 agent 放在同一份程式旁邊的人。可以跑成 Docker 網頁、macOS 的 Tauri 桌面（本機 Python sidecar），或 iOS 薄客戶端（連回你已經架好的實例）。桌面端也能連到自己的遠端實例，介面稱作 cloud。本次讀的是 2026-10-08 的 `main`（`7a5f288`）README 與原始碼，沒有安裝、沒有跑起來。

## 主要 Features

### 一個 workspace，一間 sandbox

每個 workspace 綁一個 sandbox。預設是 Docker 容器（容器名前綴 `agentrove-sandbox-`），也可以改成本機目錄。聊天、終端機、檔案讀寫都走這個 sandbox。ACP session 在裡面啟動對應的 agent 程式。平行的工人若打開 worktree，會在同一個 sandbox 裡拿到自己的 git worktree 和分支，避免同時改同一份檔案。

### Persona 是具名系統提示，和這一輪的工作分開

Persona 存在使用者設定，欄位只有 `name` 和 `content`。`content` 就是系統提示。選了自訂 persona 時，Claude、Codex、Grok、OpenCode 會用它換掉該 agent 的預設提示。程式註明 Cursor 和 Copilot 會忽略這層替換，Antigravity 也不在支援名單。名字是 Default 時，保留 agent 自己的提示，只另外附上聊天提及和後續建議的說明。

這一輪要做的事是另一個欄位：聊天裡的 prompt，或自動化、串流按鈕上的指令。使用者的 custom instructions 再包成 `<user_instructions>`，接在使用者訊息前面，和 persona 分開存。

人物和工作內容因此是兩份資料：同一個 persona 可以套到不同任務，領班派工時用名字指定是誰。人物檔沒有 goal、工具清單或記憶，內容就是那段系統提示。

### 領班聊天派子執行緒

內建 MCP server 把整個實例變成工具（`send_message`、`get_messages`、`list_models`、`list_personas` 等）。任一則聊天裡的 agent 都能呼叫。領班用 `parent_chat_id` 開子執行緒：繼承同一個 workspace，可指定模型、persona、推理力度，以及要不要 worktree。子執行緒到此為止，不能再往下嵌套。領班輪詢訊息直到該回合結束，再對照程式決定要不要把返工送回同一條執行緒。後續回合會沿用上一輪的模型、persona 和推理設定。

這種編排回合走該 agent 的全權限模式（例如 Claude 的 bypassPermissions、Codex 的 full-access），中途不彈權限詢問。

### Channel：人和多個成員坐在同一份程式上

Channel 是同一個 workspace 裡的群組對話。每個成員是一條聊天，自帶顯示名稱、模型、persona、權限模式。頻道還可以指定共用的分支或 worktree。系統會告訴成員：你和使用者以及其他成員在同一工作目錄；改檔前先說，不要改別人正在改的檔；沒有新東西就只回 `PASS`。人在頻道裡發言，成員依此決定要不要接話。

子執行緒是領班派一件工作、工人做完回報。Channel 是同一張桌子上的對話，人直接在場。

### 人看什麼、在哪裡插手

互動中的聊天會把權限詢問畫在畫面上，也可以取消、把下一句排進佇列。Workspace 裡看得到 diff、終端機、檔案，並有 GitHub 的 repo 瀏覽、PR 列表、建立 PR、產生說明與選 reviewer。每一輪助手訊息可以留下 checkpoint（當時的 cwd、base HEAD、跑之前的 diff）。

Stream action 是人在一輪結束後按的按鈕：用指定模型、persona、權限和一段指令，開一條新的子執行緒。Automation 用 cron 在指定 workspace 開一則無人值守的新聊天，側欄用未讀標出還沒看過的更新。

編排出去的工人和排程聊天預設不等你點允許。人的監督落在領班那一則、頻道裡的發言，以及事後看 diff 和 PR。

### 已安裝的 agent skills

設定頁會掃描各 agent 在 workspace 與使用者目錄裡的 `SKILL.md`。那是那些 coding agent 自己的 skills，Agentrove 負責列出來。Skills 和 persona 是兩套東西。

## 主打賣點

- 一個自架工作空間，用 ACP 把多個現成 coding agent 放進同一個 sandbox，再用聊天把它們串起來。
- 人物（可重複選用的系統提示）和這一輪的工作分開，所以派工可以同時指定誰、哪個模型、做什麼、在哪條 worktree。
- CrewAI 也用角色組隊。CrewAI 的角色是 role、goal、backstory，任務帶著期望產出，在同一次 Python 執行裡跑完。Agentrove 的 persona 是套在 Claude Code、Codex 這類外部程式上的系統提示，交接是子執行緒訊息或頻道對話，平行靠 git worktree。
- Paperclip 把外部 agent 編成公司：目標、組織、預算、heartbeat。Agentrove 的單位是程式 workspace 和裡面的聊天，人盯的是程式、diff 和 PR。
- 編輯器、終端機、PR 助手是這個程式工作空間的介面。值得帶走的是 persona、子執行緒、worktree、sandbox，以及哪一種回合會停下來問人。

## 使用情境

### 領班拆任務，工人各改一條分支

- 適合誰：想讓一個較強的模型拆工作，再把實作和審查交給不同 agent 的人。
- 在什麼情況使用：同一個 repo workspace 裡，領班先探查，再開子執行緒，實作者和審查 persona 各自帶 worktree。
- 帶來的價值：誰在做、用哪個人設、在哪條分支，留在聊天樹和 git worktree 上。工人這段不等權限，人事後看領班的結論和 diff。

### 同一份程式開一場多角色討論

- 適合誰：希望人和多個模型同時看同一份程式、彼此接話的人。
- 在什麼情況使用：開一個 channel，為成員指定模型、persona 和權限，大家共用工作目錄。
- 帶來的價值：成員被要求先說再改、沒新內容就 PASS。人在對話裡點名，而不是只看領班的摘要。討論品質尚未實測。

### 做完一輪，用人按的按鈕請固定 persona 審查

- 適合誰：主聊天仍由人看著，只想把審查或後續步驟交給固定角色的人。
- 在什麼情況使用：設定 stream action，指定審查 persona、模型和指令；一輪結束後按鈕開子執行緒。
- 帶來的價值：工作和人物仍是分開的欄位。人決定何時派出這個角色。這顆按鈕自己帶權限模式，和領班的全自動派工可以不同。

## 我們可以學什麼

- 值得借鑑的 product idea：員工名牌是可替換的 persona，任務紙是這一輪的 prompt。Sandbox 是這間房間的牆。子執行緒是領班派出去的工位，worktree 是工位自己的分支抽屜。Channel 是人也坐下來的會議桌。
- 值得借鑑的 interaction / workflow：派工要同時選人設、模型、權限和是否隔離分支。交接有兩種：領班輪詢子執行緒，或頻道裡大家說話或回 PASS。互動聊天會遞權限條；編排與排程預設不遞。Checkpoint 和 diff 讓人回到某一輪看看改了什麼。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層。一個 workspace 是一間房間，牆是 sandbox（Docker 容器或本機目錄）。領班坐中間，子執行緒是四周的工位。名牌寫 persona，桌上的任務紙是 prompt，兩張紙分開。開了 worktree 的工位旁邊有自己的分支抽屜。Channel 是房間裡的會議桌，有人說話，有人牌上寫著 PASS。人走在地板上：互動工位會遞出權限條，diff 貼在牆，cron 是牆上的鐘，時間到就開一張空桌。編排工人的桌子標明這一輪不會問人。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走進某個 workspace 房間，檔案在架上，終端機在桌上。領班站在主桌，把人派到側邊工位。每人頭上的帽子是 persona，手裡的夾板是這一輪的任務。開了 worktree 的人在自己的凹室改另一條分支。Channel 是會議桌，有人開口，有人坐著不插話。你走過去看牆上的 diff、在某張桌子批准權限，或把 PR 送出門。無人值守的工人已經在做，你看見他們在哪個凹室、戴哪頂帽子、改哪條分支。
- 需要重新設計的地方：Agentrove 自己的畫面是聊天加編輯器的程式介面。空間辦公室要另做「誰在哪一桌」。Persona 只有名稱和系統提示；座位上的員工若還要有目標、工具和記憶，要另外加欄位。Cursor、Copilot 不吃這層替換，Antigravity 也不在支援名單，這些座位要標出來。編排工人以全權限跑，空間裡要讓人看出這張桌子不會停下來問。隔離強度、頻道協調和 iOS／遠端連線都尚未實測。

## 初步看法

- 最有價值的部分：persona、任務、模型、worktree 可以在派工時分開指定，而且工人是真實的 coding agent，跑在 workspace 的 sandbox 裡。
- 最大限制或疑問：persona 的內容就是系統提示，人物深度停在這一段文字。Cursor 與 Copilot 會忽略替換。編排回合不經人點允許。這些行為來自原始碼，尚未跑過確認手感。
- 是否值得進一步研究或親自體驗：值得，尤其是子執行緒、worktree 和 channel 在同一間 sandbox 裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Repository：https://github.com/Mng-dev-ai/agentrove
- 讀取版本：`main` @ `7a5f288`（2026-10-08，Merge pull request #964）
- License：Apache-2.0（`LICENSE`）。先前猜測的 MIT 與這份授權不符。
- README：產品名 Agentrove；ACP adapters、Docker／host sandbox、persona、MCP 子執行緒、worktree
- 原始碼：`backend/app/models/types.py` 的 `PersonaDict`；`backend/app/prompts/system_prompt.py`；`backend/app/services/acp/adapters.py`（`PERSONAS_SUPPORTED_AGENTS`，並引用 Agent Client Protocol 的 session modes）；`mcp-server/server.py`；`backend/app/services/channel.py`；`backend/app/models/db_models/workspace.py`、`chat.py`、`automation.py`
- 官方網站：repo homepage 為空。社群 Discord 寫在 README。沒有另找到產品文件站。
- 名稱：README、桌面 bundle id `com.agentrove.app` 與 iOS 產物都寫 Agentrove。部分 MCP 說明字串寫成 AgentRove，這裡採 README 的拼法。
