# ai-memory

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ai-memory-cell-7aa3/reviews/ai-memory.md)
> - [Pages 閱讀器](https://giorno-giovanna-dio.github.io/AI-Agent-Products-Reviewed/#/reviews/ai-memory.md)（`main` 部署後）
>
> Cell ID：[https://github.com/akitaonrails/ai-memory](https://github.com/akitaonrails/ai-memory)
>
> Status：`untried`
>
> Category：Cross-agent memory / vendor handoff for coding CLIs
>
> Last updated：2026-10-06

## 產品介紹

ai-memory 是一套給 **coding agent CLI** 用的長期記憶基礎設施：單一 Rust
binary，在你自管的機器上跑 server，把多種 harness（Claude Code、Codex、
Cursor、Pi、OpenCode 等二十餘種）的工作軌跡收進同一套 **git 版控的
Markdown wiki**。真實來源是磁碟上的 `.md` 頁面；SQLite 只是衍生索引，供
全文、實體、連結與（可選）向量檢索。

它要解的核心痛點是：各平台內建記憶綁在單一 agent、單一筆電，換工具或換機器
就要重講架構、失敗嘗試與未決問題。ai-memory 用 lifecycle hooks 在背景
**靜默擷取** prompt 與 tool 事件（經型別化隱私邊界清理），session 結束時
整理成可讀 wiki 頁，下一個 session——任意 agent、任意機器——可搜尋記憶並
接收 **typed、claim-once** 的 handoff，把接力棒寫成協定而不是口頭慣例。

## 主要 Features

### 跨 agent、跨機器、可團隊共用

多個 coding CLI 指向同一台 ai-memory server 時，專案知識在成員之間共享；
個人 handoff 仍可保持個人範圍。內建多使用者驗證、歸屬與 audit log（官方
描述，尚未親自驗證）。

### Git-backed Markdown 為真實來源

記憶以一般 Markdown 頁面存在 wiki 樹中，可用 grep、Obsidian 或手改；資料庫
可從檔案重建。預設路徑 **不需 LLM API**：擷取、搜尋、handoff 可在零 API
花費下運作。

### Lifecycle hook 擷取與 session 彙整

Agent 透過 hook（shell `curl` 或原生 `ai-memory hook`）送事件；server 清理
後寫入 observation，SessionEnd 時產生 session 摘要頁並建立 handoff 列。
高延遲環境可 spool 本機再 drain，熱路徑不應被網路阻塞（飽和時回 429）。

### 檢索：編譯式 wiki + 融合排名

`memory_query` 結合 FTS、實體匹配、連結鄰居 RRF，可選向量 cosine；概念上
接近 Karpathy「compile-not-retrieve」的 LLM wiki——頁面隨時間被改寫、
supersede，而非只堆 atomic fact rows。

### 記憶老化（預設零 LLM）

可依 tier 調 decay；被開啟、搜尋或連結到的頁面 decay 較慢。冷掉的 episodic
內容可 **extractive compaction** 成 durable facts；近重複可合併、矛盾可
標記——這些改寫預設關閉，且 **不硬刪**：git 與 version chain 可 `restore-page`。

### 可選 LLM：consolidation 與「dream」

設定 LLM provider 後，session 摘要可改寫成 `concepts/`、`decisions/` 等
durables 頁；閒置時可選背景把冷筆記叢集 merge 成 coherent 頁（使用中會
取消）。此路徑與 auto-improvement 皆 **opt-in**，零 LLM 路徑才是預設。

### 跨 vendor handoff 協定

Handoff 有型別、擁有者、**exactly-once claim**，下一個 harness 注入 bounded
brief 而非依賴使用者複製貼上。與 [Pi](https://github.com/earendil-works/pi)
durable handoff 等 **單一 runtime 內** 持久化互補：ai-memory 偏 **跨產品、
跨機器** 的共用記憶層。

## 主打賣點

- **檔案你擁有 + 單一二進位**：自架、可離線、purge／audit 語意清楚；對比
  hosted memory API 或 mandatory LLM loop，預設不綁雲端 spend。
- **換 CLI 不必重講故事**：與 Claude Code 內建 `MEMORY.md`、各 IDE 片段
  記憶不同，定位是 **專案級、跨 harness** 的 wiki 與 handoff。
- **自動擷取工作本身**：相對需要使用者說「記住這個」的工具，hook 路徑記錄
  實際 prompt／tool 生命週期（在清理邊界之內）。
- **與 [gbrain](https://github.com/garrytan/gbrain) 等差異**：gbrain 偏
  綜合問答與知識圖譜式 brain；ai-memory 更貼 **repo 專案 wiki + coding CLI
  整合矩陣**，並強調 vendor 切換與 markdown 可編輯性。
- 部分能力（向量、LLM consolidation、dream）是既有 memory 研究的重包裝，
  但 **零 LLM 預設 + git markdown SoT + claim-once handoff** 的組合仍是
  其差異化主張。

## 使用情境

### 同一 repo 在 Claude Code 與 Codex 之間切換

- 適合誰：常因配額、模型或個人偏好換 coding CLI 的開發者。
- 在什麼情況使用：長任務做到一半要換工具，但不想重畫架構圖或重述已失敗方案。
- 帶來的價值：下一個 agent 收到 handoff 與可搜尋 wiki，從「進行中狀態」
  接續而非從零 onboarding。

### 桌面與筆電共用專案記憶

- 適合誰：在家裡 homelab 或筆電上跑同一 ai-memory server（或 sync wiki）。
- 在什麼情況使用：離開辦公室前 session 未結束，回家用不同機器繼續。
- 帶來的價值：open questions 與決策頁仍在同一專案命名空間，減少「只有
  某台機器的 chat 裡才有」的孤島。

### 小團隊共用 agent 學到的 repo 知識

- 適合誰：多人對同一 codebase 用不同 agent，希望 **專案層** 記住 gotcha
  與決策的人。
- 在什麼情況使用：A 的 session 踩過的坑，B 的 Codex session 應能檢索到。
- 帶來的價值：知識從個人 chat 提升到可審計、可 grep 的 wiki（權限與隔離
  需實測確認）。

## 我們可以學什麼

- **記憶應是 workspace 的共用層，不是某個 ADE 側欄**：Conductor、Maestro、
  Orca 等 orchestration UI 仍需要底下有一層 **canonical、可 handoff 的
  project memory**；ai-memory 示範如何把這層做成 **檔案 + server**，而不是
  綁在單一 dashboard。
- **Handoff 要協定化**：claim-once、typed owner、bounded brief 注入——在
  2D／3D workspace 裡，「換員工接案」應有可見的 **接力狀態**（誰 claim、
  還剩哪些 open questions），而不是只有 chat 摘要。
- **Context 流動**：capture → consolidate → recall → handoff 四段可對應
  workspace 上的 **進度與 provenance**：哪些 observation 變成 wiki 頁、
  哪一版 supersede 哪一版、audit 誰改了記憶。
- **2D workspace**：可視化一個 **專案 wiki 樓層**——session 摘要、decisions、
  gotchas 是平面上的房間或卡片；搜尋與 RRF 排名是「員工查檔案櫃」的互動，
  handoff 佇列是待接案的收件箱。重點是 **工作記憶的可讀與可編**，不是
  知識圖譜 3D 旋轉。
- **3D workspace**（可走進的 office，如 Agent Office）：員工（各 harness）
  離開座位或換人時，office 裡應看到 **handoff 火炬** 從 A 傳到 B；共用
  server 上的 wiki 是 **辦公室圖書館**，誰在查哪一頁可對應 spatial
  attention（尚未確認 ai-memory 是否提供即時 presence API）。
- **Runtime／權限**：hook 熱路徑不阻塞、429 背壓、sanitize 邊界——我們設計
  sandbox 與 observation 管線時可借鑑 **typed privacy boundary** 與
  idempotent hook replay。
- **不該照搬**：完整 Rust server 與二十 harness 矩陣對我們 workspace 產品
  可能過重；可先吸收 **markdown SoT、零 LLM 預設 aging、handoff 協定** 三
  個 primitive，再決定是否自建索引或接 MCP。

## 初步看法

- 最有價值的部分：把「跨 CLI 記憶」從口頭慣例變成 **可版本化、可搜尋、
  可 claim 的基礎設施**；與本 repo 研究軸「context、memory 與 handoff」
  高度對齊。
- 最大限制或疑問：部署與 hook 設定對非 Rust／非 DevOps 使用者門檻高；多使用者
  隔離、召回品質與 dream 路徑的實際體驗 **尚未確認**。與 gbrain、Mem0、
  mcp-memory-service 等並存時，團隊需明確選 **repo wiki** 還是 **brain／
  fact store** 為 canonical。
- 是否值得進一步研究或親自體驗：值得作為 **coding CLI 共用記憶層** 的
  參考實作；若要做 2D／3D workspace，優先研究 handoff 與 wiki 頁生命週期
  的資料模型，不必先跑完整 auto-improvement。

## 後續補充（選填）

此 Cell 僅依官方 README、`docs/ARCHITECTURE.md` 與 support matrix 做產品
研究，未 clone 或執行 binary。若 hands-on，建議在 `/workspace-labs/ai-memory`
驗證：兩個 harness 交替、handoff claim 是否 exactly-once、零 LLM 搜尋是否
夠用。

## Sources

- [ai-memory repository](https://github.com/akitaonrails/ai-memory)
- [Architecture](https://github.com/akitaonrails/ai-memory/blob/main/docs/ARCHITECTURE.md)
- [Support matrix](https://github.com/akitaonrails/ai-memory/blob/main/docs/support-matrix.md)
- [Comparison with other memory tools](https://github.com/akitaonrails/ai-memory/blob/main/docs/comparison.md)
- Discovery：GitHub starred scan（2026-10-06）
