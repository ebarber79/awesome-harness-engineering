# Tier-1 Synthesis — The Agent Loop, Four Ways

One-page synthesis of the four Tier-1 loop-fundamentals sources. Read this to
hold the whole mental model at once; read the originals for depth.

Sources:
- ReAct (Yao et al., 2022) — https://arxiv.org/abs/2210.03629
- Unrolling the Codex Agent Loop (OpenAI / Michael Bolin, 2026) — https://openai.com/index/unrolling-the-codex-agent-loop/
- LangGraph — Low Level Concepts — https://langchain-ai.github.io/langgraph/concepts/low_level/
- Building Effective Agents (Anthropic, 2024) — https://www.anthropic.com/research/building-effective-agents

---

## The one idea they share

An agent is a **loop over a growing context**: the model emits a thought and an
action, the environment returns an observation, the observation is appended to
context, and the loop repeats until a stop condition fires. Everything else —
planning, memory, tools, verification — is scaffolding bolted onto that spine.
The four sources describe the same spine at four altitudes: **theory (ReAct) →
production mechanics (Codex) → durable state machine (LangGraph) → when to loop
at all (Anthropic).**

## 1. ReAct — *why* the loop is Thought/Action/Observation

The foundational move: interleave **reasoning traces** with **actions** instead
of separating them. Reasoning-only agents hallucinate (no grounding);
acting-only agents are opaque and can't recover from surprises. Interleaving lets
the model *induce, track, and update a plan* while actions *ground it in external
truth* — so an unexpected observation revises the reasoning trajectory in-flight
rather than corrupting everything downstream.

**Mechanism an engineer must supply:** (1) a model that emits structured
thought/action tokens, (2) action-parsing logic, (3) observation injection back
into the context window, (4) tool APIs for grounding. That is the irreducible
harness. **Takeaway:** in-flight error correction is the whole point of the loop —
design so a bad observation can cheaply redirect the next thought.

## 2. Codex — *how* one iteration actually runs in production

The same cycle, unrolled into five concrete steps per turn:
1. **Build the input window** — system instructions + repo context + request + state.
2. **Ask the model (streaming)** — tool-call args arrive as deltas; accumulate before JSON-parsing.
3. **Execute tool calls under policy** — validate name+args, enforce **allowlists** (not denylists), run sandboxed.
4. **Append observations** — feed results back into state.
5. **Evaluate stop condition** — terminate only when there are **no pending tool calls AND** a **completed** (non-truncated) response.

**Production hardening the theory omits:**
- **Bounded iteration** — cap max turns *and* max tool calls (their example: 12 turns) to stop runaway loops.
- **Stateful-by-ID context** — send only *new* items each turn (via `previous_response_id`) to avoid re-sending history — controls cost and context pollution.
- **Failure is a loop-design problem, not a model problem** — under-specified driver/state/tool/policy layers, weak sandboxing, or compaction that discards requirements. "If you can't answer these, you don't have an agent — you have an expensive surprise generator."

## 3. LangGraph — the loop as a *durable, resumable* state machine

Where Codex gives you the runtime, LangGraph gives you the **explicit control-flow
model**: the loop is a directed graph over typed state.
- **State** — a typed schema; **reducers** define how each node's output merges in (append vs. overwrite). This is your context, made explicit and mergeable.
- **Nodes** = units of work (call model, run tool); **edges** = flow; **conditional edges** = branch on state (the "should I loop again or stop?" decision, coded not prompted).
- **START/END** bound the graph; the classic agent is a `model → (conditional) → tools → model` cycle.
- **Checkpointers + threads** = persistence: state is saved after every step, so a loop can **pause, survive a crash, and resume mid-flight** — and supports human-in-the-loop interrupts and time-travel.

**Takeaway:** termination and branching become *typed code on state*, not hope
in a prompt — and checkpointing turns a fragile long loop into a resumable one.

## 4. Building Effective Agents — *whether* to run an autonomous loop at all

Anthropic's reframe: don't reach for the loop first. Distinguish **workflows**
(LLM steps on predetermined code paths — predictable) from **agents** (the model
directs its own process — flexible). Their pattern ladder, simplest first:
- **Prompt chaining** — fixed sequential subtasks with gates between them.
- **Routing** — classify input, dispatch to a specialized handler.
- **Parallelization** — sectioning (split for speed) or voting (consensus/guardrails).
- **Orchestrator-workers** — a lead LLM decomposes *dynamically* and delegates, then synthesizes. (This is the claude-loop / parallel-Claudes shape.)
- **Evaluator-optimizer** — generate → critique → refine when clear eval criteria exist. (This is the correctless RED/GREEN + adversarial-review shape.)
- **Autonomous agent** — a true open-ended loop on environmental feedback, with human checkpoints and stop conditions. Highest cost/latency and compounding-error risk; use only when steps genuinely can't be predetermined.

**Three governing principles:** **simplicity** (add complexity only when simpler
provably fails, measured by evals), **transparency** (show planning steps), and
**tooling as UX** (invest in the agent-computer interface — docs, examples, edge
cases — as much as a human UI). Start with raw LLM APIs; adopt frameworks only
once you understand what they hide.

---

## Synthesis for your stack

- **The spine is settled** (ReAct): thought → action → observation → append → repeat. Your claude-loop and correctless are both this loop with different stop conditions.
- **Harden the iteration** (Codex): bounded turns/tool-calls, allowlists, sandbox, and *don't re-send history* are production requirements your autonomous loops should encode explicitly — not emergent behavior.
- **Make it resumable** (LangGraph): claude-loop restarts subagents fresh; typed-state + checkpointing is the pattern for durable mid-loop resume you haven't adopted (flagged as a gap in `READING_LIST.md`).
- **Pick the smallest pattern** (Anthropic): most work is a *workflow*, not an autonomous agent. Orchestrator-workers ≈ your wave mode; evaluator-optimizer ≈ correctless. Reach for the full autonomous loop only when the path genuinely can't be predetermined — and gate it with evals and stop conditions.

**Through-line:** the model is the least interesting part. Reliability comes from
the loop's control flow, its context discipline, its tool interface, and its stop
conditions — i.e. the harness.
