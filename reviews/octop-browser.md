# Octop Browser

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-browser-cell-6a61/reviews/octop-browser.md)
>
> Cell ID：[https://github.com/TencentCloud/octop-browser](https://github.com/TencentCloud/octop-browser)
>
> Status：`untried`
>
> Category：Agent browser runtime（CDP）
>
> Last updated：2026-10-08

## 產品介紹

Octop Browser 是騰訊雲 Octop／Harness 堆疊裡的瀏覽器執行層，Python 套件名
`octop-browser`。它幫 agent 開一台真的 Chromium，自己講 Chrome DevTools
Protocol，用短的元素代號去點、打字、截圖，並用命名 profile 把登入留在本機。

使用者從 CLI、Python 或 MCP 呼叫它。每一次呼叫看起來無狀態，同一個
profile 會接回已經開著的那台瀏覽器。它沒有任務看板，也沒有辦公室畫面。
它比較像員工工位上那台已經登入的電腦，加上看人做一次之後留下的手順。
共用的是這台電腦的登入，不是一份全辦公室記憶，也不是 coding ADE 的側欄。
尚未安裝執行。官方首個版本是 2026-09-24 的 1.0.0。

## 主要 Features

### 命名 profile：一台電腦、一份登入

每個 profile 名稱對應一份 Chrome user-data 目錄和一個 CDP 埠。登入一次，
之後用同一名稱再開，仍是同一組 cookie。關掉連線時瀏覽器預設還開著，下一道
命令可以接回去；要結束行程需另外下 kill。閒置逾時可以選擇關掉沒人用的本機
Chrome，資料仍留在磁碟。

對 workspace 來說，profile 是工位上的那台電腦，會跨過一輪對話繼續存在。

### 分級看頁面，再用代號點

`dom-tree` 有四檔：只看標題與網址、只看可點可輸入的元素、整頁可讀內容、
給程式用的 JSON。官方給的 token 量級大約是 50、200–500、1k–3k。給 agent
的預設是可互動那一檔，並附上 `btn_1` 這類短代號。

官方說這些代號在版面重排後仍指向同一個節點。架構說明寫明換頁會清掉代號；
附帶的 skill 也要求每次點擊前先重抓頁面。頁內重排是否真的穩，尚未實測。

### 同一組動作，三種入口

導覽、點擊、輸入、捲動、分頁、截圖、執行一小段 JavaScript，都走同一套動作。
Python 用一個 `browser_tool` 分派；MCP 把每個動作拆成獨立工具；CLI 是
agent skill 建議的路徑。每次動作回傳成功與否、內容、錯誤，以及耗時和估計
token。截圖寫成檔案路徑，對話裡只留下路徑。可以掛上動作前、動作後與失敗
的鉤子，方便外面的系統記 log。這是觀測接口，產品本身沒有進度畫面。

### 錄一次，變成手順，再決定怎麼重做

錄製會注入腳本，記下點擊、輸入、導覽與送出，再把原始事件收成語意步驟。
敏感欄位預設遮掉，回放時用 `--input` 把密碼這類值補上。`generate-skill`
產出一份 skill 草稿：用可見名稱描述步驟，要求頁面變了就重抓 DOM，對不上
就停下來說明。

`replay run` 是程式自己重做。每步重新抓 DOM，用錄下的目標描述找元素，
找不到再試 selector，點擊最後才退到當時的座標，結束時檢查網址或文字。
失敗會留下截圖、DOM 快照和報告。官方 README 把回放形容成「模型依意圖重做」。
依原始碼，交給模型讀的是 skill 草稿；`replay run` 本身是步驟執行器。兩條
路都尚未實際跑過。

## 主打賣點

- 它想被記住的是：agent 用真瀏覽器做事時，點得準、看得少、登入不用每次重來。
- 和常見「所有動作都經過 Playwright」的 browser-use 相比，它直接講 CDP。
  元素代號、分級 DOM、持久 profile 是這層執行期的形狀。`install-browser`
  仍可能借 Playwright 下載一次 Chromium，執行時不靠它。
- 錄製再生成 skill，把人的示範收成可重做的手順，秘密留到回放當下才注入。
  這比存一串座標巨集更接近員工訓練。
- CLI、MCP、Python 是同一組動作的不同插座。動作名稱本身是瀏覽器自動化的
  舊清單；新的是可重接的 session、可分級的上下文，以及失敗時留下的證據。

## 使用情境

### 已登入網站上的重複操作

- 適合誰：要讓 agent 操作 GitHub、後台或內部網站，又不想每次重新登入的人。
- 在什麼情況使用：人先在可見視窗登入一次，之後 agent 用同一個 profile
  接回去填表、點按鈕、截圖交差。
- 帶來的價值：登入態留在工位電腦上；agent 每次只拿當下需要的那一檔頁面描述。

### 看我做一次，之後交給員工

- 適合誰：流程不好寫成 API、但人做一遍就能說明的操作者。
- 在什麼情況使用：開錄製，自己走完結帳、查單或後台審核，停下來生成 skill
  或直接 replay。
- 帶來的價值：示範變成手順和可檢查的步驟；密碼不寫進錄製檔；失敗時有截圖
  可以對。

### 接進既有 agent，而不是換一套工作台

- 適合誰：已經有 MCP 或會跑 shell 的 agent，只缺瀏覽器的人。
- 在什麼情況使用：把 octop-browser 接成工具。agent 仍負責任務與對話，
  瀏覽器只負責這一台 Chrome。
- 帶來的價值：瀏覽器執行和任務編排分開。Octop 本體、harness-agent、
  harness-memory 是旁邊的專案，這個 Cell 只覆蓋瀏覽器這一層。

## 我們可以學什麼

- 值得借鑑的 product idea：瀏覽器是工位上的一台具名電腦。登入、分頁、截圖
  都掛在這台電腦上，對話結束電腦還在。
- 值得借鑑的 interaction / workflow：看頁面要有距離。遠看只確認網址，走近
  才看到可點的東西，坐下才讀全文。人示範一次，留下手順卡；重做時每一步
  重新看螢幕。對不上就停，並把照片留在桌上給人驗。秘密放在手順之外，用的
  時候才交。
- 在 2D workspace 裡會變成什麼：俯視平面上，每個 profile 是一張固定工位，
  名牌寫 `github` 或 `work`，螢幕上是目前分頁。員工站在工位前。鏡頭拉近，
  才從網址名牌變成可點控制項，再變成整頁。人在同一張桌子示範時，桌邊釘上
  手順卡。回放失敗時，截圖和頁面快照落在桌上。兩個員工要共用同一台電腦時，
  平面上應看到誰正坐在鍵盤前；交接是把座位讓出來。
- 在 3D workspace 裡會變成什麼：走進辦公室，走向一台開著的螢幕，看得到員工
  正在點哪一頁。人可以坐下接手同一台已登入的電腦，員工站開，登入還在。
  錄製像有人在旁邊看你操作，結束後該員工的手冊多一張手順。重做時員工每步
  轉頭看螢幕再伸手。失敗時他停手，桌上留下照片。螢幕關掉表示他在做事，但
  從走道看不到內容，走過去才能打開看。這是工位上的瀏覽器。
- 不值得照搬或需要重新設計的地方：完整動作清單不該成為 workspace 的主介面。
  座標點擊只適合作為對不上元素時的最後手段。整頁 DOM 不該倒進共用記憶。
  登入 profile 是高權限的工位電腦，接手與交回要明示；原始碼裡尚未看到兩個
  agent 同時使用同一 profile 的鎖。Octop 助理、記憶和訊息閘道是別的
  repo，瀏覽器這一層單獨就夠放進辦公室。

## 初步看法

- 最有價值的部分：具名瀏覽器工位、分級注意力、示範收成手順、失敗留下截圖
  與 DOM。這四件事都能直接放進 2D／3D 辦公室。
- 最大限制或疑問：沒有自己的空間介面；同一 profile 的並行使用尚未確認。
  README 對「模型回放」和「代號跨重排仍有效」的說法，需要親手跑一次才能
  和 skill、RefCache、replay 原始碼對上。附帶 skill 裡的 repo 網址仍指向
  內部 git，和 GitHub canonical 不一致。
- 是否值得進一步研究或親自體驗：值得。尤其是可見視窗裡的接手、錄製隱私，
  以及失敗證據要怎麼出現在工位上。這和 Agent Office「人看見員工在螢幕前
  工作」可以直接對上。

## 後續補充（選填）

Not tried yet。這次只讀官方 README、CHANGELOG、AGENTS.md、附帶 skill，以及
refs、replay、skill generator 的原始碼，沒有安裝或執行。

## Sources

- Official website：[https://octop.cloud](https://octop.cloud)
- Repository：[https://github.com/TencentCloud/octop-browser](https://github.com/TencentCloud/octop-browser)
- Documentation：repo `README.md`、`README_CN.md`、`AGENTS.md`、`CHANGELOG.md`（1.0.0，2026-09-24）
- Related：[harness-agent](https://github.com/TencentCloud/harness-agent)、[Octop](https://github.com/TencentCloud/Octop)
