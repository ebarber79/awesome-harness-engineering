# Scope — Loop Observability / Tracing on the IONOS VPS

Closes gap #1 from `READING_LIST.md` (no tracing over loop runs).
Grounded in live VPS state checked 2026-07-01.

## Live VPS state (the constraints)
- **RAM: 7.7 GB total, ~518 MB free, 3.1 GB swap IN USE** — already under pressure.
- Top consumers: llama-server 2.6 GB (Ollama qwen3.5:2b, keep-alive ~24h), Chrome 1.3 GB (browser-use), python 0.5 GB, next-server 0.2 GB.
- Disk: 126 GB free of 237 GB — not a constraint.
- Already running: `claude-mem` stack (server + worker + postgres:17 + valkey) — Claude Code session-ingest/memory tool; overlaps "what happened in my loop." Reconcile, don't duplicate.
- Access pattern precedent: services bind to Tailscale IP 100.93.253.90 (e.g. finance-grounding :8848).

## Decision: Phoenix, not Langfuse
- **Langfuse v3 = 5–6 services (web/worker/Postgres/ClickHouse/Redis/MinIO) ≈ 2–4 GB.** Non-starter on a box with 518 MB free + active swap.
- **Phoenix = single container, ~300–600 MB, OTLP-native**, with first-class OpenInference auto-instrumentors for the exact VPS frameworks (DSPy, LangChain/LiteLLM, OpenAI-compatible, browser-use). Persists to SQLite or a Postgres.
- Revisit Langfuse only if the workload later justifies a bigger box.

## Instrumentation reality — two tracks
- **Track A — VPS Python agents (deep, tractable):** DSPy / AgentScope / ai-hedge-fund / browser-use instrumented with OpenInference SDK → OTLP → Phoenix. Real span trees (prompts, tool calls, latencies, token counts).
- **Track B — Claude Code loops (shallow, fiddly):** claude-loop & correctless run in the Claude Code harness, which emits OTEL **metrics + events** (token usage, cost, tool-call decisions per session) — NOT per-subagent LLM spans. Route via `OTEL_EXPORTER_OTLP_ENDPOINT` → collector → metrics dashboard. Gives cost/usage/tool-event visibility, not turn-level traces.

## Phased plan
**Phase 0 — Reclaim RAM (~15 min, prerequisite).**
Lower Ollama keep-alive so the 2.6 GB model unloads when idle (`OLLAMA_KEEP_ALIVE=5m` or per-request `keep_alive`). Frees ~2.6 GB when no inference is running, giving Phoenix headroom. Confirm with `free -h`.

**Phase 1 — Deploy Phoenix (~30 min).**
- Docker container `arizephoenix/phoenix`, `mem_limit: 1g`, restart=unless-stopped.
- Bind UI + OTLP to Tailscale IP 100.93.253.90 (not 0.0.0.0): UI :6006, OTLP/gRPC :4317.
- Persistent volume on the 126 GB disk; start with SQLite (simplest) — reuse claude-mem's Postgres only if retention grows.
- Verify UI over Tailscale, confirm OTLP endpoint accepts a test span.

**Phase 2 — Instrument ONE VPS agent as proof (~30–45 min).**
- Pick DSPy or browser-use (both have OpenInference instrumentors). `pip install openinference-instrumentation-<framework> arize-phoenix-otel`, register the tracer pointing at the VPS OTLP endpoint, run one task, confirm a span tree lands in Phoenix.
- Then fan out to the remaining agents.

**Phase 3 — (optional) Claude Code metrics track (~1 hr).**
- Set Claude Code OTEL env (`CLAUDE_CODE_ENABLE_TELEMETRY`, `OTEL_EXPORTER_OTLP_*`) on the Pi to export to an OTLP collector on the VPS; visualize cost/usage/tool events. Accept this is metrics, not spans.

**Phase 4 — Reconcile with claude-mem (~15 min).**
- Determine what claude-mem already captures from Claude Code sessions; decide whether Phoenix (Track A) + claude-mem covers the need, or whether Track B adds enough to justify the wiring.

## Decisions locked (2026-07-01)
- **Primary target: Track A — VPS agent spans** (OpenInference → Phoenix). Track B (Claude Code loop metrics) deferred to optional/later; not in the initial build.
- **RAM reclaim: lower Ollama keep-alive** (`OLLAMA_KEEP_ALIVE=5m`) to free ~2.6 GB when idle before deploying Phoenix. Model reloads on next inference (~seconds).

## Locked build order
1. **Phase 0 — ✅ DONE (2026-07-01)** — edited drop-in `/etc/systemd/system/ollama.service.d/keepalive.conf` (24h→5m, kept MAX_LOADED_MODELS=1), backup saved as `keepalive.conf.bak.*`, daemon-reload + restart. Verified: model unloaded (`/api/ps` empty), `OLLAMA_KEEP_ALIVE=5m` effective, **available RAM 2.1 GB → 4.8 GB**, swap 3.1 GB → 2.5 GB. Model reloads (~sec) on next inference, unloads after 5 min idle.
2. **Phase 1 — ✅ DONE (2026-07-01)** — `arizephoenix/phoenix:latest` container `phoenix`, `--memory 1g`, `--restart unless-stopped`, bound to Tailscale IP 100.93.253.90 (UI/OTLP-HTTP :6006, OTLP-gRPC :4317), SQLite volume `/opt/phoenix/data`. Audited: UI HTTP 200; real protobuf OTLP span ingested (`POST /v1/traces → 200`); API lists `default` project; `phoenix.db` persisted to volume; container healthy at ~418 MB (of 1 GB cap); 4.5 GB RAM still available. (Note: JSON OTLP returns 415 — Phoenix accepts protobuf only; real SDKs send protobuf, so this is expected, not a defect.)
   - **Access:** `http://100.93.253.90:6006` over Tailscale. **OTLP endpoints for Phase 2:** HTTP `http://100.93.253.90:6006/v1/traces`, gRPC `100.93.253.90:4317`.
3. **Phase 2 — ✅ DONE (2026-07-02)** — instrumented **DSPy** (`/home/jarvis/dspy`, real install, as `jarvis`): `pip install openinference-instrumentation-dspy arize-phoenix-otel opentelemetry-exporter-otlp-proto-http`; `phoenix.otel.register(endpoint=".../v1/traces", project_name="dspy-vps")` + `DSPyInstrumentor().instrument()`; ran a `dspy.Predict` against local Ollama (qwen3.5:2b). **Verified:** `dspy-vps` project in Phoenix with a **12-span tree** — `Predict.forward → Predict(StringSignature).forward → ChatAdapter/JSONAdapter.__call__ → LM.__call__`, kinds `{LLM, CHAIN}`. Reusable recipe saved at `/home/jarvis/dspy/phoenix_trace_demo.py`.
   - **Gotcha found:** DSPy's default `max_tokens` is large; CPU generation on this box is slow enough that an uncapped call runs many minutes and looks hung. Fix: set `max_tokens` low (used 64) for smoke tests. Also run via `systemd-run --uid=jarvis` (transient unit), not `nohup &` over SSH — nohup jobs die when the SSH channel closes.
   - **Fan-out #1 — ✅ ai-hedge-fund (2026-07-02):** `openinference-instrumentation-langchain` into its poetry env; bootstrap `/root/ai-hedge-fund/phoenix_bootstrap.py` calls `run_hedge_fund(...)` directly (bypasses the interactive `questionary` menu), Groq `llama-3.1-8b-instant`, project `ai-hedge-fund`. **Verified 18-span multi-agent LangGraph tree**: `LangGraph → {ben_graham_agent, risk_management_agent, portfolio_manager} → RunnableSequence → ChatPromptTemplate → ChatGroq → PydanticOutputParser` (kinds CHAIN:8, AGENT:4, LLM:2, PROMPT:2). Groq (cloud) avoids the slow-CPU problem entirely — instant run. Phoenix projects now: `ai-hedge-fund`, `dspy-vps`, `default`.
   - **Fan-out #2 — ✅ AgentScope (2026-07-02):** AgentScope 2.0.3 needs NO OpenInference instrumentor — it ships a **native OTel `TracingMiddleware`** (`agentscope.middleware.TracingMiddleware`) that activates whenever a real global SDK `TracerProvider` is set. Recipe: plain OTel SDK (already in its venv, zero installs) — `TracerProvider(resource={"openinference.project.name": "agentscope-vps"})` + `BatchSpanProcessor(OTLPSpanExporter(endpoint="http://100.93.253.90:6006/v1/traces"))` + `trace.set_tracer_provider(tp)`, then pass `middlewares=[TracingMiddleware()]` to the `Agent`. Demo at `/root/agentscope/phoenix_trace_demo.py` (adapted smoke test, qwen3.5:2b + one tool call). **Verified 4-span tree** in project `agentscope-vps`: `invoke_agent Jarvis (AGENT) → chat qwen3.5:2b (LLM) → execute_tool get_weather (TOOL) → chat qwen3.5:2b (LLM)`. Note: Phoenix project routing via the `openinference.project.name` **resource attribute** (that's all `phoenix.otel.register` does under the hood); call `tp.force_flush()` before exit.
   - **Fan-out #3 — ✅ browser-use (2026-07-02):** **Correction — browser-use 0.13.1 is NOT LangChain-based** (Track A above wrongly grouped it with LangChain). Its `ChatGroq` wraps the raw `groq` SDK, so the correct integration is **`openinference-instrumentation-groq`** (+ `arize-phoenix-otel` + `opentelemetry-exporter-otlp-proto-http`) installed into the browser-use venv `/root/browser-use-venv` only. The Groq instrumentor alone emits *flat* LLM spans, so a **root AGENT span is added manually** to wrap `agent.run()` and give the tree a proper parent. Wiring is opt-in via `BU_PHOENIX=1` in `/root/run_agent.py` (default `./bu` path is byte-identical to before; original backed up at `/root/run_agent.py.bak.pre-phoenix`). **Verified 8-span tree** in project `browser-use`: `browser_use.Agent.run (AGENT, root) → 7× AsyncCompletions (LLM, one per agent step)`. Reproduce: `systemd-run --unit=… --setenv=BU_PHOENIX=1 ./bu "…task…"`. Gotchas: harmless OpenInference warning serializing multimodal message content (cosmetic, spans still land); Groq free-tier 500k tokens/day cap can exhaust the org key and block *subsequent* runs (did not block this trace).
   - **All four Track-A agents now traced in Phoenix:** `dspy-vps`, `ai-hedge-fund`, `browser-use`, `agentscope-vps`. Remaining runbook item is Phase 4 (reconcile with claude-mem).
4. **Phase 4 — ✅ DONE (2026-07-02)** — reconciled with claude-mem; see "Phase 4 — reconciliation (resolved)" below.
- Track B (Phase 3) only revisited if Claude Code loop metrics prove needed after Track A is live.

## Rough total effort
Phases 0–2 (Phoenix live + first VPS agent traced): **~1.5 hrs**.

## Phase 4 — reconciliation (resolved 2026-07-02)

**What claude-mem actually captures — inspected read-only, nothing modified.**
- Stack: `thedotmack/claude-mem` v13.4.0 server-beta at `/opt/claude-mem` on the VPS (4 containers healthy, server bound `127.0.0.1:37877`, tunnel-only). Runbook: Obsidian Vault 2 → `System Admin/claude-mem Memory Server on IONOS VPS`.
- Design: Claude Code **hook events** (session/prompt/tool-use) → `POST /v1/events` → raw `agent_events` rows (event_type + jsonb payload) → Gemini worker compresses them into text **`observations`** (LLM-written memory summaries, tsvector full-text + embedding search). It is **semantic session memory, not telemetry** — no latency, token, or cost fields anywhere in the schema.
- **Reality: the database is EMPTY** — 0 observations, 0 sessions, 0 agent_events, 1 placeholder project (`local-hook-project`). The client hooks were **deliberately never installed on the Pi** (installer drags in Bun + uv + per-event hook execs — violates keep-Pi-lean; documented in the 2026-06-23 runbook) and no other machine connected. claude-mem is infrastructure-in-waiting, not an active capture system. There is no overlap with Phoenix to reconcile.

**Coverage matrix** (✅ covers, ◐ partial/would-if-wired, ✗ no):

| Dimension | Phoenix (Track A) | claude-mem (as deployed) | Track B (if wired) |
|---|---|---|---|
| Per-run LLM spans/prompts — VPS agents | ✅ verified (4 projects) | ✗ | ✗ |
| Tool calls + latencies — VPS agents | ✅ span trees | ✗ | ✗ |
| Claude Code sessions: what happened (semantic) | ✗ | ◐ would, if a client existed; today ✗ | ✗ |
| Claude Code sessions: token/cost/tool-event metrics | ✗ | ✗ (schema has no such fields) | ✅ (metrics, not spans) |
| Claude Code sessions: turn-level LLM spans | ✗ | ✗ | ✗ (harness doesn't emit them) |
| Cross-session memory / recall | ✗ | ◐ dormant | ✗ |

Net gap: nothing observes Claude Code sessions today. Track B would cover only the metrics slice of that gap; claude-mem would cover only the semantic slice, and only if a client is ever connected.

**Decision: do NOT wire Track B. Phoenix (Track A) closes the scoped need.**
- Gap #1 that motivated this scope was "no tracing over loop runs" — the deep-visibility target was the VPS Python agents, and that is now fully covered with verified span trees.
- Track B's payoff is cost/usage dashboards for Claude Code sessions: real but low-value today (no active cost problem), and it isn't free — it needs an OTLP **metrics** collector + dashboard on the VPS (Phoenix is trace-ingest; it won't render these metrics), i.e. another always-on service on a RAM-pressured box. Pi-side wiring is just env vars, but the VPS side is the cost.
- Revisit trigger: an actual cost/usage question about Claude Code sessions, or a loop-debugging need that transcripts + MEMORY.md can't answer.
- Side observation (no action taken): the 4 idle claude-mem containers have burned RAM for ~5 days holding zero data. Options when RAM matters: `docker compose down` (keeps volumes) until a client is connected, or connect a non-Pi client per its runbook. Owner's call — out of scope here.
