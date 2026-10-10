# Harbor

> **網頁閱讀（建議）**
>
> - [GitHub 渲染版](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-harbor-cell-ad04/reviews/harbor.md)
>
> Cell ID：[https://github.com/harbor-framework/harbor](https://github.com/harbor-framework/harbor)
>
> Status：`untried`
>
> Category：Agent 評測 harness（task／trial／sandbox）
>
> Last updated：2026-10-10

## 產品介紹

Harbor 是 [Terminal-Bench](https://www.tbench.ai) 作者做的開源 Python 框架，用來評測 agent，並把每一次作答的分數和軌跡留成之後可以比較、重評、再拿去最佳化的材料。GitHub 簡介寫的是 evaluating and improving agents。README 寫的是評測與最佳化 agent 和語言模型，並產生給強化學習用的 rollout。先前那句「framework for evaluating and improving agents」指的就是這個評測 harness，不是另一個產品。

它不是容器倉庫 Harbor（常見的 goharbor），也不是桌面上看終端機的 ADE。使用者用 `harbor run` 指定一道題或一整個資料集、一個 agent、一個模型。Harbor 在隔離沙箱裡跑完這一次嘗試，再跑驗收腳本得到分數。題目本身是一個目錄，不依賴 Harbor 才能存在；Harbor 負責把它們成批跑起來。官方網站是 [harborframework.com](https://harborframework.com)，文件在 [docs.harborframework.com](https://docs.harborframework.com)。這個 repo 原先的網址 `laude-institute/harbor` 現在指到 `harbor-framework/harbor`。本次只讀 README、LICENSE、文件與公開原始碼，沒有安裝。

## 主要 Features

### 題目、一次作答、一批評測

一道 **task** 是一個目錄：給 agent 的說明（`instruction.md`）、設定（`task.toml`）、環境規格（常見是 Dockerfile）、驗收腳本，以及可選的參考解答。**Dataset** 是一組題目，通常對應一個基準，例如 Terminal-Bench，或經 adapter 轉進來的 SWE-Bench。Harbor 是 Terminal-Bench 2.0 的官方 harness。

**Trial** 是一個 agent 對一道題的一次嘗試：先啟動沙箱，agent 作答並留下軌跡，驗收再打分，然後沙箱關掉。**Job** 把很多 trial 收成一輪，可以是多個 agent、多個模型、多題、多次嘗試，並行跑。同時幾間、失敗重試、從本機目錄、Git repo 或 Harbor Hub 取題，都寫在這輪的設定裡。預設把各題分數平均，缺分數當 0；資料集可以自帶另一套演算法。

### 沙箱與網路權限

每一個 trial 住在自己的沙箱。命令列仍把沙箱叫做 environment。預設是本機 Docker；要大量並行可以換成 Daytona、Modal 等雲端環境。同一份題目換沙箱時，環境定義還是那一份，但各家做不到的能力會在開跑前被拒絕。

網路可以分成公開、完全斷網、或只允許名單上的主機。安裝 agent 用的是環境的基準網路；agent 真正作答時可以收緊，驗收時可以再不同。文件舉的例子是：安裝時可以上網，作答時只留模型 API。這是為了安全、可重現，以及避免 agent 用額外網路騙分。哪些沙箱做得到動態切換，文件有能力表；本次沒有實測。

### 分數、隔離驗收、重評

驗收入口是 `tests/test.sh`（Windows 題是 `test.bat`）。它要在 `/logs/verifier/reward.txt` 寫一個數字，或在 `reward.json` 寫多個命名分數。兩個都有時，Harbor 採用 JSON。

預設驗收跟 agent 在同一個沙箱，看得到 agent 改過的檔案。題目也可以把驗收放到另一個沙箱：評分程式不跟 agent 見面，只有事先宣告的成品會被送過去。改了評分尺、又不想重跑 agent 時，可以用 regrade：把已經收下的成品放進新的驗收環境再打一次分。這要求更新後的題目使用隔離驗收，而且需要的成品當初都有被收集。

`solution/` 裡的腳本是參考解答。文件寫 oracle agent 會執行它，用來確認這題解得開。沒有解答腳本，oracle 就不能跑。公開基準可以選擇不附解答。

### 軌跡、多步驟、記憶怎麼交

軌跡是作答時的對話與動作。Harbor 用 ATIF 當可交換格式。把舊軌跡灌進新的一次，目前文件寫 claude-code 和 codex 做得到。灌進去的是對話，不是沙箱裡的檔案。

多步驟題把一題拆成連續關卡。沙箱裡的檔案會留到下一步；對話預設每步重開，只有支援續接的 agent 開了 resume 才接著講。某一步分數低於門檻就可以停，不再發下一關。整題分數預設是各步平均，也可以改成只看最後一步。文件寫從指定步驟開始還未提供。

做完之後，`harbor trial handoff` 可以把該次的原生對話帶回本機 agent CLI，讓人追問它為什麼這樣做。文件寫目前只有 claude-code 支援，而且一樣不帶回沙箱檔案。多個 session 的 trial 不支援。

另一種「人」是模擬使用者：一個 user agent 拿著題目，受測 agent 開場看不到題目，要靠對話才知道要做什麼，最後仍由驗收打分。模擬使用者頁寫 ACP bridge 的對象包含 claude-code、gemini-cli、codex、opencode；agent 能力表只列 claude-code 和 gemini-cli。兩邊不一致，確切名單尚未確認。

### 人怎麼看、比較、介入

本機有一個結果瀏覽器：`harbor view` 打開工作清單，點進一輪看每個 trial 的檔案、驗收與設定，並可用方向鍵換 trial、換 job。畫面也能直接開新的一輪。這是結果網頁，不是辦公室。

跑的當下可以加 stream：在瀏覽器裡看新的動作，並即時瀏覽沙箱檔案。文件寫目前是 Claude Code 與 Codex，沙箱是 Daytona、Modal、Smol Machines、Tensorlake 或本機 Docker。這是旁觀。文件沒有寫人可以在 trial 進行中接手打字；那種介入尚未確認。

Harbor Hub 可以發布題目、上傳結果、在雲端開一輪。排行榜是人自己定義欄位並填上分數；連結 trial 不會自動把分數算出來。公開排行榜可以不登入閱讀。建立排行榜需要該資料集組織的 owner。哪些雲端功能要另外付費，尚未確認。

改善 agent 的部分：這個 harness 產出分數和軌跡。官方 cookbook 的 README 另列了用 Harbor 任務做強化學習和 harness 最佳化的範例。那些訓練步驟本次沒有讀完，尚未確認。

## 主打賣點

- **它加上的是「一道可重跑的考題」和「一次可打分的作答」。** 說明、沙箱、驗收、分數、軌跡是分開的。換 agent 或換模型，題目不用重寫。
- **和一般 coding agent 不同。** Claude Code、Codex、OpenCode 是被送進考場的員工。Harbor 是開考場、收卷、打分的一方。它不取代那些 agent。
- **驗收可以跟作答隔離，分數可以重算。** 改評分尺時，不必請 agent 再做一次。
- 大量雲端沙箱、現成基準 adapter、Hub 排行榜，是把同一套考題放大到很多間房間。核心仍是 task、trial、reward。

## 使用情境

### 同一份考卷比較幾個 agent

- 適合誰：要決定團隊用哪個 coding agent、哪個模型的人。
- 在什麼情況使用：用同一資料集開一輪，並行多個 trial，再在結果瀏覽器或排行榜上對分數。
- 帶來的價值：差異留在分數、軌跡和成品上，而不是各開一個聊天視窗憑感覺。

### 寫一道自己的題，並確認題目本身成立

- 適合誰：要為內部工作流做評測的人。
- 在什麼情況使用：寫說明、環境、驗收；先用 oracle 跑參考解答，確定解得開，再送真正的 agent。
- 帶來的價值：考場先被驗證，失敗比較能歸給 agent，而不是歸給壞掉的題目。

### 改評分尺，或事後追問失敗的那一次

- 適合誰：已經有一批跑完的 trial、不想重花模型費用的人。
- 在什麼情況使用：隔離驗收時用 regrade 重打；或對 claude-code 的完成 trial 做 handoff，在本機接著問。
- 帶來的價值：成品和對話可以分開再用。對話接上了，不代表沙箱檔案也還原了。

## 我們可以學什麼

- 值得借鑑的 product idea：員工是被送去考試的 agent；工作是一道帶沙箱和驗收的題；一輪評測是同一層樓裡很多間同時開的考場。分數是門上的結果，軌跡是過程，成品是可以送去另一間評分室的東西。
- 值得借鑑的 interaction / workflow：人委派的是「哪一層考場、哪個 agent、哪份題」，不是逐句下指令。比較是並排看同一題的兩次作答。驗證是驗收員進房，或在隔壁評分室只看交出的成品。介入分成旁觀（stream）、事後追問（handoff）、以及換一把尺重評（regrade）。模擬使用者是考場裡的委託人，不是站在外面的操作者。
- 在 2D workspace 裡會變成什麼：一張俯視的平面樓層。一輪 job 是這一層。每間小房間是一個 trial，名牌寫 agent 和模型，桌上是這題的說明。牆壁是沙箱。網路政策是這間房的門禁：安裝工具時門開著，作答時可以只留一條線給模型 API。Agent 做完離開，驗收的人進來打分，分數寫在門上。隔離驗收是隔壁鎖起來的評分室，只收下事先交出的成品。多步驟是同一間房換下一張考卷，分數不夠就不再發。Oracle 是對照房裡照解答腳本做完的參考員工，用來確認題目成立。人在走廊的分數板上看整層，走進某一間看即時檔案。做完後可以把目前文件允許的那個 agent 請到走廊訪談；房裡的檔案留在房內。載入舊軌跡是把上次的對話筆記放上桌，桌面不會自己回到上次。
- 在 3D workspace 裡會變成什麼：可走進的辦公室，如同 [Agent Office](https://github.com/AgentSystemLabs/agent-office)。大廳牆上是這一輪的分數板。走廊兩側是考場，有人在的房間看得到是誰在哪張桌子做事。你走進去，螢幕上是正在增加的軌跡，架子上是沙箱裡長出來的檔案。驗收員在 agent 離開後進房，或在隔壁評分室只處理送過去的成品。Oracle 在一間對照房。人的工作是委派要開哪些房間、走到兩間做同一題的考場比較、在進行中旁觀、做完後把人請出來追問、以及拿著同一疊成品換一把尺重評。重點是看見誰在哪一間沙箱、做到哪一步、分數是多少。現有的 `harbor view` 是結果網頁；進到這間辦公室時，那些 trial 要變成走得進去的房間。
- 需要重新設計的地方：Harbor 的房間是暫時的考場，跑完預設拆掉。若辦公室要有持久員工，座位不能等於一次 trial。幾千個並行沙箱不能全畫出來，要能依題目、agent、分數挑出要走進去的幾間。文件沒有寫執行中接手打字，旁觀和事後訪談要分成兩種在場。Handoff 和載入軌跡都不帶回檔案，介面不能讓人以為對話接上了、桌面也還原了。Hub 排行榜的分數是人填的，不能畫成系統自動從 trial 算出來。ACP 對象名單文件不一致，模擬使用者的座位先不要做死。這也不是容器倉庫，樓層上不該出現映像檔倉庫的畫面。

## 初步看法

- 最有價值的部分：把一次作答拆成題目、沙箱、軌跡、成品、分數，而且驗收可以隔離、分數可以重算。
- 最大限制或疑問：它服務的是開評測的人。執行中介入很窄，交接只覆蓋少數 agent。Cookbook 裡的最佳化迴圈尚未讀完。
- 是否值得進一步研究或親自體驗：值得，尤其是考場、評分室、分數板在 2D／3D 辦公室裡怎麼被看見。Status 維持 `untried`。

## 後續補充

Not tried yet.

## Sources

- Official website：https://harborframework.com
- Repository：https://github.com/harbor-framework/harbor （舊網址 https://github.com/laude-institute/harbor 現在指到同一 repo）
- Documentation：https://docs.harborframework.com （本次讀了 core concepts、tasks、verifier、artifacts、multi-step、network policies、solution、jobs、stream、handoff、regrade、trajectories、simulate a user、sandboxes、agents、metrics、leaderboards）
- Cookbook：https://github.com/harbor-framework/harbor-cookbook （只讀 README 的範例目錄）
- License：repo `main` 的 `LICENSE` 為 Apache License 2.0
- README（`main`）：評測與最佳化、Terminal-Bench 2.0、並行沙箱、RL rollout
