# Hindsight

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hindsight-cell-7aa3/reviews/hindsight.md)
> - [Pages 閱讀器](https://giorno-giovanna-dio.github.io/AI-Agent-Products-Reviewed/#/reviews/hindsight.md)（`main` 部署後）
>
> Cell ID：[https://github.com/vectorize-io/hindsight](https://github.com/vectorize-io/hindsight)
>
> Status：`untried`
>
> Category：Agent memory / learning layer
>
> Last updated：2026-10-06

## 產品介紹

Hindsight 是 Vectorize.io 開源的 agent 記憶系統，標榜「會學習的記憶」，而不只是
把對話歷史存起來再搜尋。它把新資訊寫進隔離的 **memory bank**，在背景整理成
事實、經驗、觀察與心智模型，再透過 **recall** 取回、**reflect** 做較深的推論。

開發者可以自架 Docker／pip／Helm，用 Python、Node、Go 或 MCP 接上既有 agent；
也可以用 LLM wrapper 在每次呼叫前自動 recall、呼叫後 retain。另提供針對 coding
agent 的套件，從 git 與過往 session 建立 per-repo bank，並有託管版 Hindsight Cloud。

## 主要 Features

### Retain / Recall / Reflect 三動作

**Retain** 用 LLM 從文字抽出實體、時間、關係並正規化後入庫；**Recall** 並行
做語意、關鍵字、圖譜與時間檢索再融合排序；**Reflect** 在既有記憶上整理、連結，
回答需要「想清楚」而不只是 lookup 的問題。這把「存、找、想」拆成清楚 workflow，
方便 orchestrator 決定何時寫入、何時只讀、何時交給深度推理。

### 仿生記憶分層（world / experiences / observations / mental models）

世界事實與 agent 自身經驗分路徑保存；相關 retain 會在背景 consolidate 成帶
證據的 **observations**（可強化或削弱，而非靜默覆蓋）。**Mental models** 與
**knowledge pages** 像預先寫好的常駐答案或 wiki 頁，session 開始時可直接讀
 settled 知識，減少每次重挖。

### Memory bank 與 handoff 邊界

每個 bank 是嚴格隔離的「一顆腦」（使用者、專案或 agent）。bank 可帶 disposition
與模板，並用 metadata 做 per-user 等存取限制。多 agent 或換 session 時，handoff
可以變成「同一 bank_id 延續」或「新 agent 讀同一專案 bank」，而不是整段 chat
貼進 prompt。

### 低摩擦接入 orchestration 生態

除 REST／SDK 外，有 LiteLLM wrapper、60+ 框架與 coding agent 整合、內建 MCP
（retain／recall／reflect 當 tools），以及可選的 Memory Defense（PII／secret
掃描）。部署路徑從 embedded Python、本機 Docker 到 PostgreSQL／企業儲存與
Prometheus 監控，偏「記憶基礎設施」而非單一 IDE 外掛。

## 主打賣點

- 核心差異是 **learning**：官方強調 observations 精煉、reflect 建連、mental models
  隨 bank 更新，而不只做向量 RAG 或靜態知識圖。
- **Recall 多路徑 + rerank** 與 LongMemEval 等 benchmark 敘事，定位在長期記憶
  準確度；實際分數與延遲尚未在本 Cell 驗證。
- **Knowledge pages + coding-agents 套件** 把「專案慣例、架構、進行中工作」變成
  agent 開工前可注入的 handoff 包，接近「員工上班先看內部 wiki」。
- 與 [gbrain](https://github.com/garrytan/gbrain) 同屬記憶層，但 Hindsight 更偏
  可部署的 memory server、商業整合與 reflect／observation 管線；gbrain 較輕、
  動詞導向。兩者概念可對照，不必視為同一產品。

## 使用情境

### 多 session coding agent 的專案 handoff

- 適合誰：用 Claude Code、Cursor、Codex 等 CLI，同一 repo 長期開發的團隊。
- 在什麼情況使用：新 session 或換 agent 時，需要架構、慣例與未完工脈絡。
- 帶來的價值：per-repo bank 與 knowledge pages 當共用 handoff，減少重講背景。

### 需要「記住使用者」的對話或任務 agent

- 適合誰：做個人化助理、銷售或支援 bot 的產品團隊。
- 在什麼情況使用：要跨對話保留偏好、互動結果，並用 metadata 限制可見範圍。
- 帶來的價值：retain  enriched metadata + bank 隔離，比整包 chat log 更易治理。

### 編排層統一管理記憶的 platform 團隊

- 適合誰：已有多 agent framework，想抽離記憶成獨立服務的人。
- 在什麼情況使用：不同 worker 共用同一專案 bank，或要 webhook／監控觀察 retain
  與 consolidation 生命週期。
- 帶來的價值：MCP 與多語言 client 讓 orchestrator 用同一套 retain／recall／reflect
  契約，而不是每個 agent 自做 RAG。

## 我們可以學什麼

- **Orchestration workspace 應把 memory 當基礎設施**：任務、branch、sandbox 是
  「現在在做什麼」；bank + observations + knowledge pages 是「這間辦公室已經
  知道什麼」。handoff 文件應可對應到 bank 內可 recall 的 artifact，而不是只
  存在 Slack 或 PR 描述裡。
- **三動作對應 human oversight**：人委派新任務前可先 reflect「風險與缺口」；
  agent 完成後 retain 決定與結果；下一個 agent recall 再開工——這比單一「搜尋
  記憶」按鈕更貼合審核與接力。
- **2D workspace**：平面地圖上每個任務／員工綁一個 bank 或 bank 切片；側欄顯示
  最新 observations、mental model 摘要與 recall 來源卡；handoff 線徑標示「從
  A 的 retain 到 B 的 recall」。
- **3D workspace**（可走進的 office，非 3D 物件）：記憶室或檔案櫃區域代表 bank；
  員工進房間開工前顯示 knowledge page 一頁摘要；reflect 進行中可在該區有
  「深度思考」狀態，讓人看見不是卡在工具而是整理記憶。
- **不宜照搬**：完整 Docker + LLM 管線對輕量 workspace 可能過重；benchmark
  敘事與 Cloud 計費需另評估。我們應先吸收 bank 隔離、observation 精煉、
  knowledge page 開工注入，再決定自架還是抽象成較薄的 memory API。

## 初步看法

- 最有價值的部分：把記憶從「RAG 側車」提升為有 retain／recall／reflect 生命週期、
  可接 MCP 與 coding handoff 的共用層，直接對應 orchestration 的 context 流動。
- 最大限制或疑問：整合與部署選項很多，初次選型成本高；reflect 與 consolidation
  的 LLM 成本與延遲尚未確認。與 gbrain 等方案的取捨需 hands-on 比較 recall
  品質與治理模型。
- 是否值得進一步研究或親自體驗：值得作為「會學習的 agent memory」代表 Cell 研究；
  若驗證，優先測 per-repo coding 整合與跨 session handoff，而非一次上 production
  全栈。

## 後續補充（選填）

Not tried yet。若實測，建議在 `/workspace-labs/hindsight` 自架 Docker，驗證
同一 bank 跨兩個 mock agent session 的 retain→recall，以及 knowledge page 是否
能減少重複背景說明。

## Sources

- [Hindsight repository](https://github.com/vectorize-io/hindsight)
- [Documentation](https://hindsight.vectorize.io)
- [Integrations](https://hindsight.vectorize.io/integrations)
- [Paper (arXiv)](https://arxiv.org/abs/2512.12818)
