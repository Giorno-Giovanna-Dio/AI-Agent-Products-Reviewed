# Hermes Workspace

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hermes-workspace-cell-ad04/reviews/hermes-workspace.md)
>
> Cell ID：[https://github.com/outsourc-e/hermes-workspace](https://github.com/outsourc-e/hermes-workspace)
>
> Status：`untried`
>
> Category：Hermes Agent 的 Web 指揮台（ADE）
>
> Last updated：2026-10-10

## 產品介紹

Hermes Workspace 是給 Hermes Agent 用的網頁指揮台。GitHub 簡介寫的就是 native web workspace：聊天、終端、記憶、技能和檢查器放在同一個介面。它服務的是已經在跑、或準備去跑 Hermes Agent 的人，用瀏覽器看對話、檔案、記憶和多個工人，而不是再做一個 agent 執行期。官方網站是 [hermes-workspace.com](https://hermes-workspace.com)。這次只讀 repo 的 README、LICENSE、`docs/swarm`、`docs/hermesworld` 與官網，沒有安裝。

Hermes Agent 是另一個產品。它的 canonical repo 是 [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)，文件在 [hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com/docs/)。那是 Nous Research 的自主 agent：做完工作會留下技能和記憶，有自己的終端介面，也能從 Telegram 等訊息管道說話；執行環境可以在本機、Docker、SSH，或 Daytona、Modal 這類會休眠的 sandbox。Workspace 的 README 把兩邊分開：Workspace 是介面，Hermes Agent 是腦子。v2 稱為 zero-fork，直接接 Nous 官方安裝的原版 agent，不再靠自己的 fork。介面預設在 `:3000`，透過 gateway（`:8642`，聊天、模型、串流、工作）和 dashboard（`:9119`，工作階段、技能、設定、MCP）跟 agent 說話。若只接一個 OpenAI 相容的聊天端點，會掉進 portable mode：聊天還在，工作階段、記憶和技能顯示不可用。

## 主要 Features

### 接上現成的 agent

README 寫了三種進場：Docker Compose 同時拉起 agent 映像和這個 UI、一行安裝、或是 gateway 已經在跑就只填網址。連線可以事後在 Settings → Connection 改，並寫進 `~/.hermes/workspace-overrides.json`。人用手機或另一台機器進來時，gateway 和 dashboard 的網址都要指到對方找得到的位址。介面若不是只綁在本機，啟動時會要求密碼，否則拒絕對外聽。這層處理的是「畫面怎麼找到腦子」。

### 聊天、檔案、終端

聊天走即時串流，工具呼叫畫成卡片，可以開多個工作階段。檔案區用編輯器看工作目錄，終端是瀏覽器裡的 PTY。人可以在同一頁看 agent 做了什麼、打開它碰過的檔，必要時自己下指令。官方要人記住的是指揮台，不是再包一層聊天視窗。

### 記憶、技能、MCP

記憶可以瀏覽、搜尋，並用 Markdown 改。技能頁用來翻目錄、看出處、進市集；README 寫 2,000 筆以上，數量尚未實測。MCP 有目錄頁，上游端點不夠時退回本地設定。這些資料住在 Hermes Agent 那邊（文件寫在 `~/.hermes` 這類家目錄；Docker 則放在名為 `claude-data` 的 volume）。Workspace 是書櫃的窗口。

### Swarm：角色、任務單、檢查點

`docs/swarm` 把 Swarm 寫成控制平面。可以有很多個有名字的 Hermes Agent 工人，每個有角色、profile、技能，以及持續開著的 tmux。一個 orchestrator 負責派工、抓偏離、決定下一步。人對主 agent（文件稱 Aurora）說出想要的結果，Aurora 收成一張 SwarmBrief：目標、範圍、交付物、要什麼證據、檢查點格式。工人做完交檢查點，狀態是 DONE、HANDOFF、BLOCKED、NEEDS_REVIEW、NEEDS_INPUT 或 IN_PROGRESS，並附改了哪些檔、跑了哪些指令、證據和下一步。檢查點預設先回到 orchestrator。工人不直接對人報流水帳。只有 NEEDS_INPUT，或 orchestrator 的 tmux 連不上，才升到主工作階段。

角色預設包含 Orchestrator、Builder、Reviewer、Triage、Lab、Sage、Scribe、Foundation、QA、Mirror Integrations。常設任務是這個位子一直負責的事；臨時派遣仍用同一份檢查點合約。文件另把工作分成三條車道：發表、議題與 PR、實驗。實驗車道刻意跟產品車道分開。

### 看板、收件匣、放行

TaskBoard 用 backlog、ready、running、review、blocked、done 看任務走到哪。Reports 和 Inbox 收檢查點、阻塞、交接，以及等人判斷的項目。Greenlight Gate 規定不可逆或對外的動作要人點頭，例如強制推送、合併或關閉 PR、發佈套件、公開貼文。審查員要看 diff、跑測試或建置，給出 APPROVED、CHANGES_REQUESTED 或 BLOCKED，而且不能自己合併。已知故障有一份修復手冊；修不了，或一修就會造成破壞，就升級請人看。

### Conductor、儀表與 Agent View

Conductor 用來下達並拆開一項使命。dashboard 有 mission API 時走那裡；沒有就改走 Workspace 自己的 Swarm，並回傳 `mode: native-swarm`。Dashboard 彙總工作階段、模型、花費，以及需要注意的項目。Agent View 是聊天旁的即時面板：頭像、佇列、歷史、用量。Operations 用另一組人格預設看多個 agent，README 寫的是 Sage、Trader、Builder、Scribe、Ops。這組和 Swarm 角色表不是同一份清單，兩邊怎麼對上，尚未確認。

### HermesWorld 是嵌進來的遊戲

官網把 HermesWorld 寫成用這個 Workspace 做出的例子：一個瀏覽器裡的開放世界，人跟 agent 一起在裡面活動。repo 的 `docs/hermesworld` 說遊戲本體在 hermes-world.ai，Workspace 用 iframe 嵌進去，自己仍是開源殼。技術棧寫到 Three.js。區域、任務、同伴、Sigils 是遊戲用語，文件也寫它還沒做完。這是遊戲畫面，不是這個 repo 的辦公室。

## 主打賣點

- **它加上的是一層網頁指揮台，腦子留在 Hermes Agent。** 聊天、記憶、技能、終端和多工人調度都從瀏覽器操作。agent 仍是 Nous 的 runtime。zero-fork 是為了跟著上游走。
- **Swarm 想被記住的是派工合約。** 角色、SwarmBrief、檢查點、收件匣、Greenlight，把「誰做、做到哪、什麼要人看」寫成固定形狀。多開幾個聊天分頁並沒有這份合約。
- **看板、儀表、Agent View 是同一套 ADE 的不同面板。** 它們讓人在網頁裡比較狀態。這些面板不是平面辦公室，也不是可走進的辦公室。
- HermesWorld、Electron 桌面版、雲端協作是周邊，或還沒做完。README 把桌面版標成開發中，雲端版標成即將推出。現在能用的是 PWA 和 Tailscale：同一個網頁裝到手機上。

## 使用情境

### 已經在跑 Hermes Agent，想換一個畫面

- 適合誰：用終端或訊息管道跟 Hermes Agent 工作，想在瀏覽器一次看到聊天、檔案和記憶的人。
- 在什麼情況使用：gateway 已經在 `:8642`，再把 Workspace 指過去；要完整的工作階段和技能，就一併開 dashboard。
- 帶來的價值：不用改 agent 本體。後端若只是 Ollama 這類相容端點，可以停在 portable mode，進階面板會標成不可用。

### 一件事拆給多個角色，人只看放行

- 適合誰：想讓寫程式、寫文件、審查、實驗各走各的車道，又不想自己當路由器的人。
- 在什麼情況使用：對 orchestrator 說一個結果，它依角色派出 SwarmBrief；審查和對外動作停在 Inbox 與 Greenlight。
- 帶來的價值：交接是檢查點和交付路徑。人在 NEEDS_INPUT 或不可逆動作時才進來。

### 人離開座位，還要看誰卡住

- 適合誰：agent 跑在家裡的機器或伺服器上，人用手機看的人。
- 在什麼情況使用：把網頁裝成 PWA，經 Tailscale 打開。介面若對外聽，要先設密碼。
- 帶來的價值：收件匣和看板還在同一個介面。原生桌面通知和雲端同步，文件寫成還沒做好。

## 我們可以學什麼

- 值得借鑑的 product idea：介面和 runtime 分開。員工是有角色、有 profile、有常設任務的持久工人。工作是一張寫明目標、範圍、交付物和證據的 SwarmBrief。人的位子是放行，不是每一句都回。
- 值得借鑑的 interaction / workflow：人說出結果，Aurora 收成任務單，orchestrator 派到工位，工人用檢查點回報，預設不直接找人。看板是六個狀態。Inbox 只放等人判斷的東西。Greenlight 擋住合併、發佈、公開發言這類出得了門的動作。Lab 跟產品車道隔開。修復手冊先處理已知故障，處理不了再升級。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。每個工人是固定工位，名牌寫角色和常設任務。Orchestrator 坐調度桌，任務單從這裡送出。六欄看板貼在牆上。檢查點紙放進工位旁的文件匣，等人看的才送到人的收件匣。Greenlight 是蓋章桌，合併和對外動作要在這裡停一下。記憶是抽屜裡的 Markdown，技能是架子上的目錄，tmux 是工位上的螢幕。Lab 在隔開的側間。人平時看牆和收件匣，需要證據時才走到那張桌子。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。進門看得到誰在場：調度員在中央，Builder 在自己的位子寫程式，Reviewer 守在放行門前看 diff，Scribe 整理文件，Lab 在側房做實驗，不會把半成品堆到產品座位上。交接是拿著資料夾走到下一張桌子。NEEDS_INPUT 時人被叫到那張桌子；不可逆動作則要人走到蓋章桌。要驗證就站到工位後面，看終端和檢查點。HermesWorld 那種 Three.js 奇幻世界（區域、任務、同伴）是嵌在網頁裡的遊戲，不是這間辦公室。
- 需要重新設計的地方：這個產品是網頁 ADE。聊天、儀表板、工人卡片、看板都是面板。空間辦公室要另做「誰在哪一張桌子」。HermesWorld 用遊戲場景組織工具和進度，不能把它的立體地圖當成我們的 3D 工作空間。Operations 的人格預設和 Swarm 角色表怎麼對應，尚未確認，先不要畫成兩套員工。工人的沙盒、工具開關和終端後端在 Hermes Agent，不在這個 UI；網頁密碼只保護指揮台。官網文案寫從 PyPI 安裝 agent，repo 的 `install.sh` 註解寫它不在 PyPI、改走 Nous 安裝器，以哪一份為準尚未確認。預設模型名稱、技能總數，以及「人不需要手動派工」，都是文件說法，尚未實測。Electron 與雲端版還沒有可以走進去的介面。

## 初步看法

- 最有價值的部分：把 Hermes Agent 留在原處，再用角色、任務單、檢查點和放行門組織一群持久工人。
- 最大限制或疑問：它仍是瀏覽器裡的指揮台。Swarm 是否真的讓工人停在 tmux、檢查點是否帶著證據，要跑過才知道。官網和 repo 的安裝敘事也不一致。
- 是否值得進一步研究或親自體驗：值得，尤其是任務單、收件匣和 Greenlight 在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://hermes-workspace.com
- Repository：https://github.com/outsourc-e/hermes-workspace
- Documentation：repo `docs/swarm`（README、ARCHITECTURE、ROLES）、`docs/hermesworld/FAQ.md`
- License：repo `main` 的 `LICENSE` 為 MIT（Copyright (c) 2026 Eric (outsourc-e)）
- Hermes Agent（另一個產品，不是這個 Cell）：https://github.com/NousResearch/hermes-agent ，文件 https://hermes-agent.nousresearch.com/docs/ ，授權同為 MIT
- README（`main`）：zero-fork、gateway／dashboard 配對、portable mode、Swarm、Conductor、安全預設
- 官網文案與 repo `install.sh` 對「是否從 PyPI 安裝 hermes-agent」的說法不一致，尚未確認
