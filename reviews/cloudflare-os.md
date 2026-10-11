# Cloudflare OS

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cloudflare-os-cell-c2d2/reviews/cloudflare-os.md)
>
> Cell ID：[https://github.com/cloudflare/cloudflare-os](https://github.com/cloudflare/cloudflare-os)
>
> Status：`untried`
>
> Category：Workers 上的 AI 生產力環境
>
> Last updated：2026-10-09

## 產品介紹

Cloudflare OS 是 Cloudflare 先在公司裡用、再開源的 AI 生產力環境。官方給它兩個意思：讓同事用 AI 做事時有一層安全邊界，以及用類似作業系統的方式管理這些 AI 工作。打開之後是網頁「Gadgets Workshop」：首頁寫第一句話，就開一個 workspace，請 agent 做簡報、改文件，或做出一個小程式。

它是執行環境加上網頁工作台。官方用線上辦公套件來比喻使用方式：每個成品預設只有自己看得到，可以分享，也可以從範本長出來。畫面是對話、成品預覽和待審清單。授權是 Apache-2.0，程式主要是 TypeScript。這份筆記沒有部署，也沒有執行。

## 主要 Features

### 一個 workspace 同時放對話、小應用和產出

使用者看到的單位叫 workspace。清單頁寫：每個 workspace 是隔離環境，有自己的對話、gatekeeper 和輸出。後端把每個 workspace 放在自己的 Durable Object。跑起來的小程式叫 Gadget，放在關掉對外網路的 Dynamic Worker。同一個 workspace 裡，Gadget 和對外連線都編成編號的 workpiece。Outputs 頁把各個 workspace 做出來的東西收成一份清單，人不用記得它生在哪一次對話。

### 預設沒有外部權限，要先介紹資源

Agent 和 Gadget 一開始碰不到外部帳號。部署就算已經接了 GitHub 或 Google，也不會自動帶進每一段對話。人要主動把某個資源介紹進去，例如貼一個 repo 連結，或在介面裡選。Agent 也可以請求介紹，由人答應或拒絕。Gatekeeper 是針對單一外部服務的 Worker：處理登入、把權限縮到那一個資源，並留下動作紀錄。

### 有副作用的動作先模擬，人晚點再批

官方說明：需要核准的動作不會把 agent 停住。Gatekeeper 在本地模擬結果，讓 agent 繼續排後面的步驟；人可以之後整批核准，或一筆一筆看。前端有 Activity，分成待審、歷史（動作、觀察、hook）和自動核准。程式註解寫：一旦讀到受限資料，自動核准會停用。這些是原始碼和 README 的描述，我們沒有點過畫面。

### 沙盒裡的成品，人和 agent 都能進去改

Gadget 的伺服器端不能自己上網，只能走人明確接上的 binding。瀏覽器端跑在沙盒 iframe，只透過 Cap'n Web 和自己的伺服器說話。因為介面本來就是這套呼叫，agent 也能直接叫同一個 API，在成品裡面一起改內容。程式用 git 存。Blueprint 分享的是程式快照，不含聊天紀錄、資料庫和憑證。別人用它生出自己的副本，更新不會自動套上，人要先預覽再接受。

### 分享時用對方自己的權限再檢查一次

協作者有兩種角色。`build` 可以改程式、用聊天、管連線。`use` 只能操作已經部署的介面，看不到主控台和動作紀錄。對方打開時，要用自己的外部帳號通過檢查，確認他本來就能讀這份東西已經讀過的資料。文件把過不了的人擋在外面，之後若讀到對方不該看的新資料，那次讀取也會被擋下。`docs/observers.md` 自己寫成可能過時的實作計畫，所以檢查的每個邊角尚未確認。分享連結的金鑰只在建立時出現一次，伺服器存的是雜湊。

### 人怎麼看到進度

進行中的事在 workspace 的聊天，以及 Activity 的待審和歷史。通知文件寫：人自己發起的回合做完，或需要權限時，先送給正在看的瀏覽器分頁；約三秒沒人收下，才推到已登記的裝置。排程回呼和被生出來的 agent，只在需要人點頭時通知。深連結會打開那個聊天。另外，agent 回合會送出追蹤 span，給 Cloudflare 的 Agents 儀表板看回合、模型請求、工具呼叫和核准；span 帶 token 數，不帶提示詞內容。那是平台觀測。自架可以不接推播服務，瀏覽器通知仍然可用。實際通知是否這樣出現，尚未確認。

### 公司知識：後端有資料庫，導覽頁還是稿

README 說 agent 會帶著公司怎麼運作的知識。Repo 裡有 Context Library，依帳號保存私人文集，也可以有依網域的公開文集；agent 能列出、搜尋、讀文件，並從 `SKILL.md` 露出技能。前端 `/context` 的註解寫這塊導覽還沒接上，頁面是 Coming Soon 的設計稿。所以「一打開就預載公司手冊」到什麼程度，尚未確認。

## 主打賣點

- 它想被記住的是：每個人跑自己的一份小應用，agent 可以改它；外部世界只從窄權限的 Gatekeeper 進來；有副作用的事先模擬，人晚點批。
- Workers 是執行底層。官方寫 Dynamic Workers、Facets 等能力是為了這個產品加進 runtime 的。本地用 wrangler／workerd 跑，正式自架 workerd 的部署文件標成即將推出。產品層多出來的是 sandbox、介紹制權限、分享時的再檢查、可更新的 Blueprint，以及 Durable Object 上的即時協作。
- 「辦公套件」是他們對檔案怎麼被分享、怎麼從範本長出來的比喻。畫面是工作台。
- Agent 迴圈借用 Pi 的 `pi-agent-core`，用 Code Mode 寫一小段程式立刻執行。模型可以換。官方說同樣模型下往往更快、更省 token。這句我們沒有驗證。
- 這份 repo 是 v2 重寫。官方在 2026 年 8 月稱它為早期存取，還有很多粗糙的地方。

## 使用情境

### 做出一份只屬於自己的簡報或小工具

- 適合誰：想讓 agent 做簡報、白板或小程式，又希望成品預設是私人副本的人。
- 在什麼情況使用：從內建 Blueprint 開始，或直接說要一個新的小應用，缺功能再請 agent 改程式。
- 帶來的價值：內容和程式在 sandbox 裡；要給別人時可以協作，或只給程式範本。手感尚未確認。

### 改外部文件，先不要真的寫出去

- 適合誰：要把 GitHub、Google 文件這類資源接進來，又不想每一步都停下來等核准的人。
- 在什麼情況使用：人先介紹一個 repo 或文件，agent 把有副作用的步驟排好，用模擬結果繼續。
- 帶來的價值：人晚一點在 Activity 裡整批看過再核准。Gatekeeper 要先設定 OAuth，這一步我們沒有做。

### 把小工具複製給同事，各自接自己的帳號

- 適合誰：想把一個內部小工具變成別人也能改的範本，而不是自己長期維護一台共用服務。
- 在什麼情況使用：發布 Blueprint，同事用自己的模型和連線生出副本；或用 `build`／`use` 一起改同一份。
- 帶來的價值：程式更新要人預覽才套上；協作者用自己的模型與帳號。權限邊界尚未實測。

## 我們可以學什麼

- 值得借鑑的 product idea：文件、小應用和 agent 住在同一個 workspace，權限也是同一套。預設沒有外部能力，人把資源介紹進去才算數。有副作用的事先用模擬結果往下做，真的寫出去留在待審匣。分享時用對方自己的權限再查一次，避免資料跟著連結流出去。Blueprint 給的是可更新的程式副本，Outputs 把成品從各 workspace 收成一面牆。
- 值得借鑑的 interaction / workflow：人先看成品和待審，再決定要不要進聊天。完成通知只打擾人自己交代的那一回合；排程和子 agent 只在需要簽名時叫人。自動核准遇到受限資料就停。`use` 的人可以操作成品，碰不到程式和紀錄。
- 在 2D workspace 裡會變成什麼：一張平面樓層。每個 workspace 是一間工位。桌上是正在跑的成品（簡報、白板或小應用），旁邊是對話本，桌角是待審匣，裡面是還沒真正生效的模擬動作。Gatekeeper 是牆邊還沒插上的線路，人介紹資源之後才接通，每一筆外出都留在工位的紀錄條。`use` 的同事可以站到桌前操作成品，程式抽屜和線路櫃鎖著。讀到受限資料時，工位上的自動核准章收起來。Outputs 是走廊上的成品牆，不用逐間翻對話。公司知識若接上，會是工位旁的文件架；現在產品的導覽頁還是稿，所以先畫成空架。Cloudflare 的 Agents 儀表板留在機房圖例，人在樓層上看的是工位，不是那塊儀表板。
- 在 3D workspace 裡會變成什麼：走進一間可走的辦公室，一間房就是一個 workspace。成品攤在會議桌上，agent 坐在桌邊一起改，人走過去就能看進度、插手或簽名。有副作用的步驟是櫃上的一疊待寄文件：agent 拿模擬副本繼續做事，人晚點整疊簽過或退回。門口是 Gatekeeper 櫃台，沒被介紹的資源出不了門，每次外出都有登記。只有使用權的同事可以進房碰桌上的東西，程式櫃和連線間不開。進門前櫃台用他本人的權限核對：這房裡讀過的資料，他本來是不是就看得到。牆上的時鐘是排程，只有需要簽名才把人叫過來。追蹤 span 和 Workers 儀表板在建築外面的機房，看進度就在這間辦公室裡完成。
- 不值得照搬或需要重新設計的地方：整包作業系統比喻、每一家外部服務的 Gatekeeper，以及 Cap'n Web 的實作細節，都留在他們的 runtime。模擬結果如果畫得跟真的一樣，人會搞錯什麼已經生效，2D／3D 裡要標成「尚未寫出」。Context 導覽還沒接上，先不要把它畫成完成的公司記憶。早期存取和 OAuth 設定成本都還沒親手確認。自架 workerd 的正式部署文件仍標成即將推出。

## 初步看法

- 最有價值的部分：成品、agent 和權限邊界是同一個 workspace。人可以晚點批，agent 不必卡在第一步。
- 最大限制或疑問：它是平台工作台，空間呈現要我們自己做。公司知識的使用介面還沒接上。推播、模擬核准和分享檢查都還沒跑過。OAuth 與早期存取的粗糙程度尚未確認。
- 是否值得進一步研究或親自體驗：值得。優先看「介紹資源」、待審匣，以及分享時用對方權限再檢查，這三個怎麼放進平面樓層和可走進的辦公室。

## 後續補充（選填）

Not tried yet.

## Sources

- Repository：https://github.com/cloudflare/cloudflare-os
- Product site：https://os.cloudflare.app
- License：Apache-2.0（repo 的 LICENSE）
- 參考：README、`docs/sharing.md`、`docs/blueprints.md`、`docs/notifications.md`、`docs/observers.md`（文件自標可能過時）、workshop 前端的 workspace／Activity／context 路由、Context Library worker
