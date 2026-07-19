# Design Primitives — Reading List

Extracted from `README.md` (Design Primitives section), organized by subsection.
Source: ai-boost/awesome-harness-engineering @ 73336b6 (2026-07-01).

Tuned for loop/harness engineering study. The **Start Here** tier below is the
curated path; the full per-subsection index follows underneath.

---

## ★ Start Here — loop/harness core path

Read in this order. Each tier builds on the last.

### Tier 1 — Loop fundamentals (read first, in order)
1. [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629) — the Thought/Action/Observation cycle every harness is built on. The mental model for everything else.
2. [Unrolling the Codex Agent Loop](https://openai.com/index/unrolling-the-codex-agent-loop/) — canonical decomposition of one loop iteration: observe → plan → act → verify.
3. [LangGraph — Low Level Concepts](https://langchain-ai.github.io/langgraph/concepts/low_level/) — the most concrete engineering treatment: loop as typed-state graph, termination conditions, checkpointing, resumption.
4. [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) — the reference taxonomy of loop/workflow patterns (chaining, routing, orchestrator-workers, evaluator-optimizer). Your vocabulary anchor.

### Tier 2 — Context is the loop's fuel budget
5. [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — why context, not prompts, is the real lever.
6. [Compaction — Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/compaction) + [Claude Code Compaction Explained](https://okhlopkov.com/claude-code-compaction-explained/) — exactly the mechanism that summarizes your long sessions; know it cold.
7. [Prompt Caching — Claude API Docs](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching) — the 5-min cache TTL that governs loop-cadence cost (directly relevant to /loop timing).

### Tier 3 — Long-horizon planning & orchestration (your claude-loop territory)
8. [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) — the single most on-point essay for what you're building.
9. [Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps) — planning artifacts (PLAN.md/IMPLEMENT.md), the same pattern as this repo's templates.
10. [Plan-and-Execute Agents](https://blog.langchain.com/plan-and-execute-agents/) — plan/execute separation = your orchestrator-vs-subagent split.
11. [Building a C Compiler with a Team of Parallel Claudes](https://www.anthropic.com/engineering/building-c-compiler) — worked parallel-agent case study; mirrors claude-loop wave/parallel mode.
12. [Multi-Agent Workflows Often Fail. Here's How to Engineer Ones That Don't.](https://github.blog/ai-and-ml/generative-ai/multi-agent-workflows-often-fail-heres-how-to-engineer-ones-that-dont/) — failure modes to design against.

### Tier 4 — Verification & human-in-the-loop (closing the loop)
13. [Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) — how to know a loop actually works.
14. [Humans and Agents in Software Engineering Loops](https://martinfowler.com/articles/exploring-gen-ai/humans-and-agents.html) — where the human belongs in the cycle.
15. [Measuring AI Agent Autonomy in Practice](https://www.anthropic.com/news/measuring-agent-autonomy) — framing for how much rope to give an autonomous loop.

### Deep-dive extras (optional, high-signal)
- [Introducing dynamic workflows in Claude Code](https://claude.com/blog/introducing-dynamic-workflows-in-claude-code) — the Workflow tool's own design rationale.
- [How Middleware Lets You Customize Your Agent Harness](https://blog.langchain.com/how-middleware-lets-you-customize-your-agent-harness/) — hook/middleware layering.
- [Hooks – Codex](https://developers.openai.com/codex/hooks) — deterministic lifecycle hooks (SessionStart/PreToolUse/PostToolUse).
- [A Scheduler-Theoretic Framework for LLM Agent Execution](https://arxiv.org/abs/2604.11378) — loop cadence/scheduling as a formal problem.

---

## Agent Loop

- [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629)
- [Unrolling the Codex Agent Loop](https://openai.com/index/unrolling-the-codex-agent-loop/)
- [LangGraph — Low Level Concepts](https://langchain-ai.github.io/langgraph/concepts/low_level/)
- [Unlocking the Codex Harness: How We Built the App Server](https://openai.com/index/unlocking-the-codex-harness/)
- [Hooks – Codex](https://developers.openai.com/codex/hooks)
- [Extended Thinking — Claude API Docs](https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking)
- [Improving Deep Agents with Harness Engineering](https://blog.langchain.com/improving-deep-agents-with-harness-engineering/)
- [Life-Harness](https://github.com/Tianshi-Xu/Life-Harness)
- [How Middleware Lets You Customize Your Agent Harness](https://blog.langchain.com/how-middleware-lets-you-customize-your-agent-harness/)
- [Agents Learn Their Runtime: Interpreter Persistence as Training-Time Semantics](https://arxiv.org/abs/2603.01209)
- [Real-Time Deadlines Reveal Temporal Awareness Failures in LLM Strategic Reasoning](https://arxiv.org/abs/2601.13206)
- [A Scheduler-Theoretic Framework for LLM Agent Execution](https://arxiv.org/abs/2604.11378)
- [Confucius Code Agent (CCA)](https://github.com/facebookresearch/cca-swebench)
- [The Design Space of Today's and Future AI Agent Systems](https://arxiv.org/abs/2604.14228)
- [deepclaude](https://github.com/aattaran/deepclaude)
- [The Coding Harness Behind GitHub Copilot in VS Code](https://code.visualstudio.com/blogs/2026/05/15/agent-harnesses-github-copilot-vscode)
- [statewright](https://github.com/statewright/statewright)
- [Introducing dynamic workflows in Claude Code](https://claude.com/blog/introducing-dynamic-workflows-in-claude-code)
- [AgentSPEX](https://github.com/ScaleML/AgentSPEX)

## Planning & Task Decomposition

- [Run Long-Horizon Tasks with Codex](https://developers.openai.com/blog/run-long-horizon-tasks-with-codex/)
- [Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- [Plan-and-Execute Agents](https://blog.langchain.com/plan-and-execute-agents/)
- [microsoft/TaskWeaver](https://github.com/microsoft/TaskWeaver)
- [LATS: Language Agent Tree Search](https://arxiv.org/abs/2310.04406)
- [Agyn: A Multi-Agent System for Team-Based Autonomous Software Engineering](https://arxiv.org/abs/2602.01465)
- [Plan-and-Act: Improving Planning of Agents for Long-Horizon Tasks](https://arxiv.org/abs/2503.09572)
- [Choosing the Right Multi-Agent Architecture](https://blog.langchain.com/choosing-the-right-multi-agent-architecture/)
- [Multi-Agent Workflows Often Fail. Here's How to Engineer Ones That Don't.](https://github.blog/ai-and-ml/generative-ai/multi-agent-workflows-often-fail-heres-how-to-engineer-ones-that-dont/)
- [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [Task-Adaptive Multi-Agent Orchestration (AdaptOrch)](https://arxiv.org/abs/2602.16873)
- [Task-Decoupled Planning for Long-Horizon Agents (TDP)](https://arxiv.org/abs/2601.07577)

## Context Delivery & Compaction

- [Harness Engineering](https://openai.com/index/harness-engineering/)
- [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Compaction — Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/compaction)
- [LLMLingua](https://github.com/microsoft/LLMLingua)
- [Prompt Caching — Claude API Docs](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)
- [Autonomous Context Compression](https://blog.langchain.com/autonomous-context-compression/)
- [Active Context Compression: Autonomous Memory Management in LLM Agents](https://arxiv.org/abs/2601.07190)
- [context-mode](https://github.com/mksglu/context-mode)
- [Making Agent-Friendly Pages with Content Negotiation](https://vercel.com/blog/making-agent-friendly-pages-with-content-negotiation)
- [A-RAG: Scaling Agentic Retrieval-Augmented Generation via Hierarchical Retrieval Interfaces](https://arxiv.org/abs/2602.03442)
- [LLM Readiness Harness: Evaluation, Observability, and CI Gates for LLM/RAG Applications](https://arxiv.org/abs/2603.27355)
- [ByteRover: Agent-Native Memory Through LLM-Curated Hierarchical Context](https://arxiv.org/abs/2604.01599)
- [Claude Code Compaction: How Context Compression Works](https://okhlopkov.com/claude-code-compaction-explained/)
- [Token Savior](https://github.com/Mibayy/token-savior)
- [Trellis](https://github.com/mindfold-ai/Trellis)
- [OpenViking](https://github.com/volcengine/OpenViking)
- [DESIGN.md](https://github.com/google-labs-code/design.md)
- [codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp)
- [Mirage](https://github.com/strukto-ai/mirage)
- [dirac](https://github.com/dirac-run/dirac)
- [MinishLab/semble](https://github.com/MinishLab/semble)
- [harness-experimental](https://github.com/hoangnb24/harness-experimental)
- [headroom](https://github.com/chopratejas/headroom)
- [Context7](https://github.com/upstash/context7)
- [Context Pruning for Coding Agents via Multi-Rubric Latent Reasoning](https://arxiv.org/abs/2605.15315)

## Tool Design

- [Writing Effective Tools for Agents](https://www.anthropic.com/engineering/writing-effective-tools-for-agents)
- [Tool Use — Claude API Docs](https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview)
- [Function Calling — OpenAI Docs](https://platform.openai.com/docs/guides/function-calling)
- [Tool Annotations as Risk Vocabulary](https://blog.modelcontextprotocol.io/posts/2026-03-16-tool-annotations/)
- [outlines](https://github.com/dottxt-ai/outlines)
- [instructor](https://python.useinstructor.com/)
- [SkillTester: Benchmarking Utility and Security of Agent Skills](https://arxiv.org/abs/2603.28815)
- [AutoHarness: Improving LLM Agents by Automatically Synthesizing a Code Harness](https://arxiv.org/abs/2603.03329)
- [Scaling Parallel Tool Calling for Efficient Deep Research](https://arxiv.org/abs/2602.07359)
- [EigentSearch-Q+](https://arxiv.org/abs/2604.07927)
- [TopoCurate: Modeling Interaction Topology for Tool-Use Agent Training](https://arxiv.org/abs/2603.01714)
- [Design Patterns for Deploying AI Agents with Model Context Protocol](https://arxiv.org/abs/2603.13417)
- [tui-use](https://github.com/onesuper/tui-use)
- [CLI-Anything](https://github.com/HKUDS/CLI-Anything)
- [zerolang](https://github.com/vercel-labs/zerolang)

## Skills & MCP

- [Model Context Protocol](https://modelcontextprotocol.io/introduction)
- [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)
- [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp)
- [Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp)
- [agent-device](https://github.com/callstackincubator/agent-device)
- [A2A Protocol](https://github.com/a2aproject/A2A)
- [Announcing the Agentic Resource Discovery specification](https://developers.googleblog.com/announcing-the-agentic-resource-discovery-specification/)
- [MCP Inspector](https://github.com/modelcontextprotocol/inspector)
- [Shell + Skills + Compaction: Tips for Long-Running Agents](https://developers.openai.com/blog/skills-shell-tips)
- [Composio](https://github.com/ComposioHQ/composio)
- [MCP Streamable HTTP Transport](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports)
- [The 2026 MCP Roadmap](https://blog.modelcontextprotocol.io/posts/2026-mcp-roadmap/)
- [Developer's Guide to AI Agent Protocols](https://developers.googleblog.com/en/developers-guide-to-ai-agent-protocols/)
- [AG-UI](https://github.com/ag-ui-protocol/ag-ui)
- [Code Execution with MCP: Building More Efficient Agents](https://www.anthropic.com/engineering/code-execution-with-mcp)
- [Microsoft Skills Framework](https://github.com/microsoft/skills)
- [SkillNet & SkillsBench: Infrastructure for AI Agent Skills at Scale](https://github.com/skillmatic-ai/awesome-agent-skills)
- [AWS Bedrock AgentCore with WebRTC Support](https://aws.amazon.com/about-aws/whats-new/2026/03/amazon-bedrock-webrtc/)
- [Hermes Agent: Unified Streaming for Real-Time Agent Workflows](https://juliangoldie.com/hermes-agent-unified-streaming/)
- [Google Developers: Closing the Knowledge Gap with Agent Skills](https://developers.googleblog.com/closing-the-knowledge-gap-with-agent-skills/)
- [What's New with GitHub Copilot Coding Agent](https://github.blog/ai-and-ml/github-copilot/whats-new-with-github-copilot-coding-agent/)
- [Announcing Official MCP Support for Google Services](https://cloud.google.com/blog/products/ai-machine-learning/announcing-official-mcp-support-for-google-services)
- [Dataverse Skills: Your Coding Agent Now Speaks Dataverse](https://devblogs.microsoft.com/powerplatform/dataverse-skills-your-coding-agent-now-speaks-dataverse)
- [Agent Toolkit for AWS](https://github.com/aws/agent-toolkit-for-aws)
- [agentic-stack](https://github.com/codejunkie99/agentic-stack)
- [mcp-agent](https://github.com/lastmile-ai/mcp-agent)
- [vurb.ts](https://github.com/vinkius-labs/vurb.ts)
- [SkillOpt](https://github.com/microsoft/SkillOpt)
- [superpowers](https://github.com/obra/superpowers)
- [Antigravity Awesome Skills](https://github.com/sickn33/antigravity-awesome-skills)
- [agentgateway](https://github.com/agentgateway/agentgateway)
- [AIP: A Graph Representation for Learning and Governing Agent Skills](https://arxiv.org/abs/2606.04781)

## Permissions & Authorization

- [Beyond Permission Prompts](https://www.anthropic.com/engineering/beyond-permission-prompts)
- [OWASP LLM06:2025 — Excessive Agency](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/)
- [GitHub Enterprise — Governing Agents](https://wellarchitected.github.com/library/governance/recommendations/governing-agents/)
- [Claude Code Auto Mode: A Safer Way to Skip Permissions](https://www.anthropic.com/engineering/claude-code-auto-mode)
- [Claude Agent SDK — Configure Permissions](https://platform.claude.com/docs/en/agent-sdk/permissions)
- [Two Different Types of Agent Authorization](https://blog.langchain.com/two-different-types-of-agent-authorization/)
- [Authorization and Governance for AI Agents: Runtime Authorization Beyond Identity at Scale](https://techcommunity.microsoft.com/blog/microsoft-security-blog/authorization-and-governance-for-ai-agents-runtime-authorization-beyond-identity/4509161)
- [IETF draft-klrc-aiagent-auth: AI Agent Authentication and Authorization](https://datatracker.ietf.org/doc/draft-klrc-aiagent-auth/)
- [Nango: Pre-Built Authentication for AI Agents](https://nango.dev)
- [AgentDoG: A Diagnostic Guardrail Framework for AI Agent Safety and Security](https://arxiv.org/abs/2601.18491)
- [Open Agent Passport (OAP): Deterministic Pre-Action Authorization for Autonomous AI Agents](https://arxiv.org/abs/2603.20953)
- [nah](https://github.com/manuelschipper/nah)

## Memory & State

- [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents)
- [Letta (MemGPT)](https://github.com/letta-ai/letta)
- [mem0](https://github.com/mem0ai/mem0)
- [Stash](https://github.com/alash3al/stash)
- [TencentDB-Agent-Memory](https://github.com/Tencent/TencentDB-Agent-Memory)
- [Zep](https://github.com/getzep/zep)
- [engram](https://github.com/Gentleman-Programming/engram)
- [MemPalace](https://github.com/MemPalace/mempalace)
- [agentmemory](https://github.com/rohitg00/agentmemory)
- [claude-memory-compiler](https://github.com/coleam00/claude-memory-compiler)
- [How We Built Agent Builder's Memory System](https://blog.langchain.com/how-we-built-agent-builders-memory-system/)
- [Building an Agentic Memory System for GitHub Copilot](https://github.blog/ai-and-ml/github-copilot/building-an-agentic-memory-system-for-github-copilot/)
- [MemArchitect: A Policy-Driven Memory Governance Layer](https://arxiv.org/abs/2603.18330)
- [Codified Context: Infrastructure for AI Agents in a Complex Codebase](https://arxiv.org/abs/2602.20478)
- [Facts as First Class Objects: Knowledge Objects for Persistent LLM Memory](https://arxiv.org/abs/2603.17781)
- [Recoverability Has a Law: The ERR Measure for Tool-Augmented Agents](https://arxiv.org/abs/2601.22352)
- [MAGMA: Multi-Graph Agentic Memory Architecture](https://arxiv.org/abs/2601.03236)
- [GAAMA: Graph Augmented Associative Memory for Agents](https://arxiv.org/abs/2603.27910)
- [Graph-Native Cognitive Memory for AI Agents: Formal Belief Revision Semantics for Versioned Memory Architectures](https://arxiv.org/abs/2603.17244)
- [Continual learning for AI agents](https://blog.langchain.com/continual-learning-for-ai-agents/)
- [cognee](https://github.com/topoteretes/cognee)
- [Hindsight](https://github.com/vectorize-io/hindsight)
- [ClawVM: Harness-Managed Virtual Memory for Stateful Tool-Using LLM Agents](https://arxiv.org/abs/2604.10352)

## Task Runners & Orchestration

- [Harness Engineering](https://openai.com/index/harness-engineering/)
- [Building a C Compiler with a Team of Parallel Claudes](https://www.anthropic.com/engineering/building-c-compiler)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [LangGraph](https://github.com/langchain-ai/langgraph)
- [OpenAI Agents SDK](https://github.com/openai/openai-agents-python)
- [Google ADK](https://github.com/google/adk-python)
- [strands-agents/harness-sdk](https://github.com/strands-agents/harness-sdk)
- [Build Long-running AI agents that pause, resume, and never lose context with ADK](https://developers.googleblog.com/build-long-running-ai-agents-that-pause-resume-and-never-lose-context-with-adk/)
- [Build Cross-Language Multi-Agent Team with Google's Agent Development Kit and A2A](https://developers.googleblog.com/en/build-cross-language-multi-agent-team-with-google-agent-development-kit-and-a2a/)
- [AutoGen](https://github.com/microsoft/autogen)
- [CrewAI](https://github.com/crewAIInc/crewAI)
- [PydanticAI](https://github.com/pydantic/pydantic-ai)
- [LangGraph 2.0 Release](https://github.com/langchain-ai/langgraph)
- [OmniRoute: Multi-Provider LLM Gateway](https://github.com/diegosouzapw/OmniRoute)
- [OpenSquilla](https://github.com/opensquilla/opensquilla)
- [Scaling Managed Agents: Decoupling the Brain from the Hands](https://www.anthropic.com/engineering/managed-agents)
- [Microsoft Agent Framework at BUILD 2026: Agent Harness, Hosted Agents, CodeAct, and more](https://devblogs.microsoft.com/agent-framework/microsoft-agent-framework-at-build-2026-announce/)
- [Microsoft Agent Framework 1.0](https://devblogs.microsoft.com/agent-framework/microsoft-agent-framework-version-1-0/)
- [Conductor](https://github.com/microsoft/conductor)
- [AgentScope Runtime](https://github.com/agentscope-ai/agentscope-runtime)
- [Orchestrating Ambient Agents with Temporal](https://temporal.io/blog/orchestrating-ambient-agents-with-temporal)
- [Vercel AI SDK](https://github.com/vercel/ai)
- [Mastra](https://github.com/mastra-ai/mastra)
- [open-multi-agent](https://github.com/JackChen-me/open-multi-agent)
- [The next evolution of the Agents SDK](https://openai.com/index/the-next-evolution-of-the-agents-sdk/)
- [Symphony](https://github.com/openai/symphony)
- [Harmonist](https://github.com/GammaLabTechnologies/harmonist)
- [Hive](https://github.com/aden-hive/hive)
- [thClaws](https://github.com/thClaws/thClaws)
- [sandcastle](https://github.com/mattpocock/sandcastle)
- [bernstein](https://github.com/sipyourdrink-ltd/bernstein)

## Verification & CI Integration

- [Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [promptfoo](https://github.com/promptfoo/promptfoo)
- [AgentBench](https://github.com/THUDM/AgentBench)
- [Testing Agent Skills Systematically with Evals](https://developers.openai.com/blog/eval-skills)
- [Agent Evaluation Readiness Checklist](https://blog.langchain.com/agent-evaluation-readiness-checklist/)
- [Evaluating Skills](https://blog.langchain.com/evaluating-skills/)
- [AgentAssay: Token-Efficient Regression Testing for Non-Deterministic Agent Workflows](https://arxiv.org/abs/2603.02601)
- [Agentic Harness for Real-World Compilers: A Case Study in Specialized Tool Design](https://arxiv.org/abs/2603.20075)
- [Eval-Driven Development: Build and Evaluate Reliable AI Agents](https://developers.redhat.com/articles/2026/03/23/eval-driven-development-build-evaluate-ai-agents)
- [Agent Evaluation Framework 2026: Metrics, Rubrics & Benchmarks](https://galileo.ai/blog/agent-evaluation-framework-metrics-rubrics-benchmarks)
- [The 2025 AI Agent Index: Documenting Technical and Safety Features of Deployed Agentic AI Systems](https://arxiv.org/abs/2602.17753)
- [sentrux](https://github.com/sentrux/sentrux)

## Observability & Tracing

- [OpenLLMetry](https://github.com/traceloop/openllmetry)
- [Arize Phoenix](https://github.com/Arize-ai/phoenix)
- [Langfuse](https://github.com/langfuse/langfuse)
- [Weights & Biases Weave](https://github.com/wandb/weave)
- [OTel GenAI Semantic Conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/)
- [Pydantic Logfire](https://github.com/pydantic/logfire)
- [Helicone](https://github.com/Helicone/helicone)
- [OpenObserve: Unified Observability for LLM Agents](https://openobserve.ai/)
- [Braintrust](https://www.braintrust.dev)
- [Building Observable AI Agents: Temporal Now Integrates with Braintrust](https://temporal.io/blog/building-observable-ai-agents-temporal-now-integrates-with-braintrust)
- [Introducing BigQuery Agent Analytics](https://cloud.google.com/blog/products/data-analytics/introducing-bigquery-agent-analytics/)
- [Distributed Tracing for Agentic Workflows with OpenTelemetry](https://developers.redhat.com/articles/2026/04/06/distributed-tracing-agentic-workflows-opentelemetry)
- [Red-Teaming Anthropic's Internal Agent Monitoring Systems — METR](https://metr.org/blog/2026-03-25-red-teaming-anthropic-agent-monitoring/)
- [Future AGI](https://github.com/future-agi/future-agi)

## Debugging & Developer Experience

- [AgentOps](https://github.com/AgentOps-AI/agentops)
- [claude-devtools](https://github.com/matt1398/claude-devtools)
- [Syncause/debug-skill](https://github.com/Syncause/debug-skill)
- [AgentTrace: Causal Graph Tracing for Root Cause Analysis in Multi-Agent Systems](https://arxiv.org/abs/2603.14688)
- [TraceCoder: A Trace-Driven Multi-Agent Framework for Automated Debugging of LLM-Generated Code](https://arxiv.org/abs/2602.06875)
- [AgentRx: Systematic Debugging for AI Agents](https://www.microsoft.com/en-us/research/blog/systematic-debugging-for-ai-agents-introducing-the-agentrx-framework/)
- [Debugging Deep Agents with LangSmith](https://blog.langchain.com/debugging-deep-agents-with-langsmith/)
- [Where LLM Agents Fail and How They Can Learn From Failures (AgentDebug)](https://arxiv.org/abs/2509.25370)
- [AgentPrism](https://github.com/evilmartians/agent-prism)
- [Characterizing Faults in Agentic AI](https://arxiv.org/abs/2603.06847)
- [More Visibility into Copilot Coding Agent Sessions](https://github.blog/changelog/2026-03-19-more-visibility-into-copilot-coding-agent-sessions/)
- [AgentStepper: Interactive Debugging of Software Development Agents](https://arxiv.org/abs/2602.06593)

## Human-in-the-Loop

- [aws-samples/sample-human-in-the-loop-patterns](https://github.com/aws-samples/sample-human-in-the-loop-patterns)
- [Dify Human-in-the-Loop Node](https://github.com/langgenius/dify/discussions/32245)
- [HITL Protocol](https://github.com/rotorstar/hitl-protocol)
- [LangGraph — Human-in-the-Loop Concepts](https://langchain-ai.github.io/langgraph/concepts/human_in_the_loop/)
- [AutoGen — Human-in-the-Loop](https://microsoft.github.io/autogen/0.2/docs/tutorial/human-in-the-loop/)
- [Claude Agent SDK — Handle Approvals and User Input](https://platform.claude.com/docs/en/agent-sdk/user-input)
- [HiL-Bench: Do Agents Know When to Ask for Help?](https://arxiv.org/abs/2604.09408)
- [Human Judgment in the Agent Improvement Loop](https://blog.langchain.com/human-judgment-in-the-agent-improvement-loop/)
- [Humans and Agents in Software Engineering Loops](https://martinfowler.com/articles/exploring-gen-ai/humans-and-agents.html)
- [Measuring AI Agent Autonomy in Practice](https://www.anthropic.com/news/measuring-agent-autonomy)
- [AutoResearchClaw HITL Co-Pilot](https://github.com/aiming-lab/AutoResearchClaw)

---

## Overlap Map — what you already run vs. gaps to study

Anchored to your existing stack: **claude-loop** (prd.json story-by-story, fresh
subagent per story, sequential + wave/parallel-worktree modes), **correctless**
(RED/GREEN TDD agents, multi-agent adversarial spec review, verify/drift audit),
your **file-based memory system** (one-fact-per-file + MEMORY.md index), and your
**MCP stack** (Gmail, finance-grounding, browser, Airtable + plugin servers).

### ✅ Already implemented — read to compare, not to build
These describe patterns you're already running. Use them to name what you built and spot refinements.

- [Plan-and-Execute Agents](https://blog.langchain.com/plan-and-execute-agents/) — your claude-loop orchestrator/subagent split, verbatim.
- [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) + [Harness Design for Long-Running Apps](https://www.anthropic.com/engineering/harness-design-long-running-apps) — the prd.json + PLAN/IMPLEMENT artifact pattern you already drive.
- [Building a C Compiler with a Team of Parallel Claudes](https://www.anthropic.com/engineering/building-c-compiler) — your wave/parallel-worktree mode. Merge-barrier comparison done: their model is shared-repo + lock files in `current_tasks/` + tolerate-live-merge-conflicts (fully parallel, no barrier stall); yours isolates each agent in its own worktree with a merge+verify barrier between waves (no live conflicts to resolve, but the wave has to converge before the next one starts). A deliberate tradeoff, not an oversight — worth stating that way if this repo's README ever documents it.
- [Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) + [Testing Agent Skills with Evals](https://developers.openai.com/blog/eval-skills) — correctless's RED-phase test-from-spec discipline.
- [Compaction](https://platform.claude.com/docs/en/build-with-claude/compaction) + [Claude Code Compaction Explained](https://okhlopkov.com/claude-code-compaction-explained/) — the summarize-on-long-session behavior you rely on. Mechanics confirmed: 150k-input-token default trigger (50k floor), re-checked every sampling iteration, `pause_after_compaction` to hand-pick survivors instead of folding them into the summary, custom summarization instructions. Billing gotcha: real total spend lives in `usage.iterations`, not the top-level `input_tokens`/`output_tokens` fields — worth checking whether any cost tracking on your VPS/cron jobs already accounts for this.
- Most of **Skills & MCP** (MCP core, servers, Inspector, playwright/chrome-devtools MCP) — you already operate a live multi-server MCP stack.
- [Claude Agent SDK — Handle Approvals and User Input](https://platform.claude.com/docs/en/agent-sdk/user-input) + [LangGraph HITL](https://langchain-ai.github.io/langgraph/concepts/human_in_the_loop/) — the approval/review gates in your outreach-draft skills.

### 🟡 Partial — you do a version; these push further
- [How Middleware Lets You Customize Your Agent Harness](https://blog.langchain.com/how-middleware-lets-you-customize-your-agent-harness/) + [Hooks – Codex](https://developers.openai.com/codex/hooks) — you use skills/subagents but not a formal hook/middleware layer; this is the settings.json-hooks direction.
- [Continual learning for AI agents](https://blog.langchain.com/continual-learning-for-ai-agents/) + [Facts as First Class Objects](https://arxiv.org/abs/2603.17781) — your one-fact-per-file memory is exactly "facts as objects"; these formalize revision/consolidation you do by hand.
- [Codified Context: Infrastructure for AI Agents in a Complex Codebase](https://arxiv.org/abs/2602.20478) — your MEMORY.md index is a lightweight take; this is the heavier version.
- [Beyond Permission Prompts](https://www.anthropic.com/engineering/beyond-permission-prompts) + [Claude Code Auto Mode](https://www.anthropic.com/engineering/claude-code-auto-mode) — relevant to how far you let VPS cron jobs run headless without prompts.
- [Multi-Agent Workflows Often Fail…](https://github.blog/ai-and-ml/generative-ai/multi-agent-workflows-often-fail-heres-how-to-engineer-ones-that-dont/) — **reclassified from ✅**: correctless's adversarial review panel solves *disagreement*, not this article's actual failure modes (untyped data exchange, ambiguous action space, unenforced contracts between agents). Open question: are claude-loop's subagent briefs and correctless's RED→GREEN handoffs typed/enumerated schemas, or natural-language prose the receiving agent has to interpret? If the latter, this gap is still live.
- [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — names a concrete sub-agent return budget (1,000–2,000 tokens) that claude-loop's orchestrator/subagent split does informally but doesn't enforce; worth adopting as an explicit contract on subagent return payloads.
- [Prompt Caching — Claude API Docs](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching) — your own `ScheduleWakeup` tool is already correctly tuned to this (1h delay ceiling matches the 1h cache TTL, explicitly to avoid a cache cliff). Unaudited: whether headless VPS/cron jobs (feng alerts, outreach followups, browser-use) place any cache-breaking content (timestamps, live counters) before their cache breakpoint — the 20-block lookback window makes this an easy silent cost leak to check for.

### 🔴 Gaps — not in your stack; highest learning value
Where the reading actually teaches you something new for your studies.

- **Observability & Tracing (whole subsection)** — [Langfuse](https://github.com/langfuse/langfuse), [Arize Phoenix](https://github.com/Arize-ai/phoenix), [OTel GenAI conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/). You have no trace/eval dashboard over claude-loop or correctless runs — biggest blind spot.
- **Debugging & DX** — [AgentDebug: Where LLM Agents Fail](https://arxiv.org/abs/2509.25370), [AgentStepper](https://arxiv.org/abs/2602.06593). Systematic loop-failure post-mortems beyond reading transcripts.
- [A Scheduler-Theoretic Framework for LLM Agent Execution](https://arxiv.org/abs/2604.11378) + [Real-Time Deadlines…](https://arxiv.org/abs/2601.13206) — formal loop-cadence/scheduling; directly sharpens your /loop and cron timing intuition.
- [Recoverability Has a Law: The ERR Measure](https://arxiv.org/abs/2601.22352) — how recoverable a tool-using loop is after failure; a metric you don't currently track.
- **Thread persistence** — [LangGraph — Low Level Concepts](https://langchain-ai.github.io/langgraph/concepts/low_level/) (explicit checkpoint/resume state machine; checkpointers + `thread_id`) and [Unlocking the Codex Harness](https://openai.com/index/unlocking-the-codex-harness/) (Item/Turn/Thread protocol) both name the object you don't have: an explicit, checkpointed "thread" record that a loop can resume mid-flight with real state, not just a fresh read. claude-loop restarts subagents fresh rather than adopting durable mid-loop resumption; today, zero-shared-context pickup in your stack is answered ad hoc — compaction summaries + `MEMORY.md` facts reconstructed each session, and claude-mem's session-observation layer sitting empty/unwired (`SCOPE_OBSERVABILITY.md`) — not a typed thread object claude-loop or correctless can resume from.
- [Verification & CI Integration](https://github.com/promptfoo/promptfoo) (promptfoo, AgentBench) — regression-testing non-deterministic agent workflows in CI, beyond correctless's per-feature TDD.

### ❓ Open — verify against your own implementation
Questions the reading raised that only checking your actual code/config can answer.

- [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) — their feature list pre-marks every item as failing at the start, specifically to prevent premature-completion-declaration. Does claude-loop's `prd.json` do the same, or does `passes` only get written on completion?
