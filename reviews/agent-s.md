# Agent-S

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-s-cell-9381/reviews/agent-s.md)
>
> Cell ID：[https://github.com/simular-ai/Agent-S](https://github.com/simular-ai/Agent-S)
>
> Status：`untried`
>
> Category：Computer-use agent（桌面 GUI）
>
> Last updated：2026-10-10

## 產品介紹

Agent-S 是 Simular 的開源 computer-use agent。人用一句話交代一件桌面工作，它看螢幕，再用滑鼠和鍵盤操作一般的應用程式。它服務的是想研究、或想接上「會自己用電腦的員工」的人。這位員工坐在一部真實桌面前。我們的 2D／3D 工作空間，是人走過去看他、叫停他、核對他做完沒有的場所。

現行套件名稱是 `gui-agents`，`setup.py` 寫版本 `0.3.2`，指令 `agent_s` 進入的是第三代 Agent S3。同一個 repo 仍把 Agent S、Agent S2、Agent S2.5、Agent S3 的程式分目錄留著。官方網站是 [simular.ai](https://www.simular.ai)。託管產品 [Sai](https://www.sai.work/) 和 [Simular API](https://platform.simular.ai/) 是同一家的雲端電腦：人用 HTTP 或 MCP 把任務交出去，自己不必跑這套框架。本次讀的是 `main` 在 2026-10-08 的 `15776a8`（README、LICENSE、論文摘要與現行模組），沒有安裝，也沒有執行。

## 主要 Features

### 看螢幕、計畫、動手

S3 預設把階層拿掉。類別註解寫的是少一次推論。人給一句指令之後，CLI 每一拍做這幾件事：用螢幕截圖當觀察；反思開啟時（預設開），另一個 agent 看目前軌跡，判斷是在原地打轉、可以繼續，或認為已經做完；Worker 依這張圖和反思寫下一步計畫；grounding 把「點哪一個東西」換成座標；接著以 pyautogui 點擊、打字或捲動。Worker 認為做完或放棄就停。CLI 這個迴圈最多 15 步。OSWorld 論文用的步數更長（S3 文中的 100 步設定），和這支 CLI 的上限不同。

Worker 也可以把這一件任務裡要記住的字放進 `notes`，下一拍以文字緩衝讀回來。這是同一件任務裡的便條。跨任務還能翻出來的經驗，留在 S1／S2 的知識庫。

### 世代：階層、專家、再收回成一個人

四代都還在 repo 裡，角色不一樣。

**Agent S（S1）** 論文談的是經驗加強的階層規劃。Manager 看螢幕和指令，必要時查外部網頁知識，把長任務拆成子任務佇列。程式把這份計畫收成有向無環圖，再做拓撲排序。Worker 只做最上面那一棒。這一棒失敗就帶著失敗理由重規劃；做完就換下一棒。整件任務結束後，敘事記憶（narrative memory）留下這趟的高層摘要；情節記憶（episodic memory）留下每一棒的做法。下次規劃和執行會再召回。ACI 把動作收成「點這個元素」這類語言，再對到畫面上的元件。

**Agent S2** 論文稱為 generalist–specialist：規劃、執行、定位拆給不同模組。Mixture of Grounding 把「這句話指的是畫面上哪一點」交給專門的定位模型。Proactive hierarchical planning 是觀察變了就改計畫，不必等到這一步失敗。程式裡仍是 Manager、Worker，以及用 embedding 召回的知識庫。Worker 也可開反思，並使用子任務經驗。

**Agent S2.5** 把經理拿掉。類別註解寫「不用階層，以縮短推論」。剩下 Worker 加反思，軌跡裡只保留有限張螢幕（預設 8 張圖）。

**Agent S3** 沿用這條扁的迴圈，並加上可選的程式碼 agent，以及評測用的 Behavior Best-of-N。`models.md` 的示例仍 import `AgentS2_5`，README 的用法示例是 `AgentS3`。兩份文件對「現在該跑哪一代」的示範尚未對齊。

### 定位的手，和程式碼的手

Worker 說的是一整句「點哪裡」。另一個 grounding 模型看同一張截圖，只回一個座標，再縮放到實際螢幕。README 建議主模型用 OpenAI `gpt-5-2025-08-07`，定位用 UI-TARS-1.5-7B。文字範圍另走 OCR（Tesseract），再讓模型挑出詞的位置。這些是官方建議的搭配，本次未跑。

需要改試算表、大量改檔或跑指令時，Worker 可以呼叫 code agent。對方在背景跑 Python 或 Bash，步數有上限（程式預設 20），結束後交回摘要：做完、放棄，或步數用盡。CLI 的 `--enable_local_env` 預設關閉。打開之後，程式以執行者本人的權限跑，README 寫明這會執行任意程式碼。Bash 有 30 秒逾時。

### 同一件事做幾次，再挑一條

S3 論文的 Behavior Judge 處理的是單次執行容易累積小錯。做法是同一任務跑多條軌跡，先把每條收成行為敘事（做了什麼、畫面因此變成什麼），再由比較者挑選較好的一條。README 寫 CLI 跑的是沒有這一步的 S3。敘事與評判的腳本在 OSWorld 評測目錄，會在截圖上標出滑鼠動作。官方報告 Agent S3 加上這一步在 OSWorld 到 72.60%，並寫超過該基準的人類水準；2026 年 8 月的 Sai 在 OSWorld 2.0 為 73%。這些是論文與官方文章的數字，本次未複驗。

### 人怎麼叫停

S3 的 CLI 用 Ctrl+C 暫停當前流程，Esc 繼續，暫停中再按一次 Ctrl+C 就結束。程式裡另有一個執行前詢問的系統對話框，S1 到 S3 的 CLI 都定義了它，但搜尋不到呼叫點。目前恢復之後，下一手是直接 `exec`。權限等於啟動這個程式的使用者。README 還要求單螢幕。任務結束時，macOS 與 Linux 會彈一個完成對話框。

repo 裡有一份 OpenClaw skill：別的 agent 可以把 GUI 工作交給 Agent-S。那是員工之間的交接，本次未接上。

## 主打賣點

- **它要被記住的是一位會自己看螢幕、點滑鼠、打字的員工。** 應用程式不必先有 API。迴圈是觀察、計畫、行動，再加上反思，或做完之後留下的經驗。
- **世代改的是這位員工怎麼想。** S1 把經驗放進階層規劃。S2 把定位和規劃拆給專家，觀察一變就改計畫。S2.5 與 S3 拿掉經理，讓一個人邊看邊做，旁邊有人提醒他有沒有打轉。S3 再把「做幾次再挑」留在評測流程。
- **工作場所是另一層。** Agent-S 是桌上的那部電腦，以及坐在前面的人。平面辦公室和可走進的辦公室，是主管走過去看他的場所。
- Sai、Simular Cloud、Simular API 是官方的託管包裝：雲端電腦、排程、登入前詢問。那些互動是否如文章所寫，尚未確認。基準分數也尚未確認。

## 使用情境

### 把一件桌面雜事交給會操作 GUI 的員工

- 適合誰：不想為每個應用寫腳本，又希望用自然語言交代桌面工作的人。
- 在什麼情況使用：關閉視窗、在現成軟體裡填表、點選單完成一段流程。
- 帶來的價值：員工直接看螢幕動手。人要在場看他點了什麼，因為他用的是這台電腦上的真實滑鼠。

### 研究長任務為什麼會走歪

- 適合誰：在做 computer-use agent 的研究者。
- 在什麼情況使用：同一題在 OSWorld、WindowsAgentArena 或 AndroidWorld 上跑，比較單次執行和多條軌跡再挑選。
- 帶來的價值：失敗可以對回某一拍的螢幕、計畫和反思。Behavior Best-of-N 則把整段行為收成可比較的敘事。分數是否如論文所寫，尚未確認。

### 另一個 agent 把桌面工作交出來

- 適合誰：已經有 coding agent，但有一段工作只存在 GUI 裡的人。
- 在什麼情況使用：repo 提供的 OpenClaw skill，或官方描述的 Simular API MCP。
- 帶來的價值：桌面操作有一個專門的員工。交接之後，人仍要能走過去看那塊螢幕。MCP 與雲端核准的實際流程尚未確認。

## 我們可以學什麼

- 值得借鑑的 product idea：員工的工作內容是坐在一部電腦前。觀察螢幕、寫下一步、點下去、事後記住，是這位員工的內在迴圈。產品要分開兩件事：他眼前的桌面，以及人與他共處的工作場所。
- 值得借鑑的 interaction / workflow：人監督一位正在用電腦的員工，靠觀看、打斷、驗證。觀看是站到他背後，看到現在的截圖、上一手的座標，以及他剛剛的計畫和反思。打斷是拍肩膀：CLI 的 Ctrl+C 暫停、Esc 放行、再按一次請他離開。驗證有三層。反思 agent 是他自言自語有沒有在打轉。S1／S2 做完把敘事和情節寫進知識庫，下一趟翻出來。S3 的 Behavior Judge 是同一件事做了幾次，人讀行為敘事再留下一條。人要核對的是螢幕上的結果。
- 在 2D workspace 裡會變成什麼：一張俯視的平面辦公室。一位員工坐在固定工位，桌上是他正在使用的那部螢幕，滑鼠停在他剛點的位置，旁邊紙條寫這一步的計畫和反思。人可以在樓層上走過去，站在椅後看那塊螢幕。暫停是人走到桌邊按住他的手；放行之後他才繼續點。若同一任務留了幾次嘗試，桌上並排幾張做完時的螢幕，人比較哪一次。S1／S2 的經驗放在桌下抽屜，分成整件任務的敘事和每一棒的情節。S1／S2 若經理還在，另一張桌子先拆子任務，工作紙再送到這張桌。S3 預設這張桌上只有做事的人。這是工作的地方：人、桌子、螢幕，以及走過去的距離。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。員工坐在工位，桌上螢幕播的是他正在操作的那部桌面。人走進去，繞到椅背後看他點了哪裡。反思判斷軌跡不對時，桌上亮起信標，人聽得到他卡住。拍肩膀是暫停；離開前可以指著螢幕，要他下一手先等。做完後人核對畫面上的檔案或視窗，再讓他下班。多次嘗試時，同一層樓並排幾個工位或幾段錄影，人走過去挑一份留下。S1 的敘事記憶和情節記憶是工位抽屜，下一班還在。重點是看見誰坐在哪、正在看哪一塊螢幕。
- 需要重新設計的地方：同意對話框寫了卻沒接上，所以現在的暫停後繼續會直接動手。權限等於使用者本人，程式碼 agent 打開後也一樣，辦公室裡要另做「這一手要人點頭」和「這台電腦的權限範圍」。CLI 的 15 步和論文的 100 步是不同的工期，空間裡的進度要標明是哪一種。單螢幕是官方前提，多螢幕的員工尚未確認。`models.md` 仍示範 S2.5，和現行 CLI 的 S3 尚未對齊。Sai 在登入前詢問是官方說法，尚未確認。

## 初步看法

- 最有價值的部分：把 computer use 收成一位坐在螢幕前的員工，並留下主管觀看、暫停、核對結果的縫。
- 最大限制或疑問：本次沒有跑。它會真的動到使用者的滑鼠和鍵盤。階層經驗在 S3 預設迴圈裡看不到，Behavior Best-of-N 也不在 CLI 裡。基準數字尚未複驗。
- 是否值得進一步研究或親自體驗：值得，尤其是人站在員工背後時，螢幕、反思、暫停和驗收要怎麼同時被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://www.simular.ai
- Repository：https://github.com/simular-ai/Agent-S
- Documentation：repo `README.md`、`models.md`、`osworld_setup/s3/OSWorld.md`、`integrations/openclaw/README.md`
- License：repo `main` 的 `LICENSE` 為 Apache License 2.0（範本版權欄未填姓名）
- Package：`setup.py` 的 `gui-agents` 版本 `0.3.2`，console script `agent_s` 指向 `gui_agents.s3.cli_app`
- 閱讀版本：`main` @ `15776a8`（2026-10-08，Merge pull request #230）
- Papers：
  - Agent S（ICLR 2025）：https://arxiv.org/abs/2410.08164
  - Agent S2（COLM 2025）：https://arxiv.org/abs/2504.00906
  - Agent S3（README 寫 TMLR 2026；論文標題為 Scaling Agents for Computer Use）：https://arxiv.org/abs/2510.02250
- 官方文章（分數未複驗）：https://www.simular.ai/articles/agent-s3 、https://www.simular.ai/articles/sai-tops-osworld-2-0
- 託管產品：https://www.sai.work/ 、https://platform.simular.ai/
