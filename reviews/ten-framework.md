# TEN Framework

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ten-framework-cell-9381/reviews/ten-framework.md)
>
> Cell ID：[https://github.com/TEN-framework/ten-framework](https://github.com/TEN-framework/ten-framework)
>
> Status：`untried`
>
> Category：即時語音對話框架（extension graph）
>
> Last updated：2026-10-10

## 產品介紹

TEN Framework 是開源的即時多模態語音對話框架，版權標為 Agora（2025）。開發者把一通語音通話拆成可替換的 extension，再用一張 graph 決定聲音、畫面、文字和指令怎麼流動。它服務的是要做語音 agent 的工程師，不是坐在程式編輯器裡看 coding agent 寫程式的人。官方網站是 [theten.ai](https://theten.ai)，文件在 [theten.ai/docs](https://theten.ai/docs)。本次只讀 repo 的 README、LICENSE 與現行文件，沒有安裝、沒有通話。

一次語音 session 就是 app 裡的一個 graph instance。`property.json` 的 `predefined_graphs` 先列出幾種預設場面；每個 graph 有 nodes 和 connections。Node 是一個 extension 實例：`name` 在這張圖裡唯一，`addon` 指向實作，同一種 addon 可以放多個 node。Connection 用四種協定把產出送出去：audio frame、video frame、data、cmd。文件描述的串接語音助理是：RTC 把 `pcm_frame` 送到語音辨識，辨識文字再交給語言模型，合成語音送回通話。另一種即時 voice-to-voice 是：RTC 的聲音直接進多模態模型，主 extension 不碰音訊，只處理模型送回的逐字稿、工具呼叫、打斷，以及誰加入、誰離開。回合靠 VAD 判斷有沒有人在說話，再用 Turn Detection 把這句話分成講完、還沒講完、或請對方等。TMAN Designer（README 示例是 localhost:49483）用來改這張圖；Agent Examples 的 localhost:3000 用來試聽。兩者都是儀表板。平面辦公室和可走進的辦公室要另做。

## 主要 Features

### Graph 就是一場通話的編制

TEN runtime 管 extension 的生命週期、資料流和執行緒。App 可以是獨立行程，也可以是既有行程裡的一條執行緒，並同時跑多張 graph；圖可以事先寫死，也可以在執行中組起來。文件說每個 graph instance 是 app 裡的一個 session。Extension group 對應一條執行緒：同一群、同一種語言的 extension 跑在同一條執行緒上，開發者宣告群組，不必自己管執行緒。每個 extension 有一個 ID，形如 `app-uri/graph-name/group-name/extension-name`。跨語言是刻意的：文件舉例用 C++ 做即時通訊、用 Python 做模型，放進同一張圖。一場通話的「誰負責聽、誰負責想、誰負責說」因此是可替換的節點。

### 即時媒體怎麼進這場通話

聲音和畫面從媒體 extension 進來。文件裡的語音助理 playground 寫明 RTC 目前用 Agora，需要 App ID 與 App Certificate。Repo 另有 WebSocket 範例、Twilio SIP 電話範例，以及唇形同步的 avatar 範例。是否每一張 graph 都綁 Agora，尚未確認。連線的單位是 frame 和訊息：例如 `agora_rtc` 把名為 `pcm_frame` 的 audio frame 送到 `deepgram_asr`。Video frame 走同一套連線，所以唇形、畫面分析是圖上的另一個節點。SIP 範例把同一套 agent 接到電話；實際接通品質尚未實測。

### 兩種把 agent 接上通話的方式

串接（cascade）是傳統語音助理：語音辨識、語言模型、語音合成分成幾個 extension，中間有一個 main extension 當事件匯流。文件說較新的預設 app 每張圖都有這個 main。進來的事件要在 `property.json` 先把來源接到 main；出去的事件可以在執行中用 `setDests` 指定目的地。即時 voice-to-voice 把音訊從 graph 直接送到多模態模型（文件稱 MLLM）。Python 即時範例的主程式只處理伺服器到客戶端的事件：逐字稿、工具呼叫、打斷、使用者加入與離開，並提供設上下文、送訊息、回工具結果、觸發回應的呼叫。音訊由 graph 直達模型。兩種組法可以是不同的 graph name，例如 README 的 `voice-assistant` 與 `voice-assistant-realtime`。哪一張是某個發行版的預設，尚未確認。

### 回合：什麼時候該開口

生態系把「有沒有人在說話」和「這句話結束了沒有」拆開。TEN VAD 是串流的語音活動偵測，用來知道使用者正在講，以及插話（barge-in）。TEN Turn Detection 是另一個 repo 的模型，文件說它把使用者文字分成三種狀態：Finished（講完了，在等回應）、Unfinished（停頓但還要繼續）、Wait（請 AI 暫停或停止）。整合順序寫成：VAD 先判斷有聲音，Turn Detection 再判斷狀態，agent 再決定回應、等待或停下。文件列出的準確率尚未實測，這裡不採用。語音助理範例可以再掛上 Memory、VAD、Turn Detection 等 extension；預設 graph 是否已經接上，尚未確認。

### 改圖的 Designer 是儀表板

TEN Manager（`tman`）負責建立 app、安裝 extension、處理依賴。TMAN Designer 是它的網頁介面：開啟既有 graph、在畫布上新增節點、改 property、從 Cloud Store 安裝別人做的 extension。同協定的節點可以整顆換掉（例如換一家語音辨識），連線留著。Cloud Store 在文件裡被比成應用商店。Agent Examples 另有一個試聽用的網頁。這些畫面用來編排和除錯，是儀表板。人要看「誰在通話、哪一桌正在做事」，得另做平面或可走進的工作空間。

## 主打賣點

- **它加上的是「一場通話 = 一張 extension graph」。** 節點是可替換的聽、想、說、偵測回合；連線是 audio frame、video frame、data、cmd。換模型通常是換 addon 或改 property。
- **TEN 和 LiveKit Agents 都是即時語音 runtime。** LiveKit Agents 另有一份筆記，這裡不依賴那份內容。兩者都在處理通話與回合；TEN 的組裝單位是 graph 裡的 extension。文件的語音助理把媒體路徑接在 Agora RTC 上，repo 另有 WebSocket 與 SIP 範例。
- **多語言 extension 共用同一場 session。** 媒體可以用 C++ 或 Go，模型可以用 Python，字幕可以用 TypeScript，文件說它們在同一張圖裡協作。Quick start 的 transcriber demo 就是 Go 的 WebSocket、Python 的語音辨識、TypeScript 的字幕。
- Designer、試聽頁、Cloud Store、現成的語音助理與 avatar 範例，是把 graph 編排出來並試聽的周邊。核心仍是 runtime、extension 和 graph。

## 使用情境

### 串接一通可替換零件的語音助理

- 適合誰：想先把聽、想、說拆開，再逐個換供應商的開發者。
- 在什麼情況使用：通話從 RTC 進來，`pcm_frame` 進語音辨識，文字進語言模型，合成語音回通話；API key 放在節點 property 或環境變數。
- 帶來的價值：換一家辨識或合成時，文件描述可以保留連線、只換同協定的 addon。實際替換手感尚未實測。

### 低延遲的即時對話，並處理插話

- 適合誰：希望聲音直接進多模態模型、並能在使用者插話時停下來的人。
- 在什麼情況使用：選即時 voice-to-voice graph，音訊直達模型；main 只看逐字稿、工具、打斷和加入離開。VAD 與 Turn Detection 決定現在該答、該等，還是該停。
- 帶來的價值：回合變成圖上的一個職責。Finished、Unfinished、Wait 是否真的讓對話變自然，尚未確認。

### 電話或多人說話還是同一張圖

- 適合誰：要把同一套 agent 接到電話，或要標出誰在說話的人。
- 在什麼情況使用：SIP 範例把電話接進 TEN；說話人分離範例即時標出講者。唇形 avatar 則是把合成結果再送到一個畫面節點。
- 帶來的價值：進線方式（網路通話、電話）和「誰在講」可以是圖上的 extension。接通範圍與供應商清單尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：一場語音工作是一張編制表。員工是 extension（聽、想、說、偵測有沒有人說話、判斷這句話結束了沒有）。這一班人組成一個 graph instance，也就是一場 session。同一種 addon 可以有多個工位，用 node name 區分。人加入或離開是這場通話的在場名單。
- 值得借鑑的 interaction / workflow：串接是聲音從通話桌交到辨識桌，文字交到模型桌，語音再交回通話桌。即時模式是聲音直接交到多模態模型桌，協調桌只收逐字稿、工具、打斷。Turn Detection 的三種狀態是協調者的手勢：講完才答、還沒講完就等、對方說停就停。`setDests` 是執行中改交件對象。換供應商是同一張桌子換人，路線留著。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室，一場通話是一間工作的房間。RTC 那張桌子的名牌寫著現在誰在線上。語音辨識、語言模型、語音合成、VAD、回合判斷、main 各有一張桌子，名牌是 node name，職務是 addon。工作單沿著連線走：audio frame、文字 data、cmd。串接房間裡，工作單依序經過辨識桌、模型桌、合成桌。即時房間裡，聲音單直接送到多模態模型桌，協調桌看逐字稿和打斷。回合判斷的人看這句是講完、還沒完，還是請對方等，再讓合成桌開口或停嘴。多場 session 是多間房間。Extension graph 是這些桌子之間的工作路由，人在樓層裡看的是誰在通話、哪一桌正在處理這一輪。TMAN Designer 掛在牆上，是改節點和 property 的儀表板。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。你推門進這一場通話，先看到誰在線上。辨識的人坐在自己的位子收聲音，模型的人等文字到了才開始寫回答，合成的人把話送回通話那一桌。即時房間裡，聲音直接走到多模態模型的位子，協調的人站在中間看打斷和工具呼叫。有人插話時，正在說話的員工停下來。回合判斷的人在房間裡做手勢：等、答、停。SIP 是另一扇門進來的電話。說話人分離讓你看得出房間裡哪一位來賓正在講。Graph 的連線是工作從這一桌交到下一桌的路線。人走在房間裡看的是員工在場、誰在哪裡做事。Designer 仍是某張桌上的螢幕，走過去可以改圖；辦公室本身是這一場通話的現場。
- 需要重新設計的地方：TEN 的執行現場是 log、property 和 Designer 畫布。空間辦公室要另做「誰在哪一桌、這一輪聲音走到哪」。Designer 與試聽頁保持儀表板。VAD 和 Turn Detection 是生態系裡的獨立專案，預設 graph 有沒有接上尚未確認，先不要假設每間房都有這兩個工位。文件中的準確率、Agora 是否為唯一媒體路徑，以及 `setDests` 在每一種 graph 的實際行為，都尚未確認。授權也要分開看：根目錄 LICENSE 是 Apache License 2.0 再加條件，包含不得以和 Agora 競爭的方式部署，以及不得把框架裝到終端使用者裝置（含行動終端）；README 寫 `packages` 目錄是 Apache 2.0。借用「工位與路由」這個概念，和把 TEN runtime 嵌進辦公室，是兩件事。

## 初步看法

- 最有價值的部分：一場通話被寫成 session graph。聽、想、說、回合是不同工位，連線是工作的路由，人可以看見誰在線上、這一輪交到哪一桌。
- 最大限制或疑問：它是語音 runtime。Designer 是改圖的儀表板。授權附加條件、預設 graph 的實際接線、以及回合模型是否如文件所寫，都要等到親自跑過才知道。
- 是否值得進一步研究或親自體驗：值得，尤其是串接與即時兩種房間、加入離開、以及插話時誰停下來。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://theten.ai
- Repository：https://github.com/TEN-framework/ten-framework
- Documentation：https://theten.ai/docs
- License：repo `main` 的 `LICENSE` 為 Apache License 2.0，並附加部署條件（Copyright © 2025 Agora）。README 另寫 `packages` 為 Apache License 2.0。
- README（`main`）：即時多模態對話、Agent Examples、TMAN Designer、生態系（Framework、VAD、Turn Detection、Portal）
- 已讀文件：Concept overview、property.json、TMAN Designer、Turn Detection、VAD、Framework quick start、語音助理 playground、即時 V2V main extension、修改 main extension
