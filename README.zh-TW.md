<p align="center">
  <img src="assets/banner.jpg" alt="AI Agent Products Reviewed. A public catalog of AI agent products." width="100%">
</p>

<p align="center">
  <a href="README.md">English</a>
  &nbsp;·&nbsp;
  <strong>繁體中文</strong>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-3d3a36" alt="MIT License"></a>
  <a href="CONTRIBUTING.md"><img src="https://img.shields.io/badge/PRs-welcome-3d3a36" alt="PRs welcome"></a>
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/sponsor-GitHub%20Sponsors-ea4aaa?logo=githubsponsors&logoColor=white" alt="GitHub Sponsors"></a>
</p>

<p align="center">
  <a href="#目錄">目錄</a>
  &nbsp;·&nbsp;
  <a href="#歡迎貢獻">貢獻</a>
  &nbsp;·&nbsp;
  <a href="CODE_OF_CONDUCT.md">共事準則</a>
  &nbsp;·&nbsp;
  <a href="#支持這個目錄">支持</a>
</p>

# AI Agent Products Reviewed

這是一個公開的 AI agent products 蒐集樞紐。產品、框架、工作台、記憶層和
runtime 散落在不同的 repository 與官方網站。這裡把它們收成同一份目錄：
每個產品是一個 Cell，看得出它的性質，以及它在一組 AI agent orchestration
team 裡補上的部分。

目錄用來對照，不用來排名。我們用同一套問題閱讀它們：agents、tasks、
context、execution environments 與 human oversight 如何被組織，以及這些
概念放進 2D／3D AI agent workspace 時會變成什麼。

授權是 [MIT](LICENSE)。共事方式見 [共事準則](CODE_OF_CONDUCT.md)。

## 歡迎貢獻

**新來的貢獻者也很歡迎。** 這份目錄要靠大家把散落各地的 AI agent products 補進來。

- 提出一個還沒收錄的產品。
- 寫一篇洞察筆記，並把它放進編排團隊的一個席位。
- 修正已經在目錄裡的 Cell：過時資訊、失效連結，或放錯的席位。

從 [貢獻準則](CONTRIBUTING.md) 開始。一個新產品一個 pull request。還沒實際用過也可以寫，把 status 留在 `untried`。倉庫預設首頁是英文 [README.md](README.md)。新增一列時，中英文各加一次，席位相同。

## 目錄

- [歡迎貢獻](#歡迎貢獻)
- [研究問題](#research-goals)
- [Repository 結構](#repository-結構)
- [Cell](#cell-model)
- [評測流程](#評測流程)
- [在編排團隊裡的位置](#在編排團隊裡的位置)
- [支持這個目錄](#支持這個目錄)

可自行執行的候選專案會 clone 到本 repository 以外的獨立實驗區；這裡只
保存來源資訊、產品與 feature 分析、實際體驗，以及對 2D／3D workspace 的
設計啟示。沒有公開 repository 的產品則記錄官方頁面與可取得的版本資訊。

## Research goals

每個 Cell review 應協助回答：

- 它代表了哪一種新的 agent workspace 或 interaction model？
- 它如何呈現 agents、tasks、branches、sandboxes、artifacts 與進度？
- 使用者如何委派、比較、介入、驗證及收回控制權？
- 哪些能力來自 model，哪些來自 agent runtime 或 orchestration？
- 它在 2D workspace（平面／pixel 風格工作空間）裡會變成什麼？
- 它在 3D workspace（可走進的辦公室介面，不是 3D 物件）裡會變成什麼？
- 哪些 pattern 值得採用、重新設計或明確避免？

主要研究面向：

1. Workspace 與 spatial organization
2. Multi-agent orchestration
3. Context、memory 與 handoff
4. Runtime、sandbox 與 permissions
5. State、progress 與 observability
6. Human-in-the-loop control
7. Artifacts、provenance 與 review
8. Collaboration 與 extensibility

## Repository 結構

- [`README.md`](README.md)：同一份目錄的英文版，也是倉庫預設首頁。
- [`README.zh-TW.md`](README.zh-TW.md)：這份繁體中文目錄。
- [`CONTRIBUTING.md`](CONTRIBUTING.md)：如何提案、新增或修正一個 Cell。
- [`cells.yaml`](cells.yaml)：所有 Cells 的結構化 metadata。
- [`reviews/README.md`](reviews/README.md)：共同評測規則與證據標準。
- [`reviews/`](reviews/)：每個 Cell 的產品洞察（原始 Markdown；**建議用 GitHub 渲染連結在瀏覽器閱讀**，見 [`reviews/README.md`](reviews/README.md)）。
- `/workspace-labs/<cell-name>`：建議的本機實驗位置，不屬於本
  repository，也不會被 Git 追蹤。

## Cell model

一個 **Cell** 是表格中的一個獨立研究單元。Cell 不會保存 upstream 的完整
程式碼；它只連結來源，並保存我們從產品中得到的洞察。

- Repository-backed Cell 的 ID 是 canonical GitHub repository URL，例如
  `https://github.com/mattpocock/sandcastle`。
- 沒有公開 repository 的產品使用官方 canonical URL，例如
  `https://www.conductor.build/`。
- GitHub URL 統一移除 `.git`、query、fragment 與尾端 `/`。
- Repository 改名或轉移後，新的 canonical URL 成為 ID，舊網址放進
  `aliases`。

## 評測流程

1. 將候選產品建立為 `cells.yaml` 中的 Cell，狀態設為 `untried`。一般貢獻依
   [`CONTRIBUTING.md`](CONTRIBUTING.md)。若來源是 GitHub repo 或官方網址，
   也可以呼叫 [`/create-cell-pr`](.cursor/skills/create-cell-pr/SKILL.md)
   讓 sub-agent 寫洞察筆記並開獨立 PR。
2. 若有公開原始碼，clone 到獨立實驗區並記錄實際測試的完整 commit SHA；
   否則記錄產品版本。
3. 依照 [`reviews/README.md`](reviews/README.md) 的方法，使用
   [`reviews/_template.md`](reviews/_template.md) 建立評測文件。
4. 只有在關鍵問題無法透過官方文件、source 或 demo 釐清時，才依 upstream
   推薦方式進行 hands-on validation；Docker 並非強制要求。
5. 更新實測狀態與初步看法，並把這個 Cell 放進下方的編排團隊席位。以一項明確變更建立一個 atomic commit。

候選專案不應直接放在本 repository 之下。若實驗時需要修改產品程式碼，
應另外 fork 該產品；修改提交在產品 fork，評測結果則提交在這裡。

## 在編排團隊裡的位置

這張表分類每個產品的性質，以及它在一組 AI agent orchestration team 裡補上的部分。每個 Cell 只放一個主要席位。旁邊還會碰到的能力，寫在「貢獻」裡。實測狀態寫在各篇筆記，以及 [`cells.yaml`](cells.yaml) 的 `evaluation.status`。

| 席位 | 在團隊裡負責 |
| --- | --- |
| 指揮 | 拆工作、派給對的人、把結果收回來。 |
| 治理 | 管目標、編制、預算、權限，以及沒人盯著時能不能開工。 |
| 工作台 | 讓人同時看見、比較、介入多個 agent。 |
| 在場 | 用空間顯示誰在忙、誰在等、誰做完了。 |
| 員工 | 真正讀寫、操作介面、說話或回覆的那位，以及組裝這位的 runtime。 |
| 記憶 | 讓下一輪、下一位 agent 還用得到先前的上下文。 |
| 方法 | 規定這份工作該怎麼做：技能、程序、完成定義。 |
| 邊界 | 決定在哪裡跑，以及能碰哪些檔案、網路、終端和瀏覽器。 |
| 驗證 | 留下分數、軌跡和可比較的作答，用來判斷這次做得如何。 |
| 交付表面 | 團隊要一起改的那層產出：畫面、文件、表格、簡報。 |

Cell 名稱連到該篇洞察筆記的 GitHub 渲染。Cell ID 寫在筆記開頭，也寫在 [`cells.yaml`](cells.yaml)。

### 指揮

拆工作、派給對的人、把結果收回來。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [Sandcastle](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/sandcastle.md) | 編排函式庫 | 用程式把 coding agent 放進隔離環境，做完再依分支合併 |
| [Octop Harness](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-harness-cell-7618/reviews/octop-harness.md) | 部署 runtime | 在一個 process 裡登記多個彼此隔離的 agent |
| [OpenRig](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openrig-cell-7aa3/reviews/openrig.md) | 團隊 harness | 用 YAML 描述座位與成員，一次啟動並派工 |
| [Deep Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-deepagents-cell-9e0b/reviews/deepagents.md) | 長任務 harness | 把子 agent、虛擬檔案、記憶和人工核准包成一個長任務 runtime |
| [CrewAI](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-crewai-cell-90bd/reviews/crewai.md) | 編排框架 | 用角色和任務組成小隊，外面再用事件流程控分支 |
| [OmO](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-openagent-cell-90bd/reviews/oh-my-openagent.md) | 編排層 | 主 session 拆工派工，暫時工人負責改檔並交回證據 |
| [Oh My OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-opencode-cell-103e/reviews/oh-my-opencode.md) | 多角色外掛 | 把一次開發編成訪談、計畫、派工、實作和查資料 |
| [AgentScope](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentscope-cell-fb52/reviews/agentscope.md) | 編排框架 | 在程式裡組 agent，並讓多個 agent 把工作交來交去 |
| [Gas Town](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gastown.md) | CLI 編排 | 同時調度多個 coding agent，工作狀態寫進可恢復的帳本 |
| [Routa](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/routa.md) | 交付協調台 | 把長聊天拆成任務、看板、筆記和專員契約 |
| [Agency Swarm](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agency-swarm-cell-ad04/reviews/agency-swarm.md) | 編排框架 | 用職位和單向溝通圖決定誰可以派工、誰接手整段對話 |
| [Agent Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-squad-cell-ad04/reviews/agent-squad.md) | 對話路由 | 把每一句話交給最合適的專門 agent，並記住這段聊天 |
| [Amux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-amux-cell-80a8/reviews/amux.md) | 自架控制面 | 給既有 coding agent 一面共用看板、傳話管道和排程 |
| [AutoAgent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autoagent-cell-ca43/reviews/autoagent.md) | 編排框架 | 用自然語言組出專員和工作流程，由分診員分派 |
| [AutoGen](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autogen-cell-9381/reviews/autogen.md) | 編排框架 | 用程式組一群會自己做事、也能和人一起做事的 agent |
| [BeeAI Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-beeai-framework-cell-ad04/reviews/beeai-framework.md) | 編排框架 | 在 Python 或 TypeScript 裡寫會交接的 agent 與流程 |
| [Bernstein](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-bernstein-cell-80a8/reviews/bernstein.md) | 排程編排 | 把一個目標拆給多個 CLI agent，再用排程決定領工、重試和合併 |
| [LangGraph](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langgraph-cell-9381/reviews/langgraph.md) | 圖式 runtime | 用共用狀態、節點和邊編排會跑很久、可中斷再續的流程 |
| [MetaGPT](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-metagpt-cell-9381/reviews/metagpt.md) | 編排框架 | 把多個角色編成一家軟體公司，依 SOP 交出設計和程式 |
| [Microsoft Agent Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-microsoft-agent-framework-cell-9381/reviews/microsoft-agent-framework.md) | 編排框架 | 寫會呼叫工具的 agent，或把多個 agent 串成 workflow |
| [MS-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ms-agent-cell-ad04/reviews/ms-agent.md) | 長任務 harness | 負責規劃、權限、子 agent，以及隔天還能接著做的專案記憶 |
| [OpenAI Agents SDK](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openai-agents-python-cell-9381/reviews/openai-agents-python.md) | agent SDK | 用 agent、handoff 和 guardrail 組多 agent 流程 |
| [PocketFlow](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-cell-ca43/reviews/pocketflow.md) | 圖式框架 | 把一次應用寫成節點、動作和一份共用資料 |
| [Pragma](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pragma-cell-80a8/reviews/pragma.md) | Agent Team 平台 | 把專家、流程、工具、記憶和人要點頭的關卡收成可帶走的團隊 |
| [Youtu-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-youtu-agent-cell-ad04/reviews/youtu-agent.md) | 編排框架 | 用 YAML 組裝並執行 agent，同一份設定還能拿去評測和改進 |

### 治理

管目標、編制、預算、權限，以及沒人盯著時能不能開工。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [Paperclip](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-paperclip-cell-7aa3/reviews/paperclip.md) | 組織控制面 | 用目標、編制、預算和 heartbeat 把外部 agent 當員工喚醒 |
| [Agenta](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agenta-cell-ad04/reviews/agenta.md) | 團隊工作空間 | 讓團隊組出會自己開工的同事，並調整指示、技能和權限 |

### 工作台

讓人同時看見、比較、介入多個 agent。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [Conductor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/conductor.md) | 桌面 ADE | 把多個 coding agent 的 worktree、預覽和合併放進同一個控制台 |
| [Maestro](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/maestro.md) | 桌面 ADE | 用鍵盤優先的控制台同時推進多個專案和任務佇列 |
| [Orca](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/orca.md) | 桌面 ADE | 每個 CLI agent 一個 worktree，同一應用看對話、終端和 diff |
| [cmux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cmux.md) | 終端機工作空間 | 用分頁、分割和「需要你」的通知組織很多 CLI session |
| [Emdash](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/emdash.md) | 桌面 ADE | 以任務為單位跑既有 agent，再在同一個 app 看 diff、CI 和 PR |
| [Paseo](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/paseo.md) | 自架控制面 | daemon 在本機跑既有 CLI，桌面、手機和網頁連回同一台 |
| [Nimbalyst](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/nimbalyst.md) | 視覺工作台 | 人和 agent 改同一批檔案，平行 session 用 worktree 隔開 |
| [Odysseus](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-odysseus-cell-7aa3/reviews/odysseus.md) | 自架個人工作空間 | 把聊天、研究、文件、郵件和待辦收進同一個介面 |
| [T3 Code](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-t3code-cell-7aa3/reviews/t3code.md) | harness 控制面 | 連到本機已登入的 CLI，用同一套 UI 開 thread、看 diff、批核權限 |
| [OpenChamber](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openchamber-cell-0717/reviews/openchamber.md) | OpenCode 工作台 | 在桌面、瀏覽器、VS Code 和手機上監督同一批 OpenCode session |
| [Ekko Studio](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ekko-studio-cell-fb52/reviews/ekko-studio.md) | 工作台與節點流程 | 在單人聊天、群組房間和可執行的節點圖之間切換 |
| [Codeg](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-codeg-cell-fb52/reviews/codeg.md) | 多 agent ADE | 用 ACP 把多個 CLI 收進同一套對話、diff 和權限提示 |
| [Agentrove](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentrove-cell-c2d2/reviews/agentrove.md) | 自架程式工作空間 | 一個 workspace 綁一個 sandbox，用 ACP 啟動已安裝的 agent |
| [cc-haha](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cc-haha-cell-c2d2/reviews/cc-haha.md) | 本機桌面工作台 | 用白話改專案並看 diff，手機和即時通訊連回這台電腦 |
| [iPolloWork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/ipollowork.md) | 多引擎工作台 | 把 OpenCode、Codex 等引擎收成同一條任務、進度和檔案 |
| [Golutra](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/golutra.md) | 終端聊天室 | 把多個本機 CLI 收成頻道成員，輸出回到同一條對話 |
| [Claude Code Bridge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/claude-codex-bridge.md) | CLI 工作台 | 同時看到多個 CLI，也能用訊息把工作在他們之間交出去 |
| [AgentSpace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentspace-cell-80a8/reviews/agentspace.md) | 協作 web workspace | 給人和有崗位的數字員工一個共同的訊息、文件和審批之家 |
| [Buzz](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-buzz-cell-80a8/reviews/buzz.md) | 協作工作空間 | 人和 agent 進同一批頻道、討論串、畫布和工作流程 |
| [Claude Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-claude-squad-cell-80a8/reviews/claude-squad.md) | 終端監督 | 每個 session 自己的 worktree 和 tmux，避免搶同一個目錄 |
| [Free4chat](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-free4chat-cell-fb52/reviews/free4chat.md) | 臨時協作房間 | 用一條連結把瀏覽器裡的人和本機 agent 拉進同一段短時間的工作 |
| [Hermes Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hermes-workspace-cell-ad04/reviews/hermes-workspace.md) | Web 指揮台 | 用瀏覽器看 Hermes 的對話、終端、記憶、技能和多個工人 |
| [Kun](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-kun-cell-ad04/reviews/kun.md) | 本機工作臺 | 在 Code、Design、Work 和 Rooms 裡把目標做成可檢查的交付 |
| [Meldwork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-meldwork-cell-80a8/reviews/meldwork.md) | 桌面 ADE | 把已安裝的 CLI 放進同一個案子，可單人做、多人各答或討論後採用 |
| [Mycelium](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mycelium-cell-ca43/reviews/mycelium.md) | 共用房間 | 人和既有 coding agent 共用聊天、工作板和一份 markdown 記憶 |
| [OpenHands](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openhands-cell-80a8/reviews/openhands.md) | 自架開發控制台 | 把對話、終端、瀏覽器、檔案和自動化畫出來，動作在旁邊的 sandbox 執行 |

### 在場

用空間顯示誰在忙、誰在等、誰做完了。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [Agent Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/agent-office.md) | 3D 辦公室 | 每個 repo 一層樓，走過去看 worker 的終端並一起打字 |
| [Open Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openoffice-cell-c2d2/reviews/openoffice.md) | 2D 像素團隊 | 有名字的成員在同一層地板上計畫、寫程式、審查和預覽 |
| [Pixel Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pixel-agents.md) | 2D 像素辦公室 | 正在跑的 agent 變成樓層上的小人，卡住時頭上冒泡泡 |

### 員工

真正讀寫、操作介面、說話或回覆的那位，以及組裝這位的 runtime。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [Pi](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pi.md) | coding agent runtime | 最小、可嵌入的 coding agent，可走 CLI，也可進別的產品 |
| [Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-cell-7aa3/reviews/octop.md) | 自架助理平台 | 多位使用者各自養專家，並在 Web、桌面和多個 IM 上對話 |
| [OpenClaw](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openclaw-cell-7aa3/reviews/openclaw.md) | 助理 runtime | 長駐 Gateway，從既有聊天 app 動手做 shell、排程和裝置動作 |
| [OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-opencode-cell-9e0b/reviews/opencode.md) | coding agent | 在專案裡讀寫、跑指令，並可再叫專家進來 |
| [Agent-S](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-s-cell-9381/reviews/agent-s.md) | 桌面操作 agent | 看螢幕，用滑鼠和鍵盤完成一般應用程式裡的工作 |
| [Atomic Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-atomic-agents-cell-ca43/reviews/atomic-agents.md) | 零件庫 | 把流程拆成有 schema 的小零件，輸入輸出先檢查再接線 |
| [HelloAgents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-helloagents-cell-ca43/reviews/helloagents.md) | 元件庫 | 用工具註冊表組一輪「提出工具請求、執行、再回到模型」 |
| [LangChain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langchain-cell-ca43/reviews/langchain.md) | agent 框架 | 用模型、工具和一段 prompt 組成會自己呼叫工具的 loop |
| [LiveKit Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-livekit-agents-cell-9381/reviews/livekit-agents.md) | 語音 runtime | 讓一段程式進即時房間，成為會聽、會說、也能看的參與者 |
| [Open-AutoGLM](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-open-autoglm-cell-ca43/reviews/open-autoglm.md) | 手機操作 agent | 用一句話派差事，在接上的手機上打開 App 並做完 |
| [Qwen-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-qwen-agent-cell-9381/reviews/qwen-agent.md) | agent 框架 | 把模型、工具和文件組成一位會串流回覆的 Assistant |
| [TEN Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ten-framework-cell-9381/reviews/ten-framework.md) | 語音 runtime | 用可替換的 extension graph 組一通即時語音對話 |

### 記憶

讓下一輪、下一位 agent 還用得到先前的上下文。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [gbrain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gbrain.md) | 長期記憶 | 把決定、關係和做過的事存成下一輪還查得到的知識 |
| [llmwiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-compiler-cell-7aa3/reviews/llm-wiki-compiler.md) | 知識編譯器 | 把文件和 session 編成可追溯來源的 wiki，之後人和 agent 都查這份 |
| [Hindsight](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hindsight-cell-7aa3/reviews/hindsight.md) | 會學習的記憶 | 把新資訊整理成事實、經驗和心智模型，再 recall、reflect |
| [ai-memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ai-memory-cell-7aa3/reviews/ai-memory.md) | 跨 harness 記憶 | 把多種 coding CLI 的軌跡收進同一份 git 版控 wiki |
| [Octop Memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-memory-cell-740f/reviews/octop-memory.md) | 可搬移記憶 runtime | 抽出事實、召回一段塞得進 prompt 的上下文，並能換宿主 |
| [LLM Wiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-cell-17ae/reviews/llm-wiki.md) | 想法規格 | 貼給自己的 agent，一起長出某個主題的知識庫 |
| [MCP Memory Service](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mcp-memory-service-cell-fb52/reviews/mcp-memory-service.md) | 自架記憶服務 | 把決定、觀察和錯誤留在下一個 session 和其他 agent 都能打開的檔案櫃 |
| [Memori](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memori-cell-fb52/reviews/memori.md) | SQL 記憶層 | 記下這一輪是誰、哪一段工作，下一輪再把相關事實放進上下文 |
| [Memory OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memory-os-cell-fb52/reviews/memory-os.md) | Hermes 記憶層 | 把檔案、對話、事實和 wiki 接在 Hermes 上，呼叫模型前塞進相關舊事 |
| [memsearch](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memsearch-cell-fb52/reviews/memsearch.md) | 專案記憶 | 回合結束寫成 Markdown，需要舊決定時再查少數片段回來 |
| [Cashew](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cashew-cell-ca43/reviews/cashew.md) | 個人思考圖 | 用單一 SQLite 把想法和衍生關係留給已經在跑的 agent |
| [Cognee](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cognee-cell-fb52/reviews/cognee.md) | 知識圖譜記憶 | 把文件、程式和對話整理成可搜尋的圖譜，用問答調出相關的一段 |
| [Daem0nMCP](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-daem0n-mcp-cell-ca43/reviews/daem0n-mcp.md) | 長駐記憶 daemon | 跨 session 送上過去的決定和失敗，改東西之前先擋一下 |
| [Memlayer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memlayer-cell-ca43/reviews/memlayer.md) | 記憶函式庫 | 夾在模型和儲存中間，決定這句話要不要寫下、要不要回頭找 |
| [Memora](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memora-cell-fb52/reviews/memora.md) | MCP 記憶庫 | 把事實、待辦、問題和文件放進同一份庫，開工時依主題取出還有效的內容 |
| [MemPalace Evolve](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mempalace-evolve-cell-ca43/reviews/mempalace-evolve.md) | 本機長期記憶 | 把事實放進一個目錄，換一輪對話再找出來 |
| [PocketFlow Codebase Knowledge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-codebase-tutorial-cell-ca43/reviews/pocketflow-tutorial-codebase-knowledge.md) | 教學流程 | 把一個程式庫編成一份可重讀的 Markdown 教學 |
| [Youtube Made Simple](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-youtube-tutorial-cell-ca43/reviews/pocketflow-tutorial-youtube-made-simple.md) | 教學流程 | 把一支很長的影片收成一頁淺白說明 |

### 方法

規定這份工作該怎麼做：技能、程序、完成定義。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [gstack](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gstack.md) | skills 套件 | 用產品、工程、設計、QA、發佈等角色規定怎麼看問題和交接 |
| [mattpocock skills](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/mattpocock-skills.md) | 工程技能 | 讓 coding agent 對齊需求，並用測試和 review 建立回饋 |
| [ECC](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ecc-cell-90bd/reviews/ecc.md) | 工程程序 | 把 plan、test、implement、review、verify 留在既有 harness 裡 |
| [LifeOS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-lifeos-cell-90bd/reviews/lifeos.md) | 個人 harness | 記住你是誰、在意什麼，以及做完長什麼樣子 |
| [Hello-Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hello-agents-cell-ca43/reviews/hello-agents.md) | 教程 | 用一本書和章節程式說明智能體從原理到多智能體應用怎麼組 |

### 邊界

決定在哪裡跑，以及能碰哪些檔案、網路、終端和瀏覽器。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [OpenShell](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openshell-cell-7aa3/reviews/openshell.md) | policy sandbox | 用政策限制 agent 能碰的檔案、行程、網路和憑證 |
| [Herdr](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-herdr-cell-7aa3/reviews/herdr.md) | 終端 runtime | 保住既有 agent 的 PTY 和版面，讓上層讀得到狀態並隨時接回 |
| [Octop Browser](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-browser-cell-6a61/reviews/octop-browser.md) | 瀏覽器 runtime | 給 agent 一台真的 Chromium，用短代號操作頁面，登入留在本機 |
| [Cloudflare OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cloudflare-os.md) | 權限工作台 | 每個 workspace 預設碰不到外部帳號，要先由人把資源介紹進去 |

### 驗證

留下分數、軌跡和可比較的作答，用來判斷這次做得如何。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [AxisAgentic](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-axisagentic-cell-ca43/reviews/axisagentic.md) | 長程 runtime | 跑會用工具的長任務，並把每次執行寫成可回放的軌跡 |
| [Harbor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-harbor-cell-ad04/reviews/harbor.md) | 評測 harness | 把每次作答的分數和軌跡留下來，方便比較、重評和再最佳化 |

### 交付表面

團隊要一起改的那層產出：畫面、文件、表格、簡報。

| Cell | 性質 | 貢獻 |
| --- | --- | --- |
| [Onlook](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-onlook-cell-90bd/reviews/onlook.md) | 介面畫布 | 在正在跑的畫面上改 React 介面，再寫回程式碼 |
| [Univer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-cell-80a8/reviews/univer.md) | Office runtime | 讓人和 agent 操作同一套試算表、文件和簡報模型 |
| [Univer Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-workspace-cell-80a8/reviews/univer-workspace.md) | 文件協作區 | 人和 agent 一起改表格與文件，由人決定要不要併回正在看的版本 |

實測狀態仍只用兩個值：

- `untried`：已收錄，但尚未實際使用。
- `tried`：已經實際體驗過。

## 支持這個目錄

這份目錄以 MIT 授權公開。維持它有兩種方式。

- 補上還沒收錄的產品，或修正一個已經在目錄裡的 Cell。作法見 [貢獻準則](CONTRIBUTING.md)。
- 用 [GitHub Sponsors](https://github.com/sponsors/Giorno-Giovanna-Dio) 資助維護。倉庫頁的 Sponsor 按鈕讀的是 [`.github/FUNDING.yml`](.github/FUNDING.yml)。

<p align="center">
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/Sponsor-%E2%9D%A4-ea4aaa?style=for-the-badge&logo=githubsponsors&logoColor=white" alt="Sponsor on GitHub"></a>
</p>
