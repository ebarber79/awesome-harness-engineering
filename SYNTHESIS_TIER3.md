# Tier-3 Synthesis — Long-Horizon Planning & Orchestration

One-page synthesis of the five Tier-3 sources on planning artifacts,
multi-session handoffs, and multi-agent coordination — your claude-loop /
correctless territory. Read this to hold the whole mental model at once; read
the originals for depth.

Sources:
- Effective Harnesses for Long-Running Agents (Anthropic) — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- Harness Design for Long-Running Application Development (Anthropic) — https://www.anthropic.com/engineering/harness-design-long-running-apps
- Plan-and-Execute Agents (LangChain) — https://www.langchain.com/blog/plan-and-execute-agents
- Building a C Compiler with a Team of Parallel Claudes (Anthropic) — https://www.anthropic.com/engineering/building-c-compiler
- Multi-Agent Workflows Often Fail. Here's How to Engineer Ones That Don't. (GitHub) — https://github.blog/ai-and-ml/generative-ai/multi-agent-workflows-often-fail-heres-how-to-engineer-ones-that-dont/

---

## The one idea they share

A single loop (Tier 1) runs out of road on any task too big for one context
window or too parallel for one agent. All five sources answer the same
question from different angles — **how do you decompose work across sessions
or across agents without losing coherence?** — and the answer is never "trust
natural language to carry the intent." It's always: externalize state into an
artifact (file, schema, git history) that outlives any one context window or
any one agent's memory of the conversation.

## 1. Effective Harnesses for Long-Running Agents — the shift-handoff model

Sessions don't share memory — "each new session begins with no memory of what
came before" — so treat consecutive runs like shift-based engineering
handoffs. Two-agent architecture: an **Initializer** (first session only —
writes `init.sh`, creates `claude-progress.txt`, makes the baseline commit)
followed by repeating **Coding agents** (one feature per session, clean exit
state, progress file + commit). The planning artifact is deliberately **JSON,
not Markdown**, with a `passes` boolean per feature — Anthropic's stated
reasoning: models are less likely to "helpfully" rewrite a JSON file than a
prose one. Every session opens with the same ritual: confirm working directory
→ read git log + progress file → pick the highest-priority failing feature →
run `init.sh` + a smoke test before touching anything. Their failure-mode table
pins "premature completion declaration" specifically to *not* pre-marking every
feature as failing at the start.

## 2. Harness Design for Long-Running Application Development — the GAN-inspired split

A different shape: **Planner / Generator / Evaluator**, three roles instead of
two. The framing is explicit: models that grade their own work "confidently
praise" mediocrity (self-evaluation bias), so evaluation has to be a
structurally separate agent, GAN-style. Two mechanics worth keeping:
**file-based sprint contracts** negotiated *before* coding starts (so the
Evaluator isn't grading against a moving target), and **hard pass/fail
thresholds instead of continuous scores** (a failed criterion produces a
specific, actionable complaint, not a vague "7/10").

## 3. Plan-and-Execute Agents — separating strategy from tactics

Splits a **Planner** (reasons through the whole task once, up front) from an
**Executor** (a normal action-agent loop handling one step at a time) —
different from a single ReAct loop where planning and acting interleave every
turn. Stated limitation: "there is one planning step at the start, but then
that is never revisited" — no built-in replanning trigger. Solves prompt bloat
(a single agent's history balloons with every reasoning+action pair) and
enables swapping cheaper/faster models into the tactical role.

## 4. Building a C Compiler with a Team of Parallel Claudes — coordination at 16 agents

16 agents on one shared repo, coordinated by the simplest mechanism that
worked: claim a task by dropping a lock file in `current_tasks/`, pull/merge/
push, delete the lock — and just let Claude resolve merge conflicts inline
rather than architecting around them. Parallelization broke down on
**monolithic tasks** (compiling the Linux kernel) where every agent hit the
same bug; the fix was using GCC as an **independent oracle** to verify per-file
fixes, which re-enabled parallel decomposition of a task that initially looked
unparallelizable.

## 5. Multi-Agent Workflows Often Fail — why natural-language handoffs break

Three failure modes, one root cause: agent-to-agent communication in free text
is unreliable. (1) inconsistent data exchange → fix with **typed schemas** at
every boundary; (2) ambiguous intent → fix with **action schemas**, an
enumerated set of allowed outcomes instead of open-ended instructions; (3)
conventions without enforcement → fix by using **MCP to validate both inputs
and outputs**, not just document the contract. Thesis: "treat agents like
code, not chat interfaces."

---

## Synthesis for your stack

- **#1 and #2 are ✅ implemented** (`READING_LIST.md`) — `prd.json` is #1's
  JSON-with-`passes` pattern; correctless's spec-review-then-implement
  sequence is #2's pre-code sprint contract. Open question carried into the
  reading list: does `prd.json` pre-mark every story as failing at session
  start, the way #1's feature list does — or does `passes` only get written on
  completion?
- **#4 is ✅ but with a named architectural divergence, not just a match**:
  their model is shared-repo + lock files + tolerate-live-conflicts (fully
  parallel, no barrier stall); your wave/parallel-worktree mode isolates each
  agent and adds a merge+verify barrier between waves (safer, no live
  conflicts, but waves have to converge). Worth stating as a deliberate
  tradeoff, not an oversight.
- **#5 is reclassified from ✅ to still-open** — correctless's adversarial
  review panel solves *disagreement between reviewers*, not this article's
  actual concern (untyped, unenforced agent-to-agent contracts). Whether
  claude-loop's subagent briefs and correctless's RED→GREEN handoffs are
  typed/enumerated or natural-language prose is genuinely unanswered.
- **#3's "plan never revisited" weakness doesn't transfer 1:1** — claude-loop's
  plan (`prd.json`) is authored ahead of time by a human/agent, not generated
  by an LLM planner at runtime the way LangChain's pattern describes, so the
  specific failure mode (a bad runtime plan nobody revisits) isn't inherited
  unless whoever wrote the `prd.json` also never revisits it.

**Through-line:** every source in this tier is solving "how does intent survive
a boundary" — a session boundary (#1, #2), a strategy/tactics boundary (#3), an
agent/agent boundary at scale (#4), or a schema boundary (#5). The answer is
structurally the same each time: externalize the intent into an artifact with
an explicit contract, and don't trust either memory or prose to carry it
across.
