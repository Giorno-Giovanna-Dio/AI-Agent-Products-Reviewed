# Agent Squad

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-squad-cell-ad04/reviews/agent-squad.md)
>
> Cell ID：[https://github.com/2FastLabs/agent-squad](https://github.com/2FastLabs/agent-squad)
>
> Status：`untried`
>
> Category：多 agent 對話路由框架（Classifier／Supervisor）
>
> Last updated：2026-10-10

## 產品介紹

Agent Squad 是開源框架，把使用者的每一句話交給最合適的專門 agent，並把這段聊天記下來。它服務要做多領域聊天的開發者，呼叫方式是程式裡的 `route_request`，畫面要自己接。Python 與 TypeScript 可以跑在 AWS Lambda、容器或自己的電腦；Swift 把同一套編排放上 iPhone、iPad、Mac，整段在裝置上跑。文件在 [2fastlabs.github.io/agent-squad](https://2fastlabs.github.io/agent-squad/)。本次只讀 README、LICENSE 與文件，沒有安裝。

GitHub about 寫的是「Flexible and powerful framework for managing multiple AI agents and handling complex conversations」。這句和 repo 一致，README 再把它收成兩件事：Classifier 為這一輪選人，Orchestrator 存下這一次往返再回覆。CrewAI 的 Crew 是同一次執行裡、依任務接力的角色小隊。Agent Squad 的預設是同一段聊天的櫃檯：每一句重新分人，被選中的人只延續自己跟這位使用者的紀錄。

這份 repo 就是 AWS Labs 的 Agent Squad，前身叫 Multi-agent Orchestrator。GitHub API 對 `awslabs/agent-squad`、`awslabs/multi-agent-orchestrator`、`transfer-aws/agent-squad` 都回傳同一個 repo（id `832647441`，`full_name` 為 `2FastLabs/agent-squad`）。`fork` 是 false，`mirror_url` 是空的。README 寫專案已從 awslabs 轉到 2FastLabs 維護。現址就是這一個 repo。

## 主要 Features

### 每一輪的 Classifier

使用者的話先到 Classifier。文件寫它同時看這句話、每個 agent 的 description，以及這個 user id、session id 底下所有 agent 的對話。選中之後，那位 agent 只讀自己的歷史。短句如「再多說一點」「好」「12」，會被當成上一棒還在等的回答，分回剛才那位。分不出人時，預設交給 default agent；也可以改成回一句請使用者重講。

Python／TypeScript 內建 Bedrock、Anthropic、OpenAI classifier。README 另推 JevClassifier：用決策模型回傳選中的 agent 和校準過的信心，範圍外可以回 unknown，並說「yes, go ahead」即使中間換過話題，也會回到還在等答案的那位。Swift 用可選的 `LLMClassifier`；不給 classifier 時只會叫名單上的第一位。Python 指南寫：沒有傳 classifier、也沒裝 boto3 時，建立 orchestrator 會失敗。分類準不準尚未實測。

### 專員、小隊與固定接力

一個 agent 要有 name 和 description。description 是給分派員看的專長說明。內建專員包含 Bedrock、Anthropic、OpenAI、Lex、Lambda，以及 Bedrock 上既有的 agent 或 flow。自訂 agent 只要實作處理這一句話的方法。

SupervisorAgent 是小隊：一位 lead（文件把類型限定為 BedrockLLMAgent 或 AnthropicAgent）把隊友當成工具，可以平行詢問，再合成一次回覆。隊友彼此看不到對方。記憶分成三層：使用者和 lead 的對話、lead 和每位隊友的私人對話、lead 手邊的彙整。這位 Supervisor 也可以登錄進 Classifier，先分到哪一隊，再由隊長派工。

ChainAgent 是寫死的順序管線：上一棒的文字輸出變成下一棒的輸入，只有最後一棒可以串流。

### 對話狀態與交接

儲存鍵是 user id、session id、agent id。預設是記憶體，重開就沒了。文件另外提供 DynamoDB、SQL／Turso，以及把過長歷史壓成摘要的包裝。每位 agent 預設會存自己的來回，`save_chat` 可以關掉。歷史有上限，程式指南寫預設每個 agent 保留 100 對。

Classifier 看得到所有抽屜。專員只打開自己那一格。換人的做法是下一輪重新分類；跟進的短句才走回還在等的那位。整份對話夾不會跟著人交到下一桌。

### 工具、接地回答、人怎麼介入

專員可以掛函式工具、retriever（例如 Bedrock Knowledge Base）或 MCP server。GroundedAgent 用兩個模型：搜集者呼叫工具、看原始結果，但不對使用者說話；報告者只根據整理過的工具輸出寫回覆，看不到對話歷史和工具逐字稿。沒有呼叫工具的閒聊就跳過報告者。

框架預設的一輪裡沒有等人批准。電商客服範例自己寫了 `HumanAgent`，放在 ChainAgent 末端：把內容送進 SQS，並立刻回一句已收到。這個類別寫在範例檔裡，不在文件的內建 agent 清單。範例對客戶的那次 SQS 發送是註解掉的。文件說客服畫面可以看到哪些訊息要人處理；人改稿之後才送出的完整路徑，尚未確認。

Swift 另有裝置上的即時語音、OSLog／OTLP tracing、JSON 或 SwiftData 的本地聊天紀錄。Python 側的進度是 log 開關（分類結果、執行時間）和串流回覆。

執行環境就是函式庫所在的行程。工具是程式裡的函式、MCP，或 Lambda 這類外部呼叫。ComprehendFilterAgent 可在交給下一位之前做 PII／毒性過濾。Bedrock inline agent 的文件提到程式解讀，那是 Bedrock 的能力。框架自己的沙箱與權限座位尚未在文件裡看到。

## 主打賣點

- **每一句重新分人，每人有自己的對話抽屜。** Classifier 看所有人的歷史決定這一輪找誰；被選中的人只延續自己的上下文。短回覆能回到還在等答案的那位。GitHub 那句「管理多個 agent 與複雜對話」指的就是這套路由加儲存，而不是一張任務清單。
- **和 CrewAI 的差別在交接。** CrewAI 的 sequential 是任務紙沿桌子傳，hierarchical 是經理依角色派工。Agent Squad 的預設沒有任務單，同一段聊天每句先問分派員。Supervisor 才像隊長，而且是把隊友當工具平行問。ChainAgent 才是上一棒的輸出變成下一棒的輸入。
- Jev 的校準信心、GroundedAgent 的雙模型、Swift 的裝置上執行，是路由和回答方式的延伸。大量 Bedrock／Lex／Lambda 專員是雲端接頭。

## 使用情境

### 一個聊天視窗換很多專員

- 適合誰：要做旅遊、天氣、訂單這類多領域聊天的開發者。
- 在什麼情況使用：使用者在同一段 session 裡換話題，或用很短的話接上一句。
- 帶來的價值：每一輪重新挑人，短回覆仍回到剛才那位；每位專員只帶自己的歷史。

### 一句話要問一整隊

- 適合誰：一個問題要同時問訂單、帳單、技術，再合成一次回答的人。
- 在什麼情況使用：直接呼叫 SupervisorAgent，或先讓 Classifier 分到某一隊，再由 lead 平行詢問隊友。
- 帶來的價值：使用者只看到隊長的一次回覆；隊友的往返留在隊長的私人記憶。

### 敏感回覆先經過人

- 適合誰：客服這類不能直接把模型的話送出去的流程。
- 在什麼情況使用：電商範例把客服 agent 和自訂的 HumanAgent 串成 Chain，後者把內容放進佇列。
- 帶來的價值：文件想表達的是人可以看見並接手。範例程式本身是排隊並立刻回覆已收到；人改稿後才送出的路徑尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：員工名牌是 name 加 description。工作單位是「這一句話該找誰」，session 是一位客人的案子。案子裡每個專員有自己的對話抽屜，分派員看得到全部抽屜。
- 值得借鑑的 interaction / workflow：每一句先到分派櫃台。分派員翻過所有紀錄，走到某一桌；短句「好」「再說明」走回還在等的那桌。Supervisor 是隊長同時問幾位同事，再自己回答客人。Chain 是釘死的傳話順序。人要驗的時候，是管線末端多一張人工桌，字條先進佇列。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。中間是分派櫃台，四周是專員工位，名牌寫 name 和 description。路由是櫃台把這一張字條送到某一桌。小隊是一間小組房：隊長桌在中間，隊友桌分開，便條可以同時送到幾張隊友桌，隊友互不走動，隊長再走回櫃台對客人說話。對話狀態是每張工位一只抽屜，只裝這位客人、這一段 session、這一位專員的往返；櫃台看得到每一只抽屜，員工只翻自己的。Chain 是一排釘死的傳話桌，紙從左傳到右。人工驗證是管線最後一張桌子旁邊的托盤。過長的歷史收成抽屜上的摘要夾。串流回覆出現在被叫到的那一桌。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。分派員在門口。路由是他看過各桌的紀錄，走到該選的員工旁邊把話交給他；若這句是「好」或一個數字，他走回剛才還站在原地等的那個人。小隊是一間能走進去的小組房：隊長在房間裡同時走向幾位同事問話，同事之間不交談，問完隊長回到客人面前說一次完整的話。對話狀態是每人桌上的對話本，分派員可以逐桌翻閱，員工只讀自己那一本，換人時本子留在原桌。Chain 是一條走道，上一個人把紙交給下一個人，只有最後一位對著客人把話慢慢說完。GroundedAgent 是同一張桌子上的兩個人：搜集者去工具架拿資料，報告者只看整理好的紙，再對客人說話。人工座位在房間盡頭，字條先放進托盤。在場的是分派員、被叫到的專員、小隊裡的隊長和隊友，以及管線末端等托盤的人。
- 需要重新設計的地方：軌跡是 log 與串流文字，空間辦公室要另做「這一輪字條送到哪一桌」。若要做 CrewAI 那種整份任務紙交到下一桌，抽屜模型要另外設計。Supervisor 的 lead 被文件限制成特定 agent 類型。介紹頁列出 Agent Overlap Analysis，agents 概覽連到的頁面目前回 404，這個工具是否仍在現行程式裡尚未確認。Swift 與 Python／TypeScript 的 classifier、儲存名稱不完全相同，三套是否每一項都對齊，尚未確認。電商範例的人工改稿是否擋在送出之前，尚未確認。

## 初步看法

- 最有價值的部分：分派員看全局、專員只記自己的對話，短回覆能回到還在等的人。Supervisor 則是同一句話裡的平行問隊。
- 最大限制或疑問：它是給開發者呼叫的框架。分類準不準、換人時私人抽屜會不會丟掉客人以為還在的上下文，要跑過才知道。人工驗證在範例裡只做到排隊。
- 是否值得進一步研究或親自體驗：值得，尤其是路由、小隊和對話抽屜在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://2fastlabs.github.io/agent-squad/
- Repository：https://github.com/2FastLabs/agent-squad
- Documentation：https://2fastlabs.github.io/agent-squad/ （How it works、Classifier overview、Orchestrator overview、Storage overview、Agents overview、Supervisor Agent、Chain Agent、電商客服範例）
- License：repo `main` 的 `LICENSE` 為 Apache License 2.0
- 舊網址（GitHub API 皆回傳同一 repo id `832647441`）：https://github.com/awslabs/agent-squad 、https://github.com/awslabs/multi-agent-orchestrator 、https://github.com/transfer-aws/agent-squad
- README（`main`）：新家在 2FastLabs、Classifier 流程、SupervisorAgent、GroundedAgent、JevClassifier、Swift
- Python 指南：`python/SKILL.md`（orchestrator、儲存鍵、ChainAgent、SupervisorAgent）
