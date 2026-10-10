# Youtu-Agent

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-youtu-agent-cell-ad04/reviews/youtu-agent.md)
>
> Cell ID：[https://github.com/TencentCloudADP/youtu-agent](https://github.com/TencentCloudADP/youtu-agent)
>
> Status：`untried`
>
> Category：Python agent framework（YAML 組態、環境、評測）
>
> Last updated：2026-10-10

## 產品介紹

Youtu-Agent（程式裡簡稱 utu）是騰訊雲 Youtu 實驗室的開源 Python 框架，用來組裝、執行、評測自主 agent，並讓同一份設定用經驗繼續變好。GitHub 上的一句話是「A simple yet powerful agent framework that delivers with open-source models」。它服務要寫 agent、做實驗的工程師與研究者。使用者用 Hydra YAML 寫下 `AgentConfig`，再用 CLI 或本機 WebUI 對話；要打分時走評測腳本。文件站在 [tencentcloudadp.github.io/youtu-agent](https://tencentcloudadp.github.io/youtu-agent/)。本次只讀 README、LICENSE、文件與主要程式結構，沒有安裝。

先前的短述「lightweight orchestration」只蓋到其中一層。README 的設計原則寫了 Minimal design，基準介紹也說它用開源模型與 lightweight tools。`get_agent` 實際依 `type` 切四種執行：`simple`、`orchestra`、`orchestrator`、`workforce`。旁邊還有工具與設定的自動產生、評測管線，以及不改模型權重的 Agent Practice。端到端 Agent RL 寫在 `rl/agl` 分支。概念文件主要只展開 simple 與 orchestra，另外兩種多 agent 編排要看 YAML 與程式。

## 主要 Features

### 四種上班方式

`SimpleAgent` 是一個人的 ReAct 迴圈：想、呼叫工具、看結果，直到自己交出答案。`OrchestraAgent` 先由 Planner 把目標拆成步驟並指定工人，工人必須是 SimpleAgent，依序做完，再由 Reporter 收成一份答案。`OrchestratorAgent` 用 ChainPlanner，工人做每一步時看得到題目、計畫和已走過的軌跡，預設還會加一個 ChitchatAgent 處理閒聊。`WorkforceAgent` 會改班表：Planner 出計畫，Assigner 派工，Executor 執行，Planner 再檢查並選擇停止或改計畫，最後 Answerer 抽出答案。概念頁只把前兩種寫成範式。Workforce 的串流在程式裡仍是待辦，現場進度怎麼推到畫面，尚未確認。

### 環境、工具、脈絡拆開

`AgentConfig` 把模型、人設（name、instructions）、環境、工具組、ContextManager 拆開，用 YAML 組合。環境負責世界的狀態與生命週期。文件建議正式工作走雲端沙箱：`E2BEnv` 跑程式與檔案，`BrowserE2BEnv` 跑瀏覽器，並寫他們實務上用騰訊雲 Agent Sandbox。本機開發用 `ShellLocalEnv`（每次一個獨立工作目錄，提示詞要求 bash 只在該目錄執行）或 Docker 瀏覽器。`BasicEnv` 是空的。工具組分 builtin、mcp、customized，涵蓋搜尋、文件、Python、持久 bash、圖片、音訊等。產生出來的工具自帶虛擬環境，再用 MCP 接回 agent。

程式裡的 ContextManager 只有兩種。`dummy` 在達到 `max_turns` 時塞進一句話，要求不要再用工具、交出答案。`env` 把環境狀態注入這一輪輸入。論文寫 Context Manager 會剪掉過期的頁面 HTML；現行 `utu/context` 沒有這種壓縮。那句是否落在別的模組，尚未確認。

Skills（README 新聞寫 2026-01 起）是工作說明，不是長期記憶。它要求 `shell_local`，且 ContextManager 名為 `env`：把 `.agent/skills` 拷進工作區，agent 再用 openskills 按需讀 `SKILL.md`。

### 開工前先產生設定

README 把自動產生分成 Workflow（標準任務的固定管線）和 Meta-Agent（複雜需求）。論文把 Workflow 寫成四段：釐清意圖、找或合成工具、寫 prompt、組出 YAML。Meta-Agent 是一位架構師，可以搜現成工具、造新工具、問使用者、寫出設定。repo 裡看得到的入口是 `scripts/gen_simple_agent.py`（先問需求，再跑 `SimpleAgentGenerator`）和 `scripts/gen_tool.py`（造工具、建虛擬環境、包成 MCP）。Workflow 是否另有獨立腳本，尚未確認。人的問答發生在產生設定的時候。這次讀到的執行迴圈沒有在工具呼叫前等人放行；若藏在 hook，尚未確認。

### 評測、軌跡、經驗

評測是固定四段：preprocess、rollout、judge、stat。樣本進資料庫（預設 SQLite），用 `exp_id` 分實驗，stage 有 `init`、`rollout`、`judged`，中斷後可以續跑。判斷可以是規則比對，或另一個 LLM 當裁判。README 另有評測分析畫面。執行追蹤方面，快速入門寫 Phoenix。README 把 `DBTracingProcessor` 標成 will be released soon，主線是否已能用，尚未確認。

Agent Practice 用 Training-Free GRPO：同一題多次 rollout，對照成敗抽出文字經驗，寫進加強版 YAML，之後注入脈絡，不更新模型權重。數學與網頁搜尋有現成驗證函式。Agent RL 與 Agent-Lightning 的整合指向 `rl/agl` 分支，該分支的完成度尚未確認。

WebUI 是本機 Tornado WebSocket 加上 React 對話頁（預設 `127.0.0.1:8848`），用來顯示對話事件。這是聊天與實驗畫面。新聞裡的 Youtu-Tip 是另一個 macOS 產品，不在這個 repo 的執行路徑裡。

## 主打賣點

- 它要被記住的是：用 YAML 把環境、工具、agent 拆開，能自動產出這份設定，再用評測和經驗練習讓同一份設定變好。開源模型能跑，是他們反覆講的部署條件。
- 「輕量編排」對上的是 Minimal design、lightweight tools，以及三種多 agent 迴圈。自動產生、評測、Practice 是另外三條產品線。CrewAI 的核心是 role、task、process。Youtu-Agent 的工人是帶工具的 SimpleAgent：orchestra 一次計畫、依序做完再報告；workforce 是派工、檢查、改計畫。Octop 是多使用者、多通道的自架助理；Youtu-Agent 是開發者用 YAML 組出來、在同一次 Python 執行裡跑的 agent。
- WebUI、內建工具、Phoenix 追蹤，是這套執行的觀察包裝。編排、產生、評測、經驗練習才是定位。

## 使用情境

### 用一份 YAML 換題目

- 適合誰：要固定搜尋或檔案處理流程的開發者。
- 在什麼情況使用：選現成 simple 或 orchestra 設定，改 instructions 與工具，用 CLI 或 WebUI 跑。
- 帶來的價值：人設、工具、環境留在設定檔，換題不必重寫迴圈。

### 先問清楚再上工

- 適合誰：還不知道該掛哪些工具的人。
- 在什麼情況使用：描述需求，讓產生器問清楚、寫出 YAML，必要時合成工具再執行。
- 帶來的價值：人的判斷集中在開工前的問答；做出來的設定可以再進評測。

### 用同一批題目比較改動

- 適合誰：要做對照實驗或經驗練習的研究者。
- 在什麼情況使用：同一條評測管線跑 rollout 與裁判，或跑 Training-Free GRPO 得到加強版設定再評一次。
- 帶來的價值：進度留在樣本 stage 與經驗紀錄，中斷可續。比較的是軌跡與分數。

## 我們可以學什麼

- 值得借鑑的 product idea：員工不只有角色卡。同一間公司可以有四種上班方式：一個人自己做、一次排好的流水線、帶著軌跡往下傳的鏈、會改班表的班組。環境是房間的規則（本機抽屜或雲端沙箱），工具是抽屜裡的器具，經驗是練習後釘在牆上的備忘。
- 值得借鑑的 interaction / workflow：人先在產生階段被問清楚，再把 YAML 交出去跑。Orchestra 是計畫一次貼上、依序傳結果。Workforce 是每做完一項就檢查，決定停或改計畫。評測把每題分成尚未跑、已跑、已裁判。人要比較時看的是同一 `exp_id` 下的軌跡與分數。執行中的人工放行，這次讀到的迴圈沒有寫成一等關卡。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。SimpleAgent 是單獨一張桌子，工具掛在桌側，環境是這張桌子所屬的地板（本機工作區，或通往雲端沙箱的門）。Orchestra 是一排事先排好的工位：計畫釘在入口，工人依序接到上一份結果，Reporter 在最後一桌收成報告。Orchestrator 的鏈更長，每張桌子看得到整條已走過的軌跡，閒聊桌在旁邊。Workforce 是中間有派工桌與檢查桌的樓層，做完一項，計畫紙會被改寫。評測是樓層旁的計分牆，每張題目卡標著 init、rollout、judged。經驗練習後多出來的備忘貼在工位的提示板上。人在開工前的問答桌澄清需求。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。SimpleAgent 一個人在自己的房間裡來回拿工具。Orchestra 裡，Planner 先把步驟寫在白板，工人站在指定位子，做完把資料夾放到下一張桌子，全部完成後 Reporter 在出口把答案交給你；走進去看得到誰在場、誰還在等上一棒。Workforce 更像值班室：Assigner 把下一張工單交給某人，Planner 走過去看結果，決定把白板擦掉重寫或宣布收工，Answerer 站在門口唸出最終答案。雲端沙箱是另一棟上鎖的樓，本機 Shell 是這層樓裡隔開的工位。評測時，許多相同的工位同時跑同一批題，分數回到走廊的看板。重點是看見誰在哪裡做事，以及計畫有沒有被改過。
- 需要重新設計的地方：現有畫面是 CLI、聊天 WebUI 和評測分析，空間辦公室要另做「誰在哪一桌、計畫改了沒有」。概念文件只講兩種 type，程式卻有四種；只畫 orchestra 的流水線，會漏掉 workforce 的改計畫。論文裡的脈絡壓縮和現行 ContextManager 不一致，先不要做成會自動丟掉舊頁面的記憶抽屜。執行中的人在場放行要另外設計。Agent RL 在別的分支，Youtu-Tip 是另一個產品，都留在這間辦公室外面。本機 shell 的隔離是提示詞加上獨立目錄，文件建議正式工作改走 E2B；空間上要把「提示約束的工位」和「真的換一棟沙箱樓」分開畫。

## 初步看法

- 最有價值的部分：環境、工具、agent 拆開，再加上會改計畫的 workforce，以及評測和文字經驗這兩條讓設定變好的路。
- 最大限制或疑問：它是給開發者呼叫的框架。四種 type 的文件完整度不一樣。執行中如何暫停、並排比較兩條軌跡、或批准一次工具呼叫，尚未在產品介面上看到。README 的 WebWalkerQA、GAIA 分數是官方宣稱，這次沒有重跑。
- 是否值得進一步研究或親自體驗：值得，尤其是 workforce 的檢查與改計畫，以及評測 stage 在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://tencentcloudadp.github.io/youtu-agent/
- Repository：https://github.com/TencentCloudADP/youtu-agent （舊網址 https://github.com/Tencent/Youtu-agent 指向同一 repo）
- Documentation：https://tencentcloudadp.github.io/youtu-agent/ （Agents、Environments、Tools、Evaluation、Configuration、Automatic Generation、Agent Practice、Skills、Frontend）
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright (C) 2025 Tencent）。GitHub API 的 SPDX 顯示 NOASSERTION，以 LICENSE 內文為準。
- README（`main`）：定位、Minimal design、自動產生、Practice、評測、WebUI
- 程式（`main`）：`utu/agents/__init__.py` 的四種 type；`utu/context` 的 dummy 與 env；orchestra、orchestrator、workforce 的執行迴圈
- Paper：https://arxiv.org/abs/2512.24615 （Workflow、Meta-Agent 與 Practice 的設計說明；與程式不一致處已在正文標出）
