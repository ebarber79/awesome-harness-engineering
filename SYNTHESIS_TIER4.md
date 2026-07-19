# Tier-4 Synthesis — Verification & Human-in-the-Loop

One-page synthesis of the three Tier-4 sources on closing the loop: knowing
whether it worked, and where humans belong in it. Read this to hold the whole
mental model at once; read the originals for depth.

Sources:
- Demystifying Evals for AI Agents (Anthropic) — https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
- Humans and Agents in Software Engineering Loops (Martin Fowler / Kief Morris) — https://martinfowler.com/articles/exploring-gen-ai/humans-and-agents.html
- Measuring AI Agent Autonomy in Practice (Anthropic) — https://www.anthropic.com/news/measuring-agent-autonomy

---

## The one idea they share

An autonomous loop (Tier 1) with good context management (Tier 2) and solid
orchestration (Tier 3) is still just a fast way to be confidently wrong unless
something closes the loop — and "something" is never a single mechanism. All
three sources converge on the same shape: verification has to be
**structurally separate** from the thing being verified (a different grader, a
harness instead of an artifact, an oversight channel instead of a gate) —
because anything that grades or approves its own work drifts.

## 1. Demystifying Evals for AI Agents — how to know it actually works

Key distinction for scoring non-determinism: **pass@k** (at least one of k
attempts succeeds — fine when any working answer is enough) vs. **pass^k** (all
k succeed — the bar for customer-facing reliability). Three grader tiers, in
order of preference: code-based (cheap, deterministic), model-based (flexible,
needs calibration against humans), human (gold standard, expensive,
non-negotiable for validation). The governing rule: "we do not take eval
scores at face value until someone digs into the details of the eval and reads
some transcripts." Concrete process: start with 20–50 tasks pulled from **real
failures**, not synthetic ones; a good task is one where "two domain experts
would independently reach the same pass/fail verdict"; watch for **eval
saturation** (100% pass rate stops being a useful signal) and refresh
accordingly.

## 2. Humans and Agents in Software Engineering Loops — where the human stands

Three models, and the framing is about *where you intervene*, not *how much*:
**outside the loop** ("vibe coding" — specify outcomes, agent handles
everything; fast, but debt accumulates unwatched); **in the loop** (human
reviews every artifact; doesn't scale — "agents can generate code faster than
humans can manually inspect it"); **on the loop** (recommended — humans
engineer the harness: specs, quality gates, workflow, and when output
disappoints, fix the *system that produced it*, not the artifact itself).
Morris extends this to an "agentic flywheel" — agents improving their own
harness from test/metric/production feedback, with humans progressively
automating approval of low-risk changes.

## 3. Measuring AI Agent Autonomy in Practice — what oversight actually looks like as trust grows

Empirical, not just theoretical: as users gain experience, they don't reduce
oversight — they **shift its form**. Auto-approval of actions rises (~20% →
~40%), but so does the interruption rate (~5% → ~9%) — a move from per-action
approval toward active monitoring with selective intervention. Explicit policy
stance: don't mandate a specific interaction pattern (like "approve every
action"); judge autonomy by whether a human **can still effectively monitor and
intervene**. Named finding: a "deployment overhang" — models are capable of
more autonomy than users currently grant them, meaning autonomy is
trust-limited, not capability-limited.

---

## Synthesis for your stack

- **#1 is only half-✅** on your Overlap Map as written — correctless's
  RED-phase deterministic test-from-spec discipline covers the code-based-
  grader, single-task half of this article, but the trial-based
  pass@k/pass^k framing and ongoing eval-suite maintenance (saturation
  monitoring, refresh cadence) is closer to the still-open 🔴 gap
  (promptfoo/AgentBench) than to something you already run.
- **#2 names roles you're already occupying without a label**:
  outreach-followup-drafts is deliberately "in the loop" (drafts wait for
  review + send — correct given the stakes); your VPS cron jobs (feng alerts,
  browser-use's weekly scrape, backups) are "outside the loop" by design; and
  this entire study repo — correctless's spec-review pipeline, claude-loop's
  prd.json contract — *is* "on the loop" in Morris's exact sense.
- **#3 isn't abstract for you** — the "Auto Mode Active" policy governing
  sessions like this one (bias toward acting without stopping, but stay
  interruptible) is a live instance of the paper's finding: high
  auto-approval paired with a kept-open interrupt channel, not high
  auto-approval replacing oversight. Corroborates the 🟡 tag already on
  "Beyond Permission Prompts" + "Claude Code Auto Mode" with actual numbers.

**Through-line:** closing the loop is never "add a checker." It's deciding
*which* separate thing checks — a different grader (#1), a different locus of
human attention (#2), a different form of oversight as trust grows (#3) — and
the failure mode across all three sources is the same one: letting the thing
being verified also do the verifying.
