# Tier-2 Synthesis — Context Is the Loop's Fuel Budget

One-page synthesis of the three Tier-2 context-engineering sources. Read this to
hold the whole mental model at once; read the originals for depth.

Sources:
- Effective Context Engineering for AI Agents (Anthropic, 2026) — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Compaction — Claude API Docs — https://platform.claude.com/docs/en/build-with-claude/compaction
- Claude Code Compaction Explained (community) — https://okhlopkov.com/claude-code-compaction-explained/
- Prompt Caching — Claude API Docs — https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching

---

## The one idea they share

Context is not free text you accumulate — it's a **finite, degrading resource**
every turn of the loop draws from. Each source addresses a different way to
spend it wisely: what goes IN (context engineering's curation), what gets
DISCARDED as the loop runs long (compaction), and what gets REUSED cheaply
across turns (caching). Together these three levers turn "the loop" from Tier 1
into something that survives beyond a few dozen turns without degrading or
becoming unaffordable.

## 1. Effective Context Engineering — *what* belongs in the window

Core reframe: not prompt engineering (one instruction) but context engineering —
curating the smallest set of high-signal tokens across the whole session,
because transformer attention degrades as tokens accumulate ("context rot"),
independent of model size. Concrete techniques: **just-in-time retrieval** —
keep lightweight references (paths, IDs, queries) and fetch content only when a
step needs it, instead of pre-loading; **structured note-taking** — write
persistent notes outside the window itself (a CLAUDE.md-style file), so state
survives even if the window doesn't; **sub-agent condensation** — a worker
returns a synthesis in the **1,000–2,000 token range**, not its raw trace,
keeping the lead agent's window clean.

**Mechanism an engineer must supply:** tools that support cheap partial
retrieval (`head`/`tail`-style, not "load the whole file"), a place to persist
notes outside context, and a return-payload discipline for sub-agents.
**Takeaway:** the default failure mode isn't "not enough context" — it's too
much low-signal context diluting attention on what matters this turn.

## 2. Compaction — *how* the loop survives past its window

The mechanical answer to context rot: a server-side, threshold-triggered
summarization pass. Default trigger is **150,000 input tokens** (configurable,
50k floor), re-checked at the start of **every sampling iteration** — a single
request can compact more than once. What survives: a `compaction` block plus
anything after it; everything before is dropped from future requests, not just
hidden. `pause_after_compaction` lets you hand-pick which recent messages stay
verbatim instead of folding into the summary; custom instructions can bias what
the summary prioritizes (e.g. "preserve code snippets and variable names").
Tuning bias, per Anthropic's own guidance: **maximize recall first, then
precision** — compaction is built to over-preserve, not under-preserve.

**Production gotcha:** the real total token spend lives in `usage.iterations`,
not the top-level `input_tokens`/`output_tokens` fields — a naive cost tracker
will undercount any session that compacted.

## 3. Prompt Caching — *how cheaply* the loop can reuse what it already sent

Two TTLs: **5-minute (free to refresh on reuse)** and **1-hour (2x write cost,
worth it only past 5-minute request gaps)**. A cache hit requires an *exact*
prefix match up to a breakpoint, and reads only look backward **20 blocks** —
anything volatile (timestamps, live counters) placed before a breakpoint
silently kills the cache for everything downstream of it. Cache reads cost
**0.1x** base input price; writes cost **1.25x (5m) or 2x (1h)**. Placement
discipline matters more than TTL choice: put `cache_control` on the *last block
that stays identical across requests*, not on blocks that change every turn.

**Takeaway:** caching isn't "on or off" — it's a placement problem. A
correctly-placed 5-minute cache and an incorrectly-placed 1-hour cache can have
wildly different real costs on the same workload.

---

## Synthesis for your stack

- **Context engineering is largely already how your tools are shaped** — this
  Claude Code session itself retrieves your files just-in-time rather than
  being handed your whole filesystem, and your `MEMORY.md` index + one-fact-
  per-file system is a hand-built version of "structured note-taking outside
  the window."
- **Compaction is ✅ implemented and now precisely understood**
  (`READING_LIST.md` Overlap Map) — the mechanics (150k/50k thresholds,
  per-iteration trigger, `usage.iterations` billing) are worth knowing cold
  before debugging any "why did it forget X" report, since the answer is more
  often a precision failure in the summary than the mechanism dropping
  something outright.
- **Prompt caching is ✅ implemented and already correctly tuned** — your
  `ScheduleWakeup` tool's 1-hour delay ceiling was deliberately picked to match
  the 1-hour cache TTL, so `/loop`-style wakeups never fall outside the cache
  window. Unaudited: whether headless VPS/cron jobs (feng alerts, outreach
  followups, browser-use) place any cache-breaking content before their
  breakpoints.
- **The one number worth carrying forward: 1,000–2,000 tokens** as the target
  sub-agent return budget — currently informal in claude-loop's
  orchestrator/subagent split, worth making an explicit contract.

**Through-line:** context engineering decides what's worth keeping; compaction
is the harness's answer to what happens when you keep too much anyway; caching
is the harness's answer to not re-paying for what you keep. All three exist
because the loop from Tier 1 has no memory of its own — everything durable
across turns has to be engineered in.
