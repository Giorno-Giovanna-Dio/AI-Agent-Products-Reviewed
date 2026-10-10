# AI Agent Products Reviewed

這個 repository 是建立 2D／3D AI agent workspace 前的產品研究與技術雷達。
目標不是替產品排名，而是理解目前前沿產品如何組織 agents、tasks、context、
execution environments 與 human oversight，並萃取可用於未來 workspace
設計的 interaction 和 system primitives。

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

1. 將候選產品建立為 `cells.yaml` 中的 Cell，狀態設為 `untried`。若來源是
   GitHub repo 或官方網址，可呼叫
   [`/create-cell-pr`](.cursor/skills/create-cell-pr/SKILL.md) 讓 sub-agent
   寫洞察筆記並開獨立 PR。
2. 若有公開原始碼，clone 到獨立實驗區並記錄實際測試的完整 commit SHA；
   否則記錄產品版本。
3. 依照 [`reviews/README.md`](reviews/README.md) 的方法，使用
   [`reviews/_template.md`](reviews/_template.md) 建立評測文件。
4. 只有在關鍵問題無法透過官方文件、source 或 demo 釐清時，才依 upstream
   推薦方式進行 hands-on validation；Docker 並非強制要求。
5. 更新狀態與初步看法，並以一項明確變更建立一個 atomic commit。

候選專案不應直接放在本 repository 之下。若實驗時需要修改產品程式碼，
應另外 fork 該產品；修改提交在產品 fork，評測結果則提交在這裡。

## 狀態

| Cell | Cell ID | Tested revision | Runtime | Status |
| --- | --- | --- | --- | --- |
| [Conductor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/conductor.md) | [https://www.conductor.build/](https://www.conductor.build/) | 未記錄 | macOS native | `tried` |
| [gstack](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gstack.md) | [https://github.com/garrytan/gstack](https://github.com/garrytan/gstack) | 尚未測試 | Agent skills | `untried` |
| [gbrain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gbrain.md) | [https://github.com/garrytan/gbrain](https://github.com/garrytan/gbrain) | 尚未測試 | Agent memory | `untried` |
| [Agent Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/agent-office.md) | [https://github.com/AgentSystemLabs/agent-office](https://github.com/AgentSystemLabs/agent-office) | 尚未測試 | Node + browser 3D office | `untried` |
| [Sandcastle](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/sandcastle.md) | [https://github.com/mattpocock/sandcastle](https://github.com/mattpocock/sandcastle) | 尚未測試 | Docker / TS orchestration | `untried` |
| [mattpocock skills](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/mattpocock-skills.md) | [https://github.com/mattpocock/skills](https://github.com/mattpocock/skills) | 尚未測試 | Agent skills | `untried` |
| [Maestro](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/maestro.md) | [https://github.com/RunMaestro/Maestro](https://github.com/RunMaestro/Maestro) | 尚未測試 | Desktop orchestration | `untried` |
| [Orca](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/orca.md) | [https://github.com/stablyai/orca](https://github.com/stablyai/orca) | 尚未測試 | Cross-platform ADE | `untried` |
| [cmux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cmux.md) | [https://github.com/manaflow-ai/cmux](https://github.com/manaflow-ai/cmux) | 尚未測試 | macOS terminal workspace | `untried` |
| [Emdash](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/emdash.md) | [https://github.com/generalaction/emdash](https://github.com/generalaction/emdash) | 尚未測試 | Desktop ADE | `untried` |
| [Paseo](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/paseo.md) | [https://github.com/getpaseo/paseo](https://github.com/getpaseo/paseo) | 尚未測試 | Self-hosted daemon | `untried` |
| [Nimbalyst](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/nimbalyst.md) | [https://github.com/nimbalyst/nimbalyst](https://github.com/nimbalyst/nimbalyst) | 尚未測試 | Electron desktop | `untried` |
| [Pi](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pi.md) | [https://github.com/earendil-works/pi](https://github.com/earendil-works/pi) | 尚未測試 | Node.js coding-agent CLI | `untried` |
| [llmwiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-compiler-cell-7aa3/reviews/llm-wiki-compiler.md) | [https://github.com/atomicstrata/llm-wiki-compiler](https://github.com/atomicstrata/llm-wiki-compiler) | 尚未測試 | Node CLI / MCP knowledge compiler | `untried` |
| [OpenShell](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openshell-cell-7aa3/reviews/openshell.md) | [https://github.com/NVIDIA/OpenShell](https://github.com/NVIDIA/OpenShell) | 尚未測試 | Policy-enforced sandbox runtime for autonomous age | `untried` |
| [Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-cell-7aa3/reviews/octop.md) | [https://github.com/TencentCloud/Octop](https://github.com/TencentCloud/Octop) | 尚未測試 | Python 3.12+ single process | `untried` |
| [Octop Harness](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-harness-cell-7618/reviews/octop-harness.md) | [https://github.com/TencentCloud/octop-harness](https://github.com/TencentCloud/octop-harness) | 尚未測試 | Python 3.12+ library | `untried` |
| [Odysseus](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-odysseus-cell-7aa3/reviews/odysseus.md) | [https://github.com/odysseus-dev/odysseus](https://github.com/odysseus-dev/odysseus) | 尚未測試 | Native Python/uvicorn also supported | `untried` |
| [Hindsight](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hindsight-cell-7aa3/reviews/hindsight.md) | [https://github.com/vectorize-io/hindsight](https://github.com/vectorize-io/hindsight) | 尚未測試 | Docker or pip hindsight-api | `untried` |
| [Herdr](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-herdr-cell-7aa3/reviews/herdr.md) | [https://github.com/herdrdev/herdr](https://github.com/herdrdev/herdr) | 尚未測試 | Single Rust binary | `untried` |
| [OpenRig](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openrig-cell-7aa3/reviews/openrig.md) | [https://github.com/mvschwarz/openrig](https://github.com/mvschwarz/openrig) | 尚未測試 | Node.js 22 or 24 | `untried` |
| [OpenClaw](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openclaw-cell-7aa3/reviews/openclaw.md) | [https://github.com/openclaw/openclaw](https://github.com/openclaw/openclaw) | 尚未測試 | Node.js Gateway daemon | `untried` |
| [ai-memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ai-memory-cell-7aa3/reviews/ai-memory.md) | [https://github.com/akitaonrails/ai-memory](https://github.com/akitaonrails/ai-memory) | 尚未測試 | Single Rust binary | `untried` |
| [T3 Code](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-t3code-cell-7aa3/reviews/t3code.md) | [https://github.com/pingdotgg/t3code](https://github.com/pingdotgg/t3code) | 尚未測試 | Local server + Electron/desktop/web/mobile clients | `untried` |
| [Paperclip](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-paperclip-cell-7aa3/reviews/paperclip.md) | [https://github.com/paperclipai/paperclip](https://github.com/paperclipai/paperclip) | 尚未測試 | Node.js 24.11+ | `untried` |
| [Deep Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-deepagents-cell-9e0b/reviews/deepagents.md) | [https://github.com/langchain-ai/deepagents](https://github.com/langchain-ai/deepagents) | 尚未測試 | Python harness on LangGraph；終端機 `dcode` | `untried` |
| [Octop Memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-memory-cell-740f/reviews/octop-memory.md) | [https://github.com/TencentCloud/octop-memory](https://github.com/TencentCloud/octop-memory) | 尚未測試 | Python 3.12+ memory runtime | `untried` |
| [Octop Browser](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-browser-cell-6a61/reviews/octop-browser.md) | [https://github.com/TencentCloud/octop-browser](https://github.com/TencentCloud/octop-browser) | 尚未測試 | Python 3.11+ CDP browser | `untried` |
| [LLM Wiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-cell-17ae/reviews/llm-wiki.md) | [https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) | 尚未測試 | Idea gist（貼給 agent） | `untried` |
| [OpenChamber](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openchamber-cell-0717/reviews/openchamber.md) | [https://github.com/openchamber/openchamber](https://github.com/openchamber/openchamber) | 尚未測試 | OpenCode ADE（桌面／Web／VS Code／手機） | `untried` |
| [CrewAI](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-crewai-cell-90bd/reviews/crewai.md) | [https://github.com/crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) | 尚未測試 | Python agent framework | `untried` |
| [OmO](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-openagent-cell-90bd/reviews/oh-my-openagent.md) | [https://github.com/code-yeongyu/oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent) | 尚未測試 | `omo` CLI（senpi）；OpenCode／Codex 外掛 | `untried` |
| [ECC](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ecc-cell-90bd/reviews/ecc.md) | [https://github.com/affaan-m/ECC](https://github.com/affaan-m/ECC) | 尚未測試 | Harness plugin／skills | `untried` |
| [LifeOS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-lifeos-cell-90bd/reviews/lifeos.md) | [https://github.com/danielmiessler/LifeOS](https://github.com/danielmiessler/LifeOS) | 尚未測試 | 裝進既有 coding harness | `untried` |
| [Onlook](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-onlook-cell-90bd/reviews/onlook.md) | [https://github.com/onlook-dev/onlook](https://github.com/onlook-dev/onlook) | 尚未測試 | Next.js web；CodeSandbox sandbox | `untried` |
| [Oh My OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-opencode-cell-103e/reviews/oh-my-opencode.md) | [https://github.com/opensoft/oh-my-opencode](https://github.com/opensoft/oh-my-opencode) | 尚未測試 | OpenCode 外掛（2026-01 快照） | `untried` |
| [OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-opencode-cell-9e0b/reviews/opencode.md) | [https://github.com/anomalyco/opencode](https://github.com/anomalyco/opencode) | 尚未測試 | Terminal TUI + local HTTP server | `untried` |
| [AutoGen](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autogen-cell-9381/reviews/autogen.md) | [https://github.com/microsoft/autogen](https://github.com/microsoft/autogen) | 尚未測試 | Python 3.10+ AgentChat／Core；維護模式 | `untried` |

狀態值：

- `untried`：已收錄，但尚未實際使用。
- `tried`：已經實際體驗過。
