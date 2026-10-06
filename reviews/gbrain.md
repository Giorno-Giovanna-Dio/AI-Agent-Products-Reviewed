# gbrain

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gbrain.md)
> - [Pages 閱讀器](https://giorno-giovanna-dio.github.io/AI-Agent-Products-Reviewed/#/reviews/gbrain.md)（`main` 部署後）
>
> Cell ID：[https://github.com/garrytan/gbrain](https://github.com/garrytan/gbrain)
>
> Status：`untried`
>
> Category：Agent memory / knowledge layer
>
> Last updated：2026-10-04

## 產品介紹

gbrain 是給 AI agents 用的長期記憶系統。一般 chat 結束後，agent 通常會
忘記先前決定、人物關係和做過的事；gbrain 把這些內容存成可搜尋、可引用
的知識，讓下一輪對話還能用。

使用者可以把它接進 Claude Code、Codex、Cursor 或其他 agent，也可以讓它
在背景持續吸收會議、信件和筆記。它不是 coding workflow 本身，而是 agent
背後的大腦。

## 主要 Features

### 給答案，而不只是搜尋結果

問「明天和 Alice 開會前我該知道什麼」時，它會整理成一段有來源的回答，
並指出目前還缺少哪些資訊，而不是丟回一堆頁面讓人自己讀。

### 自動建立知識圖譜

寫入一頁內容時，它會抽出人物、公司、會議等關係。之後可以問「誰在這家
公司工作」或「上次談過什麼」，而不只靠關鍵字比對。

### 跨 session 記憶

Agent 可以把決定、學習和人物資料寫進 brain。重開一個新 session 後，仍
能把這些內容找回來，不需要每次重新解釋背景。

### 簡單的記憶動詞

可以用 `recall`、`remember`、`entity`、`synthesize`、`forget` 這類清楚
動作操作記憶，不必一次面對整套複雜工具。

### 本機或團隊共用

可以在本機快速建立一個不需伺服器的 brain，也可以作為團隊共享的公司
記憶。官方強調查詢時只會看到被允許的資料。

## 主打賣點

- 核心主張是：agent 不該每次都從空白開始，而應先查自己已經知道的事。
- 與一般筆記搜尋不同，它強調綜合回答、知識關係和「目前還不知道什麼」。
- 與 [gstack](https://github.com/garrytan/gstack) 不同：gstack 比較像不同
  專長的員工，gbrain 比較像整間辦公室共用的記憶。兩者可以搭配，但應分開
  理解。
- 它可以是輕量的 coding-agent memory，也可以是一直在吸收資訊的個人或
  公司 brain。

## 使用情境

### 讓 coding agent 記得專案決定

- 適合誰：常用 Claude Code 或 Codex，卻每次都要重講背景的人。
- 在什麼情況使用：同一專案會跨很多 session 持續開發。
- 帶來的價值：agent 可以先查「我們對 auth 做過什麼決定」，再開始工作。

### 開會或見面前的準備

- 適合誰：需要快速回顧人物、公司和未完成事項的人。
- 在什麼情況使用：腦中有會議、信件、筆記，但來不及全部重讀。
- 帶來的價值：先得到一份有來源的摘要，再決定要補問什麼。

### 建立團隊共用記憶

- 適合誰：希望多人 agents 共用制度知識，但不互相看到私人筆記的團隊。
- 在什麼情況使用：專案決策、客戶關係或研究資料需要長期保存。
- 帶來的價值：把公司知識從個人 chat history 裡拿出來，變成可查詢的
  共享層。

## 我們可以學什麼

- Agent workspace 需要把「這次對話」和「長期記憶」分開。記憶不該只藏在
  prompt 裡，而該是辦公室裡大家都能去查的東西。
- 搜尋結果和綜合答案是兩種不同東西：一個是來源，一個是整理後的判斷。
- 2D 很適合讀這些資料：來源卡、綜合回答、人物與決定的關係。那是員工查
  記憶時看到的內容，不是辦公室本身。
- 這裡說的 3D，不是去做 3D 模型、立體圖譜，也不是把記憶變成可旋轉的物件。
  3D 指的是像
  [Agent Office](https://github.com/AgentSystemLabs/agent-office) 那樣，
  可以走進一間辦公室、看到員工在場工作的介面。
- 這也不是 Firstmate、Maestro 或 Conductor 那種 ADE dashboard。那些只是把
  記憶做成側欄或搜尋面板。
- 在這個 office 裡，gstack 是走進空間的員工，gbrain 是他們共用的記憶。
  Agent Office 目前偏陽春，但已經有「誰在辦公室裡」；還缺的是員工坐下後
  能先查這間辦公室記得什麼，而不是每次重問使用者。
- 值得借鑑「先查再問」：員工進辦公室後，應先問共用記憶，而不是先問人。
- 不該直接照搬全部 50 多個 skills。我們應先吸收 memory verbs、citation
  和 gap analysis 這幾個核心概念。

## 初步看法

- 最有價值的部分：把 agent 從一次性 chat 變成有記憶、有來源、知道自己
  缺什麼的系統。
- 最大限制或疑問：安裝路徑很多，範圍從本機 memory 到 24/7 個人 agent，
  第一次使用容易不知道該選哪一層。尚未親自驗證隱私隔離與實際查詢品質。
  對未來 3D office 來說，這也是 improvement gap：記憶應該是辦公室的共用
  層，而不是每個員工自己再裝一套大腦。
- 是否值得進一步研究或親自體驗：值得先研究 memory、graph 和 synthesis
  的產品概念；不必先部署完整 always-on agent。

## 後續補充（選填）

此 Cell 目前只做產品研究，沒有 clone 或實測。若之後要體驗，優先驗證：
跨 session 能否找回一筆決定、綜合回答是否真的附來源，以及它與 gstack
skills 搭配時的 handoff。

## Sources

- [gbrain repository](https://github.com/garrytan/gbrain)
- [Using GBrain with GStack](https://github.com/garrytan/gstack/blob/main/USING_GBRAIN_WITH_GSTACK.md)
- [Agent Office](https://github.com/AgentSystemLabs/agent-office)
