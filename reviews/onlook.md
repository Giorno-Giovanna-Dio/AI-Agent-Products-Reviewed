# Onlook

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-onlook-cell-90bd/reviews/onlook.md)
>
> Cell ID：[https://github.com/onlook-dev/onlook](https://github.com/onlook-dev/onlook)
>
> Status：`untried`
>
> Category：Visual editor for a running UI
>
> Last updated：2026-10-08

## 產品介紹

Onlook 是一套把「正在跑的介面」當成畫布的設計工具。官方文件稱它為給設計師的
Cursor：在瀏覽器裡改 React／Next.js 與 Tailwind 的畫面，改動再寫回程式碼。
使用者把專案放進會跑起來的環境，用預覽看結果，點畫面上的元素對回原始碼，
或用 AI chat 請它改程式。

這個 GitHub repo 的 README 把自己寫成「一開始的開源視覺編輯器」（Next.js +
Tailwind），並把接下來的 hosted 產品放在 early access waitlist。同一份 README
也寫可以用 [onlook.com](https://onlook.com) 或本機跑。我們沒有實際打開產品，
所以開源版和網站上的產品現在差多少，尚未確認。

和 [Nimbalyst](https://github.com/nimbalyst/nimbalyst) 相比：Nimbalyst 的視覺
物件是 markdown、mockup、圖表，包在 coding agent 旁邊；Onlook 的物件就是那個
正在跑的介面。

## 主要 Features

### 跑起來的介面就是畫布

README 描述的流程是：程式載入 web container，容器把畫面 serve 出來，編輯器
放進 iframe，再讀取、索引程式，並用 instrumentation 把元素對回原始碼。人先改
畫面上的東西，然後才寫進 code。AI chat 也有程式工具。

Architecture 文件用的是另一套說法：像瀏覽器開發者工具一樣先改 DOM，變更在寫回
程式前不算持久；build 時塞入類似 sourcemap 的屬性，指出程式位置與元件範圍，
再解析 AST 寫回。該頁仍指向舊的 Electron 架構筆記，並寫成程式寫回本機。兩份
文件對「程式跑在哪、寫回哪」沒有對齊，實際以哪一套為準，尚未確認。文件說這套
對應理論上可用在會宣告式畫出 DOM 的框架，目前專注 Next.js 與 Tailwind。

### 點元素，落到原始碼

README 的用法說明寫：隨時可以對元素按右鍵，打開它在程式裡的位置。交接發生在
「畫面上的這一塊」和「程式裡的那一行」之間，不必先另寫一份規格。

### 圖層、樣式與程式並排

Getting started 把介面分成中央 canvas、layers、properties、Tailwind 視覺樣式、
code panel 和 AI chat，並有專案與檔案導覽。歡迎頁另寫主題／樣式系統，以及從
應用裡部署。README 的功能清單把品牌 tokens、頁面、圖層、即時預覽、即時程式
編輯列為已有；拖放元件面板仍標成未完成。這些是官方清單，我們沒有逐項操作。

### AI chat 附在畫布旁邊

文件說可以用文字或圖片開專案、用預設模板，並用 AI chat 建立或修改正在做的
專案。Getting started 把 AI 寫成能依描述產生元件、給建議、把設計轉成程式、
協助除錯。README 清單把「一次排多則訊息」標成已有；用圖片當參考、在專案裡
接 MCP、讓 Onlook 呼叫自己去開 branch，仍標成未完成。對話品質尚未確認。

### 分支、checkpoint、發佈

README 把 branching（試驗不同設計）和 checkpoint（儲存、還原）列為已有。即時
共同編輯打勾，留言未完成。部署清單包含分享連結與自訂網域。這些機制在 hosted
與自架是否同一套，尚未確認。

### 沙盒與外部服務

README 的技術棧把 CodeSandbox SDK 寫成 dev sandbox，Freestyle 寫成 hosting，
並用到 Next.js、Tailwind、tRPC、Supabase、Drizzle、AI SDK、OpenRouter，以及
Bun 與 Docker。自架文件要求 Supabase、OpenRouter key、CodeSandbox token。
另一頁 external services 又寫可擴充部署時用 E2B，並說完全本地的 sandbox 是
以後才加。所以「現在預覽到底跑在哪一種沙盒」，文件之間尚未對齊。沙盒裡的權限
邊界尚未確認。

## 主打賣點

- 它最想被記住的是：設計發生在正在跑的介面上，每個元素能對回真實程式，而不是
  另畫一張不會執行的稿。
- 官方把自己放在視覺編輯加上你的 codebase，並和 Bolt、Lovable、v0、Figma Make、
  Webflow 這類放在一起。我們沒有實測，不能把「做出可上線的程式、不必深入會寫
  程式」當成已驗證事實。
- AI chat、即時預覽、寫回程式，很多是既有 design-to-code 的組合。辨識度在
  「點正在跑的畫面，落到原始碼」這條對應。和 Nimbalyst 的差別是 artifact：
  那邊是文件與圖，這邊是 running UI。
- README 同時提供 hosted app 連結，又把下一代 hosted 產品放進 waitlist。
  現在打開 onlook.com 會看到哪一套，尚未確認。

## 使用情境

### 直接改正在跑的畫面

- 適合誰：看得到 React 畫面、想用手勢改樣式與文案，並要立刻看到執行結果的人。
- 在什麼情況使用：調整間距、文字、Tailwind 樣式，或從圖層選到某一塊元件。
- 帶來的價值：驗證的對象是畫面本身，改完能指回程式位置。

### 用分支比較兩種介面

- 適合誰：想試兩個視覺方向，再決定留哪一個的人。
- 在什麼情況使用：README 所寫的 branching，配上 checkpoint 還原。
- 帶來的價值：比較的是兩份會跑的介面。分支實際隔離到什麼程度，尚未確認。

### 請 AI 改畫面，人用點選介入

- 適合誰：想用自然語言生或改元件，但仍要自己點選、改屬性、看程式的人。
- 在什麼情況使用：從文字、圖片或模板開專案，或對現有畫面下指令。
- 帶來的價值：委派和介入發生在同一塊畫布上。AI 改得準不準，尚未確認。

## 我們可以學什麼

- **Agents、tasks、goals**：依文件，Onlook 是一塊視覺成品表面。畫布是正在跑的
  介面，AI chat 是附在旁邊、帶程式工具的助手。目標是「這塊介面要長怎樣」。
  README 有訊息佇列；讓產品呼叫自己去開 branch 仍是未完成項。文件沒有把工作
  編成共享 backlog 或一組有座位的員工。
- **Context、memory、handoff**：交接靠元素到原始碼的對應。Architecture 頁說
  每次修改存成可序列化的 action，以便以後協作，或讓 agent 產生 action。這條
  和現在 README 的 AI chat 工具是不是同一條路，尚未確認。品牌 tokens 是一小塊
  設計記憶，不是團隊的長期共用記憶。
- **Runtime、sandbox、permissions**：預覽在 iframe。README 寫程式進入
  CodeSandbox SDK 的 web container，發佈走 Freestyle。自架還要 Supabase 與
  模型 key。E2B 與未來的本地 sandbox 出現在另一頁，權限模型尚未確認。這裡管理
  的是「這份 UI 跑在哪個容器」，不是一排 agent 座位。
- **進度、artifacts、review**：看得到的成品是正在跑的介面，加上 checkpoint 和
  分享連結。審的方式是看預覽，再用右鍵對回程式。這和 Nimbalyst 在渲染後文件上
  做紅綠 diff 是兩種 review。
- **人如何委派、比較、介入、驗證**：用 chat 委派；用文件所寫的 branch 比較；
  用點選元素、改樣式或直接改 code 介入；用預覽加上原始碼位置驗證。即時共同
  編輯在清單裡打勾，留言未完成，所以「誰在場」還沒有完整的協作模型。
- **在 2D workspace 裡會變成什麼**：一層平面工作室。地板中央是一塊大螢幕，
  上面就是正在跑的產品。左邊一疊圖層紙，右邊一張程式桌。點螢幕上的按鈕，一條
  線連到右邊對應的檔案。AI 站在螢幕側邊，改動先出現在螢幕上，再寫進程式桌。
  不同設計分支是相鄰的地板，人走過去比較兩塊會跑的畫面。Checkpoint 是釘在這層
  樓牆上的快照。確認的方式是看這塊螢幕。
- **在 3D workspace 裡會變成什麼**：可走進的設計工作室，接近
  [Agent Office](https://github.com/AgentSystemLabs/agent-office) 那種能看到
  誰在場、人站在哪。牆就是正在跑的產品。摸到牆上的一個控制項，對應的程式工位
  會亮起來，人可以走過去看那份原始碼。AI 員工站在牆前，先在玻璃上改（iframe），
  再把結果放進原始碼櫃。另一個人若也在改，會站在同一面牆前；文件說即時編輯已有，
  在場感實際長怎樣尚未確認。分支是側間，裡頭是同一面牆的另一個版本。驗證是站到
  牆前面看。重點是誰站在這面介面牆、改動落在哪一張工位。
- **需要重新設計的地方**：進到 workspace 時，值得留下的是兩步：點正在跑的元素，
  落到原始碼或工位；改動先出現在預覽，再寫回程式。完整的 Figma 式面板可以縮到
  這兩步需要的圖層與樣式。文件對 runtime 的說法還沒對齊，進 workspace 前要先
  選定容器。歡迎頁寫的「不必深入會寫程式」是官方說法，尚未驗證。

## 初步看法

- 最有價值的部分：把審閱物件從文件改成正在跑的介面，並保留元素到原始碼的對應。
- 最大限制或疑問：README 把開源編輯器和下一代 hosted 產品拆開；architecture
  頁仍描述本機寫回與舊 Electron 架構；sandbox 在 CodeSandbox、E2B、未來本地
  方案之間沒有單一說法。現在使用者會遇到哪一套，尚未確認。
- 是否值得進一步研究或親自體驗：值得。若 2D／3D workspace 要展示「誰正在改產品
  的哪一塊」，點選對應比再做一面文件牆更接近那個畫面。本 Cell 尚未親自使用。

## Sources

- Official website：https://onlook.com
- Repository：https://github.com/onlook-dev/onlook
- Documentation：https://docs.onlook.com
- Architecture：https://docs.onlook.com/developers/architecture
- Self-hosting：https://docs.onlook.com/self-hosting
- License：Apache-2.0（`LICENSE.md`，Copyright 2024 On Off, Inc.）
