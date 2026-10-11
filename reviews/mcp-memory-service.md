# MCP Memory Service

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mcp-memory-service-cell-fb52/reviews/mcp-memory-service.md)
>
> Cell ID：[https://github.com/doobidoo/mcp-memory-service](https://github.com/doobidoo/mcp-memory-service)
>
> Status：`untried`
>
> Category：Shared agent memory service
>
> Last updated：2026-10-10

## 產品介紹

MCP Memory Service 是給 AI agent 用的自架記憶服務。它把專案決定、觀察和
錯誤留在一座大家都能打開的檔案櫃裡：下一個 session、另一個工具、另一位
agent，都可以再讀到同一批內容。它是 Python 服務（PyPI 套件
`mcp-memory-service`，本筆記對到的版本是 11.15.0），由 Heinrich Krupp
維護，授權 Apache-2.0。早期開發在 Codeberg，2026-09-05 起以 GitHub 為
主線。

使用者在自己的機器上跑 `memory`。桌面與終端機裡的 agent 多半用 MCP 當門；
LangGraph、CrewAI、AutoGen 這類流程在文件裡改打同一座服務的 HTTP。預設
只聽本機。要給別的機器用，官方要求 API key 或 OAuth。本 Cell 尚未實際
跑過。

## 主要 Features

### 任何員工都對同一座櫃子讀寫

MCP 是員工讀寫這座記憶的門。Claude Desktop、Claude Code、Cursor、
VS Code 等客戶端把服務接成 memory server 之後，就能存入、搜尋、刪除。
多 agent 流程的整合指南則讓 LangGraph、CrewAI、AutoGen 用 HTTP 打同一座
服務，不必自帶 MCP client。兩邊進的是同一份記憶：寫下去的決定，下一位
員工可以依內容、標籤或時間找回來。

Agent 指南仍把 MCP 寫成主要給 Claude Desktop／Code；主 README 列出更多
MCP 客戶端。哪些客戶端真的共用同一份庫，尚未確認。

### 標籤標出是誰的、哪個專案的

每筆記憶有類型（決定、學習、錯誤、觀察等；不認得的類型會收成
observation）和標籤。標籤用前綴分區：`agent:` 是哪位員工，`proj:` 是哪個
專案，另外還有品質、主題、時間、使用者與系統。HTTP 寫入若帶
`X-Agent-ID`，服務會自動加上 `agent:<id>`。搜尋可以要求標籤全部符合，或
符合其中一個，把「這位員工的筆記」和「這個專案的決定」分開拿。

同一段對話若帶 `conversation_id`，服務會略過「內容太像就當成重複」的
檢查，讓這一輪可以連續加頁。文件也把 `crew:`、以及像 `msg:cluster` 這種
哨兵標籤，當成員工之間傳紙條的方式：一人寫入，另一人依標籤來取。標籤
分類器目前列出的合法前綴沒有 `crew:` 和 `msg:`。這兩種慣例寫進去會不會
被擋下，尚未確認。

### 座位抽屜與全辦公室的櫃子是兩層

SQLite-vec 是這一台機器上的抽屜，適合單人，多個本機客戶端也可以在 WAL
模式下共用同一個檔，或改連同一個 HTTP 服務。Cloudflare 是雲端那一份。
官方建議的 Hybrid 是讀的時候先看本地（官方稱約 5ms，本 Cell 未測），背景
再同步到 Cloudflare，或同步到另一台自架的 MCP Memory Service。

v11.15 起，HTTP 還能用 store 分區。寫入必須指定一個分區；讀取才可以一次
看全部分區。分區有多嚴格、和標籤範圍是否重疊，尚未確認。嵌入預設在本地
用 ONNX 計算，官方以此說明記憶不必送去別人家的 API。

### 閉館後有人整理櫃子

服務可以排程整理，官方用睡眠週期作比喻：每天處理近期記憶，每週找關聯，
每月做較長的壓縮與歸檔。整理包含衰退計分、把相近的記憶聯想起來、聚類、
把一疊筆記壓成較短的摘要（預設仍保留原文），以及把太久沒被翻到的東西
歸檔。類型影響能留多久，決定比一般觀察留得更久。重複出現的錯誤可以累加成
mistake note，整理時較不容易被清掉。

整理也會依類型與用詞推測筆記之間的關係：造成、修復、支持、矛盾、跟隨。
v11.15 的說明是：矛盾可以留下這條關係，同時不必把舊記憶藏起來
（`MCP_CONSOLIDATION_AUTO_SUPERSEDE`）。這項行為尚未親自驗證。整理跑完才
會在本機留下報告。有人寫入或刪除時，服務可以用 SSE 通知其他正在聽的客戶端。

## 主打賣點

- 最想被記住的是一座自架服務：所有 agent 用 MCP 或 HTTP 讀寫同一份記憶，
  標籤標出是誰寫的、屬於哪個專案，整理交給背景排程。
- 和 [gbrain](https://github.com/garrytan/gbrain) 的差別在於交付方式。
  gbrain 先給一段有來源的綜合回答。這裡是員工自己走到檔案櫃存取，再用
  標籤歸檔。
- 和 [Hindsight](https://github.com/vectorize-io/hindsight) 的差別在於
  隔離與整理的發起者。Hindsight 用 memory bank 把一顆腦隔開，並把存、找、
  想分成 retain／recall／reflect。這裡的範圍主要是標籤與 store 分區；整理
  由服務依日、週、月自己跑。
- 和 [ai-memory](https://github.com/akitaonrails/ai-memory) 的差別在於
  真實來源。ai-memory 以 git 上的 Markdown wiki 為準，並從 coding CLI 的
  生命週期收下工作軌跡。這裡的真實來源是向量記憶庫。是否每個客戶端都會
  在背景自動寫入，尚未確認。
- 和 [Octop Memory](https://github.com/TencentCloud/octop-memory) 的差別
  在於記憶怎麼留下、怎麼搬走。Octop Memory 把對話當證據，通過晉升才變成
  事實，並用套件換宿主。這裡是直接寫入，再由背景做衰退、壓縮、關聯與
  歸檔；換宿主的方式是大家連到同一座服務。
- 官方用來對比 Mem0、Zep 與自建向量庫的「5ms、零雲端費用、因果圖譜」是
  定位與行銷數字，本 Cell 沒有複測。
- 官網的立體節點動畫和儀表板上的力導向圖，是在看筆記怎麼連。儀表板分頁
  （搜尋、瀏覽、分析、API 文件等）是後台終端。兩者都是檢視記憶的方式。

## 使用情境

### 換一個 coding agent，仍打開同一座櫃子

- 適合誰：同一專案會在 Claude Code、Cursor、VS Code 之間切換的人。
- 在什麼情況使用：新 session 要接著上次的決定，又不把整段聊天貼進下一個
  工具。
- 帶來的價值：每個工具都用 MCP 對同一座服務讀寫。專案決定掛上 `proj:`，
  換座位之後仍找得到。

### 多位員工共用決定，紙上蓋著寫的人

- 適合誰：用 LangGraph、CrewAI、AutoGen，或任何會打 HTTP 的多 agent 流程。
- 在什麼情況使用：一位員工寫下限制或做法，另一位要依專案或作者找回，而
  不是把所有人的筆記混成一次語意搜尋。
- 帶來的價值：`X-Agent-ID` 自動蓋章。需要點對點交接時，文件建議用哨兵
  標籤當紙條。標籤能不能真的當訊息匯流排，尚未確認。

### 白天先讀抽屜，晚上再歸到全辦公室

- 適合誰：希望本機讀得快，同時要多裝置或團隊有一份同步副本的人。
- 在什麼情況使用：日常讀寫走本地，背景同步到雲端或另一台自架服務；太舊、
  太碎的筆記交給排程整理。
- 帶來的價值：座位抽屜與共用櫃子分開。人看到的是誰在歸檔、整理有沒有跑完。

## 我們可以學什麼

- 值得借鑑的 product idea：記憶是員工在一個地方裡使用的服務。門是 MCP，
  流程類員工也可以走 HTTP。櫃子裡用標籤區分誰的、哪個專案的。座位抽屜與
  同步出去的共用櫃是兩層。
- 值得借鑑的 interaction / workflow：開工時員工走到櫃子，依專案與自己的
  印章取出相關筆記，寫下去時蓋上自己的名字。閉館後有人整理：相近的訂在
  一起，太舊的歸檔，互相矛盾的留在桌上。別人寫入或抽走時，櫃子上的燈亮
  一下，在場的人知道記憶剛被改過。整理跑完，櫃台上多一份報告。
- 在 2D workspace 裡會變成什麼：一張俯視的樓層。中間是共用檔案櫃，每個
  座位有自己的抽屜。員工起身打開櫃子時，平面上看得見他站在櫃前。抽屜
  標籤是 `agent:`、`proj:`、品質與時間。Hybrid 同步是抽屜與中央櫃子之間
  的一條搬運路線。store 分區是不同層架：放進去時只能放進一層，讀的時候
  可以望向全部。整理發生在同一張地圖的閉館時段。這張樓層是員工工作的
  地方；產品儀表板留在櫃檯後面的終端，不鋪滿整層樓。
- 在 3D workspace 裡會變成什麼：一間走得進去的辦公室，像
  [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你走進
  檔案室，看見哪位員工正在抽檔，或把一張紙放進去，紙上蓋著 `agent:` 印章。
  座位抽屜是本地那份，檔案室層架是同步後的共用份。夜班員工在檔案室把舊紙
  壓成摘要，用迴紋針把「造成／修復／矛盾」別在一起，太久沒人翻的放進歸檔
  箱。SSE 是檔案室的燈。官網那顆可旋轉的立體知識圖譜留在索引說明裡；走進
  去時看到的是人與員工在檔案室裡，以及誰正在讀、誰正在寫。
- 不值得照搬或需要重新設計的地方：八個儀表板分頁與 D3 圖留在後台。標籤當
  員工之間的紙條很輕，沒有收件確認，容易變成沒人拆的字條。整理會壓縮、
  歸檔，也可能改寫關聯；檔案室要讓人看見「昨晚動過哪些抽屜」，否則背景
  工作會改掉大家以為還有效的決定。檔案室若對整層樓開放，要有鑰匙。v11.15
  已把未驗證、又綁在非本機位址上的 MCP-only 入口擋下來，網路客戶端改走
  有 API key 或 OAuth 的 HTTP。`crew:`、`msg:` 與標籤分類器是否一致，進
  辦公室前要先確認，否則印章會貼錯抽屜。

## 初步看法

- 最有價值的部分：任何員工都用同一套讀寫碰到共用記憶，再用標籤、分區、
  本地／同步與背景整理把檔案室的日常說清楚。這比較接近辦公室裡的共用櫃子。
- 最大限制或疑問：尚未使用，所以延遲、整理品質，以及標籤是否夠當權限，
  都尚未確認。MCP 客戶端清單與 agent 指南的範圍不一致。真實來源是服務裡
  的記憶，整理又會壓縮與歸檔，出錯時不容易像改一份 Markdown 那樣手改回來。
- 是否值得進一步研究或親自體驗：值得。若之後要體驗，放在
  `/workspace-labs/mcp-memory-service`，不要 clone 進本 repo。優先看兩位
  員工是否讀到彼此蓋過章的同一筆決定、本地抽屜與同步櫃是否一致，以及整理
  之後舊決定還在不在。

## 後續補充（選填）

Not tried yet。研究時只讀官方 README、架構、agent 整合、整理指南與
v11.15.0 發行說明，沒有安裝，也沒有把 upstream 放進本 repo。

## Sources

- Repository：[https://github.com/doobidoo/mcp-memory-service](https://github.com/doobidoo/mcp-memory-service)
- Package：[mcp-memory-service 11.15.0 on PyPI](https://pypi.org/project/mcp-memory-service/)（發行於 2026-10-03）
- Official website：[https://mcpmemory.services](https://mcpmemory.services)
- Documentation：[架構](https://github.com/doobidoo/mcp-memory-service/blob/main/docs/architecture.md)、[agent 整合](https://github.com/doobidoo/mcp-memory-service/blob/main/docs/agents/README.md)、[整理指南](https://github.com/doobidoo/mcp-memory-service/blob/main/docs/guides/memory-consolidation-guide.md)、[多客戶端](https://github.com/doobidoo/mcp-memory-service/blob/main/docs/integration/multi-client.md)、[記憶類型](https://github.com/doobidoo/mcp-memory-service/blob/main/docs/memory-ontology.md)
- Earlier archive：[Codeberg](https://codeberg.org/doobidoo/mcp-memory-service)（2026-09-05 起標為 archive，開發主線在 GitHub）
- License：Apache-2.0
- 相關 Cell：[gbrain](https://github.com/garrytan/gbrain)、[Hindsight](https://github.com/vectorize-io/hindsight)、[ai-memory](https://github.com/akitaonrails/ai-memory)、[Octop Memory](https://github.com/TencentCloud/octop-memory)
