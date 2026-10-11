<p align="center">
  <img src="assets/banner.jpg" alt="AI Agent Products Reviewed. A public catalog of AI agent products." width="100%">
</p>

<p align="center">
  <strong>English</strong>
  &nbsp;·&nbsp;
  <a href="README.zh-TW.md">繁體中文</a>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-3d3a36" alt="MIT License"></a>
  <a href="CONTRIBUTING.md"><img src="https://img.shields.io/badge/PRs-welcome-3d3a36" alt="PRs welcome"></a>
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/sponsor-GitHub%20Sponsors-ea4aaa?logo=githubsponsors&logoColor=white" alt="GitHub Sponsors"></a>
</p>

<p align="center">
  <a href="#contents">Contents</a>
  &nbsp;·&nbsp;
  <a href="#contributing">Contributing</a>
  &nbsp;·&nbsp;
  <a href="CODE_OF_CONDUCT.md">Code of conduct</a>
  &nbsp;·&nbsp;
  <a href="#support-this-catalog">Support</a>
</p>

# AI Agent Products Reviewed

This is a public hub for AI agent products. Products, frameworks, workbenches,
memory layers, and runtimes are scattered across repositories and official
sites. This catalog gathers them. Each product is a Cell: you can see what
kind of thing it is, and which part it fills in an AI agent orchestration team.

The catalog is for comparison, not ranking. We read each product with the same
questions: how agents, tasks, context, execution environments, and human
oversight are organized, and what those ideas become inside a 2D or 3D AI
agent workspace.

Licensed under [MIT](LICENSE). How we work together is in the
[code of conduct](CODE_OF_CONDUCT.md). The conduct note and the full
contribution guide are in Traditional Chinese.

## Contributing

**Contributions are welcome.** This catalog depends on people adding AI agent
products that are still scattered around.

- Propose a product that is not listed yet.
- Write an insight note and place it in one seat of the orchestration team.
- Correct a Cell that is already listed: outdated facts, broken links, or the wrong seat.

Start with [CONTRIBUTING.md](CONTRIBUTING.md). One new product per pull
request. You can write a note before you have used the product; leave status
at `untried`. When you add a row here, add the same Cell to the Traditional
Chinese [README.zh-TW.md](README.zh-TW.md).

## Contents

- [Contributing](#contributing)
- [Research goals](#research-goals)
- [Repository layout](#repository-layout)
- [Cell model](#cell-model)
- [Review process](#review-process)
- [Where it sits in an orchestration team](#where-it-sits-in-an-orchestration-team)
- [Support this catalog](#support-this-catalog)

Projects you want to run yourself are cloned outside this repository. This
repo keeps the source link, the product and feature analysis, what you
actually tried, and what it suggests for a 2D or 3D workspace. A product
without a public repository is recorded from its official page and whatever
version information is available.

## Research goals

Each Cell review should help answer:

- Which new agent workspace or interaction model does it represent?
- How does it present agents, tasks, branches, sandboxes, artifacts, and progress?
- How does a person delegate, compare, intervene, verify, and take control back?
- Which abilities come from the model, and which come from the agent runtime or orchestration?
- What does it become in a 2D workspace (a flat or pixel workplace)?
- What does it become in a 3D workspace (an office you can walk into, not a 3D object)?
- Which patterns are worth adopting, redesigning, or deliberately avoiding?

Main research angles:

1. Workspace and spatial organization
2. Multi-agent orchestration
3. Context, memory, and handoff
4. Runtime, sandbox, and permissions
5. State, progress, and observability
6. Human-in-the-loop control
7. Artifacts, provenance, and review
8. Collaboration and extensibility

## Repository layout

- [`README.md`](README.md): this English catalog. It is the repository homepage. [`README.zh-TW.md`](README.zh-TW.md) is the Traditional Chinese catalog.
- [`CONTRIBUTING.md`](CONTRIBUTING.md): how to propose, add, or correct a Cell. Written in Traditional Chinese.
- [`cells.yaml`](cells.yaml): structured metadata for every Cell.
- [`reviews/README.md`](reviews/README.md): shared review rules and evidence standard.
- [`reviews/`](reviews/): one insight note per Cell. Read them through the GitHub rendered link in a browser. See [`reviews/README.md`](reviews/README.md).
- `/workspace-labs/<cell-name>`: a suggested local experiment path. It is outside this repository and is not tracked by Git.

## Cell model

A **Cell** is one research unit in the tables. A Cell does not store the
upstream source tree. It links to the source and keeps what we learned from
the product.

- A repository-backed Cell ID is the canonical GitHub repository URL, for example `https://github.com/mattpocock/sandcastle`.
- A product with no public repository uses its official canonical URL, for example `https://www.conductor.build/`.
- Strip `.git`, the query, the fragment, and a trailing `/` from GitHub URLs.
- If a repository is renamed or transferred, the new canonical URL becomes the ID and the old URL goes in `aliases`.

## Review process

1. Add the product as a Cell in `cells.yaml` with status `untried`. Follow [`CONTRIBUTING.md`](CONTRIBUTING.md). If the source is a GitHub repo or an official URL, you can also call [`/create-cell-pr`](.cursor/skills/create-cell-pr/SKILL.md) and let a sub-agent write the note and open its own PR.
2. If the source is public, clone it into a separate experiment area and record the full commit SHA you actually tested. Otherwise record the product version.
3. Follow [`reviews/README.md`](reviews/README.md) and start from [`reviews/_template.md`](reviews/_template.md).
4. Do hands-on validation, the way upstream recommends, only when official docs, source, or a demo cannot answer a key question. Docker is not required.
5. Update the tried/untried status and the early read, and place the Cell in one seat below. One clear change per atomic commit.

Do not put candidate projects inside this repository. If an experiment needs
code changes, fork that product. The fork holds the code changes. The review
stays here.

## Where it sits in an orchestration team

These tables classify what a product is, and which part it fills in an AI
agent orchestration team. Each Cell has one primary seat. Abilities that also
touch a neighboring seat go in Contribution. Whether anyone has tried it is
recorded in the note and in [`cells.yaml`](cells.yaml) under
`evaluation.status`.

| Seat | What it does on the team |
| --- | --- |
| Orchestration | Splits the work, assigns it to the right agent, and brings the result back. |
| Governance | Covers goals, headcount, budget, permissions, and whether work can start while nobody is watching. |
| Workbench | Lets a person see, compare, and step into several agents at once. |
| Presence | Uses space to show who is busy, who is waiting, and who is done. |
| Worker | The one that reads, writes, operates a UI, speaks, or replies, and the runtime that assembles them. |
| Memory | Keeps earlier context available to the next turn and the next agent. |
| Method | Says how this work should be done: skills, procedure, and what done looks like. |
| Boundary | Decides where it runs, and which files, network, terminal, and browser it may touch. |
| Evaluation | Keeps scores, traces, and comparable attempts so you can judge how this run went. |
| Delivery surface | The layer of output the team edits together: screens, documents, sheets, and slides. |

The Cell name links to the GitHub-rendered insight note. The Cell ID is at
the top of the note and in [`cells.yaml`](cells.yaml).

### Orchestration

Splits the work, assigns it to the right agent, and brings the result back.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [Sandcastle](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/sandcastle.md) | orchestration library | Runs a coding agent in an isolated environment from code, then merges by branch strategy. |
| [Octop Harness](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-harness-cell-7618/reviews/octop-harness.md) | deployment runtime | Registers several isolated agents inside one process. |
| [OpenRig](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openrig-cell-7aa3/reviews/openrig.md) | team harness | Describes seats and members in YAML, then starts them and assigns work. |
| [Deep Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-deepagents-cell-9e0b/reviews/deepagents.md) | long-task harness | Packs sub-agents, a virtual filesystem, memory, and human approval into a long-task runtime. |
| [CrewAI](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-crewai-cell-90bd/reviews/crewai.md) | orchestration framework | Builds a crew from roles and tasks, then wraps it in an event flow for branching. |
| [OmO](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-openagent-cell-90bd/reviews/oh-my-openagent.md) | orchestration layer | The main session splits and assigns work; temporary workers edit files and return evidence. |
| [Oh My OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-opencode-cell-103e/reviews/oh-my-opencode.md) | multi-role plugin | Turns one development effort into interview, plan, assignment, implementation, and research. |
| [AgentScope](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentscope-cell-fb52/reviews/agentscope.md) | orchestration framework | Assembles agents in code and lets them hand work to each other. |
| [Gas Town](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gastown.md) | CLI orchestration | Schedules several coding agents at once, with work state kept in a recoverable ledger. |
| [Routa](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/routa.md) | delivery coordination desk | Breaks a long chat into tasks, a board, notes, and specialist contracts. |
| [Agency Swarm](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agency-swarm-cell-ad04/reviews/agency-swarm.md) | orchestration framework | Uses roles and a one-way communication graph to decide who may assign work and who takes over the conversation. |
| [Agent Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-squad-cell-ad04/reviews/agent-squad.md) | conversation router | Routes each utterance to the specialist agent and keeps the chat. |
| [Amux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-amux-cell-80a8/reviews/amux.md) | self-hosted control plane | Gives existing coding agents a shared board, a message channel, and a schedule. |
| [AutoAgent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autoagent-cell-ca43/reviews/autoagent.md) | orchestration framework | Builds specialists and workflows in natural language, then a triage agent assigns the work. |
| [AutoGen](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autogen-cell-9381/reviews/autogen.md) | orchestration framework | Assembles in code a group of agents that can work on their own and with a person. |
| [BeeAI Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-beeai-framework-cell-ad04/reviews/beeai-framework.md) | orchestration framework | Writes agents and flows that hand work off, in Python or TypeScript. |
| [Bernstein](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-bernstein-cell-80a8/reviews/bernstein.md) | scheduled orchestration | Splits one goal across CLI agents, then a scheduler decides who takes work, when to retry, and when to merge. |
| [LangGraph](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langgraph-cell-9381/reviews/langgraph.md) | graph runtime | Orchestrates long, resumable flows with shared state, nodes, and edges. |
| [MetaGPT](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-metagpt-cell-9381/reviews/metagpt.md) | orchestration framework | Organizes roles into a software company that delivers design and code by an SOP. |
| [Microsoft Agent Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-microsoft-agent-framework-cell-9381/reviews/microsoft-agent-framework.md) | orchestration framework | Writes a tool-calling agent, or chains several agents into a workflow. |
| [MS-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ms-agent-cell-ad04/reviews/ms-agent.md) | long-task harness | Handles planning, permissions, sub-agents, and project memory that can resume the next day. |
| [OpenAI Agents SDK](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openai-agents-python-cell-9381/reviews/openai-agents-python.md) | agent SDK | Composes multi-agent flows from agents, handoffs, and guardrails. |
| [PocketFlow](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-cell-ca43/reviews/pocketflow.md) | graph framework | Writes one application as nodes, actions, and a shared store. |
| [Pragma](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pragma-cell-80a8/reviews/pragma.md) | Agent Team platform | Packs specialists, flows, tools, memory, and human checkpoints into a team you can take with you. |
| [Youtu-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-youtu-agent-cell-ad04/reviews/youtu-agent.md) | orchestration framework | Assembles and runs agents from YAML, and can evaluate and improve that same config. |

### Governance

Covers goals, headcount, budget, permissions, and whether work can start while nobody is watching.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [Paperclip](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-paperclip-cell-7aa3/reviews/paperclip.md) | org control plane | Wakes external agents as employees with goals, an org chart, a budget, and a heartbeat. |
| [Agenta](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agenta-cell-ad04/reviews/agenta.md) | team workspace | Lets a team assemble colleagues that start on their own, and tune instructions, skills, and permissions. |

### Workbench

Lets a person see, compare, and step into several agents at once.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [Conductor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/conductor.md) | desktop ADE | Puts worktrees, previews, and merges for several coding agents in one console. |
| [Maestro](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/maestro.md) | desktop ADE | Moves several projects and a task queue forward from a keyboard-first console. |
| [Orca](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/orca.md) | desktop ADE | Gives each CLI agent its own worktree, and shows chat, terminal, and diff in one app. |
| [cmux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cmux.md) | terminal workspace | Organizes many CLI sessions with tabs, splits, and a notification when an agent needs you. |
| [Emdash](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/emdash.md) | desktop ADE | Runs existing agents per task, then shows diff, CI, and the PR in the same app. |
| [Paseo](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/paseo.md) | self-hosted control plane | A daemon runs existing CLIs locally; desktop, phone, and web connect back to that machine. |
| [Nimbalyst](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/nimbalyst.md) | visual workbench | People and agents edit the same files, with parallel sessions isolated in worktrees. |
| [Odysseus](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-odysseus-cell-7aa3/reviews/odysseus.md) | self-hosted personal workspace | Gathers chat, research, documents, mail, and todos into one interface. |
| [T3 Code](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-t3code-cell-7aa3/reviews/t3code.md) | harness control surface | Connects to CLIs already signed in locally, and uses one UI to open threads, read diffs, and approve permissions. |
| [OpenChamber](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openchamber-cell-0717/reviews/openchamber.md) | OpenCode workbench | Supervises the same OpenCode sessions from desktop, browser, VS Code, and phone. |
| [Ekko Studio](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ekko-studio-cell-fb52/reviews/ekko-studio.md) | workbench and node flow | Switches between a solo chat, a group room, and an executable node graph. |
| [Codeg](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-codeg-cell-fb52/reviews/codeg.md) | multi-agent ADE | Uses ACP to gather several CLIs into one chat, diff, and permission prompt. |
| [Agentrove](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentrove-cell-c2d2/reviews/agentrove.md) | self-hosted coding workspace | Binds one workspace to one sandbox and starts installed agents through ACP. |
| [cc-haha](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cc-haha-cell-c2d2/reviews/cc-haha.md) | local desktop workbench | Edits a project in plain language and reads the diff; phone and chat apps connect back to this computer. |
| [iPolloWork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/ipollowork.md) | multi-engine workbench | Folds engines such as OpenCode and Codex into one stream of tasks, progress, and files. |
| [Golutra](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/golutra.md) | terminal chat room | Turns local CLIs into channel members and brings their output back into one conversation. |
| [Claude Code Bridge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/claude-codex-bridge.md) | CLI workbench | Shows several CLIs at once, and lets them hand work to each other by message. |
| [AgentSpace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentspace-cell-80a8/reviews/agentspace.md) | collaborative web workspace | Gives people and role-bound digital employees one home for messages, documents, and approvals. |
| [Buzz](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-buzz-cell-80a8/reviews/buzz.md) | collaborative workspace | Puts people and agents in the same channels, threads, canvases, and workflows. |
| [Claude Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-claude-squad-cell-80a8/reviews/claude-squad.md) | terminal supervisor | Gives each session its own worktree and tmux so they do not share one directory. |
| [Free4chat](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-free4chat-cell-fb52/reviews/free4chat.md) | temporary collaboration room | Uses one link to pull browser participants and local agents into a short stretch of shared work. |
| [Hermes Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hermes-workspace-cell-ad04/reviews/hermes-workspace.md) | web command deck | Uses a browser to watch Hermes chats, terminals, memory, skills, and several workers. |
| [Kun](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-kun-cell-ad04/reviews/kun.md) | local workbench | Turns a goal into a checkable delivery across Code, Design, Work, and Rooms. |
| [Meldwork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-meldwork-cell-80a8/reviews/meldwork.md) | desktop ADE | Puts installed CLIs on one case: one worker, several answers, or a discussion before you adopt a result. |
| [Mycelium](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mycelium-cell-ca43/reviews/mycelium.md) | shared room | Shares a chat, a board, and one markdown memory between a person and the coding agents they already use. |
| [OpenHands](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openhands-cell-80a8/reviews/openhands.md) | self-hosted dev console | Draws chat, terminal, browser, files, and automation; the actions run in a sandbox beside it. |

### Presence

Uses space to show who is busy, who is waiting, and who is done.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [Agent Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/agent-office.md) | 3D office | Gives each repo a floor; walk over, read a worker's terminal, and type with them. |
| [Open Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openoffice-cell-c2d2/reviews/openoffice.md) | 2D pixel team | Named members on one floor plan, write code, review, and preview. |
| [Pixel Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pixel-agents.md) | 2D pixel office | A running agent becomes a figure on the floor, with a bubble when it is stuck. |

### Worker

The one that reads, writes, operates a UI, speaks, or replies, and the runtime that assembles them.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [Pi](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pi.md) | coding agent runtime | A minimal, embeddable coding agent that can run as a CLI or inside another product. |
| [Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-cell-7aa3/reviews/octop.md) | self-hosted assistant platform | Several users each keep their own experts, and talk to them on the web, desktop, and chat apps. |
| [OpenClaw](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openclaw-cell-7aa3/reviews/openclaw.md) | assistant runtime | A long-running Gateway that acts from chat apps you already use: shell, schedules, and device actions. |
| [OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-opencode-cell-9e0b/reviews/opencode.md) | coding agent | Reads, writes, and runs commands in a project, and can call in specialists. |
| [Agent-S](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-s-cell-9381/reviews/agent-s.md) | desktop-use agent | Looks at the screen and finishes work in ordinary apps with mouse and keyboard. |
| [Atomic Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-atomic-agents-cell-ca43/reviews/atomic-agents.md) | parts library | Splits a flow into schema-checked parts, and wires them only after inputs and outputs are validated. |
| [HelloAgents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-helloagents-cell-ca43/reviews/helloagents.md) | component library | Uses a tool registry to run one loop: request a tool, execute it, return to the model. |
| [LangChain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langchain-cell-ca43/reviews/langchain.md) | agent framework | Builds a tool-calling loop from a model, tools, and a prompt. |
| [LiveKit Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-livekit-agents-cell-9381/reviews/livekit-agents.md) | voice runtime | Places a program in a realtime room as a participant that can hear, speak, and see. |
| [Open-AutoGLM](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-open-autoglm-cell-ca43/reviews/open-autoglm.md) | phone-use agent | Sends an errand in one sentence and finishes it in an app on a connected phone. |
| [Qwen-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-qwen-agent-cell-9381/reviews/qwen-agent.md) | agent framework | Composes a model, tools, and documents into an Assistant that streams its reply. |
| [TEN Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ten-framework-cell-9381/reviews/ten-framework.md) | voice runtime | Builds a realtime voice conversation from a swappable extension graph. |

### Memory

Keeps earlier context available to the next turn and the next agent.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [gbrain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gbrain.md) | long-term memory | Stores decisions, relationships, and past work as knowledge the next turn can look up. |
| [llmwiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-compiler-cell-7aa3/reviews/llm-wiki-compiler.md) | knowledge compiler | Compiles documents and sessions into a sourced wiki that people and agents query afterward. |
| [Hindsight](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hindsight-cell-7aa3/reviews/hindsight.md) | learning memory | Turns new information into facts, experience, and mental models, then recalls and reflects. |
| [ai-memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ai-memory-cell-7aa3/reviews/ai-memory.md) | cross-harness memory | Collects traces from many coding CLIs into one git-versioned wiki. |
| [Octop Memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-memory-cell-740f/reviews/octop-memory.md) | portable memory runtime | Extracts facts, recalls a prompt-sized context, and can move that memory to another host. |
| [LLM Wiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-cell-17ae/reviews/llm-wiki.md) | idea spec | Hand it to your own agent and grow a knowledge base for one subject together. |
| [MCP Memory Service](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mcp-memory-service-cell-fb52/reviews/mcp-memory-service.md) | self-hosted memory service | Keeps decisions, observations, and errors in a cabinet the next session and other agents can open. |
| [Memori](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memori-cell-fb52/reviews/memori.md) | SQL memory layer | Records who was in this turn and which piece of work it was, then puts related facts into the next context. |
| [Memory OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memory-os-cell-fb52/reviews/memory-os.md) | Hermes memory layer | Attaches files, chats, facts, and a wiki to Hermes, and inserts relevant history before the model call. |
| [memsearch](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memsearch-cell-fb52/reviews/memsearch.md) | project memory | Writes the turn to Markdown, then looks up a few passages when an old decision is needed. |
| [Cashew](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cashew-cell-ca43/reviews/cashew.md) | personal thought graph | Keeps ideas and the links between them in one SQLite file for an agent that is already running. |
| [Cognee](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cognee-cell-fb52/reviews/cognee.md) | knowledge-graph memory | Turns documents, code, and chats into a searchable graph, and answers with a relevant passage. |
| [Daem0nMCP](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-daem0n-mcp-cell-ca43/reviews/daem0n-mcp.md) | persistent memory daemon | Brings past decisions and failures across sessions, and pauses before something is changed. |
| [Memlayer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memlayer-cell-ca43/reviews/memlayer.md) | memory library | Sits between the model and storage, and decides whether this utterance is written down or looked up. |
| [Memora](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memora-cell-fb52/reviews/memora.md) | MCP memory store | Puts facts, todos, questions, and documents in one store, and pulls what is still valid by topic when work starts. |
| [MemPalace Evolve](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mempalace-evolve-cell-ca43/reviews/mempalace-evolve.md) | local long-term memory | Puts facts in a directory and finds them again in a later conversation. |
| [PocketFlow Codebase Knowledge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-codebase-tutorial-cell-ca43/reviews/pocketflow-tutorial-codebase-knowledge.md) | tutorial pipeline | Compiles a codebase into a Markdown tutorial you can reread. |
| [Youtube Made Simple](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-youtube-tutorial-cell-ca43/reviews/pocketflow-tutorial-youtube-made-simple.md) | tutorial pipeline | Turns a long video into one plain page. |

### Method

Says how this work should be done: skills, procedure, and what done looks like.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [gstack](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gstack.md) | skills pack | Uses product, engineering, design, QA, and release roles to say how to look at a problem and hand it off. |
| [mattpocock skills](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/mattpocock-skills.md) | engineering skills | Aligns a coding agent with the requirements, and builds feedback from tests and review. |
| [ECC](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ecc-cell-90bd/reviews/ecc.md) | engineering procedure | Keeps plan, test, implement, review, and verify inside the harness you already use. |
| [LifeOS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-lifeos-cell-90bd/reviews/lifeos.md) | personal harness | Remembers who you are, what you care about, and what done looks like. |
| [Hello-Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hello-agents-cell-ca43/reviews/hello-agents.md) | tutorial | Explains, in a book and chapter code, how to build agents from first principles through multi-agent apps. |

### Boundary

Decides where it runs, and which files, network, terminal, and browser it may touch.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [OpenShell](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openshell-cell-7aa3/reviews/openshell.md) | policy sandbox | Uses policy to limit the files, processes, network, and credentials an agent may touch. |
| [Herdr](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-herdr-cell-7aa3/reviews/herdr.md) | terminal runtime | Keeps the PTY and layout of an existing agent alive so a layer above can read state and attach again. |
| [Octop Browser](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-browser-cell-6a61/reviews/octop-browser.md) | browser runtime | Gives an agent a real Chromium, operates the page by short handles, and keeps logins on the machine. |
| [Cloudflare OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cloudflare-os.md) | permission workbench | A workspace cannot touch external accounts until a person introduces the resource. |

### Evaluation

Keeps scores, traces, and comparable attempts so you can judge how this run went.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [AxisAgentic](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-axisagentic-cell-ca43/reviews/axisagentic.md) | long-horizon runtime | Runs long tool-using tasks and writes each run as a replayable trace. |
| [Harbor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-harbor-cell-ad04/reviews/harbor.md) | evaluation harness | Keeps the score and trace of each attempt so they can be compared, regraded, and optimized. |

### Delivery surface

The layer of output the team edits together: screens, documents, sheets, and slides.

| Cell | Nature | Contribution |
| --- | --- | --- |
| [Onlook](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-onlook-cell-90bd/reviews/onlook.md) | interface canvas | Edits a React interface on the running screen, then writes the change back to code. |
| [Univer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-cell-80a8/reviews/univer.md) | Office runtime | Lets people and agents operate the same spreadsheet, document, and slide model. |
| [Univer Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-workspace-cell-80a8/reviews/univer-workspace.md) | document workspace | People and agents edit sheets and documents together; a person decides whether to merge back into the version everyone is looking at. |

Hands-on status has two values:

- `untried`: catalogued, not actually used yet.
- `tried`: someone has used it.

## Support this catalog

This catalog is public under the MIT license. Two ways to keep it going:

- Add a product that is not listed, or correct a Cell that is. See [CONTRIBUTING.md](CONTRIBUTING.md).
- Fund maintenance with [GitHub Sponsors](https://github.com/sponsors/Giorno-Giovanna-Dio). The Sponsor button on the repository page reads [`.github/FUNDING.yml`](.github/FUNDING.yml).

<p align="center">
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/Sponsor-%E2%9D%A4-ea4aaa?style=for-the-badge&logo=githubsponsors&logoColor=white" alt="Sponsor on GitHub"></a>
</p>
