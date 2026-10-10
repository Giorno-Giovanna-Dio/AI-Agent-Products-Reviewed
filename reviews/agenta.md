# Agenta

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agenta-cell-ad04/reviews/agenta.md)
>
> Cell ID：[https://github.com/Agenta-AI/agenta](https://github.com/Agenta-AI/agenta)
>
> Status：`untried`
>
> Category：團隊 agent workspace（聊天組裝、權限、自動化）
>
> Last updated：2026-10-10

## 產品介紹

Agenta 是開源的團隊工作空間，用來做出會做固定工作的 agent，並讓它們在沒有人盯著時自己開工。使用者用聊天描述工作、接上工具，再用回饋改設定；也可以直接改指示、技能、權限和模型。它服務的是要共用同一批同事型 agent 的團隊（文件舉的是行銷、營運這類重複工作），不是坐在終端機裡寫程式的人。官方網站是 [agenta.ai](https://agenta.ai)。可以上 Agenta Cloud，或用 Docker Compose 自架。本次只讀 repo 的 README、LICENSE 與現行文件，沒有安裝。

README 把它和三種東西分開：它不是寫程式的 ADE（Claude Code、OpenCode 那一類），不是單人助理，也不是畫流程的 n8n。執行層用的是現成 harness：Claude Code、Pi、Codex。Agenta 加上的是共用檔案、團隊、觸發、版本和追蹤。`ee/` 目錄是企業授權，其餘是 MIT。使用者網址 `agenta-ai/agenta` 會導到 `Agenta-AI/agenta`，Cell ID 用後者。

## 主要 Features

### 一個 agent 是一份固定工作

文件說，值得做成 agent 的是會重複的工作，不是單次提問。一個 agent 把指示、技能、工具、檔案、權限、harness 和模型收成同一份設定。可以在 playground 用對話讓它改自己的設定，也可以自己點開來改。兩條路寫進同一份設定。改動會留下版本，版本裡記著當時的指示、技能、工具、權限、harness 和模型。

### 指示、技能、檔案分開載入

指示每次開場都整份送進模型。技能是一個資料夾，中心是 `SKILL.md`；模型一開始只看到名稱和描述，任務對上才載入正文，資料夾裡還可以放範本和腳本。檔案在打開之前不佔上下文。每個 agent 有兩層檔案：session 檔案只屬於這一次對話，agent 檔案跨 session 共用。子資料夾可以放自己的 `AGENTS.md`，人在別的資料夾工作就不會載入那些規則。學到的東西要寫進檔案，再在指示裡指過去。

### 權限先於動作，沙箱是另一層

每個工具呼叫在執行前會得到允許、先問人、或拒絕。工具可以自己覆寫，否則跟 agent 的預設政策。預設有四種：只放行讀取、全部放行、全部先問、全部拒絕。操作指南寫新 agent 從「全部放行」開始；概念頁寫新 agent 從「讀取自己跑、寫入先問」開始。兩頁不一致，實際預設尚未確認。需要批准時，這一次 run 暫停，對話裡出現批准卡，人看參數後核准或拒絕。沒人在場的自動化若停在批准，run 會等著，不會自己做完。

沙箱是另一層。自架時，API 不跑 agent，而是把這一次交給 runner。Harness 是 Claude Code、Pi 或 Codex；沙箱是 local（預設，跑在 runner 容器裡）或 Daytona（文件說與 runner 和其他 run 隔離）。網路可以全開、全關或用 CIDR 允許清單。參考頁寫檔案系統權限目前只是宣告、尚未強制執行；操作指南仍提供唯讀等選項。兩處是否同一套行為，尚未確認。Daytona 預設把模型金鑰和 MCP 金鑰換成佔位字串，真正的值只在打到指定主機時換上；Bedrock 和 Vertex 這類在沙箱內簽名的憑證不在此列。local 沙箱跑在自己的 runner 裡，這套隱藏不適用。README 說模型憑證不會被 agent 看到，和 Daytona 這條路徑接近，但不是所有路徑。

### 聊天和自動化是同一個 agent

自動化不是另一套流程圖。排程或連線 app 的事件會送出第一句訊息，agent 的指示、技能、工具、檔案照舊。事件本身的資料不會自動交進去，訊息要寫清楚去哪裡找變化、結果放到哪裡。沒有人回答，所以不能留「你是指哪一檔活動」這種空缺。每次 run 留下完整對話和錯誤。README 還說可以接續某次 run 的 session 來改；概念頁只寫到 run history，畫面上能否接續，尚未確認。

### 委派是呼叫另一份 workflow

README 說 agent 可以叫其他 agent、把工作交出去。參考頁的做法是 `reference` 工具：父 agent 像呼叫工具一樣呼叫另一個 workflow，伺服器端跑完把結果交回。可以跟某個 variant 的最新修訂，或釘死版本，或跟著某個環境（例如 production）上已部署的修訂。子 workflow 有自己的執行上下文，trace 用連結對回父層。沒有獨立的「同時最多幾個子 agent」設定。這比較像把文件送到另一張桌子再拿回來，不是一群人在同一間房裡一起討論。

架構頁還有評估佇列。v1.0 文件仍在講測試集、LLM-as-a-judge、OpenTelemetry 追蹤，以及把 prompt 版本連到 span。現行概念頁把重點放在聊天組出的同事。這兩層是不是同一個畫面，尚未確認。

## 主打賣點

- **它加上的是團隊工作空間，不是另一個會自己規劃的迴圈。** 迴圈在 Claude Code、Pi、Codex 裡。Agenta 讓人記住的是：用聊天把一份工作收成可版本化的 agent，讀和寫的權限分開，該問人時暫停，同一個人可以在聊天裡做，也可以被排程叫起來。
- **和 CrewAI、Paperclip、OpenCode 不同。** CrewAI 是同一次 Python 執行裡的角色小隊。Paperclip 是組織控制平面，把外部 coding agent 雇成員工。OpenCode 是寫程式的 ADE。Agenta 的座位是「一份工作的設定」，執行時才把某個 harness 放進沙箱。
- **技能資料夾、追蹤、用測試集評分，是既有做法的包裝。** `SKILL.md` 跟 agent 生態系共用。OpenTelemetry 追蹤、variant／環境、評估器，是這套產品較早的 LLM 應用層，文件還在。聊天組裝同事、批准卡、以及「自動化只是換了第一句話的同一位同事」，才是現在 README 要人記住的差異。
- 自架可用已付費的 Claude 或 ChatGPT 訂閱，是文件上的部署選項。這次沒有跑過。

## 使用情境

### 團隊用聊天做出一位行銷同事

- 適合誰：要共用同一位 agent、但不想每人維護一份 prompt 的團隊。
- 在什麼情況使用：工作會重複，例如寫活動簡報、查數據、起草要發出的訊息。
- 帶來的價值：指示、技能和已核准範例留在這位 agent 上；發出前可以停下來等人看參數。

### 把每週報告從對話改成自動開工

- 適合誰：已經在聊天裡把一件事做穩的人。
- 在什麼情況使用：時間到了，或某個 app 有新事件，要同一位 agent 自己做完並把結果放到頻道或檔案。
- 帶來的價值：不用重畫流程。前提是第一句話寫齊去哪裡找資料、結果放哪裡；若中途要人批准，清晨那次 run 會停住。

### 請另一位同事看過再交回

- 適合誰：希望檢查步驟跟主工作分開、而且要釘住某一版檢查者的人。
- 在什麼情況使用：父 agent 用 reference 呼叫另一份 reviewer workflow，可以釘修訂或跟著 production。
- 帶來的價值：交接是一次有權限的工具呼叫，結果回到原來的對話；子層的金鑰留在伺服器端。實際協作品質尚未實測。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是一份會重複的工作（指示、技能、工具、檔案、權限），不是一次對話。Harness 和模型是桌子底下的引擎，換引擎不換這個人。自動化是同一位員工接到一張寫好的第一張字條。
- 值得借鑑的 interaction / workflow：聊天是人和員工一起改設定；批准卡是動作停住、等人看參數；session 檔案是今天的桌面，agent 檔案是下次還在的櫃子；委派是把一份稿送到另一張桌子再拿回來；run history 留下沒人看時它做了什麼。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。每位 agent 是固定工位，名牌寫這份工作。技能是架上的手冊，對上任務才抽下來。今天的 session 是桌面上的紙，agent 檔案是工位旁的共用櫃。人在工位旁邊用對話改指示。工具要批准時，桌上出現一張待蓋章的卡片，這輪工作停住。自動化是牆上的時鐘或門鈴，把同一張字條放到空著的工位，結果必須放進指定的收件盤，因為辦公室裡可能沒有人。reference 是把文件送到另一張工位再送回。版本是這個工位抽屜上的標籤，可以對照上一版指示和權限。追蹤是工位旁的工作紀錄，不是另開一面儀表板。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你看見誰在哪一張桌子。同一個 agent 的多個 session 是這個人桌上分開的幾件工作，不會混成一條對話。批准是員工轉過身，把要執行的動作遞給你，你點頭才繼續；沒人在的清晨，員工會停在那裡等。委派是走到另一張桌子把稿放下、等結果拿回來，不是兩個人在房間中間一起討論。Daytona 沙箱是和這棟樓分開的上鎖房間；local 是 runner 這棟樓裡的位子，文件沒有說每個 session 都有自己的房間。檔案櫃跨天還在，但沒有設定持久儲存時，工作目錄每次都是空的。重點是看見誰在場、誰停下來等人、結果放到了哪。
- 需要重新設計的地方：Agenta 的畫面是網頁 workspace 和批准卡，空間辦公室要另做「誰在哪一桌、卡在哪一步」。委派在文件裡是工具呼叫返回結果，若畫成一群同事同時在場，要另外設計你看不看得到那一趟。local 沙箱共用 runner 容器，不能畫成每人一間隔離房。檔案系統權限是否真的強制、新 agent 的預設政策，文件互相矛盾，先不要做死。README 還提到瀏覽器、WhatsApp、改正一次就記得、以及 agent 做出小程式；概念頁把「記得」寫成改設定和寫檔案。獨立的記憶系統、瀏覽器、WhatsApp 是否和 Slack／Telegram 同一條通道，都尚未確認。v1.0 的 prompt 遊樂場和現在的同事 workspace 是否同一層，也尚未確認，不要合成一間辦公室。

## 初步看法

- 最有價值的部分：同一位同事可以陪你聊天，也可以被字條叫起來；權限把「讀」和「發出」分開，人在場才放行。
- 最大限制或疑問：它是包住現成 harness 的工作空間。沙箱隔離、預設權限、以及舊的評估層和現在的聊天層是否同一產品，文件沒有對齊。
- 是否值得進一步研究或親自體驗：值得，尤其是批准暫停、無人值守的自動化、以及委派回來的那一刻在 2D／3D 辦公室怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://agenta.ai
- Repository：https://github.com/Agenta-AI/agenta （使用者網址 https://github.com/agenta-ai/agenta 導向此處）
- Documentation：https://agenta.ai/docs/
- License：repo `main` 的 `LICENSE` 寫明 `ee/` 以外為 MIT；`ee/LICENSE` 為 Agenta Enterprise License。GitHub API 的 license 欄位是 `NOASSERTION`。README 寫 MIT。
- README（`main`）：團隊 workspace、Slack／Telegram／WhatsApp、自動化、沙箱、權限、Claude Code／Pi／Codex
- 已讀概念頁：What is Agenta、Agents、Permissions、Skills、Files and knowledge、Harnesses and models、Automations、Control what an agent can do
- 已讀參考與自架頁：Subagents and delegation、How agents run、Sandboxes and security、Self-host quick start、System architecture（Slack／Telegram worker、評估佇列）
- v1.0 文件仍在線：Observability、Configure Evaluators。是否與現行 workspace 同一畫面，尚未確認。
