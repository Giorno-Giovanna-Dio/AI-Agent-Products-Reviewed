# Open-AutoGLM

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-open-autoglm-cell-ca43/reviews/open-autoglm.md)
>
> Cell ID：[https://github.com/zai-org/Open-AutoGLM](https://github.com/zai-org/Open-AutoGLM)
>
> Status：`untried`
>
> Category：手機操作 Agent 框架（Phone Agent）
>
> Last updated：2026-10-10

## 產品介紹

Open-AutoGLM 是智譜（Zhipu AI）開源的手機 Agent 框架。人用一句話派一件差事，例如打開某個 App 搜尋或比價，程式就在接上的手機上做完。跑在電腦上的是 Phone Agent；看畫面的是另一邊的視覺語言模型。手機用 ADB 接 Android、用 HDC 接鴻蒙，iPhone 則走 WebDriverAgent。官方介紹頁是 [autoglm.z.ai/blog](https://autoglm.z.ai/blog)。這次只讀 repo、模型卡與介紹頁，沒有安裝，也沒有連手機。

它服務的是想自己接一條「看螢幕、做手勢」迴圈的開發者，不是坐在桌面 ADE 裡看終端機的人。README 寫明僅供研究與學習。介紹頁把更大的 AutoGLM 說成能拿起手機把一件事做到底，並提到雲端虛擬手機可以回放、審計、隨時介入；這個 repo 裡看得到的是本機或遠端真機上的一步一動作。雲端那間手機房有沒有跟著開源，尚未確認。

## 主要 Features

### 看一眼、做一步

`PhoneAgent.run` 收到一句任務後進入迴圈。每一步先截圖，並附上目前在哪個 App。模型用 `<think>` 說短理由，用 `<answer>` 給一個動作。動作做完，這一步的圖片會從對話裡拿掉，留下文字推理和動作紀錄。下一步再看新畫面。預設最多 100 步，用盡就停並回報。沒給任務時，命令列一題一題問，做完就 `reset`，下一題不帶上一題的對話。`step()` 可以一次只走一步，用來看它想什麼。

### 手勢，以及用名字打開 App

模型可以做的事包括啟動 App、點、滑、輸入、返回、回桌面、長按、雙擊、等待，以及用 `finish` 交回一句結果。點和滑用的是相對座標，程式再換成這支手機的像素。Android 與鴻蒙各有一份 App 名稱對照，`Launch` 直接開 App，不必從桌面一格一格找。README 寫 Android 50 款以上、鴻蒙 60 款以上的中文 App。iPhone 是另一條 `IOSPhoneAgent`，文件要求 macOS、Xcode 和 WebDriverAgent。哪些 App 真的能做完，尚未實測。

### 人要插手的時刻

點擊若帶著 message，提示詞說那是財產、支付、隱私這類按鈕，程式會先問人。人拒絕，這次任務結束。`Take_over` 用在登入或驗證碼：預設在終端機停下，等人在手機上做完再繼續，迴圈不結束。提示詞還寫了 `Interact`（好幾個選項要人挑）、`Note`（記下這一頁）、`Call_API`（摘要已記下的內容）。程式裡 `Note` 和 `Call_API` 是空的，什麼都不存；`Interact` 只回傳需要互動，沒有停下來等回答。這三個和提示詞說的不一樣。

### 畫面抓不到的時候

ADB 與 HDC 的截圖若失敗，會交回一張黑圖，並標成敏感。README 把這種失敗聯想到支付、密碼、銀行頁。主迴圈沒有讀這個旗標，也不會因此自動呼叫接管。模型看到黑畫面會不會自己選 `Take_over`，尚未實測。

### 模型與手機是分開的

這個 repo 不含模型權重。模型走 OpenAI 相容介面，可以接智譜 BigModel 的 `autoglm-phone`、ModelScope 上的 AutoGLM-Phone-9B，或自己用 vLLM／SGLang 開服務。另有多語模型給英文畫面，系統提示用 `--lang cn` 或 `en`。裝置可以 USB，也可以用無線調試連到另一台機器，並用 `device_id` 指定多台裡的哪一台。Hugging Face 上 AutoGLM-Phone-9B 的模型卡寫 MIT；這個 repo 的 `LICENSE` 是 Apache-2.0。Midscene.js 已接 AutoGLM 模型，那是另一條 UI 自動化 SDK，不是這個框架的工作畫面。

## 主打賣點

- **它加上的是「手機螢幕就是工作台」。** 一句任務、每步一張畫面、一個手勢，然後再看。人不用為每個 App 寫 selector。
- **和瀏覽器裡看畫面再點的 agent 相同的是眼睛和手。** 不同的是工作發生在手機 App 裡，控制通道是 ADB、HDC 或 WebDriverAgent，系統提示還帶著中文 App 的操作習慣，例如先確認是不是目標 App、走錯就返回、頁面沒出來最多等三次。
- **人的角色很窄。** 敏感點擊要點頭，登入和驗證碼要把手機接過去。這裡沒有經理分派，也沒有多角色小隊。
- 介紹頁的雲端虛擬手機、可回放可審計，是更大的 AutoGLM 敘事。這個 repo 開源的是真機上的 Phone Agent 迴圈。模型、多語、遠端調試、Midscene 接入，是同一套看螢幕做事的周邊。

## 使用情境

### 用一句話跑完一個手機差事

- 適合誰：想讓自己的 Android、鴻蒙或 iPhone 照自然語言做事的開發者。
- 在什麼情況使用：任務寫在命令列，或在互動模式裡一題一題派。
- 帶來的價值：agent 看現在的畫面決定下一步，不用為每個 App 先寫腳本。

### 付錢或登入時人站在旁邊

- 適合誰：願意讓 agent 操作，但不想它自己按支付或自己填驗證碼的人。
- 在什麼情況使用：點擊帶確認訊息，或模型發出 `Take_over`。
- 帶來的價值：敏感的一步停在人的回覆。人拒絕就收工。登入做完，再把手機還給 agent 繼續。

### 一次只看一步，或連著做幾件不相干的差事

- 適合誰：要檢查推理，或連續派幾件互不相干的事的人。
- 在什麼情況使用：用 `step()` 單步走；互動模式一題做完就清空再問下一題。
- 帶來的價值：除錯時看得到每步的理由。下一件差事不會繼承上一件的對話。

## 我們可以學什麼

- 值得借鑑的 product idea：員工不是寫程式的角色，而是一位手機辦事員。任務是一句話。工作台是手機螢幕。每一步只做一個手勢，然後重新看畫面。
- 值得借鑑的 interaction / workflow：看畫面、說一句理由、做一個動作、再看。敏感點擊要人點頭才出手。登入和驗證碼是人把手機接過去，做完再坐下。一題結束交回一句結果，下一題把桌上的筆記清掉。步數上限是這一班做到哪裡必須停。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。辦事員坐在一張桌子，桌上平放著手機，螢幕就是他正在處理的工作。名牌寫目前打開的 App。每一步看見他看著螢幕，頭上冒出一句短理由，然後手點一下、滑一下或打字。點到付錢或隱私時，手停住，桌上出現一張要人蓋章的紙條；人不過來這一步就不做，拒絕就收工。登入時他站起來把手機遞給走過來的人，人做完放回桌上他才繼續。上一張畫面不會一直留著，桌上只留這一步做了什麼的短筆記。一題做完，桌上清空，等下一句差事。多支手機是多張桌子，要看的是誰坐在哪一張。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。走進房間就看見辦事員坐在哪張桌子、正在看哪一支手機。走到旁邊才看得到他眼前的畫面，和他正要做的那一下。敏感操作時他轉頭等你點頭，你不點頭他的手不會按下去。驗證碼時他把手機遞給你，你站在他的位子上做完再還他。遠端的手機仍是這張桌子上的工作，人要走到這張桌子才知道他在等。重點是看見誰在場、誰在哪裡做事、誰正在等你接手。
- 需要重新設計的地方：現在的軌跡是終端機印出的思考和動作，空間辦公室要另做「人坐在哪張桌、手停在哪」。`Note`、`Call_API` 在程式裡是空的，不要做成記事本或摘要員。`Interact` 提示詞說要問人選哪一個，程式沒有停下來等，先不要做成詢問站。黑畫面有敏感標記，迴圈卻沒有因此把手機交出來。它是一次只做一件差事的單一辦事員，不是多角色小隊。模型服務在另一台機器，辦公室裡看見的應該是辦事員，不是模型伺服器的儀表。介紹頁的雲端虛擬手機可以回放、審計，這個 repo 沒有那間房間。

## 初步看法

- 最有價值的部分：一步一畫面、敏感操作要人點頭、登入要人接手。這三件事可以直接變成工位上看得到的工作。
- 最大限制或疑問：提示詞裡的 `Interact`、`Note`、`Call_API` 和程式行為不一致。黑畫面會不會真的讓人接手，尚未實測。它要使用者自己的手機開調試，README 也把它限制在研究與學習。
- 是否值得進一步研究或親自體驗：值得看一步的節奏，以及人接手的時機怎麼出現在工位上。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://autoglm.z.ai/blog （2025-12-08，介紹頁；雲端虛擬手機為該頁敘事）
- Repository：https://github.com/zai-org/Open-AutoGLM
- Documentation：`README.md`、`README_en.md`、`docs/ios_setup/ios_setup.md`、`resources/privacy_policy.txt`
- License：repo `LICENSE` 為 Apache-2.0（Copyright 2025 Zhipu AI）。Hugging Face `zai-org/AutoGLM-Phone-9B` 模型卡 front matter 寫 `license: mit`
- 本次讀到的程式：`phone_agent/agent.py`、`agent_ios.py`、`actions/handler.py`、`model/client.py`、`adb/screenshot.py`、`hdc/screenshot.py`、`config/prompts_zh.py`、`examples/basic_usage.py`、`main.py`
- 沒有 release 或 tag。`main` 最近一次 push 為 2026-03-06
