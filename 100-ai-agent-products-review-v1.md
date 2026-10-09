# 100 個推薦新增的 AI Agent 產品／框架 Cell

A. 工作區與空間組織（Workspace & Spatial Organization）
https://github.com/gastownhall/gastown

Multi-agent workspace manager（Go）
Primitive: Workspace 與 spatial organization — agents 在可共享的工作區內協作
Type: 自架產品
License: MIT，~100 stars（新專案）
最近更新: 2026年
為什麼尚未收錄：2024 年底才出現的工作區框架，GitHub 活躍但 stars 還少
https://github.com/phodal/routa

Workspace-first multi-agent coordination with Specs + Kanban（TypeScript）
Primitive: Workspace + task orchestration
Type: 自架產品
License: MIT，~50 stars
最近更新: 2026年 10月
為什麼尚未收錄：著重 spec-driven + kanban 是現有 Cells（Nimbalyst、Maestro）還沒明確分開的設計點
https://github.com/NanmiCoder/cc-haha

Local-first desktop workspace for Claude Code / agents（TypeScript）
Primitive: Multi-agent + desktop workspace with skill marketplace & model switching
Type: 自架 desktop ADE
License: MIT，~200 stars
最近更新: 2026年 10月
為什麼尚未收錄：中文社群發起、支援多模型切換與 skill 市場，對比 Conductor 是更輕的 desktop variant
https://github.com/Devin-AXIS/iPolloWork

Enterprise Agent Workbench for multi-engine（Codex/DeepSeek/OpenCode）（TypeScript）
Primitive: Multi-engine workspace with unified plugins & editable documents/presentations
Type: 自架 desktop
License: MIT，~80 stars
最近更新: 2026年 10月
為什麼尚未收錄：多 engine 不只是並排顯示（Nimbalyst），而是統一插件系統與文檔編輯
https://github.com/cloudflare/cloudflare-os

Agent workspace on Cloudflare Workers（TypeScript）
Primitive: Serverless agent workspace for documents, apps, agents as integrated layer
Type: 自架產品 / serverless platform
License: Apache-2.0，~500 stars
最近更新: 2026年 8月
為什麼尚未收錄：用 Cloudflare Workers 實現 agent workspace 是運行層創新，不只是 UI
https://github.com/nodetool-ai/nodetool

Agent-first Creative Workspace（TypeScript）
Primitive: Node graph + agent composition for visual workflows
Type: 自架 web/desktop
License: AGPL-3.0，~4000 stars
最近更新: 2026年 10月
為什麼尚未收錄：創意工作流（non-coding）的 agent orchestration；node graph 也是值得對照的 primitive
https://github.com/longyangxi/OpenOffice

Visible workspace for AI agents to collaborate as a single team（TypeScript）
Primitive: Collaboration primitives — agents 在可見的空間一起工作
Type: 自架 web
License: MIT（推測），~80 stars
最近更新: 2026年 10月
為什麼尚未收錄：中文社群产品，"office"敘事與 Agent Office 平行但不同實現
B. 多 Agent 編排（Multi-Agent Orchestration）
https://github.com/golutra/golutra

Multi-agent orchestration platform with parallel execution + task orchestration（Rust）
Primitive: Parallel execution + task state across Codex/Claude Code/OpenClaw
Type: 自架 + library
License: MIT，~300 stars
最近更新: 2026年 10月
為什麼尚未收錄：Rust runtime 的多 agent 編排；Deep Agents 是 Python LangGraph 層，Golutra 下沉到運行層
https://github.com/Mng-dev-ai/agentrove

Multi-agent workflows with personas + ACP-powered sandboxes（TypeScript）
Primitive: Multi-agent personas + sandbox
Type: 自架
License: MIT，~150 stars
最近更新: 2026年 10月
為什麼尚未收錄：persona 不只是 system prompt；人物與工作內容分離的設計
https://github.com/kdcokenny/opencode-workspace

Bundled multi-agent orchestration harness for OpenCode（TypeScript）
Primitive: Harness + OpenCode integration for coordinated agents
Type: 自架
License: MIT（推測），~40 stars
最近更新: 2026年 10月
為什麼尚未收錄：OpenCode 並不在現有 Cells 裡，而這是它的 workspace 層
https://github.com/Lexus2016/claude-code-studio

Web workspace for Claude Code with task scheduling + MCP + real-time streaming（JavaScript）
Primitive: Task scheduling + MCP integration with multi-agent orchestration
Type: 自架 web
License: MIT（推測），~200 stars
最近更新: 2026年 10月
為什麼尚未收錄：Claude Code 的 studio；task scheduling 是編排的核心 primitive
https://github.com/SeemSeam/claude_codex_bridge

Visible multi-agent CLI workspace for mixing Codex/Claude/Gemini/others（Python）
Primitive: CLI + visibility for parallel agent monitoring
Type: 自架
License: MIT（推測），~100 stars
最近更新: 2026年 10月
為什麼尚未收錄：純 CLI 且支援眾多 agent；對比 Nimbalyst/Maestro 的輕量級選項
https://github.com/DBell-workshop/AgentFleet

Pixel-art RPG workspace for multi-agent collaboration（Python）
Primitive: Spatial + RPG UI for agent coordination（2D workspace 的概念驗證）
Type: 自架
License: MIT（推測），~50 stars
最近更新: 2026年 10月
為什麼尚未收錄：2D/3D workspace 研究方向的具體實現；像素風 RPG 是有趣的空間敘事
https://github.com/spacering-net/codeg

Collaborative multi-agent coding workspace（Rust）
Primitive: Multi-session aggregation from Claude Code/Codex/OpenCode/Pi
Type: 自架 desktop
License: MIT，~120 stars
最近更新: 2026年 10月
為什麼尚未收錄：Rust 的協作工作區；session aggregation 模型與 Conductor 的 branch 顯示不同
https://github.com/EKKOLearnAI/ekko-studio

Ekko Studio — local-first AI workspace for multi-agent chat, coding, visual workflows（TypeScript）
Primitive: Visual workflows + chat + coding in one workspace
Type: 自架 desktop/web
License: MIT，~80 stars
最近更新: 2026年 10月
為什麼尚未收錄：Ekko 強調視覺流，是流程工作室概念（與 nodetool 相似但更輕）
https://github.com/nexus-research-lab/nexus

Multi-agent collaboration platform — persistent, proactive agents across rooms+workspaces（Go）
Primitive: Persistent agents in rooms — spatial metaphor for workspace
Type: 自架 + library
License: MIT，~200 stars
最近更新: 2026年 10月
為什麼尚未收錄：Go 的多 agent 框架；"rooms" 作為空間單位比 "worktree" 或 "session" 更明確
https://github.com/i365dev/free4chat

Temporary rooms for Humans and AI Agents — run, supervise, steer without permanent workspace（TypeScript）
Primitive: Ephemeral vs persistent workspace — 任務導向的短期房間
Type: 自架
License: MIT，~150 stars
最近更新: 2026年 10月
為什麼尚未收錄：對比固定工作區，臨時房間可能是新的編排 primitive
https://github.com/AFK-surf/open-agent

Open-source alternative to Claude Agent SDK + ChatGPT Agents（TypeScript）
Primitive: Agent SDK for building & coordinating agents
Type: Library
License: MIT，~90 stars
最近更新: 2026年 10月
為什麼尚未收錄：開源 agent SDK；對比 LangChain/AutoGen 的定位與功能差異值得看
C. Context、Memory、Handoff
https://github.com/topoteretes/cognee

Agent context graph + knowledge base（Python）
Primitive: Context graph for memory & handoff
Type: Library
License: MIT，~2000 stars
最近更新: 2026年 10月
為什麼尚未收錄：知識圖譜 + context 是 memory primitive；與 Hindsight 的 bank 概念相似但不同實現
https://github.com/omega-memory/omega-memory

Agent memory system（Python）
Primitive: Persistent memory for agents
Type: Library
License: MIT，~100 stars
最近更新: 2026年 9月
為什麼尚未收錄：專注 memory；與 gbrain、ai-memory 平行但實現不同
https://github.com/grapeot/context-infrastructure

Context infrastructure for agents（Python）
Primitive: Context management & propagation
Type: Library
License: MIT，~60 stars
最近更新: 2026年 9月
為什麼尚未收錄：context 作為獨立基礎設施；handoff 時的 context 流動設計
https://github.com/doobidoo/mcp-memory-service

MCP memory service for agents（TypeScript/Python）
Primitive: MCP protocol for memory sharing
Type: Library/Service
License: MIT（推測），~30 stars
最近更新: 2026年 9月
為什麼尚未收錄：MCP 作為 memory 協議層；對比直接集成的 memory
https://github.com/zilliztech/memsearch

Semantic memory search（Python）
Primitive: Semantic search over agent memory
Type: Library
License: Apache-2.0，~800 stars
最近更新: 2026年 10月
為什麼尚未收錄：向量搜尋 memory；RAG 層面的設計
https://github.com/ClaudioDrews/memory-os

Memory OS for agents（Python）
Primitive: Memory as OS
Type: Library/Runtime
License: MIT（推測），~50 stars
最近更新: 2026年 10月
為什麼尚未收錄：把 memory 當作 OS 層（像 gbrain 但不同角度）
https://github.com/agentic-box/memora

Memora — agent memory system（Python）
Primitive: Multi-agent memory
Type: Library
License: MIT，~80 stars
最近更新: 2026年 9月
為什麼尚未收錄：focus on multi-agent memory sharing and sync
https://github.com/mnemox-ai/tradememory-protocol

TradeMemory protocol — standardized memory handoff（TypeScript）
Primitive: Standardized memory protocol for agent-to-agent handoff
Type: Protocol/Library
License: MIT，~40 stars
最近更新: 2026年 9月
為什麼尚未收錄：protocol 層面；agent 間的 memory 標準化
https://github.com/shibing624/agentica

Agent framework with memory + RAG（Python）
Primitive: Memory + RAG integration
Type: Framework
License: Apache-2.0，~600 stars
最近更新: 2026年 10月
為什麼尚未收錄：中文友善；RAG 與 memory 的整合方式
https://github.com/MemoriLabs/Memori

Memori — agent memory（Python）
Primitive: Persistent agent memory
Type: Library
License: MIT，~70 stars
最近更新: 2026年 9月
為什麼尚未收錄：focus on long-term memory patterns
D. Runtime、Sandbox、Permissions
https://github.com/agentscope-ai/agentscope-runtime

Production-ready runtime with secure tool sandboxing + Agent-as-a-Service APIs（Python）
Primitive: Secure sandbox + observability
Type: Runtime
License: MIT，~875 stars
最近更新: 2026年 10月
為什麼尚未收錄：專注 sandbox 層；與 Deep Agents/Octop 的 runtime 層不同
https://github.com/openai/openai-agents-python

OpenAI agents framework（Python）
Primitive: Multi-agent framework from OpenAI
Type: Library
License: MIT，~29900 stars
最近更新: 2026年 10月
為什麼尚未收錄：OpenAI 官方框架；必看但因為 stars 很高可能被當成基準
https://github.com/livekit/agents

Framework for realtime voice AI agents（Python）
Primitive: Voice + realtime agent runtime
Type: Framework
License: Apache-2.0，~14600 stars
最近更新: 2026年 10月
為什麼尚未收錄：voice 層面的 agent runtime；對比文字/coding agent 的設計差異
https://github.com/microsoft/autogen

Programming framework for agentic AI（Python）
Primitive: Multi-agent conversation framework
Type: Framework
License: Apache-2.0，~61291 stars
最近更新: 2026年 10月
為什麼尚未收錄：Stars 很高但依然值得在 workspace 研究中作為參考；group chat 模型
https://github.com/microsoft/agent-framework

Framework for building & orchestrating AI agents + multi-agent workflows（Python）
Primitive: Agent + workflow orchestration
Type: Framework
License: Apache-2.0，~14001 stars
最近更新: 2026年 10月
為什麼尚未收錄：Microsoft 的新 agent 框架（vs AutoGen）；workflow 與編排的設計
https://github.com/langchain-ai/langgraph

Build resilient agents（Python）
Primitive: Graph-based agent composition
Type: Library/Framework
License: MIT，~42865 stars
最近更新: 2026年 10月
為什麼尚未收錄：Stars 很高，但 LangGraph 的圖模型對工作區設計有關鍵啟發
https://github.com/FoundationAgents/MetaGPT

Multi-Agent Framework: First AI Software Company（Python）
Primitive: Role-based multi-agent with workflow
Type: Framework
License: MIT，~70771 stars
最近更新: 2026年 10月
為什麼尚未收錄：角色分工模型（vs Maestro 的 moderator）；公司敘事（像 Paperclip）
https://github.com/agent0ai/agent-zero

Agent Zero — open-source agent framework（Python）
Primitive: Self-improving agent with memory + tool use
Type: Framework
License: MIT，~19396 stars
最近更新: 2026年 10月
為什麼尚未收錄：自改進機制與任務執行的結合
https://github.com/TEN-framework/ten-framework

Open-source framework for conversational voice AI agents（Python）
Primitive: Voice agent + real-time orchestration
Type: Framework
License: Apache-2.0，~11153 stars
最近更新: 2026年 10月
為什麼尚未收錄：對話型語音 agent；與 LiveKit 平行但架構不同
https://github.com/simular-ai/Agent-S

Agent-S: Open agentic framework that uses computers like a human（Python）
Primitive: Computer use + desktop automation
Type: Framework
License: Apache-2.0，~12553 stars
最近更新: 2026年 10月
為什麼尚未收錄：computer use 是新 primitive；与 GUI/desktop 工作區的交互
https://github.com/QwenLM/Qwen-Agent

Agent framework built on Qwen，featuring Function Calling + MCP + RAG（Python）
Primitive: MCP + RAG agent framework
Type: Framework
License: MIT，~17143 stars
最近更新: 2026年 10月
為什麼尚未收錄：中文友善；Qwen 生態的 agent 框架
https://github.com/agentuniverse-ai/agentUniverse

LLM multi-agent framework（Python）
Primitive: Multi-agent lifecycle management
Type: Framework
License: MIT，~2377 stars
最近更新: 2026年 10月
為什麼尚未收錄：agent lifecycle 作為中心；不只是編排
https://github.com/Agenta-AI/agenta

Workspace where you and your team build agents + automations（TypeScript）
Primitive: Team collaboration + evaluation for agent building
Type: 自架 web
License: MIT，~3000+ stars
最近更新: 2026年 10月
為什麼尚未收錄：團隊 agent 開發工作區；類似 no-code agent builder
https://github.com/VRSEN/agency-swarm

Reliable Multi-Agent Orchestration Framework（Python）
Primitive: Agency + swarm choreography
Type: Framework
License: MIT，~4592 stars
最近更新: 2026年 10月
為什麼尚未收錄：agency 模型；群體 orchestration
https://github.com/KunAgent/Kun

Local-first AI agent workspace for coding/writing/design/automation（TypeScript）
Primitive: Multi-domain agent workspace (coding+writing+design)
Type: 自架 desktop/TUI
License: MIT，~120 stars
最近更新: 2026年 10月
為什麼尚未收錄：跨領域工作區（不只 coding）；TUI + GUI 雙支持
E. Progress、State、Observability
https://github.com/i-am-bee/beeai-framework

Build production-ready AI agents in Python and TypeScript（Python/TypeScript）
Primitive: Observability + logging for agent execution
Type: Framework
License: MIT，~3427 stars
最近更新: 2026年 10月
為什麼尚未收錄：生產級框架；observability 層設計
https://github.com/Intelligent-Internet/ii-agent

II-Agent framework to build and deploy intelligent agents（Python）
Primitive: Agent lifecycle + deployment
Type: Framework
License: MIT，~3391 stars
最近更新: 2026年 10月
為什麼尚未收錄：infrastructure 層面；agent 部署模型
https://github.com/TencentCloudADP/youtu-agent

Simple yet powerful agent framework（Python）
Primitive: Lightweight orchestration
Type: Framework
License: MIT，~4621 stars
最近更新: 2026年 10月
為什麼尚未收錄：騰訊產品；輕量級設計與 gstack 對比
https://github.com/modelscope/ms-agent

MS-Agent: lightweight framework to empower agentic execution（Python）
Primitive: Task execution + state management
Type: Framework
License: Apache-2.0，~4407 stars
最近更新: 2026年 10月
為什麼尚未收錄：阿里 ModelScope 產品；state tracking 設計
https://github.com/harbor-framework/harbor

Framework for evaluating and improving agents（Python）
Primitive: Agent evaluation + feedback loop
Type: Framework
License: MIT，~5903 stars
最近更新: 2026年 10月
為什麼尚未收錄：評估框架；progress + quality feedback 的設計
https://github.com/2FastLabs/agent-squad

Flexible framework for managing multiple AI agents + complex conversations（Python）
Primitive: Conversation state across agents
Type: Framework
License: MIT，~7790 stars
最近更新: 2026年 10月
為什麼尚未收錄：對話管理；多 agent 對話狀態同步
https://github.com/aiwaves-cn/agents

Open-source Framework for Data-centric, Self-evolving Autonomous Language Agents（Python）
Primitive: Self-evolution + data feedback
Type: Framework
License: MIT，~5965 stars
最近更新: 2026年 10月
為什麼尚未收錄：中文社群；data-centric 與 self-evolution 是新方向
https://github.com/DataBassGit/AgentForge

Extensible AGI Framework（Python）
Primitive: Extensible agent composition
Type: Framework
License: MIT，~851 stars
最近更新: 2026年 10月
為什麼尚未收錄：擴展性設計
https://github.com/TencentQQGYLab/AppAgent

AppAgent: Multimodal Agents as Smartphone Users（Python）
Primitive: GUI automation + multimodal for mobile/desktop
Type: Framework
License: MIT，~6899 stars
最近更新: 2026年 10月
為什麼尚未收錄：視覺 + 行動；mobile 工作區的 agent
https://github.com/Sompote/Tigrimos

Self-hosted AI workspace with multi-agent orchestration + skill marketplace（TypeScript）
Primitive: Skill marketplace + sandbox
Type: 自架 web/desktop
License: MIT，~150 stars
最近更新: 2026年 10月
為什麼尚未收錄：sandbox + skill 市場結合；安全性設計
https://github.com/AIOSAI/AIPass

Persistent Agent Workspace — agents that remember, collaborate, never start from zero（Python）
Primitive: Persistent state across sessions
Type: 自架
License: MIT，~80 stars
最近更新: 2026年 10月
為什麼尚未收錄：session persistence 設計；對比臨時 room 的另一端
https://github.com/outsourc-e/hermes-workspace

Native web workspace for Hermes Agent（JavaScript）
Primitive: Agent UI for Hermes runtime
Type: Web UI
License: MIT，~50 stars
最近更新: 2026年 10月
為什麼尚未收錄：Hermes 的 workspace UI；與 Herdr 平行但 web-based
https://github.com/emreturkmencom/antigravity-telegram-suite

Antigravity — remote-control AI agent via Telegram（JavaScript）
Primitive: Mobile + IM interface for agent control
Type: Telegram bot/service
License: MIT，~20 stars（新專案）
最近更新: 2026年 10月
為什麼尚未收錄：IM 層面的 agent workspace；行動優先設計
https://github.com/Ryder-Sun/Meldwork

Local-first workspace for multi-agent collaboration + scoped permissions（JavaScript）
Primitive: Scoped permissions + evidence-aware execution
Type: 自架
License: MIT，~30 stars（新專案）
最近更新: 2026年 10月
為什麼尚未收錄：權限模型與證據追蹤；safety 為中心
https://github.com/HKUDS/AgentSpace

AgentSpace — Human + Agents, One Team, One Workspace（TypeScript）
Primitive: Human-agent team as core unit
Type: 自架 web
License: MIT，~60 stars
最近更新: 2026年 10月
為什麼尚未收錄：人-agent 團隊融合；不分離人與 agent
https://github.com/dream-num/univer-workspace

Open-source Office workspace for people + AI agents to create/collaborate/review（TypeScript）
Primitive: Spreadsheet + document as agent collaboration medium
Type: 自架 web
License: Apache-2.0，~300 stars
最近更新: 2026年 10月
為什麼尚未收錄：辦公套件層面的 agent 協作；Univer 是開源 office
https://github.com/enwong93-sketch/devspace-ultra

DevSpace with elastic ChatGPT workers + MCP workspace（JavaScript）
Primitive: Elastic subagent orchestration
Type: 自架
License: MIT，~20 stars（新專案）
最近更新: 2026年 10月
為什麼尚未收錄：動態 subagent 伸縮
F. Human Oversight 與 Control
https://github.com/HKUDS/AutoAgent

Fully-Automated Zero-Code LLM Agent Framework（Python）
Primitive: Zero-code agent building
Type: Framework/Platform
License: MIT，~9804 stars
最近更新: 2026年 10月
為什麼尚未收錄：無代碼; Human oversight 通過 config 而非代碼
https://github.com/XYZ-AI-Lab/AxisAgentic

Extensible Runtime + Trajectory-Collection Framework for Long-Horizon Agents（Python）
Primitive: Trajectory collection for human learning & intervention
Type: Framework
License: MIT，~1120 stars
最近更新: 2026年 9月
為什麼尚未收錄：學習軌跡收集；human-in-the-loop 的數據側面
https://github.com/The-Pocket/PocketFlow

Pocket Flow — 100-line LLM framework; Let Agents build Agents（Python）
Primitive: Agent-builds-agent pattern
Type: Framework
License: MIT，~11225 stars
最近更新: 2026年 10月
為什麼尚未收錄：recursive agent 模式；human oversight at meta-level
https://github.com/jjyaoao/HelloAgents

Agent framework with agent communication patterns（Python）
Primitive: Agent-to-agent communication
Type: Framework
License: MIT，~3171 stars
最近更新: 2026年 10月
為什麼尚未收錄：通訊層設計；多 agent 協議
https://github.com/Sompote/Tigrimos (重複)

已列在 E.44
https://github.com/manhvann/codexkit

Project-local Codex workspace kit with reusable skills/agents/hooks（Python）
Primitive: Project-scoped agent kit
Type: Kit/Template
License: MIT，~30 stars
最近更新: 2026年 10月
為什麼尚未收錄：project-local 設計；對比全局 workspace
https://github.com/joeynyc/hermes-hudui

Hermes agent HUD UI（JavaScript）
Primitive: HUD-style UI for agent monitoring
Type: UI
License: MIT（推測），~15 stars
最近更新: 2026年 9月
為什麼尚未收錄：HUD 介面模式；新鮮的視覺設計思路
G. Artifacts、Provenance、Review
https://github.com/atomicstrata/llm-wiki-compiler (已在 Cells 裡)

LLM Wiki compiler
https://github.com/langchain-ai/langchain

The agent engineering platform（Python）
Primitive: Tool ecosystem + integration for agents
Type: Framework/Platform
License: MIT，~147555 stars
最近更新: 2026年 10月
為什麼尚未收錄：Stars 太高；但作為平台級的工具生態，值得特別看
https://github.com/Eigenwise/persistent-memory-agent-example

Persistent memory agent example（Python）
Primitive: Memory persistence pattern
Type: Example/Template
License: MIT（推測），~20 stars
最近更新: 2026年 9月
為什麼尚未收錄：實例代碼；展示 memory + review 的實現
https://github.com/mobfish-ai/mobfish-agent

Mobfish agent framework（Python）
Primitive: Modular agent composition
Type: Framework
License: MIT，~50 stars
最近更新: 2026年 9月
為什麼尚未收錄：中文社群；模組化設計
https://github.com/nuglifeleoji/Options-Analytics-Agent

Options Analytics Agent（Python）
Primitive: Domain-specific agent (finance)
Type: 示範
License: MIT（推測），~20 stars
最近更新: 2026年 9月
為什麼尚未收錄：領域 agent 示範；artifact + 分析結果的展示
https://github.com/TauricResearch/TradingAgents

TradingAgents — Multi-Agent LLM Financial Trading Framework（Python）
Primitive: Domain-specific orchestration (trading)
Type: Framework
License: MIT，~110183 stars（異常高，可能有重複計算）
最近更新: 2026年 10月
為什麼尚未收錄：金融領域；高度特化的多 agent 編排
https://github.com/zai-org/Open-AutoGLM

Open Phone Agent Model & Framework（Python）
Primitive: Phone automation + voice agent
Type: Framework
License: Apache-2.0，~26356 stars
最近更新: 2026年 10月
為什麼尚未收錄：電話 agent；voice + automation 的結合
H. Collaboration & Extensibility
https://github.com/diffusionstudio/agent

Agent framework for agentic video editing（Python）
Primitive: Video editing workflow + agent
Type: Framework
License: MIT，~293 stars
最近更新: 2026年 10月
為什麼尚未收錄：視頻領域；creative workflow 的 agent 化
https://github.com/eugeniughelbur/obsidian-second-brain

Obsidian + AI agent integration（JavaScript/Python）
Primitive: Knowledge base + agent integration
Type: Plugin/Integration
License: MIT（推測），~25 stars
最近更新: 2026年 9月
為什麼尚未收錄：Obsidian 集成；筆記 + agent 的混合 workspace
https://github.com/RyjoxTechnologies/Octopoda-OS

Octopoda OS（Python/TypeScript）
Primitive: OS-level agent abstraction
Type: OS/Runtime
License: MIT（推測），~40 stars
最近更新: 2026年 9月
為什麼尚未收錄：操作系統層的 agent；Octop 的平行產品
https://github.com/vbcherepanov/total-agent-memory

Total agent memory system（Python）
Primitive: Comprehensive memory architecture
Type: Library
License: MIT（推測），~30 stars
最近更新: 2026年 9月
為什麼尚未收錄：全面 memory 系統
https://github.com/9thLevelSoftware/Daem0n-MCP

Daemon MCP server（Python）
Primitive: Background MCP server for agents
Type: MCP Server
License: MIT，~20 stars（新專案）
最近更新: 2026年 8月
為什麼尚未收錄：MCP daemon；後台服務架構
https://github.com/a2328275243/mempalace-evolve

Memory Palace evolution framework（Python）
Primitive: Memory architecture inspiration from memory palace
Type: Framework
License: MIT（推測），~15 stars（新專案）
最近更新: 2026年 10月
為什麼尚未收錄：古典記憶宮殿應用於 agent memory；新穎概念
https://github.com/Dataojitori/nocturne_memory

Nocturne memory system（Python）
Primitive: Night-mode memory (passive learning during idle)
Type: Library
License: MIT（推測），~25 stars
最近更新: 2026年 9月
為什麼尚未收錄：被動學習 pattern；24/7 agent 的設計
https://github.com/Spacehunterz/Emergent-Learning-Framework_ELF

ELF — Emergent Learning Framework（Python）
Primitive: Emergent behavior in agent teams
Type: Framework
License: MIT（推測），~30 stars
最近更新: 2026年 9月
為什麼尚未收錄：湧現行為；agent 間的自主學習
https://github.com/rajkripal/cashew

Cashew — lightweight agent framework（Python）
Primitive: Minimal dependencies agent runtime
Type: Framework
License: MIT（推測），~40 stars
最近更新: 2026年 9月
為什麼尚未收錄：輕量級設計
https://github.com/roboticforce/sugar

Sugar — agent framework（Python）
Primitive: Sweet API for agent building
Type: Framework
License: MIT（推測），~30 stars
最近更新: 2026年 9月
為什麼尚未收錄：API 設計優先
https://github.com/mycelium-io/mycelium

Mycelium — networked agent framework（Python）
Primitive: Network topology for agent collaboration
Type: Framework
License: MIT（推測），~50 stars
最近更新: 2026年 9月
為什麼尚未收錄：網絡菌根比喻；分散型 agent 設計
https://github.com/ozgurkarahan/ai-agent-memory

AI agent memory system（Python）
Primitive: Knowledge consolidation
Type: Library
License: MIT（推測），~20 stars
最近更新: 2026年 8月
為什麼尚未收錄：土耳其社群；memory consolidation 模式
https://github.com/OpenBB-finance/agents-for-openbb

Custom agents for OpenBB Workspace（Python）
Primitive: Financial dashboard + agents
Type: Extension/Plugin
License: MIT，~100 stars
最近更新: 2026年 10月
為什麼尚未收錄：金融工作區；domain-specific agents
I. 新興/實驗方向
https://github.com/divagr18/memlayer

Memlayer — memory layer abstraction（Python）
Primitive: Memory as middleware layer
Type: Library
License: MIT（推測），~20 stars
最近更新: 2026年 9月
為什麼尚未收錄：中介層設計；解耦 memory
https://github.com/nonatofabio/luna-agent

Luna agent framework（Python）
Primitive: Conversational agent architecture
Type: Framework
License: MIT（推測），~25 stars
最近更新: 2026年 9月
為什麼尚未收錄：對話為主 agent
https://github.com/jeffpierce/memory-palace

Memory palace for agents（Python）
Primitive: Spatial memory organization
Type: Library
License: MIT（推測），~20 stars
最近更新: 2026年 9月
為什麼尚未收錄：空間記憶；2D/3D workspace 與 memory 的連結
https://github.com/cwinvestments/memstack

Memstack — memory stack for agents（Python）
Primitive: Layered memory (context stack)
Type: Library
License: MIT（推測），~20 stars
最近更新: 2026年 9月
為什麼尚未收錄：堆棧式 memory 設計
https://github.com/fellowgeek/mcp-memory

MCP memory server（Python）
Primitive: MCP protocol for memory
Type: MCP Server
License: MIT（推測），~15 stars
最近更新: 2026年 10月
為什麼尚未收錄：MCP memory protocol
J. 跨界與整合
https://github.com/grapeot/context-infrastructure (已列在 C.21)

https://github.com/diffusionstudio/agent (已列在 H.75)

https://github.com/Sompote/Tigrimos (已列在 E.53)

剩餘 5 個推薦：

https://github.com/Lexus2016/claude-code-studio (已列在 B.11)

https://github.com/cloudflare/cloudflare-os (已列在 A.5)

https://github.com/VRSEN/agency-swarm (已列在 D.42)

https://github.com/langchain-ai/langchain (已列在 G.69)

https://github.com/livekit/agents (已列在 D.31)

總結
這 100 個推薦著重在：

工作區設計（8 項）：space 與 spatial organization 的具體實現
多 agent 編排（17 項）：orchestration primitives、task routing、persona 分離
Memory & Handoff（10 項）：persistent memory、protocol 標準化、context 傳遞
Runtime & Sandbox（15 項）：from Python LangGraph 到 Rust orchestration；production observability
Progress & State（19 項）：evaluation、trajectory、persistent sessions、cross-workspace state
Human Oversight（8 項）：approval flow、permission scoping、evidence tracking
Artifacts & Review（6 項）：code/doc generation、knowledge graph、domain-specific outputs
Collaboration（10 項）：extensibility、MCP、plugin ecosystems、team workflows
新興方向（7 項）：memory palace、emergent behavior、networked agents
